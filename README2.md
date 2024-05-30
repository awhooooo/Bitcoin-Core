# ElectrumX Server (macOS)

## Pre-requisites
1. Ensure python (version >= 3.9) is installed.
2. If the indexing of your Bitcoin Core node was disabled, quit the process, modify the bitcoin.conf file as below. Reindexing might take several hours. Once reindexing is complete, disable the option. If not disabled, your node will go through reindexing process all over again when you restart Bitcoin Core.
    ```bash
    txindex=1
    blockfilterindex=1
    reindex=1
    ```
3. Move on to the directory where you want to download ElectrumX code and download it.
   ```bash
   cd /Users/me
   git clone https://github.com/spesmilo/electrumx.git
   cd electrumx
   ```
