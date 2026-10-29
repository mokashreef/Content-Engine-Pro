# Getting Started Walkthrough — Content Engine Pro

This step-by-step tutorial guides you through configuring your first AI provider, setting up fallback priorities, and publishing your first comprehensive article.

---

## Step 1: Configure Your First AI Provider (100% Free Option)

Content Engine Pro supports generous free-tier providers like **Google Gemini** and **Groq Cloud**. You do not need a paid subscription or credit card to begin generating content.

1. Navigate to **Content Engine** -> **Providers (AI)** ("المزودون") in your WordPress admin sidebar.
2. Choose a primary provider:
   * **Google Gemini (Recommended)**:
     1. Visit [Google AI Studio](https://aistudio.google.com/) and create a free API key.
     2. Paste the key into the **API Key** field in the Gemini card.
     3. Click **Discover Models** and select `gemini-1.5-flash`.
     4. Set the status toggle to **Active**.
     5. Click **Save Settings**.
     6. Click **Test Connection** to verify live connectivity. A green success message will confirm response time.
   * **Groq Cloud (Ultra-Fast Inference)**:
     1. Visit [Groq Console](https://console.groq.com/) and generate a free API key.
     2. Paste the key into the Groq card.
     3. Select `llama-3.3-70b-versatile`.
     4. Set status to **Active** and click **Save Settings**.

---

## Step 2: Establish Automatic Fallback Priorities

If multiple providers are active, Content Engine Pro automatically switches to the next available provider if your primary provider hits an HTTP 429 rate limit.

1. On the **Providers (AI)** screen, locate the **Provider Priority** section.
2. Drag and drop the provider cards into your desired fallback sequence (for example: **1. Google Gemini** -> **2. Groq Cloud** -> **3. Cerebras Cloud**).
3. Click **Save Priority Order**.

---

## Step 3: Generate Your First Article

1. In the sidebar, navigate to **Content Engine** -> **Create Article** ("إنشاء مقال").
2. Fill out the editorial parameters:
   * **Topic**: Enter your subject (e.g., *"Comprehensive Guide to Modern WordPress Security Best Practices"*).
   * **Content Type**: Choose from Standard Article, Technical Tutorial, In-Depth Guide, or Opinion Piece.
   * **Target Word Count**: Choose your desired depth (e.g., 1,500 words on Pro; up to 700 words on Free).
   * **Language**: Select **العربية (Arabic)** or **English**.
   * **Tone**: Select Professional, Educational, Engaging, or Authoritative.
   * **Target Audience**: Beginners, Developers, Small Business Owners, etc.
   * **Research Notes (Optional)**: Provide specific key points, brand requirements, or factual constraints you want included.
3. Click **Start Intelligent Generation**.
4. Observe the live 6-stage progress tracker:
   * **[1/6] Planning**: Generates structured outline sections based on target word count.
   * **[2/6] Sequential Writing**: Generates each section sequentially with context memory.
   * **[3/6] Context Memory**: Injects previous section summaries into subsequent prompts.
   * **[4/6] Local Assembly**: Converts content into Gutenberg blocks natively in PHP.
   * **[5/6] SEO & Schema**: Generates meta description, focus keyword, and JSON-LD FAQ schema.
   * **[6/6] Saved**: Saves the post safely as a WordPress Draft.
5. Click **View Draft in WordPress Editor** to review your completed article.

---

## Step 4: Schedule Recurring Publishing (Pro Feature)

To put your content strategy on autopilot:

1. Navigate to **Content Engine** -> **Schedules** ("الجدولة").
2. Click **Add New Schedule Rule**.
3. Configure your publishing cadence:
   * **Schedule Name**: e.g., *"Weekly Tech Reviews"*.
   * **Target Category**: Select an existing WordPress post category.
   * **Days of the Week**: Select your publishing days (e.g., Monday, Wednesday, Friday).
   * **Post Times**: Enter target times in 24-hour format (e.g., `09:00, 15:00`).
   * **Target Word Count**: Set standard article length.
4. Click **Save Schedule**.
5. Content Engine Pro's minutely WP-Cron engine will automatically enqueue and generate drafts according to your schedule.

---

## Step 5: Build a Multi-Lesson Course Series (Pro Feature)

1. Navigate to **Content Engine** -> **Content Series** ("السلاسل التعليمية").
2. Click **Create New Course Series**.
3. Enter your overall course topic (e.g., *"Mastering Object-Oriented PHP 8"*) and specify the number of lessons (e.g., 6 lessons).
4. Click **Generate Syllabus with AI**.
5. Review the generated lesson list. You can edit lesson titles, change lesson order, or add custom notes to specific lessons.
6. Click **Approve Course and Enqueue Lessons**.
7. The engine schedules each lesson with progressive context awareness, ensuring lessons reference concepts introduced in earlier lessons.

