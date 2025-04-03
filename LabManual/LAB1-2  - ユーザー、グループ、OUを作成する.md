---
lab：
    title： 'Active Directory Domain Service の高度な管理'
---

# ラボ1-2  - ユーザー、グループ、OUを作成する（目安：15分）

## 演習で使用するマシン

以下のマシンを使用します。

| マシン名           | 用途/目的                            |
| ------------------ | ------------------------------------ |
| DomainController_1 | OU、グループ、ユーザーを作成します。 |



## 実施内容

+ タスク 1：OUを作成する
+ タスク 2：グループを作成する
+ タスク３：ユーザーを作成する
+ タスク４：PowerShellコマンドでOU、グループ、ユーザーを作成する



## 手順

#### タスク 1：OUを作成する

1. 「スタート」メニューから「サーバーマネージャー」を開きます。

1. サーバーマネージャーの「ツール」メニューから「Active Directory ユーザーとコンピューター」を選択します。

1. 「Active Directory ユーザーとコンピューター」ウィンドウが開きます。

1. 「Active Directory ユーザーとコンピューター」ウィンドウの左側のペインで、`ctct.local` ドメインを右クリックします。

1. メニューから「新規作成」 > 「組織単位」を選択します。

1. 「新しいオブジェクト - 組織単位」ウィンドウが表示されます。

1. 「名前」に「Tokyo」と入力し、「OK」をクリックします。

1. 左側のペインで「Tokyo」のOUが作成されたことを確認します。

     

#### タスク 2：グループを作成する

1. 「スタート」メニューから「サーバーマネージャー」を開きます。
1. サーバーマネージャーの「ツール」メニューから「Active Directory ユーザーとコンピューター」を選択します。
1. 「Active Directory ユーザーとコンピューター」ウィンドウが開きます。
1. 「Active Directory ユーザーとコンピューター」ウィンドウの左側のペインで、`ctct.local` ドメイン直下の「Tokyo」OUを右クリックします。
1. トメニューから「新規作成」 > 「グループ」を選択します。
1. 「新しいオブジェクト -グループ」ウィンドウが表示されます。
1. 「グループ名」に「Sales」と入力し、「OK」をクリックします。
1. 左側のペインで「Sales」のグループが作成されたことを確認します。



#### タスク３：ユーザーを作成する

1. 「Active Directory ユーザーとコンピューター」ウィンドウを開きます。

1. 「Active Directory ユーザーとコンピューター」ウィンドウの左側のペインで、`ctct.local` ドメイン直下の「Tokyo」OUを右クリックします。

1. コンテキストメニューから「新規作成」 > 「ユーザー」を選択します。

1. 「新しいオブジェクト - ユーザー」ウィンドウが表示されます。

1. 必要な情報を入力します。「名」と「ユーザーログオン名」に「duser01」と入力します。

   > ※フルネームとユーザーログオン名(Windows2000より以前)は自動で入力されます。

1. 「次へ」をクリックし、パスワード欄に「Pa55w.rd1234」と入力します。

1. ユーザーは次回ログオン時にパスワードの変更が必要のチェックを外し、「次へ」をクリックします。

1. フルネーム：duser01、ユーザーログオン名が`duser01@ctct.local`になっていることを確認し、「完了」をクリックします。

1. 一覧にduser01が表示されます。ダブルクリックします。

1. 「duser01のプロパティ」ウィンドウにある「所属するグループ」タブを選択します。

1. 「所属するグループ」タブにある「追加」をクリックします。

1. 「グループの選択」ウィンドウにある「選択するオブジェクト名を・・・」に「Sales」と入力し、「名前の確認」→「OK」をクリックします。

1. 「所属するグループ」の一覧に「Sales」が表示されたことを確認します。「OK」をクリックします。

   

#### タスク 4：PowerShellを使用してユーザーを作成する

1. スタートメニューからPowerShellを起動し、以下のコマンドを入力します。

1. PowerShellがActive Directoryコマンドを理解できるように、ADモジュールをインポートします。

   ```powershell
   Import-Module ActiveDirectory
   ```

   

1. 以下のコマンドをコピーして、PowerShellウィンドウに貼り付けて実行します。ここでは複数のOUを作成します。

   ```powershell
   # Osaka OU を作成
   New-ADOrganizationalUnit -Name "Osaka" -Path "DC=ctct,DC=local"
   
   # Nagoya OU を作成
   New-ADOrganizationalUnit -Name "Nagoya" -Path "DC=ctct,DC=local"
   ```

   

1. 以下のコマンドをコピーして、PowerShellウィンドウに貼り付けて実行します。ここではTokyoOU内にグループを作成します。

   ```powershell
   # Sample-Domain-Local グループの作成
   New-ADGroup -Name "Sample-Domain-Local" -GroupCategory Security -GroupScope DomainLocal -Path "OU=Tokyo,DC=ctct,DC=local" -Description "Domain Local security group for Tokyo"
   
   # Sample-Global グループの作成
   New-ADGroup -Name "Sample-Global" -GroupCategory Security -GroupScope Global -Path "OU=Tokyo,DC=ctct,DC=local" -Description "Global security group for Tokyo"
   
   # Sample-Universal グループの作成
   New-ADGroup -Name "Sample-Universal" -GroupCategory Security -GroupScope Universal -Path "OU=Tokyo,DC=ctct,DC=local" -Description "Universal security group for Tokyo"
   
   # Sample-Distribution グループの作成
   New-ADGroup -Name "Sample-Distribution" -GroupCategory Distribution -GroupScope Universal -Path "OU=Tokyo,DC=ctct,DC=local" -Description "Distribution group for Tokyo"
   ```

1. 以下のコマンドをコピーして、PowerShellウィンドウに貼り付けて実行します。ここではTokyoOU内にユーザーを作成します。

   ```powershell
   # 固定された10名の日本人の名前（ローマ字）
   $users = @(
       @{ FirstName="Hiroshi"; LastName="Tanaka" },
       @{ FirstName="Yuki"; LastName="Suzuki" },
       @{ FirstName="Takeshi"; LastName="Takahashi" },
       @{ FirstName="Akiko"; LastName="Yamamoto" },
       @{ FirstName="Kenji"; LastName="Kobayashi" },
       @{ FirstName="Naoko"; LastName="Ito" },
       @{ FirstName="Kazuo"; LastName="Nakamura" },
       @{ FirstName="Sakura"; LastName="Matsumoto" },
       @{ FirstName="Ryo"; LastName="Yamada" },
       @{ FirstName="Mayumi"; LastName="Saito" }
   )
   
   # 固定パスワード
   $password = "Pa55w.rd1234"
   
   # ユーザー作成
   foreach ($user in $users) {
       $fullName = "$($user.LastName) $($user.FirstName)" # フルネーム (苗字 名前)
       $samAccountName = "$($user.FirstName)_$($user.LastName)" # ログオン名 (名前_苗字)
       
       New-ADUser -Name $fullName `
                  -SamAccountName $samAccountName `
                  -UserPrincipalName "$samAccountName@ctct.local" `
                  -Path "OU=Tokyo,DC=ctct,DC=local" `
                  -AccountPassword (ConvertTo-SecureString $password -AsPlainText -Force) `
                  -Enabled $true `
                  -Description "User in Tokyo OU" `
                  -GivenName $user.FirstName `
                  -Surname $user.LastName
   
       Write-Host "Created user: $fullName ($samAccountName)"
   }
   ```

1. 以下のコマンドを実行して、ユーザーが作成されていることを確認します。

   ```powershell
   Get-ADUser -Filter 'Name -like "*"' | Select-Object Name, SamAccountName, DistinguishedName
   ```



これでラボは完了です。お疲れ様でした。
