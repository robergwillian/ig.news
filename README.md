# igNews

## Serviços utilizados

### Stripe - pagamentos

- api de pagamentos, developer friendly

### GITHUB - AUTENTICAÇÃO - OAuth

- utilizar autenticação via github
- OAuth - padrao de autenticação

### FaunaDB - Banco de Dados

- faunaDb, é especifico para aplicações SERVERLESS ( cada roda da aplicação sera executada em um ambiente isolado,
  ou seja, cada vez que o usuario acessa um recurso na nossa aplicação, ao invez de executar tudo em um servidor node.js que ta la executando 24h por dia,
  só no momento que o ususario chamar a rota, o nosso provedor de cloud ( AWS, AZURE, ETC ), vai instanciar uma maquina virtual, vai executar a funcao e devolver o resultado , e depois deleta a maquina de forma bruta. )

toda conexão com o db é feita por http por meio de rest

### Prismic CMS - Content Management System

- painel de administração para o site, para cadastrar os dados.

## NEXT.js

- Framework criado em cima do React ( biblioteca ),
- SSR - Server Side Rendering
- File System Routing ( cada arquivo dentro do /pages se torna uma roda de acesso por exemplo
  products.tsx podemos acessar por meio de http://localhost:3000/products )

### Starting Next Project

- yarn create next-app nomeDoProjeto

### Adicionando TypeScript ao Projeto

- yarn add typescript @types/react @types/node -D

### Estilizando com SASS

- scoped css: cada componete tera seu proprio css
- arquivos devem ter nome home.module.css - todo arquivo que tiver module.css é um scoped css
- import styles from '../styles/home.module.scss';
- <h1 className={styles.title}>

- yarn add sass

### O que o arquivo \_app.tsx faz:

- É um componete que sempre vai ficar por volta de todas as paginas
- É o primeiro componente a ser mostrado
- Podemos colocar components que irá aparecer em todas as paginas, ex: header
- Toda vez que o usuario troca de pagina, esse \_app sera reexecutado

### \_document.tsx:

- semelhante ao \_app, porem é carregado uma unica vez quando se carrega a aplicação
- comparado ao index.html da pasta public do react-app
- é um component react
- colocamos o html statico do app
- extende Document do next
- Algumas tags html são substituidas por componentes do next
- <Html> substitui <html lang="en">
- <Head> substitui o <head>
- <Main> substitui o div id="root">
- <NextScript /> é obrigatorio que é onde o next ira colocar nossos scripts js
- exemplo de um arquivo \_document:

```javascript
import Document, { Html, Head, Main, NextScript } from 'next/document'

export default class MyDocument extends Document {
  render() {
    return (
      <Html>
        <Head>
          <title>ig.News</title>
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    )
  }
}
```


### yarn add react-icons

- Pacote de icons para a aplicação
- import { FaGithub } from 'react-icons/fa'

```javascript

   <button type="button">
        <FaGithub color="#04d361"/>
        Sign in with Github
    </button>

```

