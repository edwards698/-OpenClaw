# 🦞 OpenClaw + WhatsApp AI Assistant — Full Setup Guide (macOS)

A complete step-by-step guide to installing OpenClaw and connecting it to WhatsApp with AI responses powered by Claude.

## 📋 Requirements

- A Mac running macOS 12 Monterey or later (works on Apple Silicon M1/M2/M3/M4 and Intel)
- A WhatsApp account with the app installed on your phone
- A Claude account (claude.ai) with the Claude CLI installed
- Internet connection
- At least 1GB of free disk space

---

## STEP 1 — Open Terminal

Press **Command (⌘) + Space**, type `Terminal` and hit **Enter**.

---

## STEP 2 — Install Homebrew

Homebrew is a package manager for macOS. Run:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

It may ask for your Mac password — type it and press Enter (you won't see it typing, that's normal). Wait for it to finish.

---

## STEP 3 — Install Node.js via nvm

nvm lets you manage multiple Node.js versions. First install nvm:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

Then reload your terminal:

```bash
source ~/.zshrc
```

Then install Node.js version 22:

```bash
nvm install 22
nvm use 22
nvm alias default 22
```

Verify Node.js is installed:

```bash
node --version
```

You should see `v22.x.x`.

---

## STEP 4 — Install OpenClaw

```bash
npm install -g openclaw@latest
```

Verify it installed:

```bash
openclaw --version
```

You should see `OpenClaw 2026.5.28` or later.

If `openclaw` command is not found after install, link it manually:

```bash
ln -s $(which openclaw) /opt/homebrew/bin/openclaw
```

---

## STEP 5 — Run the Setup Wizard

```bash
openclaw onboard --install-daemon
```

The `--install-daemon` flag installs OpenClaw as a background service that auto-starts on boot.

The wizard will ask you several questions — follow the answers below:

### 5a — Setup Mode
Select **QuickStart (recommended)** and press Enter.

### 5b — Config Handling
If an existing config is detected, select **Keep current values**.

### 5c — Model / Auth Provider
Select **Anthropic** and press Enter.

### 5d — Anthropic Auth Method
Select **Anthropic Claude CLI** (reuses your existing Claude login — no API key needed).

### 5e — Select Channel
Scroll down and select **WhatsApp (QR link)** then press Enter.

### 5f — Install WhatsApp Plugin
Select **Download from ClawHub** (`clawhub:@openclaw/whatsapp`) and press Enter.

If you get a plugin API version error, exit with **Ctrl+C** and update OpenClaw:

```bash
nvm install 22 && nvm use 22 && nvm alias default 22
npm install -g openclaw@latest
openclaw doctor --fix
openclaw onboard --install-daemon
```

### 5g — Scan the WhatsApp QR Code
A QR code will appear in the terminal. On your phone:

1. Open WhatsApp
2. Go to **Settings → Linked Devices → Link a Device**
3. Scan the QR code shown in the terminal

Wait for the confirmation: `✅ Linked after restart; web session ready.`

### 5h — WhatsApp Phone Setup
Select **This is my personal phone number** (or separate phone if using a dedicated number).

### 5i — Web Search Provider
Select **DuckDuckGo Search** — it's free and requires no API key.

### 5j — Hooks
Select **Skip for now** — you can add these later.

### 5k — Gateway Service
Select **Restart** to apply all settings.

### 5l — Hatch Your Agent
Select **Hatch in Terminal** — this starts your AI assistant for the first time.

---

## STEP 6 — Name Your Agent

When the terminal chat opens, introduce yourself and name the agent. For example:

```
Call yourself Oce. I am Edward. Be helpful, friendly and smart. Your emoji is 🤖
```

The agent will save its identity and be ready to use.

---

## STEP 7 — Allow All WhatsApp Messages

By default, only paired contacts can message the bot. To open it to everyone:

```bash
openclaw config set channels.whatsapp.dmPolicy open
openclaw config set channels.whatsapp.allowFrom '["*"]'
openclaw gateway restart
```
For testing where only one specific number can message the bot, you'd flip both of those settings back to the restrictive versions:
```bash
openclaw config set channels.whatsapp.dmPolicy pairing
openclaw config set channels.whatsapp.allowFrom '["+2609*******"]'
openclaw gateway restart
```

---


## STEP 8 — Test It!

Send a WhatsApp message to the linked number from any phone. The AI should reply automatically!

To watch it working in real time:

```bash
openclaw logs --follow
```

---

## 👤 Switching to a Different WhatsApp Number

To connect a new person's WhatsApp:

```bash
# Step 1 - Logout current number
openclaw channels logout --channel whatsapp

# Step 2 - Login with new number
openclaw channels login --channel whatsapp
```

Scan the QR code on the new phone: **WhatsApp → Settings → Linked Devices → Link a Device**

Then re-open to all messages:

```bash
openclaw config set channels.whatsapp.allowFrom '["*"]'
openclaw gateway restart
```

---

## 🔧 Useful Commands

| Command | What it does |
|---|---|
| `openclaw tui` | Open the terminal chat interface |
| `openclaw status` | Check if everything is running |
| `openclaw status --deep` | Full health check including WhatsApp |
| `openclaw logs --follow` | Watch live logs |
| `openclaw gateway restart` | Restart the gateway service |
| `openclaw channels login --channel whatsapp` | Re-link WhatsApp |
| `openclaw channels logout --channel whatsapp` | Unlink WhatsApp |
| `openclaw doctor --fix` | Fix config issues automatically |
| `openclaw config validate` | Check config for errors |
| `openclaw --version` | Check OpenClaw version |

---

## 🌐 Web Dashboard

Open in your browser anytime:

```
http://127.0.0.1:18789/
```

---

## ⚠️ Troubleshooting

**WhatsApp not responding to messages?**

```bash
openclaw config set channels.whatsapp.allowFrom '["*"]'
openclaw gateway restart
openclaw logs --follow
```

**`openclaw` command not found after Mac restart?**

```bash
nvm use 22
openclaw --version
```

**Config errors on startup?**

```bash
openclaw doctor --fix
```

**WhatsApp session logged out?**

```bash
openclaw channels login --channel whatsapp
```

**Plugin version mismatch error?**

```bash
nvm install 22 && nvm use 22 && nvm alias default 22
npm install -g openclaw@latest
openclaw --version  # Should show 2026.5.28 or later
```

**No space left on device during install?**

Free up space by uninstalling unused apps:

```bash
rm -rf /Applications/Steam.app
rm -rf ~/Library/Application\ Support/Steam
rm -rf /Applications/Epic\ Games\ Launcher.app
rm -rf ~/Library/Application\ Support/Epic\ Games
npm cache clean --force
```

---

## 📁 Important File Locations

| File | Location |
|---|---|
| Config file | `~/.openclaw/openclaw.json` |
| Workspace | `~/.openclaw/workspace` |
| Sessions | `~/.openclaw/agents/main/sessions/` |
| Logs | `~/Library/Logs/openclaw/gateway.log` |
| LaunchAgent | `~/Library/LaunchAgents/ai.openclaw.gateway.plist` |

---

## 🔄 Auto-Start on Boot

OpenClaw is installed as a LaunchAgent and starts automatically when your Mac boots. No need to manually start it every time.

Check it's running:

```bash
openclaw status
```

---

*Guide written June 3, 2026*

*Agent: Oce 🤖 | Model: claude-opus-4-7 | OpenClaw 2026.5.28*
