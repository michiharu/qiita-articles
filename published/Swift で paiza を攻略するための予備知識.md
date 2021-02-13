- Swift
- 標準入出力
- 関数型プログラミング
- 文字列操作 

[paiza](https://paiza.jp/)のスキルチェックをはじめました。
スキルチェックは、いわゆる競技プログラミングの成績で就職活動をするためのサービスです。

とりあえずこれだけ知っていればSwiftでpaizaのスキルチェックがはじめられるだろうというものを紹介します。

# paizaのSwiftのバージョンについて
以下のURLに各言語のバージョンが記載されていますが、現時点（2019/05/06)ではswiftは3.0.1です。
ちょっと古いので最新のXCodeのPlaygroundなどでコードを試すならコピペする際エラーが出るかもしれませんね。

[各言語のバージョン、環境情報](https://paiza.jp/guide/language)

同じくpaizaのサービスである[paiza.io](https://paiza.io/ja)ならswift4.2で実行出来ます。
swift3.0.1でプログラミング出来る環境が簡単に手に入れば紹介したいのですが、とりあえず私はpaiza.ioで満足しています。

# 標準入出力
まずは標準入出力から紹介します。
以下はpaizaのスキルチェックでSwiftを選択すると最初から与えてもらえるコードです。

```
let input_line=readLine()! // 標準入力から１行目を取得
print(input_line)
```

「readLine()」は標準入力を１行ずつ読み込んでくれる関数です。２行読ませたい場合は以下のように書きます。

```
let input_line1 = readLine()! // 標準入力から１行目を取得
let input_line2 = readLine()! // 標準入力から2行目を取得
```

# よく使う文字列操作
標準入力から与えられる文字列から複数の値を配列で受け取る方法です。

```
let input_line = "1 3 5 7"
let strings = input_line.split(separator: " ")
print(strings) // => ["1", "3", "5", "7"]
```

# map, filter, reduce の書き方
関数型プログラミングに欠かせない関数もの使い方も紹介します。これらの関数はいろいろな書き方が出来るようですが、競技プログラミングで使いそうな書き方だけ紹介します。

## mapの書き方
### 例１：文字列→数値に変換
```
let input_line = "1 3 5 7"
let strings = input_line.split(separator: " ")
let numbers = strings.map({Int($0)!}) // $0から配列の一つ一つの要素にアクセス出来ます。
print(numbers) // => [1, 3, 5, 7]
```

### 例２：数値を２倍
```
let input_line = "1 3 5 7"
let strings = input_line.split(separator: " ")
let numbers = strings.map({Int($0)! * 2})
print(numbers) // => [2, 6, 10, 14]
```

## filterの書き方
### 例：3の倍数のみに絞り込む
```
let input_line = "1 2 3 4 5 6"
let strings = input_line.split(separator: " ")
let numbers = strings.map({Int($0)!})
let filteredNumbers = numbers.filter({ $0 % 3 == 0 })
print(filteredNumbers) // => [3, 6]
```

## reduceの書き方
第２引数に演算子を渡せるあたりがswiftらしいですね。
### 例：総和を求める
```
let input_line = "1 2 3 4 5 6"
let strings = input_line.split(separator: " ")
let numbers = strings.map({Int($0)!})
let number = numbers.reduce(0, +)
print(number) // => 21
```

# mapやfilterでインデックスを受け取る 
javascriptでmapやfilterを使う場合、配列の何番目か（インデックス）を第２引数で受け取れます。

```
const newArray = ["あ", "い", "う", "え", "お"]
                 .map((char, index) => `${index}: ${char}`);
console.log(newArray) // => 0: あ, 1: い, 2: う, 3: え, 4: お
```

swiftでインデックスを取得するには「.enumerated()」を使います。またmapやfilter、enumeratedは配列を返す関数なので、メソッドチェーンで繋いで書くことが出来ます。
\$0.0からインデックス、$0.1で配列の要素そのものにアクセス出来ます。

### mapでインデックスを使う例
文字列にインデックス情報を付け加える例

```
let input_line = "あ い う え お"
let strings = input_line.split(separator: " ")
let mappedStrings = strings
  .enumerated()
  .map({ String($0.0) + ": " + $0.1 })

print(mappedStrings) // => ["0: あ", "1: い", "2: う", "3: え", "4: お"]
```

### filterでインデックスを使う例
配列から最初と最後の要素を除外する例

```
let input_line = "あ い う え お"
let strings = input_line.split(separator: " ")
let filteredStrings = strings
  .enumerated()
  .filter({ $0.0 != 0 && $0.0 != strings.count - 1 })
  .map({$0.1})
print(filteredStrings) // => ["い", "う", "え"]
```

# 任意の配列を生成する
## mapのもう一つの書き方
任意の配列を生成する方法を紹介する前に、mapのもう一つの書き方を紹介しておきます。
以下の２つのコードは同じ結果になります。

```
let input_line = "1 3 5 7"
let strings = input_line.split(separator: " ")

// $0から配列の一つ一つの要素にアクセス
let numbers1 = strings.map({Int($0)!})
print(numbers1) // => [1, 3, 5, 7]

// 変数（char）を宣言して、その変数で一つ一つの要素にアクセス
let numbers2 = strings.map({char in Int(char)!}) 
print(numbers2) // => [1, 3, 5, 7]
```
変数を任意に宣言することでmap関数を入れ子で使用出来ます。


### 一次元配列の生成
配列を生成した後、map関数であれこれして問題を解くために必要なデータを作成します。

```
let input_line = "3"
let number = Int(input_line)!
let array = (0...(number - 1)).map({$0})
print(array) // => [0, 1, 2]
```

### 二次元配列の生成
以下のコードはタプルにx,yの値を持たせた二次元配列を生成しています。

```
let input_line = "2 3"
let input_numbers = input_line.split(separator: " ").map({Int($0)!})
let height = input_numbers[0]
let width  = input_numbers[1]
let table = (0...(height - 1))
    .map({y in
        (0...(width - 1)).map({x in (x: x, y: y)});
    })

print(table)
/**
 * [ 
 *     [(x: 0, y: 0), (x: 1, y: 0), (x: 2, y: 0)],
 *     [(x: 0, y: 1), (x: 1, y: 1), (x: 2, y: 1)]
 * ]
 */
```

# まとめ
以上です。競技プログラミングは文法が分かってやっとスタートライン。
あとはアルゴリズム力を存分に発揮して問題を解いて下さい。
