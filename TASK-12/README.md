1. Setup the Godwoken Testnet Network in MetaMask


From the network selection dropdown, select "Custom RPC".

![metamask-network-menu](https://user-images.githubusercontent.com/88998318/130059264-15e7a987-69de-4f6d-b926-8274f61e1ba7.png)

MetaMask Network Menu
From there you will be presented with a form to specify the network settings.

MetaMask Add Network
Enter the following details.


![metamask-networks2](https://user-images.githubusercontent.com/88998318/130059285-d2aa9826-de77-4b51-8bbb-96ef7d8511c7.png)



```
Network Name: Godwoken Testnet
RPC URL: https://godwoken-testnet-web3-rpc.ckbapp.dev
Chain ID: 71393
Currency Symbol: <Leave Empty>
Block Explorer URL: <Leave Empty>
```

  
2. clone the existing Ethereum dapp 
  


```
cd ~/projects
git clone git@github.com:terryboy/dapp-nervos-integration.git
cd dapp-nervos-integration
yarn
yarn build
yarn start:ganache
yarn ui
```

open a browser tab to http://localhost:3000 to view the dApp UI!
  
  

3. Install Polyjuice Dependencies
  

```
cd ~/projects/dapp-nervos-integration
yarn add @polyjuice-provider/web3@0.0.1-rc7 nervos-godwoken-integration@0.0.6
```
  
  
4. Configure the Web3 Provider for the Polyjuice Web3 Provider
   
We will modify these config:
```
cd ~/projects/dapp-nervos-integration/src
vi config.ts
```
modify export const CONFIG  content:
  
```
 export const CONFIG = {
    WEB3_PROVIDER_URL: 'https://godwoken-testnet-web3-rpc.ckbapp.dev',
    ROLLUP_TYPE_HASH: '0x4cc2e6526204ae6a2e8fcf12f7ad472f41a1606d5b9624beebd215d780809f6a',
    ETH_ACCOUNT_LOCK_CODE_HASH: '0xdeec13a7b8e100579541384ccaf4b5223733e4a5483c3aec95ddc4c1d5ea5b22'
  };
```

  
We need to import a few dependencies.
  
We will update the main UI in the file

```
cd ~/projects/dapp-nervos-integration/src/ui
vi app.tsx
```

Next, we add the following lines in the main dependency importation section of the file.
  
```
import { PolyjuiceHttpProvider } from '@polyjuice-provider/web3';
import { CONFIG } from '../config';
```

This imports the Polyjuice Web3 Provider, which we will use in a moment, and the config file that we just created.

Next we prepare a few constants, create the Polyjuice Provider, and use the Polyjuice Provider with a Web3 instance.
  
    
```
const godwokenRpcUrl = CONFIG.WEB3_PROVIDER_URL;
    const providerConfig = {
    rollupTypeHash: CONFIG.ROLLUP_TYPE_HASH,
    ethAccountLockCodeHash: CONFIG.ETH_ACCOUNT_LOCK_CODE_HASH,
    web3Url: godwokenRpcUrl};
    const provider = new PolyjuiceHttpProvider(godwokenRpcUrl, providerConfig);
    const web3 = new Web3(provider);
```
 Now our application is setup to communicate with Polyjuice using Web3!

5. Set High Gas Limit
  
```
cd ~/projects/dapp-nervos-integration/src/lib/contracts
vi SudtERC20ProxyWrapper.ts
vi ElectionWrapper.ts
```
 

We define a simple object that contains the gas property used by MetaMask.

```
const DEFAULT_SEND_OPTIONS = {
    gas: 6000000
};
```
This can be added in the top region of the file, and we will be using this constant in several other places.

We will be adding it into the ElectionWrapper.ts object passed to send() :


```
.send({
            ...DEFAULT_SEND_OPTIONS,
            from: fromAddress
```

  
6. Display Polyjuice Address in Your Application
 
Every Ethereum address can be translated into a Polyjuice address on Nervos' Layer 2. This can be done using the AddressTranslator class.
We will show the basic code here

**import { AddressTranslator } from 'nervos-godwoken-integration';**
  
We can then use the following code to find the Polyjuice address.


```
const addressTranslator = new AddressTranslator();
const polyjuiceAddress = addressTranslator.ethAddressToGodwokenShortAddress(ethereumAddress);
```

  
7. View the Completed  your dapp 


```
cd dapp-nervos-integration
yarn
yarn build
yarn ui.
```
change your MetaMask network to Godwoken Testnet,
open http://localhost:3000

