# ElectrumX Server (macOS)

## Pre-requisites
1. Ensure python (version >= 3.9) is installed.
2. If the indexing of your Bitcoin Core node was disabled, quit the process, modify the bitcoin.conf file as below. Reindexing might take several hours.
    ```bash
    txindex=1
    blockfilterindex=1
    reindex=1
    ```
3. Install relevant homebrew and python packages.
   ```bash
   brew install leveldb
   pip3 install plyvel
   pip3 install aiohttp
   pip3 install uvloop
   pip3 install pylru
   ```
4. Move on to the directory where you want to download ElectrumX code and download it.
   ```bash
   cd /Users/me
   git clone https://github.com/spesmilo/electrumx.git
   cd electrumx
   ```
