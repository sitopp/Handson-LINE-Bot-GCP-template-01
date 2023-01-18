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

![image](https://user-images.githubusercontent.com/1670181/213183173-488e2147-e115-4f68-92cc-df9d0eca499b.png)


### 注意事項
若干、Google Cloudの課金が発生する可能性があります。それ以外は課金など発生しません。
終わった後はリソース一式を削除するなど、無料範囲に収まるようにご留意ください。


# 2.実装 

## LINE側の設定


- https://developers.line.me/ja/services/messaging-api/ にアクセス。

- 「今すぐはじめよう」のボタンを押す

<img src="https://user-images.githubusercontent.com/1670181/211333630-a0162d3a-d906-4147-bbb2-c5df6b288af7.png"  >

- 「LINEアカウントでログイン」を押す

<img src="https://user-images.githubusercontent.com/1670181/211335264-2b450a14-aff0-45dc-b317-2d784d3fc676.png" width="50%" >

- LINEのログインを求められるのでログイン

<img src="https://user-images.githubusercontent.com/1670181/211335374-147a0484-aac3-4569-bdf4-120a67c48ed1.png" width="50%" >

### 新規チャンネル作成

#### チャネルの種類

- Messaging APIになっているか確認

<img src="https://user-images.githubusercontent.com/1670181/211335880-f4def702-1b09-4f60-9f55-abd513ba4e44.png" width="50%" >

#### プロバイダ

- 既にプロバイダーを作っている場合 → 利用するプロバイダーを選択

<img src="https://user-images.githubusercontent.com/1670181/211336057-6531ae92-26fd-4dd0-a989-338a83a2917f.png" width="50%"  >

- 「初めて」 → 新規プロバイダー作成を選択しプロバイダー名を入力
    - ※プロバイダー名に「LINE」という文字は含められません。

<img src="https://user-images.githubusercontent.com/1670181/211336106-49139b55-2884-42b2-b24e-211f108e3568.png" width="50%"  >

#### 会社・事業者の所在国・地域
<img src="https://user-images.githubusercontent.com/1670181/212520933-0121c2fc-a073-43d9-af70-cc5b7d2af3a2.png" width="50%"  >

#### チャネルアイコン

- チャネルアイコンを登録しましょう。（今回は必須ではありません）

<img src="https://user-images.githubusercontent.com/1670181/212520954-6f3c6a81-80b7-4b69-a970-68d5784792c8.png" width="50%"  >

#### チャネル名、チャネル説明

- 以下を入力
    - チャネル名：「HandsonBot」
    - チャネル説明：「HandsonBotです。」

<img src="https://user-images.githubusercontent.com/1670181/212521031-475baa45-a27a-40a9-a75d-1a021da22e96.png" width="50%"  >


#### 大業種、小業種

<img src="https://user-images.githubusercontent.com/1670181/212521037-c80419e7-3bed-474c-9cb0-11d866a7c774.png" width="50%"  >

#### メールアドレス、プライバシーポリシーURL、サービス利用規約URL

- メールアドレスを確認
- プライバシーポリシーURLとサービス利用規約URLは任意入力

<img src="https://user-images.githubusercontent.com/1670181/212521072-4448d30e-b1b2-4e50-93bb-ae0396770b39.png" width="50%"  >


#### 作成実行

- 下記２点の利用規約にチェックをして「作成」ボタンを押す

<img src="https://user-images.githubusercontent.com/1670181/212521083-d6027a6c-5b61-493c-b14e-53047e50643b.png" width="50%"  >

- 「OK」を押す

<img src="https://user-images.githubusercontent.com/1670181/212521094-0f3d1f82-6e23-42d3-aec4-653616c60f09.png" width="50%"  >

- 「同意する」を押す

<img src="https://user-images.githubusercontent.com/1670181/212521098-06e5b1d3-f0f3-4e6c-8235-c011cb972ec8.png" width="50%"  >

#### LINE公式アカウント(LINE Bot)と友だちになる

- 「Messaging API設定」タブに移動し、QRコードを読み取って、友だち追加する

<img src="https://user-images.githubusercontent.com/1670181/212521108-930b988e-cb5c-4751-991a-852b3cfc4b55.png" width="50%"  >

#### チャネルシークレット、アクセストークンの取得

- 「チャネル基本設定」タブからチャネルシークレットを、「Messaging API設定」タブからアクセスキーをそれぞれ取得する

    - チャネルシークレット
<img src="https://user-images.githubusercontent.com/1670181/212521114-951ba5dc-e42d-424e-8af0-3991dee94fc6.png" width="50%"  >

    - アクセストークン
<img src="https://user-images.githubusercontent.com/1670181/212521144-d04a70da-d16e-4a48-aac6-c3a7c3b8650b.png" width="50%"  >

<img src="https://user-images.githubusercontent.com/1670181/212521151-9182c848-6e0a-4698-815d-6fef64533f7d.png" width="50%"  >

##### Note:

- チャネルシークレット：Botサーバで受信したリクエストが不正なものでないかのチェックで使用される(LINE以外のサーバーからリクエストが送られていないかの確認)

- アクセストークン：LINEのAPI(機能)を実行する際に、認可が通り利用権限があることを証明するトークン

#### 応答モードをOFFにする

- LINE Official Account Manager 画面にアクセスして、Botの「応答モード」設定をOFFにする。
    - デフォルトの設定はONになっており、メッセージを送るたびにデフォルトのメッセージが返ってきてしまうため

<img src="https://user-images.githubusercontent.com/1670181/212521176-36e7a3d0-2233-48ee-8cf7-e4f764e0f51a.png" width="50%"  >


<img src="https://user-images.githubusercontent.com/1670181/213186106-486bc77b-4dcd-400c-97aa-456f8c49a2f7.png"  >


## GCP側の設定

- Google Cloud の コンソールにログイン
    - https://console.cloud.google.com/

- 日本語に変更するやり方
    - 右上のケバブ > Preferences > Language & region > Languageを日本語にする > 保存

### プロジェクト作成 

- ヘッダの「プロジェクトの選択▼」＞新しいプロジェクト
- プロジェクト名 ： Handson-LINE-Bot-GCP

<img width="575" alt="image" src="https://user-images.githubusercontent.com/1670181/212521749-8bae7ab6-05c5-4188-ae82-14a539c4e1aa.png">

- 右上の ベルのアイコンをクリック > プロジェクト「Handson-LINE-Bot-GCP」を作成 〜 プロジェクトを選択 をクリックして、Handson-LINE-Bot-GCPのダッシュボードを開く

<!-- <img src="https://user-images.githubusercontent.com/1670181/212521800-8533f70b-7af2-447b-97fc-f0718e646056.png"  >
 -->
 
### Cloud Runの有効化

- 画面左上のケバブをクリック > 「その他のプロダクト」 > [API とサービス] > [ライブラリ] を選択
- 検索バーに「Cloud Run」と入力し、検索結果のリストで Cloud Run API を選択
- プロジェクト「Handson-LINE-Bot-GCP」の課金の有効化、住所とクレジットカードの入力
- CLoud Run APIの[有効にする] をクリック。ブラウザの「戻る」を押すと、以下のように「APIが有効です」となっている

![image](https://user-images.githubusercontent.com/1670181/213209274-3dc7a983-facd-4948-bd6d-8f9c805ce172.png)


<!-- ### Container Registoryの有効化

- 画面左上のケバブをクリック > 「その他のプロダクト」 >  [Container Registory] を選択
- 特に有効化は必要ない。
 -->

### Cloud Shellの起動

```
Cloud ShellはGCP上に用意される無料のオンライン環境で、
利用が終わると停止しますが、作業内容はディスクに保存され、次回ログイン時にも引き続き使うことができます。
なおディスク容量は5GBしかありませんので、それ以上の大きなデータを扱う場合は 
VM Instanceなど他のサービスを使う必要があります。
参考： https://cloud.google.com/shell/pricing?hl=ja
```

- ヘッダのCloud Shellアイコンをクリック

<img src="https://user-images.githubusercontent.com/1670181/212522520-9617596a-f90c-4309-af5c-47b597fb525f.png"  >

 

### LINE公式サンプルコードをGitHubからClone

- Cloud Shellターミナルで以下のコマンドを入力

```
$ git clone https://github.com/line/line-bot-sdk-nodejs.git
$ cp -r ~/line-bot-sdk-nodejs/examples/echo-bot/ ~/handson-line-bot-gcp-01
$ cd handson-line-bot-gcp-01
```


### Cloud Shellのエディタでファイル編集

- Cloud Shell ターミナルの「エディタを開く」をクリック
- エディタの上を引っ張り上げると、面積を広げることができる

<!-- <img src="https://user-images.githubusercontent.com/1670181/212523391-bed90312-2d23-439e-8f38-331156fae316.png"  >
 -->
- 左ペインの 「handson-line-bot-gcp-01」 >  「Package.json」 を開いて編集
 
```
"name": "echo-bot",
```
↓
```
"name": "handson-line-bot-gcp-01" ,
```

- File＞「Auto Save」にチェックが入っていることを確認する。これが入っていればちゃんと自動保存される

![autosave](https://user-images.githubusercontent.com/1670181/213190518-6d18127e-9eab-4c43-8840-6e48b441b20d.png)

### Dockerfileを書く

- 「handson-line-bot-gcp-01」フォルダ右クリック > メニュー一番上の New FIle をクリックし、"Dockerfile"(拡張子無し)という名前のファイルを作る
- 以下を記入

```
FROM node:12
WORKDIR /usr/src/app
COPY package.json package*.json ./
RUN npm install --only=production
COPY . .
CMD [ "npm", "start" ]
```

- 1〜2秒で自動保存される

### 認証

- Cloud Shell ターミナルで以下を実行

```
$ gcloud auth login
```

- 「Do you want to continue (Y/n)? 」 には 「Y」 と入力
- 「Go to the following link in your browser:」 の　https://〜 で始まる長いURLをクリックするとブラウザが開くので、Google メールアドレスで認証→許可。
- 以下のような画面が表示されるので、copyをクリックして、verification codeをコピーする

<img src="https://user-images.githubusercontent.com/1670181/212524210-c5bf75ea-28fb-4af5-84c6-ce01fe6e0052.png"  >

- Cloud Shellのターミナルに戻り、 「Enter authorization code: 」 のプロンプトに、上でゲットしたverification code をペーストして、エンター
- 「You are now logged in as〜」と表示されればOK



<!-- ### Cloud Run ビルド、デプロイ

- 以下のnpmコマンドで、必要なパッケージをインストール

```
$ cd ~/handson-line-bot-gcp-01
$ npm install 
```
★これnpm installいらないんでは

 -->


### コンテナをビルドし、Container Registoryへ格納

- Cloud Shell ターミナルで以下を実行

```
$ cd ~/handson-line-bot-gcp-01
$ gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/handson-line-bot-gcp-01
```

- 「Cloud Shellの承認」というポップアップが出るので「承認」をクリック
- 「API [cloudbuild.googleapis.com] not enabled on project [99670530964]. Would you like to enable and retry (this will take a few minutes)? (y/N)?」 と表示されたら、「y」を入力
- 1〜2分かかりますので、ストレッチでもしましょう
- STATUS: SUCCESSで終了すればOK

<img src="https://user-images.githubusercontent.com/1670181/213172667-eaa72bd3-d9a2-4299-9bc2-58d9a666e0a7.png"  >

<!-- - トラブルシュート
   - 「GCP ERROR: (gcloud.builds.submit) HTTPError 404: The requested project was not found.」が出たら、
   
```
$ gcloud config list project
[core]
project = handson-line-bot-gcp ←プロジェクトIDが返却されるので、コピー
$ gcloud config set project handson-line-bot-gcp ←プロジェクトIDを指定して実行
Updated property [core/project]. ← 正常終了
``` 
 -->
 
 
### Cloud Runへデプロイ

```
デプロイする際、あわせてチャネルアクセストークンとチャネルシークレットを環境変数に登録するやり方です。
ただし環境変数はプロジェクト閲覧者以上の権限を持つユーザーには見えてしまいますので、
正式なサービスでは避けた方がよく、代わりにSecret Managerを使用してください。
参考）https://cloud.google.com/run/docs/configuring/environment-variables?hl=ja#command-line
```

- Cloud Shell ターミナルで以下を実行

```
$ CHANNEL_ACCESS_TOKEN=ここにチャネルアクセストークンを記入（ダブルクウォートなどで囲む必要なし)
$ CHANNEL_SECRET=ここにチャネルシークレットを記入（ダブルクウォートなどで囲む必要なし)

$ gcloud run deploy handson-line-bot-gcp-01 \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/handson-line-bot-gcp-01 \
  --set-env-vars "CHANNEL_ACCESS_TOKEN=$CHANNEL_ACCESS_TOKEN"  \
  --set-env-vars "CHANNEL_SECRET=$CHANNEL_SECRET"  \
  --platform managed   \
  --region asia-northeast1 \
  --allow-unauthenticated  \
  --max-instances=1
```

- トラブルシュート
    - 「CHANNEL_ACCESS_TOKEN = 」のようにイコールの前や後に空白が入ると失敗する

- 成功すると以下のようなOKメッセージが表示されるので、Service URLをメモ帳などにコピーしておく

```
OK Deploying... Done.                                                             
  OK Creating Revision...                                                         
  OK Routing traffic...
  OK Setting IAM Policy...
Done.
Service [line-bot-gcp-01] revision [line-bot-gcp-01-00002-has] has been deployed and is serving 100 percent of traffic.
Service URL: https://line-bot-gcp-01-hogehogehoge-uc.a.run.app ←これをコピー
```
<img src="https://user-images.githubusercontent.com/1670181/212861496-89cb7048-a6a0-42d8-b8b1-0a4c6d959f97.png"  >

 - トラブルシュート
     - シークレットの変数の代入わすれ
     

### URLの動作確認

- ブラウザで、上記でゲットしたService URLにアクセス
- 以下のように「Cannot GET /」と表示されればOK！

<img src="https://user-images.githubusercontent.com/1670181/212787661-8db9e74e-c04b-49ab-9507-daccc8c489e1.png"  >




## 3.統合

### Webhook URLをLINE 側に登録

- LINE Developersのコンソール画面に戻って、「Messaging API設定」タブのWebhookに、上記で取得したCloud RunのService URLを記入し、末尾に必ず「/callback」を追加する
- Webhookの利用 > オンにする
例）
https://line-bot-gcp-01-hogehogehoge-uc.a.run.app
↓
https://line-bot-gcp-01-hogehogehoge-uc.a.run.app/callback

<img src="https://user-images.githubusercontent.com/1670181/212745319-c53ca0bc-fa12-4879-b09e-bae538a1c560.png" width="50%" >

<!-- <img src="https://user-images.githubusercontent.com/1670181/212861546-23c2d763-eb11-4154-b97e-05a817ec0631.png"  >
 -->
 
 <img width="790" alt="webhook" src="https://user-images.githubusercontent.com/1670181/213231301-6268a469-53cc-40ab-83a4-528246722c48.png">


- 「検証」をクリックして、200 OKになることを確認

- トラブルシュート
    - 404の場合、末尾に/callbackがついていないのかも


# これでLINEのBotを動かす準備は全て整いました！


### 実機確認

- LINE トークアプリでBotに話しかけて、おうむ返しされることを確認

<img src="https://user-images.githubusercontent.com/1670181/212753508-7633e79b-8313-4749-ba15-a589a2de07a0.png" width="50%" >



## 99.片づけ

### Container Registory : コンテナを消す

- 左上のナビゲーションバー > Container Registory > イメージ >  handson-line-bot-gcp-01 > コンテナにチェックを入れて 「削除」 

### Cloud Run: アプリを消す

- 左上のナビゲーションバー > Cloud Run > handson-line-bot-gcp-01 にチェックを入れて「削除」


参考文献
https://github.com/line/line-bot-sdk-nodejs
https://developers.line.biz/ja/docs/messaging-api/getting-started/
https://cloud.google.com/run/docs/quickstarts/build-and-deploy/deploy-nodejs-service?hl=ja


