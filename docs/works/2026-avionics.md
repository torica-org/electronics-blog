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
        solder{{"はんだ付け"}}
        toUnder{{"PA 4ピン"}}
        toFuse{{"PA 4ピン"}}
        toRudder{{"PA 4ピン"}}
        toServo{{"PA 3ピン"}}
        
        solder ~~~ toUnder ~~~ toFuse ~~~ toServo ~~~ toRudder
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

    Conn12 <==> solder

    solder == "LiPo接続ケーブル" <==> XT
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
        platform(["⌛️プラホ到達⌛️"])
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

    servo -- "❌️ No ❌️" --> debugServo1 --> debugServo2 --> platform --> lock
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
flowchart TD
    servoNCCheck["サーボケーブル<br>抜線確認"]
    powerOn(("電源投入"))
    servoCall["周囲に呼びかけ"]
    servoSafe["可動範囲の安全と<br>自身の頭の位置を確認"]
    servoConn(("サーボケーブル<br>接続"))

    subgraph on["電源投入"]
        servoNCCheck --> powerOn
        powerOn -- "駆動可能な場合" --> servoCall --> servoSafe --> servoConn
    end

    powerOff(("電源遮断"))
    servoNC(("サーボケーブル<br>抜線"))
    
    subgraph off["電源遮断"]
        powerOff -- "サーボ駆動中だったなら" --> servoNC
    end

    on ~~~ off
</pre>
{% endraw %}

{% raw %}
<script type="module">
    import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
    let config = { startOnLoad: true, htmlLabels: true };
    mermaid.initialize(config);
</script>
{% endraw %}


