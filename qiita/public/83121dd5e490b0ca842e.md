---
title: useCallbackを正しく扱う
tags:
  - hooks
  - React
  - useCallback
private: false
updated_at: '2025-10-19T20:11:14+09:00'
id: 83121dd5e490b0ca842e
organization_url_name: null
slide: false
ignorePublish: false
---

React の `useCallback` は「関数の参照を固定する」ためのフックですが、闇雲に使うと逆に複雑さを招きます。この記事では、基本的な振る舞いと利用すべき場面、そしてメモ化そのものを減らすための考え方を整理します。

## useCallback とは

`useCallback` はコンポーネントが再レンダーされたときでも同じ関数インスタンスを再利用できるようにするフックです。依存配列の値が変わらない限り、以前と同じ関数オブジェクトを返します。

```jsx
const memoizedFn = useCallback(fn, dependencies);
```

- `fn`: メモ化したい関数
- `dependencies`: 依存値の配列。ここに含まれる値が変化したときだけ新しい関数が返る。（関数内の動的な値をすべて含める。）

```jsx
const greet = useCallback((name) => {
  console.log(`Hello, ${name}!`);
}, []);
```

上の例では依存配列が空のため、コンポーネントが何度再レンダーされても `greet` は同じ関数参照を保ち続けます。

## 使うべきタイミング

`useCallback`の目的はあくまで「不要な再レンダーや副作用を避ける」ことで、レンダーのパフォーマンスを最適化することです。そのため、効果的なのは次の 2 パターンと言われています。

1. `React.memo` でメモ化された子コンポーネントに関数を渡すとき
2. `useEffect` や `useMemo` などの依存配列に関数を含める必要があるとき

### 1. メモ化された子への props

`React.memo` でラップされた子コンポーネントは、props が変わらなければ再レンダーされません。ここに毎回新しい関数を渡すと比較に失敗してしまうため、`useCallback` で関数を安定化させます。

```jsx
const Child = React.memo(({ onClick }) => {
  console.log("Child rendered");
  return <button onClick={onClick}>Click</button>;
});

function Parent() {
  const [count, setCount] = useState(0);

  // onClick の参照を固定する
  const handleClick = useCallback(() => {
    setCount((prev) => prev + 1);
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <Child onClick={handleClick} />
    </div>
  );
}
```

### 2. 依存配列に入れる必要がある関数

`useEffect` や `useMemo` の依存配列に関数を含めるとき、その関数が毎回新しく生成されると依存が常に変わったと判断され、無限ループになる危険があります。`useCallback` でメモ化すれば、依存値が変化しない限り同じ参照を保てます。

```jsx
function Fetcher({ url }) {
  const [data, setData] = useState(null);

  // fetchData の参照を固定して無限ループを防ぐ
  const fetchData = useCallback(async () => {
    const res = await fetch(url);
    const json = await res.json();
    setData(json);
  }, [url]);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

### カスタムフックで返す関数

カスタムフックから関数を返す場合、利用側で 1 と 2 の状況に当てはまることが多くなります。必要に応じて `useCallback` でメモ化しておくと、安全なデフォルトになります。

```jsx
function useToggle(initial = false) {
  const [value, setValue] = useState(initial);

  const toggle = useCallback(() => {
    setValue((prev) => !prev);
  }, []);

  return { value, toggle };
}
```

## メモ化自体を減らす考え方

`useCallback` に頼らなくても済むように、以下のような設計を意識すると効果的です。

1. **子を JSX として渡す**: 関数ではなく要素の子として受け取るようにすると、props の比較がシンプルになります。
2. **state を局所化する**: state を上位に持ち上げすぎると、不要な範囲まで再レンダーが波及します。できるだけ近いコンポーネントに置きましょう。
3. **レンダーを純粋に保つ**: 同じ props で常に同じ UI を返すようにし、関数生成など副作用的な処理を避けると比較が容易になります。
4. **不必要な副作用を避ける**: `useEffect` 内で毎回 state を更新するパターンはレンダーを連鎖させがちです。副作用の発火条件を見直しましょう。
5. **依存配列を最小限にする**: 依存に関数を入れざるを得ない場合でも、不要な値を取り除けばリレンダーを抑えられます。

## まとめ

- `useCallback` は関数参照を安定させて再レンダーや副作用を抑えるためのフック
- `React.memo` と組み合わせるとき、依存配列に関数を入れるときに特に効果的
- カスタムフックでも必要に応じてメモ化しておくと安全
- 設計を見直してメモ化そのものを減らすことも重要

## 参考資料

- https://ja.react.dev/reference/react/useCallback
- https://www.youtube.com/watch?v=lGEMwh32soc
