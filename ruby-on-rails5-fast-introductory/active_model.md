# ActiveModel の使い方

ActiveRecord の便利な機能を一般クラスに持ち込む

- ActiveModel::Attributes  
  `attribute`メソッドで、`attribute_accessor`と同じようにフィールドを生やせる。
  型の定義もでき、代入時に暗黙的な型変換を実現できる。

- **ActiveModel::AttributeAssignment**  
  ハッシュを渡すことで`attr_accessor`などで定義した属性にまとめて値を代入できる`assign_attributes`メソッドを定義する。また、属性をハッシュに抽出したり、属性名だけ取り出したりもできる。

- **ActiveModel::Conversion**  
  ActiveRecord と同じように、モデルから URL パラメータの作成やパーシャルのパス解決ができるようになる

- ActiveModel::Callbacks  
  ActiveRecord と同じコールバックを使用できる。`ActiveRecord::Validators::Callback`をインクルードすることで`validate`系のコールバックがしようできるようになる。

- ActiveModel::Dirty  
  属性を変更した場合に、`changed?`などのメソッドが使用可能になる。

- **ActiveModel::Naming**  
  Routing や i18n など、命名規約に基づいた類推に使用される、model_name クラスメソッドを定義する

- ActiveModel::Serializers::JSON
  `to_json`、`from_json`メソッドを実装します。`attributes`、`attributes=`メソッドが必要。

- **ActiveModel::Translation**
  `human_attribute_name`などのメソッドがつかるようになる。フォームヘルパなどが属性名を i18n を使って変換してくれる。

- **ActiveModel::Validations**
  バリデーションを可能にし、`errors`メソッドでエラーを扱うこともできるようになります。

外部サービスのロジックを１クラスに閉じ込めたり、複数のモデルが絡む場合の処理をするモデルを作れたりする。
