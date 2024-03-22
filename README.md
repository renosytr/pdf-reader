# PDF Reader with Vue 3 + Vite

The PDF reader, which was built with Vue 3, is based on a library called [pdfjs-dist](https://www.npmjs.com/package/pdfjs-dist). Mozilla published this pdfjs-dist library as open source. We utilize this library to parse and display PDF files. This library's advantage rests in its ease of implementation. Because, once the PDF file is successfully parsed, we will receive an object as output. Then, We are free to manipulate and show this object on the HTML canvas.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```
