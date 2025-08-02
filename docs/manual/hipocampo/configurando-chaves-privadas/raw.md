# Raw

Perfeito, Pedro. Vamos montar um `.sh` que:

1. Instala o `git` se ainda não estiver instalado.
2. Verifica se o arquivo `id_ed25519` existe no `~/Downloads`.
3. Cria a pasta `~/.ssh` (se não existir).
4. Move o arquivo para a pasta correta.
5. Define as permissões corretas (pra segurança).
6. Adiciona ao `ssh-agent` (opcional, mas útil).
7. (Opcional) Configura o `config` do SSH com alias.

***

### 🧠 Neuroanalogia rápida:

* `~/.ssh/id_ed25519` = **seu neurônio-assinatura digital**
* `ssh-agent` = **sinapse que mantém a chave ativada**
* `config` = **roteador neural que diz “quem é quem”**

***

### ✅ Script `setup_ssh_key.sh`

Crie um arquivo com esse conteúdo:

```bash
#!/bin/bash

echo "🔧 Instalando Git..."
sudo pacman -S --noconfirm git || { echo "❌ Erro ao instalar git"; exit 1; }

echo "📁 Verificando chave em ~/Downloads..."
KEY_FILE=~/Downloads/id_ed25519
DEST_DIR=~/.ssh

if [ ! -f "$KEY_FILE" ]; then
  echo "❌ Chave não encontrada em $KEY_FILE"
  exit 1
fi

echo "🗂️ Criando diretório ~/.ssh (se necessário)..."
mkdir -p "$DEST_DIR"

echo "🚚 Movendo chave privada..."
mv "$KEY_FILE" "$DEST_DIR/id_ed25519"

echo "🔐 Ajustando permissões..."
chmod 700 "$DEST_DIR"
chmod 600 "$DEST_DIR/id_ed25519"

echo "🧠 Iniciando ssh-agent e adicionando chave..."
eval "$(ssh-agent -s)"
ssh-add "$DEST_DIR/id_ed25519"

# (Opcional) Configurar alias no SSH
SSH_CONFIG="$DEST_DIR/config"
if ! grep -q "Host github.com" "$SSH_CONFIG" 2>/dev/null; then
  echo "🔧 Configurando ~/.ssh/config..."
  cat <<EOF >> "$SSH_CONFIG"
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519
EOF
  chmod 600 "$SSH_CONFIG"
fi

echo "✅ Setup concluído com sucesso!"
```

***

### 🚀 Como usar

1.  Salve com nome `setup_ssh_key.sh`:

    ```bash
    nano setup_ssh_key.sh
    ```
2.  Torne executável:

    ```bash
    chmod +x setup_ssh_key.sh
    ```
3.  Rode:

    ```bash
    ./setup_ssh_key.sh
    ```

***

Perfeito, Pedro. Vamos montar **um sistema leve e offline** que transforma seu notebook com i3 em um _neuroagente simbólico de depuração_, usando um modelo da DeepSeek localmente (sem API nem internet), com ativação via comando especial.

***

### ⚙️ VISÃO GERAL DO QUE VAMOS MONTAR

| Componente                 | Função                    | Analogia Neural             |
| -------------------------- | ------------------------- | --------------------------- |
| LLM Local (DeepSeek-Coder) | Geração de sugestões      | Córtex preditivo            |
| Script `debug_on`          | Ativação do agente        | Sinal neurológico de alerta |
| Monitor de terminal        | Captura de erros do shell | Percepção sensorial         |
| Correção automática        | Sugestão baseada na LLM   | Sinapse de resposta reflexa |

***

### 🧠 PRÉ-REQUISITOS (instalar 1x)

Antes de tudo, instale:

```bash
sudo pacman -S python3 git wget unzip
```

E o **LM Studio AppImage** (roda sem instalar):

```bash
cd ~/Downloads
wget https://api.lmstudio.ai/download/latest/linux -O lmstudio.AppImage
chmod +x lmstudio.AppImage
./lmstudio.AppImage &
```

No LM Studio:

1. Vá em **Models**
2. Procure por `deepseek-coder`
3. Baixe o modelo `deepseek-coder-1.3b-base.Q4_K_M.gguf`
4. Ele será salvo em: `~/.cache/lm-studio/models/...`

***

### ⚒️ SCRIPT: `debug_on`

Esse script ativa o modo “Lucifuge Correção Assistida™” — monitora comandos e saídas, manda para a LLM, e retorna sugestões offline.

#### ✅ Instale o script

Crie o arquivo:

```bash
nano ~/debug_on.sh
```

Cole isso:

```bash
#!/bin/bash

MODEL_PATH="$HOME/.cache/lm-studio/models/TheBloke_deepseek-coder-1.3b-base.Q4_K_M.gguf"
PROMPT_FILE="/tmp/ghost_input.txt"

echo "🧠 Ativando Modo Debug Assistido com DeepSeek..."
echo "Digite seu comando (ou cole um erro) e pressione ENTER."
echo "Para sair, digite 'exit'"

while true; do
  echo -ne "\n🔍 Comando ou erro > "
  read input

  if [[ "$input" == "exit" ]]; then
    echo "🧠 Modo debug desativado."
    break
  fi

  echo "### Corrija o seguinte erro ou comando, mantendo foco em terminal Linux simples:
$input" > "$PROMPT_FILE"

  # Executa o modelo local com o prompt
  ~/lmstudio.AppImage infer \
    --model "$MODEL_PATH" \
    --prompt-file "$PROMPT_FILE" \
    --n-predict 300
done
```

Salve e torne executável:

```bash
chmod +x ~/debug_on.sh
```

***

### 🧪 COMO USAR

1. Abra o LM Studio e deixe rodando.
2.  No terminal:

    ```bash
    ./debug_on.sh
    ```
3.  Digite algo como:

    ```bash
    fatal: not a git repository (or any of the parent directories): .git
    ```

E o Lucifuge Neural Agent™ responde com sugestões práticas de correção, **sem conexão com internet**.

***

### 🩻 FUTURO: versão com `zsh` hook automático

Quer que eu monte a versão onde ele escuta todos os comandos executados e responde **automaticamente ao detectar falha (código ≠ 0)**?

Isso é possível com:

* Hook de shell `zsh`
* Redirecionamento de stderr para pipe
* Buffer temporário para a LLM local

***

Confirma se você quer esse modo "**auto-correção stealth**" acoplado ao terminal (como se fosse um segundo cérebro embutido).\
Ou prefere manter o modo manual por enquanto?
