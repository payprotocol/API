
# Chaincode: `kiesnet-token`

## Terms

- PAOT : personal(main) account of the token
- EOA : Externally Owned Accounts

## API

method **`func`** [arg1, _arg2_, ... ] {trs1, _trs2_, ... }

- method : **query** or **invoke**
- func : function name
- [arg] : mandatory argument
- [_arg_] : optional argument
- {trs} : mandatory transient
- {_trs_} : optional transient

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

> Balance
```json
{
  "@balance": "account address",
  "amount": "big int string",
  "created_time": "RFC3339Nano format string",
  "updated_time": "RFC3339Nano format string",
  "last_pruned_pay_id": "last pruned pay id"    "// used for pay balance only"
}
```

> BalanceLog Type (int)
- 0x00 : mint
- 0x01 : burn
- 0x02 : send
- 0x03 : receive
- 0x04 : deposit (create a pending balance)
- 0x05 : withdraw (from the pending balance)
- 0x06 : pay
- 0x07 : refund
- 0x08 : prune pay
- 0x09 : prune fee
- 0x0a : wrap
- 0x0b : unwrap
- 0x0c : wrap complete
- 0x0d : unwrap complete

> BalanceLog
```json
{
  "@balance_log": "account address",
  "type": "<BalanceLog Type>",
  "rid": "relative ID - account address or contract ID",
  "diff": "delta value - big int string",
  "amount": "modified balance amount - big int string",
  "fee": "fee - big int string",
  "memo": "memo (length limit 1024)",
  "created_time": "RFC3339Nano format string",
  "prune_start_id": "prune start ID",    "// used for pruned balance log only" 
  "prune_end_id": "prune end ID",    "// used for pruned balance log only"
  "pay_id": "pay ID",    "// used for pay balance log only"
  "order_id": "order ID",    "// vendor specific unique identifier"
  "ext_code": "external token code",    "// used for wrap, unwrap balance log only"
  "ext_tx_id": "external transaction id"    "// used for unwrap, wrap/complete balance log only"
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
  "fee": "fee - big int string",
  "memo": "memo (length limit 1024)",
  "created_time": "RFC3339Nano format string",
  "pending_time": "RFC3339Nano format string",
  "order_id": "order ID",    "// vendor specific unique identifier"
}
```

> Token
```json
{
  "@token": "token code",
  "decimal": 8,    "// 0 ~ 18 int"
  "max_supply": "supply max limit - big int string",
  "supply": "current supplied amount - big int string",
  "genesis_account": "genesis account address",
  "fee_policy": "<FeePolicy>",
  "wrap_bridge": "<WrapBridge>",
  "created_time": "RFC3339Nano format string",
  "updated_time": "RFC3339Nano format string"
}
```

> FeePolicy
```json
{
  "target_address": "account address receiving fee",
  "rates": {
    "pay": {
      "rate": "fee rate of pay",
      "max_amount": "max fee amount of pay"    "// 0 is unlimit"
    },
    "transfer": {
      "rate": "fee rate of transfer",
      "max_amount": "max fee amount of transfer"    "// 0 is unlimit"
    }
  }
}
```

> WrapBridge
```json
{
  "token code": {    "// external token code"
    "ext_chain": "external blockchain",    "// external blockchain"
    "wrap_address":"account address for wpci bridge"
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

> invoke **`account/create`** [__token_code__, _co-holders..._]
### Need to add response for option value
- Create an account
- [__token_code__] : issued token code (ex, PCI, ETH, BTC ...)
- [_co-holders..._] : PAOTs (excluding the invoker self, max 127)
- If holders(include invoker) are more than 1, it creates a joint account. If not, it creates the PAOT.
- response: `Account or Contract(joint account)`

> query **`account/get`** [__token_code|address__]
- Get the account
- If the parameter is token code, it returns the PAOT.
- response: `Account`

> invoke **`account/holder/add`** [__account__, __holder__]
- Create a contract to add the holder
- [account] : the joint account address
- [holder] : PAOT of the holder to be added
- response: `Contract`

> invoke **`account/holder/remove`** [__account__, __holder__]
- Create a contract to remove the holder
- [account] : the joint account address
- [holder] : PAOT of the holder to be removed
- response: `Contract`

> query **`account/list`** [__token_code__, _bookmark_, _fetch_size_]
- Get account list
- If token_code is empty, it returns all accounts regardless of the tokens.
- [_fetch_size_] : max 200, if it is less than 1, default size will be used (which is 20)
- response: `List\<Holder>`

> invoke **`account/suspend`** [__token_code__]
- Suspend the PAOT
- response: `Account`

> invoke **`account/unsuspend`** [__token_code__]
- Unsuspend the PAOT
- response: `Account`

> query **`balance/logs`** [__token_code|address__, _log_type_, _bookmark_, _fetch_size_, _starttime_, _endtime_]
- Get balance logs
- [__token_code|address__] : If it is token code, it returns logs of the PAOT.
- [_log_type_] : \<BalanceLog Type>
- [_fetch_size_] : max 200, if it is less than 1, the default size will be used (which is 20)
- [_starttime_] : **time(seconds)** represented by int64
- [_endtime_] : **time(seconds)** represented by int64
- response: `List\<BalanceLog>`

> query **`balance/pending/get`** [__pending_balance_id__]
- Get the pending balance
- response: `PendingBalance`

> query **`balance/pending/list`** [__token_code|address__, _sort_, _bookmark_, _fetch_size_]
- Get pending balances list
- [__token_code|address__] : If it is token code, it returns logs of the PAOT.
- [_sort_] : 'created_time' or 'pending_time'(default)
- [_fetch_size_] : max 200, if it is less than 1, default size will be used (which is 20)
- response: `List\<PendingBalance>`

> invoke **`balance/pending/withdraw`** [__pending_balance_id__]
- Withdraw the balance
- response: `BalanceLog`

> query **`token/get`** [__token_code__]
- Get the current state of the token
- response: `Token`

> invoke **`transfer`** [__sender__, __receiver__, __amount__, _memo_, _order_id_, _pending_time_, _expiry_, _extra-signers..._]
- Transfer the amount of the token or create a contract
- [__sender__] : an account address, **empty = PAOT**
- [__receiver__] : an account address
- [__amount__] : an amount to transfer (big int string)
- [_memo_] : max 1024 charactors
- [_order_id_] : order ID (vendor specific)
- [_pending_time_] : **time(seconds)** represented by int64
- [_expiry_] : **duration(seconds)** represented by int64, multi-sig only
- [_extra-signers..._] : PAOTs (excluding the invoker self, max 127)
- response: `BalanceLog`

> query **`ver`**
- Get version
