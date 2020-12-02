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
上記の2行は等価であり、上側ではアロー関数が、下側ではFuntion.prototype.bindが使われている。
どちらの場合でも、第一引数がid, 第二引数がReactイベントになる。アロー関数ではeを明示的に渡す必要があるが、bindの場合にはid以降の追加の引数は自動的に転送される。













