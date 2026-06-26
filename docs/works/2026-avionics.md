---
layout: default
title: "2026 REWRITE搭載電装 - 取り扱い説明書"
date: 2026-06-22
---

# 2026 REWRITE搭載電装 - 取り扱い説明書

## ⚠️注意事項⚠️
- 発煙・発火などの異常が見られた場合，速やかに電源を遮断すること．
- LiPoバッテリーに膨張などの異常が見られた場合，使用しないこと．
- 電源を投入する際，サーボモーターが急激に駆動することを周知する声掛けをすること．
- ***異常と判断できなくても，気になることがあればすぐに26代の電装班員に報告・確認すること．***
    - 異常を見つけてくれたらヒーロー🦸
    - 異常じゃなければ一安心🥰

## ケーブルの接続
- 

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

## アセンブリ - シーケンス図
- 変更される可能性有り．
- 正確に，かつ速やかに動作させられる状態にもっていくこと．
- カウル，翼，桁など，電装班では馴染みの少ない製作物に触れるため，十二分に注意すること．

{% raw %}
<pre class="mermaid" style="background-color:white;">
sequenceDiagram
    <!-- actor member as 全体 -->
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

## デバッグフローチャート
- 重大なエラーが発生しても，パイロットの安全を重視し***サーボモーターは動作させられるよう最大限努力する．***
- 本番当日は，基本的にマイコンに***ソフトウェアを書き込まない．***
- 本番直前では，フライトロガーの動作について***「諦める」***可能性有り．

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

{% raw %}
<script type="module">
    import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
    let config = { startOnLoad: true, htmlLabels: true };
    mermaid.initialize(config);
</script>
{% endraw %}


