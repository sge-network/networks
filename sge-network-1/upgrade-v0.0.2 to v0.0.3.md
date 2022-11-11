# Binary Upgrade Instructions
## Instructions for upgrading sge-v0.0.2 to sge-v0.0.3

The following document describes the necessary steps involved that full-node operators
must take in order to upgrade from `v0.0.2` to `v0.0.3`.

This change includes some breaking changes in the form of store modifications, parameter changes and bug fixes. The full change log can be found [here](https://github.com/sge-network/sge/compare/v0.0.2...v0.0.3).

For this upgrade, we will be using a software-upgrade proposal. This proposal contains the name of the upgrade and the height at which the upgrade will occur.

- Upgrade Name: **0.0.3**
- Proposal ID: **3**
- Proposal Hash: **6F99FDAAEBE671C96041B9411FDBB80B85A140BC882473B6D942397BFE392050**
- Upgrade Height: **215000**
- Approx Upgrade Time: **11/10/2022 16:00 Hrs (UTC)**

The proposal for the upgrade has been made at **Nov 10, 2022 11:14 Hrs (UTC)**. According to our current governance parameters, the voting period is set to 1 Day. So the validators need to vote on it to make the proposal pass within **Nov 11, 2022 11:14 Hrs (UTC)**. The proposal can be viewed on the [block explorer](https://blockexplorer.testnet.sgenetwork.io/proposals/3).

---

To view the proposal, run the query:
```
sged q gov proposal 3
```

To vote **yes** to the proposal:
```
sged tx gov vote 3 yes --from key --chain-id sge-network-1 --fees 1000usge
```

To query the current tally of votes for the proposal,
```
sged q gov votes 3
```

Once the voting period is over, the state of the proposal (accepted/rejected) can be queried:
```
sged q gov proposal 3
```
---

If the proposal passes, the chain will continue to run up to the height mentioned in the proposal (215000). At this height, all nodes will automatically stop generating blocks expecting an upgrade.

To do the upgrade:
- Stop the node
- Replace the old sged binary with the new version.
  - You can get the new release version from [here](https://github.com/sge-network/sge/releases/tag/v0.0.3).
  - Build the binary and replace the old one
- Start the node
  - The chain may not immediately start generating blocks since 67% voting power might not be reached.
  - Once the chain reaches the required voting power, block generation will resume.
