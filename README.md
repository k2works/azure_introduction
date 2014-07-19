Azure入門
===
# 目的
コマンドラインベースでAzureの基本操作を修得する。

# 前提
| ソフトウェア     | バージョン    | 備考         |
|:---------------|:-------------|:------------|
| OS X           |10.8.5        |             |
| npm       　　　|1.4.17        |             |
| azure-cli 　　　|0.8.6        |             |

+ [アカウント作成済み](http://azure.microsoft.com/ja-jp/)

# 構成
+ [アカウント設定](#1)
+ [バーチャルマシン設定](#2)
+ [バーチャルマシンエンドポイント設定](#3)
+ [バーチャルマシンイメージ設定](#4)
+ [バーチャルマシンデータディスク設定](#5)
+ [Webサイト設定](#6)
+ [SSH対応](#7)
+ [Docker対応](#8)

# 詳細
## <a name="1">アカウント設定</a>
### アカウント情報の設定と公開情報の設定
[コマンドラインツールをインストール]()する
```bash
$ npm install azure-cli -g
```
サブスクリプションファイルをダウンロードしてインポートする。
```bash
$ azure account download
$ azure account import publishsettings.publishsettings
```
登録アカウントの確認
```bash
$ azure account list
```
登録アカウントの削除
```bash
$ azure account clear
```
### アフィニティグループの作成
以下のコマンドではうまく行かなかったのでWeb管理コンソールから追加
```bash
$ azure vm location list
info:    Executing command vm location list
+ Getting locations
data:    Name
data:    ----------------
data:    South Central US
data:    Central US
data:    East US 2
data:    East US
data:    West US
data:    North Europe
data:    West Europe
data:    East Asia
data:    Southeast Asia
data:    Japan West
info:    vm location list command OK
$ azure account affinity-group create k2works -l "Japan West"
$ azure account affinity-group list
info:    Executing command account affinity-group list
+ Getting affinity groups
data:    Name     Label    Location
data:    -------  -------  ----------
data:    k2works  k2works  Japan West
info:    account affinity-group list command OK
```
アフィニティグループの削除
```bash
$ azure account affinity-group delete k2works
```
### アカウント環境の設定
```bash
$ azure account env show
info:    Executing command account env show
Environment name: AzureCloud
data:    Name:                       AzureCloud
data:    activeDirectoryEndpointUrl: https://login.windows.net
data:    activeDirectoryResourceId:  https://management.core.windows.net/
data:    commonTenantName:           common
data:    galleryEndpointUrl:         https://gallery.azure.com/
data:    isPublicEnvironment:        true
data:    managementEndpointUrl:      https://management.core.windows.net
data:    portalUrl:                  http://go.microsoft.com/fwink/?LinkId=254433
data:    publishingProfileUrl:       http://go.microsoft.com/fwlink/?LinkId=254432
data:    resourceManagerEndpointUrl: https://management.azure.com/
data:    sqlManagementEndpointUrl:   https://management.core.windows.net:8443/
data:    sqlServerHostnameSuffix:    .database.windows.net
info:    account env show command OK
```
追加  
`account env add [options] [environment]`  
セット  
`account env set [options] [environment]`  
削除  
`account env delete [options] [environment]`  

## <a name="2">バーチャルマシン設定</a>
作成
```bash
$ azure vm image list | grep Linux
$ azure vm create k2works 0b11de9248dd4d87b18621318e037d37__RightImage-CentOS-6.5-x64-v13.5.3 k2works --location "East Asia"
```
確認
```bash
$ azure vm list
info:    Executing command vm list
+ Getting virtual machines
data:    Name     Status     Location   DNS Name              IP Address
data:    -------  ---------  ---------  --------------------  -------------
data:    k2works  ReadyRole  East Asia  k2works.cloudapp.net  100.92.34.102
info:    vm list command OK
$ azure vm show k2works
info:    Executing command vm show
+ Getting virtual machines
data:    DNSName "k2works.cloudapp.net"
data:    Location "East Asia"
data:    VMName "k2works"
data:    IPAddress "100.92.34.102"
data:    InstanceStatus "ReadyRole"
data:    InstanceSize "Small"
data:    Image "0b11de9248dd4d87b18621318e037d37__RightImage-CentOS-6.5-x64-v13.5.3"
data:    OSDisk hostCaching "ReadWrite"
data:    OSDisk name "k2works-k2works-0-201407190310110202"
data:    OSDisk mediaLink "https://portalvhdsnjqwkcpjd362d.blob.core.windows.net/vhd-store/k2works-231a68e9661365be.vhd"
data:    OSDisk sourceImageName "0b11de9248dd4d87b18621318e037d37__RightImage-CentOS-6.5-x64-v13.5.3"
data:    OSDisk operatingSystem "Linux"
info:    vm show command OK
```
起動・再起動・停止
```bash
$ azure vm start k2works
info:    Executing command vm start
+ Getting virtual machines
+ Starting VM
info:    vm start command OK
$ azure vm restart k2works
info:    Executing command vm restart
+ Getting virtual machines
+ Restarting VM
info:    vm restart command OK
$ azure vm shutdown k2works
info:    Executing command vm shutdown
+ Getting virtual machines
+ Shutting down VM
info:    vm shutdown command OK
```
削除
```bash
$ azure vm delete k2works
info:    Executing command vm delete
+ Getting virtual machines
Delete the VM k2works ? [y/n] y
+ Deleting VM
+ Deleting Cloud Service
info:    vm delete command OK
```
## <a name="3">バーチャルマシンエンドポイント設定</a>
作成
```bash
$ azure vm endpoint create k2works 8888 8888
info:    Executing command vm endpoint create
+ Getting virtual machines
+ Reading network configuration
+ Updating network configuration
info:    vm endpoint create command OK
```
確認
```bash
$ azure vm endpoint list k2works
info:    Executing command vm endpoint list
+ Getting virtual machines
data:    Name           Protocol  Public Port  Private Port  Virtual IP     EnableDirectServerReturn  Load Balanced
data:    -------------  --------  -----------  ------------  -------------  ------------------------  -------------
data:    tcp-8888-8888  tcp       8888         8888          207.46.148.60  false                     No
info:    vm endpoint list command OK
```
削除
```bash
$ azure vm endpoint delete k2works tcp-8888-8888
info:    Executing command vm endpoint delete
+ Getting virtual machines
+ Reading network configuration
+ Updating network configuration
info:    vm endpoint delete command OK
```
## <a name="4">バーチャルマシンイメージ設定</a>
一覧
```bash
$ azure vm image list
```
確認
```bash
$ azure vm image show 0b11de9248dd4d87b18621318e037d37__RightImage-CentOS-6.5-x64-v13.5.3
```
## <a name="5">バーチャルマシンデータディスク設定</a>
```bash
$ azure vm disk list
info:    Executing command vm disk list
+ Fetching disk images
data:    Name                                  OS
data:    ------------------------------------  -----
data:    k2works-k2works-0-201407190310110202  Linux
data:    k2works-k2works-0-201407190447320868  Linux
info:    vm disk list command OK
```
## <a name="6">Webサイト設定</a>
作成
```bash
$ azure site create k2works --location "Japan West"
info:    Executing command site create
+ Getting sites
+ Getting locations
info:    Creating a new web site at k2works.azurewebsites.net
\info:    Created website at k2works.azurewebsites.net
+
info:    site create command OK
```
確認
```bash
$ azure site list
info:    Executing command site list
+ Getting locations
+ Getting sites
data:    Name     Slot  Status   Location    Mode  URL
data:    -------  ----  -------  ----------  ----  -------------------------
data:    k2works        Running  Japan West  Free  k2works.azurewebsites.net
info:    site list command OK
$ azure site browse k2works
info:    Executing command site browse
info:    Launching browser to http://k2works.azurewebsites.net
info:    site browse command OK
$ azure site show k2works
info:    Executing command site show
info:    Showing details for site
+ Getting site information
+ Getting site config information
+ Getting repository settings
+ Getting diagnostic settings
+ Getting site instances information
+ Getting locations
data:
data:    Web Site Name:  k2works
data:    Site Mode:      Free
data:    Enabled:        true
data:    Availability:   Normal
data:    Last Modified:  Sat Jul 19 2014 14:15:05 GMT+0900 (JST)
data:    Location:       Japan West
data:
data:    Host Name
data:    -------------------------
data:    k2works.azurewebsites.net
data:
data:    Instance Id
data:    ----------------------------------------------------------------
data:    6a8b5a3376c6e5be7b4d2b242823b21788b539713b0c25f187146bd1341efa54
data:
data:    App Setting                   Value
data:    ----------------------------  -------
data:    WEBSITE_NODE_DEFAULT_VERSION  0.10.29
data:
data:    Default Documents
data:    -----------------
data:    Default.htm
data:    Default.html
data:    Default.asp
data:    index.htm
data:    index.html
data:    iisstart.htm
data:    default.aspx
data:    index.php
data:    hostingstart.html
data:
data:    Platform & Frameworks
data:    ---------------------
data:    .NET Framework Version:     v4.0
data:    PHP Version:                5.4
data:    Work Process:               32-bit
data:    Worker Count:               1
data:
data:    Logging and Diagnostics
data:    -----------------------
data:    HTTP Logging:
data:
data:    Source Control
data:    --------------
data:    Type:            None
data:    Git Repository:  https://k2works.scm.azurewebsites.net/k2works.git
data:    Branch:          master
info:    site show command OK
```
サイトチェックアウト
```bash
$ git clone https://k2works.scm.azurewebsites.net/k2works.git site
Cloning into 'site'...
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
$ cd site
```
サイト作成・更新
```bash
$ vi index.html
$ git add .
$ git commit -am "Setup"
$ git push origin master
Counting objects: 3, done.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 250 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '62e5d2ada5'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: 'index.html'
remote: Finished successfully.
remote: Deployment successful.
To https://k2works.scm.azurewebsites.net/k2works.git
 * [new branch]      master -> master
$ azure site browse k2works
info:    Executing command site browse
info:    Launching browser to http://k2works.azurewebsites.net
info:    site browse command OK
```
サイト停止
```bash
$ azure site stop k2works
info:    Executing command site stop
info:    Stopping site k2works
+ Updating site information
info:    Site k2works has been stopped
info:    site stop command OK
```
サイト開始
```bash
$ azure site start k2works
info:    Executing command site start
info:    Starting site k2works
+ Updating site information
info:    Site k2works has been started
info:    site start command OK
```
サイト削除
```
$ azure site delete k2works
info:    Executing command site delete
Delete site k2works? [y/n] y
+ Deleting site
info:    Site k2works has been deleted
info:    site delete command OK
```

## <a name="7">SSH対応</a>
SSH用の鍵作成
```bash
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
$ chmod 0600 myPrivateKey.key
```
VM作成
```bash
$ azure vm docker create -z small --location "Japan West" --ssh 22 --ssh-cert myCert.pem -P k2w-demo 0b11de9248dd4d87b18621318e037d37__RightImage-CentOS-6.5-x64-v13.5.3 k2works
$ ssh -i myPrivateKey.key k2works@k2w-demo.cloudapp.net
```
"Saving password to keychain failed"が出る場合は以下の作業をする
```
$ mv myPrivateKey.key myPrivateKey.key.pkcs
$ openssl rsa -in myPrivateKey.key.pkcs -out myPrivateKey.key
$ chmod 0600 myPrivateKey.key
$ ssh-keygen -f myPrivateKey.key -p
```

## <a name="8">Docker対応</a>
```
$ azure vm docker create -z small --location "Japan West" --ssh 22 --ssh-cert myCert.pem -P k2w-demo 0b11de92d4d87b18621318e037d37__RightImage-CentOS-6.5-x64-v13.5.3 k2works
```
エンドポイントにdockerは表示されているが繋がらない。
```
$ docker --tls -H tcp://k2w-demo.cloudapp.net:4243 run images
2014/07/19 17:04:42 Cannot connect to the Docker daemon. Is 'docker -d' running on this host?
```

# 参照
+ [Azure command-line tool for Mac and Linux](http://azure.microsoft.com/en-us/documentation/articles/command-line-tools/#Commands_to_manage_your_Azure_virtual_machine_endpoints)
+ [MSOpenTech/azure-sdk-tools-xplat](https://github.com/MSOpenTech/azure-sdk-tools-xplat/tree/docker)
+ [Windows azure 仮想マシンをコマンドラインツールから操作する](http://qiita.com/zakkied/items/d94ff3845ee098a2c5e2)
+ [MacでMicrosoft Azureを使ってみる](http://qiita.com/adeonhy/items/21f06b489c958f7678a5)
+ [Mavericksでssh接続すると”Saving password to keychain failed”が表示される](http://moyashiki.hateblo.jp/entry/2014/04/03/163225)
