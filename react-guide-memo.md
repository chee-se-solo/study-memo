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
```
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```

# 要素のレンダー
## React は必要な個所のみを更新する

# コンポーネントとprops
## 関数コンポーネントとクラスコンポーネント
```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
