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
