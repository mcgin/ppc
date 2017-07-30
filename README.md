# ppc
```
# https://alexshepherd.me/articles/production-bitcoind-service-on-systemd

export PEERCOIN_VERSION=0.5.4
export PEERCOIN_SRC_SHA=5aee5ee92422c10f9c7679b344acd378362e40ba

sudo apt-get update
sudo apt-get -y install \
    git \
    build-essential \
    libssl-dev \
    libboost-thread-dev \
    libboost-filesystem-dev \
    libboost-program-options-dev \
    libdb++-dev libminiupnpc-dev \
    libqrencode-dev \
    qt4-qmake \
    libqt4-dev \
    wget

# Compile peercoin
wget https://github.com/peercoin/peercoin/archive/v${PEERCOIN_VERSION}ppc.tar.gz
echo "${PEERCOIN_SRC_SHA}  v${PEERCOIN_VERSION}ppc.tar.gz"|shasum -c
tar xzf v${PEERCOIN_VERSION}ppc.tar.gz
cd peercoin-${PEERCOIN_VERSION}ppc/src
export USE_UPNP=-
export STATIC=all
make -f makefile.unix
strip ppcoind
mv ppcoind /usr/local/bin/ppcoind


# On seraparte machine
wget https://github.com/mcgin/ppc/releases/download/0.5.4-arm-linux-gnueabihf/ppcoind
mv ppcoind /usr/local/bin/
chmod 755 /usr/local/bin/ppcoind


if ! grep -q ecryptfs /etc/modules; then echo "ecryptfs">>/etc/modules; fi
modprobe ecryptfs



## Encrypted user
#sudo adduser --encrypt-home --system --group --home /var/lib/peercoind peercoin
sudo adduser --system --group --home /var/lib/peercoind peercoin
sudo mkdir /etc/peercoin
sudo touch /etc/peercoin/peercoin.conf
sudo chmod 600 /etc/peercoin/peercoin.conf

sudo chown -R peercoin:peercoin /etc/peercoin

##########################################
    START CONFIGURATION for public node
##########################################
## https://en.bitcoin.it/wiki/Running_Bitcoin
## https://github.com/peercoin/peercoin/wiki/Installation#sample-ppcoin-configuration
# RPC
echo "rpcuser=bitcoinrpc">>/etc/peercoin/peercoin.conf
export rpcpass=$(head /dev/urandom | tr -dc 'A-Za-z0-9!#$%&()*+,-./:;<=>?@[\]^_`{|}~' | head -c 32 ;)
echo "rpcpassword=${rpcpass}">>/etc/peercoin/peercoin.conf
echo "rpcport=9902">>/etc/peercoin/peercoin.conf
# Disable rpc on public server
echo "server=0">>/etc/peercoin/peercoin.conf
echo "port=9901">>/etc/peercoin/peercoin.conf
# Accept incoming connections
echo "listen=1">>/etc/peercoin/peercoin.conf
echo "upnp=0">>/etc/peercoin/peercoin.conf
# Add the private node's IP to ensure always connected
#addnode=69.164.218.197
##########################################
    End public config
##########################################

##########################################
    START CONFIGURATION for private node
##########################################
## https://en.bitcoin.it/wiki/Running_Bitcoin
## https://github.com/peercoin/peercoin/wiki/Installation#sample-ppcoin-configuration
## ppcoind --help

# RPC
echo "rpcuser=bitcoinrpc">>/etc/peercoin/peercoin.conf
export rpcpass=$(head /dev/urandom | tr -dc 'A-Za-z0-9!#$%&()*+,-./:;<=>?@[\]^_`{|}~' | head -c 32 ;)
echo "rpcpassword=${rpcpass}">>/etc/peercoin/peercoin.conf
echo "rpcport=9902">>/etc/peercoin/peercoin.conf
# Disable rpc on private server
echo "server=0">>/etc/peercoin/peercoin.conf
echo "port=9901">>/etc/peercoin/peercoin.conf
# Disable incoming connections
echo "listen=0">>/etc/peercoin/peercoin.conf
echo "upnp=0">>/etc/peercoin/peercoin.conf
# Add the public node's IP as the only node to connect to
#connect=69.164.218.197
##########################################
    End public config
##########################################

#TODO Encrypt peercoin directory
# https://en.bitcoin.it/wiki/Securing_your_wallet#Hot_wallets:_minimizing_risks

#TODO https://bitcoin.stackexchange.com/questions/41426/how-do-i-configure-bitcoin-core-to-connect-always-to-a-particular-node

#TODO setup peercoin as a service

ppcoind -daemon -conf=/etc/peercoin/peercoin.conf -datadir=/var/lib/peercoind

```
