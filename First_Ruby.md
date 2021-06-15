# Rubyの基礎

## 基礎知識メモ

* **定数名** は最初が大文字。慣例的に **全部大文字** にすることが多い。定数を書き換えた場合Warningが発生するがErrorにはならないので注意する。

* 使用可能なメソッドを確認するには、`インスタンス.methods`を使用する。

* 分数を扱うには、`Rational(2, 5)`または`2/5r`とかける。

* `upcase!`など、 **[!]** がついたメソッドは、**破壊的なメソッド**と呼ばれ、元のインスタンスを書き換える。

* 配列の範囲を超えた添え字を指定すると、`nil`という値を返す。エラーにはならないので注意！

* ""や''の略記法として、%Q,%q,%W,%wが存在している。

* クラス名は大文字から始める。

### ループ処理

* while

```Ruby
while i < 10 do
    # 何らかの処理
    i+=1
end

#または
while i < 10 {
    # 何らかの処理
    i+=1
}
```

* times:ループの回数を指定し、回数分|i|が自動でインクリメントされる。

```Ruby
10.times do |i|
    # 何らかの処理
end

#または
10.times { |i|
    # 何らかの処理
}
```

* for

```Ruby
for i in 0..10 do
    #何らかの処理
end

#または
for i in 0..10 {
    #何らかの処理
}
```

* each

```Ruby
0..10.each do |i|
    #何らかの処理
end

#または
0..10.each { |i|
    #何らかの処理
}
```

* loop:無限ループ

```Ruby
loop do
    #何らかの処理
end
```

## クラスの作成

```Ruby
class User

    #クラス変数の定義は、@@をつける
    @@count = 0

    #クラス定数も定義可能
    VERSION = 1.1

    attr_accessor :name #こう書くことで、自動でnameにたいしてメソッドを作成できる
    # setter: name=(value) <-----nameの値を変更するメソッド
    # getter: name <----nameの値を参照するメソッド
    #getterだけを定義したい場合は、[attr_reader :name]

    def initialize(name) #コンストラクタ
        @@count += 1
        @name = name  #クラス内の変数は、先頭に@をつけて定義する
    end

    def sayHi
        #selfはそのオブジェクト自身を指す
        puts "hi! i am #{self.name}"
        # selfを省略することも可能　puts "hi! i am #{name}"
        #puts "hi! i am #{@name}"
    end

    #クラスメソッドはself.[method]の形式で定義
    def self.info
        puts "#{VERSION}: User Class, #{@@count} instances."
    end
end

#クラスの外から参照する場合
User.info
p User::VERSION
```

## 基本的な関数

### 標準出力

* `print`:引数を改行なしで出力する。

* `puts`:引数を改行ありで出力する。

* `p`:引数のオブジェクトをわかりやすく出力する。デバッグ用。文字列には **""** がつく。

* `printf`:書式付きで文字列を出力できる。C言語の`printf`と同じ使い方ができるのかな？

### 標準入力

* `gets`:標準入力を文字列として取得する。

* `chomp`:取得した文字列の改行コードを削除する。

### 文字列

* 文字列の連結は`+`

* `upcase`:小文字を大文字に変換する。

* `downcase`:大文字を小文字に変換する。

### 配列

* `push`:配列の末尾に要素を追加する。

* `size`:要素の個数を返す。

* `sort`:要素を並び替える。

### ハッシュ:{key:value}

* `keys`:キーのリストを返す。

* `values`:値のリストを返す。

* `has_key?(key)`:指定したキーが存在すればtrue,存在しなければfalseを返す。

### オブジェクト変換

* `to_i`:整数(int?)型に変換

* `to_f`:浮動小数点数(float?)型に変換

* `to_s`:文字列に変換

* `to_a`:ハッシュを2次元配列に変換

* `to_h`:配列をハッシュに変換

