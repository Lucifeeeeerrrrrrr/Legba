# Passo a Passo

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
