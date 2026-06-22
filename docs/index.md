<p align="center">
  鳥科 電装班の公式ブログへようこそ！
</p>

# 鳥科とは？

東京理科大学 葛飾キャンパスを拠点とする鳥人間サークルです．鳥人間コンテスト 滑空機部門に出場し，私たちで作った飛行機を琵琶湖の空に飛ばすために活動しています．

<span style="font-size:large;"> 詳しくは，[鳥科公式ブログ](https://torica-tus.com)をご確認ください！</span>

# 電装班とは？

機体に搭載する，電子機器の製作を担当している班です．

# 何を作っている？

## フライトロガー

以下のようなデータを取得し，microSDカードに保存するための装置です．
- 対気速度
- 高度（琵琶湖の湖面までの距離）
- ...

## 操舵システム

サーボモーターを用いて機体後部の垂直尾翼を駆動しています．
パイロットの手元にあるレバーにより，繊細な操作が可能です．

# 記事

## PR
- [RP2040小型化基板の製作とJLCPCBの魅力に迫る](commercials/TORICAberry_Pi_Bico)

## 作業
- [一般教養としての電装](work/electronics-introduction) 

<details>
  <summary>

## 電装班員の寝言
    
  </summary>
  <ul>
    {% assign negoto_pages = site.pages | where_exp: "item", "item.path contains 'negoto/'" %}
    {% for page in negoto_pages %}
      {% comment} インデックスページ（index.mdなど）を除外する場合 {% endcomment %}
      {% unless page.url == '/negoto/' or page.url contains 'index' %}
        <li>
          <a href="{{ page.url }}">{{ page.title | default: page.name }}</a>
        </li>
      {% endunless %}
    {% endfor %}
  </ul>
</details>
