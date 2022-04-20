
![alt](screenshot.png)

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


### INTEGRANDO O STRIPE

- YARN ADD STRIPE

- criar conta teste no stripe.com
- criar produto
- pegar secret_key da api
- criar arquivo de varial global chamado .env.local
- adicionar:

STRIPE_API_KEY=sk_test_51JfpFNLH4UkGSCcoyPRURf9jQ6YTSSN6SORJLwynpVEgvO1jfYNCD7i4akmqmLFar0gb4lbKu0pNKZFpjVAXx0Vp00WIiMIOfS

---

### Fazer chamada de API ( Stripe ) pelo SSR ( Server Side Rendering )

- Criar pasta services na src
- criar aquivo stripe.ts:

```javascript
import Stripe from 'stripe'
import {version} from '../../package.json'

export const stripe = new Stripe(
    process.env.STRIPE_API_KEY,
    {
        apiVersion: '2020-08-27',
        appInfo: {
            name: 'igNews',
            version
        }

    }
)

```


- dentro de uma pagina do NEXT, não funciona no componente

```javascript
export const getServerSideProps: GetServerSideProps = async () => {
  const price = await stripe.prices.retrieve("price_1JfpJCLH4UkGSCcoPwLaion6", {
    expand: ["product"], // traz todos os dados do produto
  });

  const product = {
    priceId: price.id,
    amount: new Intl.NumberFormat("en-us", {
      style: "currency",
      currency: "usd",
    }).format(price.unit_amount / 100), //esse valor vem sempre em centavos
  };

  return {
    props: {
      product,
    },
  };
};
```

- Necessario criar uma interface, para poder utilizar os dados do produto puxado pela api
  

```javascript
interface HomeProps {
  product: {
    priceId: string;
    amount: number;
  };
}

export default function Home({ product }: HomeProps) 
```

### STATIC SITE GENERATION ( SSG )

- O next salva o html de forma estatica gerado da primeira render que o usuario pedir
- da proxima vez que for acessado, o next retorna o html estatico para nao precisar renderizar tudo novamente
- pra fazer isso muda-se o getServerSideProps para getStaticProps
- o return da função tambem muda, adicionando o revalidate:
  
```javascript
 return {
    props: {
      product,
    },
    revalidate: 60 * 60 * 24// 24 horas quanto tempo em segundo eu quero que essa pagina fique sem revalidar
  };
  ```


### No next temos 3 formas de fazer chamadas de API

- Client-side
- Server-side
- Static Site Generation

### Backend for FrontEnd

- O Next permite criar uma estrutura de back-end para ser utilizada na nossa aplicação, isso é possivel porque o Next foi desenvolvido em cima do Node.

- Utiliza um sistema de File System Routing. Dentro da pasta pages, criamos uma pasta API, todo arquivo dentro dessa passa se torna uma rota de acesso da api.

- Serverless, não precisa de um servidor rodando o tempo inteiro para que a aplicação funcione, conforme necessario o Next instancia um camada de acesso de api para que a aplicação tenha acesso aos dados necessarios.

### Estrategias de AUTENTICAÇÂO

- Temos 3 metodos ou formas de autenticação que podesmos utilizar no next:
  - JWT - utiliza token, simples e seguro, utiliza storage e pode ter data de expiração e utilizar o refresh conforme necessario

  - NEXT AUTH: Simples, login social, não precisamos nos preocupar em salvar credenciais do usuario no backend
  - EXTERNOs: Cognito, Auth0, são providers de autenticação ou autenticação como serviço, todo controle é feito por um terceiro.

### Parametrização das rotas da API

- criar uma pasta pra cada roda
- dentro de cada pasta criar aquivo index.ts  
- o segundo arquivo da pasta deve ser o arquivo de parametrização
  - arquivo deve ter um [] no nome, com o parametro passado dentro .tsx
  - exemplos:
    - [id].tsx : passa o id digitado na rota como parametro pra esse pagina por meio de params, e dentro desse arquivo inserimos a logica crud. Porem, fazer desse jeito se for uma aplicação que complexa, teriamos muitos arquivos.
    - [...params].tsx : utilizando o spread operator, podemos passar varios parametros ao mesmo tempo pela rota, e criar toda a logica dentro de um unico arquivo, podeira passar por exemplo: localhost:3000/api/users/remove/1 , os parametros remove e o 1, poderemos utlizar como indicador de qual função sera executada, e o id para qual usuario essa função irá executar.


### Autenticação com Next Auth

- seguir o guia na pagina do Next Auth
- adicionar [...nextauth].ts dentro de pages/api/auth
  
  import NextAuth from "next-auth";
import Providers from "next-auth/providers";
  
```javascript
export default NextAuth({
  // Configure one or more authentication providers
  providers: [
    Providers.GitHub({
      clientId: process.env.GITHUB_CLIENT_ID,
      clientSecret: process.env.GITHUB_CLIENT_SECRET,
      scope: 'read:user'
    }),
    // ...add more providers here
  ],
});

```

- yarn add next-auth
- yarn add @type/next-auth -D
- adicionar chaves ( clientID, clientSecret) do github no .env.local
- url de callback pra aplicacao é endereço/api/auth/callback
- scope : quais informações eu quero ter acesso do usuario.
<p>No componente que vai fazer a aplicação teremos que fazer a importacao das seguintes funções que fazem o controle do usuario:</p>

  - import { signIn, signOut, useSession } from 'next-auth/client'
  - essas funções devem ser aplicadas ao onClick dos botões que vão exercer essa funções.
  - signIn('github') : recebe por parametro qual provider de auth que vai ser utilizado
  - useSession - retorna informações relacionadas a se o ususario esta logado ou não
  - signOut - finaliza a session do usuario.
  - O nextAuth utiliza o useCOntext para prover informações para todas as paginas e componentes avisando se o ususario esta logado ou nao. Devido a isso, temos que encapsular todos os componentes com o provider do Next-Auth como a seguir:

```javascript
import { Provider as NextAuthProvider } from 'next-auth/client'

function MyApp({ Component, pageProps }: AppProps) {
  return (
    <NextAuthProvider session={pageProps.session}>
      <Header />
      <Component {...pageProps} />
    </NextAuthProvider>
  );
}

export default MyApp;

```

## usando FaunaDB

- indicado para serverless
- toda conexão por HTTP
- nao precisa manter um pool de conexão aberto
  

### Configurando o FaunaDB

- Criar conta no fauna_db
- criar key
- adicionar key no env.local = FAUNA_KEY
- criar uma colection no dashbaord do fauna
- instalar a sdk dentro do app: yarn add faunadb
- criar fauna.ts dentro de services

```Javascript
import { Client } from "faunadb";

export const fauna = new Client({
  secret: process.env.FAUNA_KEY,
});

```

## Salvar o usuario no banco logo que ele fizer login no app

- o nextauth tem alguns callbacks que são funções que ocorrem automaticamente
- adicionamos o callback de signIn conforme o seguinte:

```Javascript
  callbacks: {
    async signIn(user, account, profile) {
      const { email } = user;

      try {
        await fauna.query(q.Create(q.Collection("users"), { data: { email } }));
        return true;
      } catch (error) {
        console.log(error);
        return false;
      }
    }, 
  },
  ```

  - awaia fauna.query abre uma conexão com o faunddb
  - metodo query.Create() especifica uma metodo de inserção de dados no db
  - parametro: query.Collection() especifica a collection pra qual inserir
  - { data: {email }} - dados a serem inserido.

## Evitar ususario duplicado

```javascript
callbacks: {
    async signIn(user, account, profile) {
      const { email } = user;

      try {
        await fauna.query(
          q.If(
            q.Not(
              q.Exists(
                q.Match(q.Index("user_by_email"), q.Casefold(user.email))
              )
            ),
            q.Create(q.Collection("users"), { data: { email } }),
            q.Get(q.Match(q.Index("user_by_email"), q.Casefold(user.email)))
          )
        );
        return true;
      } catch (error) {
        console.log(error);
        return false;
      }
    },
  },
```



