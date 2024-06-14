# homeassistant
home assitant on rpi setup


# set rights on ssh
chmod 600 ~/.ssh/id_rsa
# docker install
curl -sSL https://get.docker.com | sh

# RIGHTS (do relogin after)
sudo usermod -aG docker walther

# folder
mkdir ~/homeassistant

# start
docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -e TZ=Europe/Amsterdam \
  -v ~/homeassistant:/config \
  -v /run/dbus:/run/dbus:ro \
  --network=host \
  ghcr.io/home-assistant/home-assistant:stable


### zero tier setup for outside access

# add key
curl https://raw.githubusercontent.com/zerotier/ZeroTierOne/master/doc/contact%40zerotier.com.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/zerotierone-archive-keyring.gpg >/dev/null

# add correct release
RELEASE=$(lsb_release -cs)

echo "deb [signed-by=/usr/share/keyrings/zerotierone-archive-keyring.gpg] http://download.zerotier.com/debian/$RELEASE $RELEASE main" | sudo tee /etc/apt/sources.list.d/zerotier.list

sudo apt update

sudo apt install -y zerotier-one

sudo zerotier-cli join [NETWORKID]


# auto start
sudo systemctl enable zerotier-one

# auto join network
sudo touch /var/lib/zerotier-one/networks.d/[NETWORKID].conf

