---
tipo: documento
categoria: game-design
projeto: CODEXIA
status: em-andamento
versao: 0.5
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
- A orientação do projeto é utilizar os assets existentes como base visual, sem gerar arte nova e sem recorrer a placeholders quando houver recurso adequado.

### 4.3 Água

A estética da água de **Top Down Kingdom 2D** foi aprovada como referência complementar. O pacote de água será enviado para inspeção em conversa separada. A solução deverá combinar com o pacote visual principal e priorizar leveza. Uma possibilidade técnica a avaliar é usar texturas reutilizáveis com animação procedural ou shader/material leve. Essa abordagem é uma proposta, não uma decisão técnica final. Licenças deverão ser verificadas antes de incorporar assets de terceiros.

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

- Qual será a lore, o universo e o tom narrativo?
- Como será o protagonista original?
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
- Referência visual principal: Pixel Art Top Down - Basic v1.2.3.
- Protagonista original; personagem demonstrativo do pacote não será usado como protagonista ou personagem jogável.
- Água inspirada visualmente em Top Down Kingdom 2D; pacote será enviado para inspeção.
- Versão instalada da engine: Unity 6.3 LTS **6000.3.28f1**, com Visual Studio Community 2026 como editor de C#. A Unity 6.6 já instalada no computador não é usada no CODEXIA.
- Projeto Unity em `V:\Programação\CODEXIA Unity\CODEXIA`, fora do cofre do Obsidian, criado pelo template Universal 2D (URP com Renderer 2D).
- Sorting Layers e camadas de física do pacote adotadas no CODEXIA (Default, Layer 1, Layer 2, Layer 3), importadas do `TagManager.asset` original.
- Diretrizes de arquitetura e padrões C# registradas em [[Diretrizes de Arquitetura e Padrões C#]] (a divisão Core/Shared/UI continua como proposta, sem implementação).
- Preservação dos assets originais.
- Utilizar os assets existentes como base visual; não gerar arte nova nem substituir assets adequados por placeholders.
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
- [[Diretrizes de Arquitetura e Padrões C#]] — padrões para o código futuro (ainda sem implementação).
- Documento de Level Design — a desenvolver.
- Documento de Sistemas de Jogo — a desenvolver.
- Lore e Universo — a desenvolver.
- Roadmap de Desenvolvimento — a desenvolver.

## 14. Histórico

- **0.1:** consolidação inicial das informações e decisões existentes no planejamento do CODEXIA.
- **0.2:** inclusão das regras reforçadas de uso dos assets e registro dos documentos iniciais de catálogo, identidade visual e planejamento da base técnica.
- **0.3:** aprovação da Unity 6.3 com URP 2D; registro da preparação técnica inicial e envio posterior do pacote complementar de água.
- **0.4:** Unity 6.3 LTS (6000.3.28f1) instalada; projeto CODEXIA criado com o template Universal 2D; pacote principal importado sem ProjectSettings; criação do [[Relatório de Validação Técnica]].
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

### Próximas atividades

- Testar o executável no computador do usuário e registrar o resultado.
- Confirmar a ordenação por profundidade (Custom Axis 0, 1, 1).
- Decidir sobre o patch URP 2D Lit para Unity 6.3 incluído no pacote.
- Definir escala de câmera, resolução-alvo e uso de Pixel Perfect Camera.
- Inspecionar a cena SC All Props.
- Retirar o ZIP de assets do cofre (aprovado; precisa ser feito pelo usuário).
- Inspecionar o pacote complementar de água quando enviado. Até agora só foi recebida uma imagem de referência (`TX waterreference.jpg`).
- Planejar o protagonista original e a arquitetura de código (Core/Shared/UI) para aprovação, antes de qualquer script.

O protagonista e os scripts próprios ainda não foram criados. O executável atual é uma vista estática da área inicial (câmera fixa, sem personagem).
