# Passo a passo

Remap Caps Lock → Ctrl (funciona em todos os TTYs e X/Wayland)

```bash
# Instalar pacote de remapeamento
sudo pacman -S interception-caps2esc

# Criar arquivo de configuração com echo
sudo bash -c 'cat > /etc/udevmon.yaml <<EOF
- JOB: "intercept -g \$DEVNODE | caps2esc -m 1 | uinput -d \$DEVNODE"
  DEVICE:
    EVENTS:
      EV_KEY: [KEY_CAPSLOCK]
EOF'

# Criar serviço systemd com echo
sudo bash -c 'cat > /etc/systemd/system/udevmon.service <<EOF
[Unit]
Description=udevmon CapsLock→Ctrl
Wants=systemd-udev-settle.service
After=systemd-udev-settle.service

[Service]
ExecStart=/usr/bin/nice -n -20 /usr/bin/udevmon -c /etc/udevmon.yaml

[Install]
WantedBy=multi-user.target
EOF'

# Ativar e iniciar serviço
sudo systemctl enable --now udevmon.service

```

Snap com AppArmor otimizado:

```bash
sudo pacman -S snapd
sudo systemctl enable --now snapd.socket
sudo systemctl enable --now snapd.apparmor.service
```

Ativar AppArmor no GRUB

```bash
# Adiciona os parâmetros no GRUB de forma automática
sudo sed -i 's/GRUB_CMDLINE_LINUX="\(.*\)"/GRUB_CMDLINE_LINUX="\1 apparmor=1 security=apparmor"/' /etc/default/grub

# Atualiza o GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

