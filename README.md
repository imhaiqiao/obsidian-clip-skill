# Obsidian Clip

One-command web clipping to Obsidian using natural language.

## Prerequisites

### Required

| Software | Notes |
|----------|-------|
| **Obsidian** | Local note-taking app, any recent version |
| **Google Chrome** | Chrome only — Safari/Firefox not supported |
| **Obsidian Web Clipper** (Chrome Extension) | [Install from Chrome Web Store](https://chrome.google.com/webstore/detail/obsidian-web-clipper/ofdgopchbanhlkickijgiamfgkxinbbm) |

### Recommended

| Plugin | Notes |
|--------|-------|
| **Local Images Plus** (Obsidian community plugin) | Auto-downloads remote images locally, prevents link rot |

---

## Install the Skill in OpenClaw

### Via npx (Recommended)

```bash
npx --yes skills add your-username/your-repo
```

### Verify the skill is loaded:

```bash
openclaw status
# or
openclaw skills list
```

---

## Configure the Dependencies

### Step 1 — Install Obsidian Web Clipper Extension

1. Open Chrome and visit the [Chrome Web Store page](https://chrome.google.com/webstore/detail/obsidian-web-clipper/ofdgopchbanhlkickijgiamfgkxinbbm)
2. Click "Add to Chrome"
3. Look for the orange book icon 📕 in the Chrome toolbar

### Step 2 — Set the Clip Shortcut

1. Click the extension icon → click the ⚙️ settings icon (top right)
2. Find **Keyboard shortcut** settings
3. Bind the shortcut to **⌥⇧O** (Option + Shift + O)
   - This triggers "Save to Obsidian", not the generic clip action

### Step 3 — Authorize Obsidian to Receive Clips

1. On first clip, the extension will prompt for vault authorization
2. Select your Obsidian vault
3. Confirm saving to the **Clippings** folder (or your preferred default)

### Step 4 (Optional) — Install Local Images Plus

1. Open Obsidian → Settings → Community Plugins
2. Search "Local Images Plus"
3. Install and enable
4. Remote images in clipped notes will auto-download to `Clippings/attachments/`

---

## Usage

### Trigger Phrases

The skill activates when a message contains a URL **plus** one of these trigger words:

```
保存这个网页 → saves https://example.com
保存到 Obsidian → saves https://example.com
clip this page → saves https://example.com
收藏到 Obsidian → saves https://example.com
clip this article → saves https://example.com
save to Obsidian → saves https://example.com
```

**How it works:** OpenClaw matches your message against the `description` field in `SKILL.md` and automatically invokes this skill.

### Common Use Cases

- WeChat public accounts (微信公众号)
- Long-form articles (知乎, 少数派, Medium, etc.)
- Tech blogs and documentation
- Any webpage

---

## How It Works

```
User sends URL + trigger phrase
        ↓
  OpenClaw identifies skill
        ↓
  Open target page (Chrome, profile=openclaw)
        ↓
  Wait for page to fully load
        ↓
  Auto-scroll page (triggers lazy-loaded images)
        ↓
  Bring Chrome to foreground
        ↓
  Press ⌥⇧O shortcut
        ↓
  Obsidian Web Clipper saves Markdown to vault
        ↓
  Return confirmation to user
```

---

## Saved File Location

Clipped notes go to the `Clippings/` folder in your vault:

```
Your Vault/
└── Clippings/
    ├── your-article-title.md          ← Note content (Markdown)
    └── attachments/                    ← Downloaded images
        ├── image-001.png
        └── image-002.jpg
```

---

## Known Limitations

- **Chrome only** — Safari, Firefox, and other browsers are not supported
- **Content quality depends on the source site** — some sites have anti-scraping measures or heavy JS rendering
- **Shortcut conflicts** — if ⌥⇧O is claimed by another app (e.g., Alfred, Raycast), clipping will fail. Either change the shortcut or close the conflicting app
- **WeChat articles** — WeChat public accounts have anti-crawler protections; images may not auto-download. Installing Local Images Plus is strongly recommended

---

## Publishing (for skill authors)

Just push `obsidian-clip-skill/` to a public GitHub repo. That's it — no registry, no login.

Users install via:
```bash
npx --yes skills add your-username/your-repo
```

---

## Troubleshooting

### Shortcut not responding

1. Verify ⌥⇧O is correctly set in the Web Clipper extension settings
2. Make sure Chrome is the active foreground window
3. Check if another app (Alfred, Raycast, etc.) is intercepting the shortcut

### Content is incomplete or missing

1. Open the page manually in Chrome to verify it loads correctly
2. Enable the Local Images Plus plugin
3. Some sites require login to view full content — clipping will be limited in those cases

### Clip doesn't trigger (OpenClaw doesn't respond)

1. Confirm your message contains **both a URL and a trigger word** (e.g., "保存")
2. Check that the skill folder is correctly placed at `~/.claude/skills/obsidian-clip-skill/`
3. Restart the gateway: `openclaw gateway restart`
4. Make sure Obsidian is running (background is fine)

### Skill installed but not working

1. Verify `SKILL.md` `description` field contains the trigger phrase you used
2. Confirm the folder name matches the `name` field in `SKILL.md`
3. Check OpenClaw logs for errors: `openclaw logs`
