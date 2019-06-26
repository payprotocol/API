
# Chaincode: `kiesnet-contract`

## Response objects

> Contract
```json
{
    "@contract": "contract ID",
    "creator": "invoker's KID",
    "signers_count": 10,
    "approved_count": 5,
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

## Functions

> invoke __`approve`__ [__contract_id__]
- Approve the contract
- If all signers have approved the contract, it invokes 'contract/execute' callback.
- response: Contract

> invoke __`disapprove`__ [__contract_id__]
- Disapprove the contract
- It invokes 'contract/cancel' callback.
- response: Contract

> query __`get`__ [__contract_id__]
- Get the contract
- response: Contract

> query __`list`__ [__ccid__, _option_, _bookmark_]
- Get contracts list of the invoker
- [ccid] : chaincode ID created a contract
- [option] : 1 of [finished, unfinished, approved, unsigned, all], default unsigned
- response: List\<Contract>

> query __`ver`__
- Get version
