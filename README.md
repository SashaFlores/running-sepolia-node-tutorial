# running a sepolia node tutorial

This tutorial will guide you through the process of running a sepolia node on your local machine.

### Prerequisites:

- [Node.js](https://nodejs.org/en/download/) installed on your machine.
- [Metamask](https://metamask.io/download.html) installed on your browser.
- [IPFS](https://docs.ipfs.io/install/command-line-quick-start/)
- [Go-Ethereum](https://geth.ethereum.org/docs/install-and-build/installing-geth) installed on your machine.
- Or downloaded on your machine with developer tools included [Go-Ethereum](https://geth.ethereum.org/downloads) . 
- [Prysm](https://docs.prylabs.network/docs/getting-started)


### Step 1: open your terminal

```bash
$ geth --version
```

-  You should see the version of geth if it's installed properly on your machine, you may run into an error `geth isn't recognized as an internal or external command` then you need to add the path to the geth executable to your system environment variables by opening the environment variables settings on your machine and adding the path to the geth executable to the PATH variable.


- If you running your terminal or bash in VSCode, you may need to restart the terminal or bash to see the changes take effect. Sometimes you may need to add
the path to the geth executable to the PATH variable in your `.bashrc` or `.bash_profile` file in your home directory by adding the following line to the `.bashrc` and most probably you will need to restart your terminal or bash to see the changes take effect you will have a `PATH` already existing so you can add the second line for example:

    - export PATH="$PATH:/c/Program Files/Geth" 
    - export PATH="$PATH:/c/Users/yourUserName/.foundry/bin:/c/Program Files/Geth"

- Your `.bash_profile` is already properly set up to source .bashrc, as it includes:

    - test -f ~/.profile && . ~/.profile
    - test -f ~/.bashrc && . ~/.bashrc

- If you're using powerShell and you run into same error, you need to look up where your profile exists by running
```bash
$profile
```

- Normally, it should exists in `C:/Users/YourUserName/Documents/PowerShell`
  then you should add the path to the geth executable to the PATH variable in your profile file by adding the following line to the profile file and most probably you will need to restart your terminal  see the changes take effect:

    - $env:Path += ";C:\Program Files\Geth"

### Step 2: Generate Accounts

It isn't a necessity to generate accounts but it's a good practice to generate accounts for your blockchain network. To generate accounts, run the following command:

```bash
geth --datadir "pathToDirectoryToSaveGeneratedKeys" account new
```
- `--data-dir` flag specifies the directory where the blockchain data will be stored.
- `"pathToDirectoryToSaveGeneratedKeys"` is the path to the directory where the generated keys will be saved, in this tutorial it is saved in `keys` directory.
- `account new` command generates a new account.
- You will be prompted to enter a password to secure the account, make sure to remember the password as you will need it to unlock the account.
- You can generate as many accounts as you want by running the command multiple times.

```bash
$ geth --datadir "D:\Me-GitHub\running-sepolia-node-tutorial\devNode" account new
```

- A new directory will be automatically created named `keystore` in the specified directory to store the generated keys.

### Step 3: Initialize the node in a development mode first to test the network

To initialize the node in a development mode, run the following command:

```bash
geth --datadir "pathToDirectoryToRunNode" --dev --password "pathToPasswordFile.secret.text" 
```
- `pathToDirectoryToRunNode` is the directory path where the node will be initialized in this tutorial `devNode` is the selected directory.
- `password` flag specifies the password file that contains the password to unlock the account.
- `pathToPasswordFile.secret.text` is the path to the password file that contains the password to unlock the account, in this tutorial it's `secret.tx` for simplicity.

```bash
$ geth --datadir "D:\Me-GitHub\running-sepolia-node-tutorial\devNode" --password D:\Me-GitHub\running-sepolia-node-tutorial\devNode\secret.txt
```
- If everything is set up properly, you should see the node running in the terminal.
- You need to look for `url=\\.\pipe\geth.ipc` in the terminal, this is the endpoint to connect to the node, where it ends with `ipc` stands for Inter-Process Communication that is responsible for communication between Geth and other processes.
- You can connect to the node using the `geth attach` command in another terminal.

```bash
$ geth attach \\.\pipe\geth.ipc
```
- This opens up a JavaScript console where you can interact with the node. You can check all the available commands by running `eth` 

// Video of commands



Some of the commands you can run in the console are:

- `eth.accounts` - lists all the accounts in the node.


- `eth.getBalance(accounts[0])` - gets the balance of the specified account.

- `eth.blockNumber` - gets the block number of the latest block.
- `eth.coinbase` - gets the coinbase address, which the the default account that receives the mining rewards.
- `eth.chainId` - gets the chain id.
- `eth.feeHistory` - gets the fee history.
- `eth.gasPrice` - gets the current gas price.
- `eth.createAccessList` generates an access list for a transaction, specifying which storage slots and contracts will be accessed, potentially reducing gas costs under EIP-2930.
- `eth.sendTransaction({from:eth.accounts[accountIndex],to:eth.accounts[accountIndex],value:web3.toWei(1,"Ether")})` - sends 1 Eth from first account to second account and returns the transaction hash.

### You can find list of all commands line options [here](https://geth.ethereum.org/docs/fundamentals/command-line-options)

### Step 4: Initialize the node in a testnet mode

Since Ethereum started to implement the proof of stake consensus mechanism, we need two clients; an execution client which Geth and an [consensus client](https://ethereum.org/en/developers/docs/nodes-and-clients/#consensus-clients), there are various consensus clients available with different programming languages, in this tutorial we will be using Prysm since it uses Go. 


