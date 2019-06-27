
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

> KID
```json
{
    "@kid": "KID of the user(client)",
    "lock": "serial number of the certificate (omitempty)"
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
        "---<Something> objects---"
    ]
}
```

## Functions

> query __`get`__
- Get invoker's identity
- response: Identity

> query __`kid`__
- Get invoker's KID
- response: KID string

> query __`list`__ [_bookmark_]
- Get invoker's certificates list
- response: List\<Certificate>

> invoke __`lock`__
- Lock the identity with the invoker's certificate
- response: KID

> invoke __`register`__
- Register invoker's certificate
- response: Identity

> invoke __`revoke`__ [serial_number]
- Revoke the certificate
- response: Certificate

> invoke __`unlock`__
- Unlock the identity with the invoker's certificate
- The invoker's certificate must be the certificate which was used to lock the identity.
- response: KID

> query __`ver`__
- Get version
