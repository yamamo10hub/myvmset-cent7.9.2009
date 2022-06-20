# centos myvmset
version 7.9.2009  
  
## 概要
vmwareでコードインフラを構成するためにベースとなるtemplate用OSimageが必要だった  
作成時の情報を記載する  
  
## 形態
手動でVM作成、OSinstall、IP付与  
sshでコマンド投入  
shutdownしvcenterからtemplate化  
  
## 設定内容
[machine.md](machine.md) に記載  
  
## 今後
・os/ 以下にansible-playbookでコード化する  
  