# Binary Upgrade Instructions
## Instructions for upgrading sge-v0.0.1 to sge-v0.0.2

The following document describes the necessary steps involved that full-node operators
must take in order to upgrade from `v0.0.1` to `v0.0.2`. The SGE team
will post an official updated genesis file, but it is recommended that validators
execute the following instructions in order to verify the resulting genesis file.

## Risks

As a validator performing the upgrade procedure on your consensus nodes carries a heightened risk of
double-signing and being slashed. The most important piece of this procedure is verifying your
software version and genesis file hash before starting your validator and signing.

The riskiest thing a validator can do is discover that they made a mistake and repeat the upgrade
procedure again during the network startup. If you discover a mistake in the process, the best thing
to do is wait for the network to start before correcting it. If the network is halted and you have
started with a different genesis file than the expected one, seek advice from a SGE developer
before resetting your validator.

## Halting the Chain

To ensure elegant halting of the validator nodes at the same height, it is required to set the `halt-height` field in the `.sge/config/app.toml` file.
For Example, to set the chain to halt at height `76000`, the `app.toml` file should be changed as follows:

```
# HaltHeight contains a non-zero block height at which a node will gracefully
# halt and shutdown that can be used to assist upgrades and testing.
#
# Note: Commitment of state will be attempted on the corresponding block.
halt-height = 76000
```

After making the changes to the `app.toml` file, make sure to restart the node for the changes to take effect.

```
sudo systemctl restart sged.service
```

**NOTE**: Changes will not take effect unless the node is restarted.


## Recovery

Prior to exporting `v.0.0.1` state, validators are encouraged to take a full data snapshot at the
export height before proceeding. Snapshotting depends heavily on infrastructure, but generally this
can be done by backing up the `.sge` directories.

It is critically important to back-up the `.sge/data/priv_validator_state.json` file after stopping your sged process. **This file is updated every block as your validator participates in a consensus rounds. It is a critical file needed to prevent double-signing, in case the upgrade fails and the previous chain needs to be restarted**.

In the event that the upgrade does not succeed, validators and operators must downgrade back to
v0.0.1 and restore to their latest snapshot before restarting their nodes.

## Upgrade Procedure

__Note__: It is assumed you are currently operating a full-node running v0.0.1 of the SGE.

- The upgrade height is: **76000**

1. **Backup your `.sge/data/priv_validator_state.json`** and confirm that the height is correct.
2. **Backup your `.sge/config/priv_validator_key.json` and  `.sge/config/node_key.json`**.
3. Verify you are currently running the correct version (v0.0.1) of the SGE:

   ```bash
   $ sged version
   v0.0.1
   ```

3. Stop the node exactly at the height which has been agreed(76000). Check if the height property of the `.sge/data/priv_validator_state.json` is equal to this height. 
   **NOTE** All of validators should stop their node at this height otherwise the chain (or some of validator nodes) may get down after starting.

4. Export The genesis
   **NOTE**: It is recommended for validators and operators to take a full data snapshot at the export
   height before proceeding in case the upgrade does not go as planned or if not enough voting power
   comes online in a sufficient and agreed upon amount of time. In such a case, the chain will fallback
   to continue operating `v0.0.1`. See [Recovery](#recovery) for details on how to proceed.

   Before exporting state via the following command, the `sged` binary must be stopped!

   ```bash
   $ sged export &> v0.0.1_genesis_export.json
   ```
5. At this point you now have a valid exported genesis state! All further steps now require
v0.0.2 of [SGE](https://github.com/sge-network/sge).

   **NOTE**: Go [1.18+](https://golang.org/dl/) is required!

   ```bash
   $ git clone https://github.com/sge-network/sge.git && cd sge && git checkout v0.0.2; make install
   ```
6. Verify you are currently running the correct version (v0.0.2) of the _SGE_:

   ```bash
   $ sged version
   v0.0.2
   ```
7. Copy `v0.0.1_genesis_export.json` to `.sge/config/genesis.json`
8. Reset the state
   ```
   sged tendermint unsafe-reset-all
   ```
9. Copy the backed up `priv_validator_key.json` to `.sge/config/priv_validator_key.json`
10. Start the chain with the new binary

   ```
   sged start
   ```
