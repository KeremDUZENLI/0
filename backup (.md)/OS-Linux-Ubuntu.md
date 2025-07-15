# 1 Dualboot Bootloader
## 1.1 GRUB Customizer
```bash
sudo apt update && sudo apt upgrade
sudo apt install gdebi
wget https://launchpadlibrarian.net/570407966/grub-customizer_5.1.0-3build1_amd64.deb
sudo gdebi grub-customizer*

grub-customizer --version
````

## 1.2 Theme Change
```bash
git clone https://github.com/ChrisTitusTech/Top-5-Bootloader-Themes
cd Top-5-Bootloader-Themes
sudo ./install.sh
```

## 1.3 Reset
```bash
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt-get update
sudo apt-get install -y boot-repair && boot-repair
```

## 1.4 Triple Boot Windows 10/Windows 11/Ubuntu
```powershell
bcdedit /set {bootmgr} displaybootmenu yes
```

## 1.5 Dualboot Time Problem
```bash
timedatectl set-local-rtc 1 --adjust-system-clock
timedatectl
```

---

# 2 Golang
## 2.1 Install Golang
```bash
sudo apt update
sudo apt install -y golang
go version
```

## 2.2 Remove Golang
```bash
sudo apt-get remove golang
sudo apt-get autoremove
sudo apt-get purge golang*
sudo rm -rf /usr/local/go
sudo rm -rf $HOME/go
```

## 2.3 Extract Golang Archive
```bash
tar -C /usr/local -xzf go1.11.4.linux-amd64.tar.gz
```

## 2.4 Create `.go` Directory
```bash
mkdir ~/.go
```

## 2.5 Setup Environment Variables
```bash
GOROOT=/usr/local/go
GOPATH=~/.go
PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

## 2.6 Update Alternatives
```bash
sudo update-alternatives --install "/usr/bin/go" "go" "/usr/local/go/bin/go" 0
sudo update-alternatives --set go /usr/local/go/bin/go
```

## 2.7 Verify Version
```bash
go version
```

---

# 3 Docker
## 3.1 Update APT and Install Prerequisites
```bash
sudo apt-get update
sudo apt-get install \
   ca-certificates \
   curl \
   gnupg \
   lsb-release
```

## 3.2 Add Docker Official GPG Key
```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

## 3.3 Set Up the Repository
```bash
echo \
 "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
 $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## 3.4 Update APT Index
```bash
sudo apt-get update
```

## 3.5 Install Docker Engine and Compose Plugin
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

## 3.6 Verify Docker Installation
```bash
sudo docker run hello-world
```

---

# 4. MongoDB
## 4.1 Perform System Update and Install Prerequisites
```bash
sudo apt update
sudo apt install wget curl gnupg2 software-properties-common apt-transport-https ca-certificates lsb-release
```

## 4.2 Import MongoDB Public Key
```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-6.0.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/mongodb-6.gpg
```

## 4.3 Configure MongoDB Repository
```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

## 4.4 Install MongoDB and Dependencies
```bash
wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1f-1ubuntu2.16_amd64.deb
sudo dpkg -i ./libssl1.1_1.1.1f-1ubuntu2.16_amd64.deb
sudo apt update
sudo apt install mongodb-org
```

## 4.5 Start and Enable MongoDB Service
```bash
sudo systemctl enable --now mongod
mongod --version
```