# pft-rank

**A command-line leaderboard rank checker for Post Fiat (PFT) wallets.**

The Post Fiat explorer shows individual account balances, but there's no quick way
to see where a wallet sits in the overall holder rankings from the terminal.
`pft-rank` walks the entire ledger and answers that in one command: the top 30
holders, the neighborhood of ranks immediately around your wallet, and your
balance with both overall and earner rank.

It reads only public ledger data over the public RPC endpoint — no API key, no
wallet secret, no login.

---

## Example output

```
=== Top 30 ===
   1      9999999.00 PFT  rSYSTEMxxxxxxxxxxxxxxxxxxxxxxxxxx
   ...
=== Neighborhood (earner ranks) ===
  36       640123.45 PFT  rOtherWalletxxxxxxxxxxxxxxxxxxxxx
  37       631000.00 PFT  rAnotherWalletxxxxxxxxxxxxxxxxxxx
  38       627690.61 PFT  rYourWalletxxxxxxxxxxxxxxxxxxxxxx   <- highlighted in green
  39       620000.00 PFT  rNextWalletxxxxxxxxxxxxxxxxxxxxxx
=== Wallet ===
Address:      rYourWalletxxxxxxxxxxxxxxxxxxxxxx
Balance:      627690.61 PFT
Overall rank: #50 of 2455 accounts
Earner rank:  #38 (excluding 12 system/validator wallets)
```

## Requirements

- `bash`, `curl`, `awk`, `sort` (standard on Linux and macOS)
- `jq` — JSON parser
  - Debian/Ubuntu: `sudo apt install jq`
  - macOS: `brew install jq`

## Install

```
git clone https://github.com/walkonwayvs/pft-rank.git
cd pft-rank
chmod +x pft-rank
sudo cp pft-rank /usr/local/bin/    # optional: run it from anywhere
```

## Usage

```
pft-rank <WALLET_ADDRESS>
```

Example:

```
pft-rank r4XLZKYK63z4zW8ttKzgy8NVW1Noa1pRwj
```

### Pointing at a different network

By default it queries the testnet RPC. Override with an environment variable:

```
PFT_RPC_URL=https://your-rpc-endpoint pft-rank <WALLET_ADDRESS>
```

## How it works

The script pages through the full account set via the `ledger_data` RPC method
(400 accounts per page, following the returned `marker` until the ledger is
exhausted), sorts every account by balance, and reports the requested wallet's
position. "Earner rank" excludes the first 12 system/validator wallets at the top
of the ledger so the ranking reflects actual network participants.

## Notes

- This is read-only. It never sends a transaction or touches a wallet secret.
- Walking the full ledger takes several seconds depending on account count and
  RPC speed.

## License

MIT
