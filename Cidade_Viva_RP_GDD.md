# Cidade Viva RP — Game Design Document (GDD)

**Versão do GDD:** 0.2 (derivado do Documento Mestre v1.0 · decisões de escopo incorporadas)
**Engine:** Unreal Engine 5.8 · **Plataforma inicial:** Android
**Status:** rascunho de trabalho — feito para evoluir a cada decisão tomada

---

## 0. Como ler este documento (nota de escopo)

Este GDD descreve **dois jogos ao mesmo tempo**:

- **A Visão** — o *Cidade Viva RP* completo do Documento Mestre (governador, eleições, porto, dezenas de profissões, cidade inteira do Rio). É o **norte** de longo prazo.
- **O Primeiro Jogável (MVP)** — uma fatia pequena que dá para *você sozinho terminar*, que ensina os fundamentos da Unreal e que já é gostosa de jogar.

Tudo que está marcado com 🟢 **MVP** é o que você constrói primeiro. O resto (🟡 V1 / 🔴 Visão) fica documentado para não se perder, mas **não se constrói agora**. A regra de ouro de um dev solo iniciante: *se está em dúvida se entra no MVP, não entra.*

> ✅ **Decisão tomada — arquitetura de rede.** O MVP é construído com a **arquitetura multiplayer da Unreal desde o dia 1** (replicação, autoridade do servidor, GameState/PlayerState, RPCs), mas **a infraestrutura online é adiada** (sem servidores na nuvem, sem login/contas, sem banco persistente, sem anti-cheat). O jogo é desenvolvido e testado **localmente** com *listen server* / PIE 2–4 jogadores. O single-player é só "um servidor com 1 jogador", usando o mesmo código — assim não há retrabalho ao escalar. Custo aceito: aprender replicação cedo (tópico difícil) para evitar um rewrite gigante depois.

---

## 1. High Concept

> Uma cidade brasileira viva onde suas escolhas — o trabalho que você faz, as pessoas com quem se alia, as leis sob as quais você vive — moldam o seu destino. Trabalhe, construa patrimônio, sobreviva e escreva sua própria história numa simulação social, econômica e política inspirada no Rio de Janeiro.

**Gênero:** RP / RPG / Survival / Simulação de Vida (online no longo prazo).
**Fantasia central do jogador:** *"Eu cheguei sem nada e construí uma vida nesta cidade."*

---

## 2. Pilares de Design

Tudo no jogo deve servir a pelo menos um destes pilares. Se uma feature não serve a nenhum, ela é candidata a corte.

1. **Cada escolha tem peso.** Trabalho, dinheiro, alianças e leis afetam a vida do personagem de forma visível.
2. **A cidade parece viva.** NPCs com rotina, comércio funcionando, dia e noite, ruas que não parecem vazias.
3. **Progressão honesta.** O jogador sobe na vida pelo esforço dentro do jogo, não por sorte ou por pagar.
4. **Acessível no celular real do brasileiro.** Roda decentemente num aparelho de entrada; performance é requisito de design, não detalhe técnico.

---

## 3. Plataforma e Público

| Item | Definição |
|------|-----------|
| Plataforma inicial | Android (🟢 MVP) |
| Plataformas futuras | iOS, PC (🔴 Visão) |
| Hardware-alvo de referência | Xiaomi Redmi 13; mínimo 4 GB RAM |
| Público | Jogadores BR de RP/simulação mobile, fãs de servidores estilo cidade |
| Classificação etária alvo | **16+** (armas/violência de RP). ⚠️ PvP com loot + tiro realista pode empurrar para 17+/Adulto na IARC/Google Play — confirmar quando o combate for definido. |
| Sessão típica | 15–40 min, várias vezes ao dia |

**Orçamento de performance (meta):** 30 FPS estável no perfil Muito Baixo em aparelho de 4 GB. Isso *limita ativamente* o design: poucos NPCs visíveis por vez, área de jogo pequena, sem Nanite/Lumen pesados.

---

## 4. Game Loops (o coração do GDD)

### Core loop (segundo a segundo) — 🟢 MVP
> Receber um objetivo → ir até o local → executar a atividade (trabalho/tarefa) → ganhar dinheiro/XP → gastar com necessidades e melhorias → repetir.

### Loop de sessão (uma sessão de 20 min) — 🟢 MVP
> Entrar → checar necessidades (fome/sede/energia) → fazer um turno de uma profissão → comprar comida/itens → evoluir um pouco a habilidade → sair com um pequeno progresso.

### Loop de progressão (semanas) — 🟡 V1
> Subir de nível na profissão → desbloquear empregos/itens melhores → acumular patrimônio → comprar casa/veículo → ganhar reputação social.

### Loop social/econômico/político (longo prazo) — 🔴 Visão
> Jogadores formam economia → comércio entre players → polícia regula → eleições e impostos → o estado redistribui. Só faz sentido com multiplayer e muitos jogadores.

---

## 5. Jornada do Jogador (onboarding)

1. Seleciona servidor → **cutscene inicial** apresenta a cidade e a mensagem de boas-vindas. (🟡 — no MVP pode ser uma tela simples, não uma cutscene cara.)
2. **Criação de personagem.** (🟢 MVP, versão enxuta — ver §6.1)
3. Desembarca no **Aeroporto Internacional** = ponto de entrada e área de tutorial. (🟢 MVP)
4. **Tutorial guiado:** mover, interagir, pegar um primeiro emprego, comer, ganhar o primeiro dinheiro. (🟢 MVP)

O tutorial é o seu *vertical slice*: se o aeroporto + um trabalho + uma refeição funcionarem bem, você provou o jogo.

**✅ Transporte do aeroporto (decidido):** novato spawna no aeroporto. Player com carteira **D** (ônibus) ou **B** (táxi/Uber) leva o novato ao trabalho e ganha a corrida; quando **não há nenhum player de transporte online**, um **NPC entra como plano B** para o novato nunca ficar travado. (🔴 multiplayer.) ⚠️ Como a cidade é fictícia, dar um **nome fictício ao aeroporto** (evitar "Galeão" real).

**✅ Sinalizadores de rota:** waypoints/marcadores guiam o jogador até o local de trabalho — UX essencial em mobile. Versão leve já no 🟢 MVP (guiar o limpador de vidro até o prédio).

---

## 6. Sistemas de Gameplay

### 6.1 Personagem e customização
- Sexos masculino e feminino disponíveis no lançamento.
- 🟢 **MVP:** 1 corpo por sexo, escolha de cor de pele, 2–3 rostos e 2–3 cabelos, 1 roupa civil inicial. *Esqueça troca de sexo, dezenas de rostos e uniformes por enquanto.*
- 🟡 **V1:** sistema de troca de sexo (preservando inventário e progressão), mais opções estéticas.
- ✅ **Decisão tomada:** os pacotes militares/modulares (*Female Modular Military Bundle*, *Quantum Modular Character*) **não vão para civis** — eles serão usados como **uniformes por patente da PMERJ/BOPE**. Civis usam roupa comum. Vantagem: o visual detalhado/pesado fica restrito a um grupo pequeno de personagens (policiais), não à cidade inteira, o que ajuda a performance.

- ✅ **Identidade do personagem:** na criação, o jogador preenche manualmente **nome, idade e cidade/estado de origem no Brasil**. Essa identidade aparece em documentos do jogo (ex.: carteira de motorista, com foto inicial padrão para todos).
- ✅ **Carteira de motorista:** obtida **dentro do jogo** (não vendida) — habilita dirigir / virar Táxi, Uber, Entregador. Tiers pagos podem só adiantar a emissão (ver §12).

### 6.2 Equipamentos (slots independentes)
Slots: camisa, jaqueta, colete, calça, sapato, luvas, capacete, óculos, máscara, mochila, coldre.
- 🟢 **MVP:** 3–4 slots funcionais (camisa, calça, sapato, mochila). Cada item só muda aparência + um atributo simples (ex.: mochila aumenta espaço).
- 🟡 **V1:** demais slots, compatibilidade de roupas por sexo.

### 6.3 Sistema de coldre
- ✅ **Decisão tomada:** o coldre **será funcional** — arma encaixa via *socket* no osso da perna, com animação; futuro com fuzil nas costas, rádio, algemas.
- **Sequenciamento:** desenhar agora, **construir só na fase da polícia** (🔴), depois que mover/interagir/primeira profissão funcionarem.
- ⚠️ **Requisitos técnicos:** (1) armas afetam a classificação etária na Google Play; (2) como o jogo é multiplayer-aware, o estado da arma (equipada/sacada/munição) **precisa ter autoridade no servidor** para evitar cheat; (3) é skill intermediária de Unreal (sockets + attach de actor ao esqueleto + animação).

### 6.4 Inventário
- Base: *Advanced Grid Inventory System* (avaliar código/performance/escalabilidade; ter um sistema próprio como plano B).
- 🟢 **MVP:** inventário simples por slots, com peso/espaço. Não precisa ser grid bonito ainda.

### 6.5 Survival (necessidades)
Necessidades: **fome, sede, energia, saúde.**
- 🟢 **MVP:** fome e sede caindo com o tempo; comer/beber repõe; chegar a zero causa dano à saúde. Energia e descanso entram depois.
- Design: as necessidades existem para *dar motivo ao loop econômico* (você trabalha para comer). Não devem ser punitivas a ponto de irritar.

### 6.6 RPG / progressão
- Eixos: nível, experiência, habilidades, reputação.
- 🟢 **MVP:** 1 habilidade ligada à profissão do MVP (ex.: "Entregas") que sobe com o uso e desbloqueia ganhos melhores.
- 🟡 **V1:** múltiplas habilidades, reputação social.

### 6.7 Profissões
A visão tem ~20 profissões em 5 categorias (Segurança, Saúde, Transporte, Serviços, Produção, Comércio).
- ✅ **Decisão tomada — MVP:** profissão grátis inicial = **Limpador de vidro de prédio**. É a profissão de quem não compra nenhum pacote ("nasce" nela). Loop: subir/posicionar → limpar painéis de vidro → receber. Ensina navegação vertical, interação e economia.
- 🟡 **V1:** mais profissões civis (entregador, frentista, mecânico, dono de mercado).
- 🔴 **Visão:** polícia (PMERJ/BOPE), saúde (médico/SAMU), produção (fazendeiro/pescador/minerador), todo o resto.

| Profissão | Fase | Acesso (modelo por seleção/progressão) |
|-----------|------|--------|
| Limpador de vidro (grátis) | 🟢 MVP | Profissão inicial de qualquer jogador novo |
| Táxi | 🔴 Visão | Carteira tirada no jogo · pontos fixos (1º = aeroporto); leva novatos ao trampo |
| Uber / Entregador | 🔴 Visão | Carteira tirada no jogo · roda a cidade toda; carro alugado (aluguel todo domingo) |
| Frentista, Mecânico, Mercado | 🟡 V1 | Por progressão no jogo |
| PMERJ soldado | 🔴 Visão | **Seleção/entrevista (aberto a todos, pagante ou não)** |
| Comandante PMERJ / organiza BOPE | 🔴 Visão | **Seleção/entrevista no Discord** (cargo, não compra) |
| Médico / SAMU | 🔴 Visão | Seleção (a confirmar) |

> ✅ **Decisão tomada:** cargos de **autoridade** (polícia, comandante) entram por **seleção/confiança**, não por pagamento. Empregos civis são alcançáveis **jogando** (carteira, reputação, nível). O dinheiro só vende cosmético, conveniência e largada — nunca poder. Ver §12.

> 🔴 **Escassez de emprego (multiplayer, futuro):** empregos desejáveis não podem ser ilimitados, ou a economia satura (todo mundo taxista = ninguém com passageiro). Dois modelos: **(a) vagas fixas/slots** — simples de programar, mas exige fila/turno para não travar os primeiros para sempre; **(b) salário dinâmico** — qualquer um entra, mas o pagamento cai quanto mais gente faz aquele trabalho (auto-equilíbrio). Recomendação: longo prazo, salário dinâmico (mais justo); curto prazo, slots são mais fáceis. *Sem impacto no MVP (jogo solo).*

> ✅ **Carteira de motorista e escada de transporte (decidido):** ninguém começa dirigindo. O jogador trabalha primeiro como **limpador de vidro** ou **entregador de bicicleta** (sem carteira) para juntar dinheiro e **comprar a carteira no jogo**. Categorias com custo crescente: **A** (moto) → **B** (carro: táxi/Uber) → **C** (caminhão: carga/porto) → **D** (ônibus: transporte de passageiros, leva quem chega do aeroporto). Tiers pagos só **adiantam** (Bronze: trabalha menos; Prata: já vem com A/B/C/D). Tudo isso é 🔴/🟡 futuro — não entra no MVP.

### 6.8 Economia
- 🟢 **MVP:** economia fechada single-player — você ganha de NPCs/tarefas e gasta em lojas NPC. Preços fixos.
- 🔴 **Visão:** economia entre jogadores, comércio, empresas de player, tesouro estadual alimentado por impostos.
- 🔴 **Economia do Estado (sonho de longo prazo — "deixar vivo"):** o caixa do governo começa com **R$ 2 bilhões**. O governador decide os gastos; esse dinheiro abastece **polícia** (arsenal: armas, munição, coletes, capacetes) e **hospital** (insumos). O arsenal não é infinito — começa com uma quantidade definida e é **reabastecido por produção ou pelo transporte de cargas que chegam pelo porto**, conforme o governador libera verba. Depende inteiramente do backend persistente.

### 6.9 Política e governo
- 🔴 **Visão (depende de multiplayer + comunidade):** governador eleito por voto (1 cidadão = 1 voto), campanhas (comícios, debates, propostas), impostos definidos pelo governador (armas, veículos, imóveis, alimentos, serviços) que abastecem o Tesouro Estadual gastando em segurança/saúde/infraestrutura.
- *Totalmente fora do MVP.* Só existe quando há gente de verdade jogando junto.

### 6.10 Polícia — PMERJ / BOPE
- 🔴 **Visão:** patrulhamento, blitz, prisões, fiscalização; BOPE em operações de alto risco.
- **Hierarquia / acesso (por seleção, não por pagamento):**
  - **Recruta → Soldado → ... → Comandante:** entrada e promoção por **seleção/confiança** (entrevista no Discord, avaliação interna), aberto a qualquer jogador.
  - Isso **resolve o "cold start"** (não depende mais de existir um Gold pago na cidade) e tira o peso de pay-to-win.
  - **Comandante** organiza o BOPE e gerencia o arsenal.
- **Arsenal:** gerenciado pelo comandante; estoque finito de armas/munição/coletes/capacetes, reabastecido via porto/produção e verba do governo (ver §6.8).
- ⚠️ Use **nomes fictícios inspirados**, não "PMERJ/BOPE" reais — risco de marca e de política da loja.
- ⚠️ Trancar autoridade por pagamento (Gold/Diamante) = risco de pay-to-win (ver §12).

### 6.11 Médico — Hospital / SAMU
- 🔴 **Visão:** atendimento, internação, emergência, resgate pré-hospitalar. Verba vinda do caixa do governo (§6.8).
- **Sistema de resgate (conforme definido):** jogador "no chão" (ferido) tem **120 segundos** até ser atendido por um médico. Botão de **chamar socorro**; um NPC ("carioca") que vê alguém caído dispara uma **notificação no celular do médico** informando o ocorrido. Depende do estado "abatido" e de multiplayer.

### 6.12 Porto / logística
- 🔴 **Visão:** recebimento de cargas, contratos de transporte, entregas pela cidade. (Pode virar uma versão grande do loop de Entregador.)

### 6.13 Combate, Prisão e Morte
- 🔴 **Visão — combate:** estilo "nível PUBG" (mobilidade alta, tiro). ⚠️ É **o sistema mais difícil do projeto**: netcode com autoridade de servidor + anti-cheat em mobile. Último grande sistema a construir. Ter bibliotecas ajuda, mas não reduz a complexidade de rede.
- 🔴 **Prisão:** jogador **algemado que sair do jogo continua preso por 5 horas** (exige persistência no servidor — guardar o estado mesmo offline).
- 🔴 **Morte / abatido:** ao morrer, o jogador pode ser **lootado** — qualquer player pode tomar tudo dele. Fica **120 segundos no chão** até ser atendido por médico (ver §6.11) antes de "morrer" de fato.
- ⚠️ Tudo aqui depende do backend persistente + muitos jogadores; nada entra antes disso. Também empurra a classificação etária (ver §3).

---

## 7. Mundo e Mapa

- Inspiração: Rio de Janeiro (estética, não cópia literal).
- Visão completa: aeroporto, centro, hospital, polícia, delegacia, porto, favela, posto, concessionária, área residencial.
- Meta de interiores: 60–70% dos prédios acessíveis. ⚠️ **Isso é caríssimo em mobile** — cada interior é geometria + carregamento. Reduza muito no início.
- 🟢 **MVP: um único bairro pequeno** — aeroporto + algumas ruas + 2–3 prédios com interior (uma loja, uma casa). Nada mais.
- Técnica recomendada: *World Partition* / streaming para nunca carregar a cidade inteira de uma vez.

---

## 8. NPCs e IA

- Visão: IA baseada em *NWIRO AI Pro*; NPCs com rotina diária, trabalho, locomoção e interações.
- ✅ **Nomenclatura:** os NPCs são chamados de **"cariocas"**. Um carioca que vê um jogador caído pode disparar a notificação de socorro ao médico (ver §6.11).
- 🟢 **MVP:** pouquíssimos NPCs, com comportamento simples (andar em rota, vender, dar tarefa). IA "viva" de verdade é cara em CPU mobile — limite drasticamente o número visível por vez.

---

## 9. Multiplayer e Social

- 🔴 **Visão:** mundo persistente online, muitos jogadores, economia e política compartilhadas.
- ✅ **Abordagem decidida (ver §0):** código com **arquitetura de rede da Unreal desde o dia 1** (replicação, autoridade do servidor, RPCs), mas **infraestrutura online adiada**. Desenvolvimento e testes 100% locais (*listen server* / PIE 2–4 jogadores), sem servidores na nuvem, contas ou banco persistente no MVP.
- A parte **mais cara e difícil** continua sendo a infraestrutura online (servidores dedicados, persistência, anti-cheat, custo mensal) — atacada só depois do MVP local estar sólido.

---

## 10. UI/UX

- 🟢 **MVP:** controles touch (joystick virtual + botões), HUD com necessidades, dinheiro e objetivo atual, menu de inventário simples, telas de criação de personagem e de loja.
- Princípio: tudo precisa funcionar com o **polegar**, em tela pequena, sem travar.

---

## 11. Áudio

- 🟢 **MVP:** música ambiente da cidade, sons de passos, interface, e feedback de ações (ganhar dinheiro, comer). Áudio simples já eleva muito a sensação de "vivo".

---

## 12. Monetização

✅ **Modelo escolhido — pacotes de apoio por tier:** **Grátis · Bronze · Prata · Gold · Diamante** (cada tier herda o anterior).

✅ **Princípio firmado:** dinheiro vende **cosmético, conveniência e largada melhor** — **nunca** poder permanente, autoridade, nem vantagem na economia compartilhada. Cargos de autoridade (polícia/comando) entram por **seleção**, não por compra (ver §6.10).

**Proposta de conteúdo dos tiers (a confirmar):**

| Tier | Desbloqueia (sem vender poder) |
|------|-------------|
| **Grátis** | Limpador de vidro e entregador de bicicleta (sem carteira); todos os empregos/cargos alcançáveis jogando ou por seleção |
| **Bronze** | Cosméticos + pequena grana inicial → precisa trabalhar **menos** para tirar a carteira (largada, não atalho de poder) |
| **Prata** | Mais cosméticos + skins de roupa/veículo + já começa com a **carteira A, B, C e D tiradas** (pula o grind); pode dirigir ônibus para levar quem chega do aeroporto |
| **Gold** | Cosméticos premium + reserva de nome + **+slots de inventário** (⚠️ manter diferença pequena e também ganhável jogando) + mochila do pacote + fila/benefício de apoiador |
| **Diamante** | Tudo acima + cosméticos exclusivos + status de apoiador. (A entrevista no Discord é do **cargo de comandante**, não do tier.) |

Outros pontos:
- Servidor online (fase futura) tem custo mensal contínuo; a monetização precisa cobrir isso antes de pensar em lucro.
- ⚠️ Evitar boosts de XP/dinheiro contínuos — são pay-to-win disfarçado numa economia de RP. "Largada melhor" (one-time) é aceitável; "vantagem permanente" não.
- Verificar políticas da Google Play sobre venda de pacotes/itens.

---

## 13. Escopo Faseado (o plano realista)

| Fase | Conteúdo | Objetivo |
|------|----------|----------|
| 🟢 **Protótipo** | 1 personagem move, 1 bairro pequeno, pega 1 tarefa de entrega, recebe dinheiro | Provar que é divertido |
| 🟢 **MVP single-player** | Criação de personagem enxuta, aeroporto+tutorial, 1 profissão completa, fome/sede, inventário simples, 1 loja | Um jogo pequeno e terminável |
| 🟡 **V1 single-player** | 3–4 profissões, mais survival, casa/veículo, progressão de habilidades | Um jogo "de verdade" |
| 🔴 **Online** | Backend, multiplayer, economia entre players, persistência | Projeto novo e grande |
| 🔴 **Visão completa** | Política, polícia, saúde, porto, cidade inteira | O sonho |

**Pra você agora:** mire o Protótipo e o MVP. Eles sozinhos já são meses de aprendizado e trabalho — e são realistas.

---

## 14. Métricas de Sucesso

- 🟢 MVP: o protótipo roda a 30 FPS no Redmi 13? O loop "tarefa → dinheiro → comida" é gostoso de repetir 10 vezes?
- 🟡 V1: jogadores de teste voltam no dia seguinte?
- 🔴 Online: jogadores criam laços e economia espontânea?

---

## 15. Riscos de Design e Questões em Aberto

**Riscos:**
- Escopo grande demais (o maior risco — mitigado pelo faseamento acima).
- Performance em mobile (Nanite/Lumen não rodam em aparelho fraco; pacotes do marketplace podem ser pesados).
- Uso de marcas/instituições reais (Uber, PMERJ, BOPE, SAMU, AK-47) — risco legal e de política da Google Play. Usar nomes fictícios.
- Licença comercial de cada pacote do marketplace precisa ser verificada.
- Custo e complexidade do online.

**Decisões resolvidas (v0.2):**
1. ✅ Arquitetura multiplayer desde o dia 1, infraestrutura online adiada (testes locais).
2. ✅ Profissão grátis do MVP = Limpador de vidro de prédio.
3. ✅ Cidade fictícia inspirada no Rio (decidida por limitação de mobile).
4. ✅ Pacotes militares = uniformes por patente da polícia, não civis.
5. ✅ Coldre será funcional (construído na fase da polícia, com autoridade no servidor).
6. ✅ Tiers Grátis/Bronze/Prata/Gold/Diamante e o que cada um desbloqueia (§12).
7. ✅ Hierarquia da polícia: recruta (Bronze/Prata + Gold ativo), soldado (Gold), comandante (Diamante + Discord).
8. ✅ Combate "nível PUBG"; prisão 5h (mesmo offline); morte com loot + 120s no chão e socorro médico.
9. ✅ NPCs = "cariocas". Classificação alvo = 16+. Caixa do governo inicial = R$ 2 bi.

10. ✅ **Autoridade (polícia/comando) por seleção, não por pagamento.** Tiers vendem cosmético/conveniência/largada, nunca poder. Empregos civis alcançáveis jogando.

**Questões ainda em aberto (a decidir):**
1. Confirmar a proposta de conteúdo de cada tier do §12 (cosmético/conveniência) — está bom ou ajusta?
2. Médico/SAMU: critério de seleção para virar médico.
3. Confirmar classificação 16+ vs 17+/Adulto quando o combate/loot for definido (IARC/Google Play).
4. Como funciona o processo de seleção da polícia na prática (formulário? avaliação in-game? quem aprova?).

---

*Este GDD é um documento vivo. Cada decisão que você tomar deve ser registrada aqui. O próximo passo natural é o TDD (arquitetura técnica) e um roadmap realista para o MVP.*
