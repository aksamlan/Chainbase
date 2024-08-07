# Chainbase Testnete katılıyoruz ve AVS çalıştırıyoruz.

![image](https://github.com/user-attachments/assets/854b65a7-6705-48cc-a561-e29ad2ce93e8)

## Website : [Buradan](https://linktr.ee/chainbasehq)
## Discord : [Buradan](https://discord.gg/8rR8Nb6X)
## Twitter : [Buradan](https://x.com/ChainbaseHQ)

## Sistem Gereksinimleri şu şekilde.

![image](https://github.com/user-attachments/assets/0bccdf1e-ee88-45d7-b946-9d4858af193e)

# 1. AVS Operatör kurulumu başlatalım.
```shell
# Güncelleme ve Paketleri Yükleyelim
sudo apt update & sudo apt upgrade -y
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y

# Docker Kuralım
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
docker version

# Docker-Compose Kuralım
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)

curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
docker-compose --version

# Docker Kullanıcı İzni Verelim
sudo groupadd docker
sudo usermod -aG docker $USER

# Go Kuralım
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.22.4.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> $HOME/.bash_profile
source .bash_profile
go version
```
2.  EigenLayer CLI Yükleyelim
```shell
curl -sSfL https://raw.githubusercontent.com/layr-labs/eigenlayer-cli/master/scripts/install.sh | sh -s

export PATH=$PATH:~/bin

eigenlayer --version
```
3. Chainbase AVS repo Klonlayalım
```shell
git clone https://github.com/chainbase-labs/chainbase-avs-setup

cd chainbase-avs-setup/holesky
```
4. Eigen için cüzdan oluşturalım
a. Yeni key oluşturmak için bu adımlar takip edin.
```shell
# Bu adımda kodu girdikten sonra sizden şifre oluşturmanızı isteyecek Harfler, Rakamlar ve Sembollerden oluşturulması önerilir. Sonra çıktıdaki Private keyi yedekleyin.
eigenlayer operator keys create --key-type ecdsa wallet
```
# BU ADIMA GEÇMEDEN ÖNCE Cüzdanı oluşturup yedeklerinizi kaydettikten sonra Holesky Test ağında adresinize en az 1 ETH göndermeniz gerekecek. Google üzerinden Holesky Faucet bulabilirsiniz.

5. Ayarlar ve Operatör kaydını yapalım.
```shell
eigenlayer operator config create
```
## Adımları aşağıdakiler gibi ilerleyerek gidelim
- `# operator address:` Holesk ETH adresinizi girin.
- `# earnings address:` Enter'e basın.
- `# ETH rpc url:` https://ethereum-holesky-rpc.publicnode.com
- `# network:` holesky
- `# signer type:` local_keystore
- `# ecdsa key path::` /root/.eigenlayer/operator_keys/wallet.ecdsa.key.json

6. Metadata'mızı açıp içerisini kopyalayalım ve 8. adım olan yere ekleyip Sunucumuzda metada'yı Ctrl X ile kapatalım. 
```shell
nano metadata.json
```
# Metada oluşturmak için resimleri takip edin.
## GitHub üzerinden hesabınıza girin ve Sağ üstteki resminize tıklayın ve aşağıdaki adımları takip edin.
 ![image](https://github.com/user-attachments/assets/a23c1231-0377-4d20-ac3b-49f66410aacd)
 ![image](https://github.com/user-attachments/assets/56767711-cc7d-4b21-b754-2a8895e8eefe)
 ![image](https://github.com/user-attachments/assets/350ead97-41fa-4a5e-8903-2adb5d95fbd5)
 ![image](https://github.com/user-attachments/assets/87461e12-ebad-4d75-bc83-12cc67faa4d2)
 ![image](https://github.com/user-attachments/assets/02e64dea-4018-4c83-97ca-a113114ff7f0)
 ![image](https://github.com/user-attachments/assets/c980efd5-3c29-47c1-aecf-d87a5c0f0c1a)

7. Operator.yaml dosyasını açalım ve gerekli düzenlemeleri yapalım.
```shell
nano operator.yaml
```
#### Metadata URL yazan kısma yukarıda yapmış olduğunuz Metadata klasörünüzün RAW linkini tırnakların içerisine ekleyelim ve CTRL X - Y ENTER diyerek kaydedip çıkalım.

8. Eigenlayer Holesky Nodu'muzu çalıştıralım. Register Succes çıktısı almamız gerekecek.
```shell
eigenlayer operator register operator.yaml
```
#### - Durumunu kontrol etmek için Aşağıdaki koddan bakabilirsiniz.
```shell
eigenlayer operator status operator.yaml
```
#### Metadata herhangi bir değişiklik yaptığınızda güncellemek için
```shell
eigenlayer operator update operator.yaml
```
9. Chainbase AVS Yapılandıralım
```shell
# Eskisi varsa silelim
rm -rf .env

# Yenisini oluşturup içine girelim
nano .env
```
Aşağıdaki kodları içerisine ekleyip kendinize göre düzenleyin ve CTRL X - Y ENTER yapıp kaydedin ve çıkın.
```shell
# Chainbase AVS Image
MAIN_SERVICE_IMAGE=repository.chainbase.com/network/chainbase-node:testnet-v0.1.7
FLINK_TASKMANAGER_IMAGE=flink:latest
FLINK_JOBMANAGER_IMAGE=flink:latest
PROMETHEUS_IMAGE=prom/prometheus:latest

MAIN_SERVICE_NAME=chainbase-node
FLINK_TASKMANAGER_NAME=flink-taskmanager
FLINK_JOBMANAGER_NAME=flink-jobmanager
PROMETHEUS_NAME=prometheus

# FLINK CONFIG
FLINK_CONNECT_ADDRESS=flink-jobmanager
FLINK_JOBMANAGER_PORT=8081
NODE_PROMETHEUS_PORT=9091
PROMETHEUS_CONFIG_PATH=./prometheus.yml

# Chainbase AVS mounted locations
NODE_APP_PORT=8080
NODE_ECDSA_KEY_FILE=/app/operator_keys/ecdsa_key.json
NODE_LOG_DIR=/app/logs

# Node logs configs
NODE_LOG_LEVEL=debug
NODE_LOG_FORMAT=text

# Metrics specific configs
NODE_ENABLE_METRICS=true
NODE_METRICS_PORT=9092

# holesky smart contracts
AVS_CONTRACT_ADDRESS=0x5E78eFF26480A75E06cCdABe88Eb522D4D8e1C9d
AVS_DIR_CONTRACT_ADDRESS=0x055733000064333CaDDbC92763c58BF0192fFeBf

###############################################################################
####### TODO: Operators please update below values for your node ##############
###############################################################################
# TODO: Operators need to point this to a working chain rpc
NODE_CHAIN_RPC=https://rpc.ankr.com/eth_holesky
NODE_CHAIN_ID=17000

# TODO: Operators need to update this to their own paths
USER_HOME=$HOME
EIGENLAYER_HOME=${USER_HOME}/.eigenlayer
CHAINBASE_AVS_HOME=${EIGENLAYER_HOME}/chainbase/holesky

NODE_LOG_PATH_HOST=${CHAINBASE_AVS_HOME}/logs

# TODO: Operators need to update this to their own keys
NODE_ECDSA_KEY_FILE_HOST=${EIGENLAYER_HOME}/operator_keys/wallet.ecdsa.key.json

# TODO: Operators need to add password to decrypt the above keys
# If you have some special characters in password, make sure to use single quotes
NODE_ECDSA_KEY_PASSWORD=Yukarıda+Oluşturduğunuz+Şifreyi+Giriniz
```
Docker-Compose klasörünü kendimize göre düzenleyelim.
```shell
# Eskiyi silelim
rm -rf docker-compose.yml

# Yenisini ekleyip açalım
nano docker-compose.yml
```
Aşağıdaki kodları içerisine ekleyip CTRL X - Y ENTER yapıp kaydedin ve çıkın.NOT : Portlar ile ilgili sorun yaşarsanız başlarındaki portları değiştirebilirsiniz.
```shell
services:
  prometheus:
    image: ${PROMETHEUS_IMAGE}
    container_name: ${PROMETHEUS_NAME}
    env_file:
      - .env
    volumes:
      - "${PROMETHEUS_CONFIG_PATH}:/etc/prometheus/prometheus.yml"
    command: 
      - "--enable-feature=expand-external-labels"
      - "--config.file=/etc/prometheus/prometheus.yml"
    ports:
      - "${NODE_PROMETHEUS_PORT}:9090"
    networks:
      - chainbase
    restart: unless-stopped

  flink-jobmanager:
    image: ${FLINK_JOBMANAGER_IMAGE}
    container_name: ${FLINK_JOBMANAGER_NAME}
    env_file:
      - .env
    command: jobmanager
    networks:
      - chainbase
    restart: unless-stopped

  flink-taskmanager:
    image: ${FLINK_JOBMANAGER_IMAGE}
    container_name: ${FLINK_TASKMANAGER_NAME}
    env_file:
      - .env
    depends_on:
      - flink-jobmanager
    command: taskmanager
    networks:
      - chainbase
    restart: unless-stopped

  chainbase-node:
    image: ${MAIN_SERVICE_IMAGE}
    container_name: ${MAIN_SERVICE_NAME}
    command: ["run"]
    env_file:
      - .env
    ports:
      - "${NODE_APP_PORT}:${NODE_APP_PORT}"
      - "${NODE_METRICS_PORT}:${NODE_METRICS_PORT}"
    volumes:
      - "${NODE_ECDSA_KEY_FILE_HOST:-./opr.ecdsa.key.json}:${NODE_ECDSA_KEY_FILE}"
      - "${NODE_LOG_PATH_HOST}:${NODE_LOG_DIR}:rw"
    depends_on:
      - prometheus
      - flink-jobmanager
      - flink-taskmanager
    networks:
      - chainbase
    restart: unless-stopped

networks:
  chainbase:
    driver: bridge
```
Docker için klasör oluşturalım
```shell
source .env && mkdir -pv ${EIGENLAYER_HOME} ${CHAINBASE_AVS_HOME} ${NODE_LOG_PATH_HOST}
```
İzinleri verelim
```shell
chmod +x ./chainbase-avs.sh
```
prometheus.yml klasörünü açın ve içerisindeki operator olan yere kendi adresinizi tırnakların içerisine ekleyin ve CTRL X - Y ENTER yapıp kaydedin çıkın
```shell
nano prometheus.yml
```
10. Chainbase AVS çalıştıralım ve Registering olarak çıktımızı alıp devam edelim.
```shell
./chainbase-avs.sh register
```
- AVS çalıştıralım
```shell
./chainbase-avs.sh run
```
11. AVS durumunu kontrol etmek için
```shell
docker compose logs chainbase-node -f
```
AVS Linkinizi almanız için 
```shell
export PATH=$PATH:~/bin

eigenlayer operator status operator.yaml
```
Operator sağlıgını kontrol etmek için
```shell
# Eğer Portunuz farklı ise değiştirin
curl -i localhost:8080/eigen/node/health
```
# Bu [FORMU](https://docs.google.com/forms/d/e/1FAIpQLSd-ikaoIrOMd-L_pTvr8e-BIOTxQjkRh8ENGClyl7m1IFbqPQ/viewform?usp=send_form) Doldurun.
# EN SON HOLESKY OPERATOR LİNKİNİZİ DİSCORD ÜZERİNDE OPERATOR-SUPPORT KANALINA GÖNDERİN.


