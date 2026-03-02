# Ollama Management Guide

This guide explains how to manage the Ollama service using the OpenClaw Ansible playbook.

## 🚀 Quick Command Reference

All commands should be run from the repository root:

| Action | Command |
|--------|---------|
| **Install / Reconfigure** | `./run-playbook.sh --tags ollama` |
| **Upgrade Binary** | `./run-playbook.sh --tags ollama -e ollama_update=true` |
| **Change Settings** | Edit `roles/openclaw/defaults/main.yml`, then run Reconfigure |

## 🛠 Common Operations

### 1. Reconfigure Service
Run this after changing any settings (like context length or parallel requests) in `defaults/main.yml`.

```bash
./run-playbook.sh --tags ollama
```

This will:
- Update `/etc/systemd/system/ollama.service.d/environment.conf`
- Restart the Ollama service automatically
- **Note:** This does NOT upgrade the Ollama binary.

### 2. Upgrade Ollama Version
To force an update to the latest version of Ollama:

```bash
./run-playbook.sh --tags ollama -e ollama_update=true
```

This re-runs the official install script (`curl ... | sh`), which downloads the latest binary.

### 3. Check Service Status
Verify that Ollama is running and see its logs:

```bash
sudo systemctl status ollama
# OR
sudo journalctl -u ollama -f
```

## ⚙️ Configuration Variables

These variables are in `roles/openclaw/defaults/main.yml`.

| Variable | Default | Description |
|----------|---------|-------------|
| `ollama_keep_alive` | `"-1"` | How long models stay loaded. `-1` = forever, `5m` = 5 minutes. |
| `ollama_num_parallel` | `"2"` | Number of concurrent requests allowed. |
| `ollama_context_length` | `"16384"` | Context window size (tokens). |
| `ollama_flash_attention` | `"1"` | Enable Flash Attention (faster inference). |
| `ollama_host` | `"127.0.0.1:11434"` | Bind address. **Security:** Keep as localhost. |

## 📂 File Locations

- **Service Config**: `/etc/systemd/system/ollama.service.d/environment.conf`
- **Models Directory**: `/usr/share/ollama/.ollama/models`
- **Logs**: `journalctl -u ollama`

## ❓ Troubleshooting

**"Ollama is already installed"**
- The playbook skips installation if Ollama is found. Use `-e ollama_update=true` to force a reinstall/update.

**Service won't start**
- Check logs: `sudo journalctl -u ollama -n 50`
- Ensure no other service is using port `11434`.
