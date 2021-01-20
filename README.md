# API Server for F5 Shape DeviceID

Run the docker-compose with ``docker-compose up -d``

This will start 2 containers:

    #. MongoDB container to store DeviceID per Username
    #. NodeJS server

You can connect to the API Server SwaggerUI through http://localhost:3000/explorer

You can import the Postman collection if you want to test through Postman.

# Structure of the API

The API server has been created with Loopback.io v3. This command-cli tool create the structure of the API for you.

The structure of the API is below:

    .. code-block:: json

        {
            "username": "dierick",
            "deviceid": [
                "Ac31A2AAAAAA12uwoekcLffhgfjgjghj",
                "AUq7Xti2Eowm6yVog4CYvt6nRrookqgW"
            ]
        }

There are 2 properties ``username`` and ``deviceid``. As a reminder, Shape DeviceID sets 2 cookies (Cookie A and Cookie B).

