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
   Check the contents of the electrumx folder, and find the setup.py file, build and install.
   ```bash
   -rw-r--r--   1 user  admin  2102 May 30 20:00 setup.py
   python setup.py build
   python setup.py install
   ```
   While installing, plyvel and aiorpcX might result version mismatches. If that is the case, try below...
   ```bash
   pip install --upgrade --no-cache-dir plyvel
   pip install 'aiorpcX[ws]>=0.23.0,<0.24'
   python setup.py install
   ```
   For anaconda environment users, successful installation would be like this...
   ```bash
   Installing electrumx_compact_history script to /Users/anaconda3/bin
   Installing electrumx_rpc script to /Users/anaconda3/bin
   Installing electrumx_server script to /Users/anaconda3/bin

   Using /Users/anaconda3/lib/python3.10/site-packages
   Finished processing dependencies for e-x==1.16.0
   ```
4. There is a version mismatch between leveldb 1.23 and plyvel, so leveldb 1.22 is required. Version 1.22 is available here =>
   https://github.com/google/leveldb/releases/tag/1.22
   ```bash
   tar -xvf leveldb-1.22.tar
   cd leveldb-1.22
   cd build
   export CPLUS_INCLUDE_PATH=/usr/local/include
   export LIBRARY_PATH=/usr/local/lib
   cmake -DCMAKE_BUILD_TYPE=Release ..
   cmake --build .
   sudo cp -r libleveldb.* /usr/local/lib/
   sudo cp -r ../include/leveldb /usr/local/include/
   ```
   If plyvel was already installed, deleted it and reinstall again.
   ```bash
   pip uninstall plyvel
   pip install plyvel --no-cache-dir
   ```
   Test whether plyvel is properly linked. The command below shouldn't return anything if set properly.
   ```bash
   python -c "import plyvel"
   ```


   



