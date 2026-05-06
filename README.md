# Mosquitto Remote Access Setup on Windows

## Problem
`mosquitto_sub` and `mosquitto_pub` worked only with:

```bash
127.0.0.1
```

But failed with Wi-Fi/LAN IP:

```bash
192.168.0.22
```

Error:

```text
Error: No connection could be made because the target machine actively refused it.
```

---

# Root Cause

Mosquitto v2.x starts in:

```text
Starting in local only mode.
```

when no listener is configured.

This allows connections only from localhost (`127.0.0.1`).

---

# Solution

## 1. Create Config File

Create:

```text
C:\mosquitto\mosquitto.conf
```

Add:

```conf
listener 1883
allow_anonymous true
```

---

# 2. Start Mosquitto Using Config

Do NOT use:

```bash
mosquitto -v
```

because it starts in local-only mode.

Use:

```bash
mosquitto -c C:\mosquitto\mosquitto.conf -v
```

---

# 3. Verify Broker Started Correctly

Expected output:

```text
Opening ipv4 listen socket on port 1883.
```

You should NOT see:

```text
Starting in local only mode
```

---

# 4. Verify Port Listening

Run:

```bash
netstat -an | findstr 1883
```

Expected:

```text
0.0.0.0:1883
```

or

```text
192.168.0.22:1883
```

NOT:

```text
127.0.0.1:1883
```

---

# 5. Test MQTT

## Subscriber

```bash
mosquitto_sub -h 192.168.0.22 -t test -v
```

## Publisher

```bash
mosquitto_pub -h 192.168.0.22 -t test -m "hello"
```

Expected subscriber output:

```text
test hello
```

---

# 6. Windows Firewall (If Needed)

If remote devices still cannot connect:

- Open Windows Defender Firewall
- Advanced Settings
- Inbound Rules
- New Rule
- Port
- TCP
- 1883
- Allow Connection

---

# Useful Commands

## Check IP Address

```bash
ipconfig
```

## Check Port Usage

```bash
netstat -an | findstr 1883
```

## Kill Existing Mosquitto Process

```bash
taskkill /IM mosquitto.exe /F
```

---

# Components

| Command | Purpose |
|---|---|
| `mosquitto.exe` | MQTT Broker/Server |
| `mosquitto_pub.exe` | Publisher Client |
| `mosquitto_sub.exe` | Subscriber Client |

---

# Final Working Command

```bash
mosquitto -c C:\mosquitto\mosquitto.conf -v
```

Config:

```conf
listener 1883
allow_anonymous true
```
