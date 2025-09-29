# Minimal chat

This is a very simple chat interface for interacting with LLMs. I mostly just made this to test my Apple Intelligence API.

## Features
- [X] Basic chat interface
- [X] Streaming responses
- [X] Markdown support

Improvements that could be made:
- [ ] Store chat history with local storage (a database is probably overkill for this...)
- [ ] Port to Nuxt (and move OpenRouter calls to backend)

For my purposes, this project is complete. But if you're looking to get your hands dirty with Vue or Nuxt, I would welcome any PRs!

## Project setup
```
npm install
```

### OpenRouter API key
If you want to use this, you need to create a file `./src/keys.js` with the line
```
export const API_KEY = < your OpenRouter API key >
```
Note that this is terrible for anything except simple testing. Your API key **will** be exposed to the front end!

### Compile with hot-reloads for development
```
npm run serve
```

### Compile and minify for production
```
npm run build
```

### Lint and fix files
```
npm run lint
```
