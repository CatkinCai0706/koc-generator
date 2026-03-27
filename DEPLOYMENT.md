# KOC Generator Deployment

## Project Identity

- Git repo: `https://github.com/CatkinCai0706/koc-generator`
- Project type: single-file static page
- Main file: [index.html](/Users/catkinchoi/koc-generator/index.html)
- Online URL: `http://101.43.187.45/koc-generator/`

## Important Boundary

This project is deployed independently.

Do not assume it belongs to `ai_headhunter`.
Do not deploy it through `/opt/ai_headhunter`.
Do not modify `ai_headhunter` nginx, docker-compose, certbot, or cron when updating this project.

This matters because `wxk` has a separate KOC domain / HTTPS setup already running, including:

- dedicated KOC routing
- Let's Encrypt certificates
- certbot webroot
- renewal scripts
- cron

Updating `koc-generator` must not overwrite or simplify that setup.

## Server

- Host: `101.43.187.45`
- SSH user: `ubuntu`
- SSH port: `2222`

Password is intentionally not stored in git.
Use the password provided out of band by the user.

## Actual Server Path

- Deployment directory: `/opt/koc-generator`
- Deployed file: `/opt/koc-generator/index.html`

This project is currently served as a static file deployment.

## Current Serving Shape

- The project is live as a static `index.html`
- Updating this project usually means replacing `/opt/koc-generator/index.html`
- This does **not** require editing nginx as long as the serving path remains unchanged

## Safe Update Workflow

Use this workflow by default for both Codex and Claude Code.

1. Update local repo file: `index.html`
2. Validate basic script syntax locally if needed
3. Commit and push to `CatkinCai0706/koc-generator`
4. Upload the new file to the server, preferably to a temp path first
5. Backup the current server file
6. Replace `/opt/koc-generator/index.html`
7. Verify `http://101.43.187.45/koc-generator/`

## Recommended Commands

Local git:

```bash
cd /Users/catkinchoi/koc-generator
git status
git add index.html DEPLOYMENT.md
git commit -m "Describe change"
git push origin main
```

Server upload and deploy:

```bash
scp -P 2222 index.html ubuntu@101.43.187.45:/tmp/koc-generator-index.html
ssh -p 2222 ubuntu@101.43.187.45
sudo cp /opt/koc-generator/index.html /opt/koc-generator/index.html.bak-$(date +%Y%m%d-%H%M%S)
sudo mv /tmp/koc-generator-index.html /opt/koc-generator/index.html
sudo chown root:root /opt/koc-generator/index.html
sudo chmod 644 /opt/koc-generator/index.html
```

Verification:

```bash
curl -s http://101.43.187.45/koc-generator/ | head
```

## What Not To Do

Do not do these unless the user explicitly asks and the change is necessary:

- do not deploy through `/opt/ai_headhunter`
- do not reuse `ai_headhunter` deploy scripts for this project
- do not overwrite nginx configs related to `koc-parttime.bangpin.online`
- do not change `/etc/letsencrypt` mounts
- do not remove certbot webroot config
- do not replace multi-domain nginx with a single default server
- do not touch KOC 443 / HTTPS config just to update this static page

## Quick Rule

If the task is “update the KOC generator page”, the default assumption is:

- edit `koc-generator/index.html`
- push to `CatkinCai0706/koc-generator`
- replace `/opt/koc-generator/index.html`
- leave nginx / HTTPS / certbot alone
