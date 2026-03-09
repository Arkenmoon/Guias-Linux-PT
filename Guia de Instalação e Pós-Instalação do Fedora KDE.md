 Setup Fedora KDE — Guia PT-PT

> Guia prático de pós-instalação do Fedora KDE Plasma Desktop escrito em Português de Portugal.
> Destinado a utilizadores que migram do Windows e que querem um sistema funcional, bem configurado e pronto para gaming.
> Este guia pressupõe que tenham alguns conhecimentos técnicos do Windows, mesmo que sejam básicos como formatar uma partição, etc.

---

## Índice

1. [Porquê Fedora KDE?](#1-porquê-fedora-kde)
2. [Instalação do Fedora](#2-instalação-do-fedora)
3. [Pós-Instalação Essencial](#3-pós-instalação-essencial)
   - 3.1 [Atualizar o sistema](#31-atualizar-o-sistema)
   - 3.2 [Firmware do hardware](#32-firmware-do-hardware)
   - 3.3 [RPM Fusion — Repositórios Adicionais](#33-rpm-fusion--repositórios-adicionais)
   - 3.4 [Codecs Multimédia](#34-codecs-multimédia)
   - 3.5 [Firefox ou Google Chrome](#35-firefox-ou-google-chrome)
   - 3.6 [Flatpak e Flathub](#36-flatpak-e-flathub)
   - 3.7 [Drivers AMD e NVIDIA](#37-drivers-amd-e-nvidia)
   - 3.8 [Suporte a arquivos comprimidos](#38-suporte-a-arquivos-comprimidos)
4. [Discos e Armazenamento](#4-discos-e-armazenamento)
5. [Snapshots com Btrfs](#5-snapshots-com-btrfs)
6. [DNS e Privacidade](#6-dns-e-privacidade)
   - 6.1 [NextDNS via systemd-resolved](#61-nextdns-via-systemd-resolved)
   - 6.2 [Mullvad VPN](#62-mullvad-vpn)
7. [Wayland vs X11](#7-wayland-vs-x11)
8. [Periféricos e Configuração](#8-periféricos-e-configuração)
9. [Gaming no Linux](#9-gaming-no-linux)
   - 9.1 [Steam e Proton](#91-steam-e-proton)
   - 9.2 [Lutris e Heroic Games Launcher](#92-lutris-e-heroic-games-launcher)
   - 9.3 [GameMode e MangoHud](#93-gamemode-e-mangohud)
   - 9.4 [Anti-Cheat e Compatibilidade](#94-anti-cheat-e-compatibilidade)
10. [Personalização do KDE](#10-personalização-do-kde)
11. [Otimizações e Manutenção](#11-otimizações-e-manutenção)
12. [Resolução de Problemas Comuns](#12-resolução-de-problemas-comuns)
13. [Notas Finais](#13-notas-finais)

---

## 1. Porquê Fedora KDE?

O **Fedora** é uma das distribuições Linux mais respeitadas, usa software recente que é testado antes de ser colocado disponível para todos. É mantida por uma comunidade ativa de vários utilizadores além de contar com o apoio da Red Hat(IBM). Para mim é a distribuição que considero mais simples, quem gosta de atualizações regulares sem receio de partir o sistema e intuitiva de utilizar para quem vem do Windows.

O **KDE Plasma** é o ambiente de trabalho mais parecido com o Windows em termos de estrutura visual, tem a barra de tarefas bastante similar, menu de aplicações e gestor de ficheiros familiar. Para quem vem do Windows a curva de aprendizagem é muito mais suave do que com o GNOME, que é o que muitos preferem e que é mais parecido a macOS. Se for esse o teu caso, abordarei GNOME noutro guia a publicar no futuro.

Pessoalmente, a combinação Fedora + KDE Plasma é mais sólida, moderna e personalizável, não te trata como se fosses um utilizador de testes beta como no Windows ou com o receio de alguma atualização, partir o sistema como poderá em raras ocasiões acontecer em distribuições baseadas em Arch (CachyOS, EndeavourOS, etc.). Para mim é a melhor opção no dia a dia que tanto dá para gaming como para programação, edição de vídeo/imagem/som, trabalho com documentação, navegar pela internet, etc. Na minha experiência até agora tem sido muito estável desde que o uso há quase meio ano, raramente tenho tido algum tipo de problema. Os que tenho normalmente são causados por mim próprio de andar a mexer no sistema, mas felizmente temos maneiras de voltar atrás caso se faça algum erro. A Comunidade do Fedora também é bastante amigável e no fórum deles, rapidamente respondem a questões que tenhas, mesmo que a lingua Inglesa seja uma limitação.

> ⚠️ Nota: Este guia cobre apenas o Fedora KDE Plasma (atualmente na versão 43 à data deste guia), uma versão com atualizações constantes e testadas antes de serem publicadas para todos. Futuramente será publicado um guia separado para as Atomic builds ou imutáveis como são conhecidas, estas são versões onde o sistema é mais resistente a erros e mais difícil de partir por estar em modo de leitura. Usam rpm-ostree no caso do Fedora Kinoite e ujust como task runner no caso do Bazzite, a distribuição mais focada em gaming. O dnf é apenas para a versão do Fedora abordada neste guia.

---

## 2. Instalação do Fedora

### 2.1 O que precisas

- Uma pen USB **com pelo menos 8 GB**
- O ISO do Fedora KDE: [https://fedoraproject.org/kde/](https://fedoraproject.org/kde/)
- [Balena Etcher](https://etcher.balena.io/) ou [Ventoy](https://www.ventoy.net/) para criar a pen bootável 
- Podes usar também o **Fedora Media Writer** que está na zona onde vais buscar o ISO, é bastante fácil de usar
- Acesso à BIOS/UEFI para arrancar pela pen (normalmente pressionar tecla DEL)

### 2.2 Dicas antes de instalar

- **Faz backup** de tudo o que precisas do Windows antes de proceder.
- **Vê no manual da tua Motherboard como aceder à BIOS**, porque vais precisar de lá ir para alterar duas condições de arranque.
- **Fica a carregar na tecla DEL ou DELETE**, isto permite aceder à BIOS no teu computador.
- **Dentro da BIOS mete modo avançado**, isto permite veres as opções necessárias para certificar que o teu sistema arranca sem problemas.
- **Secure Boot** — o Fedora suporta Secure Boot nativamente e funciona sem problemas. No entanto, se tens uma **NVIDIA** e vais instalar drivers proprietários, recomendo desativá-lo (consulta o manual da tua Motherboard para veres onde, normalmente é em **BOOT**) para evitar conflitos com o módulo kernel da NVIDIA. Se tens **AMD** ou **Intel**, podes deixar ativo sem stress.
- **Desativa o Fast Boot** (deverá ser no mesmo local onde desativas o **Secure Boot**) apesar de ser bastante útil no Windows, no Linux pode causar problemas e fazer com que a informação do teu hardware não carregue corretamente.

### 2.3 Durante a instalação

- Quando selecionares a pen USB do Fedora KDE, vai aparecer uma opção com duas escolhas. Seleciona a primeira opção que diz algo como **Fedora KDE Live USB**, a segunda opção é mais morosa.
- Dentro do ambiente de trabalho do Fedora vai aparecer um ícone que diz **Install to Hard Drive**.
- O menu de instalação é dos mais intuitivos, basta seguires tudo o que te pede. Vai pedir para criares a tua conta e escolheres a língua que quiseres. 
- Aconselho a não **Cifrar os Dados (Encrypt Data)**. Cifrar dados reduz a performance do Fedora e não é necessário a não ser em modo corporativo. Também não é necessário selecionar a opção **Root** pois já és o administrador principal da conta.
- O Fedora usa o sistema **Btrfs** por padrão como sistema de ficheiros, é uma boa escolha até para fazer imagens de segurança do sistema através do Snapper (explicado na [secção 5](#5-snapshots-com-btrfs)). Na minha opinião, não compensa mudar para EXT4 ou outro sistema atualmente.
- Se fizeres dual boot com Windows, instala **sempre o Windows primeiro** e depois a distribuição Linux, é muito mais fácil assim de gerir e evita conflitos. O Windows tem tendência nos updates a roubar o login só para ele, é um problema mesmo da Microsoft.
- Se estás a fazer dual boot, prepara-te para o relógio ficar trocado entre sistemas. Isto é normal e tem fix fácil — está explicado na [secção 10 - Personalização do KDE](#dual-boot--fix-do-relógio).
- O menu do Fedora é muito intuitivo como disse **mas recomendo sempre ler bem tudo o que é pedido ao utilizador com calma**, assim fica sempre tudo ao teu gosto.
- Depois da instalação estar pronta, basta iniciares (sem pen USB claro) e estarás no ambiente de trabalho do Fedora KDE limpo e pronto a começar.

---

## 3. Pós-Instalação Essencial

Após instalar o Fedora e fazer o primeiro arranque, o sistema está funcional mas incompleto pois a imagem usada está congelada no tempo e precisamos de atualizar tudo (componentes, pacotes, etc.). Estes passos cobrem o essencial!

### 3.1 Atualizar o sistema

A primeira coisa a fazer numa nova instalação, **sempre**, é atualizar tudo. Abre o Lançador de Aplicações (ícone do Fedora no canto inferior esquerdo) e procura por Konsole. Aproveita e faz pin do mesmo à barra de tarefas para facilitar, clicando no Konsole com o botão direito e **Pin to Task Manager** (ou **Fixar na barra de tarefas**). Apesar de dar para instalar atualizações e apps pelo "Discover" que é a loja de aplicações do Fedora, o Konsole facilita bastante o trabalho automatizando tudo de forma eficaz:

```bash
sudo dnf upgrade --refresh -y
```

`--refresh` força o sistema a descarregar metadados atualizados de todos os repositórios antes de calcular as atualizações, enquanto que o `-y` diz sim a tudo por ti (vamos usar `-y` bastantes vezes) não interrompendo a instalação à espera que dês autorização.

Reinicia depois de atualizar, especialmente se houve uma atualização do kernel (por norma há sempre):

```bash
sudo reboot
```

> **Nota sobre o DNF:** Pelo que vi na documentação oficial, desde o Fedora 41 o gestor de pacotes padrão é o **DNF5** que é significativamente mais rápido que o antigo DNF4. O comando `dnf` é automaticamente redirecionado para o DNF5, por isso todos os comandos deste guia funcionam sem alteração. Caso futuramente vejas em algum lado a dizer para usar **DNF5** numa instalação, basta fazeres `dnf` sem receios que ele já sabe que deve usar sempre o mais recente. 🙂

### 3.2 Firmware do hardware

Confirma **sempre** que o teu hardware tem o firmware mais recente disponível, isto importa especialmente para WiFi, Bluetooth, GPU e estabilidade geral. O Fedora inclui o `fwupd` que trata disto:

```bash
sudo fwupdmgr get-devices
sudo fwupdmgr refresh --force
sudo fwupdmgr get-updates
sudo fwupdmgr update
```

Podes correr o comando todo de uma vez sem receio, relembro que deves reiniciar depois de aplicar as atualizações de firmware para completar as mesmas. 
Nem todo o hardware tem atualizações disponíveis via `fwupd`, portanto se não aparecer nada é normal.

### 3.3 RPM Fusion — Repositórios Adicionais

O Fedora por princípio só inclui software livre, o **RPM Fusion** adiciona repositórios com software que o Fedora não pode distribuir por questões de licença como por exemplo codecs, drivers proprietários e muito mais.

Há dois repositórios: `free` (software open-source não aceite pelo Fedora) e `nonfree` (software proprietário). Iremos adicionar ambos:

```bash
sudo dnf install \
  https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm -y
```

Confirma a instalação:

```bash
sudo dnf repolist
```

Deves ver `rpmfusion-free` e `rpmfusion-nonfree` na lista.

### 3.4 Codecs Multimédia

Sem codecs o Fedora não reproduz a maioria dos formatos de vídeo e áudio (MP3, H.264, HEVC, AAC, etc.) nativamente. Com o RPM Fusion instalado resolve-se da seguinte forma:

```bash
sudo dnf swap ffmpeg-free ffmpeg --allowerasing -y
sudo dnf group upgrade multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin -y
sudo dnf group upgrade sound-and-video -y
```

Para suporte adicional a formatos da **Intel** (apenas se tens gráficos integrados ou dedicados Intel, **não corras isto em AMD ou NVIDIA**):

```bash
sudo dnf install intel-media-driver -y
```

> ⚠️ Se usas AMD, o suporte a VA-API (aceleração de hardware para vídeo) já vem incluído no driver open-source `mesa`. Para garantir a melhor compatibilidade, substitui os drivers Mesa padrão pelos do RPM Fusion (o `swap` é necessário pois os drivers padrão já estão instalados e entram em conflito com os freeworld):
> ```bash
> sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld -y
> sudo dnf swap mesa-vdpau-drivers mesa-vdpau-drivers-freeworld -y
> ```

### 3.5 Firefox ou Google Chrome

#### Firefox e codecs de vídeo

Caso uses Firefox, o mesmo no Fedora precisa de ajuda extra para reproduzir certos vídeos H.264 em sites como o Twitter/Facebook entre outros. Isto resolve-se com o codec OpenH264 da Cisco:

```bash
sudo dnf install openh264 gstreamer1-plugin-openh264 mozilla-openh264 -y
sudo dnf config-manager setopt fedora-cisco-openh264.enabled=1
sudo dnf update -y
```

Depois de instalar, reinicia o Firefox e verifica que o plugin **OpenH264** está ativo em `about:addons` na secção de Plugins.

#### Google Chrome

Se preferes o Chrome (que é o browser com melhor compatibilidade web, segurança e sincronização completa com a tua conta Google), podes instalá-lo diretamente via .rpm. Não há nada de errado em usar Chrome no Linux, funciona nativamente sem problemas e é mantido pela Google. 
Instala diretamente sem precisar de ir ao site:

```bash
sudo dnf install https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm -y
```

Isto adiciona automaticamente o repositório da Google ao teu sistema. As atualizações futuras vêm com o `sudo dnf upgrade` normal, zero manutenção.

### 3.6 Flatpak e Flathub

O **Flatpak** é o sistema de pacotes universal do Linux, permite instalar aplicações de forma isolada (sandbox) independente da distribuição. O **Flathub** é o repositório principal de aplicações Flatpak.

O Fedora já inclui Flatpak instalado por padrão mas usa o seu próprio repositório filtrado que contém um subconjunto limitado de aplicações. Para acesso ao catálogo completo, recomendo remover o repositório do Fedora e adicionar o Flathub diretamente:

```bash
flatpak remote-delete fedora
```

Agora sim adiciona o Flathub:

```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Caso as aplicações não apareçam no **Discover**, reinicia a sessão ou o computador que resolve o problema.

A partir daqui podes instalar aplicações via **Discover** (o gestor de software do KDE) ou via terminal como preferires. Recomendo no entanto o Konsole por ser mais rápido e mostrar o que faz no terminal:

```bash
# Exemplo: instalar o VLC
flatpak install flathub org.videolan.VLC
```

Se fores pelo **Discover**, no canto superior direito da aplicação, mesmo ao lado de descarregar, garante sempre que estás a sacar a aplicação do **Flathub** e não do **Fedora Linux**.

> Para a maioria das aplicações do dia-a-dia (browsers, clientes de Discord, gestores de password, etc.), Flatpak é a forma recomendada de instalar no Fedora para iniciantes. Caso a aplicação não esteja disponível no Flathub, aí sim compensa procurar nativamente pela mesma (ficheiro .RPM).

#### Flatseal - Gerir permissões de Flatpaks

As aplicações Flatpak correm em sandbox (isoladas), o que significa que por omissão podem não ter acesso a certas pastas do sistema. Se uma aplicação não consegue aceder aos teus ficheiros ou discos secundários o **Flatseal** permite gerir essas permissões de forma visual:

```bash
flatpak install flathub com.github.tchx84.Flatseal
```

Abre o Flatseal, seleciona a aplicação problemática e ajusta o acesso ao sistema de ficheiros conforme necessário. Exemplo comum: o Steam via Flatpak pode precisar de acesso a `/mnt/jogos` se tens jogos num disco secundário.

### 3.7 Drivers AMD e NVIDIA

#### AMD (RX 5000 e mais recente)

Se tens uma placa AMD moderna (série RX 5000 em diante), **não precisas de instalar drivers manualmente**. O kernel Linux já inclui o driver open-source `amdgpu` que funciona excelentemente.

Para garantir que tens as ferramentas de userspace atualizadas:

```bash
sudo dnf install mesa-dri-drivers mesa-vulkan-drivers vulkan-tools -y
```

Verifica se o Vulkan está a funcionar:

```bash
vulkaninfo --summary
```

#### NVIDIA

A NVIDIA no Linux é historicamente complicada (felizmente no futuro irá deixar de ser mas por agora ainda se mantém). O Fedora com RPM Fusion simplifica o processo mas há passos extra a seguir com cuidado.

> ⚠️ **Secure Boot:** O módulo kernel da NVIDIA requer assinatura se tiveres Secure Boot ativo. É mais fácil desativá-lo na BIOS antes de instalar os drivers.

> ⚠️ **Para GPUs da série RTX 4000 ou mais recente**, o Fedora precisa de um macro definido antes de instalar o driver para ativar o módulo kernel open-source:
> ```bash
> sudo sh -c 'echo "%_with_kmod_nvidia_open 1" > /etc/rpm/macros.nvidia-kmod'
> ```
> Corre este comando **antes** de instalar o `akmod-nvidia`.

Instala os drivers proprietários:

```bash
sudo dnf install akmod-nvidia -y
sudo dnf install xorg-x11-drv-nvidia-cuda -y  # Opcional: Dá suporte a CUDA
```

O `akmod-nvidia` compila o módulo kernel automaticamente. **Aguarda 5 a 15 minutos** após a instalação antes de reiniciar, o processo de compilação precisa de terminar. Podes monitorizar o progresso com:

```bash
sudo journalctl -f -u akmods
```

Reinicia o sistema:

```bash
sudo reboot
```

Verifica se os drivers estão ativos:

```bash
nvidia-smi
```

Se o comando devolver informação da tua GPU, está tudo a funcionar. Se não, tenta forçar a recompilação:

```bash
sudo akmods --force --kernels $(uname -r)
sudo reboot
```

### 3.8 Suporte a arquivos comprimidos

Infelizmente o Fedora de origem não suporta formatos `.rar` ou `.7z`, vais precisar disto para que funcionem:

```bash
sudo dnf install p7zip p7zip-plugins unrar -y
```

Agora o Dolphin (gestor de ficheiros do KDE) já consegue abrir e extrair estes formatos sem problemas.

---

## 4. Discos e Armazenamento

Se tens discos secundários (SSDs ou HDDs além do disco principal), precisas de os configurar para montarem automaticamente. O Linux não faz isso por omissão.

### Identificar os discos

```bash
lsblk -f
```

Este comando lista todos os discos e partições, com o sistema de ficheiros e UUID de cada um. Anota o **UUID** da partição que queres montar pois vais precisar dele.

### Criar o ponto de montagem

Um "ponto de montagem" é simplesmente uma pasta onde o disco vai aparecer. Cria-a onde preferires:

```bash
sudo mkdir -p /mnt/dados
sudo mkdir -p /mnt/jogos
```

Podes usar `/mnt/`, `/media/`, ou qualquer outra localização. O importante é seres consistente.

### Editar o fstab

O ficheiro `/etc/fstab` define o que monta automaticamente no arranque. **Edita-o com cuidado**, um erro aqui pode impedir o sistema de arrancar.

> ⚠️ **Antes de qualquer edição**, faz uma cópia de segurança: `sudo cp /etc/fstab /etc/fstab.bak`

```bash
sudo nano /etc/fstab
```

Adiciona uma linha por disco **no final do ficheiro** (nunca edites as linhas já existentes), no formato:

```
UUID=<o-teu-uuid>  /mnt/dados  ext4  defaults  0  2
```

Substitui:
- `<o-teu-uuid>` pelo UUID que anotaste com `lsblk -f`
- `/mnt/dados` pelo teu ponto de montagem
- `ext4` pelo sistema de ficheiros correto (pode ser `btrfs`, `ntfs-3g`, `exfat`, etc.)

#### Exemplos por sistema de ficheiros

**EXT4** (Linux nativo):
```
UUID=xxxx-xxxx  /mnt/dados  ext4  defaults,noatime  0  2
```

**BTRFS** (Linux nativo):
```
UUID=xxxx-xxxx  /mnt/jogos  btrfs  defaults,noatime,compress=zstd,space_cache=v2  0  0
```

**NTFS** (Windows - Atenção a leitura/escrita funciona mas com limitações):
```
UUID=xxxx-xxxx  /mnt/windows  ntfs-3g  defaults,uid=1000,gid=1000  0  0
```

**ExFAT** (compatível com Windows e macOS):
```
UUID=xxxx-xxxx  /mnt/externo  exfat  defaults,uid=1000,gid=1000  0  0
```

Para ExFAT precisas do pacote:
```bash
sudo dnf install exfatprogs -y
```

### Testar sem reiniciar

```bash
sudo systemctl daemon-reload
sudo mount -a
```

Se não aparecerem erros, a configuração está correta. Reinicia para confirmar que monta no arranque.

> ⚠️ Se `mount -a` devolver erros, **não reinicies o sistema**. Corrige o fstab primeiro ou restaura o backup: `sudo cp /etc/fstab.bak /etc/fstab`

> Para um guia mais detalhado sobre configuração de discos secundários, incluindo resolução de problemas, permissões e referência completa de flags, consulta: [Como Configurar Discos Secundários — Fedora Linux](https://github.com/Arkenmoon/Guias-Linux-PT/blob/main/Como%20Configurar%20Discos%20Secund%C3%A1rios%20%E2%80%94%20Fedora%20Linux.md)

---

## 5. Snapshots com Btrfs

Uma das maiores vantagens de usar Btrfs (o sistema de ficheiros padrão do Fedora) é a possibilidade de criar **snapshots**, que são imagens instantâneas do sistema que podes restaurar se algo correr mal. Pensa nisto como pontos de restauro do Windows mas muito mais rápidos e fiáveis.

### Instalar as ferramentas

```bash
sudo dnf install btrfs-assistant snapper -y
```

O **Btrfs Assistant** é uma interface gráfica que facilita a gestão de snapshots e subvolumes. O **Snapper** é a ferramenta que cria e gere os snapshots por baixo.

### Configurar snapshots automáticos

Abre o **Btrfs Assistant** pelo menu de aplicações para configurar os snapshots com interface gráfica. 
Se quiseres ativar snapshots automáticos periódicos podes faze-lo da seguinte forma:

```bash
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer
```

> ⚠️ O `snapper-timeline` cria snapshots a intervalos regulares (por omissão de hora a hora) e o `snapper-cleanup` remove os mais antigos automaticamente para não encher o disco. Mas sinceramente se não mexeres muito no sistema não é necessário teres o snapper a fazer snapshots de X em X tempo. Fazes só ocasionalmente antes de cada grande atualização como rede de segurança no assistente de forma manual.

> ⚠️ Antes de uma atualização grande do sistema ou de mexer em configurações arriscadas, podes criar um snapshot manual da seguinte forma no Konsole: `sudo snapper create -d "antes de atualização"`. Assim se algo correr mal, restauras o snapshot e voltas ao estado anterior.

> ⚠️ Por omissão, o Snapper faz snapshots do sistema (root `/`), **não dos teus ficheiros pessoais**. Para ficheiros pessoais faz backups separados para um disco externo ou nuvem.

---

## 6. DNS e Privacidade

> ⚠️ Esta secção é totalmente opcional, passa ao [passo 7](#7-wayland-vs-x11) se não te interessa. Aqui digo como fazer setup ao NextDNS e MullvadVPN que é o setup que tenho atualmente e que poderá ajudar outros utilizadores que poderão usar estes serviços. Futuramente incluirei também um guia com vários provedores de DNS e VPN.


### 6.1 NextDNS via systemd-resolved

O **NextDNS** é um serviço de DNS personalizado que permite bloquear anúncios, rastreadores e domínios maliciosos a nível de rede na nuvem. O plano gratuito cobre 300.000 queries por mês mas para mim compensa ter o plano pago (não é um anúncio prometo 🙂)

O Fedora usa `systemd-resolved` para gerir DNS, o que torna a configuração simples.

Cria (ou edita) o ficheiro de configuração do resolved:

```bash
sudo nano /etc/systemd/resolved.conf
```

Adiciona ou descomenta as seguintes linhas (substitui os endereços pelos teus do NextDNS):

```ini
[Resolve]
DNS=45.90.28.XXXX#xxxxxxxxxxxx.dns.nextdns.io
DNS=45.90.30.XXXX#xxxxxxxxxxxx.dns.nextdns.io
DNSOverTLS=yes
DNSSEC=allow-downgrade
```

> ⚠️ **Nota sobre DNSSEC:** Usa `DNSSEC=allow-downgrade` em vez de `DNSSEC=yes`. O modo `yes` pode causar falhas de resolução com alguns serviços de DNS (incluindo o NextDNS) porque força validação estrita que nem todos os domínios suportam. O modo `allow-downgrade` tenta validar quando possível mas não bloqueia quando o DNSSEC não está disponível.

> ⚠️ Os teus endereços NextDNS específicos estão no painel em [my.nextdns.io](https://my.nextdns.io) após criares uma conta e um perfil.

Reinicia o serviço:

```bash
sudo systemctl restart systemd-resolved
```

Verifica se está a funcionar:

```bash
resolvectl status
```

Procura o teu servidor NextDNS na secção `DNS Servers`.

### 6.2 Mullvad VPN

O **Mullvad** é um dos VPNs mais respeitados em termos de privacidade — sem conta associada a email, pagamento anónimo possível, política de zero logs auditada.

#### Instalar no Fedora

Adiciona o repositório oficial do Mullvad:

```bash
sudo dnf config-manager addrepo --from-repofile=https://repository.mullvad.net/rpm/stable/mullvad.repo
```

Instala a aplicação:

```bash
sudo dnf install mullvad-vpn -y
```

Abre a aplicação pelo menu de aplicações ou:

```bash
mullvad-vpn
```

Faz login com o teu número de conta Mullvad (não há email — o teu identificador é apenas o número de conta).

> O Mullvad tem uma app gráfica intuitiva. Podes configurar kill switch, split tunneling e escolher protocolo (WireGuard é recomendado) diretamente na interface.

---

## 7. Wayland vs X11

O Fedora KDE usa **Wayland** por omissão como servidor gráfico. Há situações em que podes precisar de trocar para **X11** (também chamado Xorg):

- Alguns jogos mais antigos ou com anti-cheat podem ter problemas em Wayland
- Aplicações que usam captura de ecrã (OBS, Discord screen share) podem funcionar melhor em X11
- NVIDIA com drivers proprietários pode ter artefactos ou problemas de performance em Wayland (tem melhorado, mas não é perfeito)

### Como trocar

Na tela de login (SDDM), antes de inserires a password, olha para o canto inferior esquerdo. Há um menu dropdown onde podes escolher entre **Plasma (Wayland)** e **Plasma (X11)**. A escolha é por sessão — podes alternar sempre que quiseres.

> Recomendo usar Wayland por omissão e só trocar para X11 se tiveres problemas concretos com um jogo ou aplicação específica. Em AMD (como a RX 9070 XT), Wayland funciona muito bem para gaming.

---

## 8. Periféricos e Configuração

### Ratos gaming — Piper e libratbag

No Windows tens software como o Logitech G Hub ou Razer Synapse para configurar DPI, botões e macros. No Linux a alternativa open-source para a maioria dos ratos gaming é o **Piper** que usa o **libratbag** por baixo.

```bash
sudo dnf install piper -y
```

Abre o Piper pelo menu de aplicações, liga o teu rato e podes configurar DPI, atribuição de botões e LEDs (se suportados). Funciona com ratos Logitech, Razer, SteelSeries e muitos outros.

> ⚠️ Para ratos Logitech sem fios como por exemplo o G502X Wireless, o `libratbag` suporta configuração via receiver USB. Se o rato não for detetado, verifica se o receiver está ligado e tenta `sudo ratbagctl list` no terminal para confirmar.

### Teclados

Para a maioria dos teclados incluindo mecânicos como por exemplo Ducky ou Keytron, não é necessário software extra. O KDE permite remapear teclas e configurar atalhos diretamente nas **Definições do Sistema > Atalhos** e **Definições do Sistema > Dispositivos de Entrada > Teclado**.

Se precisas de funcionalidades avançadas de remapeamento, o [**Input Remapper**](https://github.com/sezanzeb/input-remapper) é uma boa opção:

```bash
flatpak install flathub io.github.sezanzeb.input_remapper
```

### DACs e áudio USB externo

Se tens um DAC externo USB (como por exemplo Schiit Fulla, FiiO, Topping, etc.), a boa notícia é que a maioria funciona plug and play no Linux. O Fedora usa **PipeWire** como servidor de áudio por omissão que suporta dispositivos USB Audio Class 2 (UAC2) nativamente sem drivers adicionais.

Basta ligar o DAC via USB e selecionar o dispositivo de saída no ícone de volume no system tray do KDE. Se tiveres também um microfone USB separado (como um HyperX SoloCast ou similar), o PipeWire deteta ambos independentemente — seleciona o DAC como output e o microfone como input nas definições de áudio.

Se por algum motivo tiveres problemas de crackling ou pops de áudio (raro mas pode acontecer), cria o seguinte ficheiro de configuração:

```bash
mkdir -p ~/.config/pipewire/pipewire.conf.d/
nano ~/.config/pipewire/pipewire.conf.d/10-fix-crackling.conf
```

Com o seguinte conteúdo:

```ini
context.properties = {
    default.clock.quantum = 1024
    default.clock.min-quantum = 512
    default.clock.max-quantum = 2048
}
```

Reinicia o PipeWire para aplicar:

```bash
systemctl --user restart pipewire
```

---

## 9. Gaming no Linux

- O Linux para gaming evoluiu muito, especialmente nos últimos 3-4 anos graças à Steam. Com as ferramentas certas, sou-te sincero, a grande maioria dos jogos da Steam funciona bem incluindo muitos jogos com anti-cheat. Claro que existem alguns em que podes ser banido simplesmente por usar Linux que fica ao risco de cada um e que precisas de ter cuidado. Neste caso aconselho sempre a veres o [Are We Anti-Cheat Yet?](https://areweanticheatyet.com/) para compatibilidade, basta colocares o título que queres jogar e confirmar. Se não aparecer nada é a troca infelizmente de usar Linux, aí o Dual Boot com o Windows pode ser uma opção para estes jogos.

- Para o resto, o site [ProtonDB](https://www.protondb.com/) é o melhor local para veres se o jogo é compatível e o que necessitas de fazer para que funcione. Normalmente a comunidade dá todos os passos de como resolver mesmo em jogos recentes. Basta fazeres login com a tua conta Steam que ele vai buscar todos os jogos que tens e mostrar-te a compatibilidade de cada um!

### 9.1 Steam e Proton

O **Steam** no Linux inclui o **Proton** uma camada de compatibilidade baseada em Wine que permite jogar jogos do Windows diretamente.

#### Instalar o Steam

Via Flatpak (recomendado):

```bash
flatpak install flathub com.valvesoftware.Steam
```

Ou via RPM se preferes pacote nativo (mais recomendado em AMD porque o RPM tem acesso direto às bibliotecas Mesa do sistema sem camadas extra; em NVIDIA recomendo **Flatpak** porque inclui as dependências de compatibilidade necessárias para os drivers proprietários sem conflitos):

```bash
sudo dnf install steam -y
```

> ⚠️ A versão Flatpak do Steam está mais isolada do sistema mas funciona bem para a maioria dos casos. A versão RPM tem melhor acesso ao hardware em alguns cenários, especialmente em sistemas AMD. Se usares Flatpak e tiveres jogos em discos secundários, usa o Flatseal ([secção 3.6](#36-flatpak-e-flathub)) para dar acesso ao Steam à pasta dos teus jogos.

#### Ativar o Proton

No Steam:
1. Abre as **Definições** (ícone Steam > Definições)
2. Vai a **Compatibilidade**
3. Ativa **"Ativar Steam Play para todos os outros títulos"**
4. Seleciona a versão de Proton mais recente estável ou `Proton Experimental` para jogos mais recentes

#### Proton GE (Proton-GE)

O **Proton-GE** é uma versão não-oficial do Proton mantida pela comunidade com patches adicionais para jogos específicos. Muitas vezes resolve problemas que o Proton oficial ainda não corrigiu.

Instala o **ProtonUp-Qt** para gerir versões do Proton:

```bash
flatpak install flathub net.davidotek.pupgui2
```

Abre o ProtonUp-Qt, seleciona Steam como launcher e instala a versão GE mais recente.

Para usar num jogo específico: clica com o botão direito no jogo > Propriedades > Compatibilidade > força uma versão específica de Proton.

#### Verificar sempre compatibilidade

Reitero que antes de comprar ou instalar um jogo, consulta:
- [ProtonDB](https://www.protondb.com/) tem relatos da comunidade sobre compatibilidade, mesmo para jogos novos
- Tens também a própria página do jogo na Steam com o ícone de compatibilidade Linux/Steam Deck

### 9.2 Lutris e Heroic Games Launcher

Nem todos os jogos estão na Steam. O **Lutris** e o **Heroic** cobrem outros launchers e lojas.

#### Lutris

O Lutris é um gestor de jogos que centraliza tudo desde Steam, GOG, Epic, jogos instalados manualmente, emuladores. Tem scripts de instalação mantidos pela comunidade para muitos jogos problemáticos.

```bash
flatpak install flathub net.lutris.Lutris
```

Útil especialmente para: jogos da Battle.net (Diablo, WoW), jogos antigos que precisam de configuração Wine específica, emuladores.

#### Heroic Games Launcher

O Heroic é o cliente não-oficial para a **Epic Games Store**, **GOG** e **Amazon** no Linux. Tem uma interface limpa e boa integração com Proton/Wine.

```bash
flatpak install flathub com.heroicgameslauncher.hgl
```

Faz login com as tuas contas e os teus jogos aparecerão automaticamente.

### 9.3 GameMode e MangoHud

#### GameMode

O **GameMode** é uma ferramenta que optimiza o sistema quando estás a jogar. Ela ajusta o teu CPU, gestor de energia e outras configurações para máxima performance.

```bash
sudo dnf install gamemode -y
```

Para ativar num jogo Steam, adiciona às opções de lançamento do jogo:

```
gamemoderun %command%
```

(Botão direito no jogo > Propriedades > Opções de lançamento)

#### MangoHud

O **MangoHud** é um overlay de performance para jogos Vulkan e OpenGL. Mostra FPS, frametime, uso da CPU/GPU, temperaturas, etc. Equivalente ao overlay do MSI Afterburner no Windows. Totalmente opcional, mas adiciona se queres ter informação dos recursos.

```bash
sudo dnf install mangohud -y
```

Para ativar num jogo Steam:

```
mangohud %command%
```

Podes combinar com GameMode:

```
gamemoderun mangohud %command%
```

Para personalizar o que o MangoHud mostra, cria o ficheiro de configuração:

```bash
mkdir -p ~/.config/MangoHud
nano ~/.config/MangoHud/MangoHud.conf
```

Exemplo de configuração básica:

```ini
fps
cpu_temp
gpu_temp
ram
vram
frametime
position=top-left
font_size=24
```

A documentação completa de opções está em: [https://github.com/flightlessmango/MangoHud](https://github.com/flightlessmango/MangoHud)

### 9.4 Anti-Cheat e Compatibilidade

O anti-cheat é historicamente o maior obstáculo ao gaming no Linux. O panorama melhorou muito com certos desenvolvedores de jogos a permitir que funcionem no Linux, mas infelizmente ainda há jogos problemáticos como por exemplo Battlefield 6, League of Legends, Valorant, TFT, etc.

#### EAC (Easy Anti-Cheat) e BattlEye

A **Epic Games** e a **Valve** trabalharam com os fornecedores de anti-cheat para adicionar suporte nativo ao Linux/Proton. Hoje, muitos jogos com EAC e BattlEye funcionam sem problemas adicionais.

Mas como referi para verificar se um jogo específico funciona, consulta sempre:
- [ProtonDB](https://www.protondb.com/) — relatos da comunidade sobre compatibilidade de cada jogo
- [Are We Anti-Cheat Yet?](https://areweanticheatyet.com/) — estado do anti-cheat por jogo no Linux
- [GamingOnLinux Anti-Cheat List](https://www.gamingonlinux.com/anticheat/) — lista de compatibilidade mantida pelo GamingOnLinux

---

## 10. Personalização do KDE

O KDE Plasma é extremamente personalizável. Aqui ficam os pontos de partida para quem vem do Windows e quer orientar-se.

### Onde encontrar as definições

- **Definições do Sistema** — o equivalente ao Painel de Controlo do Windows. Acedes pelo menu de aplicações ou clicando com o botão direito no ambiente de trabalho > Configurar Desktop.
- **Aparência** — temas globais, cores, ícones, cursores e fontes estão todos em Definições do Sistema > Aparência.
- **Barra de tarefas** — clica com o botão direito na barra de tarefas > Editar Painel para adicionar/remover widgets, redimensionar e reposicionar.
- **Atalhos de teclado** — Definições do Sistema > Atalhos. Podes personalizar praticamente tudo. O `Meta` (tecla Windows) abre o menu de aplicações por omissão.
- **Efeitos de desktop** — Definições do Sistema > Comportamento do Espaço de Trabalho > Efeitos de Desktop. Desativa o que não precisares para poupar recursos.

### Dar um nome ao computador

Puramente cosmético mas personaliza o teu sistema. Aparece no Konsole e na rede local:

```bash
sudo hostnamectl set-hostname o-teu-nome-aqui
```
> ⚠️ Troca "o-teu-nome-aqui" por outra coisa como por exemplo "PC-Gaming", tens de por sempre `-` para fazer espaços entre duas palavras no nome.

```bash
hostname
```
Permite verificar que o nome foi assumido pelo teu sistema. Para uma versão mais detalhada podes usar `hostnamectl`

### Widgets úteis

Clica com o botão direito no ambiente de trabalho > Adicionar Widgets para explorar. Alguns úteis: monitor de sistema, notas, previsão meteorológica. Experimenta à vontade, podes sempre remover se não gostares de nenhum. 🙂

### Dual Boot — Fix do relógio

Se tens dual boot com Windows, vais reparar que o relógio fica errado quando saltas entre sistemas operativos. Isto acontece porque o Windows usa hora local no relógio do hardware e o Linux usa UTC. A correção:

```bash
sudo timedatectl set-local-rtc 0 --adjust-system-clock
```

Isto diz ao Linux para ajustar a forma como lê o relógio do hardware, resolvendo o conflito.

---

## 11. Otimizações e Manutenção

### Limpeza periódica

> ⚠️ Não é necessário correr isto todas as semanas. Uma vez por mês ou quando sentires que o disco está a encher é suficiente.

```bash
sudo dnf clean all
sudo dnf autoremove -y
```

> Para Flatpaks, podes remover dados de aplicações já desinstaladas:

```bash
flatpak uninstall --unused
```

### Atualizar Flatpaks e Outros Updates

Os Flatpaks não atualizam automaticamente com o `dnf`. Para mantê-los atualizados:

```bash
flatpak update
```

Faz isto periodicamente ou sempre que atualizas o sistema. Podes também ir a Configurações do Sistema > Actualização das Aplicações > Alterar como for preferível para ti. Eu pessoalmente, tenho manualmente com verificação diária > Apply System Updates = After Rebooting, quer dizer que só aplica depois de reiniciar.

---

## 12. Resolução de Problemas Comuns

Aqui ficam soluções rápidas para problemas que muitos utilizadores encontram nas primeiras semanas com o Fedora. Se tiveres um problema que não está aqui, a [Arch Wiki](https://wiki.archlinux.org/) é surpreendentemente útil mesmo para Fedora, a maioria das soluções aplicam-se a qualquer distro.

### O sistema não arranca depois de editar o fstab

Sem pânico. Quando o GRUB aparecer, seleciona a opção de arranque normal. Se o sistema ficar preso, reinicia e no GRUB edita a entrada (tecla `e`) adicionando `systemd.unit=emergency.target` no final da linha `linux`. Isto arranca em modo emergência onde podes corrigir o `/etc/fstab` ou restaurar o backup:

```bash
sudo cp /etc/fstab.bak /etc/fstab
sudo reboot
```

### Ecrã preto após atualização (NVIDIA)

Se tens NVIDIA e o sistema arranca com ecrã preto após uma atualização do kernel, é provável que o módulo `akmod` não tenha recompilado a tempo. Arranca pelo kernel anterior no GRUB (tecla de setas para selecionar) e força a recompilação:

```bash
sudo akmods --force --kernels $(uname -r)
sudo reboot
```

### Sem som após instalação

Verifica se o PipeWire está a correr:

```bash
systemctl --user status pipewire
```

Se não estiver ativo:

```bash
systemctl --user enable --now pipewire pipewire-pulse wireplumber
```

Verifica também no ícone de volume do KDE se o dispositivo de saída correto está selecionado. Por vezes o sistema escolhe HDMI em vez do output analógico ou do DAC USB.

### O Discover não mostra aplicações do Flathub

Isto acontece quando o repositório Flathub não foi adicionado corretamente ou o Fedora ainda tem o seu repositório filtrado ativo. Confirma:

```bash
flatpak remotes
```

Deves ver `flathub` na lista. Se não:

```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Reinicia a sessão ou o computador.

### Jogos Steam não arrancam

Verifica primeiro se o Proton está ativado (Steam > Definições > Compatibilidade). Se já está, tenta forçar uma versão diferente de Proton no jogo (botão direito > Propriedades > Compatibilidade). O Proton Experimental ou o Proton-GE mais recente costumam resolver a maioria dos casos. Consulta sempre o [ProtonDB](https://www.protondb.com/) para dicas específicas do jogo.

---

## 13. Notas Finais

Este guia cobre o essencial para teres o Fedora KDE funcional e preparado para gaming. Não é exaustivo, pois o Linux tem sempre mais para explorar e está sempre em constantes mudanças, mas é um ponto de partida sólido para qualquer iniciante começar a sua jornada. 
Tentarei manter este guia atualizado sempre que possível para colmatar as alterações que vão acontecendo, seja no Fedora ou no panorama do Gaming em geral.

Obrigado pela tua leitura 🙂

### Recursos úteis

| Recurso | Descrição |
|---|---|
| [ProtonDB](https://www.protondb.com/) | Compatibilidade de jogos com Proton |
| [Are We Anti-Cheat Yet?](https://areweanticheatyet.com/) | Estado do anti-cheat no Linux |
| [Fedora Docs](https://docs.fedoraproject.org/) | Documentação oficial do Fedora |
| [KDE UserBase Wiki](https://userbase.kde.org/) | Documentação e dicas para KDE Plasma |
| [Arch Wiki](https://wiki.archlinux.org/) | Wiki técnica mais completa do Linux (aplicável a Fedora) |
| [Linux Gaming Wiki](https://linux-gaming.kwindu.eu/) | Wiki comunitária sobre gaming no Linux |
| [r/linux_gaming](https://www.reddit.com/r/linux_gaming/) | Comunidade de gaming no Linux |
| [r/Fedora](https://www.reddit.com/r/Fedora/) | Comunidade do Fedora no Reddit |
| [Flathub](https://flathub.org/) | Catálogo de aplicações Flatpak |

*Última atualização: 09 de Março 2026*
