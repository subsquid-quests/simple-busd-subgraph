<p align="center">
<picture>
    <source srcset="https://uploads-ssl.webflow.com/63b5a9958fccedcf67d716ac/64662df3a5a568fd99e3600c_Squid_Pose_1_White-transparent-slim%201.png" media="(prefers-color-scheme: dark)">
    <img src="https://uploads-ssl.webflow.com/63b5a9958fccedcf67d716ac/64662df3a5a568fd99e3600c_Squid_Pose_1_White-transparent-slim%201.png" alt="Subsquid Logo">
</picture>
</p>

[![docs.rs](https://docs.rs/leptos/badge.svg)](https://docs.subsquid.io/)
[![Discord](https://img.shields.io/discord/1031524867910148188?color=%237289DA&label=discord)](https://discord.gg/subsquid)

[Website](https://subsquid.io) | [Docs](https://docs.subsquid.io/) | [Discord](https://discord.gg/subsquid)

[Subsquid Network FAQ](https://docs.subsquid.io/subsquid-network/public)

# Run a simple BUSD subgraph

This is an updated quest to run a simple subgraph using the data from the permissionless Phase 2 Subsquid Network. The subgraph used was generated with `graph init`; it indexes BUSD transfers on Binance mainnet.

You can find more info on using Subsquid Network to run subgraphs at the [Subsquid Firehose](https://docs.subsquid.io/subgraphs-support) docs page.

Here is how to run it:

### I. Install dependencies: Node.js, Docker, Git.

<details>
<summary>On Windows</summary>

1. Enable [Hyper-V](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
2. Install [Docker for Windows](https://docs.docker.com/desktop/install/windows-install/).
3. Install NodeJS LTS using the [official installer](https://nodejs.org/en/download).
4. Install [Git for Windows](https://git-scm.com/download/win).

In all installs it is OK to leave all the options at their default values. You will need a terminal to complete this tutorial - [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) bash is the preferred option.

</details>
<details>
<summary>On Mac</summary>

1. Install [Docker for Mac](https://docs.docker.com/desktop/install/mac-install/).
2. Install Git using the [installer](https://sourceforge.net/projects/git-osx-installer/) or by [other means](https://git-scm.com/download/mac).
3. Install NodeJS LTS using the [official installer](https://nodejs.org/en/download).

We recommend configuring NodeJS to install global packages to a folder owned by an unprivileged account. Create the folder by running
```bash
mkdir ~/global-node-packages
```
then configure NodeJS to use it
```bash
npm config set prefix ~/global-node-packages
```
Make sure that the folder `~/global-node-packages/bin` is in `PATH`. That allows running globally installed NodeJS executables from any terminal. Here is a one-liner that detects your shell and takes care of setting `PATH`:
```
CURSHELL=`ps -hp $$ | awk '{print $5}'`; case `basename $CURSHELL` in 'bash') DEST="$HOME/.bash_profile";; 'zsh') DEST="$HOME/.zshenv";; esac; echo 'export PATH="${HOME}/global-node-packages/bin:$PATH"' >> "$DEST"
```
Alternatively you can add the following line to `~/.zshenv` (if you are using zsh) or `~/.bash_profile` (if you are using bash) manually:
```
export PATH="${HOME}/global-node-packages/bin:$PATH"
```

Re-open the terminal to apply the changes.

</details>
<details>
<summary>On Linux</summary>

Install [NodeJS (v16 or newer)](https://nodejs.org/en/download/package-manager), Git and Docker using your distro's package manager.

We recommend configuring NodeJS to install global packages to a folder owned by an unprivileged account. Create the folder by running
```bash
mkdir ~/global-node-packages
```
then configure NodeJS to use it
```bash
npm config set prefix ~/global-node-packages
```
Make sure that any executables globally installed by NodeJS are in `PATH`. That allows running them from any terminal. Open the `~/.bashrc` file in a text editor and add the following line at the end:
```
export PATH="${HOME}/global-node-packages/bin:$PATH"
```
Re-open the terminal to apply the changes.

</details>

### II. Run the subgraph

1. Download the subgraph, enter its folder and install the dependencies

   ```bash
   git clone https://github.com/subsquid-quests/simple-busd-subgraph
   ```
   ```bash
   cd simple-busd-subgraph
   ```
   ```bash
   yarn install
   ```

> [!IMPORTANT]
> If you're on Windows, the terminal opens in `C:\Windows\system32` by default. Do not download your squid there, navigate someplace else.

2. Press "Get Key" button in the quest card to obtain the `busdSubgraphPhaseTwo.key` key file. Save it to the `./query-gateway/keys` subfolder of the squid folder. The file will be used to identify your local query gateway when locking tSQD to allocate bandwidth and as it operates.

3. Get the peer ID of your future gateway by running:
   ```bash
   yarn run get-peer-id
   ```

4. Register your future gateway using [this page](https://app.subsquid.io/profile/gateways/add?testnet).
   - Use the peer ID you obtained in the previous step.
   - Leave the "Publicly available" switch disabled.


5. Lock 10 tSQD by selecting your gateway on [this page](https://app.subsquid.io/profile/gateways?testnet), clicking "Get CU" and submitting the form. Once done, you will begin getting computation units (*CUs*) once every epoch (~15 minutes).

   The "Lock blocks duration" field lets you tune the length of time during which you'll be able to query the network, measured in blocks of Arbitrum Sepolia's L1 (that is, Ethereum Sepolia). The minumum is five hours, but you can opt to lock for longer if you intend to work on the quest over multiple days.

   | Time              | Blocks |
   |:-----------------:|:------:|
   | 5 hours (minimum) | 1500   |
   | 24 hours          | 7200   |
   | 72 hours          | 21600  |

   Be aware that you'll need to unlock your tokens manually after the end of this period. The tokens you get back will be used in subsequent quests.

   If the locking period expires before you finish your work, simply unlock your tokens, then lock them again.

6. Wait for about 15 minutes. This is the time it takes for Subsquid Network to enter a new epoch, at the beginning of which CUs will be allocated towards your gateway.

7. Start the gateway and the rest of the services required to run the subgraph as Docker containers:
   ```bash
   docker compose up -d
   ```
   Wait for about a couple of minutes before proceeding to the next step.

8. Randomize the starting block of the subgraph by running:
   ```bash
   yarn run randomize
   ```

9. Create and deploy the subgraph:
   ```bash
   yarn run create-local
   ```
   ```bash
   yarn run deploy-local
   ```
   To check if the subgraph is successfully deployed, check the output of
   ```bash
   docker logs -f --tail 10 simple-busd-subgraph-graph-node-1
   ```
   It should contain lines like this:
   ```
   Dec 10 18:49:54.966 INFO Done processing trigger, gas_used: 79221480, data_source: busd, handler: handleTransfer, total_ms: 1, transaction: 0xb0ab…129e, address: 0x55d3…7955, signature: Transfer(indexed address,indexed address,uint256), sgd: 1, subgraph_id: QmQwLxHMw4Mf7VSfVfw66pKcySKRFGfh2freN7MJbbp7br, component: SubgraphInstanceManager
   Dec 10 18:49:54.968 INFO Done processing trigger, gas_used: 79221480, data_source: busd, handler: handleTransfer, total_ms: 1, transaction: 0xac49…4960, address: 0x55d3…7955, signature: Transfer(indexed address,indexed address,uint256), sgd: 1, subgraph_id: QmQwLxHMw4Mf7VSfVfw66pKcySKRFGfh2freN7MJbbp7br, component: SubgraphInstanceManager
   ```
   Note that it might take a few minutes before the subgraph gets to that stage.

   If you get an error like this:
   ```
   ✖ Failed to deploy to Graph node http://localhost:8020/: network not supported by registrar: no network bsc found on chain ethereum
   ```
   simply restart the containers with
   ```bash
   docker compose down
   ```
   ```bash
   docker compose up -d
   ```
   and run `yarn run create-local` followed by `yarn run deploy-local` again.

10. Leave the syncing process to run overnight. Once done, shut down the containers with
   ```bash
   docker compose down
   ```

# Quest Info

| Category         | Skill Level                          | Time required (minutes) | Max Participants | Reward                              | Status |
| ---------------- | ------------------------------------ | ----------------------- | ---------------- | ----------------------------------- | ------ |
| Squid Deployment | $\textcolor{green}{\textsf{Simple}}$ | ~720                    | -                | $\textcolor{red}{\textsf{75tSQD}}$  | open   |

# Acceptance critera

Sync this subgraph for about 8-12 hours using the key from the quest card. The syncing progress is tracked by the amount of data the subgraph has retrieved from [Subsquid Network](https://docs.subsquid.io/subsquid-network/public).

# About this subgraph

Here is the exact Graph CLI command that was used to generate the subgraph used for this quest:
```bash
graph init --from-contract 0x55d398326f99059fF775485246999027B3197955 --network bsc simple-busd-subgraph --allow-simple-name
```
To make the subgraph ingest its data from the [decentralized and permissionless version](https://docs.subsquid.io/subsquid-network/public/) of Subsquid Network, we added the following files

 * `query-gateway/config/gateway-config.yml` - config for the decentralized Subsquid Network gateway
 * `config.toml` - config for the Graph node
 * `docker-compose.yml` - definition of all the involved containers, including the one running [`subsquid-firehose`](https://github.com/subsquid/firehose-grpc/)

Once the subgraph is up and running, it will expose its GraphQL API (with a GraphiQL playground) at [http://localhost:8000/subgraphs/name/simple-busd-subgraph/](http://localhost:8000/subgraphs/name/simple-busd-subgraph/). Check its status by running the following query:
```graphql
{
  _meta {
    block {
      number
      hash
      timestamp
    }
    deployment
    hasIndexingErrors
  }
}
```
