# Running Scripts

Remix IDE supports execution of JS & TS scripts.

## Write & Run a script

Create a file with `.js` or `.ts` extension and put your logic inside it. To run a script either:

- Click the play button at the upper left of the Main Panel.

- Right click on the script name in the `File Explorers` and click on the **Run** option.

- `Ctrl+Shift+S` when the script is displayed in the editor.

- Make the script the active file in the editor and run `remix.exeCurrent()` from Remix terminal

Here is a sample .js script:

```JavaScript
function test() {
  var num=12;
  if(num<10)
    console.log(num + " is less than 10");
  else
    console.log(num + " is not less than 10");
}

test();
```

Running it using one of options mentioned above will show result in Remix terminal

![](images/a-running-scripts-run.png)

## Why run scripts in Remix?

- To mimic how the front-end of your dapp will use web3.js or ethers.js
- To quickly deploy and interact with a bunch of instances of a contract without going through the Remix GUI.
- To run some tests on a previous deployed contract.

## Script to deploy a contract

Remix accepts async/await scripts to run [web3.js](https://web3js.readthedocs.io/) or [ethers.js](https://docs.ethers.io/) commands. The script needs to be wrapped in a self executing function.

## Setup

1. These scripts will need to access the contract's ABI. The ABI is located in the contract's metadata file. Make sure that this metadata file will be created by going to the **Settings** module and checking that the **Generate contract metadata** option is indeed **checked**.

2. Compile a Solidity file - to generate the contract metadata.

3. In the Deploy & Run plugin, choose the Environment.
   - Async/await scripts work on in all of the Environments: the Remix VM, Injected Provider (usually MetaMask), and External HTTP Provider.

## JS Scripts in the File Explorers

In the **scripts** folder of a **workspace**, there are 2 example files: one using **web3.js** and the other using **ethers.js**.

## Compile a contract and run a script in one click

When drafting a contract, it can be helpful to run a script just after the compilation succeeds.

With this technique, one can write some code then quickly deploy and set the state of the contracts.

The script can contains Mocha tests to be run.

In order to connect a contract with a script, add the **NatSpec** tag `@custom:dev-run-script` to the contract followed by the absolute file path, like:

```JavaScript
  /**
   * @title ContractName
   * @dev ContractDescription
   * @custom:dev-run-script file_path
   */
  contract ContractName {}
```

When you are ready to deploy the code for real, remove the NatSpec comment `@custom:dev-run-script`.

**ShortCut**: `Ctrl+Shift+S` , when editing a solidity file, will compile that file and then will run the script. In contrast, `ctrl+S` will only start the compiling.

## An Example Script

The example below deploys a solidity contract named **CustomERC20.sol**. This example is using the web3.js library. Ethers.js could also be used.

For more information about this example, please see: [running async/await scripts](https://medium.com/remix-ide/running-js-async-await-scripts-in-remix-ide-3115b5dd7687?source=friends_link&sk=04e650dfa65905fdab0ecd5b10513d41)

```JavaScript
(async () => {
  try {
    console.log('deploy...')

    // Note that the script needs the ABI which is generated from the compilation artifact.
    const metadata = JSON.parse(await remix.call('fileManager', 'getFile', 'browser/artifacts/CustomERC20.json'))
    const accounts = await web3.eth.getAccounts()

    let contract = new web3.eth.Contract(metadata.abi)

    contract = contract.deploy({
      data: metadata.data.bytecode.object,
      arguments: ["Mask", "N95"]
    })

    newContractInstance = await contract.send({
      from: accounts[0],
      gas: 1500000,
      gasPrice: '30000000000'
    })
    console.log(newContractInstance.options.address)
  } catch (e) {
    console.log(e.message)
  }
})()
```

## Adding external dependencies in Remix scripts

Remix supports some external dependencies from NPM which you can use when writing scripts. The following NPM packages are loaded by default when you open or create a script on Remix.

- ethers (v5): [Ethers v5 docs](https://docs.ethers.org/v5/)
- zokrates-js: [Zokrates docs](https://zokrates.github.io/toolbox/zokrates_js.html)
- web3: [Web3js docs](https://web3js.readthedocs.io/en/v1.10.0/)
- zksync-ethers: [Zksync ethers docs](https://www.npmjs.com/package/zksync-ethers)
- starknet: [JS library for Starknet docs](https://www.npmjs.com/package/starknet)
- snarkjs: [Snarkjs docs](https://github.com/iden3/snarkjs)
- circomlibjs: [Circomlibjs docs](https://github.com/iden3/circomlibjs)
- ffjavascript: [Finite Field Library in Javascript docs](https://github.com/iden3/ffjavascript)
- big-integer: [Big integer docs](https://www.npmjs.com/package/big-integer)
- @zk-kit/incremental-merkle-tree: [Incremental Merkle tree implementation in TypeScript docs](https://www.npmjs.com/package/@zk-kit/incremental-merkle-tree.sol)
- sindri: [Sindri CLI TypeScript SDK](https://www.npmjs.com/package/sindri)
- @semaphore-protocol/data: [Semaphore data docs](https://www.npmjs.com/package/@semaphore-protocol/data)
- @semaphore-protocol/group: [Semaphore group docs](https://www.npmjs.com/package/@semaphore-protocol/group)
- @semaphore-protocol/identity: [Semaphore identity docs](https://www.npmjs.com/package/@semaphore-protocol/identity)
- @semaphore-protocol/proof: [Semaphore proof docs](https://www.npmjs.com/package/@semaphore-protocol/proof)
- crypto-js: [Cryptojs docs](https://www.npmjs.com/package/crypto-js)
- aes-js: [AES-JS docs](https://www.npmjs.com/package/aes-js)
- multihashes: [Multihashes docs](https://www.npmjs.com/package/multihashes)
- openai: [TypeScript library for Openai docs](https://www.npmjs.com/package/openai)

These dependencies are available by default, you do not need to install them to use; just use the `import` or `require` keyword. For example:

```js
//using require
const { expect } = require("chai");

//using import
import { UltraHonkBackend } from "@aztec/bb.js";
```

If you try to import an unsupported module, you will get the error below:

```bash
<module_name> module require is not supported by Remix IDE
```

### Loading script dependencies

Asides the default script dependencies, you can load additional script dependencies using the Script Configuration panel. You can access this panel from the dropdown on the "Run Script" button.

```{note}
If the active file is not a script, then the "Run Script" button turns into the "Compile" button and the Open script configuration option is not present.
```

![Script config](./images/running-scripts/script-config.png)

You can now choose a specific configuration for your script from the available options.
