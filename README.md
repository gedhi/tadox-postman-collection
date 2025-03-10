# TadoX API Collection

🌟 New GitHub Repository: Tado X Postman Collection 🌟

Hey Tado Community! 👋

I'm excited to share a new GitHub repository I've created: TadoX Postman Collection. This collection is designed to help you make API calls compatible with Tado X valves. 🚀

🔧 What's Included:

A comprehensive set of Postman requests for interacting with Tado X valves.
Easy-to-use examples to get you started quickly.
💡 Note: I don't have a thermostat or temperature sensors to verify other behaviors, but the calls should be easily adaptable. If you have these devices, feel free to test and tweak the collection as needed!

📄 Documentation: The documentation (README.md) is generated with AI, so there might be some errors. If you find any, please [open an issue](https://github.com/Gedhi/tadox-postman-collection/issues) for correction.

🤝 Contribute: Share the repository with anyone who might find it useful and feel free to suggest changes through [Pull Requests](https://github.com/Gedhi/tadox-postman-collection/pulls). Let's make this collection even better together!

Happy coding! 💻✨


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

~~Make sure to configure the following environment variables in Postman:~~

~~- `TADO_USERNAME`: Your Tado account username.~~
~~- `TADO_PASSWORD`: Your Tado account password.~~

It is no longer necessary to configure these environment variables as Tado has changed the login logic. For more information, please refer to this [link](https://support.tado.com/en/articles/8565472-how-do-i-authenticate-to-access-the-rest-api).


## Workflow

The workflow always starts with the `Login` request (firt time).
Call `Login`, authorize the device with your Tado credentials in a browser (user `verification_uri_complete` from `Login`), and then call the `Get Token`.
The token is automatically set in the collection and has a duration of 10 minutes (600 seconds). After that, call `Get Homes` to retrieve the home ID, which can then be used to make other requests.

For APIs with parameters such as `roomId` or `deviceId`, the values should be retrieved from the `Get Rooms` or `Get Rooms and Devices` requests.

## Additional Resources

For those using Python, you might find the [PyTado](https://github.com/svobop/PyTado/tree/master) repository useful. It provides a comprehensive set of tools for interacting with the Tado API using Python.

You may also find [tado-openapispec-v2](https://github.com/kritsel/tado-openapispec-v2) helpful. This repository contains an OpenAPI specification for the Tado API, allowing for better understanding and interaction with Tado's services.

## API Endpoints

- [Get Login](#get-login)
- [Get Token](#get-token)
- [Refresh Token](#refresh-token)
- [Get Homes](#get-homes)
- [Get Home Data](#get-home-data)
- [Get Rooms](#get-rooms)
- [Get Features](#get-features)
- [Get Room Details](#get-room-details)
- [Get Rooms and Devices](#get-rooms-and-devices)
- [Set Manual Control](#set-manual-control)
- [Set Open Window](#set-open-window)
- [Delete Open Window](#delete-open-window)
- [Set Boost](#set-boost)
- [Resume Schedule](#resume-schedule)
- [Update Device Temperature Offset](#update-device-temperature-offset)
- [Get Weather](#get-weather)
- [Get Mobile Devices](#get-mobile-devices)
- [Get Mobile Device Settings](#get-mobile-device-settings)
- [Get Installations](#get-installations)
- [Get Devices](#get-devices)
- [Get Device Temperature Offset](#get-device-temperature-offset)
- [Get Device Humidity Offset](#get-device-humidity-offset)
- [Update Device Humidity Offset](#update-device-humidity-offset)
- [Get Day Report](#get-day-report)

### Login

**Description**: Initiates the device code grant flow to obtain a device code.

**Method**: POST

**URL**: `https://login.tado.com/oauth2/device_authorize`

**Headers**:
- `Content-Type: application/x-www-form-urlencoded`

**Body**:
```json
{
  "client_id": "1bb50063-6b0c-4d11-bd99-387f4a91cc46",
  "scope": "offline_access"
}
```
**Output**:
```json
{
  "device_code": "your_device_code",
  "user_code": "your_user_code",
  "verification_uri_complete": "https://auth.tado.com/device",
  "expires_in": 600
}
```

### Get Token

**Description**: Exchanges the device code for an access token.

**Method**: POST

**URL**: `https://login.tado.com/oauth2/token`

**Headers**:
- `Content-Type: application/x-www-form-urlencoded`

**Body**:
```json
{
  "client_id": "1bb50063-6b0c-4d11-bd99-387f4a91cc46",
  "device_code": "{{device_code}}",
  "grant_type": "urn:ietf:params:oauth:grant-type:device_code"
}
```
**Output**:
```json
{
    "access_token": "access-token",
    "expires_in": 600,
    "refresh_token": "refresh-token",
    "refresh_token_id": "refresh-token-id",
    "scope": "offline_access",
    "token_type": "Bearer",
    "userId": "user-id"
}
```

### Refresh Token

**Description**: Refreshes the access token using the refresh token.

**Method**: POST

**URL**: `https://login.tado.com/oauth2/token`

**Headers**:
- `Content-Type: application/x-www-form-urlencoded`

**Body**:
```json
{
  "client_id": "1bb50063-6b0c-4d11-bd99-387f4a91cc46",
  "grant_type": "refresh_token",
  "refresh_token": "refresh-token-from-previous-call"
}
```
**Output**:
```json
{
    "access_token": "access-token",
    "expires_in": 600,
    "refresh_token": "refresh-token",
    "refresh_token_id": "refresh-token-id",
    "scope": "offline_access",
    "token_type": "Bearer",
    "userId": "user-id"
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

**Possible values**:
- `power`: `ON`, `OFF`
- `type`: `TIMER`, `NEXT_TIME_BLOCK`, `MANUAL`

**Response code**:
```
204
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

### Get Weather

**Description**: Retrieves the weather information for a specific home.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/weather`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "solarIntensity": {
    "percentage": 50
  },
  "outsideTemperature": {
    "celsius": 15.5,
    "fahrenheit": 59.9
  }
}
```

### Get Mobile Devices

**Description**: Retrieves the list of mobile devices associated with a specific home.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/mobileDevices`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
[
  {
    "id": "123456",
    "name": "John's iPhone",
    "settings": {
      "geoTrackingEnabled": true
    }
  }
]
```

### Get Mobile Device Settings

**Description**: Retrieves the settings of a specific mobile device.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/mobileDevices/{{deviceId}}/settings`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "geoTrackingEnabled": true
}
```

### Get Installations

**Description**: Retrieves the list of installations for a specific home.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/installations`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
[
  {
    "id": "123456",
    "type": "HEATING",
    "installationDate": "2023-01-01T12:00:00Z"
  }
]
```

### Get Devices

**Description**: Retrieves the list of devices for a specific home.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/devices`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
[
  {
    "id": "123456",
    "deviceType": "VA01",
    "serialNo": "123456789",
    "shortSerialNo": "123456",
    "currentFwVersion": "54.8",
    "connectionState": {
      "value": true,
      "timestamp": "2023-10-01T12:00:00Z"
    },
    "characteristics": {
      "capabilities": ["INSIDE_TEMPERATURE_MEASUREMENT"],
      "batteryState": "NORMAL"
    }
  }
]
```

### Get Device Temperature Offset

**Description**: Retrieves the temperature offset for a specific device.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/devices/{{deviceId}}/temperatureOffset`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "celsius": 2.0,
  "fahrenheit": 3.6
}
```

### Get Device Humidity Offset

**Description**: Retrieves the humidity offset for a specific device.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/devices/{{deviceId}}/humidityOffset`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "percentage": 5
}
```

### Update Device Humidity Offset

**Description**: Updates the humidity offset for a specific device.

**Method**: PATCH

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/devices/{{deviceId}}/humidityOffset`

**Headers**:
- `Authorization: Bearer {{access_token}}`
- `Content-Type: application/json`

**Body**:
```json
{
  "humidityOffset": 2
}
```

**Output**:
```json
{
  "status": "success"
}
```

### Get Day Report

**Description**: Retrieves the day report for a specific zone.

**Method**: GET

**URL**: `https://my.tado.com/api/v2/homes/{{homeId}}/zones/{{zoneId}}/dayReport?date={{date}}&ngsw-bypass=true`

**Headers**:
- `Authorization: Bearer {{access_token}}`

**Output**:
```json
{
  "zoneType": "HEATING",
  "interval": {
    "from": "2024-12-17T22:45:00.000Z",
    "to": "2024-12-18T08:34:25.968Z"
  },
  "hoursInDay": 24,
  "measuredData": {
    "measuringDeviceConnected": {
      "timeSeriesType": "dataIntervals",
      "valueType": "boolean",
      "dataIntervals": [
        {
          "from": "2024-12-17T22:45:00.000Z",
          "to": "2024-12-18T08:34:25.968Z",
          "value": true
        }
      ]
    },
    "insideTemperature": {
      "timeSeriesType": "dataPoints",
      "valueType": "temperature",
      "min": {
        "celsius": 16.37,
        "fahrenheit": 61.47
      },
      "max": {
        "celsius": 19.76,
        "fahrenheit": 67.57
      },
      "dataPoints": [
        {
          "timestamp": "2024-12-17T22:45:00.000Z",
          "value": {
            "celsius": 18.5,
            "fahrenheit": 65.3
          }
        }
      ]
    },
    "humidity": {
      "timeSeriesType": "dataPoints",
      "valueType": "percentage",
      "min": {
        "percentage": 40
      },
      "max": {
        "percentage": 60
      },
      "dataPoints": [
        {
          "timestamp": "2024-12-17T22:45:00.000Z",
          "value": 50
        }
      ]
    },
    "heating": {
      "timeSeriesType": "dataPoints",
      "valueType": "duration",
      "dataPoints": [
        {
          "timestamp": "2024-12-17T22:45:00.000Z",
          "value": 3600
        }
      ]
    }
  }
}
```

### Get Login

**Description**: Initiates the device code grant flow to obtain a device code.

**Method**: POST

**URL**: `https://auth.tado.com/oauth/device/code`

**Headers**:
- `Content-Type: application/x-www-form-urlencoded`

**Body**:
```json
{
  "client_id": "tado-web-app",
  "scope": "home.user"
}
