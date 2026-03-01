# Como Configurar Discos Secundários — Fedora Linux

Guia completo para montar discos internos de forma permanente no Fedora (KDE/Dolphin). Compatível com BTRFS, EXT4 e ExFAT.

---

## Pré-Requisitos

- Fedora Linux (qualquer versão recente com systemd)
- Acesso `sudo`
- Discos já formatados com o filesystem pretendido (BTRFS, EXT4 ou ExFAT)
- Terminal aberto (Konsole, GNOME Terminal, etc.)

> ⚠️ **Nunca edites as linhas já existentes no `fstab`** — especialmente as do disco de arranque e da zram. Mexer nessas entradas pode impedir o sistema de arrancar.

---

## 0. Identificar os Discos

Antes de qualquer operação, precisas de saber exatamente que discos tens ligados, qual o filesystem de cada um, e os respetivos UUIDs (identificadores únicos universais). Sem isto, arriscas montar o disco errado ou copiar um UUID que não corresponde — e um `fstab` com UUIDs errados pode impedir o arranque do sistema.

```bash
lsblk -f
```

O output será algo como:

```
NAME        FSTYPE FSVER LABEL        UUID                                 MOUNTPOINTS
sda
└─sda1      btrfs        DiscoBTRFS   xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
sdb
└─sdb1      ext4   1.0   DiscoEXT4    xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
sdc
└─sdc1      exfat  1.0   DiscoExFAT   XXXX-XXXX
nvme0n1
├─nvme0n1p1 vfat         EFI          XXXX-XXXX                            /boot/efi
└─nvme0n1p2 btrfs        fedora       xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /
```

Opcionalmente, guarda este output num ficheiro para referência futura:

```bash
lsblk -f > ~/discos_info.txt
```

**Anota os UUIDs de cada disco** — vais precisar deles no passo 4.

---

## 1. Desmontar os Discos

Quando ligas discos internos, o gestor de ficheiros (Dolphin no KDE, Nautilus no GNOME) tende a montá-los automaticamente em `/run/media/$USER/`. Isto funciona para pens USB e discos externos, mas para discos internos permanentes é problemático — os caminhos podem mudar, as permissões ficam inconsistentes, e crias conflitos quando tentares montá-los no local definitivo.

Precisas de os desmontar primeiro para que o sistema os liberte. Substitui `NomeDoDisco` pelo label de cada disco, conforme aparece no output do `lsblk -f`:

```bash
sudo umount /run/media/$USER/NomeDoDisco1
sudo umount /run/media/$USER/NomeDoDisco2
sudo umount /run/media/$USER/NomeDoDisco3
```

> ✅ Se receberes `not mounted` ou `no mount point`, significa que o disco já está livre — não é um erro.

Para confirmar que nada ficou pendurado:

```bash
mount | grep /run/media
```

Se não devolver nenhum resultado, estás limpo.

---

## 2. Criar os Pontos de Montagem

No Linux, para aceder a um disco, ele precisa de estar "montado" num diretório — um ponto de montagem. É como dar uma morada fixa a cada disco. Sem o diretório criado, o sistema não tem onde colocar o conteúdo do disco e o `mount` falha.

Usamos `/mnt/` porque é a convenção padrão para discos internos permanentes. A alternativa `/run/media/` é gerida automaticamente pelo sistema para dispositivos removíveis (pens, discos externos) e pode causar conflitos.

Cria um diretório para cada disco. O flag `-p` evita erros caso o diretório já exista:

```bash
sudo mkdir -p /mnt/NomeDoDisco1
sudo mkdir -p /mnt/NomeDoDisco2
sudo mkdir -p /mnt/NomeDoDisco3
```

Escolhe nomes descritivos e sem espaços. Exemplos: `Jogos`, `Dados`, `Arquivo`, `Backup`.

---

## 3. Definir Permissões

Os diretórios criados com `sudo mkdir` ficam com o dono definido como `root`. Isto significa que, sem alterar as permissões, vais precisar de usar `sudo` para cada operação de leitura e escrita — copiar ficheiros, criar pastas, instalar jogos, tudo pede password. Ao mudar o proprietário para o teu utilizador, ganhas acesso direto.

```bash
sudo chown -R $USER:$USER /mnt/NomeDoDisco1 /mnt/NomeDoDisco2
```

O flag `-R` aplica a alteração recursivamente (ao diretório e a todo o conteúdo dentro dele).

> ⚠️ **Discos ExFAT não devem ser incluídos neste passo.** O ExFAT não suporta permissões POSIX (o sistema de permissões nativo do Linux). Qualquer `chown` ou `chmod` aplicado é silenciosamente ignorado. As permissões para ExFAT são definidas exclusivamente através das opções de montagem no `fstab` (via `uid` e `gid`), que veremos no próximo passo.

Para confirmar o teu `uid` e `gid` (necessário para o ExFAT):

```bash
id
```

Se fores o primeiro utilizador criado no sistema, o resultado será `uid=1000` e `gid=1000`.

---

## 4. Configurar o fstab

O `/etc/fstab` (File System Table) é o ficheiro que o Linux lê durante o arranque para saber que discos montar, onde, e com que opções. Sem uma entrada no `fstab`, os discos não montam automaticamente — terias de correr `mount` manualmente após cada reinício.

Este é o passo mais crítico do guia. Um erro de sintaxe aqui pode impedir o arranque do sistema.

### Fazer backup

Antes de qualquer edição, cria uma cópia de segurança:

```bash
sudo cp /etc/fstab /etc/fstab.backup
```

Para restaurar em caso de emergência:

```bash
sudo cp /etc/fstab.backup /etc/fstab
```

### Editar o ficheiro

```bash
sudo nano /etc/fstab
```

Vai até ao final do ficheiro (sem tocar nas linhas existentes) e adiciona as entradas correspondentes ao filesystem dos teus discos.

> ⚠️ Substitui `o-teu-uuid-aqui` pelo UUID real de cada disco, obtido no passo 0. Um UUID errado significa um disco que não monta — ou pior, o disco errado a montar no sítio errado.

---

### Opção A — BTRFS

O BTRFS é o filesystem recomendado para SSDs no Fedora. Suporta compressão transparente, snapshots e auto-reparação.

```
# -------------------------------------------------------------------
# Discos Secundários (adicionados manualmente)
# -------------------------------------------------------------------

# Disco 1 (SSD NVMe)
UUID=o-teu-uuid-aqui  /mnt/NomeDoDisco1  btrfs  defaults,noatime,compress=zstd,space_cache=v2  0  2

# Disco 2 (SSD SATA)
UUID=o-teu-uuid-aqui  /mnt/NomeDoDisco2  btrfs  defaults,noatime,compress=zstd,space_cache=v2  0  2
```

### Opção B — EXT4

O EXT4 é o filesystem mais maduro e estável do Linux. Menos funcionalidades que o BTRFS, mas extremamente fiável.

```
# -------------------------------------------------------------------
# Discos Secundários (adicionados manualmente)
# -------------------------------------------------------------------

# Disco 1 (SSD NVMe)
UUID=o-teu-uuid-aqui  /mnt/NomeDoDisco1  ext4  defaults,noatime  0  2

# Disco 2 (SSD SATA)
UUID=o-teu-uuid-aqui  /mnt/NomeDoDisco2  ext4  defaults,noatime  0  2
```

### Opção C — ExFAT

O ExFAT é ideal para discos de arquivo partilhados entre Linux e Windows. Não suporta permissões POSIX, journaling nem compressão — mas é universalmente compatível.

```
# Disco de Arquivo (HDD)
UUID=o-teu-uuid-aqui  /mnt/NomeDoDiscoArquivo  exfat  defaults,nofail,uid=1000,gid=1000,dmask=022,fmask=133  0  0
```

---

### Referência de flags

| Flag | Filesystems | O que faz |
|---|---|---|
| `defaults` | Todos | Conjunto padrão: `rw,suid,dev,exec,auto,nouser,async` |
| `noatime` | Todos | Não atualiza timestamps de acesso — menos escritas, mais performance |
| `compress=zstd` | BTRFS | Compressão transparente com Zstandard. Poupa espaço e pode até aumentar velocidade em SSDs rápidos porque reduz dados transferidos |
| `space_cache=v2` | BTRFS | Cache otimizado de blocos livres — acelera escritas |
| `nofail` | Todos | Não bloqueia o arranque se o disco falhar ou não estiver presente |
| `uid=1000,gid=1000` | ExFAT | Define o proprietário dos ficheiros (obrigatório porque ExFAT não tem permissões POSIX). Confirmar com `id` |
| `dmask=022` | ExFAT | Permissões de diretórios → `755` (dono lê/escreve/executa, outros só leem) |
| `fmask=133` | ExFAT | Permissões de ficheiros → `644` (dono lê/escreve, outros só leem) |
| `0` (5.º campo) | Todos | Desativa o `dump` — ferramenta de backup legacy |
| `2` (6.º campo) | BTRFS/EXT4 | Ordem de verificação no arranque: verificar depois do disco raiz (`1`). ExFAT usa `0` porque não suporta `fsck` no Linux |

Gravar com `CTRL + O` → `Enter` → `CTRL + X`

---

## 5. Testar e Validar

Reiniciar com um `fstab` com erros pode resultar num sistema que não arranca (kernel panic ou modo de emergência). Por isso, **nunca reinicies sem testar primeiro**. Este passo permite verificar se tudo está correto enquanto ainda tens o sistema funcional para corrigir erros.

### Validar a sintaxe do fstab

```bash
sudo findmnt --verify
```

Este comando analisa o `fstab` e reporta erros de sintaxe, UUIDs inválidos e opções mal formadas — sem montar nada. Se devolver warnings ou erros, volta ao passo 4 e corrige antes de continuar.

### Recarregar o systemd

```bash
sudo systemctl daemon-reload
```

O systemd gera unidades de montagem (`.mount`) a partir do `fstab`. Este comando força o systemd a reler o ficheiro e atualizar as unidades internas. Sem isto, o systemd pode continuar a usar a versão antiga em cache.

### Montar tudo

```bash
sudo mount -a
```

O flag `-a` monta todas as entradas do `fstab` que ainda não estão montadas — é o equivalente a simular um reboot sem reiniciar.

> ⚠️ Se este comando devolver erros, **não reinicies o sistema**. Corrige o `fstab` primeiro ou restaura o backup:
> ```bash
> sudo cp /etc/fstab.backup /etc/fstab
> ```

### Confirmar que tudo montou

```bash
df -h | grep /mnt/
```

> ✅ Deverás ver uma linha por cada disco, com o tamanho, espaço usado e ponto de montagem. Se todos aparecem — está feito.

---

## 6. Impedir Montagem Automática pelo Gestor de Ficheiros

Mesmo com os discos configurados no `fstab`, o `udisks2` (o serviço que o Dolphin, Nautilus e outros gestores de ficheiros usam para montar discos) pode tentar montá-los outra vez em `/run/media/`. Isto resulta em discos montados em dois sítios ao mesmo tempo.

A solução é criar regras `udev` que dizem ao `udisks2` para ignorar estes discos completamente. Substitui cada UUID pelo UUID real:

```bash
sudo bash -c 'cat >> /etc/udev/rules.d/99-ignorar-discos.rules << EOF
# Impedir automount dos discos internos pelo gestor de ficheiros
ENV{ID_FS_UUID}=="uuid-do-disco-1", ENV{UDISKS_IGNORE}="1"
ENV{ID_FS_UUID}=="uuid-do-disco-2", ENV{UDISKS_IGNORE}="1"
ENV{ID_FS_UUID}=="uuid-do-disco-3", ENV{UDISKS_IGNORE}="1"
EOF'
```

Recarregar as regras:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Os discos continuam acessíveis em `/mnt/`. Apenas deixam de aparecer como "dispositivos removíveis" na barra lateral do gestor de ficheiros. Podes adicionar bookmarks para os diretórios em `/mnt/` se quiseres acesso rápido.

---

## Resolução de Problemas

| Problema | Causa Provável | Solução |
|---|---|---|
| `mount: wrong fs type` | UUID errado ou filesystem diferente do declarado no `fstab` | Verificar com `lsblk -f` e corrigir a entrada |
| `mount: mount point does not exist` | Falta criar o diretório do passo 2 | `sudo mkdir -p /mnt/NomeDoDisco` |
| Disco aparece no gestor de ficheiros E em `/mnt/` | O `udisks2` está a montar em paralelo | Seguir o passo 6 |
| `Permission denied` ao escrever | `chown` não aplicado ou ExFAT sem `uid/gid` no `fstab` | Rever passo 3 ou as opções ExFAT no passo 4 |
| Sistema não arranca após edição | Erro grave no `fstab` | Arrancar em modo de recuperação, montar `/` como rw, restaurar `/etc/fstab.backup` |
| Disco não monta mas sem erros | `nofail` está ativo e o disco não está ligado | Verificar cabos e conexões, depois `lsblk` para confirmar deteção |

---

## Notas Finais

- Após tudo configurado, **reinicia o sistema** para confirmar que os discos montam automaticamente durante o arranque. Este é o teste real.
- Se adicionares ou removeres discos no futuro, repete este processo a partir do passo 0.
- Para verificar a saúde de discos BTRFS: `sudo btrfs filesystem show`
- Para verificar a saúde de discos EXT4: `sudo e2fsck -n /dev/sdXN` (apenas com o disco desmontado)
- Para verificar a saúde de discos com SMART: `sudo smartctl -a /dev/sdX` (requer o pacote `smartmontools`)
