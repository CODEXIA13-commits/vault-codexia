---
tipo: documento
categoria: game-design
projeto: CODEXIA
status: catalogo-inicial
versao: 0.2
idioma: pt-BR
tags:
  - codexia
  - assets
  - catalogo
  - pixel-art
---

# CODEXIA — Catálogo de Assets

> Inventário técnico e visual inicial do pacote original **Pixel Art Top Down - Basic v1.2.3**. Este documento descreve o conteúdo encontrado; não autoriza modificar, reestilizar ou redistribuir os arquivos.

## 1. Fonte analisada

- Arquivo: `Pixel Art Top Down - Basic v1.2.3.zip`
- Tamanho do ZIP: 2.622.004 bytes (aprox. 2,5 MiB).
- Conteúdo Unity: `Pixel Art Top Down - Basic v1.2.3.unitypackage`.
- Versão indicada pelo pacote: 1.2.3.
- Caminho interno principal: `Assets/Cainos/Pixel Art Top Down - Basic/`.
- Inspeção realizada em modo somente leitura. O ZIP original não foi alterado e não foi importado para um projeto Unity.

O Unity Package contém 1.558 entradas internas no formato de exportação do Unity e 330 caminhos de assets identificáveis: 312 sob `Assets/` e 18 arquivos de configurações de projeto sob `ProjectSettings/`.

## 2. Inventário resumido

| Categoria | Quantidade | Observações |
|---|---:|---|
| Prefabs de vegetação | 25 | Árvores, arbustos, flores e gramas |
| Prefabs de objetos/estruturas | 52 | Props, elementos de cenário, portões, escadas e estruturas |
| Prefab demonstrativo de personagem | 1 | Não será protagonista nem personagem jogável de CODEXIA |
| Prefabs de Tile Palette | 3 | Grass, Stone Ground e Wall |
| Assets de tiles das palettes | 179 | Arquivos `.asset` associados às palettes |
| Elementos de Tile Palette, contando os 3 prefabs | 182 | 179 tiles + 3 prefabs de palette |
| Texturas PNG no pacote Unity | 9 | Plant, Player, Props, sombras, Struct e tilesets |
| Animações | 6 | E, N, S, W, Idle e Move |
| Animator Controller | 1 | AC Player |
| Scripts C# incluídos | 5 | Ver seção 5 |
| Materiais | 4 | Props, Shadow, Tileset e FX Rune Glow |
| Shaders | 2 | Incluídos no pacote |
| Cenas demonstrativas | 2 | SC Demo e SC All Props |

Além do Unity Package, o ZIP externo contém duas variações extras de textura com sombras (`TX Plant with Shadow.png` e `TX Props with Shadow.png`), arquivos de documentação e changelog, e `Scene Overview.png`.

## 3. Texturas e finalidade visual

| Arquivo | Dimensão | Uso identificado |
|---|---:|---|
| TX Plant.png | 512×512 | Atlas de vegetação |
| TX Player.png | 128×128 | Atlas do personagem demonstrativo |
| TX Props.png | 512×512 | Objetos e decoração |
| TX Shadow Plant.png | 512×512 | Sombras associadas à vegetação |
| TX Shadow.png | 512×512 | Atlas de sombras |
| TX Struct.png | 512×512 | Estruturas e elementos arquitetônicos |
| TX Tileset Grass.png | 256×256 | Terreno/grama |
| TX Tileset Stone Ground.png | 256×256 | Terreno de pedra |
| TX Tileset Wall.png | 512×512 | Paredes e elementos de construção |
| TX Plant with Shadow.png | 512×512 | Variante extra de vegetação com sombra |
| TX Props with Shadow.png | 512×512 | Variante extra de objetos com sombra |
| Scene Overview.png | 1200×1200 | Visão geral da cena demonstrativa; referência de composição, não mapa definitivo |

As dimensões acima são dimensões dos arquivos de textura, não uma definição de tamanho universal de sprite ou tile. O GDD mantém 32×32 px como referência de organização, não como tamanho obrigatório de todos os recursos.

## 4. Prefabs por grupo

### Vegetação — 25
- 2 árvores.
- 6 arbustos/variações de arbusto, incluindo o grupo Bush 01–06.
- 1 flor.
- 16 variações de grama.

### Objetos e estruturas — 52
O conjunto inclui altar, barril, tijolos, baús aberto/fechado, caixas, lápides, pilares, vasos, placas, pilares rúnicos, estátua, pedras, bancos, caixões, cubo de pedra, lanterna, poço, portões e escadas em diferentes orientações/segmentos.

### Personagem demonstrativo — 1
- `PF Player.prefab`.
- Uso permitido no planejamento: estudar tecnicamente exemplos gerais de animação e movimento.
- Uso proibido no projeto: protagonista, personagem jogável ou base direta para o protagonista original.

## 5. Scripts existentes no pacote

Os scripts abaixo são conteúdo original de terceiros e devem ser preservados. A presença deles no pacote não significa aprovação automática para uso na arquitetura de CODEXIA.

| Script | Responsabilidade aparente |
|---|---|
| CameraFollow.cs | Seguir um alvo com interpolação |
| TopDownCharacterController.cs | Movimento top-down com teclado, Animator e Rigidbody2D |
| SpriteColorAnimation.cs | Animação de cor de SpriteRenderer por Gradient |
| StairsLayerTrigger.cs | Alterar layer e sorting layer ao atravessar escadas |
| PropsAltar.cs | Alterar a transparência de runas ao entrar/sair de um trigger |

Qualquer reutilização, extensão ou substituição deverá ser planejada separadamente, sem editar os arquivos originais do pacote.

## 6. Cenas e demonstrações existentes

- `SC Demo.unity`: cena demonstrativa geral do pacote.
- `SC All Props.unity`: cena demonstrativa de objetos.
- `Scene Overview.png`: imagem de visão geral disponibilizada no ZIP.

Essas cenas servem para inspeção dos recursos e entendimento de como o autor organizou os exemplos. Não são, por si só, a cena inicial de CODEXIA.

## 7. Configurações e compatibilidade

O pacote inclui 18 arquivos em `ProjectSettings/`. Eles pertencem ao projeto de origem e não devem ser copiados integralmente para o projeto de CODEXIA sem análise.

O changelog da versão 1.2.3 informa correções de shader para URP 2D no Unity 6.3. Versões anteriores também mencionam suporte a URP 2D no Unity 6. Isso é evidência útil para a avaliação de compatibilidade, mas não define sozinho a versão final da Unity de CODEXIA.

Nas texturas inspecionadas, os metadados do Unity indicam:
- `filterMode: 0` (Point).
- `spriteMode: 2` (Multiple).
- `alphaIsTransparency: 1`.
- `textureType: 8` (Sprite).

Os metadados originais devem ser mantidos. A configuração final de importação deverá ser validada no editor Unity sem sobrescrever os arquivos-fonte do pacote.

## 8. Diretrizes de uso em CODEXIA

1. Usar este pacote como referência visual principal, conforme o GDD.
2. Antes de montar cada área, procurar primeiro no catálogo e nos prefabs existentes o recurso adequado.
3. Não gerar arte nova para preencher lacunas.
4. Não substituir recursos adequados por formas geométricas ou placeholders.
5. Não alterar, recolorir, redesenhar, reestilizar ou sobrescrever arquivos originais.
6. Quando for necessário adaptar algo, manter o original intacto e planejar a adaptação como recurso separado, sujeita à aprovação.
7. Não usar o personagem demonstrativo como protagonista ou personagem jogável.
8. Usar as cenas de demonstração para inspeção, não como mapa final.
9. Verificar a licença e as condições de uso do pacote antes de qualquer distribuição do jogo.

## 9. Recursos ainda não localizados

O GDD cita **Top Down Kingdom 2D** como referência complementar para a água. Esse pacote não foi encontrado entre os arquivos disponíveis na Biblioteca consultada nesta etapa. Portanto, nenhum recurso de água desse pacote foi incluído ou avaliado neste catálogo.

## 9.1 Achados da importação no Unity (12/11/2026)

- **Pacotes internos não catalogados na v0.1:** `Patch - URP 2D Lit - Unity 6.0 to 6.2.unitypackage`, `Patch - URP 2D Lit - Unity 6.3.unitypackage` e `Patch - URP 2D Lit.unitypackage`. Foram importados como arquivos e **não foram aplicados**.
- **Dependência de configurações:** os prefabs e cenas usam as Sorting Layers Layer 1, 2 e 3 e as camadas de física Layer 1, 2 e 3 (índices 20–22), definidas no `TagManager.asset` do projeto de origem.
- **PPU 32** em todas as texturas; pivô central nos tiles e na base em objetos e vegetação.
- **ZIP atual diferente do catalogado:** 3.447.959 bytes (antes 2.622.004). Foi incluído `Texture/TX waterreference.jpg` (imagem de referência de água, 1077×1065). O `.unitypackage` interno não mudou.
- Detalhes no [[Relatório de Validação Técnica]].

## 10. Próximas verificações

- Importar o pacote em projeto Unity de teste, preservando a cópia original.
- Verificar visualmente as cenas e prefabs no editor.
- Confirmar pixels por unidade, pivôs, sorting layers, colisores e configurações de animação.
- Verificar a compatibilidade com a versão Unity/URP que vier a ser aprovada.
- Completar o inventário com capturas e notas de uso após inspeção no editor.

## 11. Histórico

- **0.1:** inventário inicial realizado a partir do ZIP original, em modo somente leitura.
- **0.2:** registro dos achados da importação no Unity 6.3: patches URP 2D Lit, dependência de Sorting Layers e camadas, PPU e divergência do ZIP.
