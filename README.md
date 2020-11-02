# インテル® OpenVINO™ ツールキットを使ったハンズオン
OpenVINOを使ってソーシャルディスタンスを検知するアプリをさくっと作ってみます。

なお、本アプリケーションは[こちら](https://github.com/AAEONPROJECT/SocialDistance)をベースにアレンジを加えたものです。事前に作者への了承を取ったうえで使用しております。 
 
## 事前準備
### ホストOSのポート開放（リモートアクセスする場合のみ）
このハンズオンではJupyter Labを使用します。特にサーバーにリモートアクセスしながら実施する場合は各環境ごとの手順に則り、ホストOSのポート「8888」番を開放ください。
### Dockerインストール
#### Linux（Ubuntu 18.04）
```Bash
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce
sudo usermod -aG docker ${USER}
su - ${USER}
id -nG
```
#### Windows 10
https://docs.docker.jp/docker-for-windows/install.html
#### macOS
https://docs.docker.jp/docker-for-mac/install.html
### Dockerイメージのダウンロード
```Bash
docker pull openvino/ubuntu18_dev
```
### Dockerコンテナの起動
```Bash
docker run -it -u 0 --privileged -p 8888:8888 openvino/ubuntu18_dev:latest /bin/bash
```
以降はコンテナ上での作業になります。
### 追加モジュールのインストール
```Bash
apt-get update
apt-get install -y wget unzip git sudo
apt-get install -y ubuntu-restricted-extras　
(※↑でEULAにacceptを求められるのでyesと入力)
apt-get install -y ffmpeg
pip install jupyterlab munkres
```
### 本レポジトリのClone
```Bash
cd ~
git clone https://github.com/hiouchiy/intel_ai_openvino_hands_on.git
```
### Jupyter Labの起動
```Bash
jupyter lab --ip=0.0.0.0 --no-browser --allow-root
```
### Notebookの起動
Jupyter Lab上で「intel_ai_openvino_hands_on」フォルダーに入り、その中の「social_distance_app.ipynb」を開き、後はノートブックの内容に従って進めてください。