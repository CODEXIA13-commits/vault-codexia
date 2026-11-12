---
tipo: documento
categoria: planejamento-tecnico
projeto: CODEXIA
status: planejamento-inicial
versao: 0.1
idioma: pt-BR
tags:
  - codexia
  - unity
  - arquitetura
  - cena-base
---

# CODEXIA — Planejamento da Base Técnica e Cena de Validação

> Planejamento inicial. Não representa implementação concluída nem aprova sistemas de RPG. A arquitetura e as configurações finais dependem de validação no Unity e atualização do GDD.

## 1. Objetivo da primeira base

Preparar uma cena de validação visual e técnica que demonstre a identidade ambiental do CODEXIA usando os assets existentes, antes da implementação do protagonista original e dos sistemas avançados.

A base deverá permitir avaliar:
- leitura do terreno e das transições;
- coerência entre tiles, vegetação, objetos, estruturas e sombras;
- escala visual e enquadramento da câmera;
- organização de camadas e ordenação de sprites;
- circulação e clareza dos caminhos;
- desempenho inicial da cena.

## 2. Conteúdo visual previsto

A composição deverá usar exclusivamente recursos adequados já existentes no pacote principal:
- tiles de grama e pedra;
- tiles de parede/estrutura quando necessários;
- prefabs de vegetação;
- prefabs de objetos e estruturas;
- materiais e sombras originais.

Não criar sprites, ícones, personagens, texturas ou ilustrações novas. Não substituir assets adequados por retângulos, círculos ou outros placeholders. Não editar os arquivos originais.

O protagonista não será incluído nesta primeira base. O prefab demonstrativo do pacote não será colocado como personagem jogável.

## 3. Organização técnica a avaliar

A arquitetura deverá separar responsabilidades sem antecipar sistemas não aprovados:
- cena de validação ambiental;
- organização de Tilemaps por função, a confirmar após inspeção;
- camadas de sorting para chão, objetos, vegetação e estruturas, a validar com os prefabs;
- colisões apenas onde os assets e o desenho do espaço exigirem;
- câmera ortográfica top-down, com escala a definir por teste;
- separação entre recursos originais do pacote e arquivos próprios de CODEXIA.

Não criar scripts próprios nesta etapa. Os cinco scripts do pacote devem ser avaliados individualmente antes de qualquer decisão de reutilização.

## 4. Sequência de preparação

1. Confirmar versão Unity e pipeline de renderização.
2. Criar projeto Unity limpo, sem sobrescrever o pacote original.
3. Importar o Unity Package original como dependência visual, preservando sua pasta e metadados.
4. Inspecionar as cenas demonstrativas e os prefabs no editor.
5. Validar pixels por unidade, pivôs, sorting layers, colisores e animações.
6. Definir a grade prática e a escala da câmera com base nos recursos reais.
7. Planejar a composição da cena-base com tiles e prefabs existentes.
8. Montar a cena de validação sem protagonista nem sistemas de RPG.
9. Testar leitura visual, circulação e desempenho.
10. Registrar resultados e decisões aprovadas no GDD.

## 5. Cuidados com arquivos originais

- Manter o ZIP original intacto.
- Não editar diretamente arquivos do pacote.
- Não copiar os arquivos de ProjectSettings do projeto de origem para o projeto de CODEXIA sem análise.
- Não alterar os scripts, sprites, texturas, materiais, prefabs, animações ou cenas originais.
- Arquivos específicos de CODEXIA deverão ficar separados e identificados como próprios do projeto.
- Qualquer adaptação deverá preservar o original e ser aprovada antes de execução.

## 6. Dependências e bloqueios atuais

- A versão Unity ainda não está definida no GDD.
- O changelog do pacote 1.2.3 cita correção de shader para URP 2D no Unity 6.3; isso deve orientar o teste de compatibilidade, sem ser tratado como escolha já aprovada.
- A referência de água **Top Down Kingdom 2D** ainda não está disponível para inspeção.
- Ainda é necessário validar os assets no editor Unity, pois a inspeção do ZIP não substitui a abertura das cenas e prefabs no próprio editor.
- O projeto Unity de CODEXIA ainda precisa ser criado/configurado.

## 7. Fora desta base

- Protagonista original.
- Combate, armas, poderes, classes e progressão.
- Missões, inventário e recompensas.
- Lore e regiões definitivas.
- Geração procedural em escala de mundo.
- Arte nova ou substituição de recursos existentes.

## 8. Critérios para considerar a base validada

- Os assets originais são exibidos sem alteração visual.
- A cena mantém coerência de escala e composição.
- Tiles e objetos não apresentam cortes ou transições visuais indevidas.
- A vegetação e os objetos não bloqueiam caminhos sem intenção.
- Sorting e sombras funcionam de forma consistente.
- A cena executa de forma estável no ambiente de teste.
- Todas as decisões técnicas aprovadas são registradas no GDD.

## 9. Histórico

- **0.1:** planejamento inicial da base técnica e da cena de validação, sem implementação.
