---
lab:
    title: 'ファイルサーバーの高度な管理'
---

# ラボ3-3  - DFS名前空間を構成（目安：20分）

## 演習で使用するマシン

以下のマシンを使用します。

| マシン名           | 用途/目的            |
| ------------------ | -------------------- |
| DomainController_1 | ドメイン環境         |
| FileServer_1       | ファイルサーバー構成 |
| FileServer_2       | ファイルサーバー構成 |



## 目標

このラボでは次の内容を学習します。

- タスク １：FileServer_2をセットアップする
- タスク 2：DFS名前空間を構成する
- タスク 3：DFSにフォルダを追加する



## 手順

#### タスク １：FileServer_2をセットアップする

この手順はFileServer_2で実施してください。

1. スタートメニューからPowerShellを起動し、以下のコマンドを入力します。

   ```powershell
   # Install DFS Namespaces and DFS Replication features
   Import-Module ServerManager
   
   Write-Host "Installing DFS Namespaces and DFS Replication..." -ForegroundColor Green
   Install-WindowsFeature -Name FS-DFS-Namespace, FS-DFS-Replication -IncludeManagementTools
   
   Write-Host "DFS Namespaces and DFS Replication installation complete!" -ForegroundColor Green
   ```

   

2. さらに以下のコマンドを入力します。

   ```powershell
   # 部署名リスト
   $departments = @("HR", "IT", "Finance", "Sales", "Marketing")
   
   # 基本のフォルダパス
   $basePath = "C:\SharedFolders"
   
   # ベースフォルダーが存在しない場合は作成
   if (!(Test-Path -Path $basePath)) {
       Write-Host "Creating base folder at $basePath..." -ForegroundColor Green
       New-Item -ItemType Directory -Path $basePath
   }
   
   # 各部署のフォルダー作成と共有設定
   foreach ($dept in $departments) {
       $deptPath = Join-Path -Path $basePath -ChildPath $dept
       $shareName = "$dept"  # 共有名を部署名と同じに設定
   
       # フォルダー作成
       if (!(Test-Path -Path $deptPath)) {
           Write-Host "Creating folder for department: $dept" -ForegroundColor Green
           New-Item -ItemType Directory -Path $deptPath
   
           # サンプルデータを作成
           $sampleFile = Join-Path -Path $deptPath -ChildPath "SampleData_$dept.txt"
           "This is a sample file for the $dept department." | Out-File -FilePath $sampleFile
       } else {
           Write-Host "Folder for department $dept already exists. Skipping creation..." -ForegroundColor Yellow
       }
   
       # 共有設定 (SMB共有)
       if (!(Get-SmbShare | Where-Object Name -eq $shareName)) {
           Write-Host "Sharing folder for department: $dept" -ForegroundColor Green
           New-SmbShare -Name $shareName -Path $deptPath -FullAccess "Everyone"
   
           # NTFS権限設定 (Everyoneにフルコントロールを設定)
           $acl = Get-Acl $deptPath
           $accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule("Everyone", "FullControl", "ContainerInherit,ObjectInherit", "None", "Allow")
           $acl.AddAccessRule($accessRule)
           Set-Acl -Path $deptPath -AclObject $acl
       } else {
           Write-Host "Folder $dept is already shared. Skipping share creation..." -ForegroundColor Yellow
       }
   
       Write-Host "Setup complete for department: $dept" -ForegroundColor Green
   }
   
   Write-Host "All department folders have been created and shared!" -ForegroundColor Green
   ```

   

3. `\\FILESERVER2\`のUNCパスにアクセスすると、複数の共有フォルダーが作成されます。

   

   

#### タスク 2：DFS名前空間を構成する

この手順はFileServer_1で実施してください。

1. サーバーマネージャのツールから 「DFSの管理」 を選択して起動します。
2. DFSの管理画面の左側ツリーにある「名前空間」を右クリックし、「新しい名前空間」をクリックします。
3. 「参照」ボタンをクリックします。
4. 「選択するオブジェクト名を入力してください」の欄に「FileServer1」と入力し、「OK」をクリックします。
5. 認証画面が表示されます。ドメイン管理者のユーザーとパスワードを入力し、「OK」をクリックします。
6. 「名前空間サーバー」の画面で、「次 >」をクリックします。
7. 名前空間の設定で名前空間を「Public」とし、「次 >」をクリックします。
8. 名前空間の種類では、「ドメインベースの名前空間」を選択し、「次 >」をクリックします。
9. 確認で設定を確認し、「作成」をクリックして名前空間を作成し、作成が成功した後に「閉じる」をクリックします。
10. DFSの管理画面で「名前空間」をクリックします。
11. 作成した「`\\ctct.local\Public`」名前空間を右クリックし、「名前空間サーバーを追加」をクリックします。
12. 「参照」をクリックし、さらに「選択するオブジェクト名欄に「FileServer2」と入力し、「OK」をクリックします。
13. 認証画面が表示されます。ドメイン管理者のユーザーとパスワードを入力し、「OK」をクリックします。
14. 「名前空間サーバーを追加」画面で「OK」をクリックします。



#### タスク 3：DFSにフォルダを追加する

この手順はFileServer_1で実施してください。

1. 作成した「`\\ctct.local\Public`」名前空間を右クリックし、「新しいフォルダー」を選択します。

2. フォルダ名に「共有フォルダー」と入力し、フォルダーターゲットの「追加」をクリックします。

3. 「フォルダー ターゲットへのパス」に「`\\FILESERVER1\共有フォルダー`」と入力し、「OK」を２回クリックします。

4. 「スタート」メニューを開き、「エクスプローラー」を選択して起動します。

5. アドレスバーに「`\\ctct.local\public\`」と入力し、「共有フォルダー」があることを確認します。

6. スタートメニューからPowerShellを起動し、以下のコマンドを入力します。

   ```powershell
   # DFS 名前空間のベースパス
   $namespacePath = "\\ctct.local\public"
   
   # DFS フォルダーとターゲットフォルダーのマッピング
   $folderMappings = @{
       "Finance"   = "\\FILESERVER2\Finance"
       "HR"        = "\\FILESERVER2\HR"
       "IT"        = "\\FILESERVER2\IT"
       "Marketing" = "\\FILESERVER2\Marketing"
       "Sales"     = "\\FILESERVER2\Sales"
   }
   
   # DFS フォルダーの作成とターゲットのリンク設定
   foreach ($folder in $folderMappings.Keys) {
       $targetPath = $folderMappings[$folder]
       $folderPath = Join-Path -Path $namespacePath -ChildPath $folder
   
       Write-Host "Adding DFS folder: $folderPath with target: $targetPath" -ForegroundColor Green
   
       # DFS 名前空間フォルダーを作成
       New-DfsnFolder -Path $folderPath -TargetPath $targetPath
   }
   
   Write-Host "DFS namespace setup complete!" -ForegroundColor Green
   ```

7. アドレスバーに「`\\ctct.local\public\`」と入力し、複数のフォルダーがあることを確認します。



これでラボは完了です。お疲れ様でした。
