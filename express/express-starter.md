# React & Express w/ TypeScript Setup

Follow the steps outlined below to setup a simple React & Express project using TypeScript.

## Top Level Setup

- On your local machine, create a directory and then go into it: `mkdir PROJECT_NAME && cd PROJECT_NAME`
- Then open it in VS Code: `code .`
- Next, create 2 top level folders called `client` and `server`: `mkdir client server`
- Next, `cd` into each folder in their own terminal tab/widow

## Client Setup

- Download and create a basic React + TypeScript boilerplate: `npx create-react-app . --template typescript`
- While that is going, let's setup the Server

## Server Setup

- In the server folder, create a `package.json` file: `npm init -y`
- Next, create a `tsconfig.json` file: `tsc --init`
- Download & install the following packages: `npm i cors express nodemon ts-node typescript @types/cors @types/express @types/node`

- Update `tsconfig.json`:
  - Set `target` to: `"target": "es6",`
  - Set `outDir` to: `"outDir": "./dist"`
  - Set `rootDir` to: `"rootDir": "./src"`
  - Leave all others as-is, and remove the excess comments
- Now, we need to create the `src` folder: `mkdir src`
- Go into this `src` folder, and create an `index.ts`: `cd src && touch index.ts`
- In `index.ts,` add the following code:

```
import cors from 'cors';
import express from 'express';

// initialize express app
const app = express();

// cors
// NOTE: this must be placed ABOVE any routing
app.use(cors());

// port
const PORT = process.env.PORT || 5000;

// listen
app.listen(PORT, () => console.log(`Server running on port: ${PORT}`));
```
