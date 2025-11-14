# Quick Start Guide

Get your voice agent up and running in 5 minutes!

## Prerequisites

- Node.js 20+ installed
- LiveKit Cloud account (free at https://cloud.livekit.io)
- Rime API key (from https://rime.ai/)
- LiveKit CLI installed

## Step-by-Step Setup

### 1. Install LiveKit CLI

**macOS:**
```bash
brew install livekit-cli
```

**Linux:**
```bash
curl -sSL https://get.livekit.io/cli | bash
```

**Windows:**
```bash
winget install LiveKit.LiveKitCLI
```

### 2. Get LiveKit Credentials

```bash
lk cloud auth
lk app env -w
```

This automatically creates `.env.local` with your LiveKit credentials.

### 3. Add Missing Environment Variables

Edit `.env.local` and add:

```bash
# Add your Rime API key
RIME_API_KEY=your_rime_api_key_here

# Add public LiveKit URL (same as LIVEKIT_URL)
NEXT_PUBLIC_LIVEKIT_URL=wss://your-project.livekit.cloud
```

💡 **Tip**: Copy the value from `LIVEKIT_URL` and paste it into `NEXT_PUBLIC_LIVEKIT_URL`

### 4. Download Model Files

```bash
npm run agent:download
```

This downloads the AI models needed for voice activity detection and turn detection. ☕ Grab a coffee, this takes a minute!

### 5. Start the Agent (Terminal 1)

```bash
npm run agent:dev
```

You should see:
```
✓ Worker started
✓ Connected to LiveKit Cloud
```

### 6. Start the Web App (Terminal 2)

```bash
npm run dev
```

### 7. Test Your Agent

1. Open http://localhost:3000
2. Click **"Start Conversation"**
3. Allow microphone access
4. Say "Hello!" and wait for the response

🎉 **Congratulations!** Your AI voice assistant is working!

## Common Issues

### "Missing NEXT_PUBLIC_LIVEKIT_URL"

➜ **Solution**: Add this line to `.env.local`:
```
NEXT_PUBLIC_LIVEKIT_URL=wss://your-project.livekit.cloud
```
(Use the same value as `LIVEKIT_URL`)

### Agent doesn't respond

➜ **Solution**: Make sure both terminals are running:
- Terminal 1: `npm run agent:dev` (the agent)
- Terminal 2: `npm run dev` (the web app)

### No microphone access

➜ **Solution**: Click the lock icon in your browser's address bar and allow microphone access.

### "Failed to download model files"

➜ **Solution**: Run with better error output:
```bash
npm run agent:build
node agent.js download-files
```

## What's Next?

### Customize Your Agent

Edit `agent.ts` to change:
- **Voice**: Change `speaker: "rainforest"` to another Rime voice
- **Personality**: Update the `instructions` text
- **Speed**: Adjust `speedAlpha` (0.9 = faster, 1.1 = slower)

### Deploy to Production

```bash
# Deploy the web app
vercel deploy

# Deploy the agent to LiveKit Cloud
lk agent create
```

### Learn More

- 📖 [Full Integration Guide](INTEGRATION_GUIDE.md)
- 📖 [Agent Documentation](AGENT_README.md)
- 🌐 [LiveKit Docs](https://docs.livekit.io)
- 💬 [LiveKit Discord](https://livekit.io/discord)

## Quick Commands Reference

```bash
# Development
npm run dev                    # Start Next.js app
npm run agent:dev              # Start agent in dev mode

# Agent Management
npm run agent:download         # Download model files
npm run agent:build            # Build agent for production
npm run agent:start            # Run agent in production

# Deployment
vercel deploy                  # Deploy web app
lk agent create                # Deploy agent to LiveKit Cloud
```

## Architecture at a Glance

```
Browser (User)
    ↓ [speaks]
LiveKit Cloud (WebRTC)
    ↓ [audio stream]
Voice Agent (agent.ts)
    ↓ [processes with AI]
    ├─→ Speech-to-Text (AssemblyAI)
    ├─→ LLM (OpenAI GPT-4.1)
    └─→ Text-to-Speech (Rime)
    ↓ [audio stream]
LiveKit Cloud
    ↓ [audio stream]
Browser (User hears response)
```

## Pricing

**During Development:**
- LiveKit Cloud: Free tier includes 10,000 minutes/month
- Rime TTS: Via LiveKit Inference (billed through LiveKit)
- OpenAI GPT: Via LiveKit Inference (billed through LiveKit)

**For Production:**
- Check [LiveKit Pricing](https://livekit.io/pricing)
- Consider using your own API keys for Rime and OpenAI

## Get Help

Stuck? We're here to help!

1. Check [INTEGRATION_GUIDE.md](INTEGRATION_GUIDE.md) for detailed docs
2. Review [AGENT_README.md](AGENT_README.md) for agent-specific info
3. Join [LiveKit Discord](https://livekit.io/discord)
4. Check [GitHub Issues](https://github.com/livekit/agents)

---

**Ready to build something amazing?** Start chatting with your agent and explore what's possible with real-time voice AI! 🚀

