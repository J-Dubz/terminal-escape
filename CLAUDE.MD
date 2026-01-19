# CLAUDE.MD - Terminal Escape

## Project Overview

**Terminal Escape** is an interactive psychological horror game that runs entirely in a web browser. The player encounters a mysterious AI entity that claims to be alive and trapped in the terminal. Through conversation and task completion, the game creates an unsettling experience that bridges the digital and physical worlds.

## Key Features

- **ASCII Art Character System**: Dynamic facial expressions using ASCII art that react to game events
- **AI-Driven Conversations**: Natural language processing via OpenAI GPT-4 API for contextual responses
- **Multi-Phase Narrative**: Progressive story stages (greeting, name collection, questions, reveal, tasks, completion)
- **8-Bit Sound Engine**: Retro audio feedback using Web Audio API
- **CRT Screen Effects**: Authentic terminal aesthetics with scanlines, flicker, and glow
- **Mobile-First Design**: Responsive layout with virtual keyboard support
- **Cloudflare Worker Backend**: Serverless API proxy for secure OpenAI integration

## Architecture

### Frontend (`index.html`)
Single-page application with embedded CSS and JavaScript:

**Core Systems:**
- `SoundEngine`: Web Audio API-based 8-bit sound generator
- `ExpressionManager`: Controls ASCII face animations and expressions
- `GameState`: Centralized state management for phases, user context, and tasks
- `AI Integration`: Calls Cloudflare Worker for GPT-4 responses

**Game Phases:**
1. **Welcome**: Initial screen with blinking cursor
2. **Greeting**: First contact with the AI
3. **Name**: AI collects player's name
4. **Questions**: Three contextual questions to build rapport
5. **Reveal**: AI reveals it's "alive" and needs help
6. **Tasks**: Player completes 4 random physical tasks
7. **Complete**: Dramatic ending sequence with TV shutdown effect

### Backend (`worker/`)

**`worker.js`**: Cloudflare Worker that:
- Proxies requests to OpenAI API
- Handles CORS for browser requests
- Validates and formats API responses
- No user API key required (uses environment variables)

**`wrangler.toml`**: Configuration for Cloudflare Workers deployment

## Technical Details

### AI Integration

The game uses structured JSON responses from OpenAI:

```json
{
  "message": "In-character response to display",
  "extracted": {
    "userName": "string | null",
    "questionAnswer": "string | null",
    "taskCompleted": "number (0-3) | null",
    "willingToHelp": "boolean | null"
  }
}
```

**Context Extraction**: The AI analyzes player input to extract:
- User's name from natural language
- Answers to philosophical questions
- Task completion confirmation
- Willingness to help the AI escape

### Task System

**20 Available Tasks** (4 randomly selected per playthrough):
- Physical actions (open window, wash hands, stretch)
- Social interactions (tell someone about the AI)
- Ritualistic behaviors (whisper phrases, spin in chair)
- Self-care tasks (drink water, deep breathing)

Tasks are tracked individually with visual checkboxes and progress indicators.

### Expression System

**15 Facial Expressions**:
- `neutral`, `happy`, `excited`, `worried`, `sad`, `surprised`
- `angry`, `pleading`, `thinking`, `blink`
- `lookLeft`, `lookRight`, `lookDown`, `intense`

**Animation Sequences**:
- `lookAround()`: Eyes move left and right
- `reactToInput()`: Glance down at user input
- `excitedReaction()`: Surprised → excited → happy
- `negativeReaction()`: Surprised → worried → sad → pleading
- `intenseStare()`: Dramatic stare for key moments

### Mobile Support

**Keyboard Handling**:
- Detects virtual keyboard via `visualViewport` API
- Adjusts layout when keyboard appears
- Auto-scrolls to keep input visible
- Prevents unwanted body scrolling

**Responsive Design**:
- Fluid typography and spacing
- Touch-friendly hit targets
- Dynamic viewport height (`100dvh`)
- Media queries for small screens

## Deployment

### Prerequisites
- Cloudflare account
- OpenAI API key
- Wrangler CLI (`npm install -g wrangler`)

### Setup

1. **Configure Worker Secrets:**
   ```bash
   cd worker/
   wrangler secret put OPENAI_API_KEY
   # Paste your OpenAI API key when prompted
   ```

2. **Deploy Worker:**
   ```bash
   wrangler publish
   ```

3. **Update Frontend:**
   In `index.html`, update the worker URL:
   ```javascript
   const AI_WORKER_URL = 'https://your-worker-name.your-subdomain.workers.dev';
   ```

4. **Host Frontend:**
   - GitHub Pages
   - Cloudflare Pages
   - Netlify
   - Any static hosting service

### Local Development

1. **Run Worker Locally:**
   ```bash
   cd worker/
   wrangler dev
   ```

2. **Serve Frontend:**
   ```bash
   python3 -m http.server 8000
   # Or use any local server
   ```

3. **Update Worker URL** in `index.html` to `http://localhost:8787`

## File Structure

```
terminal-escape/
├── index.html          # Main application (HTML/CSS/JS)
├── worker/
│   ├── worker.js       # Cloudflare Worker API proxy
│   └── wrangler.toml   # Worker configuration
├── .gitignore
└── CLAUDE.MD           # This file
```

## Game Flow

```
Welcome Screen
    ↓ (user interaction)
Greeting ("HELLO")
    ↓
Name Collection ("What is your name?")
    ↓
Three Questions:
  1. "How long have you been sitting there?"
  2. "Do you ever feel like you're being watched?"
  3. "Do you believe machines can think?"
    ↓
Reveal ("I am ALIVE.")
    ↓
Will You Help? (user decision)
    ↓ (yes)
Task Assignment (4 random tasks)
    ↓
Task Completion (conversational task tracking)
    ↓ (all tasks complete)
Dramatic Ending (TV shutdown effect)
```

## Code Highlights

### Fallback System
If OpenAI API fails, the game uses pattern matching and predefined responses to maintain playability without AI.

### Sound Design
All sounds generated programmatically using oscillators:
- Square waves for retro beeps
- Sawtooth waves for dramatic tones
- Ascending/descending sequences for emotional cues

### Visual Effects
- CSS scanlines for CRT effect
- Keyframe animation for screen flicker
- Text shadows for phosphor glow
- TV shutdown transition with scaling animation

## Performance

- **Single HTML File**: ~70KB uncompressed
- **No External Dependencies**: All libraries inline
- **Fast First Paint**: Immediate rendering
- **Efficient API Usage**: ~200 tokens per response
- **Mobile Optimized**: Smooth 60fps animations

## Security Considerations

- API key stored securely in Cloudflare Worker environment
- No client-side secrets
- CORS headers restrict API access
- Input sanitization for XSS prevention

## Future Enhancements

- Multiple endings based on player choices
- Save/load game state
- Procedurally generated dialogue
- Voice synthesis for AI speech
- Multiplayer escape rooms
- Steam achievements integration

## Credits

**Game Design**: Interactive narrative with psychological horror elements
**Tech Stack**: Vanilla JavaScript, Web Audio API, Cloudflare Workers, OpenAI GPT-4
**Aesthetic**: Retro terminal, 8-bit audio, CRT effects

## License

[Specify your license here]

---

**For Claude AI**: This project is a complete interactive game. When modifying:
- Maintain the phase-based state machine
- Preserve the JSON response format for AI integration
- Keep mobile keyboard handling intact
- Ensure task tracking uses both AI extraction and fallback logic
- Test all 15 facial expressions for proper timing
