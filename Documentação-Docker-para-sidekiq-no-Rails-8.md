- [Configurando Docker no Rails 8 com db e testes](Configurando-Docker-para-Ruby-3.4.5+Rails-8.html)

1. Adiciona gem do `sidekiq`:
```rb
gem "sidekiq", "~> 7.0"
```

2. Vamos configurar o **worker** que executará o **Sidekiq**. Primeiro, na raiz do projeto, crie um diretório chamado `/worker`. Em seguida, dentro desse novo diretório, crie um arquivo chamado `Dockerfile`.

- Adicione o código:

```dockerfile
# syntax=docker/dockerfile:1
# check=error=true

ARG RUBY_VERSION=3.4.5
FROM ruby:$RUBY_VERSION-slim

# Install dependencies
RUN apt-get update -qq && \
    apt-get upgrade -y && \
    apt-get install --no-install-recommends -y \
      build-essential \
      curl \
      libpq-dev \
      htop \
      libyaml-dev \
      tzdata && \
    rm -rf /var/lib/apt/lists/*

# Timezone
ENV TZ=America/Recife
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Rails app lives here
WORKDIR /app

# Copy only Gemfile and install gems
COPY Gemfile Gemfile.lock ./
RUN bundle install --jobs 5 --retry 5

# Copy the rest of the application code
COPY . .

# Copy entrypoint script
COPY ./worker/entrypoint.sh /app/worker/entrypoint.sh
RUN chmod +x /app/worker/entrypoint.sh

# Start worker
CMD ["bash", "/app/worker/entrypoint.sh"]
```

3. configurando o compose.

- Adicione este código ao `docker-compose.yml`:

```yml   
 worker:
    build:
      context: .
      dockerfile: ./worker/Dockerfile
    env_file:
      - ./.env.development
    volumes:
      - .:/app #diretório da aplicação
```


- agora vamos criar o arquivo `entrypoint.sh` dentro do diretório `worker`, para configurar o sidekiq e adicionar o código:
```shell
#!/bin/sh

set -e
# roda o Sidekiq com 1 thread, usando a gem instalada pelo Bundler
exec bundle exec sidekiq -c 1#!/bin/sh

```
4. Configurando as rotas do sidekiq.
- No arquivo `routes` adicione estas configurações:

```ruby
require "sidekiq/web"
Rails.application.routes.draw do

 mount Sidekiq::Web => "/sidekiq" # monitoramento do sidekiq
end
```
- Agora você vai conseguir monitorar o sidekiq na rota: `http://127.0.0.1:3000/sidekiq`

- Adicione o código no seu `development.rb` para remover algumas mensagens do console relacionado ao sidekiq:

```ruby
Rails.application.configure do
  config.web_console.whiny_requests = false
end
```