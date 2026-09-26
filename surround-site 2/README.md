# SURROUND: site source

This is the editable source for the SURROUND waitlist site. It's a static site with no build step.

## Put it in your GitHub repository (one time)
1. Unzip this folder.
2. **Replace everything in your `surround` repository with these files**: `index.html`, `support.js`, `CLAUDE.md`, `README.md` and the `assets/` folder.
   - **Website:** open the repository, then click **Add file → Upload files**. Drag in all the files *and* the `assets` folder, then click **Commit changes**. Delete any old `surround.html` if it's there.
   - **GitHub Desktop:** copy the files into your cloned folder, then **Commit to main → Push origin**.
3. GitHub Pages redeploys the same URL within about a minute.

The previous `index.html` was a single bundled file. This version is the readable source, and it needs `support.js` and `assets/` alongside it.

## Edit with Claude Code
1. Install Claude Code, then open a terminal in your cloned repository folder and run `claude`.
2. Ask for changes in plain English, for example:
   - "Change the date from [REDACTED] to 19 NOV 2026."
   - "Make the hero hand 10% bigger."
   - "Send waitlist signups to my Formspree form https://formspree.io/f/xxxx."
3. Then say **"commit and push"** and the live site updates.

Claude Code reads `CLAUDE.md` automatically, so it knows how the site is built.

## Preview locally
```
npx serve .
```
Then open http://localhost:3000.
