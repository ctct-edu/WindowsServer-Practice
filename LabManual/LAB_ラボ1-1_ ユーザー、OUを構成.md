---
lab：
    title： 'Active Directory Domain Service の高度な管理'
---

# ラボ1-1  - ADDSでOUとユーザーを作成する

## 目標

このラボでは次の内容を学習します。

+ タスク 1：演習環境へアクセス

+ タスク 2：OUを作成する

+ タスク 3：ユーザー作成ウィザードを使用してユーザーを作成する

+ タスク 4：PowerShellを使用してユーザーを作成する

  

## 予想時間： 15 分



## 手順

#### タスク 1：演習環境へアクセス

このタスクでは、使用する演習環境へアクセスします。

1. HostVM1にサインインします。
1. デスクトップにあるHyper-Vマネージャーをクリックします。
1. Hyper-Vマネージャー画面で「DC1-CTCT」をクリックし、アクセスします。



#### タスク 2：OUを作成する

1. 「スタート」メニューから「サーバーマネージャー」を開きます。

1. サーバーマネージャーの「ツール」メニューから「Active Directory ユーザーとコンピューター」を選択します。

1. 「Active Directory ユーザーとコンピューター」ウィンドウが開きます。

1. 「Active Directory ユーザーとコンピューター」ウィンドウの左側のペインで、`ctct.com` ドメインを右クリックします。

1. コンテキストメニューから「新規」 > 「組織単位」を選択します。

1. 「新規オブジェクト - 組織単位」ウィンドウが表示されます。

1. 「名前」に「Tokyo」と入力し、「OK」をクリックします。

1. 左側のペインで「Tokyo」のOUが作成されたことを確認します。

1. 同様の手順4-7を繰り返し、「Osaka」のOUも作成します。

     

#### タスク 3：ユーザー作成ウィザードを使用してユーザーを作成する

1. 「Active Directory ユーザーとコンピューター」ウィンドウを開きます。

1. 「Active Directory ユーザーとコンピューター」ウィンドウの左側のペインで、`ctct.com` ドメイン直下の「Tokyo」OUを右クリックします。

1. コンテキストメニューから「新規」 > 「ユーザー」を選択します。

1. 「新規オブジェクト - ユーザー」ウィンドウが表示されます。

1. 必要な情報を入力します。例として、「名前」に「duser01」と入力します。

1. 「次へ」をクリックし、パスワード欄に「Pa55w.rd1234」と入力します。

   

#### タスク 4：PowerShellを使用してユーザーを作成する

1. 「スタート」メニューをクリックし、「Windows PowerShell」を検索します。

1. 「Windows PowerShell」を右クリックし、「管理者として実行」を選択します。

1. 「ユーザーアカウント制御」ウィンドウが表示される場合は、「はい」をクリックして続行します。

1. 「Windows PowerShell」ウィンドウが開きます。

1. PowerShellがActive Directoryコマンドを理解できるように、ADモジュールをインポートします。

   ```powershell
   Import-Module ActiveDirectory
   ```

1. 以下のコマンドをコピーして、PowerShellウィンドウに貼り付けて実行します。ここではTokyo OUにduser02とduser03を作成します。

   ```powershell
   $password = ConvertTo-SecureString "Pa55w.rd1234" -AsPlainText -Force
   New-ADUser -Name "duser02" -GivenName "duser02" -Surname "User" -SamAccountName "duser02" -UserPrincipalName "duser02@ctct.com" -Path "OU=Tokyo,DC=ctct,DC=com" -AccountPassword $password -Enabled $true
   New-ADUser -Name "duser03" -GivenName "duser03" -Surname "User" -SamAccountName "duser03" -UserPrincipalName "duser03@ctct.com" -Path "OU=Tokyo,DC=ctct,DC=com" -AccountPassword $password -Enabled $true
   ```

1. 以下のコマンドをコピーして、PowerShellウィンドウに貼り付けて実行します。ここではOsaka OUにduser04とduser05を作成します。

   ```powershell
   New-ADUser -Name "duser04" -GivenName "duser04" -Surname "User" -SamAccountName "duser04" -UserPrincipalName "duser04@ctct.com" -Path "OU=Osaka,DC=ctct,DC=com" -AccountPassword $password -Enabled $true
   New-ADUser -Name "duser05" -GivenName "duser05" -Surname "User" -SamAccountName "duser05" -UserPrincipalName "duser05@ctct.com" -Path "OU=Osaka,DC=ctct,DC=com" -AccountPassword $password -Enabled $true
   ```

1. 以下のコマンドを実行して、duser01からduser05までのユーザーが作成されていることを確認します。

   ```powershell
   Get-ADUser -Filter 'Name -like "duser*"' | Select-Object Name, SamAccountName, DistinguishedName
   ```



これでラボは完了です。お疲れ様でした。
