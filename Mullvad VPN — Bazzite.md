# Mullvad VPN — Bazzite

> Como instalar, configurar e atualizar o Mullvad VPN no Bazzite (Fedora Atomic Desktop).

---

## Antes de Começar

O Bazzite é um sistema **imutável** baseado em Fedora Atomic. Isto significa que não usamos `dnf install` como numa distro tradicional — usamos `rpm-ostree` para "sobrepor" pacotes ao sistema base (layering).

A documentação oficial do Bazzite **recomenda o uso de Flatpak, Homebrew ou Distrobox** sempre que possível. No entanto, a Mullvad é um caso onde o layering via `rpm-ostree` **faz sentido**, porque uma VPN precisa de operar a nível do sistema (kill-switch, split tunneling, etc.), algo que a sandbox do Flatpak não permite.

> **Nota:** Este guia usa o Bazzite como referência, mas aplica-se igualmente a qualquer Fedora Atomic Desktop que use `rpm-ostree` — incluindo Aurora, Silverblue e Kinoite. Os comandos são os mesmos.

> **Nota:** Pacotes layered podem atrasar ou bloquear atualizações do sistema caso surjam conflitos de dependências. É o preço a pagar por ter uma VPN a sério e não apenas um perfil WireGuard importado.

---

## Instalação de Raiz (Fresh Install)

Para quem nunca instalou a Mullvad no Bazzite.

### Passo 1 — Adicionar o Repositório da Mullvad

O Bazzite não conhece o repositório da Mullvad. Temos de lhe dar o mapa primeiro.

**Opção A — Via `dnf5` (método recomendado no Bazzite atual):**

```bash
sudo dnf5 config-manager addrepo --from-repofile=https://repository.mullvad.net/rpm/stable/mullvad.repo
```

**Opção B — Via `wget` (método manual, igualmente funcional):**

```bash
sudo wget https://repository.mullvad.net/rpm/stable/mullvad.repo -O /etc/yum.repos.d/mullvad.repo
```

> Ambos os métodos fazem exatamente a mesma coisa: colocar o ficheiro `mullvad.repo` em `/etc/yum.repos.d/` para que o sistema saiba onde ir buscar o pacote.

### Passo 2 — Instalar o Pacote da Mullvad

```bash
rpm-ostree install mullvad-vpn
```

> Não é necessário `sudo` neste comando — o `rpm-ostree` usa autenticação via Polkit e vai pedir a password na mesma.

### Passo 3 — Reiniciar

Obrigatório. O sistema precisa de arrancar com a nova camada (deployment) que inclui a Mullvad.

```bash
systemctl reboot
```

### Passo 4 — Ativar os Serviços da Mullvad

Depois do reboot, a Mullvad está instalada mas o daemon não arranca sozinho. Temos de o acordar e dizer-lhe para arrancar com o sistema:

```bash
sudo systemctl enable --now mullvad-daemon
sudo systemctl enable --now mullvad-early-boot-blocking
```

> **Importante:** O serviço `mullvad-early-boot-blocking` é recomendado pela própria Mullvad para Fedora Atomic Desktops (Bazzite, Silverblue, Kinoite). Garante que o tráfego de rede é bloqueado antes do daemon da VPN arrancar, evitando fugas de dados (leaks) durante o arranque.

### Passo 5 — Fazer Login e Configurar

Abrir a aplicação Mullvad VPN (pelo menu de aplicações ou pelo terminal) e fazer login com o teu número de conta.

```bash
mullvad account login <NUMERO_DA_CONTA>
```

A partir daqui, podes configurar servidores, protocolo (WireGuard ou OpenVPN), kill-switch, e tudo o resto pela interface gráfica ou CLI.

---

## Atualizar o Bazzite com a Mullvad (Major Updates)

Atualizações grandes do Bazzite (ex.: saltar de versão 43 para 44) podem entrar em conflito com pacotes layered. A Mullvad é um deles. O procedimento seguro é: **remover a camada antes de atualizar, e reinstalar depois**.

### Passo 1 — Remover a Camada da Mullvad

```bash
rpm-ostree uninstall mullvad-vpn
```

### Passo 2 — Reiniciar

Obrigatório para o sistema arrancar limpo e sem a camada da Mullvad.

```bash
systemctl reboot
```

### Passo 3 — Fazer a Atualização do Bazzite

Podes fazer isto pela interface gráfica (GNOME Software / Discover) ou pelo terminal:

```bash
ujust update
```

Espera que a atualização termine e o sistema reinicie para a nova versão.

### Passo 4 — Reinstalar a Mullvad

Depois de arrancar na nova versão do Bazzite:

```bash
rpm-ostree install mullvad-vpn
```

### Passo 5 — Reiniciar Novamente

```bash
systemctl reboot
```

### Passo 6 — Reativar os Serviços

```bash
sudo systemctl enable --now mullvad-daemon
sudo systemctl enable --now mullvad-early-boot-blocking
```

### Passo 7 — (Opcional) Reconfigurar DNS Personalizado

Se usas DNS personalizado (ex.: NextDNS) e a Mullvad "esqueceu" a configuração após a reinstalação, volta a definir:

```bash
mullvad dns set custom <ENDERECO_DNS_1> <ENDERECO_DNS_2>
```

**Exemplo com NextDNS (IPv6):**

```bash
mullvad dns set custom 2a07:a8c0::XX:XXXX 2a07:a8c1::XX:XXXX
```

> Substitui `XX:XXXX` pelos endereços do teu perfil NextDNS. Podes encontrá-los na tua dashboard em [my.nextdns.io](https://my.nextdns.io) → Setup → IPv6.

Para verificar que o DNS ficou corretamente configurado:

```bash
mullvad dns get
```

---

## Comandos Úteis

| Comando | Descrição |
|---|---|
| `mullvad status` | Ver o estado atual da ligação |
| `mullvad connect` | Ligar à VPN |
| `mullvad disconnect` | Desligar a VPN |
| `mullvad relay list` | Ver todos os servidores disponíveis |
| `mullvad relay set location pt lis` | Ligar a um servidor específico (ex.: Lisboa) |
| `mullvad dns get` | Ver a configuração DNS atual |
| `mullvad dns set default --block-ads --block-trackers` | Ativar bloqueio de anúncios e trackers via DNS da Mullvad |
| `mullvad lockdown-mode set on` | Bloquear todo o tráfego fora do túnel VPN |
| `mullvad account get` | Ver informação da conta (validade, etc.) |

---

## Notas Importantes

- **Atualizações automáticas do pacote:** Como o repositório da Mullvad está adicionado ao sistema, atualizações menores da Mullvad (dentro da mesma versão do Bazzite) serão aplicadas automaticamente durante as atualizações normais do sistema.
- **Atualizações grandes (major) do Bazzite:** Seguir **sempre** o procedimento de remover → atualizar → reinstalar descrito acima para evitar conflitos.
- **Verificar a ligação:** Depois de tudo configurado, visita [mullvad.net/check](https://mullvad.net/check) para confirmar que a VPN está ativa e sem fugas de DNS.
- **WireGuard via NetworkManager:** Se não precisas de funcionalidades avançadas (kill-switch, split tunneling, bloqueio DNS), podes simplesmente importar um ficheiro de configuração WireGuard da Mullvad diretamente no NetworkManager sem fazer layering. Consulta a [documentação do Bazzite sobre VPNs](https://docs.bazzite.gg/General/VPN/) para mais detalhes.

---

## Fontes e Referências

- [Mullvad — Instalar no Linux (oficial)](https://mullvad.net/en/help/install-mullvad-app-linux)
- [Mullvad — Como usar o CLI](https://mullvad.net/en/help/how-use-mullvad-cli)
- [Bazzite — Package Layering (rpm-ostree)](https://docs.bazzite.gg/Installing_and_Managing_Software/rpm-ostree/)
- [Bazzite — VPN Setup](https://docs.bazzite.gg/General/VPN/)

---

*Última atualização: Março 2026*
