# Arquitetura do Projeto — PedroPlay: Suíte de Jogos do Alemãozinho Pedro (`Pedro`)

Este documento descreve a arquitetura, a identidade visual, os padrões de projeto, a pilha tecnológica, o fluxo de publicação no GitHub Pages e o funcionamento interno da central e dos jogos interativos contidos neste repositório. Ele serve como referência técnica e guia de contexto para evolução e manutenção do projeto.

---

## 1. Visão Geral & Identidade da Marca

O projeto **PedroPlay** é a central oficial de jogos web interativos e educativos do canal **Alemãozinho Pedro**, projetada especificamente para **crianças (foco na faixa etária de 7 anos)** jogarem no navegador (Desktop, Tablet ou Celular), além de servir como laboratório para gravação de vídeos criando jogos no Antigravity e publicando-os na web.

### Links Oficiais
- **Canal no YouTube:** [https://youtube.com/@alemaozinhopedro](https://youtube.com/@alemaozinhopedro) (`@alemaozinhopedro`)
- **Repositório GitHub:** [https://github.com/patricschurhaus/pedroplay](https://github.com/patricschurhaus/pedroplay)
- **Site Publicado (GitHub Pages):** [https://patricschurhaus.github.io/pedroplay/](https://patricschurhaus.github.io/pedroplay/)

### Estrutura do Diretório
```text
c:\Users\patri\Documents\Pedro\
├── index.html     # Central de Jogos "PedroPlay" (Portal principal e contadores de acesso)
├── avião.html     # Simulador de Cabine: Comissário de Bordo (AeroVoo 702)
├── uno.html       # UNO Mania Kids: Jogo de Cartas 1v1 contra Robô
├── xadrez.html    # Xadrez Clássico & Minimalista com IA Multinível e Modo Didático
├── img/
│   └── logo.png   # Logomarca oficial do canal "Alemãozinho Pedro"
├── .gitignore     # Ignora capturas de tela temporárias na raiz
└── GEMINI.md      # Documento de Arquitetura, Identidade Visual e Diretrizes do Projeto
```

---

## 2. Identidade Visual & Regras de Design (Padrão Obrigatório)

1. **Estética Sóbria e Elegante (Inspirada no GitHub Dark Mode):**
   - Apesar de ser voltado ao público infantil, o design deve ser **sóbrio, minimalista e elegante**, evitando fundos `#000000` puros ou elementos excessivamente carnavalizados.
   - **Paleta Base (GitHub Dark):**
     - Fundo principal (`--gh-bg-canvas`): `#0d1117`
     - Superfícies e cards (`--gh-bg-subtle`): `#161b22`
     - Superfícies elevadas no hover (`--gh-bg-elevated`): `#1c2128`
     - Botões secundários (`--gh-bg-overlay`): `#21262d`
     - Bordas sutis (`--gh-border-default` / `--gh-border-muted`): `#30363d` / `#21262d`
     - Texto primário e secundário: `#e6edf3` e `#8b949e`

2. **Acentos e Hovers nas Cores da Bandeira da Alemanha:**
   - **Preto:** `#000000`
   - **Vermelho:** `#dd0000`
   - **Dourado/Amarelo:** `#ffce00`
   - Utilizados com elegância em subtítulos, divisores discretos e estados `:hover` / `:focus-visible` de cards, botões e ícones.

3. **Tipografia da Logomarca:**
   - Títulos, cabeçalhos e insígnias utilizam a mesma família tipográfica serifada clássica em caixa-alta e negrito presente na inscrição `"ALEMÃOZINHO"` da logomarca:
     `font-family: 'Georgia', 'Times New Roman', Times, serif;`
   - Textos corridos e descrições de interface utilizam fonte de sistema limpa estilo GitHub (`-apple-system, BlinkMacSystemFont, 'Segoe UI', 'Noto Sans', Helvetica, Arial, sans-serif`).

4. **Uso Exclusivo de Bootstrap Icons (Proibido Emojis na UI):**
   - **Regra Estrita:** Sempre utilizar **Bootstrap Icons** carregados via CDN oficial (`https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css`).
   - Os ícones são inseridos como caracteres tipográficos (`<i class="bi bi-..."></i>`), permitindo transição suave de cor (`color`) no `:hover` exatamente como texto. Nunca utilizar emojis nas interfaces novas ou atualizadas.

5. **Logomarca Oficial (`img/logo.png`):**
   - Carregada via `https://raw.githubusercontent.com/patricschurhaus/pedroplay/main/img/logo.png` com fallback local automático `onerror="this.onerror=null;this.src='img/logo.png';"`.
   - Deve ser exibida limpa sobre o fundo `#0d1117`, **sem círculos ou anéis coloridos artificiais** ao redor.

---

## 3. Princípios Arquiteturais Fundamentais

1. **Arquitetura Single-File por Módulo:**
   - Cada página/jogo (`index.html`, `avião.html`, `uno.html`, `xadrez.html`) reside em um único arquivo `.html` autocontido que encapsula CSS3, HTML5 e JavaScript ES6+.
2. **Áudio Sintetizado via Web Audio API:**
   - Todos os efeitos sonoros são gerados matematicamente em tempo real usando a **Web Audio API** nativa do navegador (sem necessidade de arquivos `.mp3`/`.wav`).
3. **UX Pedagógica e Centrada na Criança (7 Anos):**
   - Ritmo calmo e acolhedor, intervalos de pensamento da IA confortáveis e feedback visual/sonoro imediato.
4. **Acessibilidade Tríplice (Mouse, Toque e Teclado):**
   - Suporte simultâneo para clique de mouse, toque em telas *mobile/tablet* e atalhos de teclado.

---

## 4. Detalhamento Técnico por Módulo

### 4.0. Central de Jogos — `index.html`
**Título:** *PedroPlay — Alemãozinho Pedro*

Portal principal de entrada da suíte de jogos, construído com estética GitHub Dark Mode, acentos da bandeira da Alemanha, logomarca oficial, link para o canal do YouTube, atalhos de teclado (`1`, `2`, `3`) e sistema de contagem de acessos.

#### Arquitetura Interna e Classes
- **`VisitCounterManager` (Contadores de Visitas Híbridos Cloud + Local):**
  - Contabiliza e exibe:
    1. Quantas visitas a central `index.html` recebeu (`#counter-index` no cabeçalho).
    2. Quantas visitas/acessos cada jogo teve individualmente (`#counter-aviao`, `#counter-uno`, `#counter-xadrez` nos cards).
    3. Total acumulado de partidas iniciadas (`#counter-total-games` no rodapé).
  - **Armazenamento Duplo:**
    - **Nuvem (`CounterAPI`):** Sincroniza via `https://api.counterapi.dev/v1/patricschurhaus_pedroplay_v1/<chave>/up` para manter a contagem global entre todos os visitantes da internet no GitHub Pages.
    - **Local (`localStorage`):** Salva nas chaves `pedroplay_visits_index`, `pedroplay_visits_aviao`, `pedroplay_visits_uno` e `pedroplay_visits_xadrez`, garantindo funcionamento instantâneo mesmo offline (`file://`).
- **`HubSound`:**
  - Sintetizador acústico discreto (Web Audio API) para feedback sonoro sutil ao passar o mouse (`hover`) ou selecionar um jogo (`select`).

---

### 4.1. Simulador de Cabine — `avião.html`
**Título:** *Comissário de Bordo: Simulador de Cabine (AeroVoo 702)*

Jogo de ação calma em rolagem lateral (*side-scrolling*) onde a criança controla um comissário de bordo atendendo passageiros da Primeira Classe (VIP), Classe Econômica e os Pilotos no Cockpit durante um voo de 3 minutos entre São Paulo (GRU) e Paris (CDG).

#### Arquitetura Interna e Classes
- **`FlightSoundEngine`:**
  - Gera som contínuo de turbina de avião usando um `AudioBuffer` de ruído branco filtrado por um `BiquadFilterNode` (*lowpass* em 140Hz) somado a um oscilador senoidal de baixa frequência (85Hz).
  - Sintetiza o sinal sonoro clássico de cabine (*ding-dong* em 698.46Hz e 523.25Hz), som de rádio dos pilotos, entrega de itens e acordes de sucesso/erro.
- **`TRAY_ITEMS` (Registro Estático):**
  - Mapeia os 5 itens da bandeja pronta para entrega imediata via clique ou atalho de teclado:
    - `[B]` Balinha, `[C]` Café, `[L]` Lanche, `[S]` Snack, `[R]` Bebida.
- **`NinjaFlightSimulator` (Motor Canvas 2D & Game Loop):**
  - **Mundo Virtual:** Dimensões de `2600x720` pixels com câmera suave interpolada (`cameraX`) acompanhando o jogador.
  - **Zonas da Aeronave:**
    1. *Cockpit (`x < 440`):* Para-brisa com nuvens dinâmicas, telas PFD e Radar ND animados, LEDs piscantes e dois pilotos que pedem café/lanche ocasionalmente (`+5 pts`).
    2. *Primeira Classe VIP (`x: 470–1010`):* 3 poltronas executivas douradas (`1A`, `2A`, `3A`). Entregas corretas valem **`+10 pontos`**.
    3. *Classe Econômica (`x: 1060–1940`):* 5 poltronas azuis (`10A` a `14A`). Entregas corretas valem **`+5 pontos`**; erros ou tempo esgotado descontam **`-1 ponto`**.
    4. *Galley / Cozinha de Bordo (`x: 2000–2600`):* Trolleys e cafeteira com animação de vapor.
  - **Controle de Fluxo Calmo (`spawnOrder`):** Limita os pedidos a no máximo **2 passageiros simultâneos**, com barras de paciência longas (`24s` a `35s`) e radar de setas nas bordas da tela (`renderOffScreenAlertArrows`).

---

### 4.2. Jogo de Cartas — `uno.html`
**Título:** *UNO Mania Kids*

Partida de UNO no formato 1 contra 1 (Jogador Humano vs. Robô Amigo), com visual de mesa de feltro escuro, cartas vetoriais construídas em CSS e mecânicas especiais.

#### Arquitetura Interna e Classes
- **`UnoAudio`:**
  - Sintetizador Web Audio API com persistência de *mute* no `localStorage` (`uno_muted`).
- **`ConfettiEffect`:**
  - Sistema de partículas em HTML5 Canvas (`#confettiCanvas`) sobreposto à tela para celebrar vitórias.
- **`UnoDeckManager`:**
  - Gera e embaralha (algoritmo *Fisher-Yates*) um baralho de **113 cartas**, incluindo cartas numéricas (`0–9`), ações (`skip`, `reverse`, `draw2`) e Coringas especiais (`wild`, `wild4`, `wildAll` Baralho Todo de 25 cartas e `swapHands` Trocar de Mão).
- **`UnoGame` (Controlador de Estado e Turnos):**
  - Reciclagem infinita do monte de compras (`ensureDrawPileHasCards`), janela regressiva de 4 segundos para gritar `"UNO!"` e IA amigável (`executeRobotTurn` com `1500ms` de tempo de resposta).

---

### 4.3. Xadrez Didático — `xadrez.html`
**Título:** *Xadrez Clássico & Minimalista*

Aplicação completa de Xadrez com regras oficiais da FIDE, peças vetoriais Staunton de alto contraste, painel lateral otimizado para maximizar o tamanho do tabuleiro (`até 750px / 84vh`) e sistema de tutoria em tempo real.

#### Arquitetura Interna e Classes
- **`PIECE_SVGS`:** Biblioteca de 12 peças vetoriais SVG (`wp` a `bk`).
- **`SynthesizedAudio`:** Efeitos acústicos amadeirados com estado salvo em `localStorage` (`chess_sound_muted`).
- **`ChessEngine`:** Motor completo de regras FIDE (Roque, *En Passant*, Promoção, Xeque-mate, Afogamento e Material Insuficiente).
- **`ChessAI`:** 5 níveis de dificuldade (`super_child`, `child`, `easy`, `medium`, `hard`) salvos em `localStorage` (`chess_difficulty`).
- **`ChessGameController` & Tutoria (`super_child`):** Destaca em verde (`.recommended`) o melhor movimento da peça selecionada, em roxo (`.danger`) casas perigosas e sugere outra peça em dourado (`.suggested-piece`) quando há jogada muito superior no tabuleiro.

---

## 5. Padrões de Estado (`localStorage`)

| Chave `localStorage` | Módulo | Descrição |
| :--- | :--- | :--- |
| `pedroplay_visits_index` | `index.html` | Contador de visitas da página principal `index.html`. |
| `pedroplay_visits_aviao` | `index.html` | Contador de acessos ao jogo `avião.html`. |
| `pedroplay_visits_uno` | `index.html` | Contador de acessos ao jogo `uno.html`. |
| `pedroplay_visits_xadrez` | `index.html` | Contador de acessos ao jogo `xadrez.html`. |
| `uno_muted` | `uno.html` | Estado de silenciamento do áudio no UNO (`'true'` / `'false'`). |
| `chess_sound_muted` | `xadrez.html` | Estado de silenciamento do áudio no Xadrez (`'true'` / `'false'`). |
| `chess_difficulty` | `xadrez.html` | Nível de dificuldade selecionado (`'super_child'`, `'child'`, `'easy'`, `'medium'`, `'hard'`). |

---

## 6. Fluxo de Git, GitHub & Publicação Automática

O repositório local está inicializado na branch `main` e autenticado com a conta `patricschurhaus` ligada ao repositório remoto `https://github.com/patricschurhaus/pedroplay.git` e ao **GitHub Pages**.

### Caminhos dos Binários Instalados no Windows (Escopo do Usuário)
Caso o `PATH` da sessão do PowerShell não carregue automaticamente os executáveis recém-instalados, utilize diretamente os caminhos abaixo:
- **Git (`git.exe`):** `"$env:LOCALAPPDATA\Programs\Git\cmd\git.exe"`
- **GitHub CLI (`gh.exe`):** `"$env:LOCALAPPDATA\Programs\gh\bin\gh.exe"`

### Comando Padrão para Publicar Alterações
```powershell
$gitExe = "$env:LOCALAPPDATA\Programs\Git\cmd\git.exe"
& $gitExe add .
& $gitExe commit -m "feat: descrição da alteração"
& $gitExe push origin main
```
Cada `push` na branch `main` atualiza automaticamente o site em `https://patricschurhaus.github.io/pedroplay/`.
