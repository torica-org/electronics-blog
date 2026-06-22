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

<!-- 自動で`commercials/`ディレクトリを列挙します -->
<ul>
  {% for p in site.pages %}
    {% if p.path contains 'commercials/' %}
      {% comment %} インデックスページやフォルダ自体を除外 {% endcomment %}
      {% unless p.path contains 'index.md' or p.path == 'commercials/' %}
        <li>
          <a href="{{ p.url | relative_url }}">
            {% if p.title %}
              {{ p.title }}
            {% else %}
              {% comment %} Front Matterにtitleがない場合はファイル名から生成 {% endcomment %}
              {{ p.name | replace: ".md", "" }}
            {% endif %}
          </a>
        </li>
      {% endunless %}
    {% endif %}
  {% endfor %}
</ul>

## 作業

<!-- 自動で`work/`ディレクトリを列挙します -->
<ul>
  {% for p in site.pages %}
    {% if p.path contains 'work/' %}
      {% comment %} インデックスページやフォルダ自体を除外 {% endcomment %}
      {% unless p.path contains 'index.md' or p.path == 'work/' %}
        <li>
          <a href="{{ p.url | relative_url }}">
            {% if p.title %}
              {{ p.title }}
            {% else %}
              {% comment %} Front Matterにtitleがない場合はファイル名から生成 {% endcomment %}
              {{ p.name | replace: ".md", "" }}
            {% endif %}
          </a>
        </li>
      {% endunless %}
    {% endif %}
  {% endfor %}
</ul>

## 電装班員の寝言

<!-- 自動で`negoto/`ディレクトリを列挙します -->
<ul>
  {% for p in site.pages %}
    {% if p.path contains 'negoto/' %}
      {% comment %} インデックスページやフォルダ自体を除外 {% endcomment %}
      {% unless p.path contains 'index.md' or p.path == 'negoto/' %}
        <li>
          <a href="{{ p.url | relative_url }}">
            {% if p.title %}
              {{ p.title }}
            {% else %}
              {% comment %} Front Matterにtitleがない場合はファイル名から生成 {% endcomment %}
              {{ p.name | replace: ".md", "" }}
            {% endif %}
          </a>
        </li>
      {% endunless %}
    {% endif %}
  {% endfor %}
</ul>
