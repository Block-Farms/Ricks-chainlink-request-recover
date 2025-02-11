## Oracle request finder for chainlink node operator
As a chainlink node operator, you may have missed a few job requests. This tool helps you to find those unfullfilled requests, so you can execute them.

# How to run


Prepare `.env` file
1. `cp .env.example .env`


| ENV_VAR | Description |
| --- | --- |
| ORACLE_ADDRESS | Oracle contract address |
| START_BLOCK | When to start collecting events |
| END_BLOCK | When to end collecting events (use 'latest' to set to latest known block) |
| NODE_ADDRESS | Whitelisted key on your Oracle address for validation |
| RPC_URL | Ethereum HTTP node RPC url |
| BLOCK_INTERVAL | How many blocks to query in for loop |
| PRIVATE_KEY | Private key (without 0x) to send a request to your Oracle contract - only needed if you wish to fulfill requests |
| GAS_TOKEN | Ticker of the gas token - only needed if you wish to fulfill requests |
| SKIP_UNPROFITABLE | Set to false to also fulfill unprofitable requests - only needed if you wish to fulfill requests |
| GAS_LIMIT_MULTIPLIER | Multiply gas limit - only needed if you experience out of gas errors during fulfilling |


2. `npm i`
3. `npm run find-missing-requests` - creates `unfulfilled_requests` file when missing requests found

Example:
```
➜  npm run find-missing-requests

> node index.js

Start processing blocks from 9229786 to 9249786
We got 445 request events. Start processing...
Request without fillfilment found! Blocknumber is 9247478
Start processing blocks from 9249786 to 9269786
```

## Fulfilling requests

We highly recommend generating new `PRIVATE_KEY` and additing it to your `Oracle.sol` contract for by calling

`setFulfillmentPermission(YOUR_NEW_ETH_ADDRESS, true)` from owner of the contract.

4. `npm run fulfill-requests` - it will try to fulfill missing requests from `unfulfilled_requests` file.

Example:

```
➜  npm run fulfill-requests

> node submit.js

nonce 1866
A new successfully sent tx 0x0a3ee324e506bdf34d6cba8f9a59f25794f5ec03a07ad12ab37255d4caf40aad
```

Unprofitable requests will be filtered, skipped and saved to: /storage/unprofitable_requests

## Run with Docker
1. Edit env variables in `./docker-compose.yml`
2. `docker-compose up -d chainlink-request-recover-eth-usd` it will store found requests in `/data/dmakers` folder
3. `docker-compose up chainlink-request-tx` to fulfill found requests
