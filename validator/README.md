# Running a Validator on Sepolia Testnet

This tutorial provides a step-by-step guide for setting up, staking and running a Sepolia testnet validator that mimincs a full node on the Ethereum mainnet using Geth as an execution client and Prysm as a consensus client. 


### Prerequisites

- Same as the prerequisites for running a beacon node on Sepolia testnet. 
- You need to deposit 32 ETH to stake on the Sepolia testnet and same amount for mainnet.

### Step One: Install Prysm

You should create one main directory `ethereum` and two subdirectories `consensus` and `execution`. 

```bash
mkdir ethereum && cd ethereum
mkdir consensus execution
```

Install Prysm in the `consensus` directory.

```bash
cd consensus
```
```bash
curl https://raw.githubusercontent.com/prysmaticlabs/prysm/master/prysm.bat --output prysm.bat
reg add HKCU\Console /v VirtualTerminalLevel /t REG_DWORD /d 1
```

### Step Two: Generate JSON Web Token (JWT) Secret

You need to generate a JWT secret to authenticate the HTTP connection between your beacon node and execution node. You may need to run your terminal of choice as an administrator to execute the following command.

```bash
prysm.bat beacon-chain generate-auth-secret
```

> If you're using Bash or PowerShell, please refer to previous [recommendations and troubleshooting](https://github.com/SashaFlores/running-sepolia-node-tutorial/blob/main/running-node/README.md#checkpoint-provided-by-beaconstate) steps in the Sepolia directory.

Prysm will output a `jwt.hex` file move it to `ethereum` directory.

### Step Three: Run your Execution Client `Geth`

This step assumes you already have Geth installed on your operating system. Navigate to the `execution` directory and run the following command to start your Geth client.

```bash
cd ../execution
```

```bash
geth --sepolia --http --http.api eth,net,engine,admin --authrpc.jwtsecret=<PATH_TO_JWT_FILE>
```


### Step Four: Generate Validator Keys & Submit Deposit 

It's recommended to generate new keys  for as a validator by downloading and decompressing [Ethereum Staking Deposit CLI](https://github.com/ethereum/staking-deposit-cli/releases) that matches your operating system. All below commands are Windows-based.

```bash
deposit.exe new-mnemonic --num_validators=<NUM_VALIDATORS> --mnemonic_language=english --chain=<CHAIN_NAME> --folder=<YOUR_FOLDER_PATH>
```

OR you want to use an existing mnemonic, you can run the following command:
    
```bash
deposit.exe existing-mnemonic --num_validators=<NUM_VALIDATORS> --validator_start_index=<START_INDEX> --chain=<CHAIN_NAME> --folder=<YOUR_FOLDER_PATH>
```

- The above command will create your unique and highly sensitive 24-word mnemonic phrase and keys. 
- After you follow the CLI prompts to generate your keys, the password you choose will be required later when importing the generated data into the Prysm validator client.
- A `validator_keys` directory will be created in the specified folder path and will contain:
    - `deposit_data.json`: contains deposit data that you’ll later upload to the Ethereum launchpad.
    - `keystore-m.json`: contains your public key and encrypted private key.
    
    - You should copy the `validator_keys` directory to an offline storage device for safekeeping by running the following command and replacing `YOUR_FOLDER_PATH` with the full path to your `validator_keys` folder:
    ```bash
    prysm.bat validator accounts import --keys-dir=<YOUR_FOLDER_PATH> --sepolia
    ```
    - You’ll be prompted to specify a wallet directory twice. Provide the path to your consensus folder for both prompts.
    - You should see Imported accounts [...] view all of them by running accounts list when your account has been successfully imported into Prysm.
    - Finally, run the following command to start your validator, replacing `YOUR_FOLDER_PATH` with the full path to your consensus folder and `YOUR_WALLET_ADDRESS` by the address of a wallet you own. 
    ```bash 
    prysm.bat validator --wallet-dir=<YOUR_FOLDER_PATH> --sepolia --suggested-fee-recipient=<YOUR_WALLET_ADDRESS>>
    ```
> Please note that Sepolia has a permissioned validators set. You cannot create a new validator on this network. If you are interested in running a validator on a testnet, please choose an other testnet, like Holesky.



### Step Five: Run a beacon node using Prysm

In running Sepolia Node tutorial, we chose to sync a beacon node from a checkpoint because it was faster than syncing from genesis. In this tutorial we will use sync from genesis. 

- You should download [Sepolia genesis.ssz](https://github.com/eth-clients/sepolia/blob/main/metadata/genesis.ssz) into your `consensus` directory.
- It's mandatory to have a wallet address to receive the fees and tips when you're running as a validator. 

```bash
prysm.bat beacon-chain --execution-endpoint=http://localhost:8551 --sepolia --jwt-secret=<PATH_TO_JWT_FILE>  --genesis-state=genesis.ssz --suggested-fee-recipient=0xYouWalletAddress
```

- It's a vital step to specify `--suggested-fee-recipient` flag in both validator and beacon node commands if you are running multiple validators.
- If no `--suggested-fee-recipient` is set on the validator client, then the beacon node will fallback to the default wallet address specified in the beacon node command.
- If no `--suggested-fee-recipient` is set neither on the validator client nor on the beacon node, the corresponding tips will be sent to the burn address, and forever lost.

### Step Six: Weight your Options

Some may think you can only run a validator if you have 32 ETH and a 24/7 independent machine for running a full node. However, you can run a validator without the need to run a full node on your own. 

|   Home Staking                                     | Staking as a Service                                         | 
|----------------------------------------------------|--------------------------------------------------------------|
| Needs Hardware & Software                          | No Hardware/Software Needed                                  |
| Technical Knowledge                                | No Technical Knowledge Needed                                |
| 24/7 Internet Connection                           | No 24/7 Internet Connection                                  |
| Full Control over Keys                             | Signing Keys are entrusted to third party                    |
| Full rewards                                       | Service Fee                                                  |
| Penalties for going offline                        | Same + counter-party risk of service provider                |
| Larger penalties for malicious behavior (Slashing) | Same + counter-party risk of service provider                |
| More profitable                                    | Less profitable                                              |


### List of Saas providers:
- [Kiln](https://www.kiln.fi/)
- [P2P Org](https://p2p.org/networks/ethereum)
- [RockX Stacking](https://www.rockx.com/staking/ethereum)
- [Consensys Staking](https://consensys.io/staking)
- [Stakefish](https://stake.fish/ethereum/)
- [Ethpool](https://ethpool.org/)
- [Figment](https://figment.io/)
- [Sensi Node](https://www.senseinode.com/)
- [Abyss Finance](https://abyss.finance/hosting)
- [Everstake Institutional](https://eth.everstake.one/)
- [ChainLabo](https://www.chainlabo.com/)
- [Allnodes](https://www.allnodes.com/eth2/staking)
- [Squid](https://www.ohsquid.com/)


### Step Seven: Withdrawal

- You can withdraw your earnings only or your full staked ETH.
- You'll need your validator mnemonic to authorize withdrawal request.
- Access to a beacon node to connect your validator to the network to submit withdrawal request.
 
    ### Partial Withdrawal (Earnings Only):
    - You'll send a message to the network that says "I authorize a partial withdrawal of my validator's staked ETH to an address that I own".
    - This message is called a BLS to Execution Change and has to be signed by your validator's private key 
    ```bash
    curl -LO  https://github.com/ethereum/staking-deposit-cli/releases/download/v2.7.0/staking_deposit-cli-fdab65d-windows-amd64.zip
    ```
    - Extract the downloaded content and you should see a deposit script.
    - Move the extracted contents into an external storage device offline for safekeeping.
    - Retrieve your validator’s withdrawal_credentials from the deposit_data-XXX.json file that was generated when you first used the staking launchpad, it should look like this:
    ```bash
    0x00500b3bf612bed69e888edeb045f590c3f37251e3e049c0732f3adaa57ea3f6
    ```
    - If you don't have the deposit_data-XXX.json file, you can retrieve your withdrawal_credentials by sending a request to your synced beacon node via this Beacon API endpoint and providing your validator index or public key:
    ```bash
    curl -X 'GET' \
  'http://YOUR_PRYSM_NODE_HOST:3500/eth/v1/beacon/states/head/validators/YOUR_VALIDATOR_INDEX_OR_PUBLIC_KEY' \
  -H 'accept: application/json'
    ```
    - Your withdrawal credentials will be visible in the response to this request - look for withdrawal_credentials. Example output with placeholder values:
    ```bash
    {
        "execution_optimistic": false,
        "data": {
            "index": "1",
            "balance": "1",
            "status": "active_ongoing",
            "validator": {
            "pubkey": "0x93247f2209abcacf57b75a51dafae777f9dd38bc7053d1af526f220a7489a6d3a2753e5f3e8b1cfe39b56f43611df74a",
            "withdrawal_credentials": "0x008e0d4e9587369b2301d0790347320302cc0943d5a1884560367e8208d920f2",
            "effective_balance": "1",
            "slashed": false,
            "activation_eligibility_epoch": "1",
            "activation_epoch": "1",
            "exit_epoch": "1",
            "withdrawable_epoch": "1"
            }
        }
    }
    ```
    - Run the staking-deposit-cli in an `offline` environment with your mnemonic to generate the `blstoexecutionchange` message
    ```bash
    ./deposit generate-bls-to-execution-change
    ```
    - You'll go through an interactive process that will ask you for the following information:
      - Your mnemonic language
      - The network you wish to perform this operation for.
      - Enter your mnemonic next.
      - Next, you will be asked for the starting index you used to create your validators, this will be 0 unless you created validators from a non default index.
      - You will then be asked the validator indices for the validators you wish to generate the message for.
      - You can find your validator indices in your Prysm validator client logs. 
      - You will be asked for your withdrawal credentials.
      - You will be asked for the thereum address you wish to use to receive your withdrawn funds.
      - verify the `blstoexecutionchange` message that the corresponding validator will set to the chosen Ethereum address.
       ```bash
        [
            {
            "message": {
            "validator_index": "838",
            "from_bls_pubkey": "0xb89bebc655569726a318c8e9971bd3144497c61aea4a6578a7a4f94b547dcba5bac16a89108b6b6a1fe3695d1a874a0b",
            "to_execution_address": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0a"
            },
            "signature": "0xa42103e15d3dbdaa75fb15cea782e4a11329eea77d155864ec682d7907b3b70c7771960bef7be1b1c4e08fe735888b950c1a22053f6049b35736f48e6dd018392efa3896c9e427ea4e100e86e9131b5ea2673388a4bf188407a630ba405b7dc5"
        },
            {
            "message": {
            "validator_index": "20303",
            "from_bls_pubkey": "0xb89bebc699769726a502c8e9971bd3172227c61aea4a6578a7a4f94b547dcba5bac16a89108b6b6a1fe3695d1a874a0b",
            "to_execution_address": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b"
            },
            "signature": "0xa86103e15d3dbdaa75fb15cea782e4a11329eea77d155864ec682d7907b3b70c7771960bef7be1b1c4e08fe735888b950c1a22053f6049b35736f48e6dd018392efa3896c9e427ea4e100e86e9131b5ea2673388a4bf188407a630ba405b7dc5"
            }
        ]
        ```
      - Submit your signed `blstoexecutionchange` message to the Ethereum network using `prysmctl`.
      - On successful submission, the SignedBLStoExecutionChange messages are included in the pool waiting to be included in a block.
      - The withdrawal will be initiated by using the execution address you provided, and your validators’ withdrawal credentials will change to look something like this:
      ```bash
       *0x010000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b* 
        ```
      - You can track your withdrawal on an Ethereum Proof of Stake Block Scanner or you can confirm the withdrawal by checking withdrawal_credentials updated by querying your local beacon node:
      
      ```bash
      curl -X 'GET' \ 'http://YOUR_PRYSM_NODE_HOST:3500/eth/v1/beacon/states/head/validators/YOUR_VALIDATOR_INDEX' \
        -H 'accept: application/json'
        ````






### References
- [Staking Deposit CLI GitHub](https://github.com/ethereum/staking-deposit-cli)
- [Check Node and Validator Status](https://docs.prylabs.network/docs/monitoring/checking-status)
- [Staking Launchpad](https://launchpad.ethereum.org/en/overview)