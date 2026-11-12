---
tipo: documento
categoria: game-design
projeto: CODEXIA
status: preliminar
versao: 0.1
idioma: pt-BR
tags:
  - codexia
  - identidade-visual
  - pixel-art
---

# CODEXIA — Manual de Identidade Visual (preliminar)

> Documento derivado da inspeção do pacote original **Pixel Art Top Down - Basic v1.2.3**. Define diretrizes de consistência; não cria nem altera arte.

## 1. Referência visual

A referência visual principal de CODEXIA é o pacote **Pixel Art Top Down - Basic v1.2.3**, conforme decisão registrada no GDD. Os recursos originais são a fonte prioritária para a composição visual do cenário.

## 2. Características observadas

- Pixel art top-down, com leitura visual de cima e leve perspectiva de volume nos objetos.
- Vegetação em tons de verde e oliva, com variação de luminosidade entre copa, folhagem e sombra.
- Terrenos de grama e pedra com textura discreta e variações de borda/transição.
- Objetos e estruturas com predominância de tons terrosos, madeira e pedra.
- Sombras separadas em texturas próprias para vegetação e objetos.
- Elementos de cenário com escala e densidade de detalhes coerentes entre si.

Estas são observações qualitativas das texturas e cenas de demonstração, não valores finais de paleta.

## 3. Regras de consistência

1. Priorizar os sprites, tiles, prefabs e materiais existentes no pacote.
2. Preservar as cores, proporções, pixels, transparências e estilo dos originais.
3. Não recolorir, redesenhar, reestilizar ou editar os arquivos originais.
4. Não criar arte nova para substituir recursos que já existam.
5. Não usar formas provisórias quando houver asset adequado no pacote.
6. Manter coerência entre terreno, vegetação, objetos, estruturas e sombras.
7. Evitar excesso de decoração que prejudique leitura, navegação ou desempenho.
8. Não inserir elementos incompatíveis com o ambiente nem bloquear caminhos indevidamente.
9. Não tratar a cena de demonstração como composição obrigatória ou mapa definitivo.

## 4. Escala e pixel art

- O GDD estabelece células de 32×32 px como referência de organização, não como tamanho obrigatório de todos os sprites.
- As texturas do pacote possuem dimensões variadas; tamanho de atlas não deve ser confundido com tamanho de tile.
- Pixels por unidade, pivôs, sorting e escala de câmera permanecem pendentes de validação no Unity.
- A filtragem Point observada nos metadados originais deve ser preservada durante a importação inicial.

## 5. Composição de ambientes

- Usar terreno e tilesets existentes como base da leitura do chão.
- Distribuir árvores, arbustos, grama, pedras e objetos de modo coerente com o ambiente.
- Reservar composição manual para pontos importantes, construções e áreas com identidade própria.
- Usar distribuição procedural controlada apenas depois de definir regras de espaçamento, colisão, densidade e compatibilidade ambiental.
- Preservar caminhos e áreas de circulação legíveis.

## 6. Água

A água deve seguir a referência complementar **Top Down Kingdom 2D**, combinada com a identidade do pacote principal. O pacote complementar ainda não foi localizado para inspeção nesta etapa. Até sua análise, não definir textura, paleta, animação ou shader final para água.

## 7. Personagem

O personagem demonstrativo do pacote não será usado como protagonista nem personagem jogável. O protagonista de CODEXIA será original e permanece fora do escopo imediato. O personagem de demonstração poderá servir apenas para observar tecnicamente animações nas quatro direções, idle, transições e ritmo.

## 8. Itens ainda não definidos

- Paleta formal com valores de cor.
- Pixels por unidade e escala final de sprites.
- Escala e enquadramento de câmera.
- Regras finais de sorting layers e profundidade visual.
- Tratamento visual da água.
- Regras específicas por bioma.
- Critérios quantitativos de densidade e espaçamento.

Esses itens só deverão ser fechados após validação no editor Unity e aprovação no GDD.

## 9. Histórico

- **0.1:** diretrizes preliminares derivadas do pacote visual principal e das regras já aprovadas no GDD.
