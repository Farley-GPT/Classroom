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
