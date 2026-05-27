# React Hooks

Hooks funcionam como **closures** no React. São funções que controlam o gerenciamento de estados e o ciclo de vida de um componente. Existem hooks prontos através da API que o React fornece, mas também podemos criar **hooks customizáveis** para encapsular lógica reutilizável entre diversos componentes.

> ⚠️ A quantidade de vezes que um hook é chamado **nunca pode mudar** entre renders.

---

## Ciclo de vida

Todo componente passa por 3 momentos:

```
Montagem → Atualização → Desmontagem
(mount)     (update)      (unmount)
```

---

## Ordem de execução em cada render

```
1. Corpo do componente roda
   └── todos os hooks são registrados
   └── useState, useRef, useMemo, useCallback → resolvidos aqui mesmo
   └── useEffect e useLayoutEffect → apenas registrados, executam depois

2. React atualiza o DOM (virtual → real)

3. useLayoutEffect executa

4. Browser pinta a tela

5. useEffect executa
```

---

## Mapa do ciclo de vida completo

```
MONTAGEM
  └── corpo do componente
  └── useLayoutEffect
  └── useEffect

ATUALIZAÇÃO (setState / props mudaram)
  └── corpo do componente roda de novo
  └── cleanup do useLayoutEffect anterior
  └── useLayoutEffect
  └── cleanup do useEffect anterior
  └── useEffect

DESMONTAGEM
  └── cleanup do useLayoutEffect
  └── cleanup do useEffect
```

---

## Hooks prontos

### useState
Mapeia uma variável local do componente. Sempre que o valor for alterado, o React re-renderiza o componente.

```js
const [count, setCount] = useState(0);
```

---

### useEffect
Efeito colateral. Recebe uma função e um array de dependências. Muito usado para fetch de dados, subscriptions e timers.

```js
useEffect(fn)           // roda em todo render
useEffect(fn, [])       // roda só na montagem
useEffect(fn, [valor])  // roda na montagem + quando valor mudar
```

A **função de retorno (cleanup)** é executada na desmontagem ou antes do próximo efeito rodar:

```js
useEffect(() => {
  // executa após o render

  return () => {
    // cleanup: executa na desmontagem ou antes do próximo efeito
  };
}, []);
```

---

### useRef
Parecido com o `useState`, porém **não dispara re-render** quando seu valor é alterado. Cria uma "caixa" que guarda um valor mutável na propriedade `.current`, que persiste entre re-renders.

É a forma idiomática do React de acessar o DOM diretamente. Muito útil para formulários básicos e para criar custom hooks como `usePrevious`.

```js
const inputRef = useRef(null);
// inputRef.current aponta para o elemento do DOM
```

---

### useReducer
Alternativa ao `useState` para estados mais complexos. Recebe um reducer e um estado inicial, e retorna o estado atual e uma função `dispatch` para disparar ações.

É preferível ao `useState` quando:
- O próximo estado depende do estado anterior de forma complexa
- Várias partes do estado mudam juntas

```js
const [state, dispatch] = useReducer(reducer, initialState);
dispatch({ type: 'increment', payload: { value: 1 } });
```

---

### useContext
Permite compartilhar dados entre componentes sem precisar passar props manualmente. Elimina o **prop drilling**, permitindo que qualquer componente — mesmo que esteja bem abaixo na árvore — consuma um valor.

> ⚠️ Todo componente que consome um context re-renderiza quando o valor do context muda, mesmo que não use a parte que mudou.

Muito usado para: tema, idioma, usuário autenticado.

---

### useMemo
Hook de otimização. Guarda o **resultado** de uma função em cache e só recalcula quando uma das dependências mudar.

Obrigatório usar com **função pura**. Só usar quando houver problemas de performance.

```js
const valorCalculado = useMemo(() => calcularAlgo(a, b), [a, b]);
```

---

### useCallback
Hook de otimização. Muito parecido com o `useMemo`, mas enquanto o `useMemo` guarda o **retorno** da função, o `useCallback` guarda a **função em si**.

```js
const handleClick = useCallback(() => fazerAlgo(), [dep]);

// useCallback(fn, deps) é equivalente a:
// useMemo(() => fn, deps)
```

Antes de usar, se faça as 3 perguntas:
- Estou passando essa função como prop para um componente filho?
- Ela tem um custo computacional grande?
- Ela está sendo executada desnecessariamente?

---

### useLayoutEffect
Muito parecido com o `useEffect`, porém é executado **antes do navegador pintar a tela**. Útil quando você precisa medir ou modificar o DOM antes que o usuário veja — evita "flash" visual.

```
useEffect        → executa depois do browser pintar
useLayoutEffect  → executa antes do browser pintar
```

Muito usado em: modais, tooltips, animações que dependem de medição do DOM.