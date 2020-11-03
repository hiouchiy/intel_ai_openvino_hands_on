# インテル® OpenVINO™ ツールキットを使ったハンズオン
OpenVINOを使ってソーシャルディスタンスを検知するアプリをさくっと作ってみます。
 
## Getting Started / スタートガイド
### Prerequisites / 必要条件
- Intel CPU（Core or Xeon）を搭載したマシン
- OS: Linux（Ubuntu 18.04がお薦め）／Windows10／
macOS
- Docker（※以下にインストール手順記載）
### Installing / インストール
#### ホストOSのポート開放（リモートアクセスする場合のみ）
このハンズオンではJupyter LabおよびOpenVINO Model Serverを使用します。特にサーバーにリモートアクセスしながら実施する場合は各環境ごとの手順に則り、ホストOSのポート「8888」、「9000」番を開放ください。
#### Dockerインストール
##### Linux（Ubuntu 18.04）
```Bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install -y docker-ce
sudo usermod -aG docker ${USER}
su - ${USER}
id -nG
```
##### Windows 10
https://docs.docker.jp/docker-for-windows/install.html
##### macOS
https://docs.docker.jp/docker-for-mac/install.html
#### Dockerイメージのダウンロード
今回はDocker版のOpenVINOを使用します（2020年11月2日現在、バージョン2021R1がダウンロードされる）。OSに直接インストールされたい方は[公式ドキュメント（英語）](https://docs.openvinotoolkit.org/latest/install_directly.html)を参照ください。
```Bash
docker pull openvino/ubuntu18_dev
```
#### Dockerコンテナの起動
コンテナはRootで起動します。また、8888番ポートをホストOSとコンテナとでバインドしておきます。
```Bash
docker run -it -u 0 --privileged -p 8888:8888 openvino/ubuntu18_dev:latest /bin/bash
```
以降はコンテナ上での作業になります。
#### 追加モジュールのインストール
```Bash
apt-get update
apt-get install -y wget unzip git sudo
apt-get install -y ubuntu-restricted-extras　
(※↑でEULAにacceptを求められるのでyesと入力)
apt-get install -y ffmpeg
pip install jupyterlab munkres
```
#### 本レポジトリをClone
```Bash
cd ~
git clone https://github.com/hiouchiy/intel_ai_openvino_hands_on.git
```
#### Jupyter Labの起動
```Bash
jupyter lab --ip=0.0.0.0 --no-browser --allow-root
```
#### WebブラウザからJupyter Labにアクセス
前のコマンド実行すると以下のようなログが出力されまして、最後にローカルホスト（127.0.0.1）のトークン付きURLが表示されるはずです。こちらをWebブラウザにペーストしてアクセスください。リモートアクセスされている場合はIPアドレスをサーバーのホストOSのIPアドレスに変更してください。
```
root@f79f54d47c1b:~# jupyter lab --allow-root --ip=0.0.0.0 --no-browser
[I 09:13:08.932 LabApp] JupyterLab extension loaded from /usr/local/lib/python3.6/dist-packages/jupyterlab
[I 09:13:08.933 LabApp] JupyterLab application directory is /usr/local/share/jupyter/lab
[I 09:13:08.935 LabApp] Serving notebooks from local directory: /root
[I 09:13:08.935 LabApp] Jupyter Notebook 6.1.4 is running at:
[I 09:13:08.935 LabApp] http://f79f54d47c1b:8888/?token=2d6863a5b833a3dcb1a57e3252e641311ea7bc8e65ad9ca3
[I 09:13:08.935 LabApp]  or http://127.0.0.1:8888/?token=2d6863a5b833a3dcb1a57e3252e641311ea7bc8e65ad9ca3
[I 09:13:08.935 LabApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 09:13:08.941 LabApp] 
    
    To access the notebook, open this file in a browser:
        file:///root/.local/share/jupyter/runtime/nbserver-33-open.html
    Or copy and paste one of these URLs:
        http://f79f54d47c1b:8888/?token=2d6863a5b833a3dcb1a57e3252e641311ea7bc8e65ad9ca3
     or http://127.0.0.1:8888/?token=2d6863a5b833a3dcb1a57e3252e641311ea7bc8e65ad9ca3
```
↑最後の「http://127.0.0.1:8888/?token=2d6863a5b833a3dcb1a57e3252e641311ea7bc8e65ad9ca3」です。
#### Notebookの起動
Jupyter Lab上で「intel_ai_openvino_hands_on」フォルダーに入り、その中の「social_distance_app.ipynb」を開き、後はノートブックの内容に従って進めてください。
## 応用編
### OpenVINO Model Serverを使ってモデルをWebサービス化
#### OpenVINO Model ServerのDockerイメージをダウンロード
ホストOS上でもう一つターミナルを開き、下記コマンドを実行
```Bash
docker pull openvino/model_server:latest
```
#### OpenVINOの事前学習済みモデルをダウンロード
ハンズオンの中で使用した人物検出用の事前学習済みモデルをダウンロードして、ホストOS上の適当なフォルダに格納しておく
- https://download.01.org/opencv/2020/openvinotoolkit/2020.1/open_model_zoo/models_bin/1/person-detection-retail-0013/FP32/person-detection-retail-0013.xml 
- https://download.01.org/opencv/2020/openvinotoolkit/2020.1/open_model_zoo/models_bin/1/person-detection-retail-0013/FP32/person-detection-retail-0013.bin
#### OpenVINO Model Serverを起動
```Bash
docker run -d -v <モデルを格納しているフォルダ>:/models/person-detection/1 -p 9000:9000 openvino/model_server:latest \
--model_path /models/person-detection --model_name person-detection --port 9000 --log_level DEBUG --shape auto
```
#### NotebookからOpenVINO Model Serverへアクセス
前のコンテナ（Jupyter Lab実行中）にて、Notebook（social_distance_app.ipynb）に戻り、「【応用編】OpenVINO Model Serverを使う」から再開ください。
## License / ライセンス
このプロジェクトは Apache 2.0の元にライセンスされています。
## Acknowledgments / 謝辞
なお、本アプリケーションは[こちら](https://github.com/AAEONPROJECT/SocialDistance)をベースにアレンジを加えたものです。作者である[Maiko007](https://github.com/Maiko007)と[techc-ego](https://github.com/techc-ego)には事前に了承を取ったうえで使用しております。改めまして、作者の二人には素晴らしいアプリケーションを提供いただいたことに感謝いたします。