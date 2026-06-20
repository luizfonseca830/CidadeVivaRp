# Cidade Viva RP — Marco A (mastigado): boneco andando no seu celular

**Objetivo do marco:** sair do zero e ter um personagem andando **no seu Redmi 13**, com controle por toque.
**Tempo realista:** de alguns dias a 1–2 semanas (a parte do Android pode dar trabalho na primeira vez — é normal).
**O que você vai precisar:** um PC com Windows (idealmente 16 GB+ de RAM, SSD, ~100 GB livres), o seu celular Android, e um cabo USB.

> Faça na ordem. Não pule o passo A2 para "depois" — descobrir tarde que não builda no celular é o pior cenário. Melhor sofrer com o Android logo no começo, com um projeto vazio, do que com meses de trabalho dentro.

---

## A1 — Criar o projeto e ver o boneco andar (no PC)

1. **Instale o Epic Games Launcher** (site da Epic Games) e crie uma conta gratuita.
2. No Launcher, aba **Unreal Engine → Library**, clique no **+** e instale a **Unreal Engine 5.8**. (É um download grande, vários GB.)
3. Abra a 5.8. Na janela de novo projeto:
   - Categoria **Games**.
   - Template **Third Person** (terceira pessoa).
   - Em **Project Defaults**, escolha **Blueprint** (não C++).
   - **Target Platform: Mobile** e **Quality Preset: Scalable** (isso já configura o projeto pensando em celular fraco).
   - Dê o nome `CidadeVivaRP` e escolha uma pasta.
4. Clique **Create**. Quando abrir, aperte **Play** (botão no topo). Você controla o boneco padrão com **WASD + mouse**.

✅ **Pronto quando:** o boneco anda e pula no editor ao apertar Play.

> Antes de seguir: feche o editor, instale **Git** + **Git LFS**, e faça o primeiro commit do projeto. A partir daqui, commit a cada passo que funcionar. (Projetos Unreal têm arquivos binários grandes — sem o Git LFS o repositório quebra.)

---

## A2 — Rodar no celular (a parte chata, faça com calma)

### Passo 1 — Instalar o toolchain do Android (Turnkey)
A UE 5.8 automatiza isso. Dentro do Unreal Editor, na barra de menu:

> **Platforms → SDK Management → Android → Install SDK**

Siga os avisos na tela. O Turnkey vai **instalar o Android Studio (se não tiver), baixar as command-line tools e instalar as versões de SDK, NDK e JDK certas para a 5.8**, e configurar as variáveis de ambiente (ANDROID_HOME, JAVA_HOME). **Reinicie o PC** ao final.

> ⚠️ Atenção a um detalhe clássico: durante a instalação, é preciso deixar o Android Studio baixar as **Android SDK Command-Line Tools**. Se pular isso, o Turnkey falha em baixar o NDK. Não pule.

### Passo 2 — Configurar o projeto para Android
No editor: **Edit → Project Settings → Platforms → Android**.
- Em **APK Packaging**, clique em **Configure Now** (se aparecer) e aceite a licença do SDK.
- **Android Package Name:** algo como `com.SeuNome.CidadeVivaRP`.
- **Minimum SDK Version:** `26` · **Target SDK Version:** `35` (são os valores de exemplo da documentação atual; para publicar na Google Play depois, o alvo precisa ser no mínimo a API exigida pela loja).
- Marque **Support arm64** (é o que os celulares atuais usam).

### Passo 3 — Preparar o celular
No Redmi 13:
- Vá em **Configurações → Sobre o telefone** e toque várias vezes em **Versão MIUI/HyperOS** até liberar as **Opções de desenvolvedor**.
- Nas Opções de desenvolvedor, ative **Depuração USB**.
- Conecte no PC pelo cabo e **autorize** a depuração quando o celular perguntar.

### Passo 4 — Lançar no celular
No editor, ao lado do botão Play há uma setinha de **Platforms**. Seu celular deve aparecer listado. Clique nele para fazer **Launch** (a Unreal compila e instala o jogo no aparelho).

✅ **Pronto quando:** o boneco anda **na tela do seu celular**.

> Requisitos do aparelho (o Redmi 13 atende): Android 8+, CPU 64-bit ARM, e suporte a Vulkan 1.1 ou OpenGL ES 3.2. Se travar, o primeiro lugar pra olhar é o **Output Log** da Unreal — o erro quase sempre está escrito lá.

---

## A3 — Controle por toque

O boneco anda no celular, mas ainda sem botões na tela. Vamos pôr controle por toque.

**Caminho rápido (primeira versão, joystick virtual):**
1. **Edit → Project Settings → Engine → Input**.
2. No campo **Default Touch Interface**, selecione a interface pronta **DefaultVirtualJoysticks**.
3. Faça Launch de novo no celular: aparecem dois "analógicos" na tela (mover / olhar).

> Observação honesta: o template Third Person usa o **Enhanced Input** (ações IA_Move / IA_Look). O joystick virtual pronto às vezes não casa 100% com ele. Se o movimento não responder direitinho, a versão "de verdade" é criar os botões de toque com **UMG** (a UI da Unreal) e ligá-los às ações do Enhanced Input. Para um primeiro contato, o joystick pronto já serve pra você sentir o jogo na mão; o refino você faz depois, seguindo um tutorial atual de **touch input / Enhanced Input** na documentação oficial.

✅ **Pronto quando:** você move o personagem no celular **só com o dedo**, sem teclado.

---

## Checklist do Marco A (commit cada um no Git)

- [ ] A1 — boneco anda no editor (PC)
- [ ] Git + Git LFS configurados, primeiro commit feito
- [ ] A2.1 — Turnkey instalou o toolchain Android, PC reiniciado
- [ ] A2.2 — Project Settings de Android preenchidos
- [ ] A2.3 — celular com depuração USB, autorizado
- [ ] A2.4 — boneco anda no celular
- [ ] A3 — controle por toque funcionando

Quando todos estiverem marcados, **o Marco A está fechado** e você prova pra si mesmo a coisa mais importante: o jogo *roda no seu celular-alvo*. A partir daí, o Marco B (montar um quarteirão pequeno) é puro level design — bem mais tranquilo que isso.

---

## Se travar (problemas comuns do Android)

- **"SDK not found" / NDK errado:** rode o Turnkey de novo (Platforms → SDK Management → Android → Install SDK) e confirme que as Command-Line Tools foram instaladas. Reinicie o PC.
- **Celular não aparece na lista de Launch:** depuração USB ativada? Autorizou no popup do celular? Tente outro cabo/porta USB.
- **Compila no PC mas crasha no celular:** quase sempre é gráfico pesado demais. Confirme que o projeto está no preset **Scalable/Mobile** e baixe a qualidade.
- **Erro na compilação:** abra **Window → Output Log**, role até a primeira linha em vermelho. Copie esse texto e pesquise — quase todo erro de Android de iniciante já foi resolvido por outra pessoa.

> Onde conferir os passos exatos para a 5.8 (os menus podem mudar de uma versão pra outra): a página **Android Quick Start** da documentação oficial da Unreal Engine 5.8.
