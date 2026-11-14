---
tipo: documento
categoria: game-design
projeto: CODEXIA
status: em-andamento
versao: 0.10
idioma: pt-BR
tags:
  - codexia
  - gdd
  - game-design
  - planejamento
---

# CODEXIA — Game Design Document

> Documento vivo de planejamento. Será atualizado gradualmente conforme as decisões do projeto forem aprovadas. Este repositório funciona como ponte de sincronização e organização do Obsidian; não é o repositório principal do desenvolvimento do jogo.

## 1. Visão geral

CODEXIA é um **RPG simulador** 2D em pixel art, com câmera top-down e mundo aberto conectado. A experiência combina a vida de sobrevivência no campo (**pesca, plantio, coleta e caça**) com aventura: **questlines**, exploração, progressão de personagem, combate e descoberta de regiões distintas. O jogo começa numa **vila próxima a uma cachoeira** (ver 5.1 e 6.3). A prioridade atual é definir uma base sólida de projeto antes de implementar sistemas avançados.

## 2. Ficha do projeto

| Campo | Definição atual |
|---|---|
| Nome | CODEXIA |
| Gênero | RPG simulador (vida no campo + aventura) |
| Engine | Unity 6.3 LTS (6000.3.28f1) |
| Renderização | Universal Render Pipeline (URP), com Renderer 2D |
| Dimensão | 2D |
| Câmera | Top-down |
| Direção visual | Pixel art |
| Referência de grade | Células de 32×32 px (referência de organização, não tamanho obrigatório de todo sprite) |
| Linguagem planejada | C# |
| Idioma | Português (PT-BR) |
| Renderização visual | Procedural / híbrida com sprites e texturas |
| Mundo | Aberto e conectado |
| Construção dos mapas | Manual, procedural controlada e híbrida |
| Lore | Definida pelo usuário em 12/11/2026 (ver 5.1) |
| Protagonista | Uma jovem caçadora cujo nome não é revelado; visual original ainda a desenhar |

## 3. Pilares de experiência

- Exploração de um mundo conectado, com regiões e ambientes variados.
- Sensação de movimento e interação fluida.
- Identidade visual consistente entre terrenos, vegetação, construções e objetos.
- Progressão gradual de personagem e descoberta de conteúdo.
- Mapas com composição intencional, sem perder variedade ambiental.

## 4. Direção artística

### 4.1 Referência visual principal

O pacote **Pixel Art Top Down - Basic v1.2.3** foi escolhido como referência visual principal (bíblia visual) para orientar paleta, saturação, contraste, escala, densidade de detalhes, sombras, composição, vegetação, construções e terrenos.

### 4.2 Preservação dos assets

- Não alterar os arquivos originais dos assets.
- Não recolorir, redesenhar ou reestilizar os recursos originais.
- Não substituir assets adequados por formas provisórias.
- Analisar os recursos existentes antes de decidir por novos recursos.
- Qualquer adaptação futura deverá preservar os arquivos-fonte.
- A orientação do projeto é utilizar os assets existentes como base visual, sem gerar arte nova e sem recorrer a placeholders quando houver recurso adequado.

### 4.3 Água

A estética da água de **Top Down Kingdom 2D** foi aprovada como referência complementar. O pacote de água será enviado para inspeção em conversa separada. A solução deverá combinar com o pacote visual principal e priorizar leveza. Uma possibilidade técnica a avaliar é usar texturas reutilizáveis com animação procedural ou shader/material leve. Essa abordagem é uma proposta, não uma decisão técnica final. Licenças deverão ser verificadas antes de incorporar assets de terceiros.

## 5. Protagonista

O protagonista será um personagem original, ainda não desenhado nem implementado.

O personagem demonstrativo incluído no pacote de assets não deverá ser usado como personagem jogável, protagonista ou base direta do protagonista. Seus exemplos podem ser estudados somente como referência geral de movimento: animações nas quatro direções, idle, transições, ritmo e proporções dos quadros.

### 5.1 Lore (definida pelo usuário em 12/11/2026)

**✅ Decisão aprovada.** Texto do usuário, organizado:

- **Infância:** a protagonista, uma menina cujo nome não é revelado, perdeu os pais numa **enchente** quando ainda era criança.
- **Tio Hank:** ela foi criada pelo tio, um homem reservado e sem muitos amigos na vizinhança. Todo dia ele vai à floresta caçar alimentos. Com ele, ela aprendeu a sobreviver: **plantar, colher, caçar e se defender**.
- **O incidente:** num dia como qualquer outro, ao saírem para caçar, ela se perde de Hank. Na neblina, vê um vulto e, como boa caçadora, segue-o furtivamente até uma **clareira com um rio**. Quando está prestes a atirar no "animal", **um elfo negro** a ataca; ela tenta fugir, mas aquilo parece uma emboscada feita por ele.
- **O despertar:** quando o elfo chega perto para atacá-la, **uma energia emana do corpo amedrontado dela** e faz o elfo fugir, pensativo, como se soubesse de algo.
- **Depois:** ela acorda em casa, com o tio preparando a carne e os legumes que havia pego mais cedo.
- **Início do jogo:** **sete anos depois**, o jogo começa com ela em frente à **ponte sobre a cachoeira**, que fica acima da **vila**, logo à frente.

**Personagens citados:** a protagonista (sem nome), Tio Hank e o elfo negro.

**✅ Aprovado pelo usuário (12/11/2026), para trabalhar bem mais à frente:**
- A energia dela é a origem dos poderes e elementos previstos na seção 7.
- O mistério do elfo negro é a questline principal.

**💡 Ainda em aberto:** o incidente da infância como prólogo jogável.

**Início do jogo (atualizado em 12/11/2026):** o jogo começa com ela **em frente à casa** (do Tio Hank), na Vila da Cachoeira. Ao passar pela **ponte da cachoeira**, o jogo carrega a próxima área.

## 6. Mundo e Level Design

O mundo será aberto e conectado, composto por regiões e áreas especiais, como biomas, cavernas e dungeons. A lista definitiva de regiões, a lore e a organização narrativa ainda serão definidas.

### 6.1 Abordagem de construção

- **Manual:** cidades, vilas, construções importantes, locais narrativos, áreas de missão e espaços únicos.
- **Procedural controlada:** distribuição de vegetação, pedras, pequenas decorações, variações de terreno e partes de regiões naturais.
- **Híbrida:** florestas, campos, cavernas e outras áreas naturais que precisam de identidade própria e variedade.

### 6.4 Mundo em zonas, como no Stardew Valley

**✅ Decisão do usuário (12/11/2026):** o mundo é dividido em zonas (áreas). A câmera segue a personagem e para na borda da zona. Ao passar por uma passagem (borda do mapa, ponte e, no futuro, portas), a tela escurece e a próxima zona é carregada. Detalhes no [[CODEXIA - Level Design]] (seção 10).

### 6.2 Regras de coerência

A geração e composição dos mapas deverão respeitar regras para evitar, por exemplo, árvores dentro de construções, objetos bloqueando caminhos indevidamente e elementos incompatíveis com o ambiente.

### 6.3 Ponto de partida: vila junto à cachoeira

**✅ Decisão aprovada (12/11/2026):** o jogo começa numa vila próxima a uma cachoeira, como na imagem de referência enviada pelo usuário (vila de madeira ao pé de um paredão, cachoeira caindo num lago, ponte de pedra em arco sobre o rio, trilhas de terra, cercas, hortas e lampiões). A cena inicial mostra a protagonista **em frente à ponte sobre a cachoeira**, com a vila logo à frente. A referência serve de **inspiração**: a arte segue o pacote e a regra de arte nova (4.2). A proposta de layout está no [[CODEXIA - Level Design]] (seção 10).

## 7. Sistemas de jogo — visão futura

**✅ Confirmado pelo usuário (12/11/2026):** o jogo terá **pesca, plantio, coleta, caça e questlines**, entre outras mecânicas de simulação. Os detalhes de cada uma ainda serão especificados.

Também estão no horizonte, mas ainda não foram especificados nem aprovados para implementação:

- Progressão por níveis e atributos.
- Classes e poderes.
- Missões e requisitos para obtenção de itens.
- Armas como espada, foice e bastão.
- Poderes ou elementos como fogo, água e terra.
- Recompensas condicionadas a missões, níveis ou atributos.

A prioridade imediata é planejar e construir a base do jogo. Esses sistemas serão detalhados em etapas posteriores.

## 8. Diretrizes técnicas iniciais

- Engine aprovada: **Unity 6.3**.
- Pipeline aprovado: **Universal Render Pipeline (URP) com Renderer 2D**.
- C# é a linguagem planejada; versão e configuração ainda precisam ser definidas pelo editor/instalação.
- A solução visual deverá combinar recursos em sprites/texturas com elementos procedurais quando fizer sentido.
- Desempenho e fluidez são requisitos importantes.
- A arquitetura técnica será definida antes da implementação.
- O pacote visual principal possui recursos e correções direcionados a URP 2D; a configuração real deverá ser validada no projeto Unity.

## 9. Ordem de desenvolvimento proposta

1. Consolidar conceito e GDD.
2. Catalogar e validar os assets e suas características técnicas.
3. Definir o manual de identidade visual.
4. Definir grade, escala, câmera, renderização e composição.
5. Planejar a arquitetura técnica da Unity.
6. Estruturar o mundo e o fluxo dos mapas.
7. Planejar a base de movimentação e interação do protagonista original.
8. Preparar um cenário-base para validação visual e técnica.
9. Testar desempenho e consistência.
10. Evoluir gradualmente os sistemas de RPG.

Esta ordem é um plano de trabalho, não uma declaração de tarefas já executadas. O catálogo inicial e os documentos preliminares relacionados foram criados; a validação dentro do editor Unity ainda está pendente.

## 10. Fora do escopo imediato

- Criar ou implementar o protagonista.
- Escrever scripts próprios ou implementar sistemas de jogo.
- Criar arte nova.
- Definir a lore completa.
- Fechar a lista de biomas e regiões.
- Implementar progressão, classes, poderes, combate ou missões avançadas.

Esses tópicos dependem de planejamento e aprovação explícita.

## 11. Questões em aberto

- ~~Qual será a lore?~~ Definida (5.1). Ainda em aberto: nome do mundo e da vila, tom narrativo, quem são os elfos negros e o que é a energia da protagonista.
- Como será o visual da protagonista (jovem caçadora) e do Tio Hank?
- Quais serão os biomas e a estrutura das regiões?
- Qual será a escala de tiles, sprites, personagens e câmera?
- Qual será a arquitetura para mapas manuais, procedurais e híbridos?
- Como será implementada a água mantendo compatibilidade visual e bom desempenho?
- Quais serão os detalhes dos sistemas de RPG e sua ordem de implementação?

## 12. Estado das decisões

### Confirmado

- RPG 2D top-down em pixel art na Unity 6.3.
- Universal Render Pipeline (URP) com Renderer 2D.
- Mundo aberto e conectado.
- **Formato do mundo: continente contínuo** com regiões conectadas por terra e fronteiras naturais (aprovado em 12/11/2026). Planejamento em três níveis (Mundo → Região → Área) no [[CODEXIA - Level Design]].
- **Arte nova (aprovado em 12/11/2026):** permitida apenas para o que **não existe** no pacote (água, terra, areia, selva, cogumelos, tamanhos de pedra etc.), redesenhada no grid de 32 px a partir das folhas de referência enviadas pelo usuário. Continua proibido recriar, recolorir ou substituir o que o pacote já tem.
- **Gênero e lore (12/11/2026):** RPG simulador com pesca, plantio, coleta, caça e questlines; lore da caçadora criada pelo Tio Hank (5.1); início na vila junto à cachoeira (6.3).
- **Versão da Unity:** em 12/11/2026 o projeto foi aberto por engano na Unity 6.6, que converteu pacotes e configurações. Com aprovação do usuário, tudo voltou para a 6.3 (backup em `CODEXIA Backups\projeto-6.6-antes-de-voltar-6.3\`). Regra: abrir sempre pelo Unity Hub, conferindo **6000.3.28f1**, e recusar a atualização para a 6.6.
- Prioridade atual: **construção do mundo** (distribuição procedural inteligente, mundo conectado e identidade dos ambientes), sem refazer os sistemas de jogo já testados.
- Referência visual principal: Pixel Art Top Down - Basic v1.2.3.
- Protagonista original; personagem demonstrativo do pacote não será usado como protagonista ou personagem jogável.
- Água inspirada visualmente em Top Down Kingdom 2D; pacote será enviado para inspeção.
- Versão instalada da engine: Unity 6.3 LTS **6000.3.28f1**, com Visual Studio Community 2026 como editor de C#. A Unity 6.6 já instalada no computador não é usada no CODEXIA.
- Projeto Unity em `V:\Programação\CODEXIA Unity\CODEXIA`, fora do cofre do Obsidian, criado pelo template Universal 2D (URP com Renderer 2D).
- Sorting Layers e camadas de física do pacote adotadas no CODEXIA (Default, Layer 1, Layer 2, Layer 3), importadas do `TagManager.asset` original.
- Diretrizes de arquitetura e padrões C# registradas em [[Diretrizes de Arquitetura e Padrões C#]] (a divisão Core/Shared/UI continua como proposta, sem implementação).
- Preservação dos assets originais.
- Utilizar os assets existentes como base visual e não substituir assets adequados por placeholders. (A proibição de arte nova foi substituída em 12/11/2026 pela regra de arte nova acima.)
- Planejamento e organização antes de implementação.

### Propostas ainda não definitivas

- Água com textura reutilizável e animação procedural/shader leve.
- Divisão de áreas entre construção manual, procedural controlada e híbrida conforme os critérios descritos.
- Sequência de desenvolvimento apresentada neste documento.
- Configurações internas do URP 2D, câmera, escala, pixels por unidade, sorting e colisões, pendentes de validação.
- Ordenação por profundidade do Renderer 2D em Custom Axis (0, 1, 1), igual à do pacote. Já aplicada para que os assets apareçam como o autor projetou; aguarda confirmação.
- Câmera do executável de teste (ortográfica, tamanho 8,4375, escala 2× em 1080p). Valor provisório, não é a escala definitiva.

## 13. Documentos relacionados

- [[Catálogo de Assets]] — inventário inicial do pacote Pixel Art Top Down - Basic v1.2.3.
- [[Manual de Identidade Visual]] — diretrizes visuais preliminares derivadas dos recursos existentes.
- [[Planejamento da Base Técnica]] — planejamento da base técnica e da cena de validação.
- [[Arquitetura Técnica Inicial]] — organização preliminar do projeto Unity 6.3 com URP 2D.
- [[Relatório de Validação Técnica]] — instalação, importação e dados técnicos verificados no editor.
- [[Diretrizes de Arquitetura e Padrões C#]] — padrões de código.
- [[Arquitetura de Código - Versão Jogável]] — assemblies, classes, eventos e integração da versão jogável (em revisão).
- [[CODEXIA - Level Design]] — estrutura do mundo, regiões, conexões, identidade dos ambientes e regras procedurais (em construção).
- [[Mecânicas da Versão Jogável]] — referências e parâmetros de movimento, vida, inventário, HUD e diálogo (proposta adotada).
- Documento de Level Design — a desenvolver.
- Documento de Sistemas de Jogo — a desenvolver.
- Lore e Universo — a desenvolver.
- Roadmap de Desenvolvimento — a desenvolver.

## 14. Histórico

- **0.1:** consolidação inicial das informações e decisões existentes no planejamento do CODEXIA.
- **0.2:** inclusão das regras reforçadas de uso dos assets e registro dos documentos iniciais de catálogo, identidade visual e planejamento da base técnica.
- **0.3:** aprovação da Unity 6.3 com URP 2D; registro da preparação técnica inicial e envio posterior do pacote complementar de água.
- **0.4:** Unity 6.3 LTS (6000.3.28f1) instalada; projeto CODEXIA criado com o template Universal 2D; pacote principal importado sem ProjectSettings; criação do [[Relatório de Validação Técnica]].
- **0.10 (12/11/2026):** energia como origem dos poderes e elfo negro como questline principal aprovados para mais à frente; o jogo começa em frente à casa; mundo em zonas, como no Stardew (6.4).
- **0.9 (12/11/2026):** gênero RPG simulador (pesca, plantio, coleta, caça, questlines); lore da protagonista e do Tio Hank (5.1); início na vila junto à cachoeira (6.3); incidente da Unity 6.6 e volta para a 6.3.
- **0.8:** regra de arte nova; mapa de teste com temperado + rio + selva (ver Level Design 4.6).
- **0.7:** mundo definido como continente; criação do [[CODEXIA - Level Design]]; foco passa à construção do mundo.
- **0.6:** primeira versão jogável (personagem provisório, vida, inventário, HUD, objetos que falam); patch URP 2D Lit aplicado; executável Teste 02.
- **0.5:** adoção das camadas do pacote; registro das diretrizes de C#; criação da cena de validação "Área Inicial" e do primeiro executável de teste.

## 15. Estado atual do trabalho

### Concluído

- Inspeção somente leitura do arquivo original **Pixel Art Top Down - Basic v1.2.3.zip**.
- Identificação dos grupos de texturas, prefabs, Tile Palettes, animações, scripts, materiais, shaders e cenas demonstrativas.
- Criação do [[Catálogo de Assets]].
- Criação do [[Manual de Identidade Visual]] preliminar.
- Criação do [[Planejamento da Base Técnica]].
- Aprovação da Unity 6.3 com URP 2D.
- Instalação da Unity 6.3 LTS (6000.3.28f1) com Visual Studio Community 2026.
- Criação do projeto CODEXIA (template Universal 2D), com URP e Renderer 2D confirmados nos arquivos do projeto; o projeto abre sem erros.
- Cópia de segurança do ZIP original, conferida por SHA-256, em `V:\Programação\CODEXIA Backups\`.
- Importação do pacote principal sem os 18 arquivos de ProjectSettings do projeto de origem; 0 erros e 1 aviso no Console; arquivos conferidos byte a byte contra o original.
- Inspeção da cena SC Demo (sem salvar) e levantamento de PPU, pivôs, sorting, colisores, câmera e shaders. Detalhes no [[Relatório de Validação Técnica]].
- Importação apenas do `TagManager.asset` do pacote (camadas), conferido byte a byte.
- Cena de validação `Validacao - Area Inicial` montada só com tiles e prefabs originais; Play Mode com 0 erros e 0 avisos.
- Primeiro executável de teste para Windows (`Builds\Teste 01 - Area Inicial\CODEXIA.exe`), com build bem-sucedido.

- Patch URP 2D Lit 6.3 aplicado (materiais, shaders e cenas demo; o URP Asset do pacote não foi importado).
- **Versão jogável** criada com 4 subagentes (referências, assets, scripts, HUD/cena): personagem demo como provisório (exceção aprovada), movimento 8 direções, vida 10 HP com invencibilidade/knockback/flash, inventário de 18 slots (9 de hotbar), HUD em uGUI criada por código, diálogo com estátua, poço (cura), placas e baú; 2 pilares rúnicos que causam dano; 10 coletáveis. Arquitetura Core/Shared/UI com Assembly Definitions.
- Testado no editor (Play Mode): andar, câmera, dano, coleta, inventário e diálogo funcionando, 0 erros. Executável `Builds\Teste 02 - Jogavel\CODEXIA.exe` gerado.

### Próximas atividades

- Revisar a [[Arquitetura de Código - Versão Jogável]] (aprovada para implementar e revisar depois).
- Testes ainda não feitos no editor: abrir o baú, usar o Pote de água, desmaio/respawn, controle (gamepad).
- Definir o protagonista original (substitui o personagem demo provisório).

- Testar o executável no computador do usuário e registrar o resultado.
- Confirmar a ordenação por profundidade (Custom Axis 0, 1, 1).
- Decidir sobre o patch URP 2D Lit para Unity 6.3 incluído no pacote.
- Definir escala de câmera, resolução-alvo e uso de Pixel Perfect Camera.
- Inspecionar a cena SC All Props.
- Retirar o ZIP de assets do cofre (aprovado; precisa ser feito pelo usuário).
- Inspecionar o pacote complementar de água quando enviado. Até agora só foi recebida uma imagem de referência (`TX waterreference.jpg`).
- Planejar o protagonista original e a arquitetura de código (Core/Shared/UI) para aprovação, antes de qualquer script.

O protagonista e os scripts próprios ainda não foram criados. O executável atual é uma vista estática da área inicial (câmera fixa, sem personagem).
