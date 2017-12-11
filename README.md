# シェフ環境の構築

chef-server、chef-client、workstation の環境構築手順を下記にまとめます。

## chef-server

*インストールモジュール*

```
Chef Server
Chef Manage
Reporting
```

*インストール手順*

*** サーバーの構築

```
# chef-server-ctl reconfigure
# opscode-manage-ctl reconfigure
# chef-server-ctl reconfigure
```

*** 状態確認とテスト

```
# chef-server-ctl status 
# chef-server-ctl test 
```

*** アカウント、組織登録

```
# chef-server-ctl user-create {chef アカウント名} {名前} {名字} {メールアドレス} '{パスワード}' --filename {chef アカウント名}.pem
# chef-server-ctl org-create {組織名} '{長い組織名}' --association_user {chef アカウント名} --filename {組織名}-validator.pem
```

*** 各秘密鍵を workstation の ~/.chef/ にコピーする

*** アカウントの秘密鍵を chef-client /etc/chef/client.pem に登録する

*** クライアント登録 このときの json および秘密鍵を保存する

*** 秘密鍵をノード側の /etc/chef/validator.pem に保存する

```
# export EDITOR=vi
# knife client create {chef client 名} -c knife.rb
```

## chef-client

*インストールモジュール*

```
Chef Client
```

*** server 側からノード登録

```
$ knife bootstrap {client の IP アドレス} -x {client のアカウント} -P {client のパスワード} [--sudo]
```

*** client 側からノード登録

```
$ knife configure client -s https://chef-server
```

*** /etc/chef の作業
```
$ vi client.rb
log_level        :info
log_location     STDOUT
chef_server_url  'https://{chef サーバー名}'
validation_client_name '{validator 名}'
node_name        '{chef ノード名}'

$ chef-client -c client.rb
```

*** chef-client - レシピの実行（ランリストを登録済み）

```
$ chef-client
```

参考 URL）
<https://dev.classmethod.jp/server-side/add-node-run-recipe/>

## workstation

*インストールモジュール*

```
Chef Development Kit
```

このモジュールは Chef Server とは別マシンにインストールしてもよい。

*** 初期化

```
$ cd ~/.chef
$ knife configure --initial
```

*** 証明書のコピー

```
$ cp /var/opt/opscode/nginx/ca/chef-server.crt /root/.chef/trusted_certs/
```

*** 接続確認

```
$ knife ssl check
```

*** ~/.chef ディレクトリ下で作業する

*** ここに {user}.pem と {validator}.pem をコピーする

*** Cookbook の作成

```
$ chef generate cookbook {Cookbook 名}
```

参考 URL）
	http://tech.aainc.co.jp/archives/10123>


*** Cookbook のアップロード

```
$ knife cookbook upload {Cookbook 名} -o .
```

*** ランリストの登録

ブラウザーの「Nodes」からノードを選択し、「Edit Run List」から登録
