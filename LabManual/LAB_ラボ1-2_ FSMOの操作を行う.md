---
lab:
    title: 'Active Directory Domain Service の高度な管理'
---

# ラボ1-2  - FSMOの操作を行う

## 目標

このラボでは次の内容を学習します。

- タスク 1：演習環境へアクセス

- タスク 2：FSMOの役割を確認する（Active Directory ユーザーとコンピューター）

- （オプション）タスク 2'：FSMOの役割「ドメイン命名マスター命名」を確認する（Active Directory ドメインと信頼関係）

- （オプション）タスク 2''：FSMOの役割「スキーママスター」を確認する（Active Directory スキーマ）

- （オプション）タスク 2'''：FSMOの役割をPowerShellで確認する

- タスク 3：FSMOの役割をPowerShellで移行する

  ※オプション演習はスキップしてOKです。

  

  ## 予想時間： 15 分

  

  ## 手順

  #### タスク 1：演習環境へアクセス

  このタスクでは、使用する演習環境へアクセスします。

  1. HostVM1にサインインします。
  1. デスクトップにあるHyper-Vマネージャーをクリックします。
  1. Hyper-Vマネージャー画面で「DC1-CTCT」「DC2-CTCT」をクリックし、アクセスします。

  

  #### タスク 2：FSMOの役割を確認する

  この手順はDC1-CTCTで実施してください。

  1. 「スタート」メニューから「サーバーマネージャー」を開きます。

  1. サーバーマネージャーの「ツール」メニューから「Active Directory ユーザーとコンピューター」を選択します。

  1. 「Active Directory ユーザーとコンピューター」ウィンドウ内で、左側のペインで「ctct.com」ドメインを右クリックし、「すべてのタスク」を選択してから、「操作マスター...」をクリックします。

  1. 「操作マスター」ウィンドウが開きます。

  1. 「操作マスター」ウィンドウには、RID、PDC、およびインフラストラクチャのタブがあります。これらのタブを順にクリックして、それぞれのFSMO役割がどのドメインコントローラーに設定されているかを確認します。

     

  #### （オプション）タスク 2'：FSMOの役割「ドメイン命名マスター」を確認する（Active Directory ドメインと信頼関係）

  この手順はDC1-CTCTで実施してください。

  1. サーバーマネージャーの「ツール」メニューをクリックし、「Active Directory ドメインと信頼関係」を選択します。
  1. 「Active Directory ドメインと信頼関係」ウィンドウが開きます。
  1. 「Active Directory ドメインと信頼関係」ウィンドウで、「ctct.com」を右クリックし、「プロパティ」を選択します。
  1. プロパティウィンドウの「信頼関係」タブを選択し、「操作マスター...」ボタンをクリックしてドメイン命名マスターを確認します。

  

  #### （オプション）タスク 2''：FSMOの役割「スキーママスター」を確認する（Active Directory スキーマ）

  この手順はDC1-CTCTで実施してください。

  1. サーバーマネージャーの「ツール」メニューをクリックし、「Active Directory スキーマ」を選択します。
  1. 「Active Directory スキーマ」ウィンドウが開きます。
  1. 「Active Directory スキーマ」ウィンドウで、左ペインの「Active Directory スキーマ」を右クリックし、「操作マスター...」を選択してスキーママスターを確認します。

  

  #### （オプション）タスク 2'''：FSMOの役割をPowerShellで確認する

  この手順はDC1-CTCTで実施してください。

  1. 「スタート」メニューをクリックし、「Windows PowerShell」を検索します。

  1. 「Windows PowerShell」を右クリックし、「管理者として実行」を選択します。

  1. 「ユーザーアカウント制御」ウィンドウが表示される場合は、「はい」をクリックして続行します。

  1. 「Administrator: Windows PowerShell」ウィンドウが開きます。

  1. PowerShellがActive Directoryコマンドを理解できるように、ADモジュールをインポートします。(省略可)

     ```powershell
     Import-Module ActiveDirectory
     ```

  1. 以下のコマンドを「Administrator: Windows PowerShell」ウィンドウに入力して、エンターキーを押します。

     ```powershell
     Get-ADForest -Identity ctct.com | Format-List SchemaMaster,DomainNamingMaster
     Get-ADDomain -Identity ctct.com | Format-List PDCEmulator,RIDMaster,InfrastructureMaster
     ```

  1. PowerShellの出力結果を確認し、各FSMO役割がどのドメインコントローラーに割り当てられているかを確認します。

  

  #### タスク 3：FSMOの役割をPowerShellで移行する

  この手順はDC1-CTCTで実施してください。

  1. 「スタート」メニューをクリックし、「Windows PowerShell」または「PowerShell」を選択して起動します。

  1. 「Windows PowerShell」を右クリックし、「管理者として実行」を選択します。

  1. 移行先サーバー「DC2-CTCT」に各FSMOの役割を移行するには、以下のコマンドを「Windows PowerShell」ウィンドウに入力してEnterキーを押します。

     ・ドメインレベルの役割（PDCエミュレーター、RIDマスター、インフラストラクチャマスター）の移行

     ```powershell
     Move-ADDomainControllerOperationMasterRole -Identity "DC2-CTCT" -OperationMasterRole PDCEmulator, RIDMaster, InfrastructureMaster -Force
     ```

     ・フォレストレベルの役割（スキーママスター、ドメイン命名マスター）の移行

     ```powershell
     Move-ADDirectoryServerOperationMasterRole -Identity "DC2-CTCT" -OperationMasterRole SchemaMaster, DomainNamingMaster -Force
     ```

  1. 移行が正常に完了したか確認するため、先ほどのFSMOの確認コマンドを再度実行して、移行が正常に行われたことを確認します。

     ```powershell
     Get-ADForest | Format-List SchemaMaster,DomainNamingMaster
     Get-ADDomain | Format-List PDCEmulator,RIDMaster,InfrastructureMaster
     ```

     

  これでラボは完了です。お疲れ様でした。
