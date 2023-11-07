---
lab:
    title: '00 - 事前準備'
---

# ラボ0  - 演習環境へアクセス



## 目標

このラボでは次の内容を学習します。

+ タスク 1：各仮想マシンの接続先情報

  

## 予想時間： 15分



## 手順

#### タスク 1：各仮想マシンの接続先情報

この演習環境は以下で構成されています。まずはHostVM1とHostVM2にRDP接続を行います。

接続後に各HostVMのデスクトップアイコン「Hyper-Vマネージャー」から仮想マシンを起動して利用します。



**■演習環境1 HostVM1**

HostVM1にはドメインコントローラ、DHCP、DNS、WSUSを利用した演習環境です。

HostVM1の中にあるHyper-Vから起動します。

| コンピュータ名 | 接続先情報                                                   | ユーザ名        | パスワード   | 備考                                                         |
| -------------- | ------------------------------------------------------------ | --------------- | ------------ | ------------------------------------------------------------ |
| Hostvm1        | ws2nd-00-hostvm1.japaneast.cloudapp.azure.com<br />～<br />ws2nd-99-hostvm1.japaneast.cloudapp.azure.com | ctctedu\ctct    | Pa55w.rd1234 |                                                              |
| DC1-CTCT       | Hyper-V マネージャーからアクセス                             | administrator   | Pa55w.rd1234 | ctct.com ドメインコントローラー                              |
| DC2-CTCT       | Hyper-V マネージャーからアクセス                             | administrator   | Pa55w.rd1234 | ctct.com ドメインコントローラー                              |
| DC-ABC         | Hyper-V マネージャーからアクセス                             | administrator   | Pa55w.rd1234 | abc.com ドメインコントローラー                               |
| DNS_DHCP_WSUS  | Hyper-V マネージャーからアクセス                             | .\administrator | Pa55w.rd1234 | 注：必ずユーザ名の先頭に.\を付けてください。                 |
| Win10-1        | Hyper-V マネージャーからアクセス                             | ctct            | Pa55w.rd1234 | 注意：ctctはローカルユーザーです。ドメイン参加後はユーザーがことなrます。 |
| Win10-2        | Hyper-V マネージャーからアクセス                             | ctct            | Pa55w.rd1234 | 注意：ctctはローカルユーザーです。ドメイン参加後はユーザーがことなrます。 |



**■演習環境2 HostVM2**

HostVM2にはドメインコントローラ、ファイルサーバーを利用した演習環境です。

HostVM2の中にあるHyper-Vから起動します。

| コンピュータ名 | 接続先情報                                                   | ユーザ名      | パスワード   | 備考 |
| -------------- | ------------------------------------------------------------ | ------------- | ------------ | ---- |
| Hostvm2        | ws2nd-00-hostvm1.japaneast.cloudapp.azure.com<br />～<br />ws2nd-99-hostvm1.japaneast.cloudapp.azure.com | ctctedu\ctct  | Pa55w.rd1234 |      |
| FileSV1        | Hyper-V マネージャーからアクセス                             | administrator | Pa55w.rd1234 |      |
| FileSV2        | Hyper-V マネージャーからアクセス                             | administrator | Pa55w.rd1234 |      |
| FileSV3        | Hyper-V マネージャーからアクセス                             | administrator | Pa55w.rd1234 |      |



各環境のユーザー名は異なりますが、パスワードは全て「Pa55w.rd1234」となります。

アクセス出来ない場合などは、講師へご連絡ください。
