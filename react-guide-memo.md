# JSXの導入
```js
const element = <h1>Hello, world!</h1>
```
```js
const name = 'Josh Prez';
const element = <h1>Hello, {name}</h1>

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

## JSXはインジェクション攻撃を防ぐ
```js
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```

# 要素のレンダー
## React は必要な個所のみを更新する

# コンポーネントとprops
## 関数コンポーネントとクラスコンポーネント
```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
```js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## コンポーネントのレンダー
```js
funciton Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDom.render(
  element,
  document.getElementById('root')
);
```
コンポーネント名は常に大文字で始めてください。  
Reactは小文字で始まるコンポーネントをDOMタグとして扱います。

## コンポーネントを組み合わせる
```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

## コンポーネントの抽出
```js
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}

function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}

function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

## propsは読み取り専用
すべてのReactコンポーネントは、自己のpropsに対して純関数のようにふるまわねばなりません。

# stateとライフサイクル
```js
ReactDom.render(
  <Clock />,
  document.getElementById('roor')
);h
```

## 関数をクラスに変換する
```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

## クラスにライフサイクルメソッドを追加する
```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
  
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
  
  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDom.render(
  <Clock />,
  document.getElementById('roor')
);
```
1. `ReactDOM.render()`で、`Clock`コンポーネントのコンストラクタ呼び出し。`this.state`を初期化
1. `Clock`コンポーネントの`render()`呼び出し
1. `Clock`の出力がDOMに挿入される。`componentDidMount`呼び出し。`tick()`メソッドでタイマー設定
1. 毎秒`tick()`メソッドが呼び出される。`setState()`メソッドの呼び出しで、stateの更新が通知され、`render()`メソッドが再度呼び出される
1. `Clock`コンポーネントがDOMから削除されると、`componentWillUnmount()`が呼び出され、タイマーが停止する

## stateを正しく使用する
- stateを直接変更しないこと
- stateの更新は非同期に行われる可能性がある  
this.propsとthis.stateは非同期に更新されるため、次のstateを求める際に依存するべきではない。
これを回避するには`setState()`の二つ目の形を使用すればよい。
```js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment
});

// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```
- stateの更新はマージされる
```js
constructor(props) {
  super(props);
  this.state = {
    posts: [],
    comments: []
  };
}

componentDidMount() {
  fetchPosts().then(response => {
    this.setState({
      posts: response.posts
    });
  });
  
  fetchComments().then(response => {
    this.setState({
      comments: response.comments
    });
  });
}
```
state内の値はそれぞれ独立して更新できる。
マージは浅く(shallow)行われるので、`this.setState({comments})`は、`posts`をそのまま残すが、`comments`を完全に置き換える。

## データは下方向に伝わる
親コンポーネントであれ子コンポーネントであれ、特定のほかのコンポーネントがステートフルかステートレスか知ることはできないし、特定のコンポーネントの定義が関数型かクラス型かを気にするべきではない。
```js
<FormattedDate date={this.state.date} />
```
```js
function FormattedDate(props) {
  return <h2>It is {props.date.toLocateTimeString()}.</h2>;
}
```
`FormattedDate`コンポーネントはprops経由で`date`を受け取るが、それが`Clock`のstateから来たのか、`Clock`のpropsから来たのか、もしくは手書きされたものなのかは分からない。
データフローは一般的には”トップダウン”もしくは”単一方向”データフローと呼ばれる。stateは特定のコンポーネントが所有し、"下"にいるコンポーネントにのみ影響する。  
コンポーネントがステートフルかステートレスかは、コンポーネントの内部実装とみなされる。ステートレスなコンポーネントをステートフルなコンポーネントの中で使うことが可能であり、その逆も同様。

# イベント処理
```js
function ActionLink() {
  function handleCheck(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }
  
  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```
イベントハンドラとして文字列ではなく関数を渡す。`false`を返してもデフォルトの動作を抑止することができない。`preventDefault`を呼び出す必要がある。`e`は合成(synthetic)イベントであり、これらの合成イベントはW3Cの使用に則って定義されているので、ブラウザ間の互換性を心配する必要はない。ネイティブのイベントとまったく同様に動作するわけではない。

```js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true);
    
    // This binding is necessary to make 'this' work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleCheck() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }
  
  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('roor')
);
```
JSXのコールバックにおける`this`の意味に注意しなければならない。  
javascriptでは一般的にonClick={this.handleClick}のように`()`を末尾に付けずに何らかのメソッドを参照する場合、そのメソッドはバインドしておく必要があります。

`bind`の呼び出しを回避する記法が２つある。
実験的なパブリッククラスフィールド構文を使用する記法。
```js
class LogginButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log('this is:', this);
  }
  
  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```
コールバック内でアロー関数を使用する記法。
```js
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }
  
  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={() => this.handleClick()}>
        Click me
      </button>
    );
  }
}
```
この構文での問題は、`LoggingButton`がレンダーされるたびに異なるコールバック関数が毎回作成されることだ。大抵のケースでは問題にならないが、このコールバックがpropsの一部として下層のコンポーネントに渡される場合、それら下層コンポーネントが余分に再描画されることになる。

## イベントハンドラに引数を渡す
```js
<button onClick={(e) => this.deleteRow(id, e)}> Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```
上記の2行は透過であり、上側ではアロー関数が、下側ではFuntion.prototype.bindが使われている。
どちらの場合でも、第一引数がid, 第二引数がReactイベントになる。アロー関数ではeを明示的に渡す必要がありますが、bindの場合にはid以降の追加の引数は自動的に転送されます。
