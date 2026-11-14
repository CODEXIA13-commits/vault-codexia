---
tipo: documento
categoria: level-design
projeto: CODEXIA
status: em-construcao
versao: 0.8
idioma: pt-BR
tags:
  - codexia
  - level-design
  - mundo
  - procedural
---

# CODEXIA — Level Design

> Documento complementar ao [[CODEXIA - GDD|GDD]]. Ele não substitui as decisões do GDD. Cada seção marca o que é **✅ Decisão aprovada** e o que é **💡 Proposta** (aguardando aprovação). Relacionados: [[Catálogo de Assets]], [[Mecânicas da Versão Jogável]], [[Arquitetura de Código - Versão Jogável]].

## 0. Regras que este documento não pode quebrar

✅ Decisões aprovadas e já implementadas, que valem para qualquer região:

| Regra | Valor atual |
|---|---|
| Estilo visual | Somente a arte do **Pixel Art Top Down - Basic** (Cainos); nenhuma arte nova; assets originais intocados |
| Escala | PPU 32, filtro Point, grade de 32×32 px |
| Profundidade | Sorting Layers Default/Layer 1/2/3 do pacote + Transparency Sort **Custom Axis (0, 1, 1)** |
| Colisões | Colisores originais dos prefabs (Box, Polygon, Edge); props dinâmicos com Rigidbody2D (gravidade 0) **nunca** começam sobrepostos |
| Distâncias | Distância mínima entre objetos; nada sobre o calçamento; coletáveis longe de perigos; tudo alcançável a pé (validação por flood fill com a caixa dos pés do jogador) |
| Iluminação | URP 2D Lit; a Global Light 2D cobre todas as Sorting Layers |
| Reprodutibilidade | Toda geração usa **semente fixa** e recria exatamente a mesma área |
| Sistemas de jogo | Movimento, vida, inventário, HUD e interação já testados; o Level Design só **posiciona** esses sistemas, não os altera |

## 1. Referências pesquisadas

| Referência | O que aproveitamos |
|---|---|
| *Zelda: A Link to the Past* (análise "Overworld Overload", Game Developer) | Mundo dividido em ~8 regiões principais ligadas por "zonas de conexão"; cada região tem qualidade de labirinto leve, e as áreas mais abertas são exceção, não regra |
| *Ocean's Heart*, *Minish Cap* | Exploração "picando" o overworld entre masmorras; vislumbres de lugares ainda inacessíveis atraem o jogador |
| Estudo *Evaluating Models for Virtual Forestry* (Williams, Ritsos e Headleand) | Florestas com posições aleatórias uniformes não parecem naturais; distribuições por pontos (disk-based) e cobertura de copa afetam realismo e jogabilidade |
| Guillaume Barlier, *Exploring Procedural World Generation* | Poisson disk de raio variável + "Poisson clusters" para árvores; ruído para abrir clareiras; detalhes de chão no **inverso** do ruído das árvores |
| Red Blob Games, *Terrain from Noise* | Mapas de ruído em várias frequências (oitavas) para variar densidade de forma suave |
| Princípios de "weenies" e breadcrumbs (Disney, GMTK, Ghost of Tsushima) | Marcos visíveis orientam sem UI; pequenas "migalhas" ao longo do caminho confirmam a direção |

Referências visuais enviadas pelo usuário (Kynseed, Ocean's Heart, World of Anterra, Little Witch in the Woods, Kloa e outras) servem para **composição e ritmo**, nunca para copiar arte.

## 2. Estrutura do mundo

### 2.1 Formato

✅ **Decisão aprovada (12/11/2026):** o mundo do CODEXIA é um **continente contínuo**, com regiões conectadas por terra e fronteiras naturais (mata densa, desníveis, ruínas e, futuramente, rios). Não é um arquipélago.

### 2.2 Três níveis de planejamento

✅ Aprovado como método:
- **Nível 1 — Mundo:** quais regiões existem, onde ficam e como se conectam.
- **Nível 2 — Região:** caminhos principais, áreas naturais, construções e pontos de interesse.
- **Nível 3 — Área:** composição detalhada (terreno, vegetação, objetos, colisões e decoração).

A conexão lógica entre regiões (grafo) é **independente** da forma de carregar cenas. Uma cena por região ou chunks será decidido depois.

### 2.3 Mapa macro

💡 **Proposta**, não é o mapa oficial:

```
                 [R7 Serras Rochosas]*
                        |  (escadaria / passagem)
 [R2 Bosque Antigo] — [R4 Platô das Ruínas] — [R5 Colina do Repouso]
        |  trilha na mata      | escadaria              |  portão
 [R1 Floresta Inicial] ——— estrada ——— [R3 Campos do Leste]
        |                                        |
   (margem futura)                         (vau futuro)
        └──────────── [R6 Vale dos Rios]* ───────┘

 * aguardando assets (água, rocha/caverna)
```

| ID | Região | Papel | Situação dos assets |
|---|---|---|---|
| R1 | **Floresta Inicial** | Início e tutorial natural; contém a clareira atual (praça, poço, santuário) | ✅ disponível |
| R2 | **Bosque Antigo** | Floresta densa, com clareiras escondidas e ruínas engolidas pela mata | ✅ disponível |
| R3 | **Campos do Leste** | Planície aberta, com bosques pequenos, pedras isoladas e estrada | ✅ disponível |
| R4 | **Platô das Ruínas** | Terreno elevado em vários níveis (paredes e escadas), com pilares rúnicos | ✅ disponível (tileset Wall + Stone Ground + Struct) |
| R5 | **Colina do Repouso** | Cemitério e santuário antigo, de clima silencioso | ✅ disponível (lápides, sarcófagos, altar) |
| R6 | Vale dos Rios | Rios, lagos, margens e pontes | ⏳ depende do pacote de água |
| R7 | Serras Rochosas | Montanhas e entradas de caverna | ⏳ depende de assets de rocha e caverna |

### 2.4 Conexões

💡 Proposta:

| De → Para | Tipo de conexão | Como se vê no jogo |
|---|---|---|
| R1 → R3 | Estrada calçada (já existe) | Calçamento contínuo; placas de direção |
| R1 → R2 | Trilha natural | Corredor sem calçamento, ladeado de mata densa; flores e pedrinhas como migalhas |
| R2 → R4 | Escadaria | `PF Struct - Stairs` subindo o desnível de parede |
| R3 → R4 | Escadaria larga | Escadas S L/M/R montadas lado a lado |
| R4 → R5 | Portão | `PF Struct - Gate 01/02` entre muros |
| R1/R3 → R6 | Margem ou vau | Futuro (água) |
| R4 → R7 | Passagem rochosa | Futuro |

**Regra de ciclo** (💡): o grafo deve ter pelo menos um **ciclo** (por exemplo R1–R2–R4–R3–R1), para o jogador nunca precisar voltar pelo mesmo caminho, como nas regiões conectadas de *A Link to the Past*.

## 3. Identidade dos ambientes

💡 Proposta. Cada região tem **regras próprias**, não só uma quantidade diferente de árvores. Os elementos listados existem no catálogo.

| | R1 Floresta Inicial | R2 Bosque Antigo | R3 Campos do Leste | R4 Platô das Ruínas | R5 Colina do Repouso |
|---|---|---|---|---|---|
| **Sensação** | Acolhedora, segura | Fechada, misteriosa | Ampla, livre | Imponente, antiga | Silenciosa, melancólica |
| **Terreno** | Grama com variações e flores; calçamento nas áreas cuidadas | Grama escura, quase sem calçamento | Grama clara, muitas flores | Stone Ground + muros (Wall) em níveis | Grama + calçamento quebrado (Pavement 1–5) |
| **Árvores** | Bosques médios (3–6 árvores) com clareiras grandes | Agrupamentos grandes e contínuos (8–20); clareiras pequenas e raras | Poucos bosques (2–4) isolados | Quase nenhuma; arbustos nas frestas | Árvores solitárias marcando o horizonte |
| **Arbustos/grama** | Médio | Alto, nas bordas dos agrupamentos | Baixo; tufos de grama altos | Baixo, nas bordas dos muros | Baixo e organizado |
| **Pedras** | Grupos pequenos | Grupos com pedra grande (Stone 07) + satélites | Rochas isoladas | Tijolos e cubos de pedra caídos | Raras |
| **Elementos narrativos** | Poço, bancos, lanternas, placas, depósito | Pilares rúnicos, estátua tomada pela mata, baú escondido | Caixas e barris de viajantes, placas | Pilares, rune pillars, altar, escadas, portões | Lápides, sarcófagos, altar, lanternas |
| **Pontos de interesse** | Praça, santuário | Clareira do pilar rúnico, ruína escondida | Acampamento de estrada | Altar no topo do platô | Santuário principal |
| **Proibido** | Perigos no caminho principal | Calçamento novo e limpo | Floresta densa | Árvores grandes sobre os muros | Props de comércio (caixas, barris) |

## 4. Distribuição procedural inteligente

### 4.1 O que evitamos

Sortear uma posição, colocar um objeto e repetir. Distribuição uniforme não parece natural (Williams et al.) e cria grades invisíveis.

### 4.2 Camadas

💡 Proposta de pipeline. Ela estende o gerador atual, que já tem semente, validação de colisão e alcance e prévia.

| Camada | Entrada | O que faz | Técnica |
|---|---|---|---|
| **0. Intenção** (manual) | Rascunho da região | Caminhos principais, POIs reservados, entradas e saídas, áreas proibidas | Máscaras desenhadas por coordenadas no layout (não é arte) |
| **1. Terreno** | Máscaras | Grama, calçamento, Stone Ground, muros e transições | Regras de vizinhança dos tiles (já usadas no calçamento atual) |
| **2. Vegetação** | Campo de densidade da região | Agrupamentos de árvores, clareiras, arbustos nas bordas | Ruído de baixa frequência → densidade; **Poisson disk de raio variável**; **clusters** (sementes de bosque com filhos ao redor) |
| **3. Elementos ambientais** | Vegetação + terreno | Pedras em grupos, tufos e flores no inverso da densidade de árvores (bordas de clareira) | Poisson disk com máscara invertida; "kits" de pedras (1 grande + 2–4 pequenas) |
| **4. Pontos de interesse** | Reservas da camada 0 | Ruínas, santuários e acampamentos montados como **kits** | Kits pré-compostos com raio livre obrigatório |
| **5. Validação** | Tudo | Colisões, sobreposição de dinâmicos, alcance, distância dos caminhos, raio livre dos POIs, densidade por região | Validadores atuais (`placement.py`, `validate_scene.py`) + novas métricas |

### 4.3 Regras ambientais

💡 Proposta:
1. **Árvores em agrupamentos:** cada bosque nasce de uma semente; os filhos ficam entre 1,2 e 3,5 u do centro, com Poisson disk interno (mínimo 1,1 u entre troncos).
2. **Clareiras naturais:** quando o ruído de clareira passa do limiar da região, a densidade de árvores vai a zero; as bordas recebem arbustos e flores.
3. **Pedras agrupadas:** 70% das pedras nascem em grupos; o restante fica isolado.
4. **Respeito aos caminhos:** faixa livre de 1,0 u de cada lado de qualquer caminho; arbustos podem encostar na faixa, árvores não.
5. **Área livre ao redor de construções e POIs:** raio de 2,5 u (POIs pequenos) a 4 u (santuários).
6. **Densidade por região:** cada região tem a sua tabela de parâmetros (4.4).
7. **Elementos compatíveis:** cada região tem uma lista **permitida** e outra **proibida** de prefabs (seção 3).
8. **Sementes reproduzíveis:** semente da área = hash(semente do mundo, ID da região, ID da camada). Mudar uma camada não embaralha as outras.
9. **Invariantes técnicos da seção 0**, verificados automaticamente.

### 4.4 Parâmetros iniciais por região

💡 Valores para teste, ajustados na Fase 6:

| Parâmetro | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Cobertura de copa alvo | 25% | 55% | 8% | 3% | 10% |
| Árvores por bosque | 3–6 | 8–20 | 2–4 | — | 1 |
| Limiar de clareira (0–1) | 0,55 | 0,75 | 0,30 | — | 0,40 |
| Arbustos por 100 u² | 4 | 9 | 1,5 | 2 | 1 |
| Tufos e flores por 100 u² | 12 | 6 | 18 | 3 | 5 |
| Pedras em grupo | 60% | 80% | 30% | 90% (tijolos) | — |
| Faixa livre dos caminhos | 1,0 u | 0,8 u | 1,2 u | 1,0 u | 1,2 u |

### 4.5 Base ecológica (pesquisa de 12/11/2026)

✅ Aprovado pelo usuário ("ajuste-as e coloque-as no jogo de maneira orgânica e fiel à realidade") e implementado na região experimental. Os números são de teste e podem ser ajustados na Fase 6.

**Escala:** uma árvore do pacote tem ~4 u de altura; então 1 u ≈ 4 m.

| Fenômeno real (fonte) | Regra no CODEXIA |
|---|---|
| **Bioma temperado em camadas:** dossel, sub-bosque, arbustos e chão; sob a copa fechada chega pouca luz (Wild Classroom; Macmillan, *Scientific American Environmental Science*) | Três camadas: árvores (Tree 01–03), arbustos (Bush 01–06) e ervas (Grass 01–15, Flower 01, tiles de flor). A quantidade de ervas cresce com a **luz** calculada a partir da copa |
| **Sob a copa fechada chega menos de 2% da luz** (Nature Documentaries, *Treefall Gap Dynamics*) | Interior do Bosque Antigo tem poucos tufos e quase nenhuma flor; arbustos sob copa fechada caem para 35% |
| **Dispersão de sementes:** a maioria cai perto da árvore-mãe, com cauda longa; o modelo 2Dt descreve bem florestas temperadas (Clark et al. 1999; Nathan & Muller-Landau 2000) | Cada árvore lança sementes com núcleo **2Dt** (escala 2,6 u em R1 e 3,0 u em R2). Isso cria **bosques** naturais, sem grade |
| **A distância de dispersão cresce com a altura da árvore; árvores maiores produzem mais sementes** (Muller-Landau et al. 2008; Norghauer et al. 2011) | Fecundidade por estágio: Tree 01 = 2, Tree 02 = 4, Tree 03 = 6 sementes por geração |
| **Janzen–Connell e Hubbell:** o recrutamento bem embaixo da mãe é menor, mas na maioria das espécies a densidade ainda diminui com a distância (padrão Hubbell; Bagchi et al., BCI 2019) | Recrutamento a menos de 1,8 u da mãe cai para 35%, voltando a 100% em 3,2 u |
| **Competição por espaço e luz, autodesbaste:** a copa ocupa o espaço e as árvores suprimidas morrem (Yoda; Reineke; Pretzsch 2019) | Distância mínima entre troncos de 2,05 u (2,35 u com Tree 03), herdada da Área Inicial. Uma muda não cresce onde a copa local (raio 2,5 u) já passa de 78% |
| **Sucessão secundária:** campo abandonado → arbustos e pioneiras → floresta jovem → floresta madura (Frontiers 2023; Wild Classroom) | Campo de **idade**: 0 junto da praça (área mantida pelas pessoas), sobe com a distância até 0,62 em R1 e chega a 1 no Bosque Antigo. A idade define a cobertura de copa e o tamanho das árvores |
| **Dinâmica de clareiras:** a queda de uma árvore abre luz, e arbustos e pioneiras colonizam rápido (Brokaw & Busing; CDU *gap-phase dynamics*) | Até 3 clareiras de queda na floresta velha (raio 3,0–4,2 u) com **explosão** (×2) de arbustos e ervas. O POI do Bosque fica numa delas |
| **Efeito de borda:** na borda da mata há mais luz, mais arbustos e mais riqueza de espécies, que caem 15–30 m para dentro (Meiners & Pickett 1999; Harper et al. 2004; EIU) | Densidade de arbustos e ervas multiplicada por `borda = 4·L·(1−L)`, que tem pico na meia-luz. Na escala do jogo: 4–8 u |
| **Relevo e umidade:** vales úmidos têm mais árvores; cristas secas, mais arbustos e solo raso (Dyer 2009; Coweeta; Zhang et al. 2018) | Campo de **umidade** (ruído + mais úmido ao norte e ao sul, futuro Vale dos Rios). Abaixo de 0,30 fica um afloramento seco: copa ×0,4 e **kits de pedra** |
| **Ecótono:** a transição natural é gradual, não uma linha (Harper et al. 2004; Ecotones/NSF) | Faixa de 8 tiles com borda ondulada por ruído entre R1 e R2. A leste, a mata abre aos poucos rumo aos Campos (R3) |

**Como a geração funciona (camadas):**
1. **Intenção:** estrada continuada até as bordas; trilha natural da praça ao POI; POI reservado.
2. **Campos:** idade, umidade, clareira, ecótono e cobertura alvo.
3. **Árvores:** fundadoras por Poisson disk (raio 8 u, aceitas conforme a cobertura alvo) e 6 gerações de sementes com competição.
4. **Clareiras de queda** removem árvores velhas.
5. **Luz** recalculada a partir da copa final.
6. **Pedras, arbustos e ervas** seguem luz, borda, umidade e manchas.
7. **Chão:** variações de grama; flores conforme a luz; trilha gasta com grama lisa.
8. **Validação.**

**Verificação de que as árvores ficaram agrupadas:** a variância das contagens em quadrados de 8 u, dividida pela média, foi comparada a um sorteio aleatório com a mesma distância mínima. Valor acima da base indica agrupamento.

| Zona | Resultado | Aleatório | Leitura |
|---|---|---|---|
| R1 oeste | 1,15 | 0,60 | agrupado |
| R1 leste | 1,47 | 0,72 | agrupado |
| R1 sul | 1,36 | 0,57 | agrupado |
| R2 | 0,92 | 0,47 | agrupado, porém mais contínuo (mata fechada) |

### 4.6 Clima, rio, estrada e distribuição matemática (pesquisa de 12/11/2026, v0.4 do gerador)

✅ Aprovado pelo usuário em 12/11/2026 ("Pesquise antes de implementar qualquer coisa relacionada a clima, ecossistema"):
- arte nova é permitida **somente para o que não existe** no pacote, redesenhada no grid de 32 px a partir das folhas de referência;
- próximo mapa de teste: **temperado + rio + selva**;
- **sem** colisores invisíveis de barreira (a mata é só visual);
- a estrada **curva em todo o mapa**, inclusive junto da praça.

| Fenômeno real (fonte) | Regra no gerador v0.4 |
|---|---|
| **Bioma = temperatura × precipitação** (diagrama de Whittaker; acerto de ~70–80%) | Campos de altitude → temperatura (resfriamento com a altitude, ~6,5 °C/km) e de umidade (mais úmido perto do rio). **Selva** só onde está quente **e** úmido: a várzea baixa do rio. Em volta, floresta temperada; ao norte, o Bosque Antigo |
| **Sombra de chuva:** a encosta a barlavento é úmida e a sotavento é seca | 💡 Reservada para o mapa macro (deserto atrás das Serras Rochosas). Ainda não aplicada |
| **Meandros:** o comprimento de onda fica entre 10 e 14 larguras do canal (média 11); o raio da curva ≈ 2,3 larguras; a forma segue uma curva gerada por seno (Langbein & Leopold); sinuosidade de 1,5 ou mais = meandrante | Rio de 3 tiles de largura, onda ≈ 11 larguras, ângulo máximo de 0,95 rad, cada curva com comprimento próprio. Sinuosidade medida: **1,32** |
| **Barra de areia na margem interna:** o sedimento se deposita na curva interna e forma a barra; o barranco fica na externa | Areia na margem interna, com largura proporcional à curvatura. Lama no barranco externo |
| **Corredeira (riffle) entre duas curvas:** trecho raso | **Vau** no trecho reto entre duas curvas: a trilha atravessa o rio a pé |
| **Mata ciliar:** pioneiros (salgueiros) colonizam as barras | Arbustos e plantas pioneiras na beira da água e nas barras; mato alto acompanha a margem |
| **Dispersão de sementes → agrupamentos (processo de Thomas / Neyman-Scott)** | Árvores = **processo de Thomas**: mães sorteadas pela densidade da mata e filhas espalhadas em gaussiana (σ = 2,2 a 2,8 u) |
| **Arbustos de deserto:** os pequenos ficam agrupados, os médios aleatórios e os grandes regulares (competição) | Distância mínima por tamanho: pequena 2,05 u, média 2,3, grande 2,7, emergente 3,2. As dominantes são aceitas primeiro (Matérn III). Um raio variável (+0 a 28%) quebra o empacotamento em grade |
| **Anel de fada "preso":** fungos micorrízicos (como o *Amanita*) frutificam ao redor da árvore parceira; o anel livre fica em campo aberto, sem árvore | 10% das árvores adultas em mata fechada ganham um arco de 3 a 6 cogumelos a 0,6–1,2 u do tronco. Um anel livre (11 cogumelos, raio 1,6 u) fica num prado |
| **Fungos de madeira morta (saprotróficos)** | Árvores de mata fechada às vezes caem (2,5%) e viram **tronco caído** com cogumelos pequenos ao lado |
| **Trilhas alargam onde há lama:** as pessoas desviam das poças e o caminho se espalha (*trail braiding*) | Estrada e trilhas ficam até +1,2 u mais largas onde a umidade é alta |
| **Blocos de pedra seguem lei de potência:** muitas pequenas, poucas grandes; as grandes rolam mais longe | Focos de pedra em clareiras e lugares altos: uma pedra grande + 3 a 8 menores, com tamanhos de Pareto (expoente 2,4), espalhadas morro abaixo |

**Estrada:**
- O traçado é uma curva suave cuja amplitude cresce longe da praça.
- Entre 300 traçados sorteados, fica o que atravessa menos mata densa e menos ladeira.
- Perto da praça o calçamento quase não tem desgaste.
- Rumo às bordas do mapa ele vira estrada de terra com lajes soltas, usando só tiles originais do pacote (calçamento inteiro e as bordas quebradas _37–_39 e _45–_47).
- Padrões de quadrante que o pacote não tem são simplificados.

**Arte nova (v0.1, pasta `Ferramentas\arte-nova-v0.1.zip`):**
- **Terrenos em camadas sobre a grama:** terra, areia, lama, chão de selva, água e vau. Cada um tem 16 tiles de canto com borda irregular contínua e 3 variações de miolo.
- **Props:** 4 árvores da selva, 2 troncos caídos, galho, 3 cogumelos, pedras com musgo, pedras e seixos em 3 tamanhos, 32 plantas e samambaias da selva.
- **Água:** desenhada à mão em azul acinzentado para combinar com o verde-oliva do pacote. O tratamento final da água continua pendente no Manual de Identidade Visual.

**Resultado do gerador v0.4 (120 × 104 tiles):**

| Item | Valor |
|---|---|
| Árvores novas | 374 (temperado, bosque e selva) |
| Objetos novos | 1.408 |
| Cogumelos | ~140 |
| Troncos caídos | 7 |
| Pedras | ~195, em 43 focos |
| Agrupamento | Variância/média de 1,7–1,9 contra 0,75 do sorteio aleatório: **agrupado** |

**Validação:** 0 objetos na água ou no calçamento; 100% da área livre alcançável; a margem sul é alcançável pelo vau; a estrada e as trilhas estão contínuas; todas as interações são alcançáveis.

**Pendente:**
- ⏳ Importar a arte e as camadas novas na Unity (tiles, prefabs, colisor da água) e testar em Play Mode.
- ⏳ Portar o gerador para C# (ferramenta de Editor), conforme a arquitetura proposta.

### 4.7 Biomas separados e lógica das árvores (v0.5, 12/11/2026)

**✅ Decisão aprovada pelo usuário:** cada bioma tem sua própria biodiversidade e seu ecossistema separado. Nada de um bioma nasce em outro (na selva não nasce cacto; no deserto não nasce árvore de selva).

**Como o gerador garante isso:**
- Cada célula do mapa recebe um bioma pelo clima (calor × umidade). Nas faixas de fronteira o bioma fica "indefinido" e **nada nasce ali**; assim nenhuma espécie vaza para o vizinho.
- Cada bioma tem uma **paleta exclusiva** (árvores, arbustos, ervas, madeira morta, pedras e chão). O validador conta violações de paleta: **0** na versão atual.

| Bioma | Árvores | Arbustos e ervas | Madeira morta | Pedras | Chão |
|---|---|---|---|---|---|
| Temperado | Tree 01 (pequena), 02 (média), 03 (grande, rara) | Bush 01–03, grama alta, flores, cogumelos 01/03, anel de fadas | Galhos (pouca) | Pedras Cainos, Boulder 03, Rock 05, seixos | Grama |
| Bosque | Tree 02 e 03 | Bush 04–06, samambaias, pouca grama | Troncos 02 e galhos (muita: mata antiga) | Stone 06, Boulder 04, Rock 06/07 | Folhiço seco |
| Selva | Selva 01 (emergente, rara), 02 (grande), 03/04 (médias) | Plantas da selva A/C (arbustos), B/D (ervas), cogumelo 02 | Tronco 01 | Pedras e matacões com musgo | Chão de selva |
| Deserto / Neve | 💡 Só planejados; paleta e arte quando a região existir | | | | |

**Lógica das árvores (posicionamento natural):**
- **Grupos (processo de Thomas):** árvores-mãe espalhadas e filhas ao redor, formando matas e clareiras.
- **Vento de oeste:** as sementes caem deslocadas e esticadas para leste, então as matas ficam alongadas, não redondas.
- **Herança de espécie:** filhas de árvores pequenas e médias tendem a ser da mesma espécie (65%).
- **Espaçamento pelo porte:** árvores pequenas podem ficar juntas; as grandes exigem mais espaço. As dominantes são plantadas primeiro.
- **Competição:** a chance de sobreviver cai com a vizinhança apertada. O resultado é muitas árvores pequenas e poucas grandes (distribuição em "J invertido").
- **Pioneiras:** vazios maiores que ~13 u recebem sementes isoladas, fechando a mata sem apagar as clareiras naturais.
- **Simbiose:** sub-bosque em anel ao redor dos troncos, cogumelos em arco e em madeira morta, plantas ripárias nas margens do rio.

**Integração na Unity (verificada em 12/11/2026):**
- Importador de Editor `CodexiaWorldImporter` (menu CODEXIA/Importar pacotes do gerador): extrai os pacotes de `CODEXIA_Import` só para Art, Tiles, Prefabs e Scenes/World. Não apaga o zip; renomeia para `.importado`.
- Pacote `mundo-v04.zip`: 776 arquivos importados sem erro.
- Cena `Assets/CODEXIA/Scenes/World/Regiao Experimental v04.unity` abre e roda em Play Mode: 0 erros, andar e colisão com árvores funcionando.
- Resultado: 554 árvores (temperado 233, bosque 123, selva 198), 2.114 objetos, ruínas da selva como POI.

**Pendente:**
- ⏳ Teste completo pelo usuário: vau, colisão da água, coleta com Enter, ruínas, ordem de desenho das camadas.
- ⏳ Portar o gerador para C#.

### 4.8 Transição gradual entre biomas e ponte do rio (v0.6, 12/11/2026)

**Problema apontado pelo usuário:** na troca de bioma, árvores e plantas nasciam no lugar errado e a transição era brusca. A ponte (na verdade um vau cinza sobre a água) estava artificial.

**Causas encontradas:**
- O chão de selva só era pintado onde a mata era fechada. Por isso árvores de selva apareciam sobre grama comum.
- A Área Inicial proibia o chão de selva, mas o clima continuava dizendo "selva" ali. O resultado era uma linha reta e algumas plantas de selva sobre a grama.
- O ruído da fronteira tinha amplitude pequena demais, e as bordas saíam como retas de ponta a ponta.

**Pesquisa (12/11/2026):**
- Fronteiras naturais não são linhas: formam uma zona de transição (ecótono) com fragmentos. Perto da borda, as falhas na mata crescem e se juntam, e a borda da mancha principal é fractal, com dimensão ≈ 7/4 (Gastner et al.). As bordas são descritas como "suaves" (Forman 1995).
- Arbustos e cipós são as plantas mais abundantes nas bordas de mata ("espécies de borda").
- Na borda há menos árvores grandes e o porte médio cai. O efeito de borda alcança dezenas de metros.

**✅ Implementado no gerador v0.6:**
- **Uma regra só:** chão e plantas usam o mesmo mapa de biomas, em grade de 0,5 u. Planta de selva só nasce sobre chão de selva, e vice-versa.
- **Fronteira sinuosa e fractal:** o clima é deformado em duas escalas, ondas de ~19 u (desvio de 5 u) e de ~7 u (desvio de 2,2 u), com um limitador suave que evita trechos retos. A borda ainda ganha dedos e manchas pequenas.
- **Faixa de transição larga:** meia-largura 0,13 no espaço do clima. Perto da Área Inicial, a selva esmaece aos poucos, com a distância também deformada.
- **Limpeza ecológica:** mancha pequena (< 120 u²) só sobrevive a até 4 u do corpo principal do seu bioma. Manchas soltas são absorvidas pelo bioma vizinho.
- **Copa inteira no bioma:** a árvore só nasce se tronco e copa cabem no seu bioma.
- **Borda:** 35% das árvores vingam bem na borda e só 25% das grandes, que se concentram no interior. A orla recebe arbustos extras, cada um da paleta do seu lado.
- **Resultado:** 0 objetos fora da paleta do bioma (validador), 100% da área livre alcançável.

**✅ Ponte de pedra (arte nova, porque o pacote não tem ponte):**
- **Traço:** segue as escadas e os arcos do pacote (TX Struct): lajes em fiadas com rejunte marrom, meio-fio claro com juntas, pilares de cabeceira e musgo junto ao meio-fio. A face sul do tabuleiro aparece, mostrando a altura da ponte, e há espuma na água dos dois lados.
- **Local:** cruza o rio perpendicular à correnteza. Como o rio gerado por seno corre de leste a oeste no ápice das curvas, ela fica no ápice mais suave perto da trilha, com 3 tiles de largura e 6 de comprimento.
- **Trilha:** chega reta às duas cabeceiras, e nada nasce encostado nelas.
- **Na Unity:** os tiles da ponte ficam numa camada própria ("Camada - Bridge"), acima do chão e abaixo dos objetos. A água sob o tabuleiro usa tiles sem colisor. Os dois parapeitos têm colisores de caixa, então não dá para cair no rio pelo lado.
- **Verificado em Play Mode:** travessia nos dois sentidos, bloqueio no parapeito e personagem desenhado por cima do tabuleiro.

**Entrega:** cena `Assets/CODEXIA/Scenes/World/Regiao Experimental v05.unity` e executável `Builds\Teste 04 - Ponte e Biomas\CODEXIA.exe`. A cena v04 e os testes anteriores continuam intactos.

## 5. Transições entre regiões

💡 Proposta:
- **Ecótono:** faixa de 6–10 tiles em que os parâmetros das duas regiões se misturam gradualmente. Não existe "linha" visível.
- **Elemento ponte:** cada transição tem pelo menos um elemento das duas regiões (por exemplo, pedras do Platô começando no fim do Bosque).
- **Estreitamento (pinch):** a entrada de uma região nova passa por um corredor ou portal (trilha estreita, escada ou portão), que marca a mudança.
- **Marco de chegada:** ao entrar numa região, um marco característico aparece logo no primeiro enquadramento da câmera.

## 6. Navegação sem UI

💡 Proposta:
- **Marcos (weenies):** um por região, visível de longe na câmera atual (estátua, rune pillar alto X3, altar, árvore solitária).
- **Migalhas:** flores, pedrinhas e lanternas ao longo das trilhas naturais.
- **Aberturas atraem:** clareiras e saídas iluminadas ou mais claras indicam caminho; a mata densa indica limite.
- **Portões de progressão (gates):** usados com moderação, como em *A Link to the Past*. Por enquanto são só físicos (muros, portões fechados), sem itens-chave.

## 7. Região experimental (Fase 5)

✅ **Implementada em 12/11/2026** (aprovação: "Pode começar a implementar!"). A proposta original fica abaixo.

| Item | Situação |
|---|---|
| Cena | `Assets/CODEXIA/Scenes/World/Regiao Experimental - R1 R2.unity`, nova. A cena de validação foi preservada |
| Tamanho | 120 × 80 tiles (x −60…60, y −28…52). A Área Inicial (54×24) fica **intacta**, com as mesmas posições, colisões e sistemas |
| Conteúdo novo | 403 árvores (132 Tree 01, 181 Tree 02, 90 Tree 03), 417 arbustos, 665 tufos, 153 flores e 139 pedras (em kits) |
| Cobertura de copa medida | R1 21,8%, ecótono 38,7%, R2 55,6%, borda de R3 10,2% |
| Trilha R1 → R2 | Corredor natural sem calçamento, com grama lisa e 3 flores coletáveis como migalhas |
| POI | Clareira de queda no Bosque Antigo com estátua tomada pela mata (Examinar), 2 pilares rúnicos quebrados, anel de arbustos e um fragmento rúnico |
| Validação automática | ✅ Troncos a ≥ 2,05 u; nada novo dentro da Área Inicial nem sobre o calçamento; nenhum prop dinâmico novo; 100% da área livre alcançável; nenhum bolsão fechado; trilha contínua; todos os coletáveis e interações alcançáveis; YAML com 0 erros |
| Teste em Play Mode | ⏳ **Pendente.** A Unity do projeto estava aberta, mas a janela não ficou acessível para o teste |
| Gerador | `V:\Programação\CODEXIA Unity\Ferramentas\gerador-cena-v3\` (`eco_rules.py` reúne todos os parâmetros). Prévias PNG na mesma pasta |

Proposta original: construir primeiro **R1 Floresta Inicial (expandida) + transição + parte de R2 Bosque Antigo**.
- A clareira atual vira o "coração" de R1. A praça, o poço e o santuário são **mantidos** com os sistemas já testados.
- Ela prova: agrupamentos, clareiras, trilha natural, transição (ecótono) e um POI escondido no Bosque.
- Tamanho sugerido: cerca de 120 × 80 tiles. Validação completa da seção 0, prévia gerada e teste em Play Mode.

## 8. Limitações conhecidas dos assets

- Não há tiles de **água**, **terra/areia** nem **caverna**. R6 e R7 ficam planejadas, sem composição.
- Apenas **3 modelos de árvore**. A variedade vem de agrupamento, densidade e mistura com arbustos, nunca de recolorir.
- Os muros (Wall) e escadas permitem **desníveis**, mas exigem montagem cuidadosa de Sorting Layers e camadas de física (Layer 1–3) e do `StairsLayerTrigger` do pacote.
- Os 3 materiais do pacote desativam o SRP Batcher 2D. Em regiões grandes, é preciso medir o desempenho (Fase 6).

## 9. Pendências para decisão

1. Aprovar ou ajustar o **mapa macro** (2.3) e as **conexões** (2.4).
2. Aprovar a **identidade** de cada região (3).
3. ✅ Pipeline por camadas e regras aprovados e implementados com a base ecológica (4.5).
4. ✅ Região experimental de 120 × 80 implementada (7). ⏳ Falta o teste em Play Mode.
5. Nomes das regiões: os atuais são provisórios e descritivos.
6. Revisão semanal do planejamento do mundo: dia e horário a definir.

## 10. Vila da Cachoeira: início do jogo

### 10.1 Layout v2 da vila: planejado (12/11/2026)

**Problema apontado pelo usuário na v1:** as casas pareciam colocadas ao acaso, e a casa e a horta estavam tortas. Além disso, o número de plantas não batia com o canteiro, as estradas tinham curvas e buracos sem motivo, havia objetos soltos pelo mapa e a ponte não se integrava ao chão.

**Pesquisa (como vilas reais se organizam):**
- Vilas planejadas agrupam as casas dos dois lados de uma rua central (vila de rua) ou em volta de uma praça verde comum (vila de praça).
- As hortas ficam no primeiro anel em volta das casas, seguidas de lavouras maiores, pastos e mata.
- As casas ficam alinhadas, de frente para a rua, com a porta perto dela.

**✅ Regras aplicadas (valem para as próximas zonas):**
- **Grade de tiles:** tudo é alinhado à grade. As casas têm o centro em x,5, então a porta cai no centro de um tile, e todas ficam numa mesma **linha de construção** (pé das fachadas em y = −2), de frente para a rua.
- **Acessos:** cada porta tem um caminho reto de lajes de pedra com 1 tile de largura (a largura da porta), da soleira até encostar na rua. O degrau fica na frente da porta, e a varanda do Hank fica centralizada nela.
- **Rua principal:** reta, com largura constante de 2 tiles, da ponte até a saída leste. Nada de curvas sem motivo.
- **Horta do Hank:** ao lado da casa (1º anel), com 6 × 4 = 24 canteiros, 1 planta centralizada por canteiro e uma cultura por fileira. A cerca fica exatamente a 1 tile do canteiro, com o portão centralizado do lado da rua.
- **Lavoura comunitária:** atrás da rua (2º anel), com 10 × 4 = 40 canteiros, na mesma regra.
- **Praça verde:** fica no eixo do Armazém (x = 5,5), com calçamento 5 × 5 em volta do poço, bancos simétricos e lampiões nos quatro cantos.
- **Função de cada casa, de oeste para leste:**
  - Casa do Pescador, junto ao lago, com o caminho de lajes até o píer;
  - Casa do Tio Hank, perto da ponte e da mata (caçador);
  - horta do Hank;
  - Armazém, no centro, de frente para a praça;
  - Casa da Família Moreira;
  - Casa do Ferreiro, na saída leste.
- **Objetos:** só onde têm função: caixotes e barris junto ao Armazém e ao Ferreiro, lenha e toco junto ao Hank, flores simétricas nas frentes e lampiões a intervalos. Removidos os tufos e objetos soltos.
- **Ponte integrada:** calçamento de pedra nas duas cabeceiras, lampiões simétricos e colisores dos parapeitos corrigidos para a posição do desenho.
- **Mata:** só fora da área roçada da vila, com arbustos na orla.

**Entrega:** o pacote `vila-cachoeira-v2.zip` foi enviado ao importador do projeto. ⏳ Falta importar, rodar `CODEXIA → Montar zonas` e testar na Unity.

### 10.0 Implementado em 12/11/2026 (v1 da vila)

**Pesquisa: como o mapa do Stardew Valley funciona**
- O mundo é feito de **locais separados**, cada um com seu mapa, ligados por **warps**: um tile de gatilho leva a um tile de chegada em outro local.
- Andar além da borda do mapa ou entrar numa construção **escurece a tela e troca de mapa**. Portas usam ações de warp.
- Os mapas usam tiles de 16 px em camadas: Back (chão e água), Buildings, Paths, Front (copas das árvores) e AlwaysFront.
- A câmera acompanha o jogador (`ViewportFollowPlayer`) e para na borda do mapa.
- Fontes: Stardew Valley Wiki, Modding:Maps; FreeGameSprites (tiles de 16 px ampliados 4×); fórum oficial (câmera em mapas pequenos).

**✅ Equivalente no CODEXIA:**
- **Uma zona = uma cena da Unity**, com:
  - `ZoneInfo`: nome mostrado ao chegar e limites da câmera;
  - `ZoneSpawnPoint`: pontos de chegada;
  - `ZoneTransition`: gatilho da passagem.
- **ZoneLoader:** escurece a tela, carrega a zona, põe a personagem no ponto de chegada, ajusta a câmera e mostra o nome da zona. O respawn passa a ser na zona atual.
- **Jogador, câmera e HUD continuam entre as zonas** (`PersistentSessionObject`), então vida e inventário são mantidos.
- A lista de cenas do projeto não foi alterada: no editor a zona carrega direto, e o build recebe a lista de zonas.

**Ligações atuais:**
- Vila da Cachoeira (início, em frente à casa do Tio Hank) → ponte de pedra em arco, a oeste → Floresta do Rio, pela borda leste da estrada.
- A volta pela borda leste da Floresta leva de novo à vila.
- A leste da vila, a Estrada da Montanha está bloqueada por uma árvore caída (zona futura).

**Composição da vila (inspirada na imagem de referência, 64 × 44 tiles):**
- **Norte:** paredão de rocha, com o rio do platô virando cachoeira e caindo num lago com píer de pesca.
- **Riacho:** desce para o sul e passa sob a ponte em arco (saída para a Floresta).
- **Casas:** casa do Tio Hank (galhada sobre a porta, varanda, lenha e machado no toco) e três casas vizinhas (telhado de ardósia, palha e telha).
- **Centro:** pracinha com poço e banco; horta cercada do Hank com repolho, cenoura, abóbora e brotos.
- **Caminhos:** lampiões de pedra, placas que se podem ler (Ponte da Cachoeira, Casa do Tio Hank, Estrada da Montanha) e mata do temperado em volta, com quintais roçados.

**Arte nova (só o que o pacote não tem):** casas, cerca, terra arada e plantações, deck/píer, lenha e toco, paredão de rocha, cachoeira animada (4 quadros) e ponte em arco. Paleta tirada da madeira e da pedra do pacote.

**Verificado em Play Mode:**
- começa em frente à casa;
- atravessa a ponte → Floresta carregada;
- volta pela borda leste → vila, com o nome "Vila da Cachoeira" na tela;
- 0 erros.

Executável: `Builds\Teste 05 - Vila da Cachoeira\CODEXIA.exe`.

**Ferramenta:** `CODEXIA → Montar zonas` (editor) monta a vila a partir do JSON do gerador e liga a Floresta. Pode ser rodada de novo.

**Pendências:**
- Interiores das casas (portas como passagens, igual ao Stardew).
- Mecânicas de pesca no píer, plantio na horta e caça.
- Moradores.
- Nome definitivo da vila.

**✅ Decisão do usuário (12/11/2026):** o jogo começa numa vila próxima a uma cachoeira, como na imagem de referência. A protagonista começa **em frente à ponte sobre a cachoeira**, com a vila logo à frente (lore no [[CODEXIA - GDD]], 5.1). Mecânicas previstas: pesca, plantio, coleta, caça e questlines.

**💡 Proposta de composição (aguarda aprovação):**
- **Platô de cima:** um rio corre e chega à borda do paredão. A ponte de pedra cruza o rio logo antes da queda, e é ali que o jogo começa, com a vila visível lá embaixo.
- **Cachoeira e lago:** a água cai num lago de queda (bom para **pesca**). A névoa da queda deixa a margem úmida, com musgo, samambaias e pedras molhadas, como na mata ciliar.
- **Descida:** a trilha desce do platô por escadas de pedra (prefabs Stairs do pacote) e por uma rampa natural.
- **Vila:** casas de madeira em volta de uma pequena praça de terra, com poço (Well do pacote), placas (Road Sign), lampiões (Stone Lantern), caixotes e barris. A **casa do Tio Hank** fica na borda da vila, voltada para a floresta, coerente com um caçador reservado.
- **Atividades:** **hortas** cercadas atrás das casas (**plantio**); a floresta temperada em volta serve para **coleta** e **caça**. O riacho que sai do lago passa sob uma segunda ponte, na saída da vila.
- **Ecologia:** a vila fica no temperado, e a selva só aparece rio abaixo (coerente com o mapa atual: a selva segue a umidade da várzea).

**O que o pacote não tem e precisaria de arte nova (pela regra de arte):**
- casas de madeira e telhados;
- cercas de madeira;
- canteiros de horta e plantas cultiváveis;
- paredão de rocha (penhasco);
- cachoeira animada e lago;
- cais de pesca;
- ponte em arco vista de lado (para rios que correm de norte a sul).

O pacote já tem: poço, placas, lampião de pedra, caixotes, barris, potes, escadas, baú e muros de pedra.

**Pendências para decisão:**
1. Nome da vila e do mundo.
2. Tamanho da vila (quantas casas) e quem mora nela além da protagonista e do Tio Hank.
3. Se o incidente da infância (o elfo negro) será um prólogo jogável.
4. Ordem da arte nova: sugestão de começar por paredão, cachoeira e lago, depois casas e cercas, depois hortas.

## 11. Histórico

- **0.8 (12–14/11/2026):** layout v2 da vila planejado em grade, com rua, praça e anéis de horta e lavoura (10.1).
- **0.7 (12/11/2026):** pesquisa do mapa do Stardew Valley; mundo em zonas com troca de área por passagem; Vila da Cachoeira v1 implementada e ligada à Floresta (10.0); executável Teste 05.
- **0.6 (12/11/2026):**
  - transição gradual entre biomas, com pesquisa de ecótono (4.8): mesma regra para chão e plantas, fronteira fractal, orla de arbustos e árvores grandes no interior;
  - ponte de pedra no lugar do vau (4.8), testada em Play Mode; executável Teste 04;
  - vila da cachoeira registrada como início do jogo, com proposta de layout (10).

- **0.5 (12/11/2026):** biomas com paleta exclusiva (4.7), lógica de árvores com vento, herança e competição documentada; importador e cena v04 integrados e verificados na Unity.
- **0.4 (12/11/2026):** ajustes pedidos pelo usuário sobre a prévia v0.4:
  - mais selva (a umidade do rio alcança mais longe) e um ecótono misto, em que sementes das duas floras se cruzam na fronteira;
  - matas mais fechadas e menos clareiras grandes: uma "chuva de sementes" de pioneiras fecha vazios maiores que ~13 u e deixa clareiras naturais;
  - as **fileiras retas plantadas do demo na Área Inicial foram removidas** e substituídas por mata natural; a clareira da praça, os coletáveis, as interações e o spawn ficaram protegidos;
  - mais sub-bosque: arbustos, mato alto, samambaias na sombra, cogumelos e troncos caídos.

- **0.3 (12/11/2026):** pesquisa de clima, rio, trilhas, fungos e padrões espaciais (4.6). Gerador v0.4 (temperado + rio + selva) com arte nova redesenhada a partir das folhas do usuário. Validado nas prévias; falta a integração na Unity.
- **0.2 (12/11/2026):** pesquisa ecológica (4.5) registrada e aplicada. Região experimental implementada e validada automaticamente (7). Falta o teste em Play Mode.
- **0.1 (12/11/2026):** criação do documento. Continente aprovado; mapa macro, identidades, pipeline procedural, transições e região experimental registrados como proposta.
