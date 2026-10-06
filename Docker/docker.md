# Guia de Estudos Docker

> Material compilado e organizado a partir de: **Comandos Docker.docx** (anotações pessoais de estudos) e documentação oficial do Docker.

---

## Sumário

1. [O que é Docker?](#1-o-que-é-docker)
2. [Instalação](#2-instalação)
3. [Conceitos Fundamentais](#3-conceitos-fundamentais)
4. [Comandos Essenciais](#4-comandos-essenciais)
5. [Gerenciamento de Imagens](#5-gerenciamento-de-imagens)
6. [Gerenciamento de Containers](#6-gerenciamento-de-containers)
7. [Redes e Portas](#7-redes-e-portas)
8. [Docker Compose](#8-docker-compose)
9. [Build de Imagens](#9-build-de-imagens)
10. [Limpeza e Manutenção](#10-limpeza-e-manutenção)
11. [Resolução de Problemas Comuns](#11-resolução-de-problemas-comuns)
12. [Boas Práticas](#12-boas-práticas)
13. [Fontes de Consulta](#13-fontes-de-consulta)

---

## 1. O que é Docker?

Docker é uma **plataforma de containerização** que permite empacotar uma aplicação com todas as suas dependências (bibliotecas, configurações, runtime) em uma unidade padronizada chamada **container**.

### Analogia simples

> **Container ≠ Máquina Virtual**
>
> - **VM:** Virtualiza o hardware completo + SO convidado (pesado, lento)
> - **Container:** Virtualiza apenas o **SO do host** (leve, rápido, inicia em segundos)

### Por que usar?

- **Portabilidade:** "Funciona na minha máquina" → funciona em qualquer lugar que tenha Docker
- **Isolamento:** Cada aplicação roda em seu próprio ambiente isolado
- **Versionamento:** Imagens são versionadas (tags), facilitando rollback
- **Escalabilidade:** Fácil subir múltiplas réplicas da mesma aplicação

---

## 2. Instalação

### Script automatizado (recomendado)

Existe um script pronto para automatizar a instalação do Docker Engine baseado na documentação oficial. Cópia disponível em:
```
home/estudos/programacao/comandos docker
```

### Instalação manual (Linux Ubuntu/Debian)

```bash
# Atualizar pacotes
sudo apt-get update

# Instalar dependências
sudo apt-get install -y ca-certificates curl gnupg lsb-release

# Adicionar chave GPG oficial do Docker
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Adicionar repositório
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker Engine
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Verificar instalação
docker --version
docker run hello-world
```

### Pós-instalação (opcional - para não usar sudo)

```bash
sudo usermod -aG docker $USER
# Faça logout e login novamente
```

---

## 3. Conceitos Fundamentais

| Conceito | Descrição | Analogia |
|---|---|---|
| **Imagem** | Template somente leitura com a aplicação + dependências | "Molde" ou "Receita" |
| **Container** | Instância em execução de uma imagem | "Bolo assado a partir da receita" |
| **Dockerfile** | Arquivo de texto com instruções para construir uma imagem | "Instruções da receita" |
| **Registry** | Repositório de imagens (ex: Docker Hub) | "Dispensa de receitas" |
| **Volume** | Persistência de dados fora do container | "Armário externo" |
| **Network** | Comunicação entre containers | "Corredor entre quartos" |

### Ciclo de vida básico

```
Dockerfile → build → Imagem → run → Container
                    ↓
              push/pull → Registry (Docker Hub)
```

---

## 4. Comandos Essenciais

### 4.1 Informação e Ajuda

| Comando | Função |
|---|---|
| `docker --version` | Versão do Docker instalada |
| `docker info` | Informações do sistema (containers, imagens, storage, etc.) |
| `docker <comando> --help` | Ajuda de um comando específico |

### 4.2 Ciclo de vida do Container

```bash
# Criar e iniciar container (run = create + start)
docker run [OPÇÕES] IMAGEM [COMANDO] [ARGUMENTOS]

# Listar containers em execução
docker ps

# Listar TODOS os containers (incluindo parados)
docker ps -a
# ou
docker container ls -a

# Parar container
docker stop <ID_OU_NOME>

# Iniciar container parado
docker start <ID_OU_NOME>

# Reiniciar container
docker restart <ID_OU_NOME>

# Pausar/despausar (congela processos)
docker pause <ID>
docker unpause <ID>

# Remover container (precisa estar parado, ou use -f)
docker rm <ID_OU_NOME>
docker rm -f <ID_OU_NOME>  # força remoção mesmo rodando
```

### 4.3 Execução Interativa

```bash
# Executar em modo interativo com terminal (bash)
docker run -it ubuntu bash

# Entrar em container JÁ RODANDO
docker exec -it <ID_OU_NOME> bash

# Executar comando único dentro do container
docker exec <ID_OU_NOME> ls -la /app
```

> **Flags úteis no `run`:**
> - `-i` = interativo (stdin aberto)
> - `-t` = aloca pseudo-TTY (terminal)
> - `-d` = detached (segundo plano)
> - `--name` = nome personalizado para o container
> - `--rm` = remove automaticamente ao parar

---

## 5. Gerenciamento de Imagens

### 5.1 Listar e Buscar

```bash
# Imagens baixadas localmente
docker images
# ou
docker image ls

# Todas as imagens (incluindo intermediárias)
docker images -a

# Buscar no Docker Hub
docker search ubuntu
```

### 5.2 Baixar e Remover

```bash
# Baixar imagem (pull)
docker pull ubuntu:22.04
docker pull nginx:latest

# Remover imagem por ID ou nome:tag
docker rmi <IMAGE_ID>
docker rmi ubuntu:22.04

# Remover múltiplas imagens
docker rmi <ID1> <ID2> <ID3>

# Forçar remoção (mesmo se container usa)
docker rmi -f <IMAGE_ID>
```

### 5.3 Inspecionar

```bash
# Detalhes completos da imagem (JSON)
docker inspect <IMAGEM>

# Histórico de camadas da imagem
docker history <IMAGEM>
```

---

## 6. Gerenciamento de Containers

### 6.1 Criação com Opções Comuns

```bash
# Rodar em background (detached)
docker run -d nginx

# Mapear porta (host:container)
docker run -d -p 8080:80 nginx

# Mapear porta aleatória (-P = Publish all exposed ports)
docker run -d -P nginx

# Definir nome do container
docker run -d --name meu_nginx nginx

# Variáveis de ambiente
docker run -d -e MYSQL_ROOT_PASSWORD=senha123 mysql:8.0

# Montar volume (persistência)
docker run -d -v /dados/host:/dados/container mysql:8.0

# Limite de memória/CPU
docker run -d --memory=512m --cpus=1 nginx
```

### 6.2 Ver Logs e Processos

```bash
# Ver logs (stdout/stderr)
docker logs <ID_OU_NOME>
docker logs -f <ID_OU_NOME>  # follow (tempo real)
docker logs --tail 100 <ID>  # últimas 100 linhas

# Processos rodando dentro do container
docker top <ID_OU_NOME>

# Estatísticas de uso (CPU, RAM, rede, disco)
docker stats <ID_OU_NOME>
```

### 6.3 Copiar Arquivos

```bash
# Host → Container
docker cp arquivo.txt <ID>:/caminho/destino

# Container → Host
docker cp <ID>:/caminho/origem ./arquivo.txt
```

---

## 7. Redes e Portas

### 7.1 Portas

```bash
# Ver mapeamento de portas de um container
docker port <ID_OU_NOME>

# Exemplo de saída:
# 80/tcp -> 0.0.0.0:8080
```

### 7.2 Redes

```bash
# Listar redes
docker network ls

# Criar rede customizada
docker network create minha_rede

# Conectar container à rede
docker network connect minha_rede <ID_OU_NOME>

# Desconectar
docker network disconnect minha_rede <ID_OU_NOME>

# Inspecionar rede
docker network inspect minha_rede

# Remover rede
docker network rm minha_rede
```

> **Dica:** Containers na mesma rede customizada se comunicam pelo **nome do container** (DNS interno do Docker).

---

## 8. Docker Compose

Para aplicações multi-container (ex: app + banco + cache).

### 8.1 Arquivo `docker-compose.yml` (exemplo)

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=db
      - DB_USER=usuario
      - DB_PASS=senha
    depends_on:
      - db
    volumes:
      - ./src:/app/src

  db:
    image: mysql:8.0
    environment:
      - MYSQL_ROOT_PASSWORD=senha123
      - MYSQL_DATABASE=meubanco
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - minha_rede

volumes:
  db_data:

networks:
  minha_rede:
```

### 8.2 Comandos Principais

```bash
# Subir tudo em background
docker compose up -d

# Subir e ver logs
docker compose up

# Parar e remover containers, redes, volumes
docker compose down

# Parar sem remover
docker compose stop

# Ver logs
docker compose logs -f

# Ver status
docker compose ps

# Build das imagens
docker compose build

# Rebuild forçado (sem cache)
docker compose build --no-cache

# Executar comando em um service
docker compose exec app bash
```

---

## 9. Build de Imagens

### 9.1 Dockerfile Básico

```dockerfile
# Imagem base
FROM ubuntu:22.04

# Metadados
LABEL maintainer="seu@email.com"

# Variáveis de ambiente
ENV APP_DIR=/app

# Diretório de trabalho
WORKDIR $APP_DIR

# Copiar arquivos (cache layer otimizado)
COPY package*.json ./
RUN npm install

COPY . .

# Porta exposta (documentação)
EXPOSE 3000

# Comando padrão
CMD ["npm", "start"]
```

### 9.2 Build e Tag

```bash
# Build básico
docker build -t meu-app .

# Build com tag (versionamento)
docker build -t usuario/meu-app:v1.0 .

# Build sem cache
docker build --no-cache -t meu-app .

# Build apontando Dockerfile específico
docker build -f Dockerfile.prod -t meu-app:prod .
```

### 9.3 Enviar para Registry (Docker Hub)

```bash
# Login
docker login

# Tag para o registry
docker tag meu-app:latest usuario/meu-app:v1.0

# Push
docker push usuario/meu-app:v1.0
```

---

## 10. Limpeza e Manutenção

### 10.1 Ver Uso de Disco

```bash
# Resumo do uso de espaço
docker system df

# Detalhado
docker system df -v
```

### 10.2 Limpeza Seletiva

```bash
# Remover containers PARADOS
docker container prune

# Remover imagens NÃO USADAS (dangling)
docker image prune

# Remover TODAS as imagens não usadas por containers
docker image prune -a

# Remover volumes não usados
docker volume prune

# Remover redes não usadas
docker network prune
```

### 10.3 Limpeza Total (CUIDADO!)

```bash
# Remove: containers parados + redes não usadas + imagens dangling + cache de build
docker system prune

# Remove TUDO acima + volumes não usados + imagens não usadas
docker system prune -a --volumes
```

### 10.4 Comandos de Emergência (do material original)

```bash
# Parar TODOS os containers de uma vez
docker stop $(docker ps -aq)

# Parar e remover todos os containers
docker stop $(docker ps -q) && docker container prune

# Remover TODAS as imagens (CUIDADO!)
docker rmi -f $(docker images -a -q)
```

---

## 11. Resolução de Problemas Comuns

### 11.1 Erro "Max Depth Exceeded"

Ocorre ao tentar remover imagens com muitas camadas.

```bash
# Solução: forçar remoção de todas as imagens
docker rmi -f $(docker images -a -q)
```

### 11.2 Container não inicia / erro de porta

```bash
# Verificar se porta já está em uso
netstat -tulpn | grep :8080
# ou
ss -tulpn | grep :8080

# Ver logs do container
docker logs <ID>
```

### 11.3 Permissão negada (socket Docker)

```bash
# Adicionar usuário ao grupo docker
sudo usermod -aG docker $USER
# Fazer logout/login
```

### 11.4 Espaço em disco cheio

```bash
# Ver uso
docker system df

# Limpeza agressiva
docker system prune -a --volumes
```

---

## 12. Boas Práticas

### 12.1 Nomenclatura de Containers

```yaml
# ❌ Ruim (conflito em servidores compartilhados)
container_name: mysql_db_app

# ✅ Bom (genérico, permite múltiplas instâncias)
# Deixe o Docker Compose gerar nomes automáticos
# ou use prefixos: projeto_mysql_db
```

### 12.2 Versionamento de Imagens

```bash
# ❌ Ruim (gera imagens <none>:<none> = "untagged")
docker build -t meu-app .

# ✅ Bom (sempre use tag)
docker build -t usuario/meu-app:v1.0.0 .
docker build -t usuario/meu-app:latest .
```

### 12.3 Limpeza Rotineira

```bash
# Execute periodicamente (ex: cron semanal)
docker image prune -a
docker volume prune
docker network prune
```

### 12.4 Dockerfile Otimizado

```dockerfile
# ✅ Bom: aproveita cache de layers
COPY package*.json ./
RUN npm ci --only=production

COPY . .

# ✅ Use .dockerignore (como .gitignore)
# node_modules
# .git
# *.log
# Dockerfile
# docker-compose.yml
```

### 12.5 Segurança

- Não rode containers como root (use `USER` no Dockerfile)
- Escaneie imagens: `docker scan <imagem>` ou ferramentas como Trivy
- Use imagens base oficiais e mínimas (ex: `alpine`, `distroless`)
- Não coloque segredos no Dockerfile (use secrets/env vars)

---

## 13. Fontes de Consulta

- **Documentação Oficial:** https://docs.docker.com/
- **Docker Hub:** https://hub.docker.com/
- **Referência de Comandos:** https://docs.docker.com/engine/reference/commandline/docker/
- **Guia de Limpeza de Cache:** https://depot.dev/blog/docker-clear-cache
- **Play with Docker (prática online):** https://labs.play-with-docker.com/
- **Docker Curriculum (tutorial interativo):** https://docker-curriculum.com/

---

## Apêndice: Folha de Referência Rápida (Cheat Sheet)

### Container
```bash
docker run -d --name nome -p 8080:80 imagem     # Criar e rodar
docker ps -a                                    # Listar todos
docker stop nome                                # Parar
docker start nome                               # Iniciar
docker restart nome                             # Reiniciar
docker logs -f nome                             # Logs tempo real
docker exec -it nome bash                       # Entrar no container
docker rm -f nome                               # Remover forçado
```

### Imagem
```bash
docker images                                   # Listar
docker pull imagem:tag                          # Baixar
docker rmi imagem:tag                           # Remover
docker build -t user/img:tag .                  # Build
docker push user/img:tag                        # Enviar
```

### Sistema
```bash
docker system df                                # Uso de disco
docker system prune -a --volumes                # Limpeza total
```

### Compose
```bash
docker compose up -d                            # Subir
docker compose down                             # Derrubar
docker compose logs -f                          # Logs
docker compose exec servico bash                # Entrar no serviço
```

---

> **Dica de Professor:** Pratique! Suba um `nginx`, um `mysql`, crie seu próprio `Dockerfile`, brinque com `docker-compose`. O Docker se aprende **fazendo**, não só lendo. Comece simples, evolua para projetos reais.