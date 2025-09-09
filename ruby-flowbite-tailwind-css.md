
# 1. Instalação do Tailwind CSS e Flowbite no Ruby on Rails 8

# Vídeo Tutorial
https://www.youtube.com/watch?si=gXqCODCtUM3_xswU&v=YBAu2O7RnRI&feature=youtu.be

Vamos criar uma nova aplicação `Rails` configurada com **`PostgreSQL`**, **`Tailwind` `CSS`** e **`Flowbite`**, pulando a instalação do **`Jbuilder`**.

1. Crie a aplicação com o comando:
```bash
 rails new app --database=postgresql --css=tailwind --skip-jbuilder
```
> 💡 O banco de dados é opcional. O essencial aqui é configurar o  `Tailwind` e usar o parâmetro `--skip-jbuilder`.

2. Após rodar o comando acima entre no diretório da sua aplicação:
```bash
cd app
```
3. Em seguida acesso o diretório do javascript que fica em `vendor/javascript`:
```bash
cd vendor/javascript
```
4. Rode o código terminal para geração do arquivo `flowbite.turbo.min.js` dentro do diretório `vendor/javascript`:
```bash
curl https://cdn.jsdelivr.net/npm/flowbite@2.5.2/dist/flowbite.turbo.min.js > flowbite.turbo.min.js
```
5. Retorne para o diretório vendor com o comando:
```bash
cd ..
```
6. Crie um novo diretório chamado `assets`:
```bash
mkdir assets
```
7. Entre no diretório assets:
```bash
cd assets
```
8. Crie um novo diretório chamado `stylesheets`:
```bash
mkdir stylesheets
```
9. Entre no diretório stylesheets:
```bash
cd stylesheets
```
10. Rode o código terminal para gerar o arquivo `flowbite.min.css`:
```bash
curl https://cdn.jsdelivr.net/npm/flowbite@2.5.2/dist/flowbite.min.css > flowbite.min.css
```
11. Retorne para o diretório principal de sua aplicação usando cd.. até chegar a base, e depois abra o VScode com:
```bash
code .
```
# 2. Configuração do importmap

1. Na sua aplicação procure pelo arquivo `importmap.rb` e adicione o código:
```rb
pin "flowbite", to: "flowbite.turbo.min.js"
```
2. No arquivo application.js adicione a importação ao flowbite:
```js
import "flowbite"
```
# 3. Configuração do Tailwind

1. Se não existir o arquivo `config/tailwind.config.js` crie ele, dentro do arquivo adicione o código abaixo:
```js
module.exports = {
    darkMode: "class",
    content: [
        "./public/*.html",
        "./app/helpers/**/*.rb",
        "./app/javascript/**/*.js",
        "./app/views/**/*.{erb,haml,html,slim}",
    ],
    theme: {
        extend: {
            fontFamily: {
                sans: ["Inter var", ...defaultTheme.fontFamily.sans],
            }
        }
    },
    plugins: [
        require("@tailwindcss/forms"),
        require("@tailwindcss/typography"),
        require("@tailwindcss/container-queries")
    ]
}
```

# 4. Configuração do application.html.erb

1. No seu arquivo `application.html.erb` adicione o seguinte código dentro do head:
```rb
<%= stylesheet_link_tag "flowbite.min", "data-turbo-track": "reload" %>
```
Pronto agora o Flowbite deve está funcionando em sua aplicação, há algumas formas de testar, podemos subir o nosso servidor com,
```bash
bin/dev
```
e depois que abrirmos uma página a web, podemos abrir o console com F12 ir na aba Sources  e procurar pelos arquivos flowbite.min, tailwind, flowbite.turbo dentro do diretório assets/controller, também podem pegar alguns componentes no site do flowbite https://flowbite.com/docs/components/modal/, por exemplo podemos pegar um modal colar na view  e ver se funciona.
