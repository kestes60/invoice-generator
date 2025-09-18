# Power Pack Static Starter

Minimal static site pre-wired for the Power Pack flow (Claude Code in Cursor + WSL + gh + vercel).

## Use

```bash
# in WSL (Cursor terminal)
cd ~/code
unzip ppack-template-static-20250914-231927.zip -d .
cd ppack-template-static-20250914-231927

# ensure Node version and CLIs are available
nvm use

# create GitHub repo & push
git init && git add -A && git commit -m "init: static starter"
gh repo create ppack-template-static-20250914-231927 --public --source=. --remote=origin --push

# run locally or deploy
npm run dev
npm run deploy
```