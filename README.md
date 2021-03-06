# Token Exchange webapp with EIP-712 signed off-chain (gasless) transactions


## What is this app about?

![image info](testapp-diagram.png)

For this assignment, the relayer service is called the Token Exchange.
It accepts off-chain ERC712 signed bids from users to buy Ester ERC20 tokens ("ESTR") on the Rinkeby blockchain (a test network for Ethereum).
It batches their bids and makes gasless transactions for them.

You can try the app at https://token-exchange-node.herokuapp.com/
Remember to set your metamask to the rinkeby network.


### How it works (roughly)

Users submit bids on the page, signed/encrypted with their metamask wallets/private keys.

Every 30 seconds, their Buy bids are auto-batched sent to the Token Exchange blockchain contract for decoding/verification.

Once verified on-chain based on EIP-712 standards, the Token Exchange executes transfer of ESTR tokens to the end-buyers. (The Exchange holds a starting amount of ESTR tokens which it sells to the buyers.)

End-buyers can go to the ESTR token contract and check their balance with their wallet addresses.

## What is ESTER (ESTR) token?

ESTR Token is an ERC-20 token, denominated to 18 decimals, similar to ETH. 
It has a 1:1 ratio with ETH. You can send the contract ETH to get ESTR tokens. Just like WETH.


## How to try the deployed app

You can try the app at https://token-exchange-node.herokuapp.com/
Remember to set your metamask to the rinkeby network.

After signing your bid, remember to confirm and save it.

In intervals of 30 seconds, the app will send your signed bid to our Token Exchange contract,which willl carry out the transaction to buy Ester tokens for you.

(Actually the Token Exchange will transfer ESTR tokens from its holdings to you)
You can verify that you have the ESTR tokens by going to the ESTR contract and use Read the Contract for your account balance.



ESTR contract has been deployed on rinkeby: https://rinkeby.etherscan.io/address/0xe0427767282c793feb3896d048395ff543dbcab2

Token Exchange contact is deployed on rinkeby: https://rinkeby.etherscan.io/address/0xadd39d12ad9b8ffe3dcb48ac3822b94dd1308d2e



### To try the app with the already-deployed contracts:
1. Git clone or download this project.
cd into project using terminal.

2. Then run using nodejs:

`node index.js`

(You might need to run `npm install` first.)

3. Then, go to page http://localhost:3000/ in your browser.

Make sure your metamask wallet is on Rinkeby network, else switch to it, and reload webpage.


4. Create your bids using the form.

5. Click Sign bid, then sign with your metamask account.

6. Then click Post bid to confirm and save it in the system/page.

You can then switch to another account in your metamask to send another transaction.
Or use the same account. You do not pay any gas. 

The page will auto-send transaction batches using its own wallet. keys stored in .env

>Note: 
>When the server wallet runs out of rinkeby eth, you can send more to it using https://faucets.chain.link/rinkeby 
>The address is 0x20A5C66B2f591c15BF3dF02618390683648C8C35


## How to deploy your own app on Rinkeby

1. Add your own .env file with these:

`ETHERSCAN_TOKEN=[your etherscan api]`

`RINKEBY_RPC_URL=[your infura or alchemy node set to ethereum for deploying contracts]`

`PRIVATE_KEY=[your metamask wallet's private key]`


2. Compile the contracts: 

`npx hardhat compile`

3. Copy the compiled token exchange contract's abi (from "artifacts/contracts/TokenExchange.sol/TokenExchange.json") into "dependencies/exchange-abi.js".
Use the existing file for reference.


4. Deploy both contracts (Ester Token and Token Exchange) to rinkeby network:

`npx hardhat run scripts/deploy_token_exchange.js --network rinkeby`

5. Copy the deployed EsterToken contract address and paste it in the address here to verify the contract. 

`npx hardhat verify 0xE0427767282C793feb3896d048395ff543DBcAB2 --network rinkeby`

If you encounter a ENOENT error while verifying, run `npx hardhat clean` and then run the above verification command again.

Optional: If you like, you can also verify the exchange contract, passing it the EsterToken contract address as an argument, e.g.:

`npx hardhat verify 0xadd39d12aD9b8FFe3DCB48ac3822b94DD1308d2E --network rinkeby 0xE0427767282C793feb3896d048395ff543DBcAB2`

6. After verification, go to rinkeby etherscan for the ESTR contract and transfer ESTER tokens to the Token Exchange contracy.

Use Write Contract method to transfer 500000000000000000000 (500 tokens) or more, to the Exchange contract.
This is to give the Exchange some ESTR tokens, so that it can carry out user bid transactions.

7. Copy the deployed token exchange contract address and paste to "index.html" (line 152). 
This is so that the index.html webpage can sign and send to the Token Exchange contract. Remember to save.


7B. Copy the Token Exchange contract's ABI from "artifacts/contracts/TokenExchange.sol/TokenExchange.json" and paste it into "public/exchange-abi.js". 

This is so that our webpage can interact with the Token Exchange contract through its interface.


8. Then, run the page server using:

`node index.js`

9. Open your chrome browser (with metamask set to Rinkeby) http://127.0.0.1:8080

10. Interact with the web app page.

Enter your amounts or just use the default amounts. The default amounts will pass. 

The minimum bid price is 10, yes just 10. You can change this in the Token Exchange contract (but run all the above steps again). There is no minimum amount to buy. The default is 1000000000000000000, which is 1 ESTER after applying decimals (18) on it.

11. Click "Sign bid", then remember to "Confirm and save bid".

12. Then click "Send batch orders manually" or wait 30 seconds for bids to send automatically.

13. Then you can go to rinkeby.etherscan to see the Token Exchange contract, under Events, it will indicate that your user has bought Ester tokens.

You can verify your wallet balance by "reading" the ESTR token contract on Rinkeby.


## Limitations of this project

- I did not write user input verification in the frontend. Just some verification of user input in the smart contract, e.g. minimum bid price.
- There are no formally written tests for the contracts. Though I did test them with hardhat console log and observation of the results of transactions/functions.

This project is mainly for learning and demo purposes.


## References
Without which this demo will not be possible. 

I referenced from Compound's method for EIP-712 transactions. 
Specifically their delegation of signatures.
https://medium.com/compound-finance/delegation-and-voting-with-eip-712-signatures-a636c9dfec5e

Plus:
https://medium.com/metamask/eip712-is-coming-what-to-expect-and-how-to-use-it-bb92fd1a7a26
https://medium.com/coinmonks/eip712-a-full-stack-example-e12185b03d54 
