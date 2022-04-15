# Chaincode: `kiesnet-id`

kiesnet-id is the chaincode to manage an user(client)'s certificate and kid(=uuid).
every user(client) must register the certificate after the enrollment.

#

## API

method **`func`** [arg1, _arg2_, ... ] {trs1, _trs2_, ... }

- method : **query** or **invoke**
- func : function name
- [arg] : mandatory argument
- [_arg_] : optional argument
- {trs} : mandatory transient
- {_trs_} : optional transient

#

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
  "records": ["---<Something> objects---"]
}
```

#

## Functions

> query **`get`**

- Get invoker's identity
- response: `Identity`

> query **`kid`**

- Get invoker's KID
- response: `KID string`

> query **`list`** [_bookmark_]

- Get invoker's certificates list
- response: `List\<Certificate>`

> invoke **`lock`**

- Lock the identity with the invoker's certificate
- response: `KID`

> invoke **`register`**

- Register invoker's certificate
- response: `Identity`

> invoke **`revoke`** [serial_number]

- Revoke the certificate
- response: `Certificate`

> invoke **`unlock`**

- Unlock the identity with the invoker's certificate
- The invoker's certificate must be the certificate which was used to lock the identity.
- response: `KID`

> query **`ver`**

- Get version
