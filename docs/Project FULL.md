# Project 1: Personal AI Blog Engine
**Duration:** 24 hours
**Skill Level:** Beginner-friendly
**End Result:** A fully functional, deployed blog with AI content generation

## Initial Setup
1. **Install Required Tools**
   - Install VS Code: https://code.visualstudio.com/
   - Install Cursor AI: https://cursor.sh/
   - Create GitHub account: https://github.com
   - Create Vercel account: https://vercel.com
   - Create Supabase account: https://supabase.com

2. **Project Initialization**
   First Cursor AI prompt:
   ```
   "Create a new Next.js 14 project with TypeScript and Tailwind CSS. Include app router, authentication with Supabase, and initial project structure for a blog."
   ```

## Phase 1: Core Structure
1. **Database Setup**
   Cursor AI prompt:
   ```
   "Create Supabase database schema for a blog with tables for posts, users, comments, and categories. Include relationships and necessary fields."
   ```

   Expected tables:
   - posts (id, title, content, user_id, created_at, updated_at)
   - categories (id, name)
   - post_categories (post_id, category_id)
   - comments (id, post_id, user_id, content, created_at)

2. **Authentication**
   Cursor AI prompt:
   ```
   "Implement Supabase authentication with Next.js. Include sign up, login, and protected routes for the admin dashboard."
   ```

## Phase 2: Core Features

1. **Blog Post Management**
   Cursor AI prompt:
   ```
   "Create CRUD operations for blog posts using Supabase and Next.js server actions. Include rich text editing with TipTap editor."
   ```

2. **AI Content Generation**
   Cursor AI prompt:
   ```
   "Implement AI content generation using OpenAI API. Create a function that generates blog post ideas and content based on user input topics."
   ```

3. **Comment System**
   Cursor AI prompt:
   ```
   "Add a real-time comment system using Supabase subscriptions. Include comment creation, deletion, and moderation features."
   ```

## Phase 3: UI/UX Development

1. **Homepage Design**
   Cursor AI prompt:
   ```
   "Create a modern responsive homepage using Tailwind CSS. Include a featured post section, recent posts grid, and category filter."
   ```

2. **Blog Post Page**
   Cursor AI prompt:
   ```
   "Design a blog post page with responsive typography, social sharing buttons, and related posts section."
   ```

3. **Admin Dashboard**
   Cursor AI prompt:
   ```
   "Build an admin dashboard with post management, comment moderation, and analytics overview using Tailwind CSS and React components."
   ```

## Phase 4: Advanced Features

1. **Search Implementation**
   Cursor AI prompt:
   ```
   "Add full-text search functionality using Supabase text search. Include search suggestions and filters."
   ```

2. **SEO Optimization**
   Cursor AI prompt:
   ```
   "Implement SEO best practices including meta tags, sitemap generation, and structured data for blog posts."
   ```

3. **Analytics**
   Cursor AI prompt:
   ```
   "Add basic analytics tracking for page views, reading time, and popular posts using Supabase."
   ```

## Phase 5: Testing & Deployment

1. **Testing**
   Cursor AI prompt:
   ```
   "Create basic test cases for core functionality using Vitest. Include tests for post creation, comment system, and authentication."
   ```

2. **Deployment**
   Cursor AI prompt:
   ```
   "Generate deployment configuration for Vercel, including environment variables and build settings."
   ```

## Key Files Structure
```
app/
├── (auth)/
│   ├── login/
│   └── signup/
├── (dashboard)/
│   ├── posts/
│   ├── comments/
│   └── analytics/
├── api/
│   ├── posts/
│   └── comments/
├── components/
│   ├── ui/
│   ├── posts/
│   └── dashboard/
└── lib/
    ├── supabase/
    └── utils/
```

## Essential Code Snippets

1. **Supabase Client Setup**
   ```typescript
   import { createBrowserClient } from '@supabase/ssr'

   export const createClient = () => {
     return createBrowserClient(
       process.env.NEXT_PUBLIC_SUPABASE_URL!,
       process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
     )
   }
   ```

2. **Protected Route Example**
   ```typescript
   import { createServerClient } from '@supabase/ssr'

   export default async function DashboardLayout({
     children,
   }: {
     children: React.ReactNode
   }) {
     const supabase = createServerClient()
     const { data: { session } } = await supabase.auth.getSession()

     if (!session) {
       redirect('/login')
     }

     return <div>{children}</div>
   }
   ```

3. **Blog Post Creation**
   ```typescript
   'use server'
   
   export async function createPost(formData: FormData) {
     const supabase = createServerClient()
     const title = formData.get('title') as string
     const content = formData.get('content') as string
     
     const { data, error } = await supabase
       .from('posts')
       .insert([{ title, content }])
       .select()
       
     revalidatePath('/dashboard/posts')
     return { data, error }
   }
   ```

## Common Issues & Solutions

1. **Authentication Flow**
   - Issue: Session not persisting
   - Solution: Ensure proper middleware setup and cookie handling

2. **Real-time Updates**
   - Issue: Comments not updating in real-time
   - Solution: Verify Supabase subscription setup and client-side handlers

3. **Image Handling**
   - Issue: Large image uploads failing
   - Solution: Implement proper image optimization and progressive loading

## Next Steps & Extensions
After completing the basic project, consider adding:
1. Social media integration
2. Newsletter subscription
3. Custom themes
4. Multiple authors support
5. Content scheduling

# Project 2: AI-Powered Personal Finance Tracker
**Duration:** 24 hours
**Skill Level:** Intermediate
**End Result:** A smart finance tracker that automatically categorizes expenses and provides insights

## Initial Setup
1. **Tools Setup**
   ```bash
   # Cursor AI prompt:
   "Create a new React project using Vite with TypeScript, TailwindCSS, and Firebase integration. Include ESLint and environment setup."
   ```

2. **Firebase Configuration**
   - Create Firebase project
   - Enable Authentication
   - Set up Firestore
   - Enable Storage (for receipts)

## Phase 1: Core Architecture

1. **Firebase Setup**
   Cursor AI prompt:
   ```
   "Create Firebase configuration with authentication and Firestore setup. Include custom hooks for auth and database operations."
   ```

   Expected files:
   ```typescript
   // lib/firebase.ts
   // hooks/useAuth.ts
   // hooks/useFirestore.ts
   ```

2. **Data Models**
   ```typescript
   interface Transaction {
     id: string;
     amount: number;
     description: string;
     category: string;
     date: Date;
     userId: string;
     receipt?: string;
     aiCategories?: string[];
     type: 'income' | 'expense';
   }

   interface Budget {
     id: string;
     category: string;
     limit: number;
     spent: number;
     period: 'monthly' | 'weekly';
     userId: string;
   }
   ```

## Phase 2: Core Features

1. **Transaction Management**
   Cursor AI prompt:
   ```
   "Create CRUD operations for financial transactions using Firebase. Include batch operations and real-time updates using custom hooks."
   ```

2. **AI Categorization**
   Cursor AI prompt:
   ```
   "Implement OpenAI integration for automatic expense categorization. Create a function that analyzes transaction descriptions and suggests categories."
   ```

3. **Receipt Processing**
   Cursor AI prompt:
   ```
   "Add receipt upload functionality with OCR using Firebase Storage and Tesseract.js. Extract amount and date automatically."
   ```

## Phase 3: Data Visualization

1. **Dashboard Components**
   Cursor AI prompt:
   ```
   "Create dashboard components using Chart.js for expense tracking. Include pie charts for categories, line charts for spending trends, and bar charts for budget vs actual."
   ```

   Key visualizations:
   - Monthly overview
   - Category breakdown
   - Spending trends
   - Budget progress

2. **Interactive Reports**
   ```typescript
   // Example chart component
   import { Line } from 'react-chartjs-2';
   
   export const SpendingTrend = ({ data }: Props) => {
     const chartData = {
       labels: data.map(d => d.date),
       datasets: [{
         data: data.map(d => d.amount),
         fill: false,
         borderColor: 'rgb(75, 192, 192)',
         tension: 0.1
       }]
     };
     
     return <Line data={chartData} />;
   };
   ```

## Phase 4: Smart Features

1. **Budget Planning**
   Cursor AI prompt:
   ```
   "Implement smart budget suggestions using historical data analysis. Create alerts for overspending and category-based recommendations."
   ```

2. **Expense Analysis**
   Cursor AI prompt:
   ```
   "Add AI-powered expense analysis that identifies spending patterns and suggests potential savings. Include monthly comparisons and anomaly detection."
   ```

3. **Export Functionality**
   Cursor AI prompt:
   ```
   "Create export functions for transactions and reports in CSV and PDF formats. Include customizable date ranges and category filters."
   ```

## Phase 5: UI/UX Implementation

1. **Responsive Design**
   ```
   // Cursor AI prompt:
   "Create a responsive dashboard layout using Tailwind CSS with a sidebar, main content area, and floating action buttons. Include dark mode support."
   ```

2. **Component Library**
   Key components:
   ```typescript
   // components/TransactionForm.tsx
   // components/BudgetCard.tsx
   // components/CategoryPieChart.tsx
   // components/SpendingTrend.tsx
   // components/ReceiptUploader.tsx
   ```

## File Structure
```
src/
├── components/
│   ├── dashboard/
│   ├── transactions/
│   ├── budgets/
│   └── charts/
├── hooks/
│   ├── useTransactions.ts
│   ├── useBudgets.ts
│   └── useAnalytics.ts
├── lib/
│   ├── firebase.ts
│   ├── openai.ts
│   └── utils/
└── pages/
    ├── Dashboard.tsx
    ├── Transactions.tsx
    └── Reports.tsx
```

## Essential Features Implementation

1. **Transaction Categorization**
   ```typescript
   async function categorizeTransaction(description: string) {
     const prompt = `Categorize this transaction: ${description}
                    Categories: groceries, utilities, entertainment, 
                    transport, dining, shopping, health`;
     
     const completion = await openai.createCompletion({
       model: "gpt-3.5-turbo",
       prompt,
       max_tokens: 10
     });
     
     return completion.data.choices[0].text.trim();
   }
   ```

2. **Budget Tracking**
   ```typescript
   function checkBudgetStatus(transactions: Transaction[], budgets: Budget[]) {
     return budgets.map(budget => {
       const spent = transactions
         .filter(t => t.category === budget.category)
         .reduce((sum, t) => sum + t.amount, 0);
         
       return {
         ...budget,
         spent,
         remaining: budget.limit - spent,
         status: spent > budget.limit ? 'exceeded' : 'within'
       };
     });
   }
   ```

3. **Real-time Updates**
   ```typescript
   function useTransactions(userId: string) {
     const [transactions, setTransactions] = useState<Transaction[]>([]);
     
     useEffect(() => {
       const unsubscribe = onSnapshot(
         query(collection(db, 'transactions'), 
         where('userId', '==', userId)),
         (snapshot) => {
           const docs = snapshot.docs.map(doc => ({
             id: doc.id,
             ...doc.data()
           }));
           setTransactions(docs);
         }
       );
       
       return () => unsubscribe();
     }, [userId]);
     
     return transactions;
   }
   ```

## Deployment (Optional)

1. **Firebase Deployment**
   ```bash
   npm install -g firebase-tools
   firebase login
   firebase init
   firebase deploy
   ```

## Common Challenges & Solutions

1. **Real-time Updates Performance**
   - Challenge: Excessive re-renders with real-time data
   - Solution: Implement debouncing and memoization

2. **Chart Performance**
   - Challenge: Slow rendering with large datasets
   - Solution: Implement data aggregation and lazy loading

3. **Mobile Responsiveness**
   - Challenge: Complex charts on small screens
   - Solution: Implement responsive breakpoints and alternative mobile views

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

# Project 4: AI Recipe Generator & Meal Planner
**Duration:** 24 hours
**Skill Level:** Intermediate
**End Result:** A smart meal planning app that generates recipes based on available ingredients and dietary preferences

## Initial Setup

1. **Project Creation**
   ```bash
   # Cursor AI prompt:
   "Create a new SvelteKit project with TypeScript, MongoDB, and Cloudinary setup. Include TailwindCSS and necessary dependencies for recipe management."
   ```

2. **Environment Setup**
   ```env
   MONGODB_URI=your_mongodb_uri
   OPENAI_API_KEY=your_openai_key
   CLOUDINARY_CLOUD_NAME=your_cloud_name
   CLOUDINARY_API_KEY=your_api_key
   CLOUDINARY_API_SECRET=your_api_secret
   ```

## Phase 1: Data Structure

1. **MongoDB Schema**
   ```typescript
   // models/Recipe.ts
   interface Recipe {
     _id: ObjectId;
     name: string;
     ingredients: {
       name: string;
       amount: number;
       unit: string;
     }[];
     instructions: string[];
     nutritionInfo: {
       calories: number;
       protein: number;
       carbs: number;
       fat: number;
     };
     prepTime: number;
     cookTime: number;
     servings: number;
     tags: string[];
     imageUrl: string;
     generated: boolean;
   }

   // models/MealPlan.ts
   interface MealPlan {
     _id: ObjectId;
     userId: string;
     weekStart: Date;
     meals: {
       date: Date;
       breakfast: ObjectId;
       lunch: ObjectId;
       dinner: ObjectId;
       snacks: ObjectId[];
     }[];
     groceryList: {
       item: string;
       amount: number;
       unit: string;
       bought: boolean;
     }[];
   }
   ```

## Phase 2: AI Recipe Generation

1. **Recipe Generator Service**
   ```typescript
   // services/recipeGenerator.ts
   async function generateRecipe(ingredients: string[], dietary: string[]) {
     const prompt = `
       Create a recipe using these ingredients: ${ingredients.join(', ')}
       Dietary restrictions: ${dietary.join(', ')}
       
       Return a JSON object with:
       1. Recipe name
       2. Ingredients with amounts
       3. Step-by-step instructions
       4. Nutrition information
       5. Prep and cook time
       
       Make the recipe creative but practical.
     `;

     const completion = await openai.createChatCompletion({
       model: "gpt-3.5-turbo",
       messages: [{ role: "user", content: prompt }],
       temperature: 0.7
     });

     return JSON.parse(completion.data.choices[0].message.content);
   }
   ```

2. **Image Generation**
   ```typescript
   async function generateRecipeImage(recipeName: string) {
     const response = await openai.createImage({
       prompt: `Professional food photography of ${recipeName}, overhead view, natural lighting, on a white plate`,
       size: "1024x1024",
       n: 1
     });
     
     const imageUrl = response.data.data[0].url;
     return await uploadToCloudinary(imageUrl);
   }
   ```

## Phase 3: Core Features

1. **Ingredient Management**
   ```svelte
   <!-- components/IngredientInput.svelte -->
   <script lang="ts">
     import { createEventDispatcher } from 'svelte';
     
     let ingredients: string[] = [];
     let newIngredient = '';
     const dispatch = createEventDispatcher();
     
     function addIngredient() {
       if (newIngredient) {
         ingredients = [...ingredients, newIngredient];
         newIngredient = '';
         dispatch('update', ingredients);
       }
     }
   </script>

   <div class="space-y-2">
     <div class="flex gap-2">
       <input
         bind:value={newIngredient}
         class="input input-bordered flex-1"
         placeholder="Add ingredient..."
       />
       <button on:click={addIngredient} class="btn btn-primary">
         Add
       </button>
     </div>
     
     <div class="flex flex-wrap gap-2">
       {#each ingredients as ingredient}
         <span class="badge badge-lg">
           {ingredient}
           <button class="ml-2" on:click={() => removeIngredient(ingredient)}>
             ×
           </button>
         </span>
       {/each}
     </div>
   </div>
   ```

2. **Meal Planning**
   ```typescript
   // services/mealPlanner.ts
   async function generateWeeklyPlan(preferences: PlanPreferences) {
     const mealTypes = ['breakfast', 'lunch', 'dinner'];
     const weekPlan = {};
     
     for (let day = 0; day < 7; day++) {
       const date = new Date();
       date.setDate(date.getDate() + day);
       
       weekPlan[date.toISOString()] = {};
       
       for (const meal of mealTypes) {
         const recipe = await generateRecipe(
           preferences.ingredients,
           preferences.dietary
         );
         weekPlan[date.toISOString()][meal] = recipe;
       }
     }
     
     return weekPlan;
   }
   ```

## Phase 4: UI Implementation

1. **Recipe Card Component**
   ```svelte
   <!-- components/RecipeCard.svelte -->
   <script lang="ts">
     export let recipe: Recipe;
   </script>

   <div class="card bg-base-100 shadow-xl">
     <figure>
       <img src={recipe.imageUrl} alt={recipe.name} class="h-48 w-full object-cover"/>
     </figure>
     <div class="card-body">
       <h2 class="card-title">{recipe.name}</h2>
       <div class="flex gap-2 text-sm text-gray-600">
         <span>🕒 {recipe.prepTime + recipe.cookTime} mins</span>
         <span>🍽️ {recipe.servings} servings</span>
       </div>
       <div class="flex flex-wrap gap-1 mt-2">
         {#each recipe.tags as tag}
           <span class="badge badge-accent">{tag}</span>
         {/each}
       </div>
     </div>
   </div>
   ```

2. **Meal Calendar**
   ```svelte
   <!-- components/MealCalendar.svelte -->
   <script lang="ts">
     export let weekPlan: MealPlan;
     
     const days = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
     const meals = ['breakfast', 'lunch', 'dinner'];
   </script>

   <div class="grid grid-cols-8 gap-4">
     <div></div>
     {#each days as day}
       <div class="font-bold text-center">{day}</div>
     {/each}
     
     {#each meals as meal}
       <div class="font-medium capitalize">{meal}</div>
       {#each days as day}
         <div class="p-2 border rounded">
           {#if weekPlan[day]?.[meal]}
             <RecipeCard recipe={weekPlan[day][meal]} mini={true} />
           {/if}
         </div>
       {/each}
     {/each}
   </div>
   ```

## Phase 5: Advanced Features

1. **Shopping List Generator**
   ```typescript
   function generateShoppingList(mealPlan: MealPlan) {
     const groceryList = new Map<string, { amount: number; unit: string }>();
     
     for (const day of mealPlan.meals) {
       for (const meal of Object.values(day)) {
         for (const ingredient of meal.ingredients) {
           if (groceryList.has(ingredient.name)) {
             const existing = groceryList.get(ingredient.name)!;
             if (existing.unit === ingredient.unit) {
               existing.amount += ingredient.amount;
             }
           } else {
             groceryList.set(ingredient.name, {
               amount: ingredient.amount,
               unit: ingredient.unit
             });
           }
         }
       }
     }
     
     return Array.from(groceryList.entries()).map(([name, details]) => ({
       item: name,
       ...details,
       bought: false
     }));
   }
   ```

2. **Nutrition Tracking**
   ```typescript
   function calculateDailyNutrition(meals: Recipe[]) {
     return meals.reduce((total, meal) => ({
       calories: total.calories + meal.nutritionInfo.calories,
       protein: total.protein + meal.nutritionInfo.protein,
       carbs: total.carbs + meal.nutritionInfo.carbs,
       fat: total.fat + meal.nutritionInfo.fat
     }), {
       calories: 0,
       protein: 0,
       carbs: 0,
       fat: 0
     });
   }
   ```

## Common Issues & Solutions

1. **Image Generation**
   - Issue: Slow image generation
   - Solution: Use placeholder images during generation

2. **Recipe Variation**
   - Issue: Similar recipes being generated
   - Solution: Add diversity parameters to AI prompts

3. **Performance**
   - Issue: Slow meal plan generation
   - Solution: Implement background processing and caching

# Project 5: AI-Enhanced Social Media Dashboard
**Duration:** 24 hours
**Skill Level:** Intermediate
**End Result:** A comprehensive social media management platform with AI-powered analytics and automation

## Initial Setup

1. **Project Creation**
   ```bash
   # Cursor AI prompt:
   "Create a new T3 Stack project with Next.js, TypeScript, Prisma, tRPC, and NextAuth.js. Include TailwindCSS and configurations for social media API integration."
   ```

2. **Environment Setup**
   ```env
   DATABASE_URL=your_postgres_url
   NEXTAUTH_SECRET=your_secret
   NEXTAUTH_URL=http://localhost:3000
   OPENAI_API_KEY=your_openai_key
   TWITTER_API_KEY=your_twitter_key
   TWITTER_API_SECRET=your_twitter_secret
   FACEBOOK_CLIENT_ID=your_facebook_id
   FACEBOOK_CLIENT_SECRET=your_facebook_secret
   LINKEDIN_CLIENT_ID=your_linkedin_id
   LINKEDIN_CLIENT_SECRET=your_linkedin_secret
   ```

## Phase 1: Data Structure

1. **Prisma Schema**
   ```typescript
   // prisma/schema.prisma
   model User {
     id            String    @id @default(cuid())
     email         String    @unique
     name          String?
     accounts      Account[]
     socialProfiles SocialProfile[]
     posts         Post[]
     analytics     Analytics[]
     schedules     Schedule[]
   }

   model SocialProfile {
     id            String    @id @default(cuid())
     userId        String
     platform      String    // twitter, facebook, linkedin
     profileId     String
     accessToken   String
     refreshToken  String?
     user          User      @relation(fields: [userId], references: [id])
     posts         Post[]
     analytics     Analytics[]
   }

   model Post {
     id            String    @id @default(cuid())
     content       String
     mediaUrl      String?
     platform      String[]
     publishedAt   DateTime?
     scheduledFor  DateTime?
     sentiment     Float?
     engagement    Json?
     userId        String
     profileId     String
     user          User      @relation(fields: [userId], references: [id])
     socialProfile SocialProfile @relation(fields: [profileId], references: [id])
   }

   model Analytics {
     id            String    @id @default(cuid())
     date          DateTime
     metrics       Json
     sentiment     Float
     trending      Boolean
     userId        String
     profileId     String
     user          User      @relation(fields: [userId], references: [id])
     socialProfile SocialProfile @relation(fields: [profileId], references: [id])
   }

   model Schedule {
     id            String    @id @default(cuid())
     userId        String
     frequency     String
     timeSlots     Json
     platforms     String[]
     active        Boolean
     user          User      @relation(fields: [userId], references: [id])
   }
   ```

## Phase 2: Social Media Integration

1. **Platform Connection Service**
   ```typescript
   // services/socialMedia.ts
   interface PlatformConfig {
     platform: string;
     apiKey: string;
     apiSecret: string;
   }

   class SocialMediaService {
     private platforms: Map<string, PlatformConfig>;

     constructor() {
       this.platforms = new Map();
       this.initializePlatforms();
     }

     private async initializePlatforms() {
       const platforms = ['twitter', 'facebook', 'linkedin'];
       
       for (const platform of platforms) {
         this.platforms.set(platform, {
           platform,
           apiKey: process.env[`${platform.toUpperCase()}_API_KEY`]!,
           apiSecret: process.env[`${platform.toUpperCase()}_API_SECRET`]!
         });
       }
     }

     async fetchPosts(platform: string, profileId: string) {
       const config = this.platforms.get(platform);
       if (!config) throw new Error(`Platform ${platform} not configured`);

       const client = await this.getClient(platform);
       return await client.getPosts(profileId);
     }

     async publishPost(platform: string, content: string, mediaUrl?: string) {
       const client = await this.getClient(platform);
       return await client.createPost({ content, mediaUrl });
     }

     async getAnalytics(platform: string, profileId: string, startDate: Date, endDate: Date) {
       const client = await this.getClient(platform);
       return await client.getMetrics(profileId, startDate, endDate);
     }
   }
   ```

2. **Content Analysis Service**
   ```typescript
   // services/contentAnalysis.ts
   class ContentAnalysisService {
     private openai: OpenAIApi;

     constructor() {
       this.openai = new OpenAIApi(new Configuration({
         apiKey: process.env.OPENAI_API_KEY
       }));
     }

     async analyzeSentiment(text: string): Promise<number> {
       const prompt = `
         Analyze the sentiment of this social media post:
         "${text}"
         Return a single number between -1 (negative) and 1 (positive).
       `;

       const completion = await this.openai.createChatCompletion({
         model: "gpt-3.5-turbo",
         messages: [{ role: "user", content: prompt }]
       });

       return parseFloat(completion.data.choices[0].message.content);
     }

     async suggestHashtags(text: string): Promise<string[]> {
       const prompt = `
         Suggest relevant hashtags for this social media post:
         "${text}"
         Return only hashtags, separated by commas.
       `;

       const completion = await this.openai.createChatCompletion({
         model: "gpt-3.5-turbo",
         messages: [{ role: "user", content: prompt }]
       });

       return completion.data.choices[0].message.content.split(',').map(tag => tag.trim());
     }
   }
   ```

## Phase 3: Core Features

1. **Post Management**
   ```typescript
   // server/api/routers/post.ts
   export const postRouter = createTRPCRouter({
     create: protectedProcedure
       .input(z.object({
         content: z.string(),
         platforms: z.array(z.string()),
         mediaUrl: z.string().optional(),
         scheduledFor: z.date().optional(),
       }))
       .mutation(async ({ ctx, input }) => {
         const sentiment = await ctx.contentAnalysis.analyzeSentiment(input.content);
         const hashtags = await ctx.contentAnalysis.suggestHashtags(input.content);

         const post = await ctx.prisma.post.create({
           data: {
             content: `${input.content} ${hashtags.join(' ')}`,
             platforms: input.platforms,
             mediaUrl: input.mediaUrl,
             scheduledFor: input.scheduledFor,
             sentiment,
             userId: ctx.session.user.id,
           },
         });

         if (!input.scheduledFor) {
           for (const platform of input.platforms) {
             await ctx.socialMedia.publishPost(
               platform,
               post.content,
               post.mediaUrl
             );
           }
         }

         return post;
       }),
   });
   ```

2. **Analytics Dashboard**
   ```typescript
   // components/AnalyticsDashboard.tsx
   interface AnalyticsProps {
     startDate: Date;
     endDate: Date;
     profiles: SocialProfile[];
   }

   const AnalyticsDashboard: React.FC<AnalyticsProps> = ({
     startDate,
     endDate,
     profiles,
   }) => {
     const analytics = api.analytics.getMetrics.useQuery({
       startDate,
       endDate,
       profileIds: profiles.map(p => p.id),
     });

     return (
       <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
         <MetricsCard
           title="Engagement Rate"
           value={analytics.data?.avgEngagement}
           trend={analytics.data?.engagementTrend}
         />
         <MetricsCard
           title="Sentiment Score"
           value={analytics.data?.avgSentiment}
           trend={analytics.data?.sentimentTrend}
         />
         <MetricsCard
           title="Post Reach"
           value={analytics.data?.totalReach}
           trend={analytics.data?.reachTrend}
         />
         <EngagementChart data={analytics.data?.engagementOverTime} />
         <SentimentChart data={analytics.data?.sentimentOverTime} />
         <PlatformComparison data={analytics.data?.platformMetrics} />
       </div>
     );
   };
   ```

## Phase 4: UI Implementation

1. **Content Calendar**
   ```typescript
   // components/ContentCalendar.tsx
   interface CalendarProps {
     posts: Post[];
     onPostClick: (post: Post) => void;
   }

   const ContentCalendar: React.FC<CalendarProps> = ({ posts, onPostClick }) => {
     const [selectedDate, setSelectedDate] = useState<Date>(new Date());
     const calendar = useCalendarGrid(selectedDate);

     return (
       <div className="bg-white rounded-lg shadow">
         <div className="flex justify-between p-4">
           <button
             onClick={() => setSelectedDate(subMonths(selectedDate, 1))}
             className="btn btn-ghost"
           >
             <ChevronLeft className="h-5 w-5" />
           </button>
           <h2 className="text-lg font-semibold">
             {format(selectedDate, 'MMMM yyyy')}
           </h2>
           <button
             onClick={() => setSelectedDate(addMonths(selectedDate, 1))}
             className="btn btn-ghost"
           >
             <ChevronRight className="h-5 w-5" />
           </button>
         </div>
         <div className="grid grid-cols-7 gap-px bg-gray-200">
           {calendar.map((day, i) => (
             <div
               key={i}
               className={cn(
                 'bg-white p-2 min-h-[100px]',
                 isToday(day) && 'bg-blue-50'
               )}
             >
               <div className="font-medium text-sm">
                 {format(day, 'd')}
               </div>
               {posts
                 .filter(post => isSameDay(parseISO(post.scheduledFor), day))
                 .map(post => (
                   <PostPreview
                     key={post.id}
                     post={post}
                     onClick={() => onPostClick(post)}
                   />
                 ))}
             </div>
           ))}
         </div>
       </div>
     );
   };
   ```

2. **Platform Connection UI**
   ```typescript
   // components/PlatformConnect.tsx
   const PlatformConnect: React.FC = () => {
     const { data: session } = useSession();
     const [connecting, setConnecting] = useState(false);
     
     const connectPlatform = async (platform: string) => {
       setConnecting(true);
       try {
         const result = await signIn(platform, {
           callbackUrl: `/dashboard/connect/${platform}`,
         });
         if (result?.error) {
           toast.error(`Failed to connect ${platform}`);
         }
       } finally {
         setConnecting(false);
       }
     };

     return (
       <div className="space-y-4">
         <h2 className="text-xl font-semibold">Connect Platforms</h2>
         <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
           {['twitter', 'facebook', 'linkedin'].map(platform => (
             <button
               key={platform}
               onClick={() => connectPlatform(platform)}
               disabled={connecting}
               className={cn(
                 'p-4 rounded-lg border-2 flex items-center gap-2',
                 connecting && 'opacity-50 cursor-not-allowed'
               )}
             >
               <PlatformIcon platform={platform} className="h-6 w-6" />
               <span className="capitalize">{platform}</span>
             </button>
           ))}
         </div>
       </div>
     );
   };
   ```

## Phase 5: Advanced Features

1. **Automated Scheduling**
   ```typescript
   // services/scheduler.ts
   class ContentScheduler {
     private async analyzeOptimalTimes(
       profileId: string,
       platform: string
     ): Promise<Date[]> {
       const analytics = await prisma.analytics.findMany({
         where: {
           profileId,
           date: {
             gte: subDays(new Date(), 30),
           },
         },
         orderBy: {
           metrics: 'desc',
         },
         take: 10,
       });

       return analytics.map(a => {
         const metrics = a.metrics as Record<string, any>;
         return new Date(metrics.peak_engagement_time);
       });
     }

     async scheduleContent(
       content: string,
       platforms: string[],
       frequency: 'daily' | 'weekly'
     ) {
       const schedule: Date[] = [];

       for (const platform of platforms) {
         const optimalTimes = await this.analyzeOptimalTimes(
           platform,
           frequency
         );

         schedule.push(...optimalTimes);
       }

       return schedule.sort((a, b) => a.getTime() - b.getTime());
     }
   }
   ```

2. **Trend Analysis**
   ```typescript
   // services/trendAnalyzer.ts
   class TrendAnalyzer {
     async analyzeTrends(posts: Post[]): Promise<TrendReport> {
       const wordFrequency = new Map<string, number>();
       const sentimentTrend = [];
       const engagementPattern = [];

       for (const post of posts) {
         // Analyze word frequency
         const words = post.content.toLowerCase().split(/\s+/);
         for (const word of words) {
           wordFrequency.set(word, (wordFrequency.get(word) || 0) + 1);
         }

         // Track sentiment over time
         sentimentTrend.push({
           date: post.publishedAt,
           sentiment: post.sentiment,
         });

         // Analyze engagement patterns
         const engagement = post.engagement as Record<string, number>;
         engagementPattern.push({
           date: post.publishedAt,
           engagement: engagement.total,
         });
       }

       // Find trending topics
       const trendingWords = Array.from(wordFrequency.entries())
         .sort((a, b) => b[1] - a[1])
         .slice(0, 10);

       return {
         trendingTopics: trendingWords,
         sentimentTrend,
         engagementPattern,
       };
     }
   }
   ```

## Common Issues & Solutions

1. **API Rate Limits**
   - Issue: Hitting social media API rate limits
   - Solution: Implement request queuing and rate limiting

2. **Data Synchronization**
   - Issue: Keeping local data in sync with social platforms
   - Solution: Use webhooks and background jobs

3. **Authentication**
   - Issue: Token         