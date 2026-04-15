# Cursor Prompts — Butt Rock Taxonomy

These are copy-paste prompts for Cursor's agent mode (Cmd+I or the composer).
Run them in order. Each one builds on the previous step.

---

## PHASE 0: Project Setup

Open a terminal and run this first (outside of Cursor):

```bash
mkdir butt-rock-taxonomy
cd butt-rock-taxonomy
git init
npm init -y
```

Then open the folder in Cursor.

---

## PROMPT 1: Scaffold the base app

Paste this into Cursor's agent/composer:

```
Create a single-file `index.html` that is an interactive "Butt Rock Taxonomy" app. 
This is a scatter-plot style map of 53 rock bands plotted on two axes:
- X-axis: Heaviness/Aggression (0-100, left=soft, right=heavy)
- Y-axis: Butt Rock Factor (0-100, bottom=artistic/genre-defining, top=peak butt rock)

Requirements:
1. DESIGN: Dark theme, grungy concert-poster aesthetic. Use Google Fonts: Bebas Neue (headings), Barlow Condensed (body), Special Elite (descriptions). Gold (#d4a44a) as accent color. Noise texture overlay. No generic AI look.

2. MAP: Each band is a colored dot positioned by its x,y coordinates. Dots are colored by primary genre:
   - Butt Rock: #d4a44a
   - Post-Grunge: #c47730  
   - Nu Metal: #c44444
   - Alt Metal: #7a44aa
   - Grunge: #448866
   - Hard Rock: #cc6633
   - Metalcore: #aa3355
   - Rap Rock: #5577cc
   - Experimental: #44aaaa

3. GENRE ZONES: Semi-transparent elliptical blobs behind the dots showing where genres overlap (like a Venn diagram). Zones for: Butt Rock, Nu Metal, Post-Grunge, Grunge Origins, Alt Metal, Experimental.

4. INTERACTION: Hover a dot to show band name. Click a dot to open a detail panel at the bottom of the map showing: band name, genre tags, description text, and three horizontal meter bars (Heaviness, Butt Rock, Artistry).

5. AXIS LABELS: "Soft/Radio-Friendly" on left, "Heavy/Aggressive" on right, "Peak Butt Rock" on top, "Artistic/Genre-Defining" on bottom.

6. Include ALL 53 bands from this data array (paste the full BANDS array from below).

Use the band data from the BANDS array in the attached reference file index.html.
```

> **Note to K**: After pasting this prompt, also paste in the BANDS array and SPOTIFY object from the current `index.html` file so Cursor has the data. You can attach the file as context.

---

## PROMPT 2: Add the sidebar roster

```
Add a collapsible sidebar to the right side of the map. It should:

1. Toggle open/closed via a "☰ Roster" button fixed in the top-right corner.
2. When open, the map area shrinks (margin-right) to make room. Smooth CSS transition.
3. Sidebar contents:
   a. Header with "Full Roster" title
   b. Search input that filters bands by name or genre in real-time
   c. Sort buttons row: Tier (default), A-Z, Heavy, Butt Rock, Artistry
   d. Stats bar showing count per tier segment
   e. Scrollable band list

4. TIER SEGMENTATION — group bands into these tiers:
   - Peak Butt Rock (y >= 85) — color #d4a44a
   - Heavy Butt Rock (y 70-84, x >= 55) — color #cc6633  
   - Soft/Radio Butt Rock (y 65-84, x < 55) — color #c47730
   - The Gray Zone (y 40-69) — color #887755
   - Artistic/Heavy (y < 40, x >= 55) — color #7a44aa
   - Grunge & Origins (y < 40, x < 55) — color #448866

5. Each tier is a collapsible accordion section with a colored bar, tier name, and band count.
6. Each band row shows: colored dot, band name, genre list, and 3 tiny meter bars (H, BR, A).
7. CROSS-LINKING: Hover a band in sidebar → highlight its dot on the map (scale up + show label). Click a band in sidebar → select it on the map (show detail panel). Click a dot on map → highlight the corresponding sidebar row and scroll to it.
8. On mobile (< 768px), sidebar takes full width.
```

---

## PROMPT 3: Add Spotify embedded player

```
Add a Spotify embedded player to the band detail panel. When a band is selected:

1. Show a Spotify embed iframe below the description and meters.
2. Use this URL format: https://open.spotify.com/embed/artist/{ARTIST_ID}?utm_source=generator&theme=0
3. Size: width 300px, height 80px, border-radius 8px.
4. These are 30-second preview players — no login required.
5. Include a small hint text: "30-sec preview · no login needed"
6. When the detail panel closes, set iframe src to empty string to stop playback.
7. If a band doesn't have a Spotify ID, hide the player row entirely.

Use this SPOTIFY artist ID mapping (paste the SPOTIFY object from the reference file).
```

---

## PROMPT 4: Add Firebase for persistence

```
Add Firebase Realtime Database for persisting community data. 

1. Add the Firebase SDK via CDN script tags (use the compat version for simplicity):
   - https://www.gstatic.com/firebasejs/10.14.1/firebase-app-compat.js
   - https://www.gstatic.com/firebasejs/10.14.1/firebase-database-compat.js

2. Add a Firebase config block at the top of the script section. Use placeholder values that I'll replace:
   ```
   const firebaseConfig = {
     apiKey: "YOUR_API_KEY",
     authDomain: "YOUR_PROJECT.firebaseapp.com",
     databaseURL: "https://YOUR_PROJECT-default-rtdb.firebaseio.com",
     projectId: "YOUR_PROJECT",
     storageBucket: "YOUR_PROJECT.appspot.com",
     messagingSenderId: "YOUR_SENDER_ID",
     appId: "YOUR_APP_ID"
   };
   ```

3. Initialize Firebase and get a database reference.

4. Create these database paths:
   - /comments — array of {id, author, text, band, time}
   - /suggestions — array of {id, band, genre, by, votes, time}

5. Create helper functions:
   - loadComments() — firebase.database().ref('comments').orderByChild('time').on('value', ...) — listen for real-time updates
   - saveComment(comment) — push new comment
   - loadSuggestions() — real-time listener on 'suggestions'
   - saveSuggestion(suggestion) — push new suggestion  
   - updateVote(suggestionId, newVoteCount) — update vote count

6. Store the user's voted suggestion IDs in localStorage (key: 'buttrock-myvotes') since votes are per-device.

7. Replace any existing window.storage calls with Firebase calls.
```

---

## PROMPT 5: Add Community tab with comments and suggestions

```
Add a tab navigation system and a Community tab. 

1. TAB NAV: Add a tab bar below the header with two tabs: "Map" and "Community". 
   - Styled with Bebas Neue font, gold active state, border-bottom indicator.
   - Switching tabs shows/hides the corresponding content.
   - The sidebar toggle and genre filter buttons only show on the Map tab.

2. COMMUNITY TAB layout (scrollable, max-width 700px, centered):

   a. SUGGEST A BAND section:
      - Three inputs in a row: "Your name", "Band name", "Genre (optional)"
      - Submit button labeled "Suggest"
      - All fields required except genre
      - Below the form: suggestion list sorted by votes (highest first)
      - Each suggestion shows: upvote button with arrow + count, band name, suggested by name, genre tag
      - Upvote is a toggle — click again to remove your vote
      - User's votes tracked in localStorage
      - Votes update Firebase in real-time

   b. DISCUSSION section:
      - Comment form with:
        - Name input (REQUIRED — show error "Please enter your name to comment" if empty on submit)
        - Textarea for comment (max 500 chars with live counter)
        - Dropdown to optionally tag a band (populated from BANDS array, sorted A-Z)
        - Submit button labeled "Post"
      - Comment list below (newest first), each showing:
        - Author name (gold, uppercase)
        - Tagged band (if any, purple pill)
        - Relative timestamp ("2m ago", "1h ago", "3d ago")
        - Comment text (Special Elite font)

3. Both sections should listen for real-time Firebase updates so multiple users see changes live.
4. Empty states: show placeholder text when no comments/suggestions exist yet.
```

---

## PROMPT 6: Firebase setup (do this manually)

This isn't a Cursor prompt — it's what you do in your browser:

1. Go to https://console.firebase.google.com
2. Click "Add project" → name it `butt-rock-taxonomy` → disable Google Analytics (optional) → Create
3. In the project dashboard, click the web icon `</>` to add a web app
4. Name it anything, DON'T check "Firebase Hosting"
5. Copy the `firebaseConfig` object it shows you
6. Paste those values into your `index.html` replacing the placeholder config
7. In the left sidebar, go to **Build → Realtime Database**
8. Click **Create Database** → pick a location → Start in **test mode** (for now)
9. Your database URL will look like `https://butt-rock-taxonomy-default-rtdb.firebaseio.com`
10. Make sure this matches the `databaseURL` in your config

> **Security note**: Test mode rules expire after 30 days. Before that, update your rules to:
> ```json
> {
>   "rules": {
>     "comments": {
>       ".read": true,
>       ".write": true
>     },
>     "suggestions": {
>       ".read": true,
>       ".write": true  
>     }
>   }
> }
> ```

---

## PROMPT 7: Push to GitHub + Deploy to Vercel

Do this in your terminal:

```bash
# In your project directory
git add .
git commit -m "butt rock taxonomy v1"
git branch -M main

# Create repo on github.com first (name: butt-rock-taxonomy), then:
git remote add origin https://github.com/YOUR_USERNAME/butt-rock-taxonomy.git
git push -u origin main
```

Then in your browser:
1. Go to https://vercel.com and sign in with GitHub
2. Click "Add New" → "Project"
3. Import `butt-rock-taxonomy`
4. Framework Preset: **Other**
5. Output Directory: `.` (just a period)
6. Click **Deploy**

Your site will be live at `butt-rock-taxonomy.vercel.app` within seconds.

Every future `git push` auto-redeploys.

---

## PROMPT 8: Polish and bug fixes (optional)

```
Review the entire index.html and fix any issues:

1. Make sure the Spotify iframe stops playing when switching tabs or closing the detail panel.
2. Verify all 53 bands render on the map without overlapping labels.
3. Test that Firebase real-time listeners don't create duplicate entries when switching tabs.
4. Add a subtle CSS entrance animation — bands should fade in with a staggered delay on page load.
5. Make sure the comment name field validation shows the error message and focuses the input.
6. On mobile, the Community tab should be fully scrollable with no overflow issues.
7. Add a footer at the bottom of the Community tab: "Built with questionable taste · 2026"
```

---

## Tips for Cursor

- **Attach the reference file**: When running Prompt 1, attach the current `index.html` from Claude as context so Cursor has the band data and Spotify IDs.
- **Use agent mode** (Cmd+I → select Agent) for the bigger prompts (1-5). 
- **If Cursor gets stuck in a planning loop**: Break the prompt into two smaller pieces. For example, split Prompt 5 into "add tab navigation" and "add comment/suggestion forms" as separate prompts.
- **Test after each prompt**: Open `index.html` in your browser with Live Server to verify before moving to the next step.
- **Firebase config**: Don't commit your real API key to a public repo. For a hobby project this is fine, but for production you'd use environment variables.
