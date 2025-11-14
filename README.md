# LiveKit Voice Agent with Next.js

A full-stack voice AI application built with [LiveKit Agents](https://docs.livekit.io/agents), [Next.js](https://nextjs.org), and [Rime TTS](https://rime.ai/). Real-time voice conversations powered by GPT-4.1 Mini, with high-quality TTS and advanced speech recognition.

- [Deploy to Render](#-deploy-to-render)
- [Features](#-features)
- [Repository Structure](#-repository-structure)
- [Quick Start with Docker](#-quick-start-with-docker)
- [Docker Commands](#-docker-commands)
- [Customization](#-customization)
- [Environment Variables](#-environment-variables)
- [Troubleshooting](#-troubleshooting)
- [Additional Documentation](#-additional-documentation)
- [Contributing](#-contributing)
- [License](#-license)

## 🚀 Deploy to Render

This project is pre-configured for one-click deployment to [Render](https://render.com/) using the included `render.yaml` blueprint.

**What you get:**
- ✅ Next.js web app with voice UI
- ✅ LiveKit voice agent service
- ✅ Auto-scaling and health checks
- ✅ Separate app and agent services
- ✅ Environment variable management

**Deployment steps:**

1. **Fork this repository to your GitHub account**

2. **Set up LiveKit Cloud:**
   - Sign up at [LiveKit Cloud](https://cloud.livekit.io/)
   - Create a new project
   - Get your API key, secret, and WebSocket URL

3. **Create environment variable group on Render:**
   - Go to [Render Dashboard](https://dashboard.render.com)
   - Create an environment group with these variables:
     - `LIVEKIT_API_KEY` - Your LiveKit API key
     - `LIVEKIT_API_SECRET` - Your LiveKit API secret
     - `LIVEKIT_URL` - Your LiveKit WebSocket URL
     - `RIME_API_KEY` - Get from [Rime.ai](https://rime.ai/)
     - `OPENAI_API_KEY` - Get from [OpenAI](https://platform.openai.com/api-keys)
     - `ASSEMBLYAI_API_KEY` - Get from [AssemblyAI](https://www.assemblyai.com/)

4. **Create a new Blueprint Instance on Render:**
   - Click "New" → "Blueprint Instance"
   - Connect your forked repository
   - Select branch (usually `main`)
   - Select the environment group you created

5. **Deploy:**
   - Click "Apply" to create all services
   - Render will:
     - Build and deploy Next.js app
     - Build and deploy LiveKit agent
     - Link services together

6. **Access your deployed app:**
   - Web App: `https://your-app-name.onrender.com`
   - Agent will auto-connect via LiveKit Cloud

**Alternative: Deploy Agent to LiveKit Cloud**

For production workloads, consider deploying the agent directly to LiveKit Cloud:

```bash
lk agent create
```

This provides managed hosting, auto-scaling, and built-in monitoring.

**Cost Estimate (Render Free Tier + LiveKit Free Tier):**
- 2 Web Services (app + agent): Free tier available
- LiveKit Cloud: Free tier includes 50GB of bandwidth/month
- Total: $0/month (with free tier limitations)

## ✨ Features

- 🎙️ **Real-time Voice Conversations** - Natural voice interactions powered by LiveKit
- 🧠 **Smart AI Agent** - Uses GPT-4.1 Mini for intelligent responses
- 🗣️ **High-Quality TTS** - Rime TTS with multiple voice options
- 🎧 **Advanced STT** - AssemblyAI for accurate speech recognition
- 🔇 **Noise Cancellation** - Built-in background noise reduction
- 🚀 **Easy Deployment** - One-click Render.com deployment + LiveKit Cloud integration

## 📁 Repository Structure

```
render-voice-agent/
├── agent/                  # LiveKit voice agent
│   ├── agent.ts           # Agent implementation (GPT-4.1 + Rime + AssemblyAI)
│   ├── AGENT_README.md    # Agent configuration and customization
│   ├── Dockerfile         # Production agent container
│   └── Dockerfile.dev     # Development agent container
├── app/                   # Next.js application
│   ├── api/              # API routes (LiveKit token generation)
│   ├── components/       # React components
│   │   └── VoiceAgent.tsx # Voice UI component
│   ├── page.tsx          # Main application page
│   └── globals.css       # Global styles
├── docker-compose.yml     # Full-stack local development (app + agent)
├── docker-compose.dev.yml # Development mode with hot reload
├── render.yaml           # Production deployment configuration (Render.com)
├── package.json          # Dependencies and scripts
└── README.md
```

## 🚀 Quick Start with Docker

**Requirements:**
- Docker & Docker Compose
- Node.js 20 or higher (for local development without Docker)
- LiveKit Cloud account ([sign up](https://cloud.livekit.io/))
- API keys for Rime, OpenAI, and AssemblyAI

**Setup:**

1. **Clone and install dependencies:**

```bash
git clone <your-repo-url>
cd render-voice-agent
npm install
```

2. **Set up LiveKit Cloud credentials:**

```bash
# Authenticate with LiveKit Cloud
lk cloud auth

# Generate credentials and save to .env.local
lk app env -w
```

This creates a `.env.local` file with:
```env
LIVEKIT_API_KEY=...
LIVEKIT_API_SECRET=...
LIVEKIT_URL=wss://...
```

3. **Add additional API keys to `.env.local`:**

```env
RIME_API_KEY=your-rime-api-key
OPENAI_API_KEY=sk-your-openai-key
ASSEMBLYAI_API_KEY=your-assemblyai-key
```

4. **Download agent model files:**

```bash
npm run agent:download
```

5. **Start all services with Docker:**

```bash
docker-compose up -d
```

This starts:
- Next.js app (port 3000)
- LiveKit voice agent

6. **Access the application:**

Open http://localhost:3000 in your browser and start a voice conversation!

## 📦 Docker Commands

**Start all services:**
```bash
docker-compose up -d
```

**Start in development mode (with hot reload):**
```bash
docker-compose -f docker-compose.dev.yml up
```

**Stop all services:**
```bash
docker-compose down
```

**View logs:**
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f app
docker-compose logs -f agent
```

**Rebuild after code changes:**
```bash
docker-compose up -d --build
```

**Run without Docker (development):**
```bash
# Terminal 1: Run the Next.js app
npm run dev

# Terminal 2: Run the LiveKit agent
npm run agent:dev
```

## 🎨 Customization

### Change the Voice

Edit `agent/agent.ts`:

```typescript
tts: new TTS({
  model: 'mistv2',
  voice: 'lagoon', // Try: rainforest, lagoon, astra
}),
```

Available voices: rainforest, lagoon, astra, and more. See [Rime TTS docs](https://docs.rime.ai/).

### Change the LLM

```typescript
llm: new inference.LLM({
  model: 'openai/gpt-4o', // Try: openai/gpt-4o, anthropic/claude-3.5-sonnet
}),
```

### Modify Agent Personality

Update the `instructions` in the `Assistant` class in `agent/agent.ts`:

```typescript
instructions: `You are a friendly and helpful voice assistant...`
```

### Tech Stack Details

**Frontend:**
- **Next.js 16** - React framework
- **LiveKit Components React** - Pre-built voice UI components
- **Tailwind CSS** - Styling

**Agent:**
- **LiveKit Agents** - Voice agent framework
- **Rime TTS** - Text-to-speech
- **OpenAI GPT-4.1 Mini** - Language model
- **AssemblyAI** - Speech-to-text
- **Silero VAD** - Voice activity detection

**Infrastructure:**
- **Docker** - Containerization
- **Render** - Cloud hosting
- **LiveKit Cloud** - Real-time communication platform

## 🔧 Environment Variables

Required environment variables (`.env.local`):

```env
# LiveKit Cloud (get with: lk cloud auth && lk app env -w)
LIVEKIT_API_KEY=...
LIVEKIT_API_SECRET=...
LIVEKIT_URL=wss://...

# API Keys
RIME_API_KEY=...          # Get from https://rime.ai/
OPENAI_API_KEY=...        # Get from https://platform.openai.com/api-keys
ASSEMBLYAI_API_KEY=...    # Get from https://www.assemblyai.com/
```

## 🐛 Troubleshooting

**1. Agent won't start:**
```bash
# Ensure all API keys are set
cat .env.local

# Download model files
npm run agent:download

# Check LiveKit Cloud authentication
lk cloud auth
```

**2. No audio in browser:**
- Ensure microphone permissions are granted
- Check browser console for errors (F12)
- Verify LiveKit Cloud connection in network tab
- Try a different browser (Chrome recommended)

**3. Docker build fails:**
```bash
# Ensure Docker is running
docker ps

# Try rebuilding from scratch
docker-compose build --no-cache

# Check Docker logs
docker-compose logs agent
```

**4. Docker port conflicts:**
```bash
# Check what's using the port
lsof -i :3000

# Change port in docker-compose.yml if needed
```

**5. Agent connection errors:**
- Verify LIVEKIT_URL is correct (should start with `wss://`)
- Check LiveKit Cloud project is active: https://cloud.livekit.io/
- Ensure API keys match the LiveKit project
- Check agent logs: `docker-compose logs agent`

**6. API key errors:**
- Verify all API keys are valid and active
- Check API key permissions and quotas
- Ensure keys are properly set in `.env.local`
- For Render deployment, verify environment variables are set in the Render dashboard

**Getting more help:**
1. Check service logs: `docker-compose logs <service-name>`
2. Verify all environment variables are set correctly
3. Ensure Docker containers are healthy: `docker-compose ps`
4. See detailed troubleshooting in `QUICK_START.md` and `DOCKER.md`
5. Join the [LiveKit Discord](https://livekit.io/discord) community

## 📚 Additional Documentation

- `QUICK_START.md` - Comprehensive getting started guide
- `agent/AGENT_README.md` - Agent configuration and customization
- `INTEGRATION_GUIDE.md` - Frontend integration details
- `DOCKER.md` - Docker deployment guide
- `RENDER_DEPLOYMENT.md` - Render deployment guide
- `DEPLOYMENT_CHECKLIST.md` - Pre-deployment checklist

**External Resources:**
- [LiveKit Documentation](https://docs.livekit.io/)
- [LiveKit Agents Guide](https://docs.livekit.io/agents)
- [Rime TTS Documentation](https://docs.rime.ai/)
- [LiveKit Cloud Dashboard](https://cloud.livekit.io/)

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

MIT
