# Bitcoin ElectrumX Server (macOS)

## Pre-requisites
1. Ensure python (version >= 3.9) is installed. In this case, anaconda3 python virtual environment is used. 
   
2. If the indexing of your Bitcoin Core node was disabled, quit the process, modify the bitcoin.conf file as below. Reindexing might take several hours. 
   ```bash
   txindex=1
   blockfilterindex=1
   reindex=0  # default
   ```
   Also, when reindexing is complete, make sure to disable the reindex option next time you run Bitcoin Core. Otherwise, the chainstate and indexes data will be gone and have to
   start the reindexing process all over again. In order to avoid enabling reindexing by mistake, use the command below when starting Bitcoin Core and remove the reindex option
   when reindexing is not needed.
   ```bash
   $ ./bitcoin-qt -conf=/Users/legacy/BitcoinCore/bitcoin-27.0/bitcoin.conf -reindex=1
   ```

3. Move on to the directory where you want to download ElectrumX code and download it.
   ```bash
   $ cd /Users/me
   $ git clone https://github.com/spesmilo/electrumx.git
   $ cd electrumx
   ```
   Check the contents of the electrumx folder, and find the setup.py file, build and install.
   ```bash
   -rw-r--r--   1 user  admin  2102 May 30 20:00 setup.py
   $ python setup.py build
   $ python setup.py install
   ```
   While installing, plyvel and aiorpcX might result version mismatches. If that is the case, try below...
   ```bash
   $ pip install --upgrade --no-cache-dir plyvel
   $ pip install 'aiorpcX[ws]>=0.23.0,<0.24'
   $ python setup.py install
   ```
   For anaconda environment users, successful installation would be like this...
   ```bash
   Installing electrumx_compact_history script to /Users/anaconda3/bin
   Installing electrumx_rpc script to /Users/anaconda3/bin
   Installing electrumx_server script to /Users/anaconda3/bin

   Using /Users/anaconda3/lib/python3.10/site-packages
   Finished processing dependencies for e-x==1.16.0
   ```

4. It seems that leveldb 1.23 and plyvel are incompatible, so leveldb 1.22 is required. Version 1.22 is available here =>
   https://github.com/google/leveldb/releases/tag/1.22
   ```bash
   $ tar -xvf leveldb-1.22.tar
   # Create a folder called "leveldb" under /opt/homebrew/Cellar and "1.22.0" folder under the "leveldb" folder you just created.
   # And put all the uncompressed data into the 1.22.0 folder. 
   $ cd /opt/homebrew/Cellar/leveldb/1.22.0
   $ mkdir -p lib
   $ cd lib
   $ cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -DBUILD_STATIC_LIBS=ON -DCMAKE_PREFIX_PATH=/opt/homebrew ..
   $ cmake --build .
   
   $ sudo ln -sf $(pwd)/libleveldb.dylib /opt/homebrew/lib/libleveldb.dylib
   $ sudo ln -sf $(pwd)/libleveldb.1.dylib /opt/homebrew/lib/libleveldb.1.dylib
   $ ln -sf $(pwd)/../include/leveldb /opt/homebrew/include/leveldb
   $ ln -s /opt/homebrew/lib/libleveldb.dylib /Users/anaconda3/lib/libleveldb.dylib
   $ ln -s /opt/homebrew/lib/libleveldb.1.dylib /Users/anaconda3/lib/libleveldb.1.dylib
   ```
   If plyvel was already installed, deleted it and reinstall again.
   ```bash
   $ export CPLUS_INCLUDE_PATH=-I"/opt/homebrew/include"
   $ export LIBRARY_PATH=-L"/opt/homebrew/lib"
   $ export DYLD_LIBRARY_PATH="/opt/homebrew/lib:$DYLD_LIBRARY_PATH"
   $ pip uninstall plyvel
   $ pip install plyvel --no-cache-dir
   ```
   Test whether plyvel is properly linked. The command below shouldn't return anything if set properly.
   ```bash
   $ python -c "import plyvel"
   ```

## Setting up ElectrumX
5. Create a new directory in the HDD (or in your computer if you have enough space) for ElectrumX database.

6. Install daemontools and setup dependencies. Since we are using daemontools (systemd is not supported on macOS), we have to make a symbolic link inside the service
   directory, connecting with /Users/me/electrumx/contrib/daemontools
   ```bash
   $ brew install daemontools
   $ sudo mkdir -p /opt/homebrew/etc/service
   $ cd /opt/homebrew/etc/service
   $ ln -s /Users/me/electrumx/contrib/daemontools electrumx
   ```

7. Creating a self-signed ssl certificate
   ```bash
   $ cd /Users/me/electrumx
   $ mkdir ssl_keys
   $ cd ssl_keys
   $ openssl genpkey -algorithm RSA -out server.key
   
   $ openssl req -new -key server.key -out server.csr
   You are about to be asked to enter information that will be incorporated
   into your certificate request.
   What you are about to enter is what is called a Distinguished Name or a DN.
   There are quite a few fields but you can leave some blank
   For some fields there will be a default value,
   If you enter '.', the field will be left blank.
   -----
   Country Name (2 letter code) [AU]:KR
   State or Province Name (full name) [Some-State]:Seoul
   Locality Name (eg, city) []:Seoul
   Organization Name (eg, company) [Internet Widgits Pty Ltd]:Seoul National University
   Organizational Unit Name (eg, section) []:College of Engineering       
   Common Name (e.g. server FQDN or YOUR name) []:awhooooo
   Email Address []:peterkids911@gmail.com
   
   Please enter the following 'extra' attributes
   to be sent with your certificate request
   A challenge password []:
   An optional company name []:.
   
   $ openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
   Certificate request self-signature ok
   subject=C=KR, ST=Seoul, L=Seoul, O=Seoul National University, OU=College of Engineering, CN=awhooooo, emailAddress=peterkids911@gmail.com
   ```

8. Inside /Users/me/electrumx/contrib/daemontools, there are two "run" files. One in /Users/me/electrumx/contrib/daemontools/run and the other one in
   /Users/me/electrumx/contrib/daemontools/log/run.
   Modify /Users/me/electrumx/contrib/daemontools/run as below...
   ```bash
   #!/bin/sh
   echo "Launching ElectrumX server..."
   USERNAME=$(envdir ./env printenv USERNAME)
   ELECTRUMX=$(envdir ./env printenv ELECTRUMX)
    
   # Setting the PATH and PYTHONPATH
   export PATH="/Users/anaconda3/bin:$PATH"
   export PYTHONPATH="/Users/anaconda3/lib/python3.10/site-packages"
   export LD_LIBRARY_PATH="/opt/homebrew/lib:$LD_LIBRARY_PATH"
    
   # Increase the file descriptor limit
   ulimit -n 10000 
    
   # Execute the ElectrumX server with the appropriate environment
   exec 2>&1 envdir ./env envuidgid $USERNAME /Users/anaconda3/bin/python /Users/anaconda3/bin/electrumx_server

   ```
   Modify /Users/me/electrumx/contrib/daemontools/log/run as below...
   ```bash
   #!/bin/sh
   exec multilog s500000 n10 "/path/to/HDD/ElectrumX/log/"
   ```

9. Inside /Users/me/electrumx/contrib/daemontools/env there are multiple settings options. Change them according to your situation.
   ```bash
   CACHE_MB => 2000
   COIN => Bitcoin
   DB_DIRECTORY => /path/to/HDD/ElectrumX
   NET => mainnet
   DB_ENGINE => leveldb
   ELECTRUMX => /Users/anaconda3/bin/electrumx_server
   DAEMON_URL => this option should be identical to your bitcoin.conf settings. In this case, http://blacksabbath:paranoid@127.0.0.1:8332/
   SERVICES => tcp://0.0.0.0:50001,ssl://0.0.0.0:50002,rpc://127.0.0.1:8000
   SSL_CERTFILE => /Users/me/electrumx/ssl_keys/server.crt
   SSL_KEYFILE => /Users/me/electrumx/ssl_keys/server.key
   USERNAME => me
   ```
   <img width="920" alt="Screenshot 2024-06-03 at 4 17 40 PM" src="https://github.com/awhooooo/Bitcoin-Core/assets/161196005/02d4a5d6-bcd3-4a2b-8d7c-9771692e106d">


10. Start syncing
    ```bash
    # Before syncing, make sure that Bitcoin Core is running with proper rpc settings.
    $ brew services start daemontools # this is optional
    $ cd /opt/homebrew/etc/service
    $ svscan /opt/homebrew/etc/service
    ```
   
    Go to the ElectrumX database directory in your HDD (or wherever that is), there will be /log/current file. Open it with a text editor and you'll see these messages...
    ```bash
    Launching ElectrumX server...
    INFO:electrumx:ElectrumX server starting
    INFO:electrumx:logging level: INFO
    INFO:Controller:Python version: 3.10.9 (main, Mar  1 2023, 12:20:14) [Clang 14.0.6 ]
    INFO:Controller:software version: ElectrumX 1.16.0
    INFO:Controller:aiorpcX version: 0.23.1
    INFO:Controller:supported protocol versions: 1.4-1.4.3
    INFO:Controller:event loop policy: None
    INFO:Controller:reorg limit is 200 blocks
    INFO:Daemon:daemon #1 at 127.0.0.1:8332/ (current)
    INFO:DB:switching current directory to /path/to/HDD/ElectrumX
    INFO:DB:using leveldb for DB backend
    INFO:DB:opened UTXO DB (for sync: True)
    INFO:DB:UTXO DB version: 8
    INFO:DB:coin: Bitcoin
    INFO:DB:network: mainnet
    INFO:DB:height: 656,563
    INFO:DB:tip: 0000000000000000000f6e8df3549ebac5a60054388439acd76c381f81853d19
    INFO:DB:tx count: 586,060,350
    INFO:DB:flushing DB cache at 2,000 MB
    INFO:DB:sync time so far: 21h 59m 26s
    INFO:History:history DB version: 1
    INFO:History:flush count: 566
    INFO:SessionManager:RPC server listening on 127.0.0.1:8000
    INFO:Prefetcher:catching up to daemon height 846,308 (189,745 blocks behind)
    ```

11. If stable maintenence is preferred, use daemontools. In this case, daemontools was not used, since I wanted to start and stop the syncing process whenever I want. The
    ElectrumX syncing process might take several hours or even days. The log file informs you how much time is left until full synchronization. If you want to quit syncing
    process, simply press ctrl + C (keyboard interrupt).

    Or you can kill the process by kill <PID> command. The ElectrumX server will automatically flush all data being processed
    into database and whenever you restart the process and the syncing will progress from the point where you left previously.
    ```bash
    $ ps aux | grep electrum
    $ kill <electrumx_server process id>
    ```

    Another method is using the electrumx_rpc file. In order to use this command, rpc address and port must be in the SERVICES environment variable.
    ```bash
    $ cd /Users/anaconda3/bin
    $ ls -al | grep electrumx
    -rwxr-xr-x    1 user  staff      207 May 30 20:33 electrumx_compact_history
    -rwxr-xr-x    1 user  staff      183 May 30 20:33 electrumx_rpc
    -rwxr-xr-x    1 user  staff      189 May 30 20:33 electrumx_server
    $ ./electrumx_rpc stop
    "stopping"
    ```
    
    ```bash
    WARNING:Controller:received SIGTERM signal, initiating shutdown
    INFO:Controller:shutting down
    INFO:Prefetcher:cancelled; prefetcher stopping 
    INFO:BlockProcessor:flushing to DB for a clean shutdown...
    INFO:DB:flushed filesystem data in 0.01s
    INFO:History:flushed history in 2.4s for 1,669,124 addrs
    INFO:DB:flushed 6,465 blocks with 10,373,272 txs, 5,428,110 UTXO adds, 4,350,829 spends in 9.7s, committing...
    INFO:DB:flush #229 took 33.2s.  Height 489,759 txs: 261,961,361 (+699,120)
    INFO:DB:tx/sec since genesis: 7,911, since last flush: 7,429
    INFO:DB:sync time: 09h 11m 52s  ETA: 1d 12h 34m
    INFO:Controller:shutdown complete
    ```
