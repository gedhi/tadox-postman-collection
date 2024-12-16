# TadoX API Collection

This is a collection of API requests for TadoX. The collection includes all the necessary APIs to interact with the Tado system.

## Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/Gedhi/tadox-postman-collection.git
    ```
2. Import the collection into Postman:
    - Open Postman.
    - Go to "File" > "Import".
    - Select the `TadoX API Collection.postman_collection.json` file from the cloned repository.

## Environment Variables

Make sure to configure the following environment variables in Postman:

- `TADO_USERNAME`: Your Tado account username.
- `TADO_PASSWORD`: Your Tado account password.

## Workflow

The workflow always starts with the `Login` request. The token is automatically set in the collection and has a duration of 10 minutes (600 seconds). After that, call `Get Homes` to retrieve the home ID, which can then be used to make other requests.

For APIs with parameters such as `roomId` or `deviceId`, the values should be retrieved from the `Get Rooms` or `Get Rooms and Devices` requests.

## API Endpoints

### Login

**Description**: Logs in to the Tado service and obtains an access token.

**Method**: POST

**URL**: `https://auth.tado.com/oauth/token`

**Headers**:
- `Content-Type: application/x-www-form-urlencoded`

**Body**:
```json
{
  "client_id": "tado-web-app",
  "client_secret": "wZaRN7rpjn3FoNyF5IFuxg9uMzYJcvOoQ8QWiIqS3hfk6gLhVlG57j5YNoZL2Rtc",
  "grant_type": "password",
  "scope": "home.user",
  "username": "{{TADO_USERNAME}}",
  "password": "{{TADO_PASSWORD}}"
}
```

**Output**:
```json
{
  "access_token": "your_access_token",
  "expires_in": 600
}
```

### Get Homes

**Description**: Retrieves the list of homes associated with the Tado account.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/me`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "homes": [
    {
      "id": "12345",
      "name": "Home 1",
      "address": {
        "addressLine1": "123 Main St",
        "city": "City",
        "postalCode": "12345",
        "country": "Country"
      },
      "geolocation": {
        "latitude": 12.345,
        "longitude": 67.890
      },
      "dateTimeZone": "Europe/Rome",
      "consentGrantSkippable": false
    }
  ]
}
```

### Get Home Data

**Description**: Retrieves detailed information about a specific home.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "id": "12345",
  "name": "Home 1",
  "address": {
    "addressLine1": "123 Main St",
    "city": "City",
    "postalCode": "12345",
    "country": "Country"
  },
  "geolocation": {
    "latitude": 12.345,
    "longitude": 67.890
  },
  "dateTimeZone": "Europe/Rome",
  "consentGrantSkippable": false
}
```

### Get Rooms

**Description**: Retrieves the list of rooms for a specific home.

**Method**: GET

**URL**: `https://hops.tado.com/homes/{{homeId}}/rooms?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
[
  {
    "id": 1,
    "name": "Living Room",
    "sensorDataPoints": {
      "insideTemperature": {
        "value": 21.5
      },
      "humidity": {
        "percentage": 40
      }
    },
    "setting": {
      "power": "ON",
      "temperature": 22
    },
    "manualControlTermination": {
      "type": "TIMER",
      "remainingTimeInSeconds": 1777,
      "projectedExpiry": "2024-12-15T09:04:16Z"
    },
    "boostMode": null,
    "heatingPower": {
      "percentage": 50
    },
    "connection": {
      "state": "ONLINE"
    },
    "openWindow": {
      "activated": false,
      "expiryInSeconds": 1734257931
    },
    "nextScheduleChange": {
      "start": "2023-10-01T12:00:00Z",
      "setting": {
        "power": "ON",
        "temperature": 22
      }
    },
    "nextTimeBlock": {
      "start": "2023-10-01T12:00:00Z"
    },
    "balanceControl": null
  }
]
```

### Get Features

**Description**: Retrieves the list of features available for a specific home.

**Method**: GET

**URL**: `https://hops.tado.com/homes/{{homeId}}/features?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "availableFeatures": ["openWindowDetection", "manualControl"]
}
```

### Get Room Details

**Description**: Retrieves detailed information about a specific room.

**Method**: GET

**URL**: `https://hops.tado.com/homes/{{homeId}}/rooms/{{roomId}}?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "id": 1,
  "name": "Living Room",
  "sensorDataPoints": {
    "insideTemperature": {
      "value": 21.5
    },
    "humidity": {
      "percentage": 40
    }
  },
  "setting": {
    "power": "ON",
    "temperature": 22
  },
  "manualControlTermination": {
    "type": "TIMER",
    "remainingTimeInSeconds": 1777,
    "projectedExpiry": "2024-12-15T09:04:16Z"
  },
  "boostMode": null,
  "heatingPower": {
    "percentage": 50
  },
  "connection": {
    "state": "ONLINE"
  },
  "openWindow": {
    "activated": false,
    "expiryInSeconds": 1734257931
  },
  "nextScheduleChange": {
    "start": "2023-10-01T12:00:00Z",
    "setting": {
      "power": "ON",
      "temperature": 22
    }
  },
  "nextTimeBlock": {
    "start": "2023-10-01T12:00:00Z"
  },
  "balanceControl": null
}
```

### Get Rooms and Devices

**Description**: Retrieves the list of rooms and devices for a specific home.

**Method**: GET

**URL**: `https://hops.tado.com/homes/{{homeId}}/roomsAndDevices?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "rooms": [
    {
      "roomId": 1,
      "roomName": "Living Room",
      "deviceManualControlTermination": {
        "type": "TIMER",
        "durationInSeconds": 600
      },
      "devices": [
        {
          "serialNumber": "123456789",
          "type": "VA01",
          "firmwareVersion": "54.8",
          "connection": {
            "state": "ONLINE"
          },
          "batteryState": "NORMAL"
        }
      ],
      "zoneControllerAssignable": true,
      "zoneControllers": []
    }
  ],
  "otherDevices": []
}
```

### Set Manual Control

**Description**: Sets manual control for a specific room.

**Method**: POST

**URL**: `https://hops.tado.com/homes/{{homeId}}/rooms/{{roomId}}/manualControl?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`
- `Content-Type: application/json`

**Body**:
```json
{
  "setting": {
    "power": "OFF",
    "isBoost": false,
    "temperature": null
  },
  "termination": {
    "type": "TIMER",
    "durationInSeconds": 600
  }
}
```

**Output**:
```json
{
  "status": "success"
}
```

### Set Open Window

**Description**: Activates the open window detection for a specific room.

**Method**: POST

**URL**: `https://hops.tado.com/homes/{{homeId}}/rooms/{{roomId}}/openWindow?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "status": "success"
}
```

### Delete Open Window

**Description**: Deactivates the open window detection for a specific room.

**Method**: DELETE

**URL**: `https://hops.tado.com/homes/{{homeId}}/rooms/{{roomId}}/openWindow?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "status": "success"
}
```

### Set Boost

**Description**: Activates the boost mode for a specific home.

**Method**: POST

**URL**: `https://hops.tado.com/homes/{{homeId}}/quickActions/boost?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "status": "success"
}
```

### Resume Schedule

**Description**: Resumes the schedule for a specific home.

**Method**: POST

**URL**: `https://hops.tado.com/homes/{{homeId}}/quickActions/resumeSchedule?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "status": "success"
}
```

### Update Device Temperature Offset

**Description**: Updates the temperature offset for a specific device.

**Method**: PATCH

**URL**: `https://hops.tado.com/homes/{{homeId}}/roomsAndDevices/devices/{{deviceId}}?ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`
- `Content-Type: application/json`

**Body**:
```json
{
  "temperatureOffset": 2
}
```

**Output**:
```json
{
  "status": "success"
}
```