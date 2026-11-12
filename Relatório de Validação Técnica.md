---
tipo: documento
categoria: validacao-tecnica
projeto: CODEXIA
status: em-andamento
versao: 0.2
idioma: pt-BR
tags:
  - codexia
  - unity
  - urp-2d
  - validacao
  - assets
---

# CODEXIA — Relatório de Validação Técnica

> Registro do que foi **executado e verificado** na preparação do ambiente (12/11/2026). Itens não testados estão marcados como pendentes. Nenhum valor aqui é decisão de design; as decisões pendentes estão na seção 9.

## 1. Ambiente verificado

| Item | Resultado | Como foi verificado |
|---|---|---|
| Sistema | Windows 64 bits | Informações do dispositivo |
| Unity Hub | Instalado (versão da Microsoft Store) | Aberto e operado |
| Unity 6.6 (6000.6.7f1) | Já existia; **preservada**, não usada no CODEXIA | Lista de instalações do Hub |
| **Unity 6.3 LTS (6000.3.28f1)** | **Instalada** em `C:\Program Files\Unity\Hub\Editor\6000.3.28f1` | Hub mostra "Instalação concluída" |
| Módulos | Editor + Windows Build Support (Mono, padrão) + Microsoft Visual Studio Community 2026 | Tela de módulos do Hub |
| Módulos não instalados | Android, iOS, Web e IL2CPP (desnecessários nesta etapa) | Tela de módulos do Hub |
| Licença | Unity Personal ativa | Console do editor |
| Espaço livre em C: antes da instalação | 71,43 GB | Tela de módulos do Hub |

## 2. Projeto CODEXIA

| Item | Resultado |
|---|---|
| Local | `V:\Programação\CODEXIA Unity\CODEXIA` (fora do cofre do Obsidian) |
| Template | **Universal 2D** (nome do template 2D URP na Unity 6) |
| Tipo | Projeto local (sem vínculo com a Unity Cloud); AI Assistant desativado |
| Versão gravada | `ProjectVersion.txt`: 6000.3.28f1 |
| URP | `com.unity.render-pipelines.universal` 17.3.0 |
| Pipeline ativo | `UniversalRP.asset` atribuído aos 6 níveis de qualidade |
| Renderer | Renderer padrão (índice 0) = `Renderer2D.asset` (Renderer 2D) ✅ |
| Espaço de cor | Linear |
| Entrada | Somente o novo Input System (`com.unity.inputsystem` 1.20.1) |
| Abertura no editor | Abriu sem erros (0 erros, 0 avisos antes da importação) |

## 3. Cópia de segurança

- Pasta: `V:\Programação\CODEXIA Backups\`.
- `Pixel Art Top Down - Basic v1.2.3.zip`: cópia idêntica, conferida por SHA-256 (`a6a4c5ce…0fb2`).
- `Extraido\Pixel Art Top Down - Basic v1.2.3.unitypackage`: cópia do pacote Unity interno (SHA-256 `3293586c…6367`), usada na importação.
- `LEIA-ME backup.txt` com origem, tamanhos e hashes.

### Divergência encontrada no ZIP

O ZIP atual tem **3.447.959 bytes**; o catalogado tinha **2.622.004 bytes**. A diferença é o arquivo `Texture/TX waterreference.jpg` (1077×1065, JPG), adicionado dentro do ZIP em 12/11/2026 às 09:10. É uma imagem de referência de água, não um asset do pacote. O `.unitypackage` interno tem os mesmos 330 caminhos registrados no catálogo.

## 4. Importação do pacote

- Importado: todo o conteúdo de `Assets/Cainos/Pixel Art Top Down - Basic/` (311 arquivos e pastas, conferidos contra a lista do pacote).
- **Não importado:** os 18 arquivos de `ProjectSettings/` do projeto de origem (todos marcados como "Override" pela Unity e desmarcados).
- **Recusado:** o Script Updater da Unity, que queria reescrever `TopDownCharacterController.cs` (arquivo original).
- Resultado no Console: **0 erros, 1 aviso**: `TopDownCharacterController.cs(47,13): warning CS0618: 'Rigidbody2D.velocity' is obsolete`.
- Integridade: 44 arquivos conferidos byte a byte contra o pacote (9 texturas, 4 materiais, 2 shaders, 5 scripts, 2 cenas e seus `.meta`): **todos idênticos**.
- `TagManager.asset` e `GraphicsSettings.asset` do CODEXIA não foram alterados pela importação.
- A cena `SC Demo` foi aberta só para inspeção e fechada sem salvar (data do arquivo inalterada).

### Conteúdo não registrado no catálogo

O pacote contém três pacotes internos de correção, importados como arquivos e **não aplicados**:
- `Patch - URP 2D Lit - Unity 6.0 to 6.2.unitypackage`
- `Patch - URP 2D Lit - Unity 6.3.unitypackage`
- `Patch - URP 2D Lit.unitypackage`

## 5. Dados técnicos dos assets

### 5.1 Escala e Pixels Per Unit
- As 9 texturas usam **PPU 32** (1 unidade Unity = 32 px), o que é coerente com a grade de referência 32×32 do GDD.
- Filtro **Point**, mipmaps desligados, tipo Sprite, modo Multiple, tamanho máximo 2048.
- Compressão: **ativada** (`textureCompression: 1`, Normal Quality). Possível risco para pixel art: verificar visualmente se há artefatos de cor. Não foi alterada.

### 5.2 Pivôs (por sprite recortado)
| Textura | Centro | Base-centro | Personalizado |
|---|---:|---:|---:|
| TX Tileset Grass | 64 | — | — |
| TX Tileset Stone Ground | 50 | — | — |
| TX Tileset Wall | 61 | — | — |
| TX Props | 4 | 33 | 11 |
| TX Plant | — | 23 | 5 |
| TX Struct | 2 | 20 | 8 |
| TX Shadow | 5 | 33 | 5 |
| TX Shadow Plant | — | 9 | — |
| TX Player (demo) | — | 4 | — |

Tiles usam pivô central; objetos e vegetação usam principalmente a base, o que é adequado para ordenação por profundidade em top-down.

### 5.3 Sorting Layers e camadas de física
- O pacote depende das Sorting Layers **Default, Layer 1, Layer 2 e Layer 3**, definidas no `TagManager.asset` de origem (não importado).
- Referências encontradas em prefabs e cenas: Layer 1 = 157, Layer 2 = 8, Layer 3 = 2, Default = 3.
- O pacote também usa as camadas de física **Layer 1, Layer 2 e Layer 3** (índices 20, 21 e 22), usadas pelo `StairsLayerTrigger.cs` para escadas e andares.
- No CODEXIA essas camadas **não existem**. Verificado no Inspector: `PF Plant - Tree 01` mostra o campo Layer vazio.
- Consequência esperada: objetos caem na ordem Default e a lógica de andares/escadas do pacote não funciona. Na Scene View, a SC Demo aparece visualmente parecida com o `Scene Overview.png`, mas a ordenação **ainda não foi validada** no Game View. **Decisão pendente** (seção 9).

### 5.4 Colisores (81 prefabs)
- 57 Box Collider 2D, 15 Polygon Collider 2D, 10 Edge Collider 2D e 8 Rigidbody 2D.
- Exemplo verificado: `PF Plant - Tree 01` tem um Box Collider 2D pequeno só na base do tronco (offset 0,01/0,12; tamanho 0,27×0,19).

### 5.5 Câmera
- As duas cenas demo usam câmera **ortográfica, tamanho 5**. Com PPU 32, isso mostra 10 unidades de altura (320 px de arte).
- Não usam Pixel Perfect Camera. Usam o script `CameraFollow.cs` do pacote.
- A câmera do CODEXIA não foi configurada (depende de decisão de escala e resolução).

### 5.6 Materiais e shaders com URP 2D
- 4 materiais usam 2 shaders próprios do pacote: `Sprite Default - Shadow Mask` (MT Props, MT Tileset, MT FX Rune Glow) e `Sprite Default - Shadow` (MT Shadow).
- Os shaders são escritos em CG (estilo Built-in).
- Na SC Demo, com URP 2D, todos renderizaram **sem erro e sem material rosa**.
- Não testado: resposta às Luzes 2D (Global Light 2D/Light 2D). O pacote oferece patches "URP 2D Lit" para isso, mas aplicá-los altera materiais e shaders do pacote. **Decisão pendente.**

### 5.7 Scripts do pacote
- `TopDownCharacterController.cs` usa `Input.GetKey` (Input antigo). O CODEXIA usa só o novo Input System, então esse script deve gerar erro em Play Mode. **Não testado** (o personagem demo não será usado).
- O aviso CS0618 também vem desse script.

### 5.8 Desempenho
- Texturas pequenas (atlas de até 512×512) com filtro Point e sem mipmaps: custo de memória baixo.
- `SC Demo.unity` tem cerca de 1,2 MB, com muitos SpriteRenderers individuais.
- **Nada foi medido ainda** (Profiler, FPS e draw calls ficam para a cena de validação).

## 6. Água

O único material de água recebido nesta etapa é `TX waterreference.jpg`, uma **imagem de referência** (captura de tela), não um pacote de assets. Ela não pode ser usada como tile ou sprite. O pacote Top Down Kingdom 2D continua pendente.

## 7. Concluído × pendente

**Concluído e verificado:** instalação da Unity 6.3 LTS; criação do projeto Universal 2D; confirmação do URP com Renderer 2D; abertura sem erros; backup com hash; importação sem ProjectSettings; integridade dos arquivos; inspeção da SC Demo; levantamento de PPU, pivôs, sorting, colisores, câmera e shaders.

**Pendente:** inspeção da `SC All Props`; validação da ordenação no Game View; versão exata do Unity Hub; decisões da seção 9; cena de validação própria; medições de desempenho; executável de teste.

## 8. Arquivos criados fora do cofre

- `V:\Programação\CODEXIA Backups\` (backup e LEIA-ME).
- `V:\Programação\CODEXIA Unity\LEIA-ME.txt` e o projeto `CODEXIA\`.

## 9. Decisões pendentes (aguardando aprovação)

1. ~~Como resolver as Sorting Layers e camadas de física do pacote.~~ **Resolvido** (seção 11.1).
2. Aplicar ou não o patch URP 2D Lit para Unity 6.3. **Não aplicado; continua pendente.**
3. Compressão de texturas e uso de Pixel Perfect Camera (após teste visual).
4. ~~Conteúdo do primeiro executável de teste.~~ **Gerado** (seção 11.3).
5. Destino do ZIP de assets que está dentro do cofre (sincronizado com o GitHub). Aprovado retirá-lo; ver seção 11.5.
6. **Novo:** confirmar a ordenação por profundidade adotada no Renderer 2D (seção 11.2).
7. **Novo:** escala de câmera definitiva (o executável usa valor provisório; seção 11.3).

## 11. Etapa 2 — camadas, área inicial e executável (12/11/2026)

### 11.1 Camadas (aprovado)
- Importado do pacote **somente** `ProjectSettings/TagManager.asset`. Os outros 17 arquivos continuaram desmarcados.
- Conferido byte a byte: idêntico ao do pacote. Sorting Layers Default, Layer 1, 2 e 3, e camadas de física Layer 1, 2 e 3 (índices 20–22).
- O pacote também traz o preset oficial `Preset/Tags & Layers - Pixel Art Top Down - Basic.preset`, o que confirma que o autor espera essas camadas.

### 11.2 Ordenação por profundidade (aplicada, aguardando confirmação)
- Os assets dependem de **Transparency Sort Mode = Custom Axis (0, 1, 1)**, valor usado no projeto de origem. Isso desenha na frente o que está mais abaixo na tela e usa o eixo Z para empurrar as sombras para trás.
- No CODEXIA o valor era Default. Foi alterado em `Assets/Settings/Renderer2D.asset` (arquivo próprio do CODEXIA, não do pacote). São só duas linhas, e a mudança é reversível.
- Antes: `m_TransparencySortMode: 0`, `m_TransparencySortAxis: {x: 0, y: 1, z: 0}`. Depois: `3` e `{x: 0, y: 1, z: 1}`.

### 11.3 Cena de validação "Área Inicial"
- Arquivo: `Assets/CODEXIA/Scenes/Validation/Validacao - Area Inicial.unity` (primeira pasta própria do CODEXIA, criada por ter conteúdo real).
- Construída **somente** com tiles e prefabs originais do pacote. Nenhum asset foi modificado, recolorido ou criado, e nenhum script próprio foi escrito.
- Estrutura: `AREA INICIAL` → `Grid` → Tilemap `Layer 1 - Chao` (1.296 tiles, Sorting Layer 1, material MT Tileset, modo Chunk), e `Objetos` com 227 instâncias de prefabs.
- Composição manual: estrada leste–oeste calçada; praça com poço, lanternas de pedra e bancos; caminho ao norte até um santuário (estátua e pilares); placas de direção; depósito com caixas, barris, potes e baú; pedras a oeste; floresta nas bordas.
- Composição procedural controlada (semente fixa 1312): 34 arbustos e 110 tufos de grama/flor, com distância mínima entre objetos e sem invadir o calçamento; variações de grama e flores no chão (5% de flores).
- Regras de coerência verificadas: nada dentro do calçamento; os colisores dos props dinâmicos (caixas, barris, potes) não se sobrepõem (0 conflitos), para a física não empurrá-los ao iniciar.
- O calçamento usa as bordas e cantos do próprio tileset de grama. Nos cantos internos foram usados apenas os tiles existentes (`Grass_60` e `Grass_61`), sem espelhar tiles.
- Câmera **provisória**: ortográfica, tamanho 8,4375, posição (0; 0,6). Em 1920×1080 isso dá escala inteira de 2× (30×16,9 unidades visíveis). Não é decisão de design.
- Testes no editor: a cena abriu sem erros; Game View conferido; Play Mode com **0 erros e 0 avisos** e nenhum prop deslocado.

### 11.4 Executável de teste
- Build Windows (64 bits) concluído com resultado **Succeeded** em 75 s.
- Pasta: `V:\Programação\CODEXIA Unity\Builds\Teste 01 - Area Inicial\` → `CODEXIA.exe`, com LEIA-ME de uso.
- Lista de cenas do build: somente a cena de validação (a SampleScene ficou desativada, não removida).
- O Script Updater foi recusado de novo durante o build (script original preservado).
- **Não testado por mim:** a execução do `.exe` fora do editor. O teste final fica com o usuário. O jogo abre em tela cheia e fecha com Alt+F4.

### 11.5 ZIP no cofre
- Criado `.gitignore` no cofre para impedir que o ZIP volte a ser sincronizado.
- O ZIP **já foi enviado ao GitHub** (commit de 12/11/2026 09:12) e continua no histórico do repositório. Retirá-lo do histórico exige uma limpeza à parte.
- A remoção do arquivo do cofre depende do usuário (a ferramenta usada não apaga arquivos). O backup verificado está em `V:\Programação\CODEXIA Backups\`.

## 10. Histórico (do documento)

- **0.1:** primeiro registro da preparação do ambiente, importação e validação técnica inicial.
- **0.2:** camadas do pacote importadas; ordenação por profundidade ajustada; cena "Área Inicial" e primeiro executável de teste.
