---
title: getとstreamって何が違う？
toc: true
tags:
  - huit
  - python
  - firebase
  - db
date: 2021-12-11 01:25:01
---

この記事は、[HUIT アドベントカレンダー 2021](https://qiita.com/advent-calendar/2021/huit) の10日目の記事です。

今回は初めての技術系記事です。色々と足りないところはあるかもしれませんが、どうか温かい目で見てください。改善点など大歓迎です！

この記事は**Firebase**から**Python**でデータを取得する際に用いる`get()`と`stream()`の違いについて考察したものです。

<!-- more -->

---

### そもそも**DB**とは

みなさんは普段どんなDBを使っているのでしょうか。**MySQL**、**PostgreSQL**、**MongoDB**など様々な種類がありますが、私はまだ**Firestore DB**しか用いたことがありません。DBの操作とかも興味があったりするのでこれから色々扱ってみたいですね。

念のためデータベースというものがよく分からないという方に説明すると、

> データベースは、アプリケーションのデータを保存・蓄積するためのひとつの手段です。大量のデータを蓄積しておいて、そこから必要な情報を抜き出したり、更新したりということが柔軟に行えるため、多くのデータを扱うアプリケーションでは欠かすことができません。
（出典：[キタミ式イラストIT塾 基本情報技術者 令和03年](https://www.amazon.co.jp/dp/B08PPJPZ4V/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)）

とのことです。確かに過去2回のハッカソンにおいてもデータベースがあると無いとでは出来ることが全然違うなーと思ってました。

---

### 事の経緯

[前回の記事](https://usk314.github.io/blog_static/2021/12/07/JPHacks2021%E3%81%AB%E5%8F%82%E5%8A%A0%E3%81%97%E3%81%BE%E3%81%97%E3%81%9F%EF%BC%81/)でも紹介しましたがつい先日**JPHacks**に参加し、私はバックエンドを担当していました。

<img src="https://i.imgur.com/IpSMKtY.png" width="300">
<br>
<br>

その際も**Firestore DB**を利用したのですが、DBからデータを持ってこようとしっかり[公式ドキュメント](https://firebase.google.com/docs/firestore/query-data/get-data)を参照し、言うとおりにコードを書いていたところ、事件が起きました。**stream型**では上手く行っていたはずのコードが**get型**では実行されなかったのです。以下、どのように実行しなかったのかを再現したコードを載せます。

・
・
・
・
・
・
・
・
・
・
・

**get型**でも出来てしまいました……

![](https://i.imgur.com/9G8z2WC.png)

「違う、君じゃない」

開発時はあれほど希望を与えてくれた200番台が、今では失望に変わってしまいました。「アドカレ当日になってまさかの没記事か……？」と背筋が凍りかけましたが、気にせずこのまま突っ走ろうと思います。

---

### 結局`get()`と`stream()`のどっちを使えばいいのか？

無事(?)**get型**でも実装はできましたが、やはりその過程には違いがあるはずだと踏んで果敢に攻め入ります。

公式ドキュメントに気になる点がありました。

![](https://i.imgur.com/lhNxiJc.png)

`Note: Use of CollectionRef stream() is prefered to get()`とありますが、これは他の言語には無い記述です。また、「複数」ではなく「単一」のドキュメントを取得する方法を示す箇所にはこのような記述はありませんでした。

というわけで、「**POST**メソッドで登録したmemberの情報を全て取得する」というコードを**get型**と**stream型**の2パターンで記述したものがこちらです。

![](https://i.imgur.com/xxGSoBZ.png)

![](https://i.imgur.com/npHYwbm.png)

違いは`db.collection("members")`につけたメソッドのみです。ともにコード内で**docs**を出力するようにしています。

以下、上のコードによって出力されたものです。

```
＊get型＊

docs : [
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA03BE50>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA03BE20>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277E9EF5130>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA03BC40>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA045550>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA0456A0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA045610>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA045040>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA0457C0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA045B20>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA045AC0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA045FA0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA045A90>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA045940>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA1644C0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA1640D0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA1645B0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA1645E0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA1643D0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA164670>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA164700>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA164790>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA164820>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA1648B0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA164940>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA1649D0>, 
<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x00000277EA164A60>]
```
```
＊stream型＊

docs : 
<generator object Query.stream at 0x00000277E9FEFC10>
```

せいぜい少しだけ内部の操作が異なるのかなあとか思ってましたがまさかここまで視覚に訴えかけてくるものだとは……

それでは早速私の稚拙な知識で分析を試みていきます。

---

### 分析フェーズ

- **get型**

    特徴的なのはやはり27行にわたる出力結果ですが、この27という数字は**Firestore**上のドキュメント（レコードと同じ）の数です。データを取得するためにあらかじめ投稿しておきました。**get型**はそのそれぞれについて**DocumentSnapshot**を作成し、配列型として**docs**に入れてくれているんですね。

    さて、**DocumentSnapshot**とは何なのでしょうか。

    大変分かりやすいサイトがありましたので紹介します。
    →[FirestoreのReferenceとSnapshotの大まかな理解（Tips）](https://zenn.dev/eitches/articles/2021-0130-firestore-reference-snapshot)
    こちらをちゃんと読んでもらえればきっと大まかに分かるんだと思います（私も今はさらっと読みましたが後ほどしっかりと読みます。新情報が入荷次第ここに更新していきたいですね）

    以下、大変ざっくりまとめた図です。
    ```
    Firebaseのオブジェクト群
    - Reference
      オブジェクトが存在する＊場所＊
        - DocumentReference
        - CollectionReference
    - Snapshot
      オブジェクトの＊データ＊
        - DocumentSnapshot
        - QuerySnapshot
        - QueryDocumentSnapshot
    ```
    なんとなく分かったのでOKです。

- **stream型**

    こちらは**get型**とは打って変わってシンプルに一行！

    まず**generator**とは何か。**generator**と大体セットで出てくるものに**iterator**がある、ということくらいなら私でもなんとなく分かっています。~~開発中何度も見なかったふりをしました~~

    **iterator**は繰り返し？**generator**は**iterator**を作成する関数？
    色々書いてありましたがサッと見ただけじゃよくわからなかったし、もう少しちゃんと調べてみたい気もしたので後日別記事として出すかもしれません。とりあえず今回は保留ということで:man-bowing:

    次に**Query.stream**について。これについてはあまりいい情報を得られませんでした。**stream**は「データの流れ」を意味するみたいなので何となく言ってることは分かるような気がしますが……

    ただ、ネットの散歩中にこんなことが書かれた記事を見つけました。
    > node.jsのStreamを使えばメモリを節約することができます。
    > 出典：[node-mysqlとStreamで大量のデータを効率的に処理](https://memo.appri.me/programming/node-mysql-stream)

    あくまで**node.js**について言及したものであること、そしてメモリの節約については特に説明がなされていなかった気がするので確証の無い情報ではありますが、もし仮にこれが正しく、かつ**Python**でも成り立つのであれば公式ドキュメントが「**get**よりも**stream**の方がいいよ！」と言っていたことの裏付けになるかもしれません。

    たしかに**get型**はドキュメントのそれぞれについて**DocumentSnapshot**を作成する仕様だったのでドキュメント数が増えれば増えるほど大変そうだなあとは思いました。

---

### 他に気になったこと

先ほど2パターンの記述方法でコードを記載しましたが、どちらもdocsという変数を設定した後にfor文で回して**doc**を取ってきています。実はこちらもそれぞれ**get型**と**stream型**の2パターンについて`print(doc)`したのですが、これが面白いことにほぼ一致したんですね。

```
＊get型＊

<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x0000019B9F641C10>
```
```
＊stream型＊

<google.cloud.firestore_v1.base_document.DocumentSnapshot object at 0x0000019B9F50F520>
```
異なったのは末尾から6桁の文字列のみで、それ以外は同じ形式でした。**docs**の形はあれだけ違うのにfor文で回した途端ほとんど同じものが出力されるってどういうことなんでしょうか。こちらについてはこの記事では検証せず、将来的な展望としておきます。

---

### まとめ

結論：おそらくいちいちドキュメントを**Snapshot**にする**get型**はメモリを浪費することになるため、**stream型**にした方が良いだろう

ということになりました。未検証な部分もいくつかありましたが、それについては追々調べていこうと思います。

技術系記事と打って出た割には雑なものに仕上がった気がしますが大目に見ていただければ幸いです。

---

明日のアドカレ記事はまたも未定です（3回目）
誰か書いてください！

終わり