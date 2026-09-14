# Resumo Git

> Material compilado e organizado a partir de: **Apostila Git** (PETEE – UFMG), **GitHub Folha de Dicas de Git** (github-git-cheat-sheet) e **Resumo GIT.docx**.

---

## Sumário

1. [O que é Git?](#1-o-que-é-git)
2. [Instalação e configurações iniciais](#2-instalação-e-configurações-iniciais)
3. [Comandos básicos](#3-comandos-básicos)
4. [Branches](#4-branches)
5. [GitHub](#5-github)
6. [Chave SSH](#6-chave-ssh)
7. [Pasta .git – verificando dados](#7-pasta-git--verificando-dados)
8. [Submódulos](#8-submódulos)
9. [Folha de dicas (cheat sheet)](#9-folha-de-dicas-cheat-sheet)
10. [Exercícios práticos](#10-exercícios-práticos)
11. [GitKraken (interface gráfica)](#11-gitkraken-interface-gráfica)
12. [Bibliografia](#12-bibliografia)

---

## 1. O que é Git?

Git é um **sistema de controle de versões**: gerencia as versões de um arquivo ao longo do tempo, salvando o estado do arquivo para permitir voltar a uma versão anterior caso as alterações não sejam satisfatórias.

**Vantagem:** evita armazenar cópias de vários estados de um arquivo, reduzindo o risco de perder uma versão desejada no futuro.

### Tipos de sistemas de controle de versão

- **Centralizados:** funcionam a partir de um servidor central que recebe novas versões e recupera versões anteriores para os usuários.
- **Distribuídos:** cada usuário tem um servidor local para salvar estados do arquivo; ao atingir uma versão estável, envia as alterações para um servidor central. **Ideal para projetos com muitos colaboradores** – organização e tratamento de erros ficam mais fáceis.

O Git é um sistema de controle de versões **distribuído**, pois organiza os projetos como uma árvore: um "tronco" (projeto principal) e "galhos" (versões em desenvolvimento).

### História

- Criado por **Linus Torvalds** e pela comunidade Linux em **2005**, após a comunidade perder o acesso gratuito ao BitKeeper (ferramenta que se tornou paga por alegação de violação de licença).
- Critérios adotados por Linus no desenvolvimento:
  - Não se parecer com o CVS;
  - Trabalhar com fluxo distribuído;
  - Ser robusto contra corrompimento de arquivos;
  - Ser altamente eficiente.
- Início do desenvolvimento: **3 de abril de 2005**. Já em **29 de abril de 2005** era referência em velocidade.
- Lançamento da versão **1.0** em **21 de dezembro de 2005**.
- Manutenção posteriormente assumida por **Junio Hamano**.
- Hoje é um dos sistemas mais usados no mercado (empresas como Twitter e Yahoo).

---

## 2. Instalação e configurações iniciais

### Conferindo instalações anteriores

```bash
git --version
```

Se retornar um número de versão (ex.: `git version 2.25.1`), o Git já está instalado.

### Windows

1. Acessar [gitforwindows.org](https://gitforwindows.org/).
2. Baixar e executar o instalador.
3. Seguir clicando em **Next** (na dúvida, manter as configurações sugeridas) e finalizar com **Finish**.

### MacOS

1. Acessar o instalador no SourceForge: [git-osx-installer](https://sourceforge.net/projects/git-osx-installer/files/).
2. Escolher a versão mais recente, seguir as instruções e clicar em **Continue**.

### Linux

**Debian/Ubuntu (apt-get):**

```bash
sudo apt-get update
sudo apt-get install git
```

**Fedora (dnf/yum):**

```bash
sudo dnf install git
sudo yum install git
```

### Configurando um usuário

```bash
git config --global user.name "John Stevens"
git config --global user.email "johnstevens@email.com.br"
```

Todos os commits feitos serão creditados a esses dados.

**Verificando os dados configurados:**

```bash
git config user.name
git config user.email
```

---

## 3. Comandos básicos

### 3.1 Inicialização e configuração

| Comando | Função |
|---|---|
| `git init` | Inicializa um novo repositório (primeiro comando básico do versionamento). |
| `git config` | Configura opções de instalação e de usuário. |

```bash
git init
# Initialized empty Git repository in /git_init_petee/.git/
```

### 3.2 Enviar arquivos

#### `git add`

Adiciona mudanças à **área de staging** (prepara o "snapshot" antes do commit).

```bash
git add hello_world.cpp
git status
```

- `git add nome_do_arquivo` – adiciona um arquivo específico.
- `git add .` – adiciona todos os arquivos modificados.
- `git rm --cached <arquivo>` – remove o arquivo da área de staging.

#### `git commit`

Submete as mudanças da area de staging ao repositório.

```bash
git commit -am "Adicionei o arquivo hello_world.cpp"
# [master (root-commit) 380e42d] Adicionei o arquivo hello_world.cpp
# 1 file changed, 6 insertions(+)
# create mode 100644 hello_world.cpp
```

A flag `-am` permite informar uma mensagem que justifica as alterações incluídas.

#### `git fetch`

Baixa commits e arquivos de um repositório remoto, **sem integrá-los** ao repositório local – permite inspecionar as alterações antes do merge.

```bash
git fetch <remote>
```

```bash
git remote add coworkers_repo git@bitbucket.org:coworker/coworkers_repo.git
git fetch coworkers_repo coworkers/feature_branch
```

#### `git pull`

Versão automatizada do `fetch`: baixa a ramificação do remoto e **mescla imediatamente** na branch atual (equivalente ao `svn update`).

```bash
git pull <remote>
```

#### `git push`

Transfere commits do repositório local para o remoto (o oposto do `fetch`).

```bash
git remote add <nome> <url>
git push <nome>
```

#### Arquivos `.gitignore`

Informam ao Git quais arquivos devem ser ignorados no commit (senhas, configurações de IDE, etc.).

```gitignore
senhas.txt
testes/
uploads/
```

**Caracteres coringas:**

| Caractere | Função |
|---|---|
| `*` | Substitui qualquer coisa |
| `?` | Substitui apenas um caractere |
| `[]` | Para intervalos |
| `!` | Para negação |
| `#` | Indica comentário |

**Exemplo:**

```gitignore
# Ignora todos os arquivos .txt
*.txt
# Ignora arquivos que começam com "erro", com qualquer caractere à frente e extensão .log
erro?.log
# Ignora arquivos teste- que terminam com números de 0 a 9 e extensão .log
teste-[0-9].log
# Faz com que o arquivo teste-4.log não seja ignorado
!teste-4.log
```

#### `git tag`

Cria **etiquetas** para estados relevantes do projeto (ex.: versão final ou versão utilizável).

- **Com anotação:**

```bash
git tag -a v1.0 -m 'Minha versão 1.0'
```

- **Sem anotação:**

```bash
git tag v1.1
```

- **Marcando um commit já feito:**

```bash
git tag v1.2 166ae0c4d3f420721acbb115cc33848dfcc2121a
```

- **Localizando o commit desejado:**

```bash
git log --pretty=oneline
```

- **Vendo informações da tag:**

```bash
git show v1.1
```

- **Enviando a tag para o servidor remoto:**

```bash
git push origin v1.1
```

- **Excluindo tag remota / local:**

```bash
git push origin --delete v1.2
git tag -d v1.2
```

### 3.3 Verificar informações

#### `git log`

Explora as revisões passadas do projeto (snapshots commitados).

```bash
git log
```

**Flags úteis:**

| Flag | Função |
|---|---|
| `-p` | Mostra o patch introduzido com cada commit |
| `--stat` | Mostra estatísticas de arquivos modificados em cada commit |
| `--shortstat` | Exibe apenas a linha de alteração/inserção/exclusão do `--stat` |
| `--name-only` | Mostra a lista de arquivos modificados |
| `--name-status` | Mostra a lista de arquivos com status adicionado/modificado/excluído |
| `--relative-date` | Exibe a data em formato relativo (ex.: "2 semanas atrás") |
| `-(n)` | Exibe somente os últimos n commits |
| `--since`, `--after` | Limita os commits após a data especificada |
| `--until`, `--before` | Limita os commits antes da data especificada |
| `--author` | Mostra commits cujo autor corresponde à string |
| `--grep` | Mostra commits com a mensagem contendo a string |

#### `git diff`

Mostra alterações entre a árvore de trabalho, o índice e outras árvores. **Vermelho = excluído; verde = adicionado.**

```bash
git diff
git diff --staged
git diff [primeiro-branch]...[segundo-branch]
```

### 3.4 Receber arquivos

#### `git clone`

Cria uma cópia de um repositório já existente.

```bash
git clone https://github.com/santiagofdavi/git_petee_course
# Cloning into 'git_petee_course' ...
```

### 3.5 Alterar branches

#### `git branch`

Gerencia as branches do repositório (criar, listar, deletar).

```bash
git branch manutencao       # cria a branch
git branch                  # lista as branches
git branch -d manutencao    # deleta a branch
git branch -a               # lista todas as branches (locais e remotas)
git branch --merged main    # lista branches mescladas em main
```

#### `git checkout`

Muda de branch ou volta para algum estado do projeto.

```bash
git checkout manutencao                        # troca de branch
git checkout 166ae0c4d3f420721acbb115cc33848dfcc2121a   # volta para um commit
git checkout tag/v1.1                          # vai para uma tag
```

> **Dica:** para criar uma branch e já trocar para ela, use:
> ```bash
> git checkout -b nome_da_branch
> ```

#### `git merge`

Mescla branches, enviando as alterações de outra branch para a branch atual.

```bash
git checkout main
git merge manutencao
```

**Conflitos de merge** acontecem quando há alterações nas mesmas partes de um documento (ou quando um arquivo é excluído numa branch e alterado em outra). O Git insere marcadores:

```
<<<<<<< HEAD
std::cout << "Hello World! :)" << std::endl;
=======
std::cout << "Olá mundo!" << std::endl;
>>>>>>> manutencao
```

- `HEAD` = branch atual; o outro nome = branch mesclada.
- Resolva manualmente, depois faça o commit:

```bash
git add hello_world.cpp
git commit -m "Resolução de conflito"
```

#### `git rebase`

Move branches, evitando *merge commits* desnecessários e criando **histórico linear**. Vai ao ancestral comum dos dois branches, salva os diffs, redefine o branch atual e reaplica cada mudança.

```bash
git checkout experiment
git rebase master
# First, rewinding head to replay your work on top of it ...
# Applying: added staged command
```

#### `git stash`

Arquiva **alterações não commitadas**, voltando ao estado do último commit e guardando as alterações adicionais.

```bash
git stash
# Saved working directory and index state WIP on main: 8117923 WIP
```

**Flags:**

| Comando | Função |
|---|---|
| `git stash list` | Lista todos os stashes guardados |
| `git stash pop` | Restaura as modificações do último stash |
| `git stash apply` | Aplica alterações sem deletar a referência |
| `git stash show` | Resume as alterações do último stash |
| `git stash show -p` | Mostra as alterações completas do último stash |
| `git stash drop` | Descarta os conjuntos de alterações mais recentes |

### 3.6 Desfazer alterações

```bash
git checkout -- nome_do_arquivo              # desfaz mudanças em um arquivo (antes do commit)
git restore --staged nome_do_arquivo         # remove do staging preservando conteúdo
git reset nome_do_arquivo                     # remove arquivo do stage (antes do commit)
git reset                                     # retira do staging todos os arquivos adicionados com git add
git reset [commit]                            # desfaz commits depois de [commit], preservando mudanças locais
git reset --hard [commit]                     # descarta todo histórico e mudanças até o commit especificado
```

---

## 4. Branches

Uma **branch** é uma linha paralela de desenvolvimento que isolara alterações e permite trabalhar em funcionalidades, correções ou experimentos sem afetar o código principal (`main`/`master`).

### Conceito

Cada branch é uma cópia do estado atual do projeto em um ponto específico. Você faz alterações nela e, quando satisfeito, mescla (`merge`) de volta para a branch principal – sem comprometer o código estável até que tudo esteja pronto.

### Como usar

1. **Feature branches** – implementar novas funcionalidades sem interferir no código existente:

```bash
git checkout -b feature-nova-funcionalidade
# ... alterações e commits ...
git checkout main
git merge feature-nova-funcionalidade
```

2. **Bugfix branches** – resolver bugs rapidamente sem interferir no trabalho em andamento:

```bash
git checkout -b bugfix-descricao-do-bug
# ... correções ...
git merge bugfix-descricao-do-bug
```

3. **Branches experimentais** – testar ideias que talvez não entrem no projeto final:

```bash
git checkout -b experimento
```

4. **Branches colaborativas** – cada membro trabalha em sua própria branch; ao finalizar, mescla-se na branch principal após revisão.

### Boas práticas

- Nomeie branches de forma **descritiva** (`feature-login`, `bugfix-header`).
- Mantenha branches **pequenas e focadas** (uma tarefa por branch).
- **Atualize a branch principal antes de mesclar** para evitar conflitos: `git pull origin main`.

### Fluxo de merge no Main

```bash
git checkout main
git pull origin main
git merge "branch trabalhada"
git push origin main
```

### Excluir branches

```bash
git branch --merged main                 # listar branches mescladas em main
git branch -d 'branch_excluir'           # deletar branch local (seguro)
git push origin --delete 'branch_excluir' # deletar branch remota
git push origin :feature-erp-user        # alternativa
```

### Benefícios

- **Isolamento:** mudanças ficam isoladas até estarem prontas.
- **Colaboração:** vários desenvolvedores sem conflito.
- **Controle de versão:** erro em uma branch não afeta o código principal.
- **Facilidade de teste:** novas ideias sem comprometer o código estável.

### Comandos de branches

```bash
git branch                     # listar branches locais
git fetch                      # atualizar informações dos branches remotos
git branch -a                  # listar branches locais e remotas
git fetch --prune              # sincronizar referências locais (remove branches excluídas no remoto)
git fetch origin               # atualizar o branch local para corresponder ao remoto
git reset --hard origin/master # reiniciar o branch local exatamente como o remoto (descarta mudanças)
git branch -d nome-da-branch   # excluir branch localmente
git push origin --delete nome-da-branch  # excluir branch no GitHub (remota)
```

---

## 5. GitHub

### Função

O GitHub é uma **plataforma web de hospedagem de arquivos** que usa o Git. Facilita o trabalho em equipe (qualquer integrante com internet acessa o projeto) e funciona também como rede social (seguir pessoas, ver trabalhos e interagir).

### Cadastro

1. Acessar [github.com](https://github.com/) e selecionar **Sign up**.
2. Informar e-mail e senha.
3. Escolher um nome de usuário.
4. Confirmar o e-mail com o código enviado.
5. Responder às perguntas sobre o objetivo de uso.

### Criação de repositórios

1. Clicar no botão verde **Create repository** (canto superior esquerdo da tela inicial).
2. Preencher as configurações iniciais do repositório.
3. Selecionar **Create repository** novamente.

### Gerenciamento de repositórios

Abas principais:

- **Code** – edição e cópia dos arquivos do repositório (não é a forma mais rápida; terminal é melhor).
- **Issues** – sugestões de melhoria do projeto (em repositórios públicos, pessoas de fora podem sugerir).
- **Pull request** – requisições de merge para uma branch; permite revisão do código antes do merge.
- **Settings** – alterações nas configurações do repositório (nome, foto, acesso, exclusão etc.).

### Perfil

Acesse o ícone da foto de perfil (canto superior direito) e selecione **Your Profile**. É possível editar foto, nome, bio, local, website e redes sociais, além de ver repositórios e histórico de atividades.

---

## 6. Chave SSH

Necessária porque o GitHub está descontinuando o clone por HTTPS; o `git clone` via SSH torna-se a forma mais simples.

**Gerar chave (Windows, MacOS ou Linux):**

```bash
ssh-keygen -t ed25519 -C "seu_email@exemplo.com"
```

- Escolher o diretório/nome (recomendado: pressionar **Enter** para o padrão).
- Criar uma senha ou deixar sem senha (Enter duas vezes).

**Windows – registrar no SSH agent:**

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/nome_da_chave
```

**Copiar a chave pública:**

```bash
# MacOS
pbcopy < ~/.ssh/nome_da_chave.pub
# Windows
clip < ~/.ssh/nome_da_chave.pub
# Linux
cat ~/.ssh/id_ed25519.pub
```

**Adicionar no GitHub:**

1. Clicar na foto de perfil (canto superior direito) → **Settings**.
2. Procurar **SSH and GPG Keys** → **New SSH Key**.
3. Dar um nome para a chave e colar a chave pública.

---

## 7. Pasta .git – verificando dados

Comandos úteis para inspecionar o repositório **sem abrir a pasta .git**:

```bash
git remote -v        # URL do repositório remoto (origin) para fetch e push
git branch -a        # branch atual + branches locais e remotas
git config --list    # configurações do repositório (usuário, e-mail, origem remota)
git log              # histórico de commits
```

Conteúdo interno da pasta `.git`:

| Arquivo/Pasta | Conteúdo |
|---|---|
| `.git/config` | Configuração do repositório (inclui o remoto `origin`) |
| `.git/refs/heads/` | Branches locais |
| `.git/refs/remotes/` | Branches remotas |
| `.git/HEAD` | Aponta para a branch atual |

> **Atenção:** editar arquivos dentro da pasta `.git` pode causar inconsistências. Prefira sempre os comandos do Git.

---

## 8. Submódulos

Referência: [The git submodule add example](https://www-theserverside-com.translate.goog/blog/Coffee-Talk-Java-News-Stories-and-Opinions/The-git-submodule-add-example)

Submódulos permitem incluir um repositório dentro de outro, mantendo o vínculo com versões específicas.

Exemplo de adição:

```bash
git submodule add <url-do-repositorio>
```

*(Consultar a referência completa para opções e fluxo detalhado.)*

---

## 9. Folha de dicas (cheat sheet)

### Configurar a ferramenta

```bash
git config --global user.name "[nome]"
git config --global user.email "[endereco-de-email]"
git config --global color.ui auto
```

### Criar repositórios

```bash
git init [nome-do-projeto]
git clone [url]
```

### Fazer mudanças

```bash
git status                     # lista arquivos novos/modificados a commitar
git diff                       # mostra diferenças não versionadas
git add [arquivo]              # faz o snapshot do arquivo (staging)
git diff --staged              # mostra diferenças entre arquivos selecionados e a última versão
git reset [arquivo]            # deseleciona o arquivo preservando o conteúdo
git commit -m "[mensagem descritiva]"  # grava o snapshot no histórico
```

### Mudanças em grupo

```bash
git branch                     # lista branches locais
git branch [nome-do-branch]    # cria um novo branch
git checkout [nome-do-branch]  # muda para o branch e atualiza o diretório de trabalho
git merge [branch]             # combina o histórico do branch no branch atual
git branch -d [nome-do-branch] # exclui o branch
```

### Refatorar nomes dos arquivos

```bash
git rm [arquivo]               # remove do diretório de trabalho e seleciona para remoção
git rm --cached [arquivo]      # remove do controle de versão preservando localmente
git mv [arquivo-original] [arquivo-renomeado]  # renomeia e seleciona para o commit
```

### Suprimir o rastreamento

`.gitignore` suprime o versionamento acidental de arquivos/pastas correspondentes aos padrões:

```gitignore
*.log
build/
temp-*
```

```bash
git ls-files --other --ignored --exclude-standard   # lista arquivos ignorados
```

### Revisar histórico

```bash
git log                                   # histórico de versões do branch atual
git log --follow [arquivo]               # histórico de versões de um arquivo (inclui renames)
git diff [primeiro-branch]...[segundo-branch]  # diferença de conteúdo entre branches
git show [commit]                        # metadata e conteúdo do commit
```

### Desfazer commits

```bash
git reset [commit]          # desfaz commits depois de [commit], preservando mudanças locais
git reset --hard [commit]   # descarta todo histórico e mudanças até o commit
```

### Salvar fragmentos (stash)

```bash
git stash          # armazena temporariamente arquivos rastreados modificados
git stash pop      # restaura os arquivos recentes em stash
git stash list     # lista conjuntos de alterações em stash
git stash drop     # descarta o conjunto mais recente de alterações
```

### Sincronizar mudanças

```bash
git fetch [marcador]                 # baixa histórico de um marcador de repositório
git merge [marcador]/[branch]        # combina o marcador do branch no branch local
git push [alias] [branch]            # envia commits do branch local para o GitHub
git pull                             # baixa o histórico e incorpora as mudanças
```

---

## 10. Exercícios práticos

Site: [Git Exercises](https://gitexercises.fracz.com/) – 23 exercícios (recomendados ao menos os 8 primeiros).

### Configurações iniciais

```bash
git clone https://gitexercises.fracz.com/git/exercises.git
cd exercises
git config user.name "Your name here"
git config user.email "Your e-mail here"
./configure.sh
git start
```

### Comandos da plataforma

```bash
git start nome-do-exercicio   # inicia um exercício
git verify                     # corrige a atividade
git start next                 # inicia o exercício seguinte
```

> **Atenção:** `git start` e `git verify` pertencem apenas à plataforma Git Exercises, não ao Git.

### Lista dos exercícios (com dicas)

1. **Push a commit you have made (master)** – `git start` + `git verify`.

2. **Commit one file (commit-one-file)** – commit de apenas um dos arquivos (A.txt ou B.txt). *Dica:* `git add` + `git commit`.

3. **Commit one file of two currently staged (commit-one-file-staged)** – ambos estão no staging; commit de apenas um. *Dica:* use `git reset HEAD <arquivo>` para remover do staging.

4. **Ignore unwanted files (ignore-them)** – ignorar extensões `exe`, `o`, `jar` e o diretório `libraries`. *Dica:* crie um `.gitignore`, insira as regras e faça o commit.

5. **Chase branch that escaped (chase-branch)** – fazer `chase-branch` apontar para o mesmo commit que `escaped`. *Dica:* `git merge`.

6. **Resolve a merge conflict (merge-conflict)** – fundir `Another-piece-of-work` na branch atual e resolver o conflito. *Dica:* resolva manualmente, `git add`, `git commit`.

7. **Saving your work (save-your-work)** – corrigir um bug e retornar ao trabalho. *Dica:* `git stash` → corrigir → `git stash pop` → terminar trabalho.

8. **Change branch history (change-branch-history)** – histórico como se tivesse começado após corrigir o bug. *Dica:* `git rebase`.

### Gabaritos

1. **master**

```bash
git start
git verify
```

2. **commit-one-file**

```bash
git add A.txt   # ou git add B.txt
git commit -m "add one file"
git verify
```

3. **commit-one-file-staged**

```bash
git reset HEAD A.txt   # ou git reset HEAD B.txt
git commit -m "destage one file"
git verify
```

4. **ignore-them**

```bash
nano .gitignore
# *.exe
# *.o
# *.jar
# libraries/
git add .
git commit -m "commit useful files"
git verify
```

5. **chase-branch**

```bash
git checkout chase-branch
git merge escaped
git verify
```

6. **merge-conflict**

```bash
git checkout merge-conflict
git merge another-piece-of-work
nano equation.txt   # resolver: 2 + 3 = 5
git add equation.txt
git commit -m "merge and resolve"
git verify
```

7. **save-your-work**

```bash
git stash                       # ou git stash push
nano bug.txt                    # excluir a linha do bug
git commit -am "remove bug"
git stash apply                 # ou git stash apply stash@{0}
nano bug.txt                    # adicionar "Finally, finished it!" ao final
git commit -am "finish"
git verify
```

8. **change-branch-history**

```bash
git checkout change-branch-history
git rebase hot-bugfix
git verify
```

---

## 11. GitKraken (interface gráfica)

### Função

Interface gráfica para Git que permite observar de forma **intuitiva** o status do repositório, commits e branches. Ex.: ao commitar, mostra mudanças em vermelho (código antigo) e verde (código atual), com clique em um botão para criar nova versão.

### Instalação

Gratuito em [www.gitkraken.com](https://www.gitkraken.com).

### Integração com o GitHub

É possível conectar ao GitHub (e também BitBucket, GitLab, Azure DevOps) para acessar/clonar repositórios remotos ou fazer *fork*. Com a conexão: **Pull** traz novas versões; **Push** envia alterações.

### Utilização

Tela inicial: **Open a repo** (abrir repositório já clonado), **Clone a repo** (clonar), **Start a local repo** (criar novo).

- **Abrir um repositório:** `Open a repo` → selecionar a pasta. *Dica:* com o botão direito na pasta, escolher "abrir com GitKraken".
- **Clonar repositório:** `Clone a repo` → clonar por URL ou por plataforma de hospedagem.
- **Criar repositório:** `Start a local repo` → criar na própria máquina ou em plataforma de hospedagem.

---

## 12. Bibliografia

- Chacon, Scott, and Ben Straub. **"Pro Git"**. Springer Nature, 2014.
- Blischak, John D., Emily R. Davenport, and Greg Wilson. **"A quick introduction to version control with Git and GitHub."** PLoS computational biology 12.1 (2016).
- **Apostila Git** – Programa de Educação Tutorial – Engenharia Elétrica (PETEE – UFMG), 2023. Autores: Davi Ferreira Santiago, Felipe Meireles Leonel, João Vitor Saade Simão.
- **GitHub Folha de Dicas de Git**, v1.1.1, GitHub Training Team.