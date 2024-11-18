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