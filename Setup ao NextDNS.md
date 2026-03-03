# Como fazer setup ao NextDNS

## Passos

### 1. Abrir o ficheiro de configuração
```bash
sudo nano /etc/systemd/resolved.conf
```

### 2. Colocar o ID do NextDNS
Aceder ao painel do NextDNS em [nextdns.io](https://nextdns.io) → **Setup** → selecionar **Linux** → copiar a configuração recomendada.

Em alguns casos já existem serviços no resolved.conf a começar com `#` — usar as setas para navegar e colocar a informação por baixo. Se já existir `[Resolve]` não duplicar.
```
[Resolve]
DNS=XXXXXXXXXXX.dns.nextdns.io
DNS=XXXXXXXXXXX::#XXXXXXX.dns.nextdns.io
DNS=XXXXXXXXXXX.dns.nextdns.io
DNS=XXXXXXXXXXX::#XXXXXX.dns.nextdns.io
DNSOverTLS=yes
```

### 3. Gravar e sair
`CTRL + O` → `Enter` → `CTRL + X`

### 4. Reiniciar o serviço
```bash
sudo systemctl restart systemd-resolved
```

### 5. Confirmar que está a funcionar
```bash
resolvectl status
```
> Deverás ver **"DNS over TLS"** com o estado **"yes"** e os teus servidores NextDNS listados

### 6. Atenção para sistemas imutáveis/Atomic
Sistemas como **Bazzite**, **Fedora Kinoite** e **Fedora Silverblue** podem fazer reset desta configuração em atualizações grandes (ex: Bazzite 43 → 44, Fedora Kinoite 43 → 44). Confirmar sempre com `resolvectl status` após cada atualização maior.

---

## Notas adicionais

| | |
|---|---|
| **Testado em** | Fedora KDE, Bazzite, Fedora Kinoite |
| **Compatível com** | Qualquer distro que use systemd-resolved |
| **NÃO compatível** | Arch Linux e derivados (CachyOS — preferível usar o CachyOS Hello que tem opção para configurar DNS), EndeavourOS, Void Linux, Alpine Linux — estas distros requerem configuração diferente |

*Última atualização: 03 de Março 2026*
