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
   Check if there is the .env file in the working directory, copy it, rename it as ".env"
   ```bash
   $ ls -al | grep env
   -rw-r--r--@   1 user  admin    6311 May 27 11:29 .env-sample
   $ cp .env-sample .env
   ```

5. 
