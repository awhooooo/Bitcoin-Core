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
$ cd bitcoin-27.0/bin
```
Inside the directory, you will see 7 Unix Executable Files and if you try to run them in the terminal, you might receive the following warning message: ("bitcoin-qt" is damaged and cannot be opened. You should move it to the Trash.) In order to resolve this issue, enter the commands below.
```bash
$ codesign -v -d bitcoin-qt
$ codesign -s - bitcoin-qt
$ xattr -d com.apple.quarantine $(pwd)/bitcoin-qt
```
Do the same commands for the rest of the Unix Executable Files inside the bitcoin-27.0/bin directory.

## Download of Bitcoin Core Data
```bash
$ ./bitcoin-qt
```
If you have enough storage space in your computer, just download the data with default data directory path. If not, set your Bitcoin Core data directory to custom, and make a new one in your HDD. The download & syncing process will start immediately. When using HDD, the download & syncing process might be slow. Try to wait until the process speeds up. If the slow download speed problem still lingers, try the steps shown below.
1. Stop bitcoin-qt
2. Open finder and move on to the directory in your HDD where Bitcoin Core data is being downloaded. Find a folder called "chainstate".
3. Rename "chainstate" to "chainstate_deprecated".
4. Create a folder called "chainstate" in the uncompression_path/bitcoin-27.0 directory and copy the entire contents of "chainstate_deprecated" and paste all of them to the new "chainstate" folder.
5. Create a symbolic link between the HDD Bitcoin data directory and bitcoin-27.0/chainstate directory.
```bash
$ ln -s /path/to/uncompression_path/bitcoin-27.0/chainstate /path/to/HDD_Bitcoin_data_directory
```

