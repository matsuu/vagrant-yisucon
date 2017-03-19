# vagrant-yisucon

## Overview

Yahoo! JAPANさんの[社内ISUCON](https://github.com/yahoojapan/yisucon)とほぼ同じ環境を構築するためのVagrantfileです。

## Usage

- vagrant実行環境を用意する
- このリポジトリ内のVagrantfileを手元に用意する
  - 必要に応じてVagrantfileを編集する
- Vagrantfileがあるディレクトリで`vagrant up`を実行する
  - ベンチマーク用サーバ(bench)と参加者用サーバ(image)が起動
- プロビジョニングが完了したら動作確認
  - image http://192.168.33.10/
  - bench http://192.168.33.11/

## 動作確認

macOS + VirtualBox 5.1.14 + Vagrant 1.9.2で動作確認済です。
VMWare Desktopやlxcでも動作するかもしれませんが未確認です。

## FAQ

### virtualboxで以下のようなエラーメッセージが表示される

> The provider 'virtualbox' that was requested to back the machine
> 'default' is reporting that it isn't usable on this system. The
> reason is shown below:
> 
> Vagrant has detected that you have a version of VirtualBox installed
> that is not supported. Please install one of the supported versions
> listed below to use Vagrant:
> 
> 4.0, 4.1, 4.2, 4.3

Vagrantのバージョンが古い可能性があります。最新のVagrantを使用してください。

### vagrant upを実行するとvboxsfのエラーが表示される

> Failed to mount folders in Linux guest. This is usually because
> the "vboxsf" file system is not available. Please verify that
> the guest additions are properly installed in the guest and
> can work properly. The command attempted was:
> 
> mount -t vboxsf -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3` vagrant /vagrant
> mount -t vboxsf -o uid=`id -u vagrant`,gid=`id -g vagrant` vagrant /vagrant
> 
> The error output from the last command was:
> 
> /sbin/mount.vboxsf: mounting failed with the error: No such device

[これと同じ現象](http://qiita.com/hapicky/items/a7f9d56588f96d005fad)と思われます。気にせず`vagrant provision`を実行してください。

### vagrant upを実行するとエラーが表示される

何らかの理由によりprovisionに失敗したものと思われます。`vagrant provision`を実行してください。

### プログラムの動かし方がわからない

以下をご確認ください。

- [Yahoo! JAPAN社内ISUCON「Y!SUCON」の問題をGitHubに公開しました](https://techblog.yahoo.co.jp/event/yisucon/)
- [yahoojapan/yisucon](https://github.com/yahoojapan/yisucon)
- [レギュレーション](https://github.com/yahoojapan/yisucon/blob/master/REGULATION.md)

### ブラウザで動作確認ができない

Vagrantfileのネットワーク設定を書き換えてみて下さい。
