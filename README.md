# Android_Remote_Access
This project is for educational and authorized penetration testing purposes only. Ensure compliance with all applicable laws and obtain proper authorization before use.
# RAT Project Structure

## Overview
This project implements an Android Remote Administration Tool (RAT) with a client-server architecture as specified in the architecture document.

## Project Layout

```
Project_normad/
├── Android Remote Administration (RAT) Architecture.pdf  # Architecture specification
├── PROJECT_STRUCTURE.md                                 # This file
├── Client_normad/                                       # Android client application
└── Server_normad/                                       # Python server application
```

## Client Structure (Android - Kotlin)

```
Client_normad/
├── app/
│   ├── build.gradle.kts                    # Build configuration with RAT dependencies
│   ├── src/main/
│   │   ├── AndroidManifest.xml             # Permissions and components
│   │   ├── java/com/hidden/client_normad/
│   │   │   ├── MainActivity.kt              # Minimal main activity (stealth)
│   │   │   ├── core/                        # Core RAT functionality
│   │   │   │   ├── RATService.kt           # Main background service
│   │   │   │   ├── ConnectionManager.kt    # Network communication
│   │   │   │   ├── CommandHandler.kt       # Command processing
│   │   │   │   └── DeviceInfoCollector.kt  # Device information
│   │   │   ├── modules/                     # Feature modules
│   │   │   │   ├── FileManager.kt          # File operations
│   │   │   │   ├── SMSManager.kt           # SMS operations
│   │   │   │   ├── CallManager.kt          # Call log operations
│   │   │   │   ├── ContactsManager.kt      # Contacts access
│   │   │   │   ├── LocationManager.kt      # GPS tracking
│   │   │   │   ├── CameraManager.kt        # Camera operations
│   │   │   │   ├── AudioManager.kt         # Microphone operations
│   │   │   │   └── ShellManager.kt         # Remote shell
│   │   │   ├── services/                    # Android services
│   │   │   │   ├── AccessibilityService.kt # Keylogger service
│   │   │   │   └── DeviceAdminService.kt   # Device admin
│   │   │   ├── receivers/                   # Broadcast receivers
│   │   │   │   ├── BootReceiver.kt         # Boot persistence
│   │   │   │   └── SMSReceiver.kt          # SMS monitoring
│   │   │   ├── utils/                       # Utility classes
│   │   │   │   ├── PermissionManager.kt    # Runtime permissions
│   │   │   │   ├── CryptoUtils.kt          # Encryption utilities
│   │   │   │   ├── FileUtils.kt            # File operations
│   │   │   │   └── NetworkUtils.kt         # Network utilities
│   │   │   └── ui/                          # Minimal UI components
│   │   │       └── theme/                   # Compose theme
│   │   └── res/                             # Resources
│   │       ├── xml/
│   │       │   ├── accessibility_service_config.xml  # Accessibility config
│   │       │   └── device_admin.xml                  # Device admin config
│   │       └── values/
│   │           └── strings.xml              # String resources
│   └── proguard-rules.pro                   # Obfuscation rules
├── gradle/                                  # Gradle wrapper
└── build.gradle.kts                         # Project build configuration
```

## Server Structure (Python)

```
Server_normad/
├── main.py                                  # Entry point
├── requirements.txt                         # Python dependencies
├── README.md                                # Server documentation
├── src/
│   ├── core/                                # Core server components
│   │   ├── __init__.py
│   │   ├── server.py                        # Main server class ✓
│   │   ├── session.py                       # Client session management
│   │   ├── protocol.py                      # Communication protocol
│   │   └── logger.py                        # Logging utilities
│   ├── commands/                            # Command implementations
│   │   ├── __init__.py
│   │   ├── base.py                          # Base command class
│   │   ├── file_ops.py                      # File operations
│   │   ├── device_info.py                   # Device information
│   │   ├── sms_ops.py                       # SMS operations
│   │   ├── call_ops.py                      # Call operations
│   │   ├── contacts.py                      # Contacts operations
│   │   ├── location.py                      # GPS location
│   │   ├── camera.py                        # Camera operations
│   │   ├── audio.py                         # Audio operations
│   │   ├── keylogger.py                     # Keylogger operations
│   │   └── shell.py                         # Remote shell
│   ├── cli/                                 # CLI interface
│   │   ├── __init__.py
│   │   ├── interface.py                     # Main CLI interface
│   │   └── commands.py                      # CLI command handlers
│   └── utils/                               # Utilities
│       ├── __init__.py
│       ├── config.py                        # Configuration management
│       ├── crypto.py                        # Encryption utilities
│       ├── database.py                      # Database operations
│       └── helpers.py                       # Helper functions
├── config/
│   └── server_config.json                   # Server configuration ✓
├── logs/                                    # Log files directory
├── downloads/                               # Downloaded files from clients
├── uploads/                                 # Files to upload to clients
└── data/                                    # Database and persistent data
```

## Key Features Implemented

### Android Client Features:
- ✅ **Project Structure**: Complete package structure with proper naming
- ✅ **Permissions**: All required permissions in AndroidManifest.xml
- ✅ **Dependencies**: RAT-specific dependencies in build.gradle.kts
- ✅ **Core Service**: RATService.kt for background operation
- ✅ **Connection Management**: ConnectionManager.kt for server communication
- ⏳ **Command Processing**: CommandHandler.kt (to be implemented)
- ⏳ **Feature Modules**: File, SMS, Camera, etc. managers (to be implemented)
- ⏳ **Stealth Features**: Boot persistence, icon hiding (to be implemented)

### Server Features:
- ✅ **Project Structure**: Complete Python package structure
- ✅ **Main Server**: RATServer class with multi-client support
- ✅ **Configuration**: JSON-based configuration system
- ✅ **Dependencies**: All required Python packages
- ⏳ **Session Management**: ClientSession class (to be implemented)
- ⏳ **Protocol Handler**: Communication protocol (to be implemented)
- ⏳ **CLI Interface**: Interactive command interface (to be implemented)
- ⏳ **Command System**: Modular command implementations (to be implemented)

## Architecture Compliance

The project structure follows the architecture document specifications:

1. **Client-Server Model**: ✅ Separate Android client and Python server
2. **TCP Communication**: ✅ Socket-based communication planned
3. **Multi-Session Support**: ✅ Server designed for multiple clients
4. **Foreground Service**: ✅ Android service with notification
5. **Permission Handling**: ✅ All required permissions declared
6. **Stealth Features**: ✅ Structure ready for implementation
7. **Command Interface**: ✅ Modular command system planned

## Next Steps

1. **Complete Core Implementation**:
   - Implement CommandHandler.kt
   - Implement ClientSession.py
   - Implement ProtocolHandler.py

2. **Feature Modules**:
   - File operations
   - SMS/Call management
   - Camera/Audio capture
   - Location tracking
   - Keylogging

3. **CLI Interface**:
   - Interactive session management
   - Command execution
   - File transfer interface

4. **Security & Stealth**:
   - Encryption implementation
   - Obfuscation setup
   - Persistence mechanisms

## Development Notes

- **Target Android**: API 26+ (Android 8.0+) as per architecture
- **Python Version**: 3.8+ for server compatibility
- **Security**: All sensitive operations require runtime permissions
- **Stealth**: Minimal UI, disguised notifications, hidden launcher icon
- **Persistence**: Boot receiver and sticky service for auto-restart

## Important Disclaimers

This project is for educational and authorized penetration testing purposes only. Ensure compliance with all applicable laws and obtain proper authorization before use.
