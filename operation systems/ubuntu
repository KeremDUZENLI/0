########## DUALBOOT BOOTLOADER THEME ##########
- GRUB CUSTOMIZER
sudo apt update && sudo apt upgrade
sudo apt install gdebi
wget https://launchpadlibrarian.net/570407966/grub-customizer_5.1.0-3build1_amd64.deb
sudo gdebi grub-customizer*

grub-customizer --version


- THEME CHANGE
git clone https://github.com/ChrisTitusTech/Top-5-Bootloader-Themes
cd Top-5-Bootloader-Themes
sudo ./install.sh


- RESET
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt-get update
sudo apt-get install -y boot-repair && boot-repair


- TRIPLE BOOT Win10/Win11/Ubuntu
bcdedit /set {bootmgr} displaybootmenu yes





########## DUALBOOT TIME PROBLEM ##########
timedatectl set-local-rtc 1 --adjust-system-clock
timedatectl





########## PYTHON ##########
sudo apt install python-is-python3





####################   GOLANG   ####################
sudo apt update
sudo apt install -y golang
go version

1 - Remove
sudo apt-get remove golang
sudo apt-get autoremove
sudo apt-get purge golang*
sudo rm -rf /usr/local/go
sudo rm -rf $HOME/go

2 - Extract
tar -C /usr/local -xzf go1.11.4.linux-amd64.tar.gz

3 - Create .go directory
mkdir ~/.go

4 - Setup environment variables
GOROOT=/usr/local/go
GOPATH=~/.go
PATH=$PATH:$GOROOT/bin:$GOPATH/bin

5 - Update
sudo update-alternatives --install "/usr/bin/go" "go" "/usr/local/go/bin/go" 0
sudo update-alternatives --set go /usr/local/go/bin/go

6 - Version
go version





########## MOCKERY ##########
- GLOBAL
wget https://github.com/vektra/mockery/releases/download/v2.26.1/mockery_2.26.1_Linux_x86_64.tar.gz
tar -xzf mockery_2.26.1_Linux_x86_64.tar.gz
sudo cp mockery /usr/local/bin/
sudo chmod +x /usr/local/bin/mockery
mockery --version

sudo rm /usr/local/bin/mockery


- LOCAL
go install github.com/vektra/mockery/v2@latest
go env GOPATH
ls $GOPATH/bin
export PATH=$PATH:$GOPATH/bin
source ~/.bashrc
mockery --version


- RUN
mockery --all





########## DOCKER ##########
1 - Update the apt package index and install packages to allow apt to use a repository over HTTPS
sudo apt-get update
sudo apt-get install \
   ca-certificates \
   curl \
   gnupg \
   lsb-release
    
2 - Add Docker official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
 
3 - Use the following command to set up the repository
echo \
 "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
 $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
 
4 - 
sudo apt-get update

5 -
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

6 - 
sudo docker run hello-world





########## MONGODB ##########
1 - Perform System Update
sudo apt update
sudo apt install wget curl gnupg2 software-properties-common apt-transport-https ca-certificates lsb-release

2 - Import the public key
curl -fsSL https://www.mongodb.org/static/pgp/server-6.0.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/mongodb-6.gpg

3 - Configure MongoDB Repo
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list

4 - Install MongoDB
wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1f-1ubuntu2.16_amd64.deb
sudo dpkg -i ./libssl1.1_1.1.1f-1ubuntu2.16_amd64.deb
sudo apt update
sudo apt install mongodb-org

5 - start and enable MongoDB
sudo systemctl enable --now mongod
mongod --version