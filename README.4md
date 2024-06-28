# Bitcoin regtest and CPUminer

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
  $ ls -al | grep 'bitcoind\|bitcoin-cli'
  -rwxr-xr-x@   1 ieunmi  User     546264 Jun 28 23:25 bitcoin-cli
  -rw-r--r--    1 ieunmi  User       1357 Jun 28 18:28 bitcoin-cli-res.rc
  -rw-r--r--    1 ieunmi  User      62771 Jun 28 18:28 bitcoin-cli.cpp
  -rw-r--r--@   1 ieunmi  User    3253584 Jun 28 23:25 bitcoin_cli-bitcoin-cli.o
  -rwxr-xr-x@   1 ieunmi  User   15916309 Jun 28 23:25 bitcoind
  -rw-r--r--@   1 ieunmi  User    2154840 Jun 28 23:14 bitcoind-bitcoind.o
  -rw-r--r--    1 ieunmi  User       1344 Jun 28 18:28 bitcoind-res.rc
  -rw-r--r--    1 ieunmi  User       9445 Jun 28 18:28 bitcoind.cpp
  ```
