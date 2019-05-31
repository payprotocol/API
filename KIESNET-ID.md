
# Chaincode: `kiesnet-id`

kiesnet-id is the chaincode to manage an user(client)'s certificate and kid(=uuid).
every user(client) must register the certificate after the enrollment.

## Response objects

> Identity
```json
{
    "id": "KID of the user(client)",
    "sn": "serial number of the certificate"
}
```

> Certificate
```json
{
    "@certificate": "KID of the user(client)",
    "sn": "serial number of the certificate"
}
```

> List\<Something>
```json
{
    "meta": {
        "fetched_records_count": 123,
        "bookmark": "---bookmark string---"
    },
    "records": [
        // <Something> objects list
    ]
}
```

## Functions

> query __`get`__ {_"kiesnet-id/pin"_}
- Get invoker's identity
- If {_"kiesnet-id/pin"_} is set, PIN validation will be performed; Otherwise, not.
- response: Identity

> query __`kid`__ [_secure_] {_"kiesnet-id/pin"_}
- Get invoker's KID
- [_secure_] is not empty, PIN validation will be performed.
- response: KID string

> query __`list`__ [_bookmark_] {_"kiesnet-id/pin"_}
- Get invoker's certificates list
- If {_"kiesnet-id/pin"_} is set, PIN validation will be performed; Otherwise, not.
- response: List\<Certificate>

> invoke __`pin`__ {"kiesnet-id/pin", "kiesnet-id/new_pin"}
- Update the PIN
- response: Identity

> invoke __`register`__ {_"kiesnet-id/pin"_}
- Register invoker's certificate
- If the client user already has PIN, {"kiesnet-id/pin"} is mandatory.
- response: Identity

> invoke __`revoke`__ [__serial_number__] {"kiesnet-id/pin"}
- Revoke the certificate
- response: Certificate

> query __`ver`__
- Get version
