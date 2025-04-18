---
lab:
    title: 'Windows ServerのHyper-V 仮想化'
---

# ラボ4-2  - Hyper-V レプリカを構成（目安：15分）

## 演習で使用するマシン

この演習では、Hostvm1、Hostvm2を使用します。

※Hyper-Vマネージャー画面で起動している全ての仮想マシンを停止してください。



## 目標

このラボでは次の内容を学習します。

- タスク１：HostVm2でHyper-V レプリカを構成する
- タスク２：Hostvm1でHyper-V レプリカを構成する
- タスク３：SV01のHyper-V レプリカを構成する
- タスク４：レプリケーションを実施
- タスク５：レプリケーションされたサーバーの起動



## 手順

#### タスク１：HostVm2でHyper-V レプリカを構成する

この手順はHostvm2で実施してください。

1. デスクトップアイコンから「Hyper-V マネージャー」をクリックします。

2. 画面右上の「操作」メニューから「Hyper-Vの設定...」をクリックします。

3. 「レプリケーションの構成」をクリックし、「レプリカサーバーとしてこのコンピュータを構成する」にチェックを入れます。

4. 「Kerberosを使用する (HTTP)」を選び、ポートとして「80」のままにします。

5. 「承認と記憶域」にある「認証されたすべてのサーバーからレプリケーションを許可する」を選択します。

6. 「OK」をクリックして設定を保存します。

7. 「受信トラフィックを許可するように...」のポップアップが表示されます。そのまま「OK」をクリックします。

   > この環境はファイヤーウォールを無効化しています。
   >
   > ケルベロス認証（ADDSがある場合）を使用する場合は、ポート80を解放します。
   >
   > 証明書を使用する場合(ADDS有または無し)を使用する場合は、ポート443を解放します。

   

#### タスク２：Hostvm1でHyper-V レプリカを構成する

この手順はHostvm1で実施してください。（タスク2の内容と同じです）

1. デスクトップアイコンから「Hyper-V マネージャー」をクリックします。

2. 画面右上の「操作」メニューから「Hyper-Vの設定...」をクリックします。

3. 「レプリケーションの構成」をクリックし、「レプリカサーバーとしてこのコンピュータを構成する」にチェックを入れます。

4. 「Kerberosを使用する (HTTP)」を選び、ポートとして「80」のままにします。

5. 「承認と記憶域」にある「認証されたすべてのサーバーからレプリケーションを許可する」を選択します。

6. 「OK」をクリックして設定を保存します。

7. 「受信トラフィックを許可するように...」のポップアップが表示されます。そのまま「OK」をクリックします。

   > この環境はファイヤーウォールを無効化しています。
   >
   > ケルベロス認証（ADDSがある場合）を使用する場合は、ポート80を解放します。
   >
   > 証明書を使用する場合(ADDS有または無し)を使用する場合は、ポート443を解放します。



#### タスク３：SV01のHyper-V レプリカを構成する

この手順はHostvm2で実施してください。

1. 「サーバーマネージャー」を開き、「ツール」から「Hyper-V マネージャー」を選択します。

2. 「hostvm1」上でレプリケーションを行いたい仮想マシン「SV01」を右クリックし、「レプリケーションを有効にする」をクリックします。

3. 「SV01のレプリケーションを有効にする」ウィザードが開いたら、「次 >」をクリックして進みます。

4. 「レプリケーションサーバー」のフィールドに「hostvm1.edu.local」を入力し、「次 >」をクリックします。

5. 「接続パラメーターの指定」画面では、ポートが「80」、認証の種類は「Kerberos認証」とのままし、「次 >」をクリックします。

6. 「レプリケーションVHDの選択」画面では、何も変更せずに、「次 >」をクリックします。

7. 「レプリケーションの頻度の構成」画面では、「5分」のまま、「次 >」をクリックします。

8. 「追加の回復ポイントの構成」画面では、何も変更せずに、「次 >」をクリックします。

9. 「初期レプリケーションの方法の選択」画面では、何も変更せずに、「次 >」をクリックします。

10. 設定内容を確認し、「完了」をクリックしてウィザードを終了します。

11. 「レプリケーションが正常に有効になりました」のポップアップが表示されます。「閉じる」をクリックします。

12. Hyper-Vマネージャーの画面でSV01が「初期レプリカの送信中」に状況が変わります。完了するまで待ちます。

    > このタイミングでHostVM1のHyper-VマネージャーにもSV01が表示されます。

    

#### タスク４：レプリケーションを実施

この手順はHostvm2で実施してください。

1. 「サーバーマネージャー」を開き、上部のメニューから「ツール」をクリックし、「Hyper-V マネージャー」を選択します。
2. SV01を右クリックし、「レプリケーション」＞「レプリケーションの正常性の表示」を選択します。
3. レプリケーションの状態や最後の同期時間などの情報を確認し、「閉じる」をクリックします。
4. SV01を右クリックし、「レプリケーション」＞「計画フェールオーバー」を選択します。
5. 「計画フェールオーバー」画面で、何も変えずに「フェールオーバー」をクリックします。



#### タスク５：レプリケーションされたサーバーの起動

この手順はHostvm1で実施してください。

1. Hostvm1の「サーバーマネージャー」を開き、上部のメニューから「ツール」をクリックし、「Hyper-V マネージャー」を選択します。
2. SV01がHostvm1上で起動していることを確認します。



これでラボは完了です。お疲れ様でした。
