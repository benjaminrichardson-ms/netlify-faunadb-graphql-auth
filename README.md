# Netlify + FaunaDB + GraphQL + Auth &nbsp;&nbsp;&nbsp;<a href="https://app.netlify.com/start/deploy?repository=https://github.com/ptpaterson/netlify-faunadb-graphql-auth&stack=fauna"><img src="https://www.netlify.com/img/deploy/button.svg"></a>

Example of using [FaunaDB](https://fauna.com/) with [Netlify functions](https://www.netlify.com/docs/functions/).

This example shows how to use HTTP only cookies for auth with FaunaDB's native Graphql API.

## About this application

This application is using [React](https://reactjs.org/) for the frontend, [Netlify Functions](https://www.netlify.com/docs/functions/) for API calls, and [FaunaDB](https://fauna.com/) as the backing database, utilizing the Graphql API with [Apollo](https://www.apollographql.com/docs/apollo-server/deployment/lambda/). This project is bundled with [Parcel](https://parceljs.org/).

The todo editing is enabled by [Draft.js](https://draftjs.org/)

This project is primarily derived from [netlify-faunadb-example](https://github.com/netlify/netlify-faunadb-example)

![faunadb netlify](https://user-images.githubusercontent.com/532272/42067494-5c4c2b94-7afb-11e8-91b4-0bef66d85584.png)

## Easy setup with Netlify dev

[Netlify Dev](https://www.netlify.com/products/dev/) works with `parcel-bundler` out of the box!

(it also works with `create-react-app`)

So there is no need to install `netlify-lambda` and set up function proxies.

## How to Setup & Run Locally

### Get your code

1. Clone down the repository

  ```bash
  git clone https://github.com/ptpaterson/netlify-faunadb-graphql-auth.git
  ```

2. Enter the repo directory

  ```bash
  cd netlify-faunadb-example
  ```

3. Install the dependencies

  ```bash
  npm install
  # -OR-
  yarn
  ```

### Prepare FaunaDB database

4. Sign up for a FaunaDB account

  https://dashboard.fauna.com/accounts/register

5. Create a master database. It will become the parent database for the app database generated by the script provided.
In the Fauna Cloud Console:

  - Click “New Database”
  - Enter any name, e.g. “Netlify”, as the “Database Name”
  - Click “Save”

6. Create a database access key

  In the Fauna Cloud Console:

  - Click “Security” in the left navigation
  - Click “New Key”
  - Make sure that the “Database” field is set to “Netlify” (or whatever you named it)
  - Make sure that the “Role” field is set to “Admin”
  - Enter any name, e.g. “Netlify Admin key” as the “Key Name”
  - Click “Save”

7. Copy the database access key’s secret

  Save the secret somewhere safe; you won’t get a second chance to see it.

### Bootstrap the database

8. Set your database access secret for running the bootstrapping scripts
  Either set the environment variables directly in your terminal, or use a `.env` file. The scripts will pick up anything in a `.env` file.

  In your terminal, run the following command:

  ```bash
  # bash
  export FAUNADB_ADMIN_KEY="YourFaunaDBSecretHere"
  ```

  > NOTE: Make sure to replace `YourFaunaDBSecretHere` with the value of the secret that you copied in the previous step.

  > NOTE: We'll set a different variable for FAUNADB_PUBLIC_KEY in a bit, which is the important key to actually run the app!

9. Bootstrap your FaunaDB collection and indexes

  ```bash
  node scripts/create-database.js
  ```

  The `bootstrap-db` script will output some important information. Copy down the created keys shown to you in the output. If you lose the keys, you can generate them by hand or run the bootstrap command again.

  Output should be similar to:

  ```
  Creating your FaunaDB Database...

  1) Create database "auth-example"
  + Created Database "auth-example"

  2) Creating temporary key
  + Created Key "temp admin key for auth-example"

  3) Uploading Graphql Schema...
  o GraphQL schema imported

  4) Update generated User Defined Functions...
  o Updated Function "login"
  o Updated Function "logout"
  o Updated Function "me"
  o Updated Function "user_create_todo"

  5) Create custom roles...
  + Created Role "public"
  + Created Key "Public key for auth-example"
  ! Public client key: fnADmZcVSKFYEykYVRyVaQ5WN9RQ3OEmEMtweMWk
  + Created Role "user"
  -Deleted Key "temp admin key for auth-example"

  Fauna Database schema has been created
  Claim your fauna database with "netlify addons:auth fauna"
  ```

10. Upload Graphql Schema

  In the Fauna Cloud Console:

  - Click “GraphQL” in the left navigation
  - Click “IMPORT SCHEMA”
  - Upload the schema at [`functions/graphql/faunaSchema.graphql`](https://github.com/ptpaterson/netlify-faunadb-graphql-auth/blob/master/functions/graphql/faunaSchema.graphql)

11. (Optional) Add example data

  Set the `FAUNADB_ADMIN_KEY` variable (or save to `.env` file).

  Then run

  ```bash
  node scripts/create-example-data.js
  ```

  Or, use the GraphQL Playground now available in the cloud console.

### Run project Locally

12. Get `netlify-cli`

  ```bash
  npm install netlify-cli -g
  ```

13. Run the project
  Either set the environment variables directly in your terminal, or use a `.env` file. The scripts will pick up anything in a `.env` file.

  In your terminal, run the following command:

  ```bash
  # bash
  export FAUNADB_PUBLIC_KEY="YourFaunaDBPublicSecret"
  ```

  ```bash
  netlify dev
  ```

## Updating the Schema

This project uses `apollo-server-lambda` for the lambda function. Lambdas are [not a great solution for fetching remote schemas](https://github.com/apollographql/apollo-server/issues/3190). Because of this, any time a new schema is uploaded to FaunaDB, the SDL should be downloaded and placed as a string in [`functions/graphql/remoteSchema.js`](https://github.com/ptpaterson/netlify-faunadb-graphql-auth/blob/master/functions/graphql/remoteSchema.js).
