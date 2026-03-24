---
name: obsidian-clip-skill
description: Clip any webpage to Obsidian using the Obsidian Web Clipper Chrome extension. Use when user provides a URL and wants to save it to Obsidian. Triggered by phrases like "clip to Obsidian", "save to Obsidian", "add to Obsidian", "收藏到 Obsidian", "clip this page", "save this article to Obsidian".
author: Haiqiao Liu
version: 1.1.0
createdAt: 2026-03-23
---

# Obsidian Clip

Clip a webpage to Obsidian via the Obsidian Web Clipper Chrome extension.

## Workflow

### Execution Rules (Mandatory)

1. Execute steps strictly in order. Do not run steps in parallel.
2. After Step 1, keep the returned `targetId` in `pageTargetId` and reuse it for all later browser actions.
3. Do not trigger the clip shortcut before Step 3 finishes successfully.
4. If any step fails, stop immediately and report the failure reason instead of continuing.

### Step 1 — Open URL and capture `targetId`

```text
browser action=open url="<url>" profile=openclaw
```

Save the returned `targetId` as `pageTargetId`.
If `targetId` is missing, run:

```text
browser action=list profile=openclaw
```

Then pick the tab whose URL matches `<url>` and set its `targetId` to `pageTargetId`.

### Step 2 — Wait for the page to be ready

```text
browser action=act kind=wait loadState=domcontentloaded targetId=<pageTargetId> timeoutMs=30000 profile=openclaw
browser action=act kind=wait loadState=networkidle targetId=<pageTargetId> timeoutMs=30000 profile=openclaw
```

### Step 3 — Scroll to bottom to trigger lazy-loaded images

Use `evaluate` and scroll on `document.scrollingElement` (fallback to `documentElement` / `body`) so the action works on more websites.

```text
browser action=act kind=evaluate targetId=<pageTargetId> timeoutMs=90000 profile=openclaw fn="async () => { const sleep = (ms) => new Promise((r) => setTimeout(r, ms)); const root = document.scrollingElement || document.documentElement || document.body; if (!root) return; let sameBottomCount = 0; let lastTop = -1; for (let i = 0; i < 120; i++) { const maxTop = Math.max(0, root.scrollHeight - window.innerHeight); const nextTop = Math.min(maxTop, root.scrollTop + 900); root.scrollTop = nextTop; window.scrollTo(0, nextTop); await sleep(350); if (nextTop === lastTop && nextTop >= maxTop) { sameBottomCount += 1; if (sameBottomCount >= 3) break; } else { sameBottomCount = 0; } lastTop = nextTop; } window.scrollTo(0, 0); await sleep(200); }"
```

### Step 4 — Trigger clip shortcut

```applescript
osascript -e '
  tell application "Google Chrome" to activate
  delay 0.8
  tell application "System Events"
    keystroke "o" using {option down, shift down}
  end tell
'
```

### Step 5 — Confirm save

Wait 2 seconds, then check whether a new note was created in the Obsidian `Clippings` folder after Step 4.
If created, extract the note title, then reply:

```
✅ {note title}.md has been saved to your Obsidian.
```

If not created, return a failure message with likely causes (shortcut conflict, extension not logged in, vault permission not granted).

## Notes

- Requires the **Obsidian Web Clipper** Chrome extension installed and enabled
- **Important:** The ⌥⇧O shortcut must be configured in the extension settings to trigger "Save" / "Add to Obsidian"
- Obsidian must be installed and running (it will receive the clip)
- The Chrome window must be the frontmost application for the shortcut to register
- Works universally across all pages; scroll step ensures lazy-loaded images are triggered
- Notes save to the Clippings folder (or your configured default folder)
- **Tip:** It is recommended to install the **Obsidian Local Images Plus** community plugin so that remote image URLs are automatically downloaded and stored locally within your Obsidian vault.
