# graph-network

A [nuthatch](https://github.com/nightswatchhq/nuthatch) nest: **The Graph Network on Arbitrum**.

Six contracts in one surface: GNS, curation, epochs, rewards, GRT and Horizon staking.

One binary, one config file, no graph-node, no gateway, no query fees.

## What it indexes

**Chain:** `arbitrum-one`. **7 contracts**, **82 tables**.

| alias | address |
|---|---|
| `gns` | `0xec9a7fb6cbc2e41926127929c2dce6e9c5d33bec` |
| `curation` | `0x22d78fb4bc72e191c765807f8891b5e1785c8014` |
| `epochs` | `0x5a843145c43d328b9bb7a4401d94918f131bb281` |
| `rewards` | `0x971b9d3d0ae3eca029cab5ea1fb0f72c85e6a525` |
| `grt` | `0x9623063377ad1b27544c965ccd7342f7ea7e88c7` |
| `staking` | `0x00669a4cf01450b64e8a2a20e9b1fcb71e61ef03` |
| `staking_legacy` | `0x00669a4cf01450b64e8a2a20e9b1fcb71e61ef03` |

## Verified

Indexed blocks **496,876,845 to 497,272,408** and sealed **12,674 events**. Every table below is generated from the vendored ABIs, and the run above is what this nest actually decoded, not an estimate.

## Read this before trusting it

- Carries **two ABIs on the staking address**. Horizon renamed the staking events wholesale, so the current implementation's ABI is silent about everything before the upgrade - one 500,000-block window at block 100,000,000 holds 32 `StakeDeposited` and 181 `AllocationCreated` that it cannot see. The `staking_legacy` entry covers them (nuthatch#773).
- `StakeDelegatedWithdrawn` is deliberately absent from the legacy entry: its signature is unchanged across the upgrade, so declaring it twice would decode every log into two identically-populated tables.
- The legacy path is verified by ABI shape against a real pre-Horizon log, **not** by a full indexed run - reaching that era is a ~397M-block backfill.

## Run it

```sh
nuthatch init --from https://github.com/nightswatchhq/graph-network
cd graph-network
nuthatch dev --dir . --backfill 50000 --seal-direct
nuthatch sql --dir . "SELECT count(*) FROM \"curation__burned\""
```

The endpoint in `nuthatch.toml` is keyless and public, so this file is publishable: a `nuthatch.toml` is pinned into the nest's content address and must never carry a credential. It is enough to follow the tip. A **backfill** wants archive depth it may not have: pass your own with `--rpc`, and check it first with `nuthatch doctor --rpc <url>`.

## Tables

```
curation__burned
curation__collected
curation__contract_synced
curation__parameter_updated
curation__set_controller
curation__signalled
curation__subgraph_service_set
epochs__contract_synced
epochs__epoch_length_update
epochs__epoch_run
epochs__parameter_updated
epochs__set_controller
gns__contract_synced
gns__counterpart_g_n_s_address_updated
gns__curator_balance_received
gns__curator_balance_returned_to_beneficiary
gns__g_r_t_withdrawn
gns__legacy_subgraph_claimed
gns__parameter_updated
gns__set_controller
gns__set_default_name
gns__signal_burned
gns__signal_minted
gns__signal_transferred
gns__subgraph_deprecated
gns__subgraph_l2_transfer_finalized
gns__subgraph_metadata_updated
gns__subgraph_n_f_t_updated
gns__subgraph_published
gns__subgraph_received_from_l1
gns__subgraph_upgraded
gns__subgraph_version_updated
grt__approval
grt__bridge_burned
grt__bridge_minted
grt__gateway_set
grt__l1_address_set
grt__minter_added
grt__minter_removed
grt__new_ownership
grt__new_pending_ownership
grt__transfer
rewards__contract_synced
rewards__horizon_rewards_assigned
rewards__parameter_updated
rewards__rewards_denied
rewards__rewards_denylist_updated
rewards__set_controller
rewards__subgraph_service_set
staking__allowed_locked_verifier_set
staking__delegated_tokens_withdrawn
staking__delegation_fee_cut_set
staking__delegation_slashed
staking__delegation_slashing_enabled
staking__delegation_slashing_skipped
staking__graph_directory_initialized
staking__horizon_stake_deposited
staking__horizon_stake_locked
staking__horizon_stake_withdrawn
staking__max_thawing_period_set
staking__operator_set
staking__provision_created
staking__provision_increased
staking__provision_parameters_set
staking__provision_parameters_staged
staking__provision_slashed
staking__provision_thawed
staking__stake_delegated_withdrawn
staking__thaw_request_created
staking__thaw_request_fulfilled
staking__thaw_requests_fulfilled
staking__thawing_period_cleared
staking__tokens_delegated
staking__tokens_deprovisioned
staking__tokens_to_delegation_pool_added
staking__tokens_undelegated
staking__verifier_tokens_sent
staking_legacy__stake_delegated
staking_legacy__stake_delegated_locked
staking_legacy__stake_deposited
staking_legacy__stake_slashed
staking_legacy__stake_withdrawn
```
