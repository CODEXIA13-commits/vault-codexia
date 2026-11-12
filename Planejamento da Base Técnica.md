---
tipo: documento
categoria: planejamento-tecnico
projeto: CODEXIA
status: planejamento-inicial
versao: 0.3
idioma: pt-BR
tags:
  - codexia
  - unity
  - urp
  - arquitetura
  - cena-base
---

# CODEXIA — Planejamento da Base Técnica e Cena de Validação

> Planejamento inicial. Não representa implementação concluída nem aprova sistemas de RPG. A arquitetura e as configurações finais dependem de validação no Unity.

## 1. Objetivo da primeira base

Preparar uma cena de validação visual e técnica que demonstre a identidade ambiental do CODEXIA usando os assets existentes, antes da implementação do protagonista original e dos sistemas avançados.

A base deverá permitir avaliar leitura do terreno, transições, coerência entre tiles, vegetação, objetos, estruturas e sombras, escala visual, câmera, sorting, circulação e desempenho inicial.

## 2. Configuração aprovada

- Engine: Unity 6.3.
- Template planejado: 2D (URP).
- Render pipeline: Universal Render Pipeline.
- Renderer: 2D Renderer.
- Linguagem: C# (versão/configuração definida pela instalação do editor).
- Plataforma inicial de teste: ainda a confirmar.

A configuração deverá ser criada pelo Unity Hub/editor. Não copiar ProjectSettings do pacote de terceiros para o projeto CODEXIA.

## 3. Conteúdo visual previsto

A composição deverá usar recursos adequados já existentes no pacote principal:
- tiles de grama e pedra;
- tiles de parede/estrutura quando necessários;
- prefabs de vegetação;
- prefabs de objetos e estruturas;
- materiais e sombras originais.

Não criar sprites, ícones, personagens, texturas ou ilustrações novas. Não substituir assets adequados por retângulos, círculos ou outros placeholders. Não editar os arquivos originais.

O protagonista não será incluído nesta primeira base. O prefab demonstrativo do pacote não será colocado como personagem jogável.

## 4. Organização técnica a avaliar

- Cena de validação ambiental separada das cenas demonstrativas do pacote.
- Tilemaps organizados por função, conforme os tiles existentes e o resultado da inspeção.
- Sorting Layers para chão, objetos, vegetação e estruturas, a validar com os prefabs.
- Colisões apenas onde os assets e o desenho do espaço exigirem.
- Câmera ortográfica top-down, com escala a definir por teste.
- Separação entre recursos originais do pacote e arquivos próprios de CODEXIA.
- Nenhum script próprio nesta etapa. Os cinco scripts do pacote devem ser avaliados individualmente antes de qualquer decisão de reutilização.

## 5. Sequência de preparação no Unity

1. Instalar/abrir Unity Hub e instalar Unity 6.3 com os módulos necessários ao ambiente de teste.
2. Criar um projeto novo usando o template **2D (URP)**.
3. Nomear o projeto como CODEXIA e escolher uma pasta de desenvolvimento separada do vault do Obsidian.
4. Fazer uma cópia de segurança do ZIP original antes da importação.
5. Importar o Unity Package original pelo Package Import, sem editar seus arquivos.
6. Conferir se o pipeline ativo usa o URP 2D Renderer e se materiais/shaders do pacote são compatíveis.
7. Abrir as cenas demonstrativas em modo de inspeção; não salvar alterações nelas.
8. Verificar prefabs, Tile Palettes, texturas, animações, pivôs, pixels por unidade, sorting layers e colisores.
9. Criar uma cena própria de CODEXIA somente após compreender a organização dos recursos.
10. Compor a cena de validação com os assets existentes.
11. Testar visual, circulação, ordenação e desempenho.
12. Registrar resultados e decisões aprovadas no GDD.

### 5.1 Situação da sequência (12/11/2026)

| Passo | Situação |
|---|---|
| 1. Unity Hub e Unity 6.3 | ✅ Unity 6.3 LTS 6000.3.28f1 instalada |
| 2–3. Projeto CODEXIA (2D URP) | ✅ Criado em `V:\Programação\CODEXIA Unity\CODEXIA` com o template Universal 2D |
| 4. Backup do ZIP | ✅ `V:\Programação\CODEXIA Backups\`, conferido por SHA-256 |
| 5. Importação | ✅ Sem ProjectSettings; 0 erros e 1 aviso |
| 6. URP 2D Renderer e shaders | ✅ Renderer 2D ativo; materiais renderizam sem erro. ⏳ Luzes 2D não testadas |
| 7. Cenas demonstrativas | ✅ SC Demo inspecionada sem salvar. ⏳ SC All Props pendente |
| 8. Propriedades técnicas | ✅ Levantadas. ⏳ Sorting depende de decisão |
| 9–12. Cena própria, composição, testes e registro final | ⏳ Pendentes |

Detalhes no [[Relatório de Validação Técnica]].

## 6. Cuidados com arquivos originais

- Manter o ZIP original intacto.
- Não editar diretamente arquivos do pacote.
- Não copiar os arquivos de ProjectSettings do projeto de origem para o projeto de CODEXIA sem análise.
- Não alterar scripts, sprites, texturas, materiais, prefabs, animações ou cenas originais.
- Arquivos específicos de CODEXIA deverão ficar separados e identificados como próprios do projeto.
- Qualquer adaptação deverá preservar o original e ser aprovada antes de execução.
- Ao inspecionar cenas demonstrativas, descartar alterações não intencionais em vez de salvar sobre as cenas originais.

## 7. Dependências e bloqueios atuais

- O pacote complementar de água **Top Down Kingdom 2D** será enviado pelo usuário em outra conversa; sua inspeção está pendente.
- ~~A versão Unity 6.3 e URP 2D ainda precisam ser instalados/configurados.~~ Resolvido em 12/11/2026 (6000.3.28f1).
- O pacote depende das Sorting Layers e camadas de física Layer 1, 2 e 3 do projeto de origem, que não foram importadas. A solução aguarda decisão.
- A inspeção do ZIP não substitui a abertura das cenas e prefabs no editor.
- A plataforma inicial de teste (Windows ou outra) ainda precisa ser confirmada.
- Escala de câmera, pixels por unidade, sorting e colisões dependem da inspeção no editor.

## 8. Fora desta base

- Protagonista original.
- Combate, armas, poderes, classes e progressão.
- Missões, inventário e recompensas.
- Lore e regiões definitivas.
- Geração procedural em escala de mundo.
- Arte nova ou substituição de recursos existentes.

## 9. Critérios para considerar a base validada

- Os assets originais são exibidos sem alteração visual.
- A cena mantém coerência de escala e composição.
- Tiles e objetos não apresentam cortes ou transições visuais indevidas.
- A vegetação e os objetos não bloqueiam caminhos sem intenção.
- Sorting e sombras funcionam de forma consistente.
- A cena executa de forma estável no ambiente de teste.
- Todas as decisões técnicas aprovadas são registradas no GDD.

## 10. Histórico

- **0.1:** planejamento inicial da base técnica e da cena de validação, sem implementação.
- **0.2:** Unity 6.3 com URP 2D aprovada; sequência de preparação atualizada.
- **0.3:** passos 1 a 8 executados e verificados; dependência de Sorting Layers registrada.
