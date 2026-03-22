---
name: obsidian-clip-skill
description: Clip any webpage to Obsidian using the Obsidian Web Clipper Chrome extension. Use when user provides a URL and wants to save it to Obsidian. Triggered by phrases like "clip to Obsidian", "save to Obsidian", "add to Obsidian", "收藏到 Obsidian", "clip this page", "save this article to Obsidian".
author: Haiqiao Liu
version: 1.0.0
createdAt: 2026-03-23
---

# Obsidian Clip

Clip a webpage to Obsidian via the Obsidian Web Clipper Chrome extension.

## Workflow

### Step 1 — Open URL

```javascript
browser.open(url, profile="openclaw")
```

### Step 2 — Wait for `document.readyState === 'complete'`

```javascript
browser.act(targetId, {
  fn: () => {
    if (document.readyState !== 'complete') {
      return new Promise(r => window.addEventListener('load', r, {once: true}));
    }
  }
})
```

### Step 3 — Scroll to bottom (trigger lazy-loaded images)

```javascript
browser.act(targetId, {
  fn: () => {
    const delay = ms => new Promise(r => setTimeout(r, ms));
    const scrollStep = 800;
    const scrollDelay = 500;
    let totalHeight = 0;
    const maxScroll = document.body.scrollHeight;
    async function scrollPage() {
      while (totalHeight < maxScroll) {
        window.scrollBy(0, scrollStep);
        totalHeight += scrollStep;
        await delay(scrollDelay);
      }
      window.scrollTo(0, 0);
    }
    return scrollPage();
  }
})
```

### Step 4 — Trigger clip shortcut

```applescript
osascript -e '
  tell application "Google Chrome" to activate
  delay 0.5
  tell application "System Events"
    keystroke "o" using {option down, shift down}
  end tell
'
```

### Step 5 — Confirm save

Wait ~3 seconds, then check the note was created in the Obsidian Clippings vault folder. Extract the note title and image count, then reply:

```
✅ {note title}.md has been saved to your Obsidian vault, containing {N} images.
```

## Notes

- Requires the **Obsidian Web Clipper** Chrome extension installed and enabled
- **Important:** The ⌥⇧O shortcut must be configured in the extension settings to trigger "Save" / "Add to Obsidian"
- Obsidian must be installed and running (it will receive the clip)
- The Chrome window must be the frontmost application for the shortcut to register
- Works universally across all pages; scroll step ensures lazy-loaded images are triggered
- Notes save to the Clippings folder (or your configured default folder)
- **Tip:** It is recommended to install the **Obsidian Local Images Plus** community plugin so that remote image URLs are automatically downloaded and stored locally within your Obsidian vault.
