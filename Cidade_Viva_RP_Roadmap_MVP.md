# Cidade Viva RP — Roadmap do MVP + Plano de Aprendizado

**Para:** dev solo iniciante · **Engine:** Unreal Engine 5.8 · **Alvo:** Android
**Meta deste roadmap:** sair de "tenho um documento" para "tenho um joguinho rodando no meu celular", em passos que não dá pra travar.

---

## 0. Como usar

- Faça **um micro-passo de cada vez**, na ordem. Não pule.
- Cada passo tem: **o que construir**, **o que ele te ensina** e **pronto quando**.
- Se um passo travar por mais de uns dias, ele era grande demais — quebre em dois.
- **Regra anti-armadilha:** nada de polícia, economia, política ou cidade inteira aqui. Isso é tudo 🔴 e fica no GDD esperando. O MVP é *limpador de vidro num bairro pequeno*.

---

## 1. Três decisões técnicas de iniciante (resolva antes de tudo)

1. **Blueprints primeiro, não C++.** O Documento Mestre fala em Clean Architecture, SOLID e C++ — isso é ótimo *para um time experiente*, mas para você agora é uma armadilha. Comece **100% em Blueprints** (a programação visual da Unreal). C++ entra muito depois, se entrar. Você aprende o jogo, não a sofrer com compilador.
2. **Multiplayer-aware, mas minúsculo.** Conforme o GDD: use a estrutura de rede da Unreal desde o início, mas teste tudo **local** com PIE (Play In Editor) usando "Number of Players = 2". Sem servidor na nuvem, sem contas. (A replicação de verdade só na Fase H — não se assuste agora.)
3. **Versionamento desde o passo 1.** Instale **Git + Git LFS** (LFS é obrigatório porque projetos Unreal têm arquivos binários grandes). Faça commit a cada passo que funcionar. Isso te salva de perder semanas de trabalho.

---

## 2. Fase 0 — Fundamentos (aprender *antes* de construir)

Antes de abrir o projeto do jogo, gaste de 1 a 3 semanas só aprendendo a engine, num projeto de teste descartável:

- Interface da Unreal: viewport, navegação, Content Browser, Outliner, Details.
- O que são **Actor, Pawn, Character, Controller, GameMode** (os tijolos de tudo).
- **Blueprints**: eventos, variáveis, funções, fluxo (Branch, Sequence).
- Importar um asset e colocar na cena.
- Rodar o jogo no editor (Play).

> Onde aprender: a aba **Learning** do Epic Games Launcher e a documentação oficial (dev.epicgames.com) têm trilhas gratuitas de iniciante. Faça o tutorial oficial de "primeiro jogo" inteiro antes de tocar no Cidade Viva.

---

## 3. Roadmap do MVP em micro-passos

### 🟢 Marco A — Um boneco anda
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| A1 | Criar projeto novo a partir do template **Third Person** (Blueprint) | Setup de projeto | O boneco padrão anda no PC ao apertar Play |
| A2 | Configurar e **empacotar para Android**; instalar no seu Redmi 13 | Pipeline mobile (SDK/NDK, packaging) | O boneco anda **no seu celular** |
| A3 | Adicionar **controles touch** (joystick virtual + botão) | Input mobile | Você joga no celular só com o dedo |

> A2 costuma ser o passo mais chato (configurar Android SDK/NDK). Faça cedo — descobrir que não builda no celular *depois* de meses é o pior cenário.

### 🟢 Marco B — Um pedaço de mundo
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| B1 | Modelar/montar **um quarteirão pequeno** (rua + alguns prédios simples) | Level design, materiais básicos | Dá pra andar pela rua |
| B2 | Definir o **aeroporto como ponto de spawn** (pode ser uma área simples) | PlayerStart, fluxo de entrada | O jogo começa o boneco no aeroporto |

> Mantenha **pequeno e leve**. Nada de cidade inteira, nada de Nanite/Lumen pesado. Mire 30 FPS no celular desde já.

### 🟢 Marco C — Interação
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| C1 | **Interagir com um objeto** (chegar perto + botão "E"/toque → acontece algo) | Traces/overlaps, eventos | Aparece "Interagir" perto do objeto e funciona |
| C2 | Um **NPC ("carioca")** parado que dá uma tarefa ao interagir | Blueprint de NPC, diálogo simples | O NPC entrega a tarefa "limpar vidro" |

### 🟢 Marco D — A profissão: limpador de vidro
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| D1 | Pegar a tarefa do NPC → objetivo aparece na HUD | UI/HUD básica, estado de quest | HUD mostra "Vá limpar o vidro do prédio X" |
| D2 | **Sinalizador/waypoint** guiando até o prédio | Marcadores de objetivo (UX mobile) | Uma seta/marca leva você até o local |
| D3 | **Minigame de limpar vidro** (ex.: segurar/arrastar para limpar painéis) | Lógica de gameplay, feedback | Limpar os painéis completa a tarefa |
| D4 | **Receber dinheiro** ao concluir | Variável de economia, recompensa | Seu saldo sobe na HUD |

> Marco D é o **coração do MVP**: o loop "pega tarefa → vai → faz → ganha". Se isso for gostoso de repetir, você tem um jogo.

### 🟢 Marco E — Survival
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| E1 | **Fome e sede** caindo com o tempo (barras na HUD) | Timers, atributos | As barras descem sozinhas |
| E2 | **Comer/beber** repõe (comprar comida com o dinheiro do D4) | Loop econômico fechado | Trabalhar → comprar comida → sobreviver |

### 🟢 Marco F — Inventário e loja simples
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| F1 | **Inventário simples** por slots | UI de inventário, dados | Itens aparecem e podem ser usados |
| F2 | Uma **loja NPC** (comprar comida/itens) | Transação, preço | Você compra e o item entra no inventário |

### 🟢 Marco G — Criação de personagem enxuta
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| G1 | Tela de criação: escolher sexo, cor de pele, 2–3 rostos/cabelos | UI, customização | O personagem criado aparece no jogo |
| G2 | Campos de **identidade** (nome, idade, origem no Brasil) | Salvar dados do player | Os dados ficam guardados na sessão |

### 🟡 Marco H — Replicação básica (o passo difícil)
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| H1 | Fazer movimento e ações funcionarem com **2 jogadores no PIE** | Replicação, autoridade do servidor, RPCs | Dois bonecos se veem andando |
| H2 | Replicar o estado importante (dinheiro, necessidades) com **autoridade no servidor** | Anti-trapaça básico | O servidor manda; o cliente não inventa valores |

> Este é o tópico mais difícil da Unreal para iniciante. Você só chega aqui depois que o single-player já funciona — e, como construiu "multiplayer-aware" desde o começo, não vai reescrever tudo.

### 🟢 Marco I — Polimento e primeiro teste jogável
| Passo | O que construir | Te ensina | Pronto quando |
|------|------------------|-----------|----------------|
| I1 | Áudio básico (passos, UI, ganhar dinheiro), ajuste de FPS no celular | Performance, áudio | 30 FPS estável no Redmi 13 |
| I2 | Dar o APK para **3–5 amigos testarem** | Feedback real | Eles conseguem jogar o loop sem você explicar |

---

## 4. Plano de aprendizado (casado com os marcos)

Estude **só o que o próximo marco exige** — não tente aprender Unreal inteiro de uma vez.

| Antes do marco | Aprenda |
|----------------|---------|
| A | Packaging para Android, Enhanced Input, Touch Interface |
| B | Level design básico, World Partition (leve), materiais |
| C | Line traces, colisões/overlap, Blueprint Interfaces |
| D | UMG (UI/HUD), máquina de estados simples, gameplay com timers |
| E–F | Estruturas de dados (Structs, Data Tables), salvar/carregar (SaveGame) |
| G | UMG avançado, customização de Character |
| H | **Networking/Replication** (a fundo): Replicated variables, RepNotify, Server/Client/Multicast RPCs, Authority |
| I | Profiling em mobile (stat fps, stat unit), Sound Cues |

---

## 5. Anti-armadilhas (cole na parede)

- **Não** comece pela cidade inteira. Comece por uma rua.
- **Não** comece por polícia, economia ou política. Comece pelo limpador de vidro.
- **Não** vá para C++/Clean Architecture agora. Blueprints.
- **Não** tente o multiplayer online (servidores, contas) no MVP. Só PIE local.
- **Não** acumule assets do marketplace sem testar performance em mobile.
- **Faça commit** (Git LFS) a cada passo que funcionar.
- **Termine o MVP** antes de adicionar qualquer coisa do GDD que esteja marcada 🟡 ou 🔴.

---

## 6. Depois do MVP (só pra ter o mapa, não pra fazer agora)

V1 single-player → mais profissões e survival → **então** o salto grande: infraestrutura online (servidor dedicado, contas, persistência) → e só aí os sistemas 🔴 do GDD (polícia por seleção, economia entre players, governo, combate). Cada uma dessas setas é meses de trabalho. Uma de cada vez.
