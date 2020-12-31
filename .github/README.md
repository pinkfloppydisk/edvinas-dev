# edvinas.dev ![GitHub pages deployment badge](https://github.com/Edvinas01/edvinas-dev/workflows/GitHub%20Pages/badge.svg)
Minimalistic, portfolio, programming, blog site.

## Prerequisites
Make sure you have [Hugo](https://gohugo.io/getting-started/installing) installed and that your version matches the one specified in [gh-pages.yml](https://github.com/Edvinas01/edvinas-dev/blob/master/.github/workflows/gh-pages.yml#L19).

## Commands
Run a development server on `localhost:8080`:
```bash
hugo server --port 8080 --buildDrafts
```

Build a production ready site under `public` dir:
```bash
hugo --minify --buildDrafts
```
