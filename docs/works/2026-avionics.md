---
layout: default
title: "2026 REWRITE搭載電装 - 取り扱い説明書"
date: 2026-06-22
---

# 2026 REWRITE搭載電装 - 取り扱い説明書

<br>

---

<br>

## ⚠️注意事項⚠️
- 発煙・発火などの異常が見られた場合，速やかに電源を遮断する．
- LiPoバッテリーに膨張などの異常が見られた場合，使用しない．
- サーボモーターが急激に駆動することを周知する声掛けをする．
- ***異常と判断できなくても，気になることがあればすぐに26代の電装班員に報告・確認する．***
    - 異常を見つけてくれたらヒーロー🦸
    - 異常じゃなければ一安心🥰
- カウル，翼，桁など，電装班では***馴染みの少ない製作物に触れるため，十二分に注意する．***

<br>

---

<br>

## 簡単な仕組み
### メインの機能
- フライトログ（フライト中の様々な物理量）の測定・記録をおこなう．
    - センサーのある各電装部からエアデータ電装部に測定した値を送る．
    - エアデータ電装部で各基板のデータを処理，1行の文字列に変換．
    - 生成したログを3箇所（エアデータ，機体下，胴体桁）のmicroSDカードに分散して保存（冗長性の確保）．
- ラダーの駆動
    - ロードセルの値を読み取り，レバーを握り込む力に応じて垂直尾翼を駆動する．
### サブの機能
- ライブラリ`SerialWeb`を用いたWi-Fi経由のデバッグログ出力．
- ピッチが水平であるかを示すビープ音をBluetoothで送信し，Bluetoothイヤホン経由でテール持ちへ伝達．
- 胴体桁電装部にあるスピーカーにより，機速などの情報をパイロット伝達．

{% raw %}
<pre class="mermaid" style="background-color:white;">
flowchart TD
    subgraph air["エアデータ電装部 (Airdata/Air)"]
        ics(["ICS変換基板"])
        main(["主マイコン"])
        sub(["副マイコン"])
        sensors(["各センサー"])
        
        ics ~~~ main
        sensors -- "機速<br>GPS<br>気温・気圧" --> main
        sensors -- "電圧<br>電流" --> sub
        main -- "ログ保存" --> sub
    end
    rudder["ラダー電装部 (Rudder)"]
    fuselage["胴体桁電装部 (Fuselage)"]
    servo["サーボモーター (Servo)"]
    under["機体下電装部 (Under)"]
    client["スマホなど(Wi-Fi)"]
    bt["無線イヤホン"]
    speaker["スピーカー"]
    tail(("テール持ち"))
    p(("パイロット"))

    under -- "超音波高度<br>LiDAR高度<br>気温・気圧" --> main
    fuselage -- "姿勢角×2<br>気温・気圧" --> main
    rudder -- "駆動角度" --> main
    rudder ---> ics -- "サーボ駆動" --> servo
    main -- "ログ保存" ---> under
    main -- "ログ保存" ---> fuselage
    sub -. "デバッグログ" .-> client
    fuselage -.-> bt -- "水平伝達" --> tail
    fuselage --> speaker -- "機速など" --> p
</pre>
{% endraw %}

<br>

---

<br>

## ケーブルの接続
- 差し込む際は，***奥まで差し込む．***
- 抜く際は，***コネクタを持ち，ケーブルを引っ張らない．***
- PAコネクタの場合は，ロック機構があることに注意する．

{% raw %}
<pre class="mermaid" style="background-color:white;">
flowchart TD
    subgraph airdata["エアデータ電装部 (Airdata/Air)"]
        Conn12{{"PA 12ピン"}}
    end

    subgraph terminal["ターミナル基板"]
        solder{{"XTコネクタ"}}
        toAir{{"PA 12ピン"}}
        toUnder{{"PA 4ピン"}}
        toFuse{{"PA 4ピン"}}
        toRudder{{"PA 4ピン"}}
        toServo{{"PA 3ピン"}}
        
        toAir ~~~ solder ~~~ toUnder ~~~ toFuse ~~~ toServo ~~~ toRudder
    end

    subgraph lipo["LiPoバッテリー"]
        XT{{"XTコネクタ"}}
    end

    subgraph rudder["ラダー電装部 (Rudder)"]
        subgraph L["左ラダー"]
            loadcellL["ロードセル"]
            L3{{"PA 3ピン"}}
        end
        subgraph R["右ラダー"]
            loadcellR["ロードセル"]
            R3{{"PA 3ピン"}}
            R4{{"PA 4ピン"}}
        end
        R3 == "ラダーLR接続ケーブル" <==> L3
    end

    subgraph under["機体下電装部 (Under)"]
        under4{{"PA 4ピン"}}
    end

    subgraph fuselage["胴体桁電装部 (Fuselage)"]
        fuselage4{{"PA 4ピン"}}
    end

    subgraph servo["サーボモーター (Servo)"]
        servo3{{"専用 3ピン"}}
    end

    Conn12 <==> toAir

    solder <==> XT
    toRudder == "Air - ラダー接続ケーブル" <==> R4
    toUnder == "Air - 機体下接続ケーブル" <==> under4
    toFuse == "Air - 胴体桁接続ケーブル" <==> fuselage4
    toServo == "Air - サーボ接続ケーブル" <==> servo3
</pre>
{% endraw %}

<br>

---

<br>


## アセンブリ - シーケンス図
- 変更される可能性有り．
- 正確に，かつ速やかに動作させられる状態にもっていく．

{% raw %}
<pre class="mermaid" style="background-color:white;">
sequenceDiagram
    participant wing as 翼班
    participant assem as 接合班
    participant cockpit as コクピ班
    participant elec as 電装班

    Note over assem: T字準備
    assem -->> cockpit:
    Note over cockpit: フレーム接合
    cockpit -->> elec:
    Note over elec: ケーブル整理
    elec -->> cockpit:
    Note over cockpit: カウル取付
    cockpit -->> assem:
    Note over assem: テール桁接合
    assem -->> cockpit:
    Note over cockpit,elec: Airケーブル通す
    Note over cockpit,elec: サーボケーブル通す
    Note over cockpit,elec: 機体下ケーブル接続
    Note over elec: Air接合
    Note over elec: Airケーブル接続
    elec -->> assem:
    Note over wing,assem: 主翼組み上げ
</pre>
{% endraw %}

<br>

---

<br>

## デバッグフローチャート
- フライト当日は，基本的にマイコンに***ソフトウェアを書き込まない***（状況が悪化する可能性があるため）．
- フライト直前では，フライトロガーの動作について***「諦める」***可能性有り．
- 重大なエラーが発生しても，パイロットの安全を重視し***サーボモーターは動作させられるよう最大限努力する．***

{% raw %}
<pre class="mermaid" style="background-color:white;">
flowchart TD
    error(("正常に動作しない"))
    power{"電源が入る？"}
    conn{"コネクタ接続は<br>適切？"}
    connFix(("コネクタ接続修正"))

    lipo{"バッテリー電圧は<br>正常？"}
    lipoReplace(("バッテリー交換"))

    poli{"ポリスイッチが<br>作動した？"}
    cableBreak{"ケーブルが<br>断線？"}
    cableReplace(("ケーブル交換"))


    servo{"サーボは<br>動作する？"}

    log{"フライトロガー<br>機能維持？"}



    subgraph theDayBefore["📅前日📅"]
        debugServo1[["デバッグ<br>（ソフト書換を含む⚡️）"]]
        debugLogger[["デバッグ<br>（ソフト書換を含む⚡️）"]]
    end

    subgraph thatDay["📅当日📅"]
        debugServo2[["デバッグ<br>（ソフト書換を含む⚡️）"]]
        noSoft[["デバッグ<br>（ソフト書換なし❌️）"]]
        morning(["⌛️フライト当日朝⌛️"])
        dock(["⌛️桟橋到達⌛️"])
        dockServo(["⌛️桟橋到達⌛️"])
        giveUp(("デバッグを終了<br>最低限動作"))
        lock(("ニュートラル<br>ロック"))
    end

    error --> conn
    conn -- "❌️ No ❌️" --> connFix
    conn -- "✅️ Yes ✅️" ---> power

    power -- "❌️ No ❌️" --> lipo
    lipo -- "✅️ Yes ✅️" ---> poli
    poli -- "✅️ Yes ✅️" ---> cableReplace
    poli -- "❌️ No ❌️" --> cableBreak 
    cableBreak -- "✅️ Yes ✅️" --> cableReplace

    lipo -- "❌️ No ❌️" --> lipoReplace
    
    power -- "✅️ Yes ✅️" --> servo

    servo -- "❌️ No ❌️" --> debugServo1 --> debugServo2 --> dockServo--> lock
    servo -- "✅️ Yes ✅️" ---> log

    log -- "❌️ No ❌️" --> debugLogger --> morning --> noSoft --> dock --> giveUp

</pre>
{% endraw %}

<br>

---

<br>

## 電源投入／遮断手順
以下の事項が要求される．
- フライトまでに，5分以上の事前運転をする．
  - 9軸センサーは地磁気を利用してヨー角を取得するため，キャリブレーション（センサーの校正）が必要．
  - 自動でおこなわれるが，時間がかかる．
- 垂直尾翼を取り付ける際は，サーボモーターの破損を防ぐためケーブルを抜く．
- 垂直尾翼が取り付けられたまま運用する際は，風により回転するのを防ぐためにサーボモーターにケーブルを接続して駆動させておく．

{% raw %}
<pre class="mermaid" style="background-color:white;">
flowchart LR
    servoNCCheck["サーボケーブル<br>抜け確認"]
    powerOn(("メインスイッチ<br><strong>ON</strong>"))
    servoCall["周囲に呼びかけ"]
    servoSafe["可動範囲の安全と<br>自身の頭の位置を確認"]
    servoConn(("サーボケーブル<br>接続"))

    subgraph on["⚡️電源投入⚡️"]
        servoNCCheck --> powerOn
        powerOn -- "駆動可能な場合" --> servoCall --> servoSafe --> servoConn
    end

    powerOff(("メインスイッチ<br><strong>OFF</strong>"))
    servoNC(("サーボケーブル<br>抜く"))
    
    subgraph off["⛔️電源遮断⛔️"]
        powerOff -- "サーボ駆動中だったなら" --> servoNC
    end

    on ~~~ off
</pre>
{% endraw %}

<br>

---

<br>
## フライト直前の動き

{% raw %}
<pre class="mermaid" style="background-color:white;">
sequenceDiagram
    actor aya
    actor mss
    actor atu
    participant wing as 翼周辺
    participant tail as テール周辺

    Note over aya,tail: 桟橋到達
    alt 風速 ~3m/sの場合
        Note over tail: 垂直尾翼接合
        tail -->> mss:
        Note over mss: 電源投入
        mss -->> atu:
        Note over atu: サーボ接続
    else 風速 3m/s~の（または運用上危険な）場合
        Note over mss: 電源投入
    end
    Note over aya,tail: 桟橋運用
    Note over aya,tail: 機体定位置
    Note over aya,tail: 馬入れ
    opt 垂直尾翼未接合なら
        Note over tail: 垂直尾翼接合
        tail -->> atu:
        Note over atu: サーボ接続
    end
    Note over mss: 電源スイッチ，ログを確認
    mss -->> aya:
    Note over aya: カーボン<br>（ドーサルフィン）取付
    Note over atu: コクピ内に潜る
    tail ->> mss: ニュートラル確認
    mss ->> atu: トリム指示
    Note over atu: トリム調整
    atu -->> tail:
    Note over tail: ドーサルフィン取付
    Note over aya,tail: 全体上げ
    Note over aya,tail: 馬はけ
    Note over atu: 胴体保持 in
    alt 同時におこなう
        Note over wing: 翼持ちA in
        Note over wing: 翼持ちB in
    else
        mss ->> atu: ラダー操作指示
        Note over atu: ラダー操作
    end
    Note over wing,tail: 運用棒抜く
    Note over wing,tail: キャノピー穴埋め
    Note over aya,tail: 位置調整・回転
    Note over atu: P交代
    Note over wing: 翼持ちA交代
    Note over tail: テール持ちセット
    Note over wing: 翼保持はけ
    Note over wing: 翼持ちB out
    Note over aya,tail: 乗り込み
</pre>
{% endraw %}

{% raw %}
<script type="module">
    import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
    let config = { startOnLoad: true, htmlLabels: true };
    mermaid.initialize(config);
</script>
{% endraw %}


