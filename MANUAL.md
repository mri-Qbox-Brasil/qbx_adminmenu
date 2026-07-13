# Manual do qbx_adminmenu

Ferramentas administrativas abrangentes para Qbox — menu de administração e comandos essenciais para administradores de servidor.

## Funcionalidades Principais

### Gerenciamento de Jogadores
- **Informações**: Visualizar informações detalhadas e identificadores
- **Ações**: Matar, reviver, congelar, expulsar, banir, teletransportar
- **Noclip**: Modo de câmera livre com velocidade ajustável
- **God Mode**: Invenibilidade do jogador
- **Editor de Dados**: Modificar dados do personagem (rádio, trabalho, nome, etc.)

### Visualização
- **Nomes**: Alternar nomes dos jogadores acima das cabeças
- **Blips**: Alternar blips dos jogadores no mapa
- **Routing Bucket**: Alterar o routing bucket do jogador

### Gerenciamento de Veículos
- **Spawnar**: Criar veículos instantaneamente
- **Tomar Posse**: Assumir veículo atual
- **God Mode do Veículo**: Tornar veículo indestrutível
- **Salvar**: Salvar veículo no banco de dados

### Recursos Admin
- **Troca de Modelo**: Alterar skin do jogador instantaneamente
- **Menu de Roupas**: Abrir menu de roupas para jogadores
- **Permissões ACE**: Modificar permissões ACE
- **Entregar Armas**: Dar todas as armas ao jogador
- **Munição Infinita**: Munição ilimitada para todas as armas

### Relatórios
- Jogadores podem enviar relatórios
- Administradores podem revisar e responder
- Sistema integrado de suporte

### Ferramentas de Desenvolvedor
- **Copiar Vetor**: Copiar vec2/vec3/vec4/heading para área de transferência
- Auxílio para criação de coordenadas

## Comandos

### Jogadores

| Comando | Descrição |
|----------|-------------|
| `/report <message>` | Enviar um relatório aos administradores |

### Administradores

| Comando | Permissão | Descrição |
|----------|-------------|-------------|
| `/admin` | `command.useMenu` | Abrir menu admin |
| `/noclip` | `command.noclip` | Alternar modo noclip |
| `/names` | `command.names` | Alternar nomes dos jogadores |
| `/blips` | `command.blips` | Alternar blips dos jogadores |
| `/setmodel [model] [id?]` | `command.setModel` | Alterar modelo do jogador |
| `/admincar` | `command.saveVehicle` | Spawnar/tomar posse de veículo |
| `/vec2` | `command.dev` | Copiar vec2 para área de transferência |
| `/vec3` | `command.dev` | Copiar vec3 para área de transferência |
| `/vec4` | `command.dev` | Copiar vec4 para área de transferência |
| `/heading` | `command.dev` | Copiar heading para área de transferência |

## Configuração

### config/server.lua
```lua
config = {
    commandPerms = {
        useMenu = 'group.admin',
        noclip = 'group.admin',
        names = 'group.admin',
        blips = 'group.admin',
        setModel = 'group.admin',
        saveVehicle = 'group.admin',
        dev = 'group.admin'
    }
}
```

## Opções do Menu Admin

| Opção | Descrição |
|--------|-------------|
| 🎮 **Noclip** | Alternar modo de câmera livre |
| 🏥 **Reviver** | Reviver um jogador morto |
| 👻 **Invisibilidade** | Ficar invisível para outros |
| ⚡ **God Mode** | Não receber dano |
| 📋 **Nomes** | Mostrar/esconder nomes dos jogadores |
| 📍 **Blips** | Mostrar/esconder blips no mapa |
| 🚗 **God Mode do Veículo** | Veículo indestrutível |
| 🎭 **Alterar Modelo** | Alterar skin do jogador |
| 🔫 **Munição Infinita** | Munição ilimitada |
| 🔫 **Entregar Armas** | Entregar conjuntos (pistola, SMG, shotgun, assault, LMG, sniper, heavy) |
| 🔗 **Algemar Jogador** | Algemar um jogador |

## Exports (API)

### Server Exports

| Export | Parâmetros | Retorno | Descrição |
|--------|------------|--------|-------------|
| `SendReport` | `source, message` | - | Enviar um relatório |
| `GetPlayers` | - | `table[]` | Obter jogadores com informações |

### Client Exports

| Export | Parâmetros | Retorno | Descrição |
|--------|------------|--------|-------------|
| `SaveCarDialog` | - | `boolean` | Diálogo de confirmação para salvar carro |
| `GetVehicleInfo` | - | `string, table` | Modelo e propriedades do veículo atual |

## Eventos

### Client Events

| Evento | Payload | Descrição |
|-------|----------|-------------|
| `qbx_admin:client:openMenu` | - | Abrir menu admin |
| `qbx_admin:client:ToggleNoClip` | - | Alternar noclip |
| `qbx_admin:client:names` | - | Alternar nomes |
| `qbx_admin:client:blips` | - | Alternar blips |
| `qbx_admin:client:setModel` | `model` | Definir modelo |
| `qbx_admin:client:Show` | - | Atualizar blips e tags |

### Server Events

| Evento | Payload | Descrição |
|-------|----------|-------------|
| `qbx_admin:server:giveAllWeapons` | `weaponType` | Entregar armas |
| `qbx_admin:server:getPlayers` | - | Callback de jogadores |

## Estrutura de Arquivos

```
qbx_adminmenu/
├── client/
│   ├── admin.lua          # Menu admin, noclip, godmode, blips
│   ├── player.lua          # Ações no jogador
│   ├── vehicle.lua         # Funções de veículo
│   ├── reports.lua         # Sistema de relatórios
│   ├── vectors.lua         # Cópia de vetores
│   ├── dev.lua            # Ferramentas de desenvolvedor
│   └── server.lua          # Comunicação servidor
├── server/
│   ├── main.lua           # Funções admin, jogadores
│   └── commands.lua       # Comandos admin
├── config/
│   └── server.lua         # Permissões de comandos
└── locales/               # Traduções
```

## Dependências

| Dependência | Versão Mínima | Obrigatória |
|------------|-------------------|----------|
| ox_lib | - | ✅ |
| qbx_core | - | ✅ |
| qbx_vehicles | - | ✅ |

## Permissões ACE

O recurso usa o sistema de permissões ACE do FiveM:
- `group.admin` - Acesso administrativo completo
- Permissões configuráveis em `config/server.lua`

## Solução de Problemas

### Menu não abre
- Verifique se tem permissão `command.useMenu`
- Confirme que o qbx_core está rodando
- Verifique se o ox_lib está atualizado

### Noclip não funciona
- Verifique se tem permissão `command.noclip`
- Confirme que não há conflitos com outros recursos
- Reinicie o recurso se necessário

### Nomes/Blips não aparecem
- Verifique se os jogadores estão online
- Confirme que as funções estão ativadas
- Verifique se há erros no console

### Relatórios não chegam
- Verifique se o sistema de relatórios está ativo
- Confirme que há admins online
- Verifique os eventos no servidor
