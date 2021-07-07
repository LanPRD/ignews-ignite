## ROUTE PARAMS

Into pages create a folder API and a file [...name].ts
Now can create a route like express.js

## index.tsx

First page

```javascript
/* eslint-disable @next/next/no-img-element */
import { GetStaticProps } from "next";
import Head from "next/head";

import { SubscribeButton } from "../components/SubscribeButton";
import { stripe } from "../services/stripe";

import styles from "./home.module.scss";

interface HomeProps {
  product: {
    priceId: string,
    amount: number,
  };
}

// Product returned from getStaticProps()
export default function Home({ product }: HomeProps) {
  return (
    <>
      <Head>
        <title> Home | ig.news</title>
      </Head>

      <main className={styles.contentContainer}></main>
    </>
  );
}

// Function from NextJS
export const getStaticProps: GetStaticProps = async () => {
  const price = await stripe.prices.retrieve("price_1J9iUiIZZKIOzkPL7W888OXn", {
    expand: ["product"],
  });

  const product = {
    priceId: price.id,
    amount: new Intl.NumberFormat("en-US", {
      style: "currency",
      currency: "USD",
    }).format(price.unit_amount / 100),
  };

  return {
    props: {
      product: product,
    },
    revalidate: 60 * 60 * 24, // revalidade static page
  };
};
```

### getStaticProps and getServerSideProps

This function returned the PROPS to main function.
<br />
<br />
getStaticProps render the static page each time set.
<br />
getServerSideProps render everything in "backend".

```javascript
export const getStaticProps: GetStaticProps = async () => {
  return {
    props: {
      product: product,
    },
    revalidate: 60 * 60 * 24, // revalidade static page
  };
};
```

## \_document.tsx

The main HTML

```javascript
// NextScript execute all others functions of Main
import Document, { Html, Head, Main, NextScript } from "next/document";

export default class MyDocument extends Document {
  render() {
    return (
      <Html>
        <Head>
          <link rel="preconnect" href="https://fonts.googleapis.com" />
          <link rel="preconnect" href="https://fonts.gstatic.com" />

          <link
            href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700;900&display=swap"
            rel="stylesheet"
          />

          <link rel="shortcut icon" href="/favicon.png" type="image/png" />
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}
```

## \_app.tsx

Providers, components, etc.

```javascript
import { AppProps } from "next/app";
import { Header } from "../components/Header";
import { Provider as NextAuthProvider } from "next-auth/client";

import "../styles/global.scss";

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

## /public

For assets

## /pages/api

Into this folder, will be files about "backend" <br />
Example file [...nextauth].ts (With this file name, we can get route params /api/any/any/etc.)

```javascript
import NextAuth from "next-auth";
import Providers from "next-auth/providers";

export default NextAuth({
  providers: [
    Providers.GitHub({
      clientId: process.env.GITHUB_CLIENT_ID,
      clientSecret: process.env.GITHUB_CLIENT_SECRET,
      scope: "read:user",
    }),
  ],
});
```
