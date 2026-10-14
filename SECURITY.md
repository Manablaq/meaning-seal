# Security model and limitations

MeaningSeal deliberately minimizes the attack surface: no funds, token transfers, persistent records, owner/admin permissions, external web requests, or read methods. Inputs are bounded and escaped as JSON strings inside the model prompt. Model output must be a JSON object with exactly one valid verdict. Validators independently reclassify and compare the normalized verdict; a disagreement should prevent consensus instead of silently changing the answer.

These controls are not a guarantee of safety. Known limitations:

1. **Model reliability:** Multiple validators can agree on a wrong answer. Ambiguous, adversarial, multilingual, domain-specific, or context-dependent statements can be misclassified. `UNCERTAIN` is a policy instruction, not a guaranteed fallback.
2. **Prompt injection:** Input text is untrusted and escaped, but an LLM may still follow instructions embedded in it. The validator agreement check reduces unilateral deviation, not correlated model errors.
3. **Availability and fees:** Bradbury, its RPC, the model service, and validators can be unavailable, disagree, time out, or change. Transactions cost testnet GEN even when they fail. Finality may be delayed.
4. **Public data:** Both compared statements and the verdict are visible in transaction data and receipts. The contract must not be used for confidential content.
5. **No persistent lookup:** The only result is the transaction return/receipt; losing the transaction hash makes discovery harder. There is no getter, index, or contract state history.
6. **Constrained task:** Only single-line 8–512-character statements are supported. The contract does not verify truth, provenance, legal equivalence, intent, document structure, or off-chain evidence.
7. **Network/version coupling:** The pinned `py-genlayer` runner and stable SDK are for the current Bradbury deployment environment. A future runtime or fee-policy change may require a new deployment.
8. **No formal proof or independent audit:** Unit tests, schema validation, and testnet checks cannot establish an absence of all weaknesses.
9. **Client argument types:** The CLI auto-converts digit-only arguments to integers. Use a typed SDK call for numeric-only statements, and verify the execution result rather than treating an accepted receipt as a successful comparison.

Do not use this contract alone for financial, legal, safety-critical, or identity decisions. Verify any disputed result with people and source documents.

To report a vulnerability, open a private GitHub security advisory if enabled; otherwise contact the repository owner privately. Do not publish exploit details before coordination.
