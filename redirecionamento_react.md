# Implantação ReactJS com Nginx (Aprimoramento de Suporte a Rotas SPA)

## Objetivo
Este tutorial demonstra como implantar uma aplicação ReactJS simples utilizando Nginx em Docker, aplicando uma configuração recomendada para aplicações SPA.  

A configuração garante que o servidor esteja preparado para lidar corretamente com rotas internas caso o sistema evolua futuramente.

---

## Pré-requisitos
- `Docker instalado`
- `Node.js e npm instalados`

---

# Etapa 1 — Criar aplicação React simples

## Passo 1.1 — Criar projeto React
No terminal:

```
npx create-react-app react-nginx-app
cd react-nginx-app
```

## Passo 1.2 — Executar aplicação localmente (opcional)
```
npm start
```

Acesse:

`http://localhost:3000`

# Etapa 2 — Gerar build de produção
## Passo 2.1 — Criar build
`npm run build`

Será criada a pasta:

- `build/`

Essa pasta contém os arquivos estáticos finais do React.

# Etapa 3 — Criar configuração Nginx
## Passo 3.1 — Criar arquivo nginx.conf

Na raiz do projeto, crie:

`nginx.conf`

Conteúdo:

```
server {
    listen 80;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

Essa configuração garante que o Nginx sempre utilize o index.html como fallback, padrão recomendado para aplicações SPA.

# Etapa 4 — Criar Dockerfile
## Passo 4.1 — Criar arquivo Dockerfile

Crie o arquivo Dockerfile na raiz do projeto:

```
# Etapa 1: build do React

FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build


# Etapa 2: servir com Nginx

FROM nginx:alpine

COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

# Etapa 5 — Criar imagem Docker

## Passo 5.1 — Build da imagem
```
docker build -t react-nginx-app .
```
# Etapa 6 — Executar container
## Passo 6.1 — Rodar container

```
docker run -d -p 8080:80 --name react-nginx-container react-nginx-app
```

# Etapa 7 — Testar aplicação

Acesse no navegador:

`http://localhost:8080`
