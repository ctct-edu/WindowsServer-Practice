---
lab:
    title: 'Active Directory Domain Service の高度な管理'
---

# ラボ1-2  - FSMOの操作を行う

## 目標

このラボでは次の内容を学習します。

+ タスク 1：演習環境へアクセス

+ タスク 2：FSMOの役割を確認する（Active Directory ユーザーとコンピューター）

+ タスク 3：FSMOの役割をPowerShellで移行する






## 予想時間：30分



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

1. 「操作マスター」ウィンドウには、RID、PDC、およびインフラストラクチャのタブがあります。これらのタブを順にクリックして、それぞれのFSMO役割がどのドメインコントローラーに設定されているかを確認します。

1. 確認後に「閉じる」をクリックします。




#### タスク 3：FSMOの役割をPowerShellで移行する

この手順はDC1-CTCTで実施してください。

1. 「スタート」メニューをクリックし、「Windows PowerShell」または「PowerShell」を選択して起動します。

1. 「Windows PowerShell」を右クリックし、「管理者として実行」を選択します。

1. 現在どのドメインコントローラがFSMOロールを保持しているか確認します。

   ```powershell
   Get-ADForest ctct.com | Format-List DomainNamingMaster, SchemaMaster
   Get-ADDomain ctct.com | Format-List RIDMaster, PDCEmulator, InfrastructureMaster
   ```

1. FSMOの強制移行を実施します。以下のコマンドを実行します。

   ```powershell
   ntdsutil
   ```

1. 対話型のコマンドが開始されます。以下の手順の「：」以降に文字を入力してください。

1. 「C:\Windows\system32\ntdsutil.exe：」には「roles」と入力し、エンターキーを押します。

1. 「fsmo maintenance：」には「connections」と入力し、エンターキーを押します。

1. 「server connections：」には「connect to server DC2-CTCT.ctct.com」と入力し、エンターキーを押します。

1. 「ローカルでログオンしているユーザーの資格情報を使って DC2-CTCT.ctct.com に接続しました。」と表示されます。

1. 「server connections：」には「quit」と入力し、エンターキーを押します。

1. 「fsmo maintenance：」には「seize schema master」と入力し、エンターキーを押します。

1. 「サーバー"DC2-CTCT.ctct.comのschema役割..."」のポップアップが表示されます。「はい」を選択します。

1. Schema Masterの移行が完了します。

    > 場合によっては、転送に失敗した旨のメッセージが表示される場合がありますが、そのまま無視してください。

1. 「fsmo maintenance：」には「seize naming master」と入力し、エンターキーを押します。

1. 「サーバー"DC2-CTCT.ctct.comのdomain naming 役割..."」のポップアップが表示されます。「はい」を選択します。

    > 場合によっては、転送に失敗した旨のメッセージが表示される場合がありますが、そのまま無視してください。

1. 「fsmo maintenance：」には「seize pdc」と入力し、エンターキーを押します。

1. 「サーバー"DC2-CTCT.ctct.comのPDC 役割..."」のポップアップが表示されます。「はい」を選択します。

1. 「fsmo maintenance：」には「seize rid master」と入力し、エンターキーを押します。

1. 「サーバー"DC2-CTCT.ctct.comのRID Master 役割..."」のポップアップが表示されます。「はい」を選択します。

1. 「fsmo maintenance：」には「seize infrastructure master」と入力し、エンターキーを押します。

1. 「サーバー"DC2-CTCT.ctct.comのInfrastructure 役割..."」のポップアップが表示されます。「はい」を選択します。

1. これで、FSMOの強制移行が完了しました。

1. 「fsmo maintenance：」に「quit」と入力し、エンターキーを押します。

1. 「C:\Windows\system32\ntdsutil.exe：」に「quit」と入力し、エンターキーを押します。

1. 再度FSMOロールの保持者を確認して、変更が正しく反映されているか確認します。

    ```powershell
    Get-ADForest ctct.com | Format-List DomainNamingMaster, SchemaMaster
    Get-ADDomain ctct.com | Format-List RIDMaster, PDCEmulator, InfrastructureMaster
    ```

1. 実行結果として、FSMOの役割が、DC2-CTCT.ctct.comに移行したことを確認します。

> この演習は、環境の都合により、強制移行をしてます。
>
> 本来は強制移行ではなく、以下のコマンドを実行します。
>
> Move-ADDirectoryServerOperationMasterRole -Identity "移行先のホスト名" -OperationMasterRole 0,1,2,3,4
>
> 参考：OperationMasterRole オプション
> ・PDC エミュレーター … 0
> ・RID マスター … 1
>
> ・インフラストラクチャマスター … 2
> ・スキーママスター … 3
> ・ドメイン名前付けマスター … 4
> ※ カンマ区切りで同時に複数を指定可能です。



これでラボは完了です。お疲れ様でした。
