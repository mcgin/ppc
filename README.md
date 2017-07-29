# ppc

# https://alexshepherd.me/articles/production-bitcoind-service-on-systemd


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
wget https://github.com/peercoin/peercoin/archive/v0.5.4ppc.tar.gz
tar xzf v0.5.4ppc.tar.gz
cd peercoin-0.5.4ppc/src
export USE_UPNP=-
make -f makefile.unix
strip ppcoind
mv ppcoind /usr/local/bin/ppcoind

if ! grep -q ecryptfs /etc/modules; then echo "ecryptfs">>/etc/modules; fi
modprobe ecryptfs



## Encrypted user
#sudo adduser --encrypt-home --system --group --home /var/lib/peercoind peercoin
sudo adduser --system --group --home /var/lib/peercoind peercoin
sudo mkdir /etc/peercoin
sudo touch /etc/peercoin/peercoin.conf
sudo chmod 600 /etc/peercoin/peercoin.conf

sudo chown -R peercoin:peercoin /etc/peercoin
echo "rpcuser=bitcoinrpc">>/etc/peercoin/peercoin.conf
echo "rpcpassword=R&8B=_dq7}&K7x|vj*wVjp-D4wLjzwKW">>/etc/peercoin/peercoin.conf



#TODO Encrypt peercoin directory
# https://en.bitcoin.it/wiki/Securing_your_wallet#Hot_wallets:_minimizing_risks

#TODO https://bitcoin.stackexchange.com/questions/41426/how-do-i-configure-bitcoin-core-to-connect-always-to-a-particular-node

#TODO setup peercoin as a service

ppcoind -daemon -conf=/etc/peercoin/peercoin.conf -datadir=/var/lib/peercoind


