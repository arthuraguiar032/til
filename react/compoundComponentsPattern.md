# Compound Components Pattern

Ao construir um componente de `Tabs`, descobri que existe um padrão bem estabelecido na comunidade chamado **Compound Components**, no qual um componente pai e seus filhos funcionam juntos, cada um com uma responsabilidade.

---

## A estrutura

```jsx
<Tabs>
  <Tab name="Sobre">conteúdo 1</Tab>
  <Tab name="Projetos">conteúdo 2</Tab>
  <Tab name="Contato">conteúdo 3</Tab>
</Tabs>
```

---

## Componente burro vs componente inteligente

O `Tab` é um **componente burro**, não tem lógica própria. Só serve como envelope para carregar dados:

```jsx
const Tab = ({ children, name }) => {
  return <div>{children}</div>;
};
```

O `name` não é usado dentro do `Tab`. Ele existe como uma 'chave' para o pai ler, o `Tab` não precisa saber que tem um `name`.

O `Tabs` é o **componente inteligente**, ele lê o `name` e o `children` de cada `Tab` via `tab.props` e decide o que mostrar:

```jsx
const Tabs = ({ children }) => {
  const [activeTab, setActiveTab] = useState(0);
  const tabs = Children.toArray(children);

  return (
    <div>
      <nav>
        <ul>
          {tabs.map((tab, index) => (
            <li key={index}>
              <button onClick={() => setActiveTab(index)}>
                {tab.props.name}
              </button>
            </li>
          ))}
        </ul>
      </nav>
      <hr />
      {tabs[activeTab].props.children}
    </div>
  );
};
```

---

## O que cada um faz

| Componente | Responsabilidade |
|---|---|
| `Tab` | Carregar `name` e `children` como dados |
| `Tabs` | Gerenciar estado, renderizar navegação e conteúdo ativo |

---

## Observação

Este padrão usa a API `React.Children`, que a documentação oficial marca como **legada**. Apesar de o React recomendar alternativas eu preferi usar Children pela legibilidade do código. Cheguei a olhar algumas dessas alternativas como render props, mas não vi como uma opção com boa expressividade. E como Compound Components é uma solução idiomática do React, preferi seguir com essa abordagem.
