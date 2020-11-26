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
