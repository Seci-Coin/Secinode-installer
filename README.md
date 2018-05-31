## SeciNode SETUP NOTES

### System requirements

#### Remote Server:
An Ubuntu 16.04 64bit server is recommended with at least 768MB
of memory and 10GB space available when running a SeciNode.

#### Local Machine:
Windows 7 64 bit or newer, or Ubuntu 16.04 64bit Desktop.
At least 2048MB of memory and 10GB space available for chain data.

________________________________________________________________________________

### Local Machine

The following steps can also be performed via command line using seci-cli instead of using the SECI Qt wallet.

1.  If you haven't already, download and install the latest version of SECI Qt client for windows (or build for linux) - https://github.com/Seci-Coin/Seci/releases (or use seci-cli)

2.  Create seci.conf file in the same folder as your wallet.dat file (probably `%appdata%/roaming/seci) with the following contents:

```
rpcallowip=127.0.0.1
rpcuser=YourUserName
rpcpassword=YourRPCPassword
server=1
daemon=1
listen=1
```
Place the configuration file in its correction location:

For Linux: `${HOME}/.seci/seci.conf`
For Windows: `%appdata%\roaming\seci\seci.conf`

3.  Start the SECI Qt gui client and allow the chain to sync

4.  Go to Help --> Debug Window --> Console and enter: `getaccountaddress 0`
Copy the generated address to a text file.

5.  In the console enter: `masternode genkey`
Copy the generated private key to a text file (save this somewhere safe).

6.  Send exactly 30,000 SECI in a single transaction to the address generated from Step 4 (you can test with a smaller amount but make sure the address receives 30,000 before you enable the SeciNode in the following steps)

7.  After you have received the SECI from Step 5, go to Help --> Debug Window --> Console and enter: `masternode outputs`
    
You should see output similar to below indicating that the node has recognised coins as eligible to be used as collateral for a SeciNode.

```
{
  "s565175079b0f4dce2294dc003799ae0c9cb703cadd956a0a4224458f176eb6e": "0"
}
```

8.  Shutdown the SECI Qt wallet

9.  Modify the seci.conf created above and add the lines noted here:

```
rpcallowip=127.0.0.1
rpcuser=YourUserName
rpcpassword=YourRPCPassword
server=1
daemon=1
listen=1
# **** add the following new lines ****
maxconnections=256
masternode=1
masternodeprivkey=<your_private_key_generated_in_step_5>
externalip=<external_ip_of_your_remote_node>:9819
promode=1
```

### On the Remote SeciNode Server

1. Clone & Build the latest version of SECI from GitHub - https://github.com/Seci-Coin/Seci (build notes at bottom of main readme)

2. Clone the SeciNode installer from GitHub
``` git clone https://github.com/Seci-Coin/Secinode-installer```

3. Navigate to the installer folder

```cd Secinode-installer```

4. *Install & configure the SeciNode*

For Ubuntu 16.04 & 17.10

```bash install_ubuntu_17.10.sh```

When the script asks, input your remote server's IP Address and Private Key (You can copy your private key and paste into the remote server if connected with Putty by right clicking)

During the install, you can also install Fail2Ban and UFW (recommended, default settings should work well but we also recommend having a professional secure your server - we do not support server management and cannot be held responsible for the security of your server)

5.  Create a masternode.conf file in the same directory as your wallet.dat (likely `appdata/roaming/seci`).  The masternode.conf format consists of a space seperated text file. Each line consisting of an alias, ip address followed by port, masternode private key, collateral output transaction id and collateral output index (matching the result of Local Machine Step 7, above)

```
alias 127.0.0.1:9819 masternode_private_key collateral_output_txid collateral_output_index
```
e.g.
```
sn01 127.0.0.1:9819 92bDZcSpmT6UinHzR8VgaVZVgBVfbDRCh1WogXXXXXXtf9pyZ4Y d565175084b0f4dce2294dc003799ae0c9cb703cadd996a0a4224458f176eb6e 0
```

6.  Start SECI daemon using command `./secid`; you may get the following output:

```
Missing masternode input, please look at the documentation for instructions on masternode creation
```

7.  Then enter the following command using *seci-cli*

```
./seci-cli masternode debug
```

### Back on Your Local Machine

1.  Back on your local machine; start the SECI Qt client

2.  Go to Help --> Debug Window --> Console and enter: `masternode start`
    You should get the following message:

```
successfully started masternode
```

3.  If you then enter: `masternodelist`
    You should be able to locate the externalip of your remote node from the
    output list of SeciNodes.

    **Congratulations your SeciNode is up and running!**

    Alternatively, on the SECI Qt client Masternodes tab --> All Masternodes you should see your
    remote node ip address listed with its public key and other data


3.  You can now shutdown your local "cold" wallet
