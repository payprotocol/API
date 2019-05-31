# Payprotocol Chaincode API

## API

method __`func`__ [__arg1__, _arg2_, ... ] {__trs1__, _trs2_, ... }
- method : __query__ or __invoke__
- func : function name
- [__arg__] : mandatory argument
- [_arg_] : optional argument
- {__trs__} : mandatory transient
- {_trs_} : optional transient

## Example code (java)

```java
// (query) chaincode-id is 'kiesnet-token', function-name is 'account/get', args is 'PCI01A428A6BEF146FB4CA39A50DAA3C21AD287C0448E1D55017F'

QueryByChaincodeRequest queryByChaincodeRequest = client.newQueryProposalRequest();
queryByChaincodeRequest.setChaincodeID('kiesnet-token');
queryByChaincodeRequest.setFcn("account/get");
queryByChaincodeRequest.setArgs("PCI01A428A6BEF146FB4CA39A50DAA3C21AD287C0448E1D55017F");
Collection<ProposalResponse> queryProposals = channel.queryByChaincode(queryByChaincodeRequest);
```

```java
// (invoke) chaincode-id is 'kiesnet-token', function-name is 'transfer', args are { from: 'PCI01A428A6BEF146FB4CA39A50DAA3C21AD287C0448E1D55017F', to: 'PCI01C218A6BEF146FBA57B1050DAA3C21AD287C0448E1D55017F', amount: 5000}

// Send transaction proposal to peers
TransactionProposalRequest transactionProposalRequest = client.newTransactionProposalRequest();
transactionProposalRequest.setChaincodeID('kiesnet-token');
// The default value of the chaincode language is 'GO_LANG' and the Payprotocol chaincode was implemented with GO_LANG. So, you don't need to set chaincode language field
//transactionProposalRequest.setChaincodeLanguage(Type.GO_LANG);
transactionProposalRequest.setFcn("transfer");
transactionProposalRequest.setProposalWaitTime(testConfig.getProposalWaitTime());
transactionProposalRequest.setArgs("PCI01A428A6BEF146FB4CA39A50DAA3C21AD287C0448E1D55017F", "PCI01C218A6BEF146FBA57B1050DAA3C21AD287C0448E1D55017F", "5000");
Collection<ProposalResponse> transactionPropResp = channel.sendTransactionProposal(transactionProposalRequest);
// Send transaction to orderer
channel.sendTransaction(successful).get(testConfig.getTransactionWaitTime(), TimeUnit.SECONDS);
```

## Chaincode APIs

+ [kiesnet-id](KIESNET-ID.md)
+ [kiesnet-token](KIESNET-TOKEN.md)
+ [kiesnet-contract](KIESNET-CONTRACT.md)
