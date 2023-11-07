---
lab:
    title: 'Active Directory Domain Service の高度な管理'
---

# ラボ1-2  - FSMOの操作を行う

## 目標

このラボでは次の内容を学習します。

- タスク 1：演習環境へアクセス

- タスク 2：FSMOの役割を確認する（Active Directory ユーザーとコンピューター）

- タスク 3：FSMOの役割をPowerShellで移行する

  ※オプション演習はスキップしてOKです。

  

  ## 予想時間：20分

  

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

  

  #### タスク 3：FSMOの役割をPowerShellで移行する

  この手順はDC1-CTCTで実施してください。

  1. 「スタート」メニューをクリックし、「Windows PowerShell」または「PowerShell」を選択して起動します。

  1. 「Windows PowerShell」を右クリックし、「管理者として実行」を選択します。

  1. 現在どのドメインコントローラがFSMOロールを保持しているか確認します。

     ```powershell
     Get-ADForest ctct.com | Format-List DomainNamingMaster, SchemaMaster
     Get-ADDomain ctct.com | Format-List RIDMaster, PDCEmulator, InfrastructureMaster
     ```

  1. FSMOロールを新しいドメインコントローラ (`DC2-CTCT`) に移動します。

     このとき、確認メッセージが表示されるので、「Y」(Yes) を選択して操作を進めます。

     ```powershell
     Move-ADDirectoryServerOperationMasterRole -Identity DC2-CTCT -OperationMasterRole 0,1,2,3,4
     ```

     >参考：OperationMasterRole オプション
     >・PDC エミュレーター … 0
     >・RID マスター … 1
     >・インフラストラクチャマスター … 2
     >・スキーママスター … 3
     >・ドメイン名前付けマスター … 4
     >※ カンマ区切りで同時に複数を指定可能です

  1. 再度FSMOロールの保持者を確認して、変更が正しく反映されているか確認します。

     ```powershell
     Get-ADForest ctct.com | Format-List DomainNamingMaster, SchemaMaster
     Get-ADDomain ctct.com | Format-List RIDMaster, PDCEmulator, InfrastructureMaster
     ```

     

  これでラボは完了です。お疲れ様でした。
