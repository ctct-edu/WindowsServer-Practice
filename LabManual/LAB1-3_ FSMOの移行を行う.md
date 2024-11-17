---
lab:
    title: 'Active Directory Domain Service の高度な管理'
---

# ラボ1-3  - FSMOの移行を行う（目安：15分）

## 演習で使用するマシン

以下のマシンを使用します。

| マシン名           | 用途/目的            |
| ------------------ | -------------------- |
| DomainController_1 | FSMOの移行操作、監視 |
| DomainController_2 | FSMOの移行操作、監視 |



## 実施内容

+ タスク 1：FSMOの役割を確認する
+ タスク 2：FSMOの役割をPowerShellで移行する
+ タスク３：FSMOの役割をPowerShellで強制移行する



## 手順

#### タスク 1：FSMOの役割を確認する

この手順はDomainController_1で実施してください。

1. 「スタート」メニューから「サーバーマネージャー」を開きます。

1. サーバーマネージャーの「ツール」メニューから「Active Directory ユーザーとコンピューター」を選択します。

1. 「Active Directory ユーザーとコンピューター」ウィンドウ内で、左側のペインで「ctct.local」ドメインを右クリックし、「すべてのタスク」を選択してから、「操作マスター...」をクリックします。

1. 「操作マスター」ウィンドウには、RID、PDC、およびインフラストラクチャのタブがあります。これらのタブを順にクリックして、それぞれのFSMO役割がどのドメインコントローラーに設定されているかを確認します。

1. 確認後に「閉じる」をクリックします。




#### タスク 2：FSMOの役割をPowerShellで移行する

この手順はDomainController_1で実施してください。

1. 「スタート」メニューをクリックし、「Windows PowerShell」または「PowerShell」を選択して起動します。

1. 「Windows PowerShell」を右クリックし、「管理者として実行」を選択します。

1. 現在どのドメインコントローラがFSMOロールを保持しているか確認します。

   ```powershell
   Get-ADForest ctct.local | Format-List DomainNamingMaster, SchemaMaster
   Get-ADDomain ctct.local | Format-List RIDMaster, PDCEmulator, InfrastructureMaster
   ```

1. FSMOの移行を実施します。以下のコマンドを実行します。

   ```powershell
   Move-ADDirectoryServerOperationMasterRole -Identity "DC2" -OperationMasterRole 0,1,2,3,4
   ```

1. 以下のプロンプトが表示されます。Aを指定してください。

   ```powershell
   操作マスターの役割の移動
   役割 'PDCEmulator' をサーバー 'dc2.ctct.local' に移動しますか?
   [Y] はい(Y)  [A] すべて続行(A)  [N] いいえ(N)  [L] すべて無視(L)  [S] 中断(S)  [?] ヘルプ (既定値は "Y"):A
   ```

   > 参考：「Move-ADDirectoryServerOperationMasterRole」コマンドのオプション
   >
   > Move-ADDirectoryServerOperationMasterRole -Identity "移行先のホスト名" -OperationMasterRole 0,1,2,3,4
   > ・PDC エミュレーター … 0
   >
   > ・RID マスター … 1
   >
   > ・インフラストラクチャマスター … 2
   >
   > ・スキーママスター … 3
   >
   > ・ドメイン名前付けマスター … 4
   > ※ カンマ区切りで同時に複数を指定可能です。

1. DC2にFSMOが移行されたことを確認します。

   ```powershell
   Get-ADForest ctct.local | Format-List DomainNamingMaster, SchemaMaster
   Get-ADDomain ctct.local | Format-List RIDMaster, PDCEmulator, InfrastructureMaster
   ```

   

#### タスク３：FSMOの役割をPowerShellで強制移行する

この手順はDomainController_1で実施してください。

1. FSMOの移行を実施します。以下のコマンドを実行します。

   ```cmd
   ntdsutil
   ```

1. 対話型のコマンドが開始されます。以下の手順の「：」以降に文字を入力してください。

1. 「C:\Windows\system32\ntdsutil.exe：」には「roles」と入力し、エンターキーを押します。

    ```cmd
    C:\Windows\system32\ntdsutil.exe: roles
    ```

1. 「fsmo maintenance：」には「connections」と入力し、エンターキーを押します。

1. 「server connections：」には「connect to server dc1.ctct.local」と入力し、エンターキーを押します。

1. 以下が表示されれば、接続成功です。

    ```cmd
    dc2.ctct.local に結合しています...
    ローカルでログオンしているユーザーの資格情報を使って dc2.ctct.local に接続しました。
    ```

1. 「server connections：」には「quit」と入力し、エンターキーを押します。

1. 「fsmo maintenance：」には「seize schema master」と入力し、エンターキーを押します。

1. 「サーバー"DC1.ctct.localのschema役割..."」のポップアップが表示されます。「はい」を選択します。

1. Schema Masterの移行が完了します。

    > 場合によっては、転送に失敗した旨のメッセージが表示される場合がありますが、そのまま無視してください。

1. 「fsmo maintenance：」には「seize naming master」と入力し、エンターキーを押します。

1. 「サーバー"DC1.ctct.localのdomain naming 役割..."」のポップアップが表示されます。「はい」を選択します。

     > 場合によっては、転送に失敗した旨のメッセージが表示される場合がありますが、そのまま無視してください。

1. 「fsmo maintenance：」には「seize pdc」と入力し、エンターキーを押します。

1. 「サーバー"DC1.ctct.localのPDC 役割..."」のポップアップが表示されます。「はい」を選択します。

1. 「fsmo maintenance：」には「seize rid master」と入力し、エンターキーを押します。

1. 「サーバー"DC1.ctct.localのRID Master 役割..."」のポップアップが表示されます。「はい」を選択します。

1. 「fsmo maintenance：」には「seize infrastructure master」と入力し、エンターキーを押します。

1. 「サーバー"DC1.ctct.localのInfrastructure 役割..."」のポップアップが表示されます。「はい」を選択します。

1. これで、FSMOの強制移行が完了しました。

1. 「fsmo maintenance：」に「quit」と入力し、エンターキーを押します。

1. 「C:\Windows\system32\ntdsutil.exe：」に「quit」と入力し、エンターキーを押します。

1. 再度FSMOロールの保持者を確認して、変更が正しく反映されているか確認します。

     ```powershell
     Get-ADForest ctct.local | Format-List DomainNamingMaster, SchemaMaster
     Get-ADDomain ctct.local | Format-List RIDMaster, PDCEmulator, InfrastructureMaster
     ```

1. 実行結果として、FSMOの役割が、DC1.ctct.localに移行したことを確認します。



これでラボは完了です。お疲れ様でした。
