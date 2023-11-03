---
lab:
    title: 'Windows Server のネットワークサービス'
---

# ラボ2-3  - DNSを構成

## 目標

このラボでは次の内容を学習します。

- タスク 1：演習環境へアクセス

- タスク 2：WSUSサーバーをインストール

- タスク 3：WSUSの初期構成

- タスク 4：グループポリシーの設定

- 

  

  ## 予想時間： 15 分

  

  ## 手順

  #### タスク 1：演習環境へアクセス

  このタスクでは、使用する演習環境へアクセスします。

  1. HostVM1にサインインします。

  1. デスクトップにあるHyper-Vマネージャーをクリックします。

  1. Hyper-Vマネージャー画面で「DNSDHCPWSUS」、「DC1-CTCT」、「Win10-1」をクリックし、アクセスします。

     

  #### タスク 2：WSUSサーバーをインストール
  
  この手順はDNSDHCPWSUSで実施してください。
  
  1. サーバーマネージャーを開く: 「スタート」メニューから「サーバーマネージャー」を開きます。
  
  2. 役割と機能の追加: サーバーマネージャーダッシュボードで「役割と機能の追加」をクリックします。
  
  3. 「役割ベースまたは機能ベースのインストール」を選択し、「次 >」をクリックします。
  
  4. インストール対象のサーバーが選択されていることを確認し、「次 >」をクリックします。
  
  5. 「Windows Server Update Services」をチェックし、「次 >」をクリックします。
  
  6. 機能の選択では、そのまま「次 >」をクリックします。
  
  7.  WSUSの概要が表示されるので、確認した後「次 >」をクリックします。
  
  8. 更新プログラムのファイルを保存するディレクトリとして「C:\WSUS」を指定し、「次 >」をクリックします。
  
  9. 「Windows Internal Database (WID)」を選択し、「次 >」をクリックします。
  
  10. Web サイトの選択: 「既定のWebサイトを使用する」を選択し、「次 >」をクリックします。
  
  11. インストールの確認: インストール設定を確認して「インストール」をクリックします。
  
  12. インストールの完了: インストールが完了するまで待ち、その後サーバーマネージャーで確認します。
  
      
  
  #### タスク 3：WSUSの初期構成
  
  この手順はDNSDHCPWSUSで実施してください。
  
  1. サーバーマネージャーから「ツール」メニューを選択し、「Windows Server Update Services」をクリックしてWSUSコンソールを開きます。
  
  2. WSUSコンソールが開くと、「WSUS初期設定ウィザード」が自動的に起動します。「次 >」をクリックして進めます。
  
  3. 「更新プログラムのソース」画面で「Microsoft Update」を選択します。設定が完了したら「次 >」をクリックします。
  
  4. 「プロキシサーバーの設定」画面で、プロキシサーバーは使用しないのでそのまま「次 >」をクリックして進めます。
  
  5. 「接続の開始」画面で「次 >」をクリックすると、WSUSサーバーがMicrosoft Updateサーバーに接続し、必要な情報をダウンロードします。
  
  6. 「製品の選択」画面で、「Windows 10」と「Windows Server 2022」を選択します。選択が完了したら「次 >」をクリックします。
  
  7. 「分類の選択」画面で、「セキュリティ更新」を選択します。選択が完了したら「次 >」をクリックします。
  
  8. 「同期スケジュールの選択」画面で、「自動同期」を選択し、毎日0時に1回の同期を設定します。設定が完了したら「次 >」をクリックします。
  
  9. 「初期同期の実行」画面で、「今すぐ開始」を選択して、初期同期を開始します。「次 >」をクリックして進めます。
  
  10.  最後に「設定の完了」画面が表示されるので、「完了」をクリックしてウィザードを終了します。
  
  ​    
  
  #### タスク 4：グループポリシーの設定
  
  この手順はDC1-CTCTで実施してください。
  
  1. 「スタート」メニューをクリックし、「Windows PowerShell」または「PowerShell」を選択して起動します。
  
  2. 「Windows PowerShell」を右クリックし、「管理者として実行」を選択します。
  
  3. Group Policy Managementの機能を利用するためのモジュールをインポートします。
  
     ```powershell
     Import-Module GroupPolicy
     ```
  
  4. デフォルトドメインポリシーを編集して、WSUSサーバーの設定を追加します。
  
     ```powershell
     # WSUSサーバーのアドレス
     $wsusServer = "http://192.168.0.30:8530"  # ポート番号8530はWSUSのデフォルトポートです。
     
     # レジストリの設定を行うためのパス
     $regPath = "Software\Policies\Microsoft\Windows\WindowsUpdate"
     
     # デフォルトドメインポリシーにWSUSサーバーの設定を追加
     Set-GPRegistryValue -Name "Default Domain Policy" -Key $regPath -ValueName WUServer -Value $wsusServer
     Set-GPRegistryValue -Name "Default Domain Policy" -Key $regPath -ValueName WUStatusServer -Value $wsusServer
     
     # クライアントの自動更新設定
     Set-GPRegistryValue -Name "Default Domain Policy" -Key $regPath -ValueName AUOptions -Value 4  # 4は自動更新とインストールを意味します。
     Set-GPRegistryValue -Name "Default Domain Policy" -Key $regPath -ValueName ScheduledInstallDay -Value 0  # 0は毎日を意味します。
     Set-GPRegistryValue -Name "Default Domain Policy" -Key $regPath -ValueName ScheduledInstallTime -Value 3  # 3はAM 3:00を意味します。
     ```
  
  
  
  #### タスク 5：Windows Updateを確認する
  
  この手順はWin10-1で実施してください。
  
  1. 「スタート」メニューをクリックし、「Windows PowerShell」または「PowerShell」を選択して起動します。
  
  2. クライアント側のレジストリ設定が正しく、WSUSサーバーを指しているかを確認します。
  
     ここで、`WUServer`と`WUStatusServer`の値が正しいWSUSサーバーのURLを指していることを確認します。
  
     ```powershell
     Get-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate'
     ```
  
  3. クライアントのマシンで手動でWindows Updateを実行し、エラーがないか確認します。
  
     Windowsの設定画面の[更新とセキュリティ]が開きます。[更新の確認]をクリックして、更新プログラムの検索が正常に行えるか確認します。
  
     ```powershell
     Start-Process -FilePath 'ms-settings:windowsupdate'
     ```
  
     
  
     これでラボは完了です。お疲れ様でした。
