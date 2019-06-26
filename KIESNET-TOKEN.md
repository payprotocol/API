
# Chaincode: `kiesnet-token`

## Terms

- PAOT : personal(main) account of the token

## Response objects

> Account Type (int)
- 0x00: unknown
- 0x01: personal
- 0x02: joint

> Account
```json
{
    "@account": "address",
    "token": "token code",
    "type": "<Account Type>",
    "created_time": "RFC3339Nano format string",
    "updated_time": "RFC3339Nano format string",
    "suspended_time": "RFC3339Nano format string",
    "holders": [    "// joint account only"
        "---<Holder> set---"
    ],
    "balance": {
        "@balance": "account address",
        "amount": "big int string",
        "created_time": "RFC3339Nano format string",
        "updated_time": "RFC3339Nano format string"
    }
}
```

> Holder
```json
{
    "@holder": "KID",
    "address": "address",
    "token": "token code",
    "type": "<Account Type>",
    "created_time": "RFC3339Nano format string"
}
```

> BalanceLog Type (int)
- 0x00 : mint
- 0x01 : burn
- 0x02 : send
- 0x03 : receive
- 0x04 : deposit (create a pending balance)
- 0x05 : withdraw (from the pending balance)

> BalanceLog
```json
{
    "@balance_log": "account address",
    "type": "<BalanceLog Type>",
    "rid": "relative ID - account address or contract ID",
    "diff": "delta value - big int string",
    "amount": "modified balance amount - big int string",
    "memo": "memo (length limit 1024)",
    "created_time": "RFC3339Nano format string"
}
```

> PendingBalance Type (int)
- 0x00 : account
- 0x01 : contract

> PendingBalance
```json
{
    "@pending_balance": "pending balance ID",
    "type": "<PendingBalance Type>",
    "account": "account address",
    "rid": "relative ID - account address or contract ID",
    "amount": "big int string",
    "memo": "memo (length limit 1024)",
    "created_time": "RFC3339Nano format string",
    "pending_time": "RFC3339Nano format string"
}
```

> Token
```json
{
    "@token": "token code",
    "decimal": 8,   "// 0 ~ 18 int"
    "max_supply": "supply max limit - big int string",
    "supply": "current supplied amount - big int string",
    "genesis_account": "genesis account address",
    "created_time": "RFC3339Nano format string",
    "updated_time": "RFC3339Nano format string"
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

> invoke __`account/create`__ [__token_code__, _co-holders..._]
- Create an account
- [__token_code__] : issued token code (ex, PCI, ETH, BTC ...)
- [_co-holders..._] : PAOTs (excluding the invoker self, max 127)
- If holders(include invoker) are more than 1, it creates a joint account. If not, it creates the PAOT.
- response: Account or Contract(joint account)

> query __`account/get`__ [__token_code|address__]
- Get the account
- If the parameter is token code, it returns the PAOT.
- response: Account

> invoke __`account/holder/add`__ [__account__, __holder__]
- Create a contract to add the holder
- [account] : the joint account address
- [holder] : PAOT of the holder to be added
- response: Contract

> invoke __`account/holder/remove`__ [__account__, __holder__]
- Create a contract to remove the holder
- [account] : the joint account address
- [holder] : PAOT of the holder to be removed
- response: Contract

> query __`account/list`__ [__token_code__, _bookmark_, _fetch_size_]
- Get account list
- If token_code is empty, it returns all accounts regardless of the tokens.
- [_fetch_size_] : max 200, if it is less than 1, default size will be used (which is 20)
- response: List\<Holder>

> invoke __`account/suspend`__ [__token_code__]
- Suspend the PAOT
- response: Account

> invoke __`account/unsuspend`__ [__token_code__]
- Unsuspend the PAOT
- response: Account

> query __`balance/logs`__ [__token_code|address__, _log_type_, _bookmark_, _fetch_size_, _starttime_, _endtime_]
- Get balance logs
- [__token_code|address__] : If it is token code, it returns logs of the PAOT.
- [_log_type_] : \<BalanceLog Type>
- [_fetch_size_] : max 200, if it is less than 1, the default size will be used (which is 20)
- [_starttime_] : __time(seconds)__ represented by int64
- [_endtime_] : __time(seconds)__ represented by int64
- response: List\<BalanceLog>

> query __`balance/pending/get`__ [__pending_balance_id__]
- Get the pending balance
- response: PendingBalance

> query __`balance/pending/list`__ [__token_code|address__, _sort_, _bookmark_, _fetch_size_]
- Get pending balances list
- [__token_code|address__] : If it is token code, it returns logs of the PAOT.
- [_sort_] : 'created_time' or 'pending_time'(default)
- [_fetch_size_] : max 200, if it is less than 1, default size will be used (which is 20)
- response: List\<PendingBalance>

> invoke __`balance/pending/withdraw`__ [__pending_balance_id__]
- Withdraw the balance
- response: BalanceLog

> query __`token/get`__ [__token_code__]
- Get the current state of the token
- response: Token

> invoke __`transfer`__ [__sender__, __receiver__, __amount__, _memo_, _pending_time_, _expiry_, _extra-signers..._]
- Transfer the amount of the token or create a contract
- [__sender__] : an account address, __empty = PAOT__
- [__receiver__] : an account address
- [__amount__] : an amount to transfer (big int string)
- [_memo_] : max 1024 charactors
- [_pending_time_] : __time(seconds)__ represented by int64
- [_expiry_] : __duration(seconds)__ represented by int64, multi-sig only
- [_extra-signers..._] : PAOTs (excluding the invoker self, max 127)
- response: BalanceLog

> query __`ver`__
- Get version
