スライドは[こちら](https://docs.google.com/presentation/d/12f9Ebat8MBmNmDdJB3zKG965jW4bscxGEAFPODjZe5o/edit)

# App Engine道場

Google App Engine™ (GAE) は、
Google™  の提供するサービスの1つであり、ウェブアプリケーションを PHP・Python・Java・Go・Node.js・Ruby・C# を使用して開発し、Google のインフラストラクチャ上で実行し、バージョン管理することができるGoogle Cloud Platform のサービスの一つです。

WEB アプリケーションを簡単に公開することができる PaaS （Platform as a Service）モデルのサービスです。
GAEの進化に伴い、徐々に対応言語が増え、GAEならではの制約も取り除かれ続けています。

GAEには `Standard Environment`と`Flexible Environment`の2つが存在します。
Standard Environmentでは、高負荷で大量のデータを処理する場合でも確実に動作するアプリケーションを簡単に構築し、デプロイできます。
アプリケーションは、ハードウェア、OS、サーバーの物理的な場所に関係なく、セキュリティで保護された信頼性の高い独自の環境で実行されます。
Flexible Environmentでは、Google Compute Engine を基盤としており、負荷分散を行いながらアプリのスケールアップやスケールダウンを自動的に行います。

このラボでは、Standard Environmentについて扱います。

## Google App Engine の魅力

+ インフラレイヤを気にする必要がなく、GCPが自動でスケールする
+ 実行するインスタンスの起動がとても早いのでスケール時に困ることはほとんどない
+ Cloud SDK™ をインストールするだけで最低限の開発環境を構築できる

||Compute Engine|Kubernetes Engine|GAE Standard|GAE Flexible|Cloud Run|Cloud Functions|
|--|--|--|--|--|--|--|
|言語サポート|任意|任意|Python, Java, PHP, Go, Node.js, Ruby|Python, Java, PHP, Go, Node.js, Ruby, .NET, カスタムランタイム|任意|Python, Go, Java, Node.js, .NET, Ruby, PHP|
|使用モデル|IaaS|IaaS, PaaS|PaaS|PaaS|マイクロサービス|マイクロサービス|
|スケーリング|サーバ自動スケール|クラスタ|サーバーレス|サーバ自動スケール|サーバーレス|サーバーレス|
|主な用途|一般的なワークロード|コンテナワークロード|スケーラブルなウェブアプリケーション|スケーラブルなウェブアプリケーション|軽量なイベント操作|スケーラブルなウェブアプリケーション|

## 柔軟な構成とデプロイ

GAE のアプリケーションには、[Versions, Services](https://cloud.google.com/appengine/docs/standard/go/an-overview-of-app-engine)という単位で構成やデプロイをコントロールする機能が用意されています。
Serviceは、単一のアプリケーションの中で独立したモジュールを提供します。各モジュールは独立したデプロイ対象となり、Service毎にプログラミング言語を指定できます。
Versionは、Service内で復数のバージョンを保持できる機能です。デプロイ時にターゲットのバージョンを任意の名前で指定して利用します。

ユーザはServiceに対してリクエストを送信します。Service中に複数のVersionが存在する場合、開発者はそれぞれのVersionにリクエスト全体の何%を割り振るかを定めることができます。
この[トラフィック分割機能](https://cloud.google.com/appengine/docs/standard/python/splitting-traffic?hl=ja)のおかげで、カナリアデプロイやBlueGreenデプロイのような様々なデプロイ戦略を採用することができます。

## 開発環境

GAEの開発やデプロイを行うCloud SDKには、開発端末上で起動できる動作確認用サーバが付属しています。
Cloud SDKの実行のためにPython導入が必要となりますが、それだけで開発環境の準備が終わります。
Cloud SDKには各サービスのエミュレーションツールも付属します。
アプリケーションのデプロイはWebブラウザから実施することも可能です。しかし、全ての機能を利用する場合はCloud SDKでのコマンドライン操作が必要になります。
そのため、Cloud SDKの利用を前提としておいた方がGAEの利便性をフル活用できます。

## GAEの初期設定

コンソール左上の「≡」メニューから「App Engine」を選択します。
その後、「開始」をクリックしてください。

![image](https://user-images.githubusercontent.com/4979283/129475222-ba2f6386-5dda-4312-aedb-50fd54c1c7de.png)

Cloud SDKが自分の端末にインストールされていなければ、「Cloud SDKをダウンロード」からインストールをしてください。

![image](https://user-images.githubusercontent.com/4979283/129476975-c602ca0f-8102-465a-aaca-5b42b877bd60.png)

その後、次のコマンドでPython用の開発環境を構築します。

```bash
gcloud components install app-engine-python
gcloud components install app-engine-python-extras
gcloud components update
```

## サンプルの実行

公開されているサンプルアプリケーションを実行します。

[https://github.com/GoogleCloudPlatform/python-docs-samples](https://github.com/GoogleCloudPlatform/python-docs-samples) をcloneし、該当ディレクトリに移動します。

```bash
git clone https://github.com/GoogleCloudPlatform/python-docs-samples
cd python-docs-samples/appengine/standard_python3/hello_world/
pip3 install -t lib -r requirements.txt
python3 main.py app.yaml
```

すると、例えば次のようなログが流れます。

```
INFO     2021-08-15 12:57:12,475 dispatcher.py:267] Starting module "default" running at: http://localhost:8080
INFO     2021-08-15 12:57:12,477 admin_server.py:150] Starting admin server at: http://localhost:8000
INFO     2021-08-15 12:57:13,494 instance.py:561] Cannot decide GOOGLE_CLOUD_PROJECT, using "test" as a fake value
[2021-08-15 21:57:13 +0900] [69404] [INFO] Starting gunicorn 20.1.0
[2021-08-15 21:57:13 +0900] [69404] [INFO] Listening at: http://0.0.0.0:21623 (69404)
[2021-08-15 21:57:13 +0900] [69404] [INFO] Using worker: sync
[2021-08-15 21:57:13 +0900] [69416] [INFO] Booting worker with pid: 69416
INFO     2021-08-15 12:57:14,553 instance.py:294] Instance PID: 69404
INFO     2021-08-15 12:57:14,575 module.py:428] [default] Detected file changes:
```

この場合、ブラウザで [http://0.0.0.0:21623](http://0.0.0.0:21623) にアクセスすれば、 `Hello World`と書かれたページが表示されます。
続いてこのアプリケーションをApp Engineへデプロイします。

## GAEへのデプロイ

次のコマンドでGAEへのデプロイができます。

```bash
gcloud app deploy --project=<YOUR_PROJECT_ID> --version=1
```

デプロイされたアプリケーションのServiceやVersionは次のコマンドで確認できます。

```bash
gcloud app services list
SERVICE  NUM_VERSIONS
default  1

gcloud app versions list
SERVICE  VERSION.ID  TRAFFIC_SPLIT  LAST_DEPLOYED              SERVING_STATUS
default  1           1.00           2021-08-15T22:04:40+09:00  SERVING
```

### 新しいバージョンのデプロイ

サンプルアプリケーションを新しいバージョンとしてデプロイする場合、バージョン名を任意に指定してデプロイすることが可能です。
`main.py`の

```python
return 'Hello World!'
```

の部分を任意の文字列に変更し、新しいバージョンのアプリケーションとしてデプロイしてみましょう。
GAEへアプリケーションをデプロイする際に、バージョン名を指定します。

```bash
gcloud app deploy --project=<YOUR_PROJECT_ID> --version=1-1
```

このコマンドを実行すると、アプリケーションの全てのトラフィックが即座に新しいバージョンに切り替わります。
実際の運用では、この挙動は不便なのでアプリケーションデプロイ時に `--no-promote`オプションを加えることで、既存のバージョンを維持したまま新しいバージョンを用意することが多いでしょう。

```bash
gcloud app deploy --project=<YOUR_PROJECT_ID> --version=1-1 --no-promote
```

改めてバージョンの一覧を確認してみましょう。

```bash
gcloud app versions list
SERVICE  VERSION.ID  TRAFFIC_SPLIT  LAST_DEPLOYED              SERVING_STATUS
default  1           1.00           2021-08-15T22:04:40+09:00  SERVING
default  1-1         0.00           2021-08-15T22:11:23+09:00  SERVING
```

バージョン `1`のアプリケーションに、全てのトラフィックがルーティングされているのがわかります。
この状態でバージョン`1-1`のアプリケーションをテストしたい時は、次のようなリンクでアクセスできます。

```
https://[バージョン番号]-dot-[PROJECT_ID].appspot.com
```

### トラフィックの割当

新しいバージョンのテストが終わったことを想定して、トラフィックを新しいバージョンに切り替えます。
あるサービスの複数のバージョンにトラフィックを分割するときには、次のようなコマンドを実行します。

```bash
gcloud app services set-traffic <SERVICE> --splits <VERSION>=1
```

今回は、`default`サービスの`1-1`バージョンに全てのトラフィックをルーティングしたいので次のようになります。

```bash
gcloud app services set-traffic default --splits 1-1=1
```

こうすると、`gcloud app browse`で表示されるページが、新しいバージョンのものになるはずです。
ちなみに、複数のバージョンに均等にトラフィックをルーティングする際には

```bash
gcloud app services set-traffic default --splits 1=5,1-1=5
```

のように、複数のバージョンに比率を指定します。
この上のコマンドの例では `5:5`と設定しましたが、比率なのでこれは`2:2`でも`1:1`でも構いません。
