# 🎨 Generate Instagram Content from Top Trends with AI Image Generation

An automated workflow that discovers trending Instagram content, analyzes it with AI, generates unique variations, and publishes them directly to your Instagram Business Account. Combines trend discovery with generative AI to maintain a consistent, engaging Instagram presence.

![Status](https://img.shields.io/badge/status-active-success.svg)
![n8n](https://img.shields.io/badge/n8n-workflow-EA4B71?style=flat&logo=n8n)
![Instagram](https://img.shields.io/badge/Instagram-Business%20API-E4405F?style=flat&logo=instagram)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4%20Vision-412991?style=flat&logo=openai)
![Replicate](https://img.shields.io/badge/Replicate-Flux%20AI-FF6B35?style=flat)

## ✨ Features

- **Automated Trend Discovery** - Scrapes top Instagram posts from trending hashtags using RapidAPI
- **AI Content Analysis** - Uses GPT-4 Vision to analyze trending images and extract visual details
- **Smart Deduplication** - PostgreSQL database prevents recreating content from the same trending posts
- **Intelligent Caption Generation** - Creates engaging Instagram captions with relevant hashtags
- **AI Image Generation** - Generates unique, inspired images using Flux AI (Replicate)
- **One-Click Publishing** - Automatically posts to Instagram Business Account with scheduling
- **Status Monitoring** - Tracks publishing progress and sends real-time updates via Telegram
- **Scheduled Automation** - Runs on configurable schedule (default: 1 PM & 7 PM daily)
- **Error Handling** - Comprehensive Telegram notifications for each workflow stage
- **Multi-Hashtag Support** - Easily add/change trending hashtags to follow (#blender3d, #isometric, etc.)

## 🎯 What It Does

Transform trending Instagram content into your own AI-generated variations with zero manual work:

1. **Discover Trends** - Fetch top posts from your chosen hashtags
2. **Analyze Images** - Extract detailed visual descriptions using GPT-4 Vision
3. **Generate Captions** - Create engaging Instagram captions with hashtags
4. **Create Images** - Generate unique AI images inspired by trends using Flux
5. **Publish** - Post directly to Instagram Business Account
6. **Notify** - Send status updates to Telegram

Perfect for:
- **Content Creators** - Never run out of content ideas
- **Marketing Agencies** - Maintain multiple client Instagram accounts
- **Brands** - Stay on top of trending topics in real-time
- **Artists** - Explore AI-generated art variations of trending styles

## 🚀 Quick Start

### Prerequisites

Active accounts with:
- [n8n](https://n8n.io) (Cloud or self-hosted v1.0+)
- [Instagram Business Account](https://business.instagram.com)
- [RapidAPI Account](https://rapidapi.com) with Instagram Scraper API subscription
- [OpenAI API](https://platform.openai.com) key (GPT-4 Vision)
- [Replicate API](https://replicate.com) token (Flux AI model)
- [PostgreSQL Database](https://www.postgresql.org/) for trend tracking
- [Telegram Bot](https://telegram.org) for notifications
- Facebook Graph API credentials (for Instagram publishing)

### Option 1: n8n Cloud Deploy

```bash
# 1. Sign up for n8n Cloud
# 2. Import workflow JSON file
# 3. Configure all credentials (see Configuration)
# 4. Create PostgreSQL top_trends table
# 5. Activate workflow
# 6. Content will auto-post on schedule
```

### Option 2: Self-Hosted n8n

```bash
# Using Docker
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n

# Using npm
npm install n8n -g
n8n start

# Access at http://localhost:5678
```

## 📁 Project Structure

```
instagram-ai-content-generator/
├── Generate Instagram Content from Top Trends with AI.json
├── README.md
├── LICENSE
├── docs/
│   ├── setup-guide.md
│   ├── api-setup.md
│   ├── database-setup.md
│   └── customization.md
└── examples/
    ├── trending-hashtags.txt
    └── prompt-templates.md
```

## 🛠️ Installation & Setup

### Step 1: Import Workflow

1. Open your n8n instance
2. Click **"Import from File"**
3. Select `Generate Instagram Content from Top Trends with AI Image Generation.json`
4. Workflow will be imported and ready for configuration

### Step 2: Create PostgreSQL Table

Run this SQL in your PostgreSQL database:

```sql
CREATE TABLE top_trends (
  id SERIAL PRIMARY KEY,
  isposted BOOLEAN DEFAULT false,
  createdat TIMESTAMP WITHOUT TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updatedat TIMESTAMP WITHOUT TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  deletedat TIMESTAMP WITHOUT TIME ZONE,
  prompt TEXT NOT NULL,
  thumbnail_url TEXT,
  code TEXT UNIQUE,
  tag TEXT
);

-- Create index for faster lookups
CREATE INDEX idx_code ON top_trends(code);
CREATE INDEX idx_isposted ON top_trends(isposted);
```

### Step 3: Configure Credentials

#### RapidAPI (Instagram Scraper)
```
Settings → Credentials → Add Custom Credential → RapidAPI
- API Key: (from https://rapidapi.com/settings/apps)
- Free tier: 500 requests/month
- Paid tier: Variable pricing
```

Subscribe to Instagram Scraper API2:
- Go to: https://rapidapi.com/social-api1-instagram/api/instagram-scraper-api2
- Click "Subscribe"
- Choose Free or Paid plan

#### OpenAI API
```
Settings → Credentials → OpenAI API
- API Key: sk-proj-...
- Model: gpt-4o-mini (for image analysis and caption generation)
```

#### Replicate API (Flux)
```
Settings → Credentials → Custom Token
- Name: replicate_token
- Token: r8_... (from https://replicate.com/account/api-tokens)
- Model: black-forest-labs/flux-schnell
```

#### Instagram/Facebook Graph API
```
Settings → Credentials → Facebook Graph API
- Access Token: (from Facebook App)
- Business Account ID: (from Instagram Business Settings)
```

#### PostgreSQL
```
Settings → Credentials → PostgreSQL
- Host: your-db-host.com
- Port: 5432
- Database: your_database
- User: postgres_user
- Password: postgres_password
```

#### Telegram Bot
```
Settings → Credentials → Telegram
- Bot Token: (from BotFather on Telegram)
- Chat ID: (your Telegram chat ID for notifications)
```

### Step 4: Fill in Required Credentials

Edit these nodes in the workflow:

1. **"Telegram Params"** node:
```javascript
{
  "telegram_chat_id": "YOUR_TELEGRAM_CHAT_ID"
}
```

2. **"Instagram params"** node:
```javascript
{
  "instagram_business_account_id": "YOUR_INSTAGRAM_BUSINESS_ACCOUNT_ID"
}
```

3. **"Rapid Api params"** node:
```javascript
{
  "x-rapid-api-key": "YOUR_RAPIDAPI_KEY"
}
```

4. **"Replicate params"** node:
```javascript
{
  "replicate_token": "YOUR_REPLICATE_TOKEN"
}
```

### Step 5: Customize Hashtags

Edit these nodes to track your desired hashtags:

- **"get top trends on instagram #blender3d"** → Change "blender3d" to your hashtag
- **"get top trends on instagram #isometric"** → Change "isometric" to your hashtag
- Add more HTTP request nodes for additional hashtags

### Step 6: Adjust Schedule

Edit **"Schedule Trigger1"** node:

```javascript
// Default: 1:05 PM & 7:05 PM daily
{
  "rule": {
    "interval": [
      {
        "field": "cronExpression",
        "expression": "5 13,19 * * *"  // Adjust times here
      }
    ]
  }
}
```

Cron format: `minute hour day month dayofweek`
- `5 13,19 * * *` = 1:05 PM & 7:05 PM every day
- `0 10 * * 1-5` = 10 AM Monday-Friday
- `0 */4 * * *` = Every 4 hours

### Step 7: Activate Workflow

1. Click **"Active"** toggle in top-right
2. Workflow will run on the configured schedule
3. Monitor Telegram for status updates
4. Check PostgreSQL table for published posts

## 🏗️ Architecture

### Workflow Process

```
STEP 1: DISCOVER TRENDS
┌──────────────────────────────┐
│ Schedule Trigger (1 PM, 7 PM)│
└──────────────┬───────────────┘
               │
   ┌───────────┴───────────┐
   ▼                       ▼
┌─────────────────┐  ┌──────────────┐
│ Scrape #blender │  │ Scrape       │
│ 3d hashtag      │  │ #isometric   │
└────────┬────────┘  └────────┬─────┘
         │                    │
         └────────┬───────────┘
                  ▼
        ┌──────────────────┐
        │ Filter: Images   │
        │ Only (no videos) │
        └────────┬─────────┘
                 │
        ┌────────┴─────────┐
        ▼                  ▼
    ┌───────┐      ┌──────────┐
    │Merge  │──────│Loop Over │
    │Data   │      │Items     │
    └───────┘      └────┬─────┘
                        │
STEP 2: CHECK DATABASE
                        ▼
        ┌──────────────────────────┐
        │ Query PostgreSQL         │
        │ Does post code exist?    │
        └──────────┬───────────────┘
                   │
        ┌──────────┴──────────┐
        │YES: Skip            │ NO: Continue
        │(Already processed)  │
        │                     ▼
        │          ┌──────────────────┐
        │          │ Insert data into │
        │          │ top_trends table │
        │          └────────┬─────────┘
        │                   │
        │  STEP 3: ANALYZE CONTENT
        │                   ▼
        │          ┌──────────────────┐
        │          │ GPT-4 Vision:    │
        │          │ Analyze image    │
        │          │ Extract details  │
        │          └────────┬─────────┘
        │                   │
        │  STEP 4: GENERATE CAPTION
        │                   ▼
        │          ┌──────────────────┐
        │          │ GPT-4o-mini:     │
        │          │ Create caption   │
        │          │ Add hashtags     │
        │          └────────┬─────────┘
        │                   │
        │  STEP 5: GENERATE IMAGE
        │                   ▼
        │          ┌──────────────────┐
        │          │ Flux AI:         │
        │          │ Generate image   │
        │          │ Based on prompt  │
        │          └────────┬─────────┘
        │                   │
        │  STEP 6: PUBLISH
        │                   ▼
        │          ┌──────────────────┐
        │          │ Prepare Instagram│
        │          │ Media (upload)   │
        │          └────────┬─────────┘
        │                   │
        │                   ▼
        │          ┌──────────────────┐
        │          │ Check Status:    │
        │          │ FINISHED?        │
        │          └────────┬─────────┘
        │                   │
        │  STEP 7: PUBLISH MEDIA
        │                   ▼
        │          ┌──────────────────┐
        │          │ Publish Media    │
        │          │ on Instagram     │
        │          └────────┬─────────┘
        │                   │
        │                   ▼
        │          ┌──────────────────┐
        │          │ Check Final      │
        │          │ Status: PUBLISHED│
        │          └────────┬─────────┘
        │                   │
        │                   ▼
        │          ┌──────────────────┐
        │          │ Send Telegram:   │
        │          │ Success message  │
        │          └──────────────────┘
        │
        └──────────────────────────────┘
```

## 🎨 Customization

### Change Analyzed Hashtags

Edit these HTTP Request nodes:

```javascript
// Change "blender3d" to your hashtag
{
  "queryParameters": {
    "parameters": [
      {
        "name": "hashtag",
        "value": "your_hashtag_here"
      }
    ]
  }
}
```

### Customize Image Generation Prompt

Edit **"Generate image on flux"** node's JSON body:

```javascript
{
  "input": {
    "prompt": "Your custom prompt here: A highly detailed 3D isometric..."
  }
}
```

### Modify Caption Template

Edit **"Analyze Content And Generate Instagram Caption"** node:

```javascript
{
  "content": "Customize this system message to change caption style, length, or hashtag focus"
}
```

### Add More Hashtags to Track

1. Duplicate one of the HTTP request nodes
2. Change hashtag name in query parameters
3. Connect filter node to merge node
4. Workflow will now track multiple hashtags

### Adjust Post Frequency

Change cron expression in **"Schedule Trigger1"**:

```javascript
// Post 4 times daily
"5 6,12,18,0 * * *"  // 6 AM, 12 PM, 6 PM, 12 AM

// Post every 2 hours
"0 */2 * * *"  // Every 2 hours

// Post on weekdays only
"0 9 * * 1-5"  // 9 AM Mon-Fri
```

## 📊 Performance Metrics

### API Rate Limits
- **RapidAPI Instagram Scraper**: Free tier 500 requests/month (~17/day)
- **OpenAI GPT-4 Vision**: 100K tokens/month for image analysis
- **Replicate Flux**: Price per image (typically $0.01-0.05)
- **Instagram Graph API**: 200 calls/hour per app

### Processing Time
- Trend scraping: ~2 seconds per hashtag
- Image analysis: ~3-5 seconds per image
- Caption generation: ~2-3 seconds
- Image generation: ~5-10 seconds (Flux)
- Publishing: ~2-3 seconds
- **Total per post: ~15-25 seconds**

### Storage Requirements
- PostgreSQL table: ~1KB per trend post
- 100 posts tracked: ~100KB
- Image generation: Cloud-hosted (Replicate)

## 🐛 Troubleshooting

### Posts Not Publishing

```javascript
// Check:
1. Instagram Business Account ID correct
2. Facebook Graph API token valid and active
3. Account has permission to publish
4. Test in Instagram Business dashboard first

// Debug:
Look at "Prepare data on Instagram" node execution
Check Facebook error messages in Telegram
Verify account doesn't have content restrictions
```

### No Telegram Notifications

```javascript
// Verify:
1. Telegram bot created (@BotFather)
2. Chat ID is correct (get via @userinfobot)
3. You've sent /start to the bot first
4. Bot credentials configured in n8n

// Test:
Create simple HTTP request node to Telegram API
Send test message manually
Check bot permissions
```

### Database Errors

```javascript
// Common issues:
1. top_trends table not created
2. PostgreSQL credentials incorrect
3. Network firewall blocking connection
4. Insufficient permissions on database

// Solutions:
Run CREATE TABLE script manually
Test connection in n8n PostgreSQL node
Verify host/port/user/password
Add n8n IP to firewall whitelist
```

### RapidAPI Rate Limit Hit

```javascript
// Options:
1. Upgrade to paid RapidAPI plan
2. Reduce number of hashtags tracked
3. Increase time between workflow runs
4. Implement request batching

// Current:
Free tier: 500 requests/month
Each run: ~2 requests (for 2 hashtags)
Max daily runs: ~8 (with buffer)
```

### Flux Image Generation Failing

```javascript
// Check:
1. Replicate token valid and active
2. Account has sufficient credits
3. Prompt not exceeding character limits
4. Image generation service not down

// Debug:
Test Replicate API directly
Check account balance on replicate.com
Try simpler prompt first
Enable verbose logging in n8n
```

### Instagram Caption Looks Poor

```javascript
// Adjust:
1. Edit system message in caption generation node
2. Add specific guidelines (length, tone, hashtags)
3. Include examples of desired output
4. Test with different prompts

// Template:
"Create captions under 150 words, friendly tone, 
5 hashtags maximum, focus on visual elements"
```

## 💡 Enhancement Ideas

- [ ] Add hashtag rotation and randomization
- [ ] Implement content calendar scheduling
- [ ] Add Carousel/multi-image post support
- [ ] Create reels/video content automation
- [ ] Add competitor content tracking
- [ ] Build analytics dashboard for post performance
- [ ] Implement comment auto-reply bot
- [ ] Add content moderation/filtering
- [ ] Create template library for captions
- [ ] Add Instagram Stories automation
- [ ] Implement hashtag performance analytics
- [ ] Add image variation generation (multiple versions per prompt)
- [ ] Create Instagram DM automation
- [ ] Add bio and profile optimization
- [ ] Implement post scheduling for optimal engagement times

## 🔒 Security & Best Practices

### API Key Security
```javascript
// Store all API keys in n8n credentials vault
// Never hardcode sensitive values
// Rotate keys regularly
// Use separate API keys per environment
// Monitor API usage for suspicious activity
```

### Account Safety
- Use dedicated Instagram Business Account for automation
- Enable two-factor authentication
- Review app permissions regularly
- Monitor published content for quality
- Set content guidelines/filters

### Rate Limiting
- Start with low posting frequency
- Monitor API usage daily
- Implement backoff strategies
- Keep published content count reasonable

### Content Quality
```javascript
// Review before publication:
1. Always review generated captions
2. Verify images match content
3. Check hashtag relevance
4. Ensure brand alignment
5. Monitor audience reaction
```

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the project
2. Create feature branch (`git checkout -b feature/HashtagRotation`)
3. Commit changes (`git commit -m 'Add hashtag rotation'`)
4. Push to branch (`git push origin feature/HashtagRotation`)
5. Open Pull Request

## 👨‍💻 Author

**Redoanuzzaman**
- GitHub: [@redoanuzzaman](https://github.com/redoanuzzaman)
- Email: redoanuzzaman707@gmail.com
- Website: [redoan.dev](https://redoan.dev)

## 🙏 Acknowledgments

- n8n community for workflow automation
- RapidAPI for Instagram Scraper API
- OpenAI for GPT-4 Vision capabilities
- Replicate for Flux AI hosting
- Instagram for Business Graph API

## 💖 Show Your Support

Give a ⭐️ if you found this Instagram automation workflow useful!

## 🔗 Useful Links

- [n8n Docs](https://docs.n8n.io)
- [RapidAPI Instagram Scraper](https://rapidapi.com/social-api1-instagram/api/instagram-scraper-api2)
- [OpenAI Vision API](https://platform.openai.com/docs/guides/vision)
- [Replicate Flux](https://replicate.com/black-forest-labs/flux-schnell)
- [Instagram Graph API](https://developers.facebook.com/docs/instagram-graph-api)
- [Telegram Bot API](https://core.telegram.org/bots/api)

## 📊 Workflow Statistics

- **Total Nodes**: 30+
- **API Integrations**: 6 (Instagram, OpenAI, Replicate, RapidAPI, Telegram, PostgreSQL)
- **Processing Steps**: 7 (Discover → Analyze → Caption → Generate → Publish → Monitor → Notify)
- **Supported Hashtags**: Unlimited (customizable)
- **Post Frequency**: Configurable (1 to 24+ times daily)
- **Database Capacity**: Unlimited trend tracking
- **Error Handling**: Comprehensive Telegram notifications

## 🎯 Use Cases

### Content Creator
Maintain daily Instagram activity by automatically posting AI-variations of trending content in your niche.

### Marketing Agency
Manage multiple client accounts with custom hashtag tracking and automated posting.

### Ecommerce Brand
Keep product feed fresh by posting trending designs and styles related to your products.

### Art Community
Explore AI-generated art inspired by trending artistic styles in real-time.

### Tech Startup
Automatically share trending tech content with AI-generated visual variations.

---

Made with 🤖 and Creative Automation

**Last Updated:** October 2025
