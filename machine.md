# OS install parameter
コード化も可能にみえるが後手に回したので、設定手順のメモを設置する  

## OSインストール時のパラメーター
◆VMスペック  
cpu 1core , mem 2G , hdd 16G , 管理用Networkを付与したNIC  
  
◆仮想マシンの作成  
名前の指定  
クラスタ、リソース、ストレージの指定  
linux , centos7  
リソースの指定でコンテンツライブラリのisoを指定して接続にチェック  
  
起動  
　メディアテスト抜きでInstallを選択  
  
言語  
　日本語を選択  
  
インストール概要  
　インストール先  
　　　作成したHDDを指定  
　ソフトウェアの選択  
　　　最小限(minimal)でアドオンを足す  
　　　　　デバッグ、開発、セキュリティ、システム管理  
インストールの開始  
  
## セットアップコマンド
◆OS設定  
networkIFの確認
```
nmcli d
-> ens192
```
IP設定
```
nmcli con mod ens192 ipv4.addresses "192.168.10.241/24"
nmcli con mod ens192 ipv4.gateway "192.168.10.1"
nmcli con mod ens192 ipv4.method manual
nmcli con mod ens192 ipv4.dns 192.168.10.1
nmcli con mod ens192 ipv4.ignore-auto-routes yes
nmcli con mod ens192 ipv4.ignore-auto-dns yes
nmcli con mod ens192 ipv4.may-fail no
nmcli con mod ens192 ipv6.method ignore
nmcli connection modify ens192 connection.autoconnect yes
systemctl restart network
```
managerユーザーの追加
```
useradd osmanager
passwd osmanager
```
sudoパーミッション  
visudo 
```
:101
osmanager   ALL=(ALL)    ALL
```
コードにすることを検討したが、permissionの都合で難しそうにみえる
```
cp /etc/sudoers /etc/sudoers.org
rm /etc/sudoers
cat /etc/sudoers.org | sed 101i'osmanager   ALL=(ALL)      ALL \n' > /etc/sudoers
chmod 440 /etc/sudoers
```
yumで追加
```
yum update
yum install open-vm-tools
yum install telnet
yum install net-tools
yum install bind-utils
yum install openssl-devel
```
systemdの設定変更
```
systemctl enable vmtoolsd
systemctl disable firewalld
```
OS全体の設定パラメーター変更
```
sed -i -e s/SELINUX=enforcing/SELINUX=disabled/g /etc/selinux/config
hostnamectl set-hostname centos-7.x-masterimg
```
dockerコンテナの利用等でselinux有効化が良いらしいが不明  
  
nmcliの停止
```
nmcli con mod ens192 ipv4.method auto
nmcli connection modify ens192 connection.autoconnect no
```
  
OSの停止
```
shutdown -h now  
```
  
vCenterで設定
```
設定の編集でCD/DVDをクライアントデバイスに変える
annotationの付与 -> 20220519 created
「サマリ」->「メモの編集」
VMのtemplate化  
「テンプレート」->「テンプレートに変換」
```
  
