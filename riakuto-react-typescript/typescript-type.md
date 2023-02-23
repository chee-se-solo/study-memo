# TypeScript の型 (4 章)

## TypeScript の特徴

- 静的型付け
- 型推論
- Null 安全性

## 型アノテーション

```ts
let n: number = 3;
n = "foo"; // compile error


// 暗黙の変換は行われないが、＋を使った文字列結合は例外

let x: number = 1;
let y: string = "a";

x + y; // '1a'
x - y; // error
x * y; // error
x / y; // error
。
```

## プリミティブ型

- Boolean
- Number
- BigInt
- String
- Symbol
- Null
- Undefined

javascript と同じ。リテラルで表現され、メソッドを持たない型。ただし、String 型などはボクシングされるので、意識されにくい。

## 共通のデータ型

### 配列

```ts
const strArray1: string[] = ["a", "b", "c"];
const strArray2: Array<string> = ["a", "b", "c"];
```

TypeScript はジェネリクスがある。Java とだいたい同じ。

### object

TypeScript の object 型はハッシュと考えて間違いない。  
ただし、JS と同じなので、prototype でもありクラスでもあり関数でもある。

```ts
const red: { rbg: string; opacity: number } = { rgb: "ff0000", opacity: 1 };
```

## interface

object 型を定義する。

```ts
// 読み込み専用と省略可能プロパティの定義
interface Color {
  readonly rgb: string;
  opacity: number;
  alias?: string;
}

const tq: Color = { rgb: "00afcc", opacity: 1 };
tq.alias = "turquiise";
tq.rgb = "03c1ff"; // error
```

### インデックスシグネチャ

```ts
interface Status {
  [parameter: String]: number;
}

const myStatus: Status = {
  level: 22,
  experience: 3058,
  maxHP: 156,
  attack: 39,
  defense: 25,
};
```

`[keyname: keytype]`という記法で型を縛れるが、すべてのプロパティがこれに従う必要がある。

## Enum 型

enum は実態が数値なので安全でない。

```ts
enum Pet {
  Cat,
  Dog,
  Rabbit,
}

[Pet.Cat, Pet.Dog, Pet.Rabbit]; // [0, 1, 2]

let Tom = Pet.Dog;
Tom = 12; // not error
```

ただし、TypeScript2.4 から文字列 enum が使える。
こちらは型安全。

```ts
enum Pet {
  Cat = "cat",
  Dog = "dog",
  Rabbit = "rabbit",
}

[Pet.Cat, Pet.Dog, Pet.Rabbit]; // ["cat", "dog", "rabbit"]

Tom = Pet.Cat;
Tom = "mouse"; // error
Tom = "dog"; // error
Tom = Pet.Dog; // ok
```

## リテラル型

enum の代替として使える。
ユニオン型と合わせて使える。

```ts
let Tom: "cat" = "cat";
Tom = "mouse"; // error

let Mary: "cat" | "dog" | "rabbit" = "cat";
Mary = "rabbit";
Mary = "parrot"; // error
```

リテラル型のほうがコンパイル後のサイズが小さいので人気。

## タプル型

配列の順番と数を制限する

```ts
const arr: [number, string, boolean] = [1, "patty", true];
const spells: [number, ...string[]] = [7, "heal", "sizz", "sooz"];
```

## any, unknown, never

any はコンパイル時にエラーを検出できず、ランタイムエラーを起こす可能性があるので危険。

```ts
let val: any = 100;
val = "buz";
val = null;

const obj = `{ "id": 100, "name": "John" }`;
const user: any = JSON.parse(obj);
user.id; // ok
user.address.zipCode; // runtime error
```

unkown を使って回避できる。unknown はプロパティを持たない型。

```ts
let val: unknown = 100;
val = "buz";
val = null;

const obj = `{ "id": 100, "name": "John" }`;
const user: unknown = JSON.parse(obj);
user.id; // error
user.address.zipCode; // error
```

never は何も代入できない型。

```ts
const greet = (friend: "Serval" | "Caracal" | "Cheetah") => {
  switch (friend) {
    case "Serval":
      return `Hello, ${friend}!`;
    case "Caracal":
      return `Hi, ${friend}!`;
    case "Cheetah":
      return `Hiya, ${friend}!`;
    default: {
      const check: never = friend;
    }
  }
};
console.log(greet("Serval")); // Hello, Serval!
```

default 節の never によって case の静的解析で検出できる。  
他には値を返さない関数（常に例外を投げる、無限ループで処理が終わらない）の返り値型に使ったりする。

## 関数の型定義

`noImplicitAny :true`を`tsconfig.json`に記述することで、引数の型定義を暗黙的に`any`にすることを抑止できる。
引数と返り値の方を定義できる。

```ts
// 関数宣言
{
  function add(n: number, m: number): number {
    return n + m;
  }
}

// 関数式
{
  const add = function (n: number, m: number): number {
    return n + m;
  };
}
// アロー関数式
const add = (n: number, m: number): number => n + m;
```

インターフェースで定義することもできる。（呼び出し可能オブジェクトとして定義）

```ts
interface NumOp {
  (n: number, m: number): number;
}

{
  const add: NumOp = function (n, m) {
    return n + m;
  };
}

{
  const add: NumOp = (n, m) => n + m;
}

// インラインで定義もできる
{
  const add: { (n: number, m: number): number } => number = function (n, m) {
    return n + m;
  };
}

// インラインでアロー構文でインターフェースを定義
{
  const add: (n: number, m: number) => number = function (n, m) {
    return n + m;
  };
}

{
  const add: (n: number, m: number) => number = (n, m) => n + m;
}
```

これはインラインではやらない方がいい。

### ジェネリクス

```ts
function toArray<T>(arg1: T, arg2: T) {
  return [arg1, arg2];
}

toArray(8, 3); // [8, 3]
toArray("foo", "bar"); // ['foo', 'bar']
toArray(8, "bar"); // error
toArray<any>(8, "bar"); // no error [8, "bar"]
```

可変長にもできる

```ts
const toArray = <T>(...args: T[]): T[] => [...args];
toArray(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

インターフェースの定義にも使え、デフォルト値の設定もできる

```ts
interface CustomError<E = Error> {
  type: "syntac" | "logic" | "runtime";
  error: E;
}

const tokenError: CustomError = {
  type: "tyntax",
  error: new Error("Unexpected Token"),
};
```

## クラス

```ts
class Rectangle {
  readonly name = "rectangle";
  sideA: number;
  sideB: number;

  constructor(sideA: number, sideB: number) {
    this.sideA = sideA;
    this.sideB = sideB;
  }

  getArea = () => this.sideA * this.sideB;
}
```

JS では、コンストラクタ内でプロパティを記述することで、そのプロパティが生成される。  
TS では、クラス定義の頭で最初に宣言する必要がある。

また、プロパティに`private`、`protected`、`public`のアクセス修飾子をつけることもできる。（宣言なしは`public`）

**また、JS ではプロパティ名に#をつけることでプライベートにできる**  
**TS3.8 以降では同じ方法で private にできるが、コンパイル時の JS の`target`バージョンで振る舞いが変わる**

- ES5 以前：コンパイルエラーになる
- ES2015 ～ ES2021：互換コードにコンパイルされる
- ES2022：そのまま残される

**一方、アクセス修飾子`private`は TS 特有の記法のため、必ず互換コードにコンパイルされる**

振る舞いが違うため、以下のように呼び分ける。

- アクセス修飾子を使う方法は**ソフトプライベート**と呼ばれる（コンパイル時のみプライベート制限がかかる）
- #を使う方法は**ハードプライベート**と呼ばれる（実行時も制限が維持される）

### 継承と合成

継承は小クラスが親クラスに密に依存して変更の影響が大きいため、近年は避けられる傾向がある。

```ts
// 継承
class Square extends Rectangle {
  readonly name = "square";
  side: number;

  constructor(side: number) {
    super(side, side);
  }
}

// 合成
class Square {
  readonly name = "square";
  side: number;

  constructor(side: number) {
    this.side = side;
  }

  getArea = () => new Rectangle(this.side, this.side).getArea();
}
```

1. 継承の場合、暗黙でプロパティ`sideA`と`sideB`を継承してしまっている。
2. また、名前空間を親と子で完全に共有しているので責任の境界線が曖昧になったり、バグの原因の追跡が難しくなったりする。
3. さらに、親クラスの`Rectangle`から`readonly`を削除しないとコンパイルが通らない。

合成の場合、親クラスから受ける影響は`getArea`メソッドのみで、最小限に抑えられる。

Go や Rust には継承が存在せず、React でも継承を避けるように公式ドキュメントに書かれている。

> Facebook では、何千というコンポーネントで React を使用していますが、コンポーネントの継承
> による階層構造を作ることが推奨されるケースはひとつもありませんでした。  
> props と composition（合成）は、コンポーネントの見た目と振る舞いを明示的かつ安全にカスタ
> マイズするのに必要十分な柔軟性を備えています。

近代的なプログラミングでは継承は避けられる傾向にある。

### 抽象クラスとインターフェースとしてのクラス

`abstract`を使用することで抽象クラスを定義できる。が、継承は好まれないので使われることは少ない。  
代わりにインターフェースを使う。実装を継承しないで済むので使いやすい。

```ts
interface Shape {
  readonly name: string;
  getArea: () => number; // オーバーロードできない
  // getArea(): number; オーバーロードできる
}

interface Quadrangle {
  sideA: number;
  sideB?: number;
  sideC?: number;
  sideD?: number;
}

class Rectangle implements Shape, Quadrangle {
  readonly name = "rectangle";
  sideA: number;
  sideB: number;

  constructor(sideA: number, sideB: number) {
    this.sideA = sideA;
    this.sideB = sideB;
  }

  getArea = () => this.sideA * this.sideB;
}
```

Java と同じように`implements`でインターフェースを実装できる。

```ts
class Point {
  x: number = 0;
  y: number = 0;
}

const PointA = new Point();
const PointB: Point = { x: 2, y: 4 };

// クラスをインターフェースに継承
interface Point3d extends Point {
  z: number = 0;
}

const PointC: Point3d = { x: 5, y: 5, z: 10 };
```

TypeScript のクラス定義は、インターフェースの宣言とコンストラクタ関数の宣言が同時に実行されている。そのため、クラスをインターフェースとして継承させることができる。

## 型エイリアス

`type` で型に別名（エイリアス）を与えることができる。

```ts
type Unit = "USD" | "EUR" | "JPY" | "GBP";

type TCurrency = {
  unit: Unit;
  amount: number;
};

interface ICurrency {
  unit: Unit;
  amount: number;
}

const priceA: TCurrency = { unit: "JPY", amount: 1000 };
const priceB: ICurrency = { unit: "USD", amount: 10 };
```

interface は実態と名前が結びついているためにエラーに名前が表示されるため、エラーを追いやすく、パフォーマンスも良い。  
type は手軽に定義できるが、interface のような利点はない。  
そのため、interface を勧められることが多いが、interface は再宣言的に拡張可能な点に注意が必要。

```ts
interface User {
  name: string;
}

// User インターフェースを拡張
interface User {
  age: number;
}

// User インターフェースをさらに拡張
interface User {
  species: "rabbit" | "bear" | "fox" | "dog";
}

// 3つのプロパティを持つインターフェースが完成
const rolley: User = {
  name: "Rolley Cocker",
  age: 8,
  species: "dog",
};
```

個人的には終わってる仕様だと思うけど、公式ではエラー時の追跡可能性からどちらかといえば interface をおすすめしている。Google や Microsoft の Style Guide ではパフォーマンスから interface を推奨している。  
拡張性より厳密性が重視されるシーンでのみ型エイリアスを使うという使い分け方が多いらしい。

## ユニオン型とインターセクション型

ユニオン型で複合的な型にすることができる。

```ts
let id: number | string = 16384;
id = "one";

interface A {
  foo: number;
  bar?: string;
}
interface B {
  foo: string;
}
interface C {
  bar: string;
}
interface D {
  baz: string;
}

type AB = A | B; // { foo: number; bar?: string } or { foo: string }
type AC = A | C; // { foo: number; bar?: string } or { bar: string }
type AD = A | D; // { foo: number; bar?: string } or { baz: string }
```

インターセクション型で型の合成ができる

```ts
interface T {
  foo: number;
}
interface U {
  bar: string;
}
interface V {
  foo?: number;
  baz: boolean;
}

type TnU = T & U; // { foo: number, bar: string }
type TnV = T & V; // { foo: number, baz: boolean }
type TnUorV = T & (U | V); // { foo: number, bar: string } or { foo: number, baz: boolean }
```

インターセクション型を使えば、型エイリアスでもインターフェースのような拡張ができる。

```ts
type Unit = "USD" | "EUR" | "JPY" | "GBP";

interface Currency {
  unit: Unit;
  amount: integer;
}

interface IPayment extends Currency {
  date: Date;
}

type TPayment = Currency & {
  date: Date;
};

const date = new Date("2022-09-01T12:00+0900");
const payA: IPayment = { unit: "JPY", amount: 10000, date };
const payB: TPayment = { unit: "USD", amount: 100, date };
```

ただし、インターフェースの場合、同名のプロパティが交差するとエラーになる。

## Null 安全性の保証

`tsconfig.json`に`strictNullChecks:true`を設定することで、明示的に Null を許可していない変数に Null が混入することを防止できる。  
許可する場合はユニオン型で明示する。

```ts
const foo: string = null; // error
const bar: number = undefined; // error

let baz: string | null = "baz";
baz = null; // ok
```

null の可能性がある参照はコンパイル時にエラーになるので Null 安全になる。非 Null アサーション演算子を使うことで無理やりコンパイルを通すこともできるが、通常は Null アクセスを回避する処理を書く。

```ts
interface Resident {
  familyName: string;
  lastName: string;
  mom?: Resident;
}

const getMomName = (resident: Resident): string => resident.mom.lastName; // error
// const getMomName = (resident: Resident): string => resident.mom!.lastName; // not error
```

## 高度な型表現
