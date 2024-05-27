# Bitcoin Core (macOS)
This repository explains the methods and processes used to install Bitcoin Core and its relevant packages/files.

Materials used
1. MacBookAir M1 (macOS Ventura)
2. HDD (4TB External Hard Drive)
3. Homebrew (macOS package manager)

## Installation of Bitcoin Core
As of this writing (May 28th, 2024), the latest release version of Bitcoin Core is 27.0 and can be downloaded from the link below. Download the relevant version of Bitcoin Core according to your computer settings.
https://bitcoincore.org/bin/bitcoin-core-27.0/
For macOS users, download bitcoin-27.0-arm64-apple-darwin-unsigned.tar.gz and then move the file to the directory you want. Once the file is uncompressed, a new folder called bitcoin-27.0 will appear. Move on to the uncompression_path/bitcoin-27.0/bin directory
```bash
cd bitcoin-27.0/bin
```
Inside the directory, you will see 7 Unix Executable Files and if you try to run them in the terminal, you might receive the following warning message: "bitcoin-qt" is damaged and cannot be opened. You should move it to the Trash. In order to resolve this issue, enter the commands below.
```bash
codesign -v -d bitcoin-qt
codesign -s - bitcoin-qt
xattr -d com.apple.quarantine $(pwd)/bitcoin-qt
```
Do the same commands for the rest of the Unix Executable Files inside the bitcoin-27.0/bin directory.
