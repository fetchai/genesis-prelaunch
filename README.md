# Validator enrolement procedure

In order to prepare for our mainnet launch, selected validators are invited to participate to an *initial setup exercise*, where they'll provide their initial transactions. We'll then setup a test network, and ensure everyone can connect and start producing blocks.

## Timeline

- From March 19th to March 23rd: gentxs collections from validators
- March 23rd: Final genesis generation
- March 24th, 12:00 UTC: collect final genesis txs  
- March 24th, 15:00 UTC: network start

## Install fetchd and fetchcli

Clone and install fetchd and fetchcli from https://github.com/fetchai/fetchd.
Make sure to checkout the `v0.6.3` release before installing

Once installed, verify you have the correct version with:

```bash
fetchcli version
```

This should output `0.6.3`.

## Create key pair

This will create your validator account, along with the private and public keys. Replace <account_name> with a name of your choice, and run:

```bash
fetchcli keys add <account_name>
```

Make sure to save the mnemonic phrase from the output, and keep it somewhere safe and private!

## Create initial transaction

We'll now retrieve the genesis file from a git repository, and generate your initial validator transaction.
You'll have to update the genesis file for the network with your validator informations and provide us your initial staking transaction by creating a pull request.

Start by forking this repository . If needed, have a look at the [Github Quickstart - Fork a repo](https://docs.github.com/en/github/getting-started-with-github/fork-a-repo) guide.

```bash
git clone <your_genesis_repo_fork_url>
cd genesis-prelaunch/

fetchd --home $(pwd) add-genesis-account <account_name> 1000000000000000000atestfet
fetchd --home $(pwd) gentx  --amount 1000000000000000000atestfet --name <account_name> 

git add .
git commit -m "<account_name> gentx"
git push origin master
```

The `gentx` amount value above matches the minimum required, but for you to verify it must match the `staking.params.min_self_delegation` defined in the genesis file.
You can retrieve it with:
```bash 
cat ./config/genesis.json | jq -r '.app_state.staking.params.min_self_delegation + "atestfet"' 
``` 

The `add-genesis-account` amount defines your initial account balance. It must be at least equal to the `min_self_delegation` to create a validator, but it can be above, so that tokens not delegated will be available for creating transactions.

Once pushed, you're ready to create your pull request. If needed, here's the [Github documentation - creating a pull request from a fork](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork).

## Start your validator

We'll soon review and merge your pull request. Once we'll have collected all initial transactions from validators, we'll generate the final genesis and publish it on this repository. We'll notify you, so you can then update your fork by pulling the latest genesis file and start your validator providing it the seed identifier:

```bash
git remote add upstream https://github.com/fetchai/genesis-prelaunch.git
git fetch upstream
git reset --hard upstream/master
fetchd --home $(pwd) start --p2p.seeds "<seed_id>"
```

You can also set the `<seed_id>` by updating the `seeds` key in your `./config/config.toml` to persist this change.

## Problems, questions ?

Feel free to [open an issue](https://github.com/fetchai/genesis-prelaunch/issues) if you encounter any problems or have a question, we'll do our best to help you!
