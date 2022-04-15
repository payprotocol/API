# Chaincode: `kiesnet-contract`

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

> Contract

```json
{
  "@contract": "contract ID",
  "creator": "invoker's KID",
  "signers_count": "signer count for contract",
  "approved_count": "approved count for contract",
  "ccid": "chaincode ID",
  "document": "contract document JSON string",
  "created_time": "RFC3339Nano format string",
  "updated_time": "RFC3339Nano format string",
  "expiry_time": "RFC3339Nano format string",
  "executed_time": "RFC3339Nano format string",
  "canceled_time": "RFC3339Nano format string",
  "finished_time": "RFC3339Nano format string",
  "sign": {
    "signer": "signer's KID",
    "approved_time": "RFC3339Nano format string",
    "disapproved_time": "RFC3339Nano format string"
  }
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

#

## Functions

> invoke **`approve`** [__contract_id__] {_"kiesnet-id/pin"_}

- Approve the contract
- If all signers have approved the contract, it invokes 'contract/execute' callback.
- response: `Contract`

> invoke **`disapprove`** [__contract_id__] {_"kiesnet-id/pin"_}

- Disapprove the contract
- It invokes 'contract/cancel' callback.
- response: `Contract`

> query **`get`** [__contract_id__]

- Get the contract
- response: `Contract`

> query **`list`** [__ccid__, _option_, _bookmark_]

- Get contracts list of the invoker
- [ccid] : chaincode ID created a contract
- [option] : 1 of [finished, unfinished, approved, unsigned, all], default unsigned
- response: `List\<Contract>`

> query **`ver`**

- Get version
