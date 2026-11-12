---
tipo: documento
categoria: game-design
projeto: CODEXIA
status: inicial
versao: 0.1
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

CODEXIA é um RPG 2D em pixel art, com câmera top-down e mundo aberto conectado. A experiência deverá combinar exploração, progressão de personagem, combate, missões e descoberta de regiões distintas. A prioridade atual é definir uma base sólida de projeto antes de implementar sistemas avançados.

## 2. Ficha do projeto

| Campo | Definição atual |
|---|---|
| Nome | CODEXIA |
| Gênero | RPG |
| Engine | Unity |
| Dimensão | 2D |
| Câmera | Top-down |
| Direção visual | Pixel art |
| Referência de grade | Células de 32×32 px (referência de organização, não tamanho obrigatório de todo sprite) |
| Linguagem planejada | C# |
| Idioma | Português (PT-BR) |
| Renderização | Procedural / híbrida com sprites e texturas |
| Mundo | Aberto e conectado |
| Construção dos mapas | Manual, procedural controlada e híbrida |
| Lore | A definir |
| Protagonista | Original, ainda a desenvolver |

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

### 4.3 Água

A estética da água de **Top Down Kingdom 2D** foi aprovada como referência complementar. A solução deverá combinar com o pacote visual principal e priorizar leveza. Uma possibilidade técnica a avaliar é usar texturas reutilizáveis com animação procedural ou shader/material leve. Essa abordagem é uma proposta, não uma decisão técnica final. Licenças deverão ser verificadas antes de incorporar assets de terceiros.

## 5. Protagonista

O protagonista será um personagem original, ainda não desenhado nem implementado.

O personagem demonstrativo incluído no pacote de assets não deverá ser usado como personagem jogável, protagonista ou base direta do protagonista. Seus exemplos podem ser estudados somente como referência geral de movimento: animações nas quatro direções, idle, transições, ritmo e proporções dos quadros.

## 6. Mundo e Level Design

O mundo será aberto e conectado, composto por regiões e áreas especiais, como biomas, cavernas e dungeons. A lista definitiva de regiões, a lore e a organização narrativa ainda serão definidas.

### 6.1 Abordagem de construção

- **Manual:** cidades, vilas, construções importantes, locais narrativos, áreas de missão e espaços únicos.
- **Procedural controlada:** distribuição de vegetação, pedras, pequenas decorações, variações de terreno e partes de regiões naturais.
- **Híbrida:** florestas, campos, cavernas e outras áreas naturais que precisam de identidade própria e variedade.

### 6.2 Regras de coerência

A geração e composição dos mapas deverão respeitar regras para evitar, por exemplo, árvores dentro de construções, objetos bloqueando caminhos indevidamente e elementos incompatíveis com o ambiente.

## 7. Sistemas de jogo — visão futura

Estão no horizonte do projeto, mas ainda não foram especificados nem aprovados para implementação:

- Progressão por níveis e atributos.
- Classes e poderes.
- Missões e requisitos para obtenção de itens.
- Armas como espada, foice e bastão.
- Poderes ou elementos como fogo, água e terra.
- Recompensas condicionadas a missões, níveis ou atributos.

A prioridade imediata é planejar e construir a base do jogo. Esses sistemas serão detalhados em etapas posteriores.

## 8. Diretrizes técnicas iniciais

- Unity será a engine.
- C# é a linguagem planejada; versão e configuração ainda precisam ser definidas.
- A solução visual deverá combinar recursos em sprites/texturas com elementos procedurais quando fizer sentido.
- Desempenho e fluidez são requisitos importantes.
- A arquitetura técnica será definida antes da implementação.

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

Esta ordem é um plano de trabalho, não uma declaração de tarefas já executadas.

## 10. Fora do escopo imediato

- Criar ou implementar o protagonista.
- Escrever scripts ou implementar sistemas de jogo.
- Criar arte nova.
- Definir a lore completa.
- Fechar a lista de biomas e regiões.
- Implementar progressão, classes, poderes, combate ou missões avançadas.

Esses tópicos dependem de planejamento e aprovação explícita.

## 11. Questões em aberto

- Qual será a lore, o universo e o tom narrativo?
- Como será o protagonista original?
- Quais serão os biomas e a estrutura das regiões?
- Qual será a escala de tiles, sprites, personagens e câmera?
- Qual versão da Unity e configuração de renderização serão adotadas?
- Qual será a arquitetura para mapas manuais, procedurais e híbridos?
- Como será implementada a água mantendo compatibilidade visual e bom desempenho?
- Quais serão os detalhes dos sistemas de RPG e sua ordem de implementação?

## 12. Estado das decisões

### Confirmado
- RPG 2D top-down em pixel art na Unity.
- Mundo aberto e conectado.
- Referência visual principal: Pixel Art Top Down - Basic v1.2.3.
- Protagonista original; personagem demonstrativo do pacote não será usado como protagonista ou personagem jogável.
- Água inspirada visualmente em Top Down Kingdom 2D.
- Preservação dos assets originais.
- Planejamento e organização antes de implementação.

### Propostas ainda não definitivas
- Água com textura reutilizável e animação procedural/shader leve.
- Divisão de áreas entre construção manual, procedural controlada e híbrida conforme os critérios descritos.
- Sequência de desenvolvimento apresentada neste documento.

## 13. Documentos relacionados planejados

Estes documentos ainda não foram criados; serão adicionados conforme o projeto amadurecer:

- Manual de Identidade Visual
- Catálogo de Assets
- Documento de Level Design
- Arquitetura Técnica
- Documento de Sistemas de Jogo
- Lore e Universo
- Roadmap de Desenvolvimento

## 14. Histórico

- **0.1:** consolidação inicial das informações e decisões existentes no planejamento do CODEXIA.
