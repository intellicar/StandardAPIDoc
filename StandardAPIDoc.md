# Standard APIs
## Table of Contents
1. [Getting Started](#getting-started)
2. [Conventions](#conventions)
3. [Common API parameters defined](#common-api-parameters-defined)
4. [APIs](#apis)
    1. [Access Control](#access-control)
    2. [Administrative](#administrative)
    3. [Location Information](#location-information)
    4. [Fuel Information](#fuel-information)
    5. [Battery Information](#battery-information)

## Getting Started
- The Standard APIs are organised around REST. It accepts JSON request bodies and
    responds with JSON.
### General Response Structure
Reponse bodies conform to the following structure:
  ```json
        {
            "status": "SUCCESS|FAILURE",
            "data": {"<data>"},
            "err": "<Error-code>",
            "msg": "<Description>"
        }
  ```
- Content in angular braces (< >) signifies the actual content mentioned and
    not to be considered verbatim (including the actual datatype i.e.
    replace the content mentioned with the appropriate datatype).
- The pipe (|) symbol in the `status` part signifies an OR.
- In the event that `err` parameter is is non-null, `data` is expected to be
    null.
- `data` can be either an Object ({}) type or an Array ([]) type.

## Conventions
- All time parameters are expected to be in UTC Epoch time (milliseconds).
    - For example, the date `Monday, February 23, 2015 12:00:00 AM IST/UTC+05:30`
      would be 1424629800000 in the prescribed standard.
- The word "last" in the context of some data (example: last location status),
  refers to the last available data from an entity (vehicle). This data could
  be from a minute ago or from a year ago depending on various factors.
- Some APIs are subject to the **availability of the data**.
- These APIs are marked as **Restricted** right below the API group heading.

## Common API parameters defined
- `vehicleno` - Represents the vehicle registration number
- `starttime` - Represents the time **starting from** which the desired data is
    fetched.
- `endtime` - Represents the time **till** which the desired data is
    fetched.

## APIs
### Access Control
#### Get Token
- `JSON Web Token` is used for access control. Every API (other than this)
   expects a `token` key to be present in the request body containing the
   generated token.
- Token is generated as follows:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/gettoken \
        --header 'Content-Type: application/json' \
        --data '{"username": "<username>", "password": "<password>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": {
           "token": "<token>",
           "username": "<username>"
       },
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
 - This API is first one that needs to be used before all else in an
     application/a script.
 - If the credentials provided are incorrect, a 401 status code is returned.
 - Every token has an expiry time post which it isn't valid.

### Administrative
#### Change Password
- Password can be changed as follows:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/changepassword \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "username": "<username>", "newpassword": "<newpassword>"}'
 ```
 - `username` can refer to any user the API calling user has access to.
 - Password requirements:
     - Minimum 6 characters.
     - No whitespace.
     - Should NOT contain `username`.

#### List Vehicles
- Vehicles which are accessible to the API calling user can be listed using the
    following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/listvehicles \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": [
           {
               "vehicleno": "<vehicleno>"
           }
       ],
       "err": "<err>",
       "msg": "<msg>"
    }
 ```

#### List Users
- Users which are accessible to the API calling user can be listed using the
    following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/listusers \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": [
           {
               "username": "<username>"
           }
       ],
       "err": "<err>",
       "msg": "<msg>"
    }
 ```

#### Vehicle Information
 - Vehicle info/meta can be accessed using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getvehicleinfo \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": {
           "vehicleno": "<vehicleno>",
           "createdat": "<utc-epoch>",
           "createdby": "<username>",
           "type": "<vehicle-type>",
           "model": "<vehicle-model>",
           "assignedgroups": [
             {
               "groupname": "<groupname>"
             }
           ]
       },
       "err": "<err>",
       "msg": "<msg>"
    }
 ```

#### Bound Devices
 - The device(s) bound to a vehicle can be accessed using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getdeviceforvehicle \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": {
           "vehicleno": "<vehicleno>",
           "createdat": "<utc-epoch>",
           "createdby": "<username>",
           "type": "<vehicle-type>",
           "model": "<vehicle-model>",
           "assignedgroups": [
             {
               "groupname": "<groupname>"
             }
           ]
       },
       "err": "<err>",
       "msg": "<msg>"
    }
 ```

### Location Information
#### Last Location Status
 - Last location status can be obtained using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getlastgpsstatus \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": {
           "commtime": "<utc-epoch>",
           "lat": "<latitude>",
           "lng": "<longitude>",
           "alti": "<altitude>",
           "devbattery": "<device-battery-voltage>",
           "vehbattery": "<vehicle-battery-voltage>",
           "speed": "<speed>",
           "heading": "<heading>",
           "ignstatus": "<ignition-status>",
           "mobili": "<mobility-status>"
       },
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
#### Location History
 - Location history can be obtained using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getgpshistory \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>", "starttime": "<epoch-utc>", "endtime": "<epoch-utc>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": [
           {
               "commtime": "<utc-epoch>",
               "lat": "<latitude>",
               "lng": "<longitude>",
               "alti": "<altitude>",
               "devbattery": "<device-battery-voltage>",
               "vehbattery": "<vehicle-battery-voltage>",
               "speed": "<speed>",
               "heading": "<heading>",
               "ignstatus": "<ignition-status>",
               "mobili": "<mobility-status>"
           }
        ],
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
#### Distance Travelled
 - Distance travelled can be obtained using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getdistancetravelled \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>", "starttime": "<epoch-utc>", "endtime": "<epoch-utc>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": {
           "startodo": "<start-odometer>",
           "endodo": "<end-odometer>",
           "starttime": "<epoch-utc>",
           "endtime": "<epoch-utc>",
           "lastignon": "<epoch-utc>",
           "lastignoff": "<epoch-utc>",
           "distance": "<distance-travelled>",
           "startLoc": [
             "<start-lat>",
             "<start-lng>"
           ],
           "endLoc": [
             "<end-lat>",
             "<end-lng>"
           ]
       },
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
### Fuel Information
**Restricted/Subject to availability of CAN data**
#### Fuel History
 - Fuel history can be obtained using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getfuelhistory \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>", "inlitres": "<boolean>", "starttime": "<epoch-utc>", "endtime": "<epoch-utc>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": [
           {
               "time": "<epoch-utc>",
               "value": "<fuel-percentage>|<fuel-litres>"
           }
       ],
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
#### Fuel Used
 - Fuel used can be obtained using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getfuelused \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>", "starttime": "<epoch-utc>", "endtime": "<epoch-utc>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": {
           "fuelused": "<fuel-used-litres>",
           "starttime": "<epoch-utc>",
           "endtime": "<epoch-utc>",
           "startfl": "<fuel-percentage>",
           "endfl": "<fuel-percentage>",
           "startfl_litres": "<fuel-litres>",
           "endfl_litres": "<fuel-litres>",
           "lastignon": "<epoch-utc>",
           "lastignoff": "<epoch-utc>",
           "refueling_events": []
       },
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
#### Last Fuel Status
 - Last fuel status can be obtained using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getfuelstatus \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>"}'
 ```
- Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": {
           "fueltime": "<epoch-utc>",
           "fuellevel": "<fuel-percentage>",
           "fuellevellitres": "<fuel-litres>"
       },
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
### Battery Information
**Restricted/Subject to availability of CAN data**
#### Last Battery Metrics
 - Last Battery Metrics can be obtained using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getlastbatterymetrics \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>"}'
 ```
 - Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": {
           "soc": {
               "value": "<val>",
               "timestamp": "epoch-utc"
            },
           "battery_temp": {
               "value": "<val>",
               "timestamp": "epoch-utc"
           },
           "battery_voltage": {
               "value": "<val>",
               "timestamp": "epoch-utc"
           },
           "current": {
               "value": "<val>",
               "timestamp": "epoch-utc"
           }
       },
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
#### Battery Metrics History
 - Battery Metrics history can be obtained using the following API:
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/getbatterymetricshistory \
        --header 'Content-Type: application/json' \
        --data '{"token": "<token>", "vehicleno": "<vehicleno>", "starttime": "<epoch-utc>", "endtime": "<epoch-utc>"}'
 ```
 - Response structure:
 ```json
    {
       "status": "SUCCESS|FAILURE",
       "data": [
           {
               "soc": "<soc>",
               "soh": "<soh>",
               "battery_temp": "<battery-temp>",
               "battery_voltage": "<battery-voltage>",
               "charge_cycle": "<charge-cycle>",
               "current": "<current>",
               "time": "<utc-epoch>"
           }
    ],
       "err": "<err>",
       "msg": "<msg>"
    }
 ```
