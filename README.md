# UNLOCK / UNLK — Ethereum mainnet launch adaptation

This revision adapts the exact source at `3b29fd1d044fb16bbe1595ddacf7ec4efbfdd8fa` for a **new Ethereum mainnet UNLK/IMD launch**. The existing Sepolia deployment, its contracts and website configuration are preserved. No website publication is part of this release.

The active manifest selects `src/mainnet/UnlkImdHook.sol`, IMD, fee12500, tick spacing60 and ten permissions (`0x3fcc`). The historical Sepolia manifest is preserved at [docs/mainnet/release/imported-sepolia-manifest.json](docs/mainnet/release/imported-sepolia-manifest.json). [ADAPTATION.md](ADAPTATION.md) records every change and both reproduced imported-audit findings.

Price uses the existing **stateless `ImdSpotUsdSource`**: official UNLK/IMD spot × existing IMD/ETH spot × fresh validated Chainlink ETH/USD; FDV uses the fixed 1B supply. There is no sampler, keeper, observation window or warm-up. Current price validity is required for activation and new gates; reference failures do not block otherwise valid trading or independent native claims. See [SPOT_PRICE.md](docs/mainnet/SPOT_PRICE.md).

Gates begin at20%. Valid instantaneous touches permanently open the highest crossed tier through95% at$40M; $50M permanently bypasses quotas for **all** holders. Cumulative mature E/S and prior sales remain intact: 1000 acquired,200 sold,then30% permits only100 more. One-block buy maturity and the authenticated standard-router initiator ledger remain unchanged. Ordinary token transfers carry no quota rights.

The launch retains 10% swarm /87% pool /3% payer, fixed1B supply/18 decimals, fee12500 and the fixed75bps treasury fee in IMD. Uniswap's independently controlled protocol fee is now accounted for by core instead of halting the launch. Collected LP revenue still splits80% payer/20% IMD distribution; with a protocol fee, those are shares of actual LP receipts, not promises of exactly1%/0.25% of gross input. The treasury beneficiary is immutable `0x48e4e74fe1d0c14404ceb57b9fec5b21329c6ec9`; NFT purchases remain manual.

**Accepted risks remain:** mature shared-executor quota pooling and manipulation of either spot pool, including same-transaction/flash manipulation of the external IMD reference, can open gates early and permanently up to the global bypass. They are not claimed fixed. Platform treasury-approved later native reward rounds can create further claim eligibility; other pools do not enforce this pool's quota. Neither risk acceptance nor local tests replace mandatory platform review.

The actual paid request binds the correct source, owner, chain and economics. Its imported-code audit is accepted; its four-specialist audit panel and judge follow this adaptation. Publication, assigned launch records, deployment receipts and sealed activation are downstream release outputs. **No mainnet stage1 or stage2 transaction is claimed here.** See [release status and operations](docs/mainnet/release/OPERATIONS.md) and [validation](docs/mainnet/release/VALIDATION.md). Stage1 alone means pool deployed, trading inactive.

Reproduce with the unchanged offline Foundry configuration and vendored dependencies:

```sh
forge build
FOUNDRY_FUZZ_SEED=0xa9a85f3e917f851909cc56d729dafbccc481a7a8bd1cc34be8bf4db901b32cb0 forge test
python3 scripts/mainnet/artifacts.py --check
python3 scripts/mainnet/check-manifest.py
python3 -B -m unittest discover -s test -p 'test_mainnet_manifest.py' -v
```

After intentionally changing companion code, rebuild and regenerate `ImdReviewedCode` with `python3 scripts/mainnet/artifacts.py --write`, then rebuild and check again. Mine the actual release salt only after final code, owner, factory and predicted token are known. The recorded-block fork and its local CREATE2 mining are simulations; no synthetic launch number, allocation tree or source attestation is a release input.
