# The Butt Rock Taxonomy

An interactive genre map, community discussion board, and Spotify-powered field guide to America's guiltiest musical pleasure.

## Stack
- **Frontend**: Single-page HTML/CSS/JS (vanilla — no framework)
- **Backend**: Firebase Realtime Database (free tier) for comments, suggestions, and votes
- **Hosting**: Vercel (auto-deploys from GitHub)
- **Music**: Spotify Embed API (no auth, 30-sec previews)

## Project Structure
```
butt-rock-taxonomy/
├── index.html          # The entire app (single file)
├── firebase-config.js  # Firebase initialization (gitignored in prod, inlined for now)
├── README.md
├── CURSOR-PROMPTS.md   # Step-by-step prompts for Cursor agent mode
└── vercel.json         # Optional Vercel config
```

## Setup
1. Create a Firebase project at https://console.firebase.google.com
2. Enable Realtime Database (start in test mode)
3. Copy your Firebase config into the app
4. Push to GitHub
5. Import into Vercel → Deploy

## Features
- Interactive scatter-plot map (heaviness × butt rock factor)
- Genre zone overlays (Venn-style blobs)
- 53 bands with descriptions, genre tags, and stat meters
- Collapsible sidebar roster with search, sort, and tier segmentation
- Spotify embedded player (30-sec previews, no login)
- Community tab: comments with required name field, band tagging
- Suggestion board with upvoting
- All community data persisted via Firebase Realtime Database
