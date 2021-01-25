# API Server for F5 Shape DeviceID

Run the docker-compose with ``docker-compose up -d``

This will start 2 containers:

    1. MongoDB container to store DeviceID per Username
    2. NodeJS server

You can connect to the API Server SwaggerUI through http://localhost:3000/explorer

You can import the Postman collection if you want to test through Postman.

You can find the NodeJS docker image in the Packages section of this repository if you don't want to use the ``docker-compose``.

# iRule used to extract the DeviceID values

iRule to decode DeviceID+ and insert into APM session variable

    when ACCESS_SESSION_STARTED {
        log local0. "Access Session Started"
        if [HTTP::cookie exists _imp_apg_r_] {
            set deviceid [URI::decode [HTTP::cookie _imp_apg_r_]]
            log local0. "URL Decoded cookie is $deviceid"
            set deviceida [lindex [regexp -inline -- (?:"diA":")(.*?)(?:") $deviceid] 1]
            log local0. "diA = $deviceida"
            set deviceidb [lindex [regexp -inline -- (?:"diB":")(.*?)(?:") $deviceid] 1]
            log local0. "diB = $deviceidb"
            log local0. "IP is [IP::client_addr]"
            log local0. "Path os [HTTP::path]"
            ACCESS::session data set session.logon.deviceid $deviceid
            ACCESS::session data set session.logon.deviceida $deviceida
            ACCESS::session data set session.logon.deviceidb $deviceidb
        } else {
        log local0. "No cookie"
        }
    }

# Structure of the API

The API server has been created with Loopback.io v3. This command-cli tool create the structure of the API for you.

The structure of the API is below:

        {
            "username": "dierick",
            "deviceid": [
                "Ac31A2AAAAAA12uwoekcLffhgfjgjghj",
                "AUq7Xti2Eowm6yVog4CYvt6nRrookqgW"
            ]
        }

There are 2 properties ``username`` and ``deviceid``. As a reminder, Shape DeviceID sets 2 cookies (Cookie A and Cookie B).

    1. GET Device ID per username : 
    
        GET /api/deviceids/findOne?filter={"where":{"username":"dierick"}}
    
    2. POST New Device ID : 
    
        POST /api/deviceids HTTP/1.1
            {
                "username": "dierick",
                "deviceid": [
                    "Ac31A2AAAAAA12uwoekcLffhgfjgjghj",
                    "AUq7Xti2Eowm6yVog4CYvt6nRrookqgW"
                    ]
            }
    3. UPDATE Device ID per username

        POST /api/deviceids/upsertWithWhere?where={"username": "dierick"} HTTP/1.1
            {
                "deviceid": [
                    "Rtdsflkj534565465kenfter"
                ]
            }


# APM Per Request Policy

You can import the PRP policy ``policy_Common_PRPstandard.conf.tar.gz`` into your v16.0.0 BIG-IP APM.

![GitHub Logo](https://raw.githubusercontent.com/MattDierick/DeviceID-api-server/main/images/PRP.png)


# Demo video

[![IMAGE ALT TEXT HERE](https://raw.githubusercontent.com/MattDierick/DeviceID-api-server/main/images/Youtube_screen.png)](https://youtu.be/PVYwh76nGVE)
