# Guia Completo de Autenticação SSH com Chaves

## Como funciona?

A autenticação por chave SSH substitui a senha. Funciona assim:

```
Seu computador (chave privada) ←→ Servidor remoto (chave pública)
```

- **Chave pública** (`.pub`): fica no servidor. É como uma "fechadura".
- **Chave privada**: fica só na sua máquina. É a "chave" que abre a fechadura.

---

## Os arquivos padrão

O nome **padrão** que o SSH procura é:

| **Arquivo** | **Função** |
|-------------|------------|
| `id_ed25519` | Chave privada (recomendada Ed25519) |
| `id_ed25519.pub` | Chave pública |

Quando você executa `ssh usuario@servidor`, o SSH automaticamente tenta usar `~/.ssh/id_ed25519`.

---

## Criando uma chave Ed25519 personalizada

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_digitenome -C "digitenome"
```

## Copiar a chave pública criada para o servidor

```bash
ssh-copy-id -i ~/.ssh/id_ed25519_digitenome.pub -p porta usuario@servidor
```

---

## Posso mudar o nome dos arquivos?

**Sim!** Você pode renomear à vontade. Exemplos de nomes personalizados:

- `buchenavia_rsa`
- `hostoo_toth`
- `id_sugesp`
- `ssh-key-2026-08-04`

**O problema:** se não for o nome padrão, o SSH não encontra automaticamente. Você precisa indicar qual chave usar. Existem **2 formas**:

### Forma 1: flag `-i` (manual)

```bash
ssh -i ~/.ssh/buchenavia_rsa usuario@servidor
```

Funciona, mas é chato digitar toda vez.

### Forma 2: arquivo `config` (recomendado)

O arquivo `~/.ssh/config` resolve tudo. Exemplo de bloco:

```
Host hostoo                  ← apelido
    Hostname 108.181.92.69   ← endereço do servidor
    User tavjrxt4            ← usuário no servidor
    Port 45363               ← porta (não-padrão)
    IdentityFile ~/.ssh/hostoo_toth   ← QUAL chave usar
```

> No caso, o bloco que **não** tem `IdentityFile` usa a chave padrão `id_ed25519`.

---

## Perguntas comuns

**1. Preciso renomear para o padrão?**

Não! Com o `IdentityFile` no `config`, o nome pode ser o que quiser.

**2. Posso ter várias chaves?**

Sim — uma por servidor/contexto. Cada bloco `Host` aponta para sua própria chave.

**3. Como criar uma chave com nome customizado?**

```bash
ssh-keygen -t ed25519 -f ~/.ssh/minha_chave -C "seu-email@exemplo.com"
```

Depois adicione no `config`:

```
Host servidor
    IdentityFile ~/.ssh/minha_chave
```

---

## Passo a passo para testar agora

1. **Copie a chave pública** para um servidor (1 vez por servidor):

```bash
ssh-copy-id -i ~/.ssh/hostoo_toth.pub -p 45363 tavjrxt4@108.181.92.69
```

2. **Conecte** usando o apelido:

```bash
ssh hostoo
```

---

## Como testar / inspecionar sua chave SSH

**1. Ver se a chave existe**

```bash
ls -l ~/.ssh/
```

**2. Ver o conteúdo da chave pública (seguro — pode compartilhar)**

```bash
cat ~/.ssh/hostoo_toth.pub
```

Serve para coletar e colar no servidor.

**3. Ver as informações da chave (tipo, tamanho, impressão digital)**

```bash
ssh-keygen -lf ~/.ssh/hostoo_toth
```

Saída do tipo: `256 SHA256:xxxx usuario@host (ED25519)`

**4. Usar a chave para login (testar de fato)**

```bash
ssh -i ~/.ssh/hostoo_toth usuario@servidor
```

Ou, se tiver no `config`: `ssh hostoo`

**5. Saber se existe senha (passphrase) na chave privada**

```bash
ssh-keygen -y -P "" -f ~/.ssh/hostoo_toth
```

- **Funciona / imprime a chave pública** → **SEM senha**
- **Pede a senha ou dá erro** → **TEM senha**

**6. Retirar a senha (passphrase) da chave**

```bash
ssh-keygen -p -P "senha_atual" -N "" -f ~/.ssh/hostoo_toth
```

- `-p`: alterar passphrase
- `-P "senha_atual"`: a senha que está hoje (se não tiver, use `-P ""`)
- `-N ""`: nova senha vazia (= remove a senha)

Se quiser trocar a senha por outra (em vez de remover):

```bash
ssh-keygen -p -P "senha_atual" -N "nova_senha" -f ~/.ssh/hostoo_toth
```

> **⚠️ Segurança:** se a chave não tiver senha e roubarem o arquivo, quem tiver ele entra no servidor. Recomendo **ter** senha na chave (mas aí precisa digitá-la uma vez por sessão, ou usar `ssh-agent`).

---

## Como saber qual chave é a correta? (comparação)

Quando você tem várias chaves e não sabe qual corresponde a cada servidor, use estes métodos:

**1. Qual chave para cada servidor? (veja o `config`)**

```bash
cat ~/.ssh/config
```

Cada bloco `Host` com `IdentityFile` mostra a chave daquele servidor. Bloco **sem** `IdentityFile` usa a **padrão** (`id_ed25519`).

**2. Impressão digital (fingerprint) da sua chave local**

```bash
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

Saída do tipo: `256 SHA256:AbCdEf... usuario@host (ED25519)`

**3. Sua chave está registrada no servidor? (comparação direta)**

```bash
diff <(ssh-keygen -lf ~/.ssh/id_ed25519.pub) <(ssh usuario@servidor 'ssh-keygen -lf ~/.ssh/authorized_keys')
```

- **Nenhuma diferença** na saída → a chave **está** no servidor (é a correta).

**4. Qual chave funciona de fato? (teste prático)**

Teste cada chave **forçando** o uso dela apenas, ignorando o agente:

```bash
ssh -i ~/.ssh/id_ed25519 -o "IdentitiesOnly=yes" usuario@servidor -p 45363
ssh -i ~/.ssh/hostoo_toth -o "IdentitiesOnly=yes" usuario@servidor -p 45363
```

- `-o "IdentitiesOnly=yes"`: usa **só** aquela chave (ignora ssh-agent).
- A que **conectar sem pedir senha** é a correta.

**5. Quais chaves estão carregadas no `ssh-agent`?**

```bash
ssh-add -l
```

Mostra os fingerprints das chaves ativas — compare com o fingerprint local.

### Tabela-resumo

| **Pergunta** | **Comando** |
|--------------|-------------|
| Qual chave para cada servidor? | `cat ~/.ssh/config` |
| Fingerprint da minha chave? | `ssh-keygen -lf ~/.ssh/id_ed25519.pub` |
| Essa chave está no servidor? | `diff <(...local) <(ssh server 'ssh-keygen -lf ~/.ssh/authorized_keys')` |
| Qual chave funciona de fato? | `ssh -i <chave> -o IdentitiesOnly=yes user@host` |

> **Dica:** o método mais confiável na prática é o **item 4** (testar cada chave com `IdentitiesOnly=yes`).

---

## Permissões de arquivo (o SSH é rígido!)

### A mensagem de erro "UNPROTECTED PRIVATE KEY FILE"

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@ WARNING: UNPROTECTED PRIVATE KEY FILE!                  @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for 'id_ed25519' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "id_ed25519": bad permissions
```

**O que significa:** suas permissões estão "abertas demais" (outros usuários podem ler o arquivo). Por segurança, o SSH **recusa** usar a chave e você não loga.

- **0644** = `rw-r--r--` → qualquer usuário do sistema consegue **ler**.

### Regra de ouro

| **Arquivo/pasta** | **Permissão** | **Significado** |
|-------------------|----------------|------------------|
| `~/.ssh/` | 700 | só o dono acessa a pasta |
| chave privada (`id_ed25519`) | 600 | só o dono lê/escreve |
| chave pública (`id_ed25519.pub`) | 644 | leitura para todos (ok, é pública) |
| `authorized_keys` (no servidor) | 600 | só o dono lê/escreve |

### Como corrigir

```bash
chmod 700 ~/.ssh                  # pasta
chmod 600 ~/.ssh/id_ed25519       # chave privada
chmod 644 ~/.ssh/id_ed25519.pub   # chave pública
```

> **Regra:** privada = **600** (nunca 644/777), pública pode ser **644**. Se aparecer a mensagem de "too open", ajuste a privada para **600**.

---

## Como instalar a chave pública no servidor?

Colar em um arquivo chamado `id_ed25519.pub` **no servidor não funciona** — o servidor só lê o arquivo `~/.ssh/authorized_keys`. O processo:

1. Você conecta → envia sua chave pública
2. O servidor compara com o que está em `authorized_keys`
3. Se bater, desafia você a provar que tem a **privada** correspondente
4. Você responde (automaticamente) → logado

### Opção A: comando (recomendado)

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub -p 45363 usuario@servidor
```

Faz tudo sozinho: cria o `.ssh`, cria `authorized_keys`, ajusta permissões.

### Opção B: manual

1. Entre no servidor com senha:

```bash
ssh -p 45363 usuario@servidor
```

2. No servidor, rode:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
echo "COLE_AQUI_SUA_CHAVE_PUBLICA" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

> `>>` **adiciona** ao final (não sobrescreve) — permite várias chaves.

---

## Como remover o uso de uma chave?

### Remover localmente (sua máquina)

```bash
rm ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub
```

Apaga a chave do seu computador — mas o servidor **ainda** terá sua pública em `authorized_keys` (só você não loga mais).

### Revogar o acesso no servidor também

Entre no servidor e apague a linha da sua chave em `authorized_keys`:

```bash
ssh usuario@servidor
# no servidor:
vim ~/.ssh/authorized_keys   # apague a linha da sua chave
```

Ou esvazie o arquivo por completo:

```bash
> ~/.ssh/authorized_keys
```

### Cuidados antes de excluir

1. **Backup antes de apagar:**

```bash
cp ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub ~/backup_ssh_$(date +%F)/
```

2. **Remova do `ssh-agent`** se estiver carregada:

```bash
ssh-add -d ~/.ssh/id_ed25519
```

3. **Confira o `config`** para não excluir a chave em uso por engano (você tem várias: `hostoo_toth`, `id_sugesp`, etc.).

---

## O que é o ssh-agent?

O `ssh-agent` é um programa que fica **rodando em segundo plano** no seu computador e **guarda suas chaves privadas na memória** (já "desbloqueadas" com a senha). Assim, você digita a senha **uma única vez** e não precisa mais toda vez que conectar.

**A analogia:** imagine um segurança (o ssh-agent) que guarda suas carteiras de chaves já abertas. Enquanto a sessão estiver aberta, você pede a carteira a ele (sem digitar a senha) em vez de abrir do zero.

### Fluxo

```
Você (digita a senha UMA vez)
        ↓
ssh-agent (guarda a chave já desbloqueada na memória)
        ↓
ssh → pergunta ao ssh-agent → conecta sem pedir senha
```

### Comandos essenciais

```bash
# 1. Iniciar o agente
eval "$(ssh-agent -s)"

# 2. Adicionar uma chave
ssh-add ~/.ssh/hostoo_toth

# 3. Ver quais chaves estão carregadas
ssh-add -l

# 4. Remover uma chave
ssh-add -d ~/.ssh/hostoo_toth

# 5. Remover TODAS as chaves
ssh-add -D

# 6. Encerrar o agente
ssh-agent -k
```

> `ssh-add -t 3600` carrega a chave com validade: expira automaticamente após N segundos (aqui 1h). Bom para segurança.

### Dica: ativar no login (Linux/macOS)

Adicione ao `~/.bashrc` ou `~/.zshrc`:

```bash
eval "$(ssh-agent -s)" > /dev/null
ssh-add ~/.ssh/hostoo_toth
```

Assim o agente inicia e carrega a chave automaticamente ao abrir o terminal. No macOS, é ainda mais automático: `ssh-add --apple-use-keychain`.

---

## Resumo visual

```
~/.ssh/
├── id_ed25519            ← chave padrão (automática)
├── id_ed25519.pub
├── hostoo_toth           ← chave personalizada
├── hostoo_toth.pub
├── config                ← "controlador": diz qual chave usar em cada servidor
└── known_hosts           ← registro de servidores já acessados (segurança)
```