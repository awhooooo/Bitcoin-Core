# Bitcoin regtest and CPUminer (macOS)

## Building Bitcoin
1. Cloning the github repository and building
   
    ```bash
    $ git clone https://github.com/bitcoin/bitcoin
    $ cd bitcoin
    $ ./autogen.sh
    $ ./configure
    $ make
    ```

   Results =>
  
    ```bash
    ...
      CXX      test/fuzz/libtest_fuzz_a-util.o
      CXX      test/fuzz/util/libtest_fuzz_a-descriptor.o
      CXX      test/fuzz/util/libtest_fuzz_a-mempool.o
      CXX      test/fuzz/util/libtest_fuzz_a-net.o
      AR       libtest_fuzz.a
      CXXLD    test/fuzz/fuzz
      CXX      univalue/test/object-object.o
      CXXLD    univalue/test/object
      CXX      univalue/test/unitester-unitester.o
      CXXLD    univalue/test/unitester
    Making all in doc/man
    make[1]: Nothing to be done for `all'.
    make[1]: Nothing to be done for `all-am'.
    ```

   Check whether bitcoind and bitcoin-cli is properly constructed =>
  
    ```bash
    $ cd src
    $ ls -al | grep 'bitcoind\|bitcoin-cli'
    -rwxr-xr-x@   1 User  admin     546264 Jun 28 23:25 bitcoin-cli
    -rw-r--r--    1 User  admin       1357 Jun 28 18:28 bitcoin-cli-res.rc
    -rw-r--r--    1 User  admin      62771 Jun 28 18:28 bitcoin-cli.cpp
    -rw-r--r--@   1 User  admin    3253584 Jun 28 23:25 bitcoin_cli-bitcoin-cli.o
    -rwxr-xr-x@   1 User  admin   15916309 Jun 28 23:25 bitcoind
    -rw-r--r--@   1 User  admin    2154840 Jun 28 23:14 bitcoind-bitcoind.o
    -rw-r--r--    1 User  admin       1344 Jun 28 18:28 bitcoind-res.rc
    -rw-r--r--    1 User  admin       9445 Jun 28 18:28 bitcoind.cpp
    ```

## Bitcoin regtest configuration file
2. Create a config file and set the parameters accordingly
   
   ```bash
   $ nano bitcoin_regtest.conf
   ```

   ```bash
   blockfilterindex=1
   dbcahce=512
   txindex=1
   datadir=/Users/legacy/BitcoinCore/bitcoin regtest/

   chain=regtest
   rpcpassword=paranoid
   rpcuser=blacksabbath
   server=1
   ```

3. Start bitcoind in regtest mode

   ```bash
   $ ./bitcoind -conf=/Users/legacy/BitcoinCore/bitcoin-27.0/bitcoin.conf3
   ```

4. Open another macOS Terminal window and check whether regtest is properly running
   
   ```bash
   $ ./bitcoin-cli -regtest -rpcuser=blacksabbath -rpcpassword=paranoid -rpcport=18443 -rpcconnect=localhost createwallet "mining"
   $ ./bitcoin-cli -regtest -rpcuser=blacksabbath -rpcpassword=paranoid -rpcport=18443 -rpcconnect=localhost loadwallet "mining"
   $ ./bitcoin-cli -regtest -rpcuser=blacksabbath -rpcpassword=paranoid -rpcport=18443 -rpcconnect=localhost getnewaddress
   bcrt1qm3swsrfz3whrep0cgg4qr8x6jff756tczkfw2r
   $ ./bitcoin-cli -regtest -rpcuser=blacksabbath -rpcpassword=paranoid -rpcport=18443 -rpcconnect=localhost getblockchaininfo
   {
     "chain": "regtest",
     "blocks": 1077,
     "headers": 1077,
     "bestblockhash": "423b7cabc1f50ae8557034df26b90f6c4ea152a164a8f17bc1762f6d9b835b6b",
     "difficulty": 4.656542373906925e-10,
     "time": 1718089599,
     "mediantime": 1718089598,
     "verificationprogress": 1,
     "initialblockdownload": true,
     "chainwork": "000000000000000000000000000000000000000000000000000000000000086c",
     "size_on_disk": 338697,
     "pruned": false,
     "warnings": [
       "This is a pre-release test build - use at your own risk - do not use for mining or merchant applications"
     ]
   }
   ```

5. Download CPUminer and connect it with regtest => https://sourceforge.net/projects/cpuminer/files/

   ```bash
   $ ./minerd --algo=sha256d --url=http://localhost:18443 --user=blacksabbath --pass=paranoid --threads=2 --coinbase-addr=bcrt1qm3swsrfz3whrep0cgg4qr8x6jff756tczkfw2r
   ```

   ```bash
   2024-06-28T15:39:57Z Saw new header hash=487cd5c013df975c360cbe060727245cb9e6a0bd0f6547a634cbfa4ab124f5b1 height=1137
   2024-06-28T15:39:57Z UpdateTip: new best=487cd5c013df975c360cbe060727245cb9e6a0bd0f6547a634cbfa4ab124f5b1 height=1137 version=0x20000000 log2_work=11.152285 tx=1138 date='2024-06-28T15:39:57Z' progress=1.000000 cache=0.3MiB(60txo)
   2024-06-28T15:39:57Z CreateNewBlock(): block weight: 808 txs: 0 fees: 0 sigops 400
   2024-06-28T15:39:57Z [mining] AddToWallet 6899753a0018b6967ff476bd3c9cea6cc11a6ec05c357829dd1556f3f0e2e52f  new Confirmed (block=487cd5c013df975c360cbe060727245cb9e6a0bd0f6547a634cbfa4ab124f5b1, height=1137, index=0)
   ```

   One more getblockchaininfo RPC method query and you'll see several more blocks have been created (mined)

   ```bash
   $ ./bitcoin-cli -regtest -rpcuser=blacksabbath -rpcpassword=paranoid -rpcport=18443 -rpcconnect=localhost getblockchaininfo
   {
     "chain": "regtest",
     "blocks": 1137,
     "headers": 1137,
     "bestblockhash": "487cd5c013df975c360cbe060727245cb9e6a0bd0f6547a634cbfa4ab124f5b1",
     "difficulty": 4.656542373906925e-10,
     "time": 1719589197,
     "mediantime": 1719589195,
     "verificationprogress": 1,
     "initialblockdownload": false,
     "chainwork": "00000000000000000000000000000000000000000000000000000000000008e4",
     "size_on_disk": 370712,
     "pruned": false,
     "warnings": [
       "This is a pre-release test build - use at your own risk - do not use for mining or merchant applications"
     ]
   }
   ```
