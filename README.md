# UTL-X — Otimizador de Jogos para Windows

**UTL-X** é uma ferramenta de otimização para Windows com interface moderna em CustomTkinter, focada em performance para jogos. Combina limpeza do sistema, ajustes do Windows, otimização de rede e um **Game Booster** que roda em segundo plano.

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Plataforma](https://img.shields.io/badge/Plataforma-Windows-lightgrey.svg)
![Licença](https://img.shields.io/badge/Licença-MIT-green.svg)

---

## ✨ Funcionalidades

### 🏠 Home
- Informações do sistema (CPU, RAM, GPU, Disco, Versão do Windows)
- Gráficos de uso de recursos em tempo real
- Ações rápidas: limpar arquivos temporários, flush DNS, reiniciar Explorer

### 🧹 Limpeza
- Limpadores baseados em toggles:
  - Arquivos temporários (`%TEMP%`, `C:\Windows\Temp`, Prefetch)
  - Cache do Windows Update
  - Arquivos do Delivery Optimization
  - Lixeira
  - Caches de navegadores (Edge, Chrome, Firefox, Opera, Brave)
  - Histórico de verificação do Windows Defender
  - Lista de standby da memória (requer Admin)
- Estado dos toggles salvo persistentemente no config

### ⚙️ Windows Tweaks
- **Privacidade/Telemetria**: Desabilita DiagTrack, WAP, PcaSvc, chaves de telemetria no registro
- **Visual/UX**: Desabilita animações, transparência, Game Bar, Xbox DVR, Sticky Keys, Inicialização Rápida
- **Performance**: Plano de Energia Ultimate, desabilita SysMain, indexação do Windows Search, HPET
- **Menu de Contexto**: Mostra "Mostrar mais opções" por padrão (estilo clássico Win10)
- **Um clique** "Aplicar Tudo" / "Reverter Tudo"

### 🌐 Rede
- **Flush DNS** + registrar/renovar
- **Otimizador TCP** (ajustes de registro estilo CTO)
- **DSCP / QoS** ("Não usar NLA" para prioridade de jogos)
- **Reset de Rede** (winsock, int ip, dns, firewall)

### 🚀 Game Booster
- Adicione jogos personalizados (nome, caminho do `.exe`, ícone extraído automaticamente)
- Toggles por jogo:
  - **Classe de Prioridade** → `REALTIME_PRIORITY_CLASS` via Image File Execution Options
  - **DSCP/QoS** → Ajuste de registro para prioridade de rede
  - **Limpeza de RAM** → Purga lista de standby a cada 30s enquanto o jogo roda (requer Admin)
- **Monitor em segundo plano**: minimiza para a bandeja do sistema, continua otimizando enquanto você joga
- Um clique em "Iniciar Booster" lança o jogo + inicia o monitor
- Reverte todos os ajustes automaticamente quando o jogo fecha

### 🎮 Jogos (Biblioteca)
- Grade visual de cards com ícones, badges de status (ONLINE / CAMINHO INVÁLIDO)
- Adicionar / editar / remover perfis
- Extração automática de ícone do `.exe` (cacheado como PNG)

### 🔧 Ferramentas
- Lista de processos com busca, CPU/RAM, "Finalizar Tarefa" (Admin)
- Gerenciador de inicialização (ativar/desativar via registro)
- Gerenciador de serviços (iniciar/parar/reiniciar, definir tipo de inicialização)
- Editor do arquivo Hosts (backup + restauração)
- **Executador de Comandos** com presets admin (sfc, dism, chkdsk, netsh, etc.)

### 🔔 Avisos
- Notificações toast (Central de Ações Windows 10/11)
- Visualizador de logs in-app (lê `utl-x.log`)

### ⚙️ Configuração
- Idioma: Português / Inglês
- Tema: Escuro / Claro / Sistema
- Iniciar minimizado na bandeja
- Verificar atualizações (GitHub Releases)

---

## 📦 Instalação

### Via Código Fonte
```bash
git clone https://github.com/SEU_USUARIO/UTL-X.git
cd UTL-X
pip install -r requirements.txt
python UTL-X.py
```

### Requisitos
```txt
customtkinter>=5.2.0
Pillow>=10.0.0
psutil>=5.9.0
pystray>=0.19.0
```

> **Nota**: `pystray` precisa de backend de bandeja do sistema. No Windows funciona nativamente. Para apps compilados, inclua `pystray` no spec do PyInstaller.

### Compilar para `.exe` (PyInstaller)
```bash
pip install pyinstaller
pyinstaller --noconfirm --onefile --windowed \
  --icon=icon.ico \
  --add-data "icon.ico;." \
  --name "UTL-X" \
  UTL-X.py
```
- O app detecta `sys._MEIPASS` e carrega `icon.ico` para a barra de tarefas/título automaticamente.

---

## 🛡️ Permissões
- **Administrador necessário** para:
  - Purga da lista de standby (limpeza de RAM)
  - Escrita no registro em `HKLM` (DSCP, Prioridade, Tweaks do Windows)
  - Controle de serviços
  - Edição do arquivo hosts
- O app **não** auto-eleva. Rode como Admin manualmente ou via atalho.

---

## 📁 Locais dos Dados
| Dado | Caminho |
|------|---------|
| Perfis de jogos (`jogos.json`) | `%APPDATA%\UTL-X\` |
| Ícones personalizados (extraídos) | `%APPDATA%\UTL-X\icones\` |
| Config (`config.json`) | `%APPDATA%\UTL-X\` |
| **Logs** (`utl-x.log`) | `%APPDATA%\UTL-X\logs\` |

Logs rotacionam a 1 MB (3 backups). Visualize in-app via **Avisos → Ver Logs**.

---

## 🗂️ Estrutura do Projeto
```
UTL-X/
├── UTL-X.py          # Aplicação em arquivo único (~6100 linhas)
├── icon.ico          # Ícone da janela/barra de tarefas (compile com o app)
├── requirements.txt
└── README.md
```

---

## ⌨️ Atalhos de Teclado
| Tecla | Ação |
|-------|------|
| `Esc` | Fechar diálogos / popups |
| `Enter` | Confirmar em diálogos |

---

## 🔒 Notas de Segurança
- **Sem telemetria**, sem chamadas de rede exceto:
  - API do GitHub (verificação de atualizações, opcional)
  - Conteúdo raw do GitHub (download de ferramentas, opcional)
- Verificação SSL **desabilitada globalmente** por simplicidade (`ssl._create_unverified_context`). Revise se for implantar em ambientes sensíveis.
- Usa `ntdll.NtSetSystemInformation` não documentado (SystemInformationClass 80) para purga da standby list — técnica padrão mas não suportada oficialmente pela Microsoft.
- `REALTIME_PRIORITY_CLASS` pode prejudicar threads do sistema; use com cautela.
- Modifica chaves `HKLM` do registro — backup recomendado.

---

## 🌐 Localização
Todas as strings da UI usam um dicionário `TRANSLATIONS` (PT → EN). Adicione novos idiomas estendendo o dicionário e atualizando `self.idioma`.

---

## 🤝 Contribuindo
1. Faça fork do repositório
2. Crie uma branch para a feature
3. Commit as mudanças
4. Abra um Pull Request

---

## 📄 Licença
Licença MIT — veja [LICENSE](LICENSE) para detalhes.

---

## 🙏 Créditos
- **Desenvolvedor**: GML10
- **Bibliotecas**: CustomTkinter, Pillow, psutil, pystray
- **Ícones**: Programáticos (raio + texto "UTL-X")

---

## 📸 Capturas de Tela
> Adicione screenshots aqui (Home, Game Booster, Limpeza, etc.)

---

**Feito para gamers Windows que querem controle sem bloat.**