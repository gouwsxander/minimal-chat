# Minimal chat

A very simple chat interface for interacting with LLMs.

<p align="center">
<img src="./interface.png" width="750">
</p>

## Features
- [X] Basic chat interface
- [X] Streaming responses
- [X] Markdown support

Improvements that could be made:
- [ ] Use NuxtUI module for chat interface elements
- [ ] Move to using Vercel's AI SDK, with API calls made on the backend
- [ ] Enable multiple chats (with side bar to switch between them)
- [ ] Store chats using a SQLite database (I'm imagining Drizzle + Bun SQLite)
- [ ] Authentication (I'm imagining better-auth)
- [ ] Allow client to change the model and/or provider
- [ ] General visual improvements...

If you're looking to get your hands dirty with Vue or Nuxt, I would welcome any PRs!

## Project setup
### Install dependencies
```
bun install
```

### OpenRouter API key
If you want to use this, you need to create a file `./app/keys.js` with the line
```
export const API_KEY = < your OpenRouter API key >
```
Note that this is terrible for anything except simple testing. Your API key **will** be exposed to the front end!

### Run development server
```
bun run dev
```

### Build for production
```
bun run build
```

### Preview production build
```
bun run preview
```
