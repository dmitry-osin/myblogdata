# Шпаргалка по styled-components с примерами

Styled-components — популярная библиотека для CSS-in-JS в React. Позволяет описывать стили компонент прямо в JS-коде с помощью **tagged template literals**, делает стили изолированными и более удобными для масштабирования.

## 1. Установка

```bash
npm install styled-components
```
или
```bash
yarn add styled-components
```


## 2. Основной синтаксис

```jsx
import styled from 'styled-components';

const Button = styled.button`
  background: palevioletred;
  color: white;
  font-size: 1em;
  border-radius: 5px;
  border: none;
  padding: 0.5em 1em;
`;

function App() {
  return <Button>Click me</Button>;
}
```
- Создаём компонент с помощью `styled.тег`.
- Внутри косых кавычек — обычный CSS.

## 3. Пропсы и динамические стили

Передавайте пропсы в компонент, чтобы динамически менять стили:

```jsx
const Button = styled.button`
  background: ${props => props.primary ? "palevioletred" : "white"};
  color: ${props => props.primary ? "white" : "palevioletred"};
`;

<Button primary>Primary</Button>
<Button>Default</Button>
```


## 4. Наследование и расширение стилей

Новый компонент может наследовать стили другого:

```jsx
const TomatoButton = styled(Button)`
  background: tomato;
  color: white;
`;
```


## 5. Вложенность, псевдоселекторы

Работают привычно, как в Sass:

```jsx
const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;

  &:hover {
    background: peachpuff;
  }

  h1 {
    color: palevioletred;
  }
`;
```


## 6. Глобальные стили

Для глобальных (reset, базовые темы):

```jsx
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  body {
    background: #eee;
    margin: 0;
    font-family: 'Roboto', sans-serif;
  }
`;

function App() {
  return (
    <>
      <GlobalStyle />
      <MainApp />
    </>
  );
}
```


## 7. Темизация: ThemeProvider

Создайте объект темы и используйте провайдер:

```jsx
import { ThemeProvider } from 'styled-components';

const theme = {
  primary: "#6200ee",
};

const Button = styled.button`
  background: ${props => props.theme.primary};
`;

function App() {
  return (
    <ThemeProvider theme={theme}>
      <Button>Theme button</Button>
    </ThemeProvider>
  );
}
```


## 8. Стилизация собственных компонентов

Можно передать свой компонент и обернуть его:

```jsx
function Custom({ className }) {
  return <div className={className}>Custom content</div>;
}

const StyledCustom = styled(Custom)`
  color: orangered;
  font-weight: bold;
`;
```


## 9. Анимации

Можно использовать keyframes:

```jsx
import styled, { keyframes } from 'styled-components';

const rotate = keyframes`
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
`;

const Rotating = styled.div`
  animation: ${rotate} 2s linear infinite;
`;
```


## 10. Лучшие практики

- Структурируйте компоненты по принципу “один компонент — один стиль”.
- Используйте **props** для модификаций, **ThemeProvider** для тем.
- Не создавайте слишком сложные вложенные селекторы: поддерживайте стили читаемыми.

## Краткие советы

- **styled-components** изолирует стили по уникальному classname.
- Стили можно наследовать, переопределять и комбинировать с обычным CSS.
- Можно легко интегрировать с TypeScript, глобальными темами и SSR-рендерингом.
- Все возможности CSS (в том числе медиазапросы, анимации, псевдоэлементы) поддерживаются напрямую.

Все описанное поддерживается и в других современных библиотечных подходах к CSS-in-JS, но styled-components максимально дружелюбен к React-разработчику.

**P.S.:** Для быстрого старта ориентируйтесь на структуру:  
`import styled from 'styled-components';`  
`const MyStyledComp = styled.тег`...  
И используйте все привычные CSS-правила прямо в JS!