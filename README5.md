# New Chain based on Bitcoin version 27.0 (macOS)

## Pre-requisites
1. Download bitcoin version 27.0 source code
   
   ```bash
   $ git clone https://github.com/bitcoin/bitcoin.git
   $ cd bitcoin
   ```

2. Download CPUminer or BFGminer
   
   CPUminer download => https://sourceforge.net/projects/cpuminer/files/   
   BFGminer download => https://sourceforge.net/projects/bfgminer/files/   
   BFGminer can also be download through Homebrew package manager
   
   ```bash
   $ brew install bfgminer
   ```

## Bitcoin source code modifications
3. File to edit => bitcoin/src/chainparamsbase.cpp
   
   Line 43: modified the rpc and onion service target port to 50010 and 50012 respectivelyF

4. File to edit => bitcoin/src/chainparamsseeds.h

   Set the chain seed nodes to empty (since this is will be a seperate network from Bitcoin Core)

   ```
   static const uint8_t chainparams_seed_main[] = {};
   ```

5. File to edit => bitcoin/src/kernel/chainparams.cpp

   A: Set the BIP34 Height to 500 and BIP34 Hash to uint256(). The reason why this is not set to 0 is to enable mining at the start.
      CPUminer software doesn't recognize BIP34 rule, so it is impossible to mine blocks using it with BIP34 activated from the very beginning.
      If BIP34 is set to zero and attempt mining using CPUminer, you will get the following messages in the debug.log file =>
      ```
      [2024-07-05 14:43:51] DEBUG: hash <= target
      Hash:   00000000ab2d84fe5fdb9e250838c5e7c2751a2cbbec90e23fe75e4e923c8a23
      Target: 00000000ffff0000000000000000000000000000000000000000000000000000
      [2024-07-05 14:43:51] accepted: 0/1 (0.00%), 4953 khash/s (booooo)
      [2024-07-05 14:43:51] DEBUG: reject reason: bad-cb-height
      ```
      For more information with regard to BIP34 and CPUminer, see here: https://bitcointalk.org/index.php?topic=5293244.0

   B: Set the BIP65, BIP66, CSV, Segwit and MinBIP9Warning Height to 0. (Always active)  
   C: Set the TapRoot nStartTime to ``` Consensus::BIP9Deployment::ALWAYS_ACTIVE. ```   
   D: Set the TapRoot nTimeout to ``` Consensus::BIP9Deployment::NO_TIMEOUT. ```   
   E: Set the TapRoot minimum activation height to 0. (No activation delay)  
   F: Set the MinimumChainWork to 0.  
   G: Set the ``` defaultAssumeValid = genesis.GetHash(); ```   
   H: Modify the magic bytes to any number you want to use.   
   I: Set the nDefaultPort to 50011.
   J: Set the assumed blockchain size and chainstate size to 0.   
   K: vSeeds and vFixedSeeds need to be empty. ``` vSeeds.clear(); vFixedSeeds.clear(); ```   
   L: Since this is a new chain, there are no checkpoints.   
      ```
      checkpointData = {
               {
                   {0, genesis.GetHash()},
               }
           };
      ```
   M: Modify the chainTxData to represent the genesis block.
      ```
      chainTxData = ChainTxData {
          // Data from RPC: getchaintxstats
          .nTime    = 1231006505,
          .nTxCount = 1,
          .dTxRate  = 0,
      };
      ```

7. File to edit => bitcoin/src/validation.cpp   
   
   Line 1986: Set the function ```ChainstateManager::IsInitialBlockDownload()``` to always return false.    
   If the Initial Block Download state of the node is true, then mining is impossible as the node thinks it is not fully synced.
   Bitcoin nodes identify themselves in Initial Block Download mode when the time interval between the timestamp of their last block
   and current time exceed 24 hours. The new chain would certainly identify itself in Initial Block Download mode since there is only
   the genesis block (hard coded as Jan/3rd, 2009). So in order to bypass this condition, the function should be modifed to always
   return false. If mining is attempted without modifying this function, the following message will appear on the terminal =>
   
   ```
   2024-07-05T05:37:33Z CreateNewBlock(): block weight: 800 txs: 0 fees: 0 sigops 400
   2024-07-05T05:43:51Z Saw new header hash=00000000ab2d84fe5fdb9e250838c5e7c2751a2cbbec90e23fe75e4e923c8a23 height=1
   2024-07-05T05:43:51Z [error] AcceptBlock: bad-cb-height, block height mismatch in coinbase
   2024-07-05T05:43:51Z [error] ProcessNewBlock: AcceptBlock FAILED (bad-cb-height, block height mismatch in coinbase)
   ```

## Node Configuration and Mining
8. Build (Creating UNIX Executable files)
   
   ```
   $ cd bitcoin
   $ ./autogen.sh
   $ ./configure
   $ make
   ```

9. Make two configuration files and allocate two different node ports, rpc ports and data directories. 
   This is to run two nodes on a local device (in my case, MacBook Air M1 macOS Ventura). A single node not connected to any peers do not
   respond to CPUminer's getblocktemplate rpc request so running two nodes is necessary.
   
   ```
   $ ./bitcoind -conf=/Users/legacy/bitcoin/bitcoin_modified.conf1 -debug=rpc
   $ ./bitcoind -conf=/Users/legacy/bitcoin/bitcoin_modified.conf2 -debug=rpc
   $ ./bitcoin-cli -conf=/Users/legacy/bitcoin/bitcoin_modified.conf1 addnode "127.0.0.1:50014" "add"  # add node2 to node1
   $ ./bitcoin-cli -conf=/Users/legacy/bitcoin/bitcoin_modified.conf2 addnode "127.0.0.1:50011" "add"  # add node1 to node2
   $ ./bitcoin-cli -conf=/Users/legacy/bitcoin/bitcoin_modified.conf1 getpeerinfo  # check if node2 is connected
   $ ./bitcoin-cli -conf=/Users/legacy/bitcoin/bitcoin_modified.conf2 getpeerinfo  # check if node1 is connected
   $ ./minerd --algo=sha256d --url=http://localhost:50010 --user=blacksabbath --pass=paranoid --coinbase-addr=bc1q7qquhjm9m7z69w5pu5qjt5k2tz7e89wk5yhv6n --debug
   ```

10. If all things are set properly, new blocks will be mined and you will be able to see the following messages on macOS terminal...

     ```
     [2024-07-06 02:17:49] LONGPOLL pushed new work
     [2024-07-06 02:17:49] thread 0: 1980 hashes, 2575 khash/s
     [2024-07-06 02:17:49] thread 1: 1912 hashes, 2598 khash/s
     [2024-07-06 02:17:49] DEBUG: hash <= target
     Hash:   000000008f4753dfb9f0f6de650fd583c4af082c6a46819208dde8606c96bc92
     Target: 00000000ffff0000000000000000000000000000000000000000000000000000
     [2024-07-06 02:17:49] accepted: 1/1 (100.00%), 5172 khash/s (yay!!!)
     ```

     ```
     2024-07-05T17:10:32Z Leaving InitialBlockDownload (latching to false)
     2024-07-05T17:10:39Z CreateNewBlock(): block weight: 800 txs: 0 fees: 0 sigops 400
     2024-07-05T17:17:49Z Saw new header hash=000000008f4753dfb9f0f6de650fd583c4af082c6a46819208dde8606c96bc92 height=3
     2024-07-05T17:17:49Z UpdateTip: new best=000000008f4753dfb9f0f6de650fd583c4af082c6a46819208dde8606c96bc92 height=3 version=0x20000000 log2_work=34.000022 tx=4 date='2024-07-05T17:17:49Z' progress=1.000000 cache=0.3MiB(1txo)
     ```












   


   
