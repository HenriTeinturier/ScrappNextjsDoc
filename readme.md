# Nextjs Doc Scraper

### Scrap datas from nextjs doc:

This will scrap the data from nextjs doc with Playwright. Data transformation and cleaning + adding wrappers to make sens of the data for ia with Cheerio. Finally save it in separate files in data/nextjs folder.

```terminal
npm run scrap
```

<div style="display: flex; align-items: center; gap: 8px; margin-bottom: 8px">
  <img src="https://playwright.dev/img/playwright-logo.svg" alt="Description de l'image" style="max-width: 40px;">
  <a href="https://playwright.dev/">Link to Playwright</a>
</div>

<div style="display: flex; align-items: center; gap: 8px">
  <img src="https://cheerio.js.org/img/orange-c.svg" alt="Description de l'image" style="max-width: 30px;">
  <a href="https://cheerio.js.org/">Link to npm Cheerio</a>
</div>

## Create dataBase for store embedding data:

- On Neon.tech créate a database (Neon because is compatible with vector data) and create a collection for store the data.
- add the connection string in DATABASE_URL in .env. Be sure to complete userName and replace \*\*\*\*\*\*\* by password
- Create Tables with the command SQL in database.sql

```sql
DROP SCHEMA public CASCADE;

CREATE SCHEMA public;

CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE IF NOT EXISTS documents (text text, n_tokens integer, file_path text, embeddings vector(1536));

CREATE INDEX ON documents USING ivfflat (embeddings vector_cosine_ops);

CREATE TABLE IF NOT EXISTS openai_ft_data (
  id SERIAL PRIMARY KEY,
  query TEXT NOT NULL,
  answer TEXT NOT NULL,
  suggested_answer TEXT,
  user_feedback BOOLEAN
);

CREATE TABLE IF NOT EXISTS usage (
  id SERIAL PRIMARY KEY,
  ip_address TEXT NOT NULL,
  created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

<div style="display: flex; align-items: center; gap: 8px">
  <img src="https://neon.tech/_next/static/svgs/e9de8fc7653111a1423e0d227c0c5e9f.svg" alt="Description de l'image" style="max-width: 70px;">
  <a href="https://neon.tech/">Link to Neon</a>
</div>

## OpenAi Key:

- Add OpenAi key in .env for use the Api for embedding the data.

<div style="display: flex; align-items: center; gap: 8px">
  <img src="https://static.vecteezy.com/system/resources/previews/022/227/364/non_2x/openai-chatgpt-logo-icon-free-png.png" alt="Description de l'image" style="max-width: 30px;">
  <a href="https://platform.openai.com/overview">Link openAi</a>
</div>

## Embedding datas:

```terminal
 npm run embedding
```

this command will do this actions:

- Create array of objetcs with texts and fileName and save it to a json file (texts.json)
- tokenize all texts with tiktoken to know token Number and save it to a json file (textsTokens.json)
- Split the texts in max 1500 tokens. If split, split according to the subtitles (Tag h2) and save it to a json file (textsTokensSplited.json)
- embedding all split texts with text-embedding-3-small from openai and save it to a json file (textsTokensSplitedEmbedding.json)
- save the embedding data to the database

tiktoken library is used to transform text into tokens. We will use this for calculate how many tokens we need to split the text in order to be able to embed it with openAi.

⏳ [Link to npm tiktoken](https://www.npmjs.com/package/@dqbd/tiktoken#nextjs) / [Lien vers le github de tiktoken](https://github.com/dqbd/tiktoken/blob/main/js/README.md)

You can uncomment displayTokenLengthStats function if you want to check the token sending statistics before saveToDatabase. In this case, don't forget to comment out saveToDatabase function.
