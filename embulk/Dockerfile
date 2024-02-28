FROM openjdk:8

# パッケージ管理システムのアップデート
RUN apt-get -y update && apt-get -y upgrade

# localeの設定
RUN apt-get -y install locales && \
    localedef -f UTF-8 -i ja_JP ja_JP.UTF-8
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:ja
ENV LC_ALL ja_JP.UTF-8

# timezone (Asia/Tokyo)の設定
ENV TZ JST-9

# vim以外にも使いそうなもの一応インストール
RUN apt-get install -y vim git zip unzip less wget curl

# MySQLコンテナ接続確認するときのためにMySQL-clientをインストール
RUN apt-get install -y default-mysql-client

# jrubyをインストール
RUN curl --create-dirs -o "./jruby-complete-9.3.14.0.jar" -L "https://repo1.maven.org/maven2/org/jruby/jruby-complete/9.3.14.0/jruby-complete-9.3.14.0.jar"
RUN chmod +x ./jruby-complete-9.3.14.0.jar

# Embulkをダウンロードして実行可能にする
RUN curl --create-dirs -o ~/.embulk/bin/embulk -L "https://dl.embulk.org/embulk-0.11.2.jar"
RUN chmod +x ~/.embulk/bin/embulk
RUN echo 'export PATH="$HOME/.embulk/bin:$PATH"' >> ~/.bashrc

# google cloud sdkをインストール
RUN apt-get install -y python3
RUN curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-465.0.0-linux-x86_64.tar.gz
RUN tar -xf google-cloud-cli-465.0.0-linux-x86_64.tar.gz
RUN chmod +x ./google-cloud-sdk/bin/gcloud
RUN echo 'export PATH="/../google-cloud-sdk/bin:$PATH"' >> ~/.bashrc

RUN . ~/.bashrc

# embulk.propertiesとgoogle cloudサービスアカウントのキーをコピー
COPY ./embulk.properties ./root/.embulk/embulk.properties
COPY ./service_account_iam_key.json ./home/service_account_iam_key.json

# Embulk と同じバージョンを指定
RUN java -jar ~/.embulk/bin/embulk gem install embulk -v 0.11.2
RUN java -jar ~/.embulk/bin/embulk gem install msgpack

# # MySQL取得とBigQuery投入用のプラグインを入れておく
RUN java -jar ~/.embulk/bin/embulk gem install embulk-input-mysql
RUN java -jar ~/.embulk/bin/embulk gem install embulk-output-bigquery