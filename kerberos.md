# Kerberos

## The 3 Parties Involved
- Client
- Key Distribution Centre consisting of:
  - AS - Authentication Server
  - TGS - Ticket Granting Server
- File Server (an example service)


## Steps 

### User logs onto Client
- with `username` and `password`
- Client puts `username` & `password` through a one way hash to create `Client secret key`

### Client -> AS
- "username wants to use file server" in clear text
- AS checks if `username` is in database
- AS generates `Client secret key` from `username` and `password`

### AS -> Client
- A: `Client/TGS session key` (encrypted with `Client secret key`)
- B: `Ticket Granting Ticket` - includes `Client ID`, `client network address`,
    `ticket validity period` and `Client/TGS session key` (encrypted with `TGS
    secret key`)
  - only the AS and the TGS know the `TGS secret key`
- Client decodes message A: using `Client secret key` to get `Client/TGS session
key`
- Client can't decode B: because it doesn't have the `TGS secret key`

### Client -> TGS
  - C: `Ticket Granting Ticket` from B: (encrypted with `TGS secret key`) +
    File Service ID
  - D: Authenitcator composed of `Client ID` and `timestamp` (encrypted with
    `Client/TGS session key` from A:)
- TGS decrypts C: to get `TGT` (which includes `Client ID`, `client network address`,
  `ticket validity period` and `Client/TGS session key`) Now client & TGS can talk
  to each other because they both have `Client/TGS session key`.
- TGS decrypts D: using `Client/TGS session` key to get `Client ID` and `timestamp`
- TGS checks that `Client ID` from C: matches `Client ID` from D: and `timestamp`
  does not exceed ticket validity period

### TGS -> Client
  - E: `Client-to-FS ticket` (`Client ID`, `network address`, `validity period`,
    `Client/Server session key` - encrypted with `FS secret key`)
  - only file server can decrypt E:
  - F: `Client/Server session key` (encrypted with `Client/TGS session key` from
    A:)
- Client decodes F: with `Client/TGS session key` to get `Client/Server session
  key`

### Client -> FS
  - E: Client to FS ticket (`Client ID`, `network address`, `validity period`,
    `Client/Server session key` - encrypted with `FS secret key`)
  - G: `Authenticator` composed of `Client ID` and `timestamp` (encrypted with
    `Client/Server session key` from F:)

- FS decrypts E: using FS secret key to get:
  - `Client ID`, `network address`,
  `validity period`, `Client/Server session key`
- FS decrypts G: using `Client/Server session key` to get `Client ID` and `timestamp`
- FS checks:
  - `Client ID` from E: matches `Client ID` from G:
  - `timestamp` does not exceed `validity period`
 
### FS server -> client
  - H: the `timestamp` found in G + 1 encrypted with `Client/Server session key`
- Client decrypts H: using `Client/Server session key`, and checks `timestamp` is `timestamp` + 1
- if so, Client and FS can communicate safely

