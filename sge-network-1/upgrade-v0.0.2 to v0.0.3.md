# Binary Upgrade Instructions
## Instructions for upgrading sge-v0.0.2 to sge-v0.0.3

The following document describes the necessary steps involved that full-node operators
must take in order to upgrade from `v0.0.2` to `v0.0.3`.

This change includes some breaking changes in the form of store modifications, parameter changes and bug fixes. The full change log can be found [here](https://github.com/sge-network/sge/compare/v0.0.2...v0.0.3).

For this upgrade, we will be using a software-upgrade proposal. This proposal contains the name of the upgrade and the height at which the upgrade will occur.

- Upgrade Name: **Peace**
- Proposal ID: **2**
- Proposal Hash: **0B7584087CCF7781FF0A18CCFACB2A10364ADD0F39F9F58096ADE1C703E2C007**
- Upgrade Height: **199000**
- Approx Upgrade Time: **10/10/2022 14:20 Hrs (UTC)**

The proposal for the upgrade has been made at **Nov 09, 2022 13:27:12 Hrs (UTC)**. According to our current governance parameters, the voting period is set to 1 Day. So the validators need to vote on it to make the proposal pass within **Nov 10, 2022 13:27:12 Hrs (UTC)**. The proposal can be viewed on the [block explorer](https://blockexplorer.testnet.sgenetwork.io/proposals/2).

---

To view the proposal, run the query:
```
sged q gov proposal 2
```

To vote **yes** to the proposal:
```
sged tx gov vote 2 yes --from key --chain-id sge-network-1 --fees 1000usge
```

To query the current tally of votes for the proposal,
```
sged q gov votes 2
```

Once the voting period is over, the state of the proposal (accepted/rejected) can be queried:
```
sged q gov proposal 2
```
---

If the proposal passes, the chain will continue to run up to the height mentioned in the proposal (199000). At this height, all nodes will automatically stop generating blocks expecting an upgrade.

To do the upgrade:
- Stop the node
- Replace the old sged binary with the new version.
  - You can get the new release version from [here](https://github.com/sge-network/sge/releases/tag/v0.0.3).
  - Build the binary and replace the old one
- Start the node
  - The chain may not immediately start generating blocks since 67% voting power might not be reached.
  - Once the chain reaches the required voting power, block generation will resume.
