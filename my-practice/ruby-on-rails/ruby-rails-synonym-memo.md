# Ruby / Rails シノニム

Ruby / Rails で気の利いた書き方を忘れたので列挙していく。
自分用なので忘れていないものは書かない。具体的に言うと`||=`みたいな js でもできる書き方とか。

## %記法

```ruby
%i(apple banana orange) # array of symbol
%w(good morning world) # array of string
%r(regexp) # regexp statement
```

文字列かシンボルの配列を一から作るときはとにかくこれを使う。
クォートやスラッシュをエスケープせずに記述できるので、そういう用途でも時々便利。
記法の部分を大文字にすると変数を展開して配列を作成する。

## proc

```ruby
1..100.map(&:to_s) # %w(1 2 3 ... 100)
```

`ruby1..100.map { |num| num.to_s }`と同じ。
`&`で `proc` を作れる。ruby はコードブロックを `proc` というオブジェクトとして扱う。

`proc` は以下のように明示的に作って代入して渡すこともできる。

```ruby
p = Proc.new { |num| num.to_s }
P1..100.map(&p)
```

`Proc`をそのまま引数に渡してもオブジェクトとして扱われる。コードブロックとして展開するために`&`をつける。配列を展開するために`*`を付けるのに似ている。 シンボルに`&`を付けてもエラーを吐かずに`proc`として動作する理由は、シンボルには`to_proc`メソッドが定義されているため。シンボルに`to_proc`して作ったコードブロックがどのようなコードになっているかは例に示した通り。

## \* (splat)

```ruby
def decoratte(str, deco)
  puts "#{deco*3} str #{deco*3}"
end

decoset = %w(hello, @)
decorate(*decoset) # @@@ hello @@@
```

見たまま。展開できる。

## try と &. の違い

```ruby
Class.name     # Class
Class&.name    # Class
Class.try :name    # Class

nil.name       # error
nil&.name      # nil
nil.try :name  # nil

10.name        # error
10&.name       # error
10.try :name   # nil
```

上の通り。`&.`も`try`も nil セーフな書き方だが、nil でないオブジェクトに使ったときに差異が出る。
さっくり言えば、`try`は絶対に例外を吐かないが`&.`は nil セーフだけどエラーを吐くこともある。

だんだん疲れてきたので以上。他にも思い出したら追記する。
Hash、String、Array、ActiveRecord あたりの便利メソッドはそれぞれ別ファイルに独立させる。
一枚に書くには長すぎる。
