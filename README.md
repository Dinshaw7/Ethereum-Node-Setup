
 This is a guide to deploy an Etherium node on CentOS 7 using Go Ethereum. Also tested and working on AWS Amazon Linux AMI.

 Requirements
 ------------
 You will need approximately 132 GB for a full Ethereum node, or approximately 22 GB for a Rinkeby node.

 Ensure you have sufficient capacity in your chosen data directory - /opt/eth in the instructions below.

 You will also need an appropriate amount of memory to facilitate syncing, say 4 GB.

--------------------------------------------------------------------------------------------------------------------------------------------------------------

 Note: 
 - Use "sudo" where necessary, e.g. for installation commands, or those involving system directories.

 - NEVER USE ADMIN ACCOUNT (e.g. root) FOR RUNNING NODE DAEMON AND / OR  FOR CONNECTING REMOTELY

--------------------------------------------------------------------------------------------------------------------------------------------------------------

 Step-by-step guide
 ------------------
 
 Add the steps involved:

 -  Install dependencies

        sudo yum -y update
        sudo yum install wget
        sudo rpm --import https://mirror.go-repo.io/centos/RPM-GPG-KEY-GO-REPO
        curl -s https://mirror.go-repo.io/centos/go-repo.repo | sudo tee /etc/yum.repos.d/go-repo.repo
        sudo yum -y install golang



-   Create user dinshaw and relevant directories as described below


        sudo useradd dinshaw
        sudo mkdir -p /opt/eth/bin
        sudo mkdir -p /opt/eth/.ethereum
        sudo mkdir -p /opt/eth/data
        sudo chown -R dinshaw:dinshaw /opt/eth
 
        sudo su dinshaw
        cd /home/dinshaw
        ln -s /opt/eth/.ethereum

        Note: Remember to change "dinshaw" to your preferred username
 

-   Download the geth client


        wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.9.9-01744997.tar.gz
        tar xf geth-linux-amd64-1.9.9-01744997.tar.gz

        Note: if getting error tar: geth-linux-amd64-1.8.23-c9427004: implausibly old time stamp 1970-01-01 00:00:00  
              then use the below command to extract :

        tar xf geth-linux-amd64-1.8.23-c9427004.tar.gz --warning=no-timestamp



-  Install the binary


       cd geth-linux-amd64-1.8.23-c9427004
       install -m 0755 -o dinshaw -g dinshaw -t /opt/eth/bin geth

       Note: exit as dinshaw and run the below commmand 

       sudo echo -e "export PATH=\$PATH:/opt/eth/bin"
       
       // insert output of sudo echo -e "export PATH=\$PATH:/opt/eth/bin" into etc/profile.d/dinshaw.sh
          change the ownership to dinshaw using the below command

       chown -R dinshaw:dinshaw dinshaw.sh
        
       // now re-login as dinshaw
       sudo su dinshaw


-  Configure the node.

       Copy the ATTACHED config file - "geth" and service file - "geth.service" into configuration directories, enable service to start at start up.

       cp geth /opt/eth
       
       Note: change the default rpc port by adding --rpcport <portnumber>  in geth command before starting geth
       
       cp geth.service /usr/lib/systemd/system
       
       sudo systemctl enable geth


- Modify geth config as you require:

      "rpcaddr" determines which IP addresses can make RPC calls to this node ("0.0.0.0" for any address).

      Add "--networkid=4" to GETH_OPTS if using the Rinkeby blockchain (see Blockchain Testnets).  

      Also, if you are going to use the Rinkeby blockchain, you will need to initialise the genesis block manually.

      wget https://www.rinkeby.io/rinkeby.json
      sudo geth --datadir=/opt/eth/data init rinkeby.json
      sudo chown -R dinshaw:dinshaw /opt/eth


-  Start the node, optionally, test through the console.

       service geth start
       geth attach --datadir=/opt/eth/data
       > eth.syncing
