# Project 3: Smart Task Manager with AI Prioritization
**Duration:** 24 hours
**Skill Level:** Intermediate
**End Result:** A smart task management app that uses AI to prioritize and organize work

## Initial Setup

1. **Project Creation**
   ```bash
   # Cursor AI prompt:
   "Create a new Vue 3 project with TypeScript, Pinia for state management, and Prisma + PlanetScale setup. Include Tailwind CSS and necessary dependencies."
   ```

2. **Database Setup**
   - Create PlanetScale account
   - Set up database
   - Configure Prisma

## Phase 1: Core Architecture

1. **Database Schema**
   Cursor AI prompt:
   ```
   "Create Prisma schema for a task management system with tasks, projects, tags, and user models. Include relationships and necessary fields."
   ```

   ```prisma
   model Task {
     id          String      @id @default(cuid())
     title       String
     description String?
     priority    Int
     dueDate     DateTime?
     status      String
     aiScore     Float?
     tags        Tag[]
     project     Project?    @relation(fields: [projectId], references: [id])
     projectId   String?
     user        User        @relation(fields: [userId], references: [id])
     userId      String
     created_at  DateTime    @default(now())
     updated_at  DateTime    @updatedAt
   }

   model Project {
     id          String    @id @default(cuid())
     name        String
     tasks       Task[]
     user        User      @relation(fields: [userId], references: [id])
     userId      String
   }

   model Tag {
     id        String    @id @default(cuid())
     name      String
     tasks     Task[]
     user      User      @relation(fields: [userId], references: [id])
     userId    String
   }

   model User {
     id        String    @id @default(cuid())
     email     String    @unique
     tasks     Task[]
     projects  Project[]
     tags      Tag[]
   }
   ```

2. **State Management**
   Cursor AI prompt:
   ```
   "Create Pinia stores for tasks, projects, and user state. Include actions for CRUD operations and task prioritization."
   ```

## Phase 2: AI Integration

1. **Priority Calculation**
   Cursor AI prompt:
   ```
   "Create an AI service that analyzes task properties and calculates priority scores. Include due date, description complexity, and tag importance in the calculation."
   ```

   ```typescript
   // services/ai.ts
   export async function calculateTaskPriority(task: Task) {
     const prompt = `
       Analyze this task and rate its priority from 1-10 based on:
       Title: ${task.title}
       Description: ${task.description}
       Due Date: ${task.dueDate}
       Tags: ${task.tags.map(t => t.name).join(', ')}
       
       Consider:
       1. Urgency based on due date
       2. Importance based on description keywords
       3. Project context
       4. Tag priorities
       
       Return only the numerical score.
     `;

     const completion = await openai.createCompletion({
       model: "gpt-3.5-turbo",
       prompt,
       max_tokens: 1,
       temperature: 0.3
     });

     return parseFloat(completion.data.choices[0].text);
   }
   ```

2. **Smart Categorization**
   ```typescript
   // services/categorization.ts
   export async function suggestTaskTags(description: string) {
     const prompt = `
       Suggest up to 3 tags for this task:
       "${description}"
       
       Return only the tags, comma-separated.
     `;

     const completion = await openai.createCompletion({
       model: "gpt-3.5-turbo",
       prompt,
       max_tokens: 20
     });

     return completion.data.choices[0].text.split(',').map(tag => tag.trim());
   }
   ```

## Phase 3: Core Features

1. **Task Management**
   Cursor AI prompt:
   ```
   "Create components for task creation, editing, and list views. Include drag-and-drop functionality and quick actions."
   ```

   Key components:
   ```
   components/
   ├── TaskList.vue
   ├── TaskCard.vue
   ├── TaskForm.vue
   ├── TaskDetails.vue
   └── QuickActions.vue
   ```

2. **Calendar Integration**
   ```typescript
   // composables/useCalendar.ts
   export function useCalendar() {
     const tasks = useTaskStore();

     const calendarEvents = computed(() => 
       tasks.list.map(task => ({
         id: task.id,
         title: task.title,
         start: task.dueDate,
         color: getPriorityColor(task.priority)
       }))
     );

     return {
       calendarEvents,
       // ... other calendar methods
     };
   }
   ```

## Phase 4: User Interface

1. **Dashboard Layout**
   Cursor AI prompt:
   ```
   "Create a responsive dashboard layout with sidebar navigation, task board, and calendar view. Include dark mode support."
   ```

2. **Task Board**
   ```vue
   <!-- components/TaskBoard.vue -->
   <template>
     <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
       <div v-for="status in ['Todo', 'In Progress', 'Done']" 
            :key="status" 
            class="bg-white dark:bg-gray-800 rounded-lg p-4">
         <h3 class="text-lg font-semibold mb-4">{{ status }}</h3>
         <draggable 
           v-model="getTasksByStatus(status)" 
           group="tasks"
           @change="handleStatusChange">
           <task-card 
             v-for="task in getTasksByStatus(status)" 
             :key="task.id" 
             :task="task" />
         </draggable>
       </div>
     </div>
   </template>
   ```

## Phase 5: Advanced Features

1. **Time Tracking**
   ```typescript
   // composables/useTimeTracking.ts
   export function useTimeTracking() {
     const timer = ref(0);
     const isRunning = ref(false);
     let interval: number;

     const startTimer = () => {
       isRunning.value = true;
       interval = setInterval(() => {
         timer.value++;
       }, 1000);
     };

     const stopTimer = async (taskId: string) => {
       clearInterval(interval);
       isRunning.value = false;
       await updateTaskTime(taskId, timer.value);
     };

     return {
       timer,
       isRunning,
       startTimer,
       stopTimer
     };
   }
   ```

2. **Progress Tracking**
   ```typescript
   // composables/useProgress.ts
   export function useProgress() {
     const tasks = useTaskStore();

     const projectProgress = computed(() => {
       return tasks.byProject.map(project => ({
         id: project.id,
         name: project.name,
         progress: calculateProgress(project.tasks),
         totalTasks: project.tasks.length,
         completedTasks: project.tasks.filter(t => t.status === 'Done').length
       }));
     });

     return {
       projectProgress
     };
   }
   ```

## Key Features to Implement

1. **Smart Scheduling**
   ```typescript
   async function suggestOptimalSchedule(tasks: Task[]) {
     const prompt = `
       Given these tasks:
       ${tasks.map(t => `
         - ${t.title}
         - Due: ${t.dueDate}
         - Priority: ${t.priority}
       `).join('\n')}
       
       Suggest an optimal daily schedule considering:
       1. Priority levels
       2. Due dates
       3. Expected duration
       4. Dependencies
       
       Return a JSON schedule.
     `;

     // Implement scheduling logic
   }
   ```

2. **Task Dependencies**
   ```typescript
   interface TaskDependency {
     taskId: string;
     dependsOn: string[];
     blockedTasks: string[];
   }
   ```

## Common Issues & Solutions

1. **Performance**
   - Issue: Slow rendering with many tasks
   - Solution: Virtual scrolling and pagination

2. **State Management**
   - Issue: Complex task updates
   - Solution: Optimistic updates with proper error handling

3. **AI Integration**
   - Issue: Slow AI responses
   - Solution: Implement request queuing and caching
