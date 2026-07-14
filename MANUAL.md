# qbx_adminmenu — Manual

Menu administrativo do Qbox construído em cima do `ox_lib`: gerência de jogadores, servidor, veículos, reports e ferramentas de desenvolvimento, com permissões por ACE.

---

## Sumário

1. [Dependências](#dependências)
2. [Instalação](#instalação)
3. [Permissões (ACE)](#permissões-ace)
4. [Configuração](#configuração)
5. [Comandos](#comandos)
6. [Menu](#menu)
7. [Reports](#reports)
8. [Integrações](#integrações)
9. [Entrypoints para outros recursos](#entrypoints-para-outros-recursos)
10. [Localização](#localização)
11. [Estrutura de arquivos](#estrutura-de-arquivos)

---

## Dependências

| Recurso | Obrigatório | Observação |
|---|---|---|
| `qbx_core` | Sim | Framework base. Usa `modules/lib.lua` (`qbx.*`), `GetQBPlayers`, `GetPlayer`, `Notify`, `SetPlayerBucket`, `AddPermission`, `RemovePermission`, `GetVehiclesByName`, `GetVehiclesByHash` |
| `ox_lib` | Sim | Menus, input dialogs, callbacks, comandos, locale |
| `oxmysql` | Sim | Grava os bans na tabela `bans` |
| `ox_inventory` | Sim | Abrir inventário de um jogador (opção "Open Inventory") |
| `pma-voice` | Sim | Lista de rádio (`getPlayersInRadioChannel`) e mute (`toggleMutePlayer`) |
| `qbx_vehicles` | Sim | `/admincar` — `CreatePlayerVehicle` e `SetPlayerVehicleOwner` |
| `qbx_vehiclekeys` | Sim | Entrega as chaves do veículo spawnado, quando o `mri_Qcarkeys` não está rodando |
| `mri_Qcarkeys` | Não | Se estiver `started`, substitui o `qbx_vehiclekeys` na entrega de chaves (`GiveTempKeys`) |
| `qb-weathersync` | Não | Necessário para as opções de clima e hora do menu de servidor |
| `qbx_medical` | Não | Necessário para a opção "Revive" (evento `qbx_medical:client:playerRevived`) |
| `qb-clothing` | Não | Necessário para a opção "Give Clothing Menu" |

---

## Instalação

1. Copie a pasta `qbx_adminmenu` para `resources/`.
2. Adicione ao `server.cfg`, depois do `qbx_core`:
   ```
   ensure qbx_adminmenu
   ```
3. A tabela `bans` usada pelo ban do menu é a do `qbx_core` — nenhum SQL adicional é necessário se o core já estiver instalado.
4. Configure as ACEs (veja abaixo). Sem elas ninguém abre o menu.
5. **Conflitos** — não rode junto com o `qb-admin`. Ambos registram `/admin`, `/names`, `/blips` e `/admincar`.

---

## Permissões (ACE)

Todo o recurso é protegido por `IsPlayerAceAllowed`. Os nomes das permissões vêm de `config/server.lua` e usam, por padrão, os grupos do `qbx_core`:

```
add_ace group.mod mod allow
add_ace group.admin admin allow
add_ace group.god god allow
```

Os grupos são hierárquicos no `qbx_core`, então um `admin` também satisfaz um gate de `mod`.

| Permissão | Cobre |
|---|---|
| `mod` | Abrir o menu (`useMenu`), responder reports, `/names`, `/blips`, opções gerais sobre jogadores, kick, lista de rádio |
| `admin` | Ban, mudar permissões, alterar dados do personagem, dar armas, menu de roupas, `/admincar`, `/setmodel`, comandos de dev (`/vec2`, `/vec3`, `/vec4`, `/heading`) |

---

## Configuração

Arquivo: `config/server.lua` (retorna uma tabela).

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `commandPerms.useMenu` | string (ACE) | Sim | Permissão do comando `/admin` e do gate de abertura do menu |
| `commandPerms.reportReply` | string (ACE) | Sim | Quem recebe e responde reports |
| `commandPerms.noclip` | string (ACE) | Sim | Permissão do comando `/noclip` (comando atualmente comentado em `server/commands.lua`) |
| `commandPerms.names` | string (ACE) | Sim | Permissão do comando `/names` |
| `commandPerms.blips` | string (ACE) | Sim | Permissão do comando `/blips` |
| `commandPerms.saveVehicle` | string (ACE) | Sim | Permissão do comando `/admincar` |
| `commandPerms.setModel` | string (ACE) | Sim | Permissão do comando `/setmodel` |
| `commandPerms.dev` | string (ACE) | Sim | Permissão dos comandos `/vec2`, `/vec3`, `/vec4` e `/heading` |
| `eventPerms.playerOptionsGeneral` | string (ACE) | Sim | Matar, reviver, congelar, ir até, trazer, sentar no veículo, mudar routing bucket |
| `eventPerms.kick` | string (ACE) | Sim | Expulsar jogador |
| `eventPerms.ban` | string (ACE) | Sim | Banir jogador |
| `eventPerms.changePerms` | string (ACE) | Sim | Alterar o grupo de permissão de um jogador |
| `eventPerms.changePlayerData` | string (ACE) | Sim | Editar dados do personagem (nome, fome, sede, stress, colete, telefone, dinheiro, job, gang, rádio, reputações) |
| `eventPerms.giveAllWeapons` | string (ACE) | Sim | Dar todas as armas de uma categoria |
| `eventPerms.getRadioList` | string (ACE) | Sim | Listar jogadores em uma frequência de rádio |
| `eventPerms.useMenu` | string (ACE) | Sim | Gate dos callbacks de listagem de jogadores e spawn de veículo |
| `eventPerms.clothingMenu` | string (ACE) | Sim | Abrir o menu de roupas de outro jogador |
| `weaponList` | tabela de arrays | Sim | Armas entregues por categoria: `pistol`, `smg`, `shotgun`, `assault`, `lmg`, `sniper`, `heavy` |

---

## Comandos

| Comando | Permissão | Descrição |
|---|---|---|
| `/report [mensagem]` | Qualquer jogador | Abre um report para a staff online |
| `/admin` | `commandPerms.useMenu` | Abre o menu administrativo |
| `/names` | `commandPerms.names` | Liga/desliga os nomes dos jogadores sobre as cabeças |
| `/blips` | `commandPerms.blips` | Liga/desliga os blips dos jogadores no mapa |
| `/admincar` | `commandPerms.saveVehicle` | Registra o veículo em que você está como propriedade do seu personagem |
| `/setmodel [model] [id?]` | `commandPerms.setModel` | Troca o modelo do ped seu ou do jogador informado |
| `/vec2` | `commandPerms.dev` | Copia a `vector2` atual para a área de transferência |
| `/vec3` | `commandPerms.dev` | Copia a `vector3` atual |
| `/vec4` | `commandPerms.dev` | Copia a `vector4` (coords + heading) |
| `/heading` | `commandPerms.dev` | Copia o heading atual |

O comando `/noclip` existe em `server/commands.lua` mas está comentado; a permissão `commandPerms.noclip` continua no config para quando ele for reativado.

---

## Menu

Aberto com `/admin`. Navegação por `lib.registerMenu` (canto superior direito).

| Submenu | O que faz |
|---|---|
| **Player Management** | Lista os jogadores online com ID, citizenid, nome, fome, sede, stress, colete, telefone, reputações, dinheiro, job, gang e identificadores (license, discord, steam). Por jogador: opções gerais (kill, revive, freeze, go to, bring, sentar no veículo, routing bucket), administração (kick, ban com horas/dias/meses, alterar permissão) e extras (abrir inventário, menu de roupas, dar item, mutar no rádio) |
| **Server Management** | Trocar clima, trocar hora, listar jogadores em uma frequência de rádio, abrir um stash pelo nome |
| **Vehicles** | Spawnar veículo (agrupado por categoria do `qbx_core`), consertar (`/fix`), comprar (`/admincar`), remover (`/dv`), trocar a placa (máx. 8 caracteres). A opção "Tune Vehicle" existe mas apenas avisa que ainda não há suporte |
| **Developer Options** | Copiar vector2/3/4 e heading, exibir coordenadas na tela, exibir telemetria do veículo (embreagem, marcha, RPM, temperatura, óleo, ângulo de esterço, lataria, sujeira, velocidade máxima estimada, net ID, hash, nome) |
| **Pending Reports** | Ver, responder e apagar reports abertos |

O submenu **Admin Options** (noclip, revive próprio, invisível, godmode, names, blips, godmode de veículo, trocar ped, munição infinita, dar todas as armas de uma categoria, algemar) está implementado em `client/admin.lua`, mas sua entrada no menu principal está comentada em `client/main.lua`. Para usá-lo, descomente a linha `label1` das opções do menu principal.

---

## Reports

- Qualquer jogador abre um report com `/report [mensagem]`.
- Todo staff com a permissão `commandPerms.reportReply` recebe uma notificação de novo report.
- No submenu **Pending Reports**, o staff vê a fila (`id`, quem enviou, mensagem, quem reivindicou), responde por chat (`[REPORT #id] [nome] mensagem` enviado ao autor) e apaga o report.
- O primeiro staff que responder passa a constar como `claimed` e todos os demais são avisados.
- A fila fica em memória (tabela `REPORTS` no servidor) e é perdida em restart do recurso.

---

## Integrações

### mri_Qcarkeys

No spawn de veículo pelo menu, se `GetResourceState('mri_Qcarkeys') == 'started'`, as chaves são entregues por `exports.mri_Qcarkeys:GiveTempKeys(source, plate)`. Caso contrário cai no `exports.qbx_vehiclekeys:GiveKeys(source, plate)`.

### pma-voice

Alimenta duas opções: "Get Radio List" (lista quem está em uma frequência, via `getPlayersInRadioChannel`) e "Mute" nos extras do jogador (`toggleMutePlayer`).

### qb-weathersync

As opções de clima e hora do menu de servidor disparam `qb-weathersync:server:setWeather` e `qb-weathersync:server:setTime`. Sem o recurso, essas opções não têm efeito.

### qbx_medical

A opção "Revive" dispara `qbx_medical:client:playerRevived` no alvo.

### qb-clothing

A opção "Give Clothing Menu" dispara `qb-clothing:client:openMenu` no alvo.

### ox_inventory

A opção "Open Inventory" chama `exports.ox_inventory:openInventory('player', id)`. O "Pull Stash" do menu de servidor usa os eventos de compatibilidade `inventory:server:OpenInventory` / `inventory:client:SetCurrentStash`.

---

## Entrypoints para outros recursos

O recurso não expõe exports. A interação com ele é por eventos e callbacks — todos protegidos por ACE no lado servidor.

### Abrir o menu em um jogador

```lua
TriggerClientEvent('qbx_admin:client:openMenu', source)
```

### Outros eventos de cliente

```lua
TriggerClientEvent('qbx_admin:client:setModel', source, 'a_m_m_soucent_04')
TriggerClientEvent('qbx_admin:client:names', source)   -- alterna nomes
TriggerClientEvent('qbx_admin:client:blips', source)   -- alterna blips
TriggerClientEvent('qbx_admin:client:copyToClipboard', source, 'coords4')
TriggerClientEvent('qbx_admin:client:killPlayer', source)
```

### Eventos de servidor

```lua
TriggerServerEvent('qbx_admin:server:playerOptionsGeneral', opcao, jogadorSelecionado, input)
TriggerServerEvent('qbx_admin:server:playerAdministration', opcao, jogadorSelecionado, input)
TriggerServerEvent('qbx_admin:server:changePlayerData', campo, jogadorSelecionado, input)
TriggerServerEvent('qbx_admin:server:giveAllWeapons', 'pistol', playerId)
TriggerServerEvent('qbx_admin:server:sendReply', report, mensagem)
TriggerServerEvent('qbx_admin:server:deleteReport', report)
```

### Callbacks (`lib.callback`)

```lua
lib.callback.await('qbx_admin:server:canUseMenu', false)
lib.callback.await('qbx_admin:server:getPlayers', false)
lib.callback.await('qbx_admin:server:getPlayer', false, playerId)
lib.callback.await('qbx_admin:server:getReports', false)
lib.callback.await('qbx_admin:server:spawnVehicle', false, model)
lib.callback.await('qbx_admin:server:clothingMenu', false, targetId)
lib.callback.await('qbx_admin:callback:getradiolist', false, frequencia)
```

---

## Localização

Strings via `ox_lib` locale. Arquivos em `locales/`:

- `cs.json`, `da.json`, `de.json`, `en.json`, `es.json`, `fr.json`, `it.json`, `pt-br.json`, `pt.json`, `sv.json`

Idioma ativo pela convar no `server.cfg`:

```
setr ox:locale "pt-br"
```

---

## Estrutura de arquivos

```
qbx_adminmenu/
├── client/
│   ├── main.lua      — menu principal e navegação entre submenus
│   ├── admin.lua     — noclip, godmode, invisível, munição infinita, dar armas, algemar
│   ├── player.lua    — lista de jogadores e submenus por jogador (geral, administração, extras)
│   ├── server.lua    — clima, hora, lista de rádio, abrir stash
│   ├── vehicle.lua   — spawn por categoria, fix, admincar, dv, troca de placa
│   ├── dev.lua       — coordenadas na tela e telemetria de veículo
│   ├── reports.lua   — fila de reports: ver, responder, apagar
│   └── vectors.lua   — cópia de vector2/3/4 e heading para a área de transferência
├── server/
│   ├── main.lua      — permissões, reports, ban/kick, edição de dados, callbacks
│   └── commands.lua  — /report, /admin, /names, /blips, /admincar, /setmodel, /vec*, /heading
├── config/
│   └── server.lua    — permissões ACE por comando e por evento, lista de armas por categoria
├── locales/          — cs, da, de, en, es, fr, it, pt-br, pt, sv
└── fxmanifest.lua
```
