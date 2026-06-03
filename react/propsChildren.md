# Componentes Aninhados e props.children

Quando precisamos de um componente formado por outros componentes filhos — como um `Tabs` que contém vários `Tab` — o React oferece a prop especial **`children`** e a API **`React.Children`** para lidar com esse padrão.

```jsx
<Tabs>
  <Tab name="Sobre">conteúdo 1</Tab>
  <Tab name="Projetos">conteúdo 2</Tab>
  <Tab name="Contato">conteúdo 3</Tab>
</Tabs>
```

Tudo que está entre as tags de um componente fica disponível automaticamente em `props.children` dentro dele.

---

## React.Children API

> Referência oficial: https://react.dev/reference/react/Children

### Children.count(children)
Retorna a quantidade de nós filhos do componente. Conta qualquer tipo de nó: strings, tags HTML ou outros componentes.

```js
Children.count(children)
```

---

### Children.forEach(children, fn)
Executa uma função para cada nó filho, sem retornar nada. Útil para efeitos colaterais.

```js
Children.forEach(children, (child) => {
  console.log(child);
});
```

---

### Children.map(children, fn)
Transforma os nós filhos, retornando um novo array. Funciona como o `.map()` de arrays.

```js
const clones = Children.map(children, (child) => {
  return cloneElement(child, { isActive: true });
});
```

---

### Children.only(children)
Garante que `children` é exatamente **um** elemento React. Lança um erro se houver zero ou mais de um filho.

```js
Children.only(children) // erro se não for exatamente 1 filho
```

---

### Children.toArray(children)
Converte `children` em um array JavaScript comum, permitindo usar `.filter()`, `.sort()` e outros métodos nativos.

```js
const tabs = Children.toArray(children);
const names = tabs.map((tab) => tab.props.name);
```

---

## Observação importante

A documentação do React marca a API `React.Children` como **legada**. Para novos projetos, recomenda-se usar padrões alternativos como **render props** ou **passagem explícita de arrays como prop**.
