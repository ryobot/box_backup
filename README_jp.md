# box_backup
BOX ストレージを使ったファイルのバックアップスクリプト

####必要なもの: linux, curl, jq

###使い方

####インストール:

ルート階層の全ファイルを１つのディレクトリにコピーして *.json 以外のファイルに実行権限を与えます。（ ```chmod +x``` ）

####BOX の authrization code の取得:

- BOX のアカウントで新しいアプリを作り ```CLIENT_ID, CLIENT_SECRET, REDIRECT_URI(https)``` を確認します。※ REDIRECT_URI は ```https://localhost``` 等でよい。

- テキストエディタなどで下記 URI を作成します。

```
https://account.box.com/api/oauth2/authorize?response_type=code&client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&state=authrization
```

- ブラウザで URI にアクセスし BOX アカウントにログインします。

- リダイレクト先の URI から ```code=AUTHORIZATION_CODE``` を見つけてコピーします。AUTORIZATION_CODE の有効期間は30秒間なので急いで次の作業をします（あらかじめ用意しておく）。

####アクセストークン、リフレッシュトークンの取得:

```
curl https://api.box.com/oauth2/token -d 'grant_type=authorization_code&code=AUTHORIZATION_CODE&client_id=CLIENT_ID&client_secret=CLIENT_SECRET' -X POST
```
上記の json 出力で```box_token.json```ファイルを上書きします。 ```box_client.json``` にも自分のアカウントの CLIENT_ID/CLIENT_SECRET を書き込みます。

####バックアップファイルのアップロード:

```
export BOX_BACKUP_DIR=/usr/local/box_backup # インストールしたディレクトリ
$BOX_BACKUP_DIR/box_backup -I -p /tmp/backup.tgz -n backup.tgz -f Docs
# -I [最初のアップロード時は I を指定]
# -p [バックアップするファイルのフルパス]
# -n [BOX上でのファイル名]
# -f [アップロード先BOXフォルダ名]
```

####バックアップの更新:

BOX 上のファイルは更新されると元のファイルがバージョニングされていきます。これを複数世代バックアップに利用します。

```
export BOX_BACKUP_DIR=/usr/local/box_backup # Installed directory
$BOX_BACKUP_DIR/box_backup -U -p /tmp/messages.tgz -n messages.tgz -f Docs -v 2
# -U [更新時は U を指定]
# -p [バックアップするファイルのフルパス]
# -n [BOX上でのファイル名]
# -f [アップロード先BOXフォルダ名]
# -v [とっておくバージョン数]
```

-v で指定した世代数よりも古いものは同時に破棄されます。破棄されたファイルも1か月間は BOX の機能を使って復元可能です。
上記更新を定期的に行う場合、[更新間隔] x [とっておくバージョン数] + 1か月　が保存期間になります。