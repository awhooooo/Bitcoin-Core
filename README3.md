# BTC Explorer (macOS)

## Pre-requisites
1. Bitcoin Core and ElectrumX has to be fully synced.
2. Download node.js => https://nodejs.org/en
3. Check whether node.js and npm is properly installed.
   
   ```bash
   $ node --version
   ```

## Setting up BTC Explorer
4. Download BTC Explorer => https://github.com/janoside/btc-rpc-explorer
   
   ```bash
   $ cd /Users/me
   $ git clone https://github.com/janoside/btc-rpc-explorer
   $ cd btc-rpc-explorer
   $ npm install
   $ npm audit fix
   $ npm --version
   ```
   Check if there is the .env or .env-sample file in the working directory, copy it, rename it as ".env"
   ```bash
   $ ls -al | grep env
   -rw-r--r--@   1 user  admin    6311 May 27 11:29 .env-sample
   $ cp .env-sample .env
   ```

5. Modify the .env file according to your environment settings.
   
   ```bash
   $ nano /Users/me/btc-rpc-explorer/.env
   ```
   
   ```bash
   BTCEXP_HOST=127.0.0.1
   BTCEXP_PORT=3002
   
   BTCEXP_BITCOIND_URI=bitcoin://blacksabbath:paranoid@127.0.0.1:8332?timeout=10000
   BTCEXP_BITCOIND_USER=blacksabbath
   BTCEXP_BITCOIND_PASS=paranoid
   BTCEXP_BITCOIND_COOKIE="/path/to/HDD_Bitcoin_data_directory/.cookie"

   BTCEXP_ADDRESS_API=electrum
   BTCEXP_ELECTRUM_SERVERS=tcp://127.0.0.1:50001
   BTCEXP_NO_INMEMORY_RPC_CACHE=true
   
   BTCEXP_DEMO=true
   BTCEXP_NO_RATES=false
   ```

   ```bash
   $ npm start
   ```
