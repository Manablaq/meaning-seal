# Bradbury deployment evidence

Target: [GenLayer Bradbury](https://docs.genlayer.com/developers/networks), chain ID `4221`, RPC `https://rpc-bradbury.genlayer.com`.

- Contract: [`0xd57aeEe92923e591Dfb026211c5d600528F8aB6D`](https://explorer-bradbury.genlayer.com/address/0xd57aeEe92923e591Dfb026211c5d600528F8aB6D)
- Deployment transaction: [`0xf1856c38e938b528fc08e1181ae83b98798596290d489965cf6fa992e7cbdb5d`](https://explorer-bradbury.genlayer.com/tx/0xf1856c38e938b528fc08e1181ae83b98798596290d489965cf6fa992e7cbdb5d)
- Deployer: `0x5bb49021001200fe8156a81c7fcf097e535e7181`
- Local and deployed source SHA-256: `e958ab606d56617a4bdf375cd2b6396718d10e2d68484024b9624ebc2badf966`
- Deployed source length: 3,824 bytes. Bradbury returned the identical source and a schema with one nonpayable write method, `compare(string,string) -> string`, and no read methods.

## Verification performed on 14 October 2026

- `genlayer-test==0.29.2` direct-mode suite: **17 passed**.
- `genvm-lint==0.11.0` with `GENVM_VERSION=v0.3.0-rc4`: lint and semantic validation passed; one write and zero view methods.
- Pyright 1.1.406 through `genvm-lint typecheck`: no type errors.
- Deployment receipt: `ACCEPTED`, `AGREE`, `FINISHED_WITH_RETURN`, five agree votes.

| Live case | Transaction | Observed result | Consensus/execution |
| --- | --- | --- | --- |
| Identical statements; no model needed | [`0xb0f8d261…`](https://explorer-bradbury.genlayer.com/tx/0xb0f8d261dda6136c50107d6254cd567c53d97b35a238dcad056dd1544ac05dc0) | `EQUIVALENT` | `ACCEPTED`, successful return, five agree votes; trace shows zero LLM calls. |
| Quantity 20 → 30 | [`0x43129945…`](https://explorer-bradbury.genlayer.com/tx/0x431299456abeb7ac24b26dbafe16155967a9d6db501248ee2562265fcfe4e8e6) | `MATERIAL_CHANGE` | `ACCEPTED`, successful return, four agree votes and one validator timeout. |
| Wording paraphrase | [`0x0e213279…`](https://explorer-bradbury.genlayer.com/tx/0x0e2132793066c82e56566d2114cbcf04e47d1f594c76e7b485304261364ed618) | `EQUIVALENT` | `ACCEPTED`, successful return, five agree votes. |
| Too-short input | [`0x85259633…`](https://explorer-bradbury.genlayer.com/tx/0x85259633e3e998470a737e688ac057aba5f611a129fd47ff50167cda75856701) | Rejected with `ORIGINAL_SIZE` | `ACCEPTED` consensus on a `FINISHED_WITH_ERROR` receipt, five disagree votes. This is the expected guard behavior, **not** a successful comparison. |

The three successful verdicts above were read from Bradbury's execution trace, not inferred from the CLI's “write operation successfully executed” line. The CLI can print that line for a consensus-accepted user error, as the negative test demonstrates. `UNCERTAIN` is covered by local mocked-model tests but has **not** been observed in a live Bradbury call.

## Finality and reproducibility

At the last check, these transactions were `ACCEPTED` (status 5), **not `FINALIZED`** (status 7). [GenLayer's finality documentation](https://docs.genlayer.com/understand-genlayer-protocol/core-concepts/optimistic-democracy/finality) says accepted results remain provisional during the appeal window. Recheck the explorer or `genlayer receipt <hash>` before relying on them as final. The deployment and tests are real, but this document does not claim finality prematurely.

The CLI v0.39.2 `estimate-fees` command failed against its bundled SDK with `client.estimateTransactionFees is not a function`; actual deployment and writes succeeded without a custom fee preset. This is a tooling caveat, not a finding about MeaningSeal. The CLI's selected network is global state and can be changed by another session, so verify `genlayer network info` before each write, even when passing `--rpc`.
