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

<pre class=mermaid style="background-color:white;">
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
        R3 <==>|"ラダーLR接続ケーブル"| L3
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

    solder <==>|"LiPo接続ケーブル"| XT
    toRudder <==>|"Air - ラダー接続ケーブル"| R4
    toUnder <==>|"Air - 機体下接続ケーブル"| under4
    toFuse <==>|"Air - 胴体桁接続ケーブル"| fuselage4
    toServo <===>|"Air - サーボ接続ケーブル"| servo3
</pre>

## アセンブリ - シーケンス図

<pre class=mermaid style="background-color:white;">
sequenceDiagram
    actor member as 全体
    participant wing as 翼班
    participant assem as 接合班
    participant cockpit as コクピ班
    participant elec as 電装班

    Note over member,elec: 組み上げ開始
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

## デバッグフローチャート
### フライト前日(7/25)
<pre class=mermaid style="background-color:white;">
flowchart TD
    error["正常に動作しない"]
    noPower["電源が入らない"]
    servo?["サーボが動作するか？"]
    noLog["ログが取れない"]

    error --> servo?
</pre>
### フライト当日(7/26)
<pre class=mermaid style="background-color:white;">
flowchart TD
    error["正常に動作しない"]
    power?{{"電源が入るか？"}}
    conn?{{"コネクタ接続は適切か？"}}
    connFix["コネクタ接続修正"]
    lipo?{{"バッテリー電圧は正常か？"}}
    lipoReplace["バッテリー交換"]
    poli?{{"ポリスイッチが作動していないか？"}}
    poliReplace["ケーブルごと交換"]
    servo?{{"サーボが動作するか？"}}
    log?{{"ログが取れるか？"}}
    burn["ソフト書き換え"]
    test["動作テスト"]

    error --> conn?
    conn? -->|-No-| connFix --> test
    conn? --->|-Yes-| power?

    power? -->|-No-| lipo?
    lipo? --->|-Yes-| poli?
    poli? -->|-Yes-| poliReplace --> test

    
    lipo? -->|-No-| lipoReplace --> test 

    power? --->|-Yes-| log?
    log? --> servo?
    servo? -->|-Yes-| burn
</pre>

<script type="module">
    import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
    mermaid.initialize({ startOnLoad: true });
</script>
