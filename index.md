# 1.概要
簡単なLINE Botの作成を通じて、、Messaging APIの基本的な使い方と、
Cloud Runでのコンテナのビルドやデプロイの方法、RestAPIの公開方法などが学べます。


### 当日の持ち物
- LINEがインストール済みのスマホ
- ChromeがインストールされたPC（Chromeのバージョンは最新にしていただくとトラブルを回避しやすいです）

### 事前準備
- GitHub アカウント作成
- Google Cloud アカウント作成
- LINEアカウント作成
- LINE公式アカウント作成

### 使用するサービス・ツール
- Google Cloud 
  - Cloud Run
  - Cloud Shell と そのエディター
- GitHub

### 使用する言語
- Node.js

### 構成図

### 注意事項
若干、Google Cloudの課金が発生する可能性があります。それ以外は課金など発生しません。
終わった後はリソース一式を削除するなど、無料範囲に収まるようにご留意ください。

# 2.実装

## LINE側の設定

### チャネルの作成スタート

https://developers.line.me/ja/services/messaging-api/ にアクセス。
「今すぐはじめよう」のボタンを押して進めていきましょう。

<img src="https://user-images.githubusercontent.com/1670181/211333630-a0162d3a-d906-4147-bbb2-c5df6b288af7.png" width="500" >

「LINEアカウントでログイン」を押してください。

<img src="https://user-images.githubusercontent.com/1670181/211335264-2b450a14-aff0-45dc-b317-2d784d3fc676.png" width="500" >

LINEのログインを求められるのでログインしてください。

<img src="https://user-images.githubusercontent.com/1670181/211335374-147a0484-aac3-4569-bdf4-120a67c48ed1.png" width="500" >

### チャネルの種類
Messaging APIになっているか確認します。

<img src="https://user-images.githubusercontent.com/1670181/211335880-f4def702-1b09-4f60-9f55-abd513ba4e44.png" width="500" >

### プロバイダ
既にプロバイダーを作っている場合 → 利用するプロバイダーを選択しましょう。

<img src="https://user-images.githubusercontent.com/1670181/211336057-6531ae92-26fd-4dd0-a989-338a83a2917f.png" width="500" >

初めて → 新規プロバイダー作成を選択しプロバイダー名を入力しましょう。

（LINEという文字は含められません。）

<img src="https://user-images.githubusercontent.com/1670181/211336106-49139b55-2884-42b2-b24e-211f108e3568.png" width="500" >


## GCP側の設定

### Cloud Shellの起動

### GitHubリポジトリのClone

LINE公式から
https://github.com/line/line-bot-sdk-nodejs/blob/master/examples/echo-bot/index.js

### Cloud Shellのエディタを起動

- クレデンシャルを書き換える
- Package.jsonを書き換える
    - "name": "handson-LINE-Bot-GCP" 


### ソースからデプロイ

ターミナルから実行

gcloud run deploy
空エンター
リージョンをきかれたら、asia-northeast1= 3を選ぶ

## 3.統合

### Webhook URLをLINE 側に登録

URLの末尾に/callbackを追加すること

検証して、200、OKになること

トラブルシュート
もし404の場合、末尾に/callbackがついているか確認

### 実機確認

LINE トークアプリでBotに話しかけて、おうむ返しされること

## 4.応用

### セキュリティ対策

クレデンシャル情報を Secret Managerに入れて呼び出す

### ビルドとデプロイを分ける

メリット：デプロイ時のオプションを指定するなど細やかな制御が可能
本番公開するサービスでの利用時は必ず分ける


#### Dockerfileを書く

#### コンテナ化し、Container Registoryへ格納

```
gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/handson-LINE-Bot-GCP-01
```

#### Cloud Runへデプロイ
※一般公開のオプションを忘れずに

```
gcloud run deploy line-bot-gcp-01  \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/handson-LINE-Bot-GCP-01  \
  --platform managed  \
  --region us-central1  \
  --allow-unauthenticated  \
  --max-instances=1
```



## 5.片づけ

### Container Registory : コンテナを消す
### Cloud Run: アプリを消す
### コード： ローカルに保存する




参考文献
https://cloud.google.com/run/docs/quickstarts/build-and-deploy/deploy-nodejs-service?hl=ja



