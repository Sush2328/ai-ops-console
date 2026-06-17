# Deploy guide

This walks through editing the observatory in Cursor and publishing it on GitHub Pages. It assumes you already have the repo `llm-observatory` under your account `sush2328`, which is where the current live version is hosted.

## A. One-time setup (skip if already done)

1. Install Cursor from cursor.com and sign in.
2. Make sure Git is installed. In Cursor open the terminal (Ctrl+backtick) and run `git --version`. If it errors, install Git first.
3. Sign in to GitHub in the browser.

## B. Get the repo onto your machine

If you already have the repo cloned, open that folder in Cursor and skip to section C.

1. In the browser, open your `llm-observatory` repo on GitHub and copy its HTTPS clone URL.
2. In Cursor: File, Open Folder, choose where you want it, then open the terminal and run:
   ```
   git clone https://github.com/sush2328/llm-observatory.git
   cd llm-observatory
   ```
3. File, Open Folder, and select the cloned `llm-observatory` folder so Cursor is working inside it.

## C. Drop in the new files

1. Replace the existing `index.html` with the new one. Easiest path: delete the old `index.html` in the Cursor file tree, then drag the new `index.html` into the file tree. Or open the old file, select all, paste the new contents over it.
2. Add `README.md` and `DEPLOY.md` to the root of the repo the same way.
3. Save everything (Ctrl+S).

## D. Preview before you publish

Always look at it locally before pushing. Two options:

- Quickest: right click `index.html` in Cursor and open it in your browser, or just double click the file.
- Cleaner: in the Cursor terminal run `python3 -m http.server 8000`, then open `http://localhost:8000`. Click through all six tabs, drag every slider, switch workloads in the routing playground, and confirm the forecast chart redraws.

Using Cursor's AI while you are in here: select any block of code and press Ctrl+L to ask it to explain that block in plain language. Do this for the routing engine and the value model until you can narrate them without notes. That is the single best protection against the "did you actually build this" question.

## E. Publish to GitHub

In the Cursor terminal, from inside the repo:

```
git add .
git commit -m "Round two: add value and ROI, routing playground, what-if simulator"
git push origin main
```

If the push asks you to sign in, follow the browser prompt once and it will remember.

## F. Confirm GitHub Pages is serving it

1. On GitHub, open the repo, go to Settings, then Pages.
2. Source should be "Deploy from a branch", branch `main`, folder `/ (root)`. If it is not, set it and save.
3. Wait one to two minutes. Your site updates at `https://sush2328.github.io/llm-observatory/`.
4. Hard refresh the live URL (Ctrl+Shift+R) so you are not looking at a cached old version.

## G. Before the interview

- Open the live URL in a fresh browser tab and click through every tab once, on the actual machine and network you will present from.
- Have the repo open in Cursor in a second window in case they ask to see the code.
- Keep `index.html` as the only page. If you want a custom domain later, that is a Pages setting, not a code change.

## If something looks wrong

- The live site shows the old version: you are seeing cache. Hard refresh, or wait another minute for Pages to rebuild.
- A chart is blank: open the browser console (F12) and read the first red error. It will name the line. Charts are pure SVG built from the data arrays, so a blank chart almost always means a data array was edited into a bad shape.
- "Powered by Claude" style live API features will not work here. GitHub Pages is static and has no server to hold a key. If you ever want a real model call, that needs a small serverless function on Vercel, not Pages.
