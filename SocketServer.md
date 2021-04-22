## Accenture Socket Server Docs 

Note: `socket.io` library was used to create the this socket server. Terminology
specific to the library may be used.

### Connection and Data Transmission Workflow
- The flow to be followed for transmitting data to the server is mentioned in-order below.
#### Connect to server
The socket server is hosted at `https://drvp.intellicar.in`. It can be connected
using any socket.io client.

#### Authentication
- Generate token using the Standard API.
 ```bash
    curl https://apiplatform.intellicar.in/api/standard/gettoken \
        --header 'Content-Type: application/json' \
        --data '{"username": "<username>", "password": "<password>"}'
 ```
- Credentials for the token generation are:
    - username: `accenture.sock`
    - password: `Wc7iCF9jlB`

- Listen to channels `AUTH_SUCCESS` and `AUTH_FAILURE`.
- Emit to channel `authpkg` a JSON object containing `token` and `origin` which
    would be as follows:
    ```json
    {
        "token": "<generated-token>",
        "origin": "accenture"
    }
    ```
- If authentication was successful, a JSON object will be sent in the `AUTH_SUCCESS` channel which would be as follows:
    ```json
    {
        "msg": "Auth success. Token validity: <expiry-in-epoch>"
    }
    ```
- If authentication was a failure, a JSON object will be sent in the `AUTH_FAILURE` channel which wouldb e as follows:
    ```json
    {
        "msg": "<reason-for-failure>"
    }
    ```

#### Data Transmission
- Data can now be transmitted on the channel `accenturedata` as a JSON object
    (in the prescribed format, if possible).
