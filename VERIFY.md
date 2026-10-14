# Independent verification

These checks are read-only. They do not need a wallet, a private key, or testnet GEN. The submission contract is `0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54` on Bradbury (chain ID `4221`). Its documented source SHA-256 is `5f8e7c984f976204c2afffe8f59df132bcd2c8ab63275ac2cfbb4305537d4093`.

## Confirm the deployed source

Run this from the repository root on macOS, with `curl`, `jq`, and `shasum` installed:

```sh
shasum -a 256 contracts/meaning_seal.py

curl -fsS --max-time 15 https://rpc-bradbury.genlayer.com \
  -H 'content-type: application/json' \
  --data '{"jsonrpc":"2.0","id":1,"method":"gen_getContractCode","params":[{"address":"0x294040337A9E2c5A89f7Fd53ADEc028F5eeCCd54","status":"finalized"}]}' \
  | jq -er '.result' | base64 -D | shasum -a 256
```

Both hashes should equal the documented SHA-256 above. On Linux, replace `base64 -D` with `base64 -d`. An RPC error, null result, or decoding error is **not** a successful match; inspect the raw RPC response if the pipeline fails.

## Check finality and execution separately

The following example queries the finalized `MATERIAL_CHANGE` test transaction:

```sh
curl -fsS --max-time 15 https://rpc-bradbury.genlayer.com \
  -H 'content-type: application/json' \
  --data '{"jsonrpc":"2.0","id":1,"method":"gen_getTransactionReceipt","params":[{"txId":"0x31c9b157c70303be8d85e1fd7a593ed059895c349c49b305e1df15407a9b17a5"}]}' \
  | jq -e '{status: .result.status, execution: .result.txExecutionResult, recipient: .result.recipient, error: .error}'
```

At the time of verification, this returned `status: 7` (finalized), `execution: 1` (successful return), the submission contract as recipient, and no RPC error. For a deliberately invalid-input example, replace the transaction hash with `0x1f81cb32b04aeb4b54072d56acaf502f2633c138f4dcf282c6ea9d0809a694ee`: it is also finalized but has `execution: 2` (error). Finality by itself is not proof that `compare` returned a verdict.

The receipt status and execution code do not display the verdict string. Inspect the corresponding [Bradbury explorer transaction](https://explorer-bradbury.genlayer.com/tx/0x31c9b157c70303be8d85e1fd7a593ed059895c349c49b305e1df15407a9b17a5) or execution trace for `MATERIAL_CHANGE`. The complete transaction matrix and expected verdicts/errors are in [DEPLOYMENT.md](DEPLOYMENT.md).

For the RPC request/response definitions, see GenLayer's documentation for [`gen_getContractCode`](https://docs.genlayer.com/api-references/genlayer-node/gen/gen_getContractCode) and [`gen_getTransactionReceipt`](https://docs.genlayer.com/api-references/genlayer-node/gen/gen_getTransactionReceipt). RPC behavior and status representations can change with network upgrades; report a mismatch rather than treating an unexpected response as a passing check.
