# 1.概要
簡単なLINE Botの作成を通じて、、Messaging APIの基本的な使い方と、
Cloud Runでのコンテナのビルドやデプロイの方法、RestAPIの公開方法などが学べます。


### 当日の持ち物
- LINEがインストール済みのスマホ
- ChromeがインストールされたPC（Chromeのバージョンは最新にしていただくとトラブルを回避しやすいです）

### 事前準備
- [GitHub アカウント](https://github.com/)作成
- LINEアカウント作成 (普段つかっているLINEアプリのアカウント)
- [LINE公式アカウント](https://developers.line.me/console/)作成
- [Google Cloud アカウント](https://cloud.google.com/free) 作成
  - 会社アカウントでも大丈夫ですが、以下の手順で *プロジェクトを作成* する内容になってますので、適宜読み替えをお願いします。

### 使用するサービス・ツール
- GitHub
- LINE Messaging API
- Google Cloud 
  - Cloud Run
  - Cloud Shell と そのエディター

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


### 作成ボタン
- 下記２点の利用規約にチェックをして「作成」ボタンを押しましょう。
<img src="https://user-images.githubusercontent.com/1670181/212521083-d6027a6c-5b61-493c-b14e-53047e50643b.png" width="500" >

- 「OK」を押しましょう。

<img src="https://user-images.githubusercontent.com/1670181/212521094-0f3d1f82-6e23-42d3-aec4-653616c60f09.png" width="500" >

- 「同意する」を押しましょう。
<img src="https://user-images.githubusercontent.com/1670181/212521098-06e5b1d3-f0f3-4e6c-8235-c011cb972ec8.png" width="500" >

### LINE公式アカウント(LINE Bot)と友だちになる
QRコードで友だち追加

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

<img width="1129" alt="image" src="https://user-images.githubusercontent.com/1670181/212521388-74044231-ed5f-4352-92fc-ab774601a71a.png">

### プロジェクト作成 

　　　　- ヘッダの「プロジェクトの選択▼」＞新しいプロジェクト
　　　　- プロジェクト名 ： Handson-LINE-Bot-GCP

<img width="575" alt="image" src="https://user-images.githubusercontent.com/1670181/212521749-8bae7ab6-05c5-4188-ae82-14a539c4e1aa.png">

  - 右上の ベルのアイコンをクリック

<img width="76" alt="image" src="https://user-images.githubusercontent.com/1670181/212521782-ebb03eca-c198-4bc1-b78d-c144285343ec.png">
  - 「プロジェクト「Handson-LINE-Bot-GCP」を作成」の通知の「プロジェクトを選択」をクリック

<img src="https://user-images.githubusercontent.com/1670181/212521800-8533f70b-7af2-447b-97fc-f0718e646056.png" width="500" >




### Cloud Shellの起動



### GitHubリポジトリのClone

LINE公式から
https://github.com/line/line-bot-sdk-nodejs/blob/master/examples/echo-bot/index.js


```
git clone https://github.com/line/line-bot-sdk-nodejs.git
mv line-bot-sdk-nodejs handson-line-bot-gcp-01
cd handson-line-bot-gcp-01
```


### Cloud Shellのエディタを起動
- index.js
  - クレデンシャルを書き換える
- Package.json
  - nameを書き換える
      - "name": "handson-line-bot-gcp-01" 
  - どんなパッケージを使ってるか眺める

- ターミナルに戻り、パッケージインストールする
```
npm install 
```



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

## 4.Cloud Run 応用 / セキュリティ、Docker利用

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

## 5. LINE Bot 応用 / リッチメニュー


## 99.片づけ

### Container Registory : コンテナを消す
### Cloud Run: アプリを消す
### コード： ローカルに保存する




参考文献
https://github.com/line/line-bot-sdk-nodejs
https://developers.line.biz/ja/docs/messaging-api/getting-started/
https://cloud.google.com/run/docs/quickstarts/build-and-deploy/deploy-nodejs-service?hl=ja


