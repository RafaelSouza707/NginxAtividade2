# Teste de Carga em Aplicação ReactJS com Apache Bench (ab)

## Objetivo
Este tutorial descreve o processo de instalação e utilização do **Apache Bench (ab)** para executar um teste de carga na página inicial (*Home*) de uma aplicação **ReactJS** servida via **Nginx** em Docker.

O objetivo é simular múltiplas requisições simultâneas para medir desempenho e capacidade de resposta do servidor.

---

## Pré-requisitos
- Aplicação ReactJS rodando via Nginx (Docker)
- Docker instalado
- Sistema Linux Ubuntu/Debian (ou WSL no Windows)
- Acesso ao terminal

---

# Etapa 1 — Validar se a aplicação está em execução

## Passo 1.1 — Conferir container em execução
No terminal do host:
```
docker ps
```
Deve aparecer um container semelhante a:

`react-nginx-container`

## Passo 1.2 — Validar acesso via navegador

Acesse:

`http://localhost:8080`

A aplicação React deve carregar normalmente.

## Passo 1.3 — Validar via terminal com curl (opcional)
`curl -I http://localhost:8080/`

O retorno esperado deve conter algo como:

`HTTP/1.1 200 OK`

# Etapa 2 — Instalar Apache Bench

## Passo 2.1 — Atualizar repositórios
`sudo apt update`

## Passo 2.2 — Instalar apache2-utils

O Apache Bench está incluído no pacote apache2-utils.

`sudo apt install apache2-utils -y`

## Passo 2.3 — Verificar instalação

`ab -V`

Se aparecer a versão, a instalação foi concluída corretamente.

# Etapa 3 — Conceitos básicos do Apache Bench

Principais parâmetros

O comando ab utiliza parâmetros importantes:

- -n → número total de requisições
- c → número de requisições simultâneas (concorrência)
- -t → tempo total do teste (segundos)
- -k → habilita KeepAlive (conexões persistentes)

# Etapa 4 — Executar teste de carga na Home da aplicação React

## Passo 4.1 — Teste inicial simples

Executar 1000 requisições com 10 usuários simultâneos:

`ab -n 1000 -c 10 http://localhost:8080/`

## Passo 4.2 — Teste intermediário com KeepAlive

Executar 5000 requisições com 50 usuários simultâneos e KeepAlive:

`ab -n 5000 -c 50 -k http://localhost:8080/`

## Passo 4.3 — Teste avançado (maior carga)

Executar 20000 requisições com 200 usuários simultâneos:

`ab -n 20000 -c 200 -k http://localhost:8080/`

Esse teste pode consumir muita CPU e memória.

# Etapa 5 — Executar teste baseado em tempo

## Passo 5.1 — Teste por 30 segundos

Simular acessos simultâneos durante 30 segundos:

`ab -t 30 -c 100 -k http://localhost:8080/`

Esse modo é útil para avaliar estabilidade do servidor sob carga contínua.

# Etapa 6 — Interpretar os resultados do teste

Após o teste, o Apache Bench exibirá estatísticas como:

```
Requests per second:  350.12 [#/sec] (mean)
Time per request:     28.56 [ms] (mean)
Failed requests:      0
Transfer rate:        1200.45 [Kbytes/sec]
```

#### Explicação dos principais indicadores:

`Requests per second`

Quantidade média de requisições respondidas por segundo.

`Time per request`

Tempo médio que o servidor levou para responder uma requisição.

`Failed requests`

Número de requisições que falharam (idealmente deve ser 0).

`Transfer rate`

Taxa média de transferência de dados durante o teste.

# Etapa 7 — Monitoramento durante o teste

## Passo 7.1 — Monitorar consumo do container

Em outro terminal:

`docker stats`

Isso mostra CPU, memória e rede do container.

## Passo 7.2 — Monitorar consumo do host

`top`

Ou:

`htop`

(se instalado)

# Etapa 8 — Salvar relatório do teste em arquivo

## Passo 8.1 — Exportar resultado

`ab -n 5000 -c 50 -k http://localhost:8080/ > resultado_ab.txt`

Isso salva o relatório completo em um arquivo texto.
