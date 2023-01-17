# 1.概要

簡単なLINE Botの作成を通じて、、Messaging APIの基本的な使い方と、
Cloud Runでのコンテナのビルドやデプロイの方法、RestAPIの公開方法などが学べます。


### 当日の持ち物

- LINEがインストール済みのスマホ
- ChromeがインストールされたPC（Chromeのバージョンは最新にしていただくとトラブルを回避しやすいです）

### 事前準備
- [GitHub アカウント](https://github.com/)作成
- LINEアカウント作成 (普段つかっているLINEアプリのアカウント)
- [LINE公式アカウント](https://developers.line.me/console/)※ ログインできるかどうかを確認ください
- [Google Cloud アカウント](https://cloud.google.com/free) 作成
  - 会社アカウントでも大丈夫ですが、以下の手順で *プロジェクトを作成* する内容になってますので、適宜読み替えをお願いします。
- (推奨) [Google Chrome](https://www.google.com/intl/ja/chrome/gsem/download/) の最新版のインストール

### 使用するサービス・ツール
- GitHub
- LINE Messaging API
- Google Cloud 
  - Cloud Run
  - Cloud Shell と そのエディター

### 使用する言語
- Node.js

### 構成図

(作成中）★

### 注意事項
若干、Google Cloudの課金が発生する可能性があります。それ以外は課金など発生しません。
終わった後はリソース一式を削除するなど、無料範囲に収まるようにご留意ください。

# 以下は清書中なのでまだ嘘がまざっています。ハンズオン当日までお待ちください 1/17 伊藤

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

### 会社・事業者の所在国・地域
<img src="https://user-images.githubusercontent.com/1670181/212520933-0121c2fc-a073-43d9-af70-cc5b7d2af3a2.png" width="500" >

### チャネルアイコン
チャネルアイコンを登録しましょう。（今回は必須ではありません）
<img src="https://user-images.githubusercontent.com/1670181/212520954-6f3c6a81-80b7-4b69-a970-68d5784792c8.png" width="500" >

### チャネル名、チャネル説明
- 下記を入力しましょう。
    - チャネル名：「HandsonBot」
    - チャネル説明：「HandsonBotです。」

<img src="https://user-images.githubusercontent.com/1670181/212521031-475baa45-a27a-40a9-a75d-1a021da22e96.png" width="500" >


### 大業種、小業種

<img src="https://user-images.githubusercontent.com/1670181/212521037-c80419e7-3bed-474c-9cb0-11d866a7c774.png" width="500" >

### メールアドレス、プライバシーポリシーURL、サービス利用規約URL

- メールアドレスを確認しましょう。
- プライバシーポリシーURLとサービス利用規約URLは入力しなくても大丈夫です。
<img src="https://user-images.githubusercontent.com/1670181/212521072-4448d30e-b1b2-4e50-93bb-ae0396770b39.png" width="500" >


### 作成実行
- 下記２点の利用規約にチェックをして「作成」ボタンを押しましょう。

<img src="https://user-images.githubusercontent.com/1670181/212521083-d6027a6c-5b61-493c-b14e-53047e50643b.png" width="500" >

- 「OK」を押しましょう。

<img src="https://user-images.githubusercontent.com/1670181/212521094-0f3d1f82-6e23-42d3-aec4-653616c60f09.png" width="500" >

- 「同意する」を押しましょう。

<img src="https://user-images.githubusercontent.com/1670181/212521098-06e5b1d3-f0f3-4e6c-8235-c011cb972ec8.png" width="500" >

### LINE公式アカウント(LINE Bot)と友だちになる

-  QRコードで友だち追加

「Messaging API設定」タブに移動し、QRコードを読み取って、友だち追加をしましょう。

<img src="https://user-images.githubusercontent.com/1670181/212521108-930b988e-cb5c-4751-991a-852b3cfc4b55.png" width="500" >

### チャネルシークレット、アクセストークンの取得
「チャネル基本設定」タブからチャネルシークレットを、「Messaging API設定」タブからアクセスキーをそれぞれ取得します。

- チャネルシークレット
<img src="https://user-images.githubusercontent.com/1670181/212521114-951ba5dc-e42d-424e-8af0-3991dee94fc6.png" width="500" >

- アクセストークン
<img src="https://user-images.githubusercontent.com/1670181/212521144-d04a70da-d16e-4a48-aac6-c3a7c3b8650b.png" width="500" >

<img src="https://user-images.githubusercontent.com/1670181/212521151-9182c848-6e0a-4698-815d-6fef64533f7d.png" width="500" >

#### Note:

- チャネルシークレット：Botサーバで受信したリクエストが不正なものでないかのチェックで使用される(LINE以外のサーバーからリクエストが送られていないかの確認)

- アクセストークン：LINEのAPI(機能)を実行する際に、認可が通り利用権限があることを証明するトークン

### 応答モードをOFFにする
LINE Official Account Manager 画面にアクセスして、Botの「応答モード」設定をOFFにします。（デフォルトの設定はONになっており、メッセージを送るたびにデフォルトのメッセージが返ってきてしまうため）

<img src="https://user-images.githubusercontent.com/1670181/212521176-36e7a3d0-2233-48ee-8cf7-e4f764e0f51a.png" width="500" >
<img src="https://user-images.githubusercontent.com/1670181/212521188-a719200b-be23-43e5-96c5-b0b5fb92ff0b.png" width="500" >


## GCP側の設定

- Google Cloud の コンソールにログイン
    - https://console.cloud.google.com/

- 日本語に変更するやり方
    - 右上のケバブ＞Preferences＞Language & region＞Languageを日本語にする＞保存

### プロジェクト作成 

- ヘッダの「プロジェクトの選択▼」＞新しいプロジェクト
- プロジェクト名 ： Handson-LINE-Bot-GCP

<img width="575" alt="image" src="https://user-images.githubusercontent.com/1670181/212521749-8bae7ab6-05c5-4188-ae82-14a539c4e1aa.png">

- 右上の ベルのアイコンをクリック ＞「プロジェクト「Handson-LINE-Bot-GCP」を作成」の通知の「プロジェクトを選択」をクリック

<img src="https://user-images.githubusercontent.com/1670181/212521800-8533f70b-7af2-447b-97fc-f0718e646056.png" width="500" >




### Cloud Shellの起動

[Cloud ShellはGCP上に用意される無料のオンライン環境](https://cloud.google.com/shell/pricing?hl=ja)で、利用が終わると停止しますが、作業内容はディスクに保存され、次回ログイン時にも引き続き使うことができます。なおディスク容量は5GBしかありませんので、それ以上の大きなデータを扱う場合は VM Instanceなど他のサービスを使う必要があります。

- ヘッダのCloud Shellアイコンをクリック

![image](https://user-images.githubusercontent.com/1670181/212522520-9617596a-f90c-4309-af5c-47b597fb525f.png)

- 該当タブの下半分に Cloud Shell ターミナルが開く
    - 「Click here to see details 〜」と「プロジェクト「Handson-LINE-Bot-GCP」を表示しています。」の通知は閉じましょう。



### サンプルコードをGitHubからClone

LINE公式アカウントが公開しているサンプルコードをCloneします。

- Cloud Shellターミナルで、以下のコマンドを入力

```
git clone https://github.com/line/line-bot-sdk-nodejs.git
cp -r ~/line-bot-sdk-nodejs/examples/echo-bot/ ~/handson-line-bot-gcp-01
cd handson-line-bot-gcp-01
```


### Cloud Shellのエディタを起動

- Cloud Shell ターミナルの「エディタを開く」をクリック
- エディタの上を引っ張り上げて面積を広げる
 ![image](https://user-images.githubusercontent.com/1670181/212523391-bed90312-2d23-439e-8f38-331156fae316.png)

- 左ペインの「handson-line-bot-gcp-01」のフォルダ名をダブルクリックし、中身を開く

 
- 「Package.json」を開いて編集

```
"name": "echo-bot",
```
↓
```
"name": "handson-line-bot-gcp-01" ,
```

- File＞「Auto Save」にチェックが入っていることを確認する


### Dockerfileを書く

handson-LINE-Bot-GCP フォルダの直下に、"Dockerfile"という名前のファイルを作り、以下を記入してください。

```
FROM node:12
WORKDIR /usr/src/app
COPY package.json package*.json ./
RUN npm install --only=production
COPY . .
CMD [ "npm", "start" ]
```


### Cloud Run ビルド、デプロイ

- Cloud Shell エディタのヘッダ部分の「ターミナルを開く」をクリック
- npmで必要なパッケージをインストールする

```
$ cd ~/handson-line-bot-gcp-01
$ npm install 
```
★これnpm installいらないんでは

1〜2分かかるのでストレッチしましょう


### 認証

- Cloud Shell ターミナルで以下を実行

```
$ gcloud auth login
（中略）
Do you want to continue (Y/n)? ←Y

Go to the following link in your browser:
　　　　https://〜 ←表示された長いURLをクリックするとブラウザが開くので、Google メールアドレスで認証。
```

- 以下のような画面が表示されるので、copyをクリックする

![verification](https://user-images.githubusercontent.com/1670181/212524210-c5bf75ea-28fb-4af5-84c6-ce01fe6e0052.png)

```
Enter authorization code: ←verification code を入力してエンター
```

### コンテナをビルドし、Container Registoryへ格納

- Cloud Shell ターミナルで以下を実行

```
$ gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/line-bot-gcp-01
```

- 以下のポップアップが出るので「承認」をクリック

![image](https://user-images.githubusercontent.com/1670181/212524053-db72d2bc-9aae-440b-ae8d-06d63cc355ea.png)


 - 失敗の場合はトラブルシュート
     - Cloud Runが有効になってない

### Cloud Runへデプロイ

デプロイする際、あわせてチャネルアクセストークンとチャネルシークレットを環境変数に登録するやり方です。
環境変数は、プロジェクト閲覧者以上の権限を持つユーザーには見えてしまいますので、正式なサービスでは利用してはいけませぬ。その場合は、Secret Manager を使用してください。
参考）https://cloud.google.com/run/docs/configuring/environment-variables?hl=ja#command-line

- Cloud Shell ターミナルで以下を実行

```
$ CHANNEL_ACCESS_TOKEN = "ここにチャネルアクセストークンを記入"
$ CHANNEL_SECRET = "ここにチャネルシークレットを記入"

$ gcloud run deploy line-bot-gcp-01 \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/line-bot-gcp-01 \
  --set-env-vars "CHANNEL_ACCESS_TOKEN=$CHANNEL_ACCESS_TOKEN"  \
  --set-env-vars "CHANNEL_SECRET=$CHANNEL_SECRET"  \
  --platform managed   \
  --region us-central1 \
  --allow-unauthenticated  \
  --max-instances=1
```

- 成功すると以下のようなOKメッセージが表示されるので、Service URLをメモ帳などにコピーしておく。

```
OK Deploying... Done.                                                             
  OK Creating Revision...                                                         
  OK Routing traffic...
  OK Setting IAM Policy...
Done.
Service [line-bot-gcp-01] revision [line-bot-gcp-01-00002-has] has been deployed and is serving 100 percent of traffic.
Service URL: https://line-bot-gcp-01-hogehogehoge-uc.a.run.app ←これをコピー
```
![image](https://user-images.githubusercontent.com/1670181/212861496-89cb7048-a6a0-42d8-b8b1-0a4c6d959f97.png)

 - 失敗の場合はトラブルシュート
     - npm
     - シークレット等の変数の代入わすれ
     - 

### URLの動作確認

- ブラウザで、上記でゲットしたService URLにアクセスしてみる.
「Cannot GET /」と表示されればOK。


![image](https://user-images.githubusercontent.com/1670181/212787661-8db9e74e-c04b-49ab-9507-daccc8c489e1.png)





## 3.統合

### Webhook URLをLINE 側に登録

LINE Developersのコンソール画面に戻って、「Messaging API設定」タブのWebhookに、上記で取得したCloud RunのService URLを設定します。
Service URLの末尾には、/callbackを追加してください。 

例）
https://line-bot-gcp-01-hogehogehoge-uc.a.run.app
↓
https://line-bot-gcp-01-hogehogehoge-uc.a.run.app/callback

![image](https://user-images.githubusercontent.com/1670181/212745319-c53ca0bc-fa12-4879-b09e-bae538a1c560.png)
![image](https://user-images.githubusercontent.com/1670181/212861546-23c2d763-eb11-4154-b97e-05a817ec0631.png)


「検証」をクリックして、200 OKになることを確認してください。

- トラブルシュート
404の場合、末尾に/callbackがついているか確認。

これでLINEのBotを動かす準備は全て整いました！


### 実機確認

LINE トークアプリでBotに話しかけて、おうむ返しされること

![image](https://user-images.githubusercontent.com/1670181/212753508-7633e79b-8313-4749-ba15-a589a2de07a0.png)



## 99.片づけ

### Container Registory : コンテナを消す
### Cloud Run: アプリを消す


参考文献
https://github.com/line/line-bot-sdk-nodejs
https://developers.line.biz/ja/docs/messaging-api/getting-started/
https://cloud.google.com/run/docs/quickstarts/build-and-deploy/deploy-nodejs-service?hl=ja


