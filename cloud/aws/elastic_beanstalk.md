# eb

## usage

```console
# projectの初期化
# 環境変数にCredentialを追加しておく
$ eb init

# ssh接続
$ eb ssh
```

### 環境の設定を取得

```console
# eb config save <env> --cfg <config-name>
$ eb config save my-app-dev --cfg my-app-dev-v1.0.0 
```

### 環境作成

```console
# saved-configは .elasticbeanstalk/saved_configs以下から検索される
# ない場合はS3から探してくるらしい。
# 内容を確認せずに適用するのは怖いので、事前fetch推奨
# 事前に、eb config list/saveでlocalに取得しておく必要がある
$ eb create --cfg <saved-config>
```

## options

全環境で有効なoptions  
https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/command-options-general.html

Platform specific options  
https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/command-options-specific.html

### precedence

cliで直接指定 -> 指定されたsaved configuration -> `.ebextensiosn` -> default value

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/command-options.html

## log

* `/var/log/eb-activity.log`

## install

```console
$ brew update
$ brew install awsebcli
$ eb --version
```
