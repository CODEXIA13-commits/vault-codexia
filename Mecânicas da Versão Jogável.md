---
tipo: documento
categoria: game-design
projeto: CODEXIA
status: em-revisao
versao: 0.2
idioma: pt-BR
tags:
  - codexia
  - unity
  - versao-jogavel
---

> Documento gerado na preparação da versão jogável (12/11/2026) a partir do trabalho dos subagentes (referências e mecânicas). Relacionado: [[CODEXIA - GDD]], [[Diretrizes de Arquitetura e Padrões C#]], [[Relatório de Validação Técnica]]. Aguarda revisão do usuário.

# CODEXIA: mecânicas da próxima versão jogável (v0.2, proposta)

Escopo: área inicial (`Validacao - Area Inicial`), personagem demo provisório, sem arte nova.
Unidades: 1 u = 32 px (PPU 32). O personagem demo tem sprite de 0,656 × 1,4 u e collider de pés BoxCollider2D 0,587 × 0,365 u (offset y 0,213).
Todos os valores abaixo vão para **ScriptableObjects de configuração** (somente leitura em runtime). O estado de runtime fica em classes C# simples ([e-book de ScriptableObjects, edição Unity 6](https://unity.com/resources/create-modular-game-architecture-scriptableobjects-unity-6)).
O Core publica eventos C# (`event Action<...>`) e a UI só escuta, como no [observer pattern da Unity Learn (Unity 6)](https://learn.unity.com/course/design-patterns-unity-6/tutorial/create-modular-and-maintainable-code-with-the-observer-pattern). Vale o mesmo para o [e-book de design patterns e SOLID](https://unity.com/resources/design-patterns-solid-ebook).

SOs propostos: `PlayerMovementConfig`, `HealthConfig`, `InventoryConfig`, `ItemDefinition` (1 por item), `DialogueDefinition` (1 por objeto), `HudConfig` (cores e tempos).

---

## 1. Personagem e movimentação

**Referências.** Em Zelda: A Link to the Past/Minish Cap e em Stardew Valley, a entrada tem 8 direções e o sprite tem 4. Na diagonal, o personagem mantém a direção para a qual já estava virado. Hyper Light Drifter e CrossCode mostram que "responsivo" significa aceleração quase instantânea com uma pequena suavização na parada. A suavização tira o "tranco" sem criar inércia de gelo.

**Física (Unity 6.3).**
- Manter o `Rigidbody2D` **Dynamic** do prefab: massa 1, Interpolate, Continuous, rotação Z travada e gravityScale 0 (já é 0 no prefab, conferido no YAML).
- Escrever `linearVelocity` em `FixedUpdate`. A doc de [`Rigidbody2D.linearVelocity`](https://docs.unity3d.com/ScriptReference/Rigidbody2D-linearVelocity.html) mostra esse padrão: ler a entrada em `Update` e aplicar em `FixedUpdate`.
- **Não usar `MovePosition`.** A [doc de `MovePosition`](https://docs.unity.com/en-us/engine/6000.3/script-reference/unityengine/rigidbody2d/moveposition) diz que ele "is intended for use with kinematic rigidbodies". Um corpo Kinematic não é barrado por colliders estáticos sem resolução manual.
- Com Dynamic + velocidade, o jogador colide com estátua, poço e árvores e empurra os potes, caixas e barris dinâmicos do pacote, no estilo de Minish Cap.
- Remover o `TopDownCharacterController` **na instância** da cena (m_RemovedComponents). Ele usa o `Input` antigo e `.velocity`.

**Comportamento.**
- `targetVel = input * MaxSpeed`. Depois, `linearVelocity = Vector2.MoveTowards(linearVelocity, targetVel, rate * fixedDeltaTime)`.
- `rate` vale `Acceleration` quando há entrada e `Deceleration` quando a entrada é zero.
- Diagonal: no teclado, o composite `2DVector` em modo `DigitalNormalized` (o padrão) já normaliza ([Input Bindings 1.19](https://docs.unity.cn/Packages/com.unity.inputsystem%401.19/manual/ActionBindings.html)). No gamepad, aplicar `Vector2.ClampMagnitude(input, 1)`. A intensidade analógica é mantida (andar devagar com o stick meio inclinado). O deadzone padrão do stick (0,125–0,925) basta.
- **Direção do sprite (4):**
  - Se |x| > |y| + 0,1, a direção é horizontal.
  - Se |y| > |x| + 0,1, a direção é vertical.
  - Caso contrário (diagonal pura), manter a direção atual se ela for um dos dois eixos pressionados; se não for, usar a horizontal.
  - A histerese de 0,1 evita a troca nervosa no stick. A direção não muda sem entrada.
- **Animator do pacote (`AC Player`):** `Direction` é int (0 = S, 1 = N, 2 = E, 3 = W, mapeamento do script original) e `IsMoving` é bool (verdadeiro quando input.magnitude > 0,1).
  - Os clipes de direção têm 1 quadro de sprite cada.
  - A camada Movement anima só `SpriteRenderer.size.y` (balanço de 1,35 a 1,45). Por isso, o flash de dano por `color` não conflita.
  - Um componente-ponte (`CharacterAnimatorBridge`) com os nomes dos parâmetros serializados isola o visual. Trocar o protagonista significa trocar a ponte ou o controller, sem mexer no motor.
- **Limites do mapa:** criar um GameObject `Limites` com `EdgeCollider2D` em loop no retângulo (−27, −12)–(27, 12). Ele impede sair pelas pontas da estrada.

| Parâmetro (`PlayerMovementConfig`) | Valor inicial | Nota |
|---|---|---|
| MaxSpeed | **4,5 u/s** (144 px/s) | O demo usa 3, que é lento. Cruza a tela de 30 u em cerca de 6,7 s. Faixa de ajuste: 4–5. |
| Acceleration | **60 u/s²** | Atinge a velocidade máxima em cerca de 0,075 s (≈ 4–5 frames). |
| Deceleration | **80 u/s²** | Para em cerca de 0,056 s. |
| MovingThreshold | 0,1 | Para `IsMoving`. |
| FacingHysteresis | 0,1 | |
| Spawn | (−0,5; −2,6) | Na estrada, ao sul do poço. Validar com `preview.py`. |

## 2. Vida e barra de vida

**Referências.**
- ALttP: ao sofrer dano por contato, Link fica invulnerável por 58 frames (≈ 0,97 s). Ele sofre recuo e fica sem controle por 19 frames (≈ 0,32 s), e o sprite pisca ([análise da RAM de ALttP](https://gamertagmythras.com/blog/zelda-a-link-to-the-past/alttp-invincibility-frames)).
- Encurtamos o tempo sem controle para a sensação mais moderna de HLD/CrossCode. A barra com "rastro" de dano atrasado é um padrão comum de action RPG.
- O poço funciona como a fonte de fadas de ALttP, que restaura a vida inteira.

**Comportamento (`Health`, Core).**
- HP é inteiro. A API é `TakeDamage(int, Vector2 sourcePos)` e `Heal(int)`.
- Eventos: `Changed(cur, max)`, `Damaged(amount, dir)`, `Healed(amount)`, `Died`.
- Dano durante a invencibilidade é ignorado.
- Morte (HP 0): trava a entrada, mostra o toast "Você desmaiou…" e, após 1,5 s, reaparece no Spawn com a vida cheia e 2 s de invencibilidade. O inventário é mantido.
- A câmera teleporta junto com o jogador.

| Parâmetro (`HealthConfig`) | Valor |
|---|---|
| MaxHP / HP inicial | **10 / 10** |
| Invencibilidade após dano | **1,0 s** (próximo dos 0,97 s de ALttP) |
| Knockback | velocidade **7 u/s** na direção oposta à fonte, decaindo linearmente a zero em **0,15 s**. A entrada fica travada por esses 0,15 s (ALttP usa 0,32 s). |
| Flash de dano | Tinta vermelha `(1; 0,35; 0,35)` por **0,1 s**. Depois, alterna o alfa entre 1 e 0,35 a cada **0,08 s** até o fim da invencibilidade. ALttP pisca a 30 Hz; aqui são ~6 Hz, sem apagar o sprite, por conforto visual. |
| Reaparecer | 1,5 s de espera e 2,0 s de invencibilidade |

**Barra (HUD).**
- Fica no canto superior esquerdo, com 120 × 10 px de referência (canvas 640×360) e margem de 8 px.
- Camadas: fundo `#1A1A1A`, rastro `#E8D9A0` e preenchimento.
- O preenchimento é verde `#5BC25B` acima de 50%, amarelo `#E0B83A` entre 25 e 50% e vermelho `#D94A4A` abaixo de 25%.
- O texto "7/10" fica à direita, com tamanho 9.
- O rastro espera 0,35 s e desce em 0,3 s. Ao receber dano, a barra treme ±2 px por 0,15 s.

## 3. Fontes de dano e cura (somente props do pacote)

- **Perigo: `PF Props - Rune Pillar Broken`.** Esse prop não está na cena hoje. Ele tem collider físico e um filho `Glow` com runas ciano.
  - Colocar 2 instâncias nas "ruínas" a oeste, perto das pedras: posições sugeridas (−6,5; 4,0) e (−14,5; 1,5), a validar no preview.
  - Override: adicionar um `CircleCollider2D` isTrigger com raio **0,8 u** (centro +0,25 em y) e o componente `DamageZone`. O dano é **2 HP** ao entrar e **reaplicado enquanto o jogador permanecer**, limitado pela invencibilidade de 1 s.
  - Opcional: adicionar à instância do `Glow` o `SpriteColorAnimation` do pacote, como componente sem edição do script. Usar o mesmo gradiente do `Rune Pillar X2` para o brilho pulsar e sinalizar o perigo.
- **Cura: `PF Props - Well 01`** em (−0,5; −0,6). Interagir abre o diálogo. A última fala aplica a **cura total** e mostra o toast "+N Vida" em verde. Com a vida cheia, o diálogo usa a variante "você já está bem". Sem cooldown (não há economia a explorar).
- **Item de cura:** "Pote de água" (seção 4) cura 3 HP.

## 4. Inventário e itens

**Referências.**
- Stardew Valley começa com 12 slots, organizados em linhas de 12. A linha superior é a barra de atalhos (toolbar), e as mochilas expandem para 24 e 36 slots ([wiki de Stardew: Inventory](https://stardewvalleywiki.com:443/Inventory)).
- Escolhemos 9 por linha para casar com as teclas 1–9, como na hotbar de Minecraft.
- Regra de adição de Stardew/Minecraft: completar primeiro as pilhas existentes e depois ocupar o primeiro slot vazio.
- ~~A coleta por contato segue as rúpias de Zelda e os itens soltos de Stardew.~~ **Alterado em 12/11/2026 (pedido do usuário):** a coleta só acontece quando o jogador aperta **Enter** perto do item (gatilho direito no gamepad). Até lá, o item fica **flutuando** no chão. O baú continua precisando de interação.

**Comportamento.**
- `Inventory` (Core, classe C# pura) guarda `ItemStack[18]`: os slots 0–8 são a hotbar e os slots 9–17 são a mochila.
- API: `TryAdd(def, qty, out leftover)`, `Remove(slot, qty)`, `Use(slot)`, `SelectedHotbarIndex`.
- Eventos: `SlotChanged(i)`, `SelectionChanged(i)`, `ItemAdded(def, qty)`, `AddFailedFull(def)`.
- Coleta: o `Pickup` (componente adicionado à instância do prop) tem `ItemDefinition` + quantidade e um `CircleCollider2D` isTrigger de raio **0,35 u**.
  - Em `OnTriggerEnter2D`/`OnTriggerExit2D`, o item se registra ou sai do `PlayerCollector` do jogador. O `PlayerCollector` é criado automaticamente em cenas antigas.
  - O prompt "[Enter] Coletar <item>" aparece quando não há outro alvo de interação. **Enter** coleta o item mais próximo; nada é coletado com diálogo ou painel aberto, nem com o jogador desmaiado.
  - O item flutua 2 pixels para cima e para baixo a cada 1,6 s, arredondado ao pixel, cada um num tempo diferente.
  - Se tudo couber, o prop é desativado. Se não, sobra a quantidade restante e aparece o toast "Inventário cheio" (no máximo 1 a cada 2 s).
  - Os props coletáveis não têm collider sólido, então o raio efetivo é ≈ 0,35 u + meia largura dos pés ≈ 0,65 u.
- Usar item: tecla F ou botão Oeste usa o slot selecionado da hotbar; dentro do painel, usa o slot sob o cursor. Itens sem efeito mostram o toast "Não é possível usar isso agora." O Pote de água com a vida cheia não é consumido (toast "Sua vida já está cheia").
- Painel (I/Tab):
  - Mostra 2 linhas × 9.
  - O cursor anda com Move (digital, repetição de 0,2 s).
  - Abaixo do painel aparecem o nome e a descrição do item.
  - Enquanto o painel está aberto, o movimento fica bloqueado (a velocidade desacelera a 0), mas `Time.timeScale` não é alterado.

| Item (`ItemDefinition`) | Descrição curta | Empilha / máx. | Ícone (sprite do pacote) | No mundo |
|---|---|---|---|---|
| **Flor do campo** | "Uma flor pequena e perfumada." | sim / 20 | `TX Plant - Flower 01` (6×5 px) | As 13 instâncias de `PF Plant - Flower 01` já na cena. Todas coletáveis, por consistência. |
| **Pedrinha** | "Lisa e redonda. Cabe no bolso." | sim / 20 | `TX Props - Stone 01` (11×10) | `PF Props - Stone 01` em (−11,2; 2,7) + 2 novas instâncias perto das ruínas (sem collider no prefab, ideal). |
| **Tijolo antigo** | "Um tijolo gasto pelo tempo." | sim / 10 | `TX Props - Brick 01` (18×16) | 3 novas `PF Props - Brick 01` nas ruínas, por exemplo (−7,2; 3,2), (−13,6; 0,8) e (−15,5; 2,6). |
| **Pote de água** | "Água fresca. Recupera 3 de vida." | sim / 5 | `TX Props Pot A` (21×34) | Só no baú (×2). Consumível: `Heal(3)`. |
| **Fragmento rúnico** | "Um pedaço de pedra com runas que ainda brilham." | não / 1 | `TX Props Rune Pillar Broken Glow` (5×12, ciano) | Só no baú (×1). Item-chave sem uso nesta versão. |

**Baú** (`PF Props - Chest 01` em (10,6; 2,6)):
- Pré-instanciar `PF Props - Chest 01 Open` na mesma posição, inativo.
- Ao interagir pela primeira vez, mostrar o diálogo, adicionar o conteúdo (2 Potes de água e 1 Fragmento rúnico), desativar o fechado e ativar o aberto.
- O objeto interativo deve ser um pai comum, ou o componente fica no fechado e transfere o estado.
- Se o inventário estiver cheio, o baú não abre (toast).
- O estado não persiste entre sessões.

**Pontos de atenção.** Os ícones vêm do campo `Sprite` do SO, que referencia o sprite do pacote por guid + fileID (só referência, sem modificar nada). Os ícones em UI usam `preserveAspect`. O pote (34 px) é reduzido para caber no slot.

## 5. HUD

**Layout (referência 640×360).**
- Superior esquerdo: barra de vida (seção 2).
- Inferior centro: hotbar de 9 slots de **28×28 px**, com gap de 2 px (largura total de 268 px) e margem inferior de 6 px.
  - Fundo do slot: `#000000` com α 0,55. Ícone em até 22×22 px.
  - Quantidade no canto inferior direito, tamanho 8, só se for > 1. Número da tecla no canto superior esquerdo, tamanho 7, com α 0,6.
  - Slot selecionado: moldura `#F2D16B` de 2 px (Image maior atrás).
- Painel de inventário: centro da tela, 2×9 slots, fundo α 0,8, título "Inventário" e área de descrição.
- Toasts: canto superior direito, empilhados para baixo.
  - Máximo de **4** visíveis. Duração de **2,5 s** + fade de **0,35 s** (CanvasGroup), em tempo `unscaled`.
  - Coletas do mesmo item em menos de 2 s se fundem ("+3 Flor do campo").
  - Cores: coleta branco, dano `#FF6B6B` ("−2 Vida"), cura `#7CFF7C`, informação `#F2D16B`.
- Feedback de dano: Image em tela cheia vermelha com α **0,22 → 0 em 0,25 s**, mais o tremor da barra.
- Prompt de interação:
  - Fica acima da hotbar e é exibido quando há alvo e nenhum diálogo ou painel aberto.
  - Texto: `[E] Falar` / `[E] Ler` / `[E] Abrir` / `[E] Beber`, com o verbo vindo do `IInteractable`.
  - Mostra `[A]` se o último dispositivo usado foi um gamepad (`context.control.device is Gamepad`).
- Caixa de diálogo: inferior, com 560×74 px de referência, fundo `#101018` com α 0,9 e borda de 1 px `#F2D16B`.
  - Nome do falante com tamanho 10 em amarelo; texto com tamanho 10 e até 3 linhas.
  - Indicador `[E]` piscando a 2 Hz no canto.
  - Enquanto a caixa está aberta, a hotbar continua visível e o prompt some.

**Tecnologia recomendada: uGUI criado em runtime (assembly `Codexia.UI`).**
- `Canvas` em ScreenSpaceOverlay (sortingOrder 100) com `CanvasScaler` em ScaleWithScreenSize 640×360 e match 0,5. O resultado é escala inteira 2× em 720p e 3× em 1080p, o que deixa os ícones nítidos. A UI overlay não é afetada pelas Luzes 2D.
- `Image` com `sprite = null` desenha um retângulo de cor sólida, sem arte.
- `UnityEngine.UI.Text` (legado) com `Resources.GetBuiltinResource<Font>("LegacyRuntime.ttf")`, que inclui acentos PT-BR. Adicionar o componente `Outline` (preto, 1 px) para legibilidade.
  - Não usar `"Arial.ttf"`: desde o 2022.x ela lança `ArgumentException: Arial.ttf is no longer a valid built in font. Please use LegacyRuntime.ttf` ([relato com o erro, 2022.3](https://groups.google.com/g/utageuser/c/gSo5Hng4Nr8)).
- Cada widget (HealthBarView, HotbarView, InventoryPanelView, ToastView, PromptView, DialogueView) cria a própria subárvore em `Awake` e assina os eventos do Core em `OnEnable`/`OnDisable`. Não é preciso YAML de UI nem ligar fileIDs.

Riscos do uGUI e como mitigar:
1. **`Image.type = Filled` não funciona sem sprite.** Sem sprite, o `Image` desenha um quad simples. O preenchimento da barra deve ser feito com `anchorMax.x = ratio` num filho.
2. **Não criar `EventSystem` com `StandaloneInputModule`.** Ele lê o `Input` antigo e gera erro com o Input System exclusivo. A HUD desta versão não precisa de cliques, então nenhum EventSystem é necessário. No futuro, usar `InputSystemUIInputModule`.
3. **Evitar TextMeshPro.** Ele depende dos "TMP Essential Resources", importados pelo editor. Se faltarem, o texto não aparece.
4. Escala não inteira (ex.: 1366×768 → 2,13×) deixa os ícones levemente irregulares. É aceitável.
5. Glifos fora de Latin-1 (▼, ►) podem cair em fallback do sistema. Usar ASCII (`[E]`, `>`).

**Por que não UI Toolkit agora.**
- O `PanelSettings` pode ser criado via `ScriptableObject.CreateInstance`, mas precisa de um **theme `.tss`**.
- O tema padrão (`Assets/UI Toolkit/UnityThemes/UnityDefaultTheme.tss`) só é gerado quando se adiciona o primeiro UIDocument no editor, e ele importa `unity-theme://default` ([manual de TSS](https://docs.unity.com/en-us/engine/6000.6/manual/uitoolkits/uielements/uie-uss/uie-tss); [`PanelSettings.themeStyleSheet`](https://docs.unity3d.com/ScriptReference/UIElements.PanelSettings.html)).
- Sem tema, surgem avisos "No Theme Style Sheet set to PanelSettings" e os controles ficam sem estilo. O texto também depende de configurações de FontAsset/TextSettings.
- Gerar `.tss` + `.meta` + `PanelSettings.asset` à mão, sem editor para validar, tem mais pontos de falha que o uGUI. Reavaliar quando houver arte de UI.

## 6. Objetos que falam (interação e diálogo)

**Referências.** As placas e os baús de Zelda são lidos ou abertos de frente com um botão. Stardew e Eastward mostram um prompt contextual. ALttP e Stardew usam texto com efeito de máquina de escrever: apertar durante a digitação completa a fala, e apertar de novo avança.

**Comportamento.**
- **Detecção (`PlayerInteractor`):** ponto = `rb.position + (0; 0,2) + facing × 0,35`. Usar `Physics2D.OverlapCircle(ponto, 0,75, ContactFilter2D, List<Collider2D>)`, a versão sem alocação.
  - Para cada collider, buscar `IInteractable` com `GetComponentInParent`. Escolher o menor `Collider2D.ClosestPoint`.
  - Usa os colliders sólidos já existentes nos prefabs (estátua e poço com Polygon; placas e baú com Box), então não é preciso collider extra. A busca roda em `FixedUpdate`.
- `IInteractable`: `string Verb`, `bool CanInteract`, `Interact(InteractionContext)`. Implementações: `DialogueInteractable` (estátua, placas), `WellInteractable` (diálogo + cura) e `ChestInteractable`.
- **Diálogo (`DialogueRunner`, Core):**
  - Recebe um `DialogueDefinition` (falante + `string[]`). Eventos: `Started`, `LineShown(i, text)` e `Ended`.
  - Durante o diálogo, o movimento, o inventário e a hotbar ficam bloqueados.
  - O efeito de máquina de escrever vale **40 caracteres/s** em tempo unscaled. Apertar E/Espaço/Enter/A durante a digitação completa a fala; apertar de novo avança; na última fala, fecha.
  - **Bloqueio de 0,2 s** após fechar, para o mesmo toque não reabrir. Esc ou B fecha a qualquer momento.

**Roteiros (PROVISÓRIOS; tom neutro, sem lore definida).**
- **Estátua do santuário** (falante: "Estátua"), em (−0,5; 6,55):
  1. "Seja bem-vindo, viajante."
  2. "Esta praça é um lugar de descanso. Ninguém tem pressa aqui."
  3. "Se estiver ferido, a água do poço vai te fazer bem."
- **Poço** ("Poço"), em (−0,5; −0,6):
  1. "A água lá embaixo é fresca e limpa."
  2. "Você bebe um pouco e se sente renovado." (aplica a cura total)
  - Variante com a vida cheia: 1. "A água reflete o céu." 2. "Você já está bem. Volte quando precisar."
- **Placa oeste** ("Placa"), em (−8,0; −1,1):
  1. "Oeste: ruínas antigas."
  2. "Cuidado com os pilares de runas. Eles ainda guardam energia."
- **Placa leste** ("Placa"), em (7,0; −1,1):
  1. "Leste: depósito."
  2. "Caixas, barris e um velho baú. Alguém deixou tudo para trás."
- **Baú** ("Baú"), em (10,6; 2,6):
  - Fechado: 1. "O baú não está trancado." 2. "Dentro há dois potes de água e um fragmento que brilha." (adiciona os itens, abre e mostra os toasts)
  - Aberto: 1. "O baú está vazio."

## 7. Controles (Input System 1.20, ações definidas em código)

**Recomendação.** Criar uma classe `GameInput` (Core) que monta dois `InputActionMap`, "Gameplay" e "UI", com `new InputAction(...)`, `AddBinding` e `AddCompositeBinding`. Os mapas são habilitados e desabilitados conforme o contexto (diálogo ou painel), e a classe faz `Disable()` + `Dispose()` em `OnDestroy`.

**Justificativa.**
- A doc confirma que é possível "create and configure actions entirely in code, including assigning the bindings". Ela também diz que essas ações começam desabilitadas e exigem `Enable()` ([Actions, Input System 1.19](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.19/manual/Actions.html)).
- Ações em código não dependem de importar um `.inputactions` (JSON + classe gerada pelo editor) nem de editar as Project-wide Actions.
- O resultado compila e é revisável aqui, sem editor, e as diferenças ficam legíveis. O rebinding pela UI fica para depois.

| Ação (tipo) | Teclado/mouse | Gamepad |
|---|---|---|
| Move (Value, Vector2) | Composite `2DVector` WASD + outro com as setas (modo DigitalNormalized) | `<Gamepad>/leftStick`, `<Gamepad>/dpad` |
| Interact / Avançar (Button) | `E`, `Space`, `Enter` (Space e Enter só para avançar o diálogo, opcional) | `buttonSouth` (A) |
| Inventory (Button) | `I`, `Tab` | `buttonNorth` (Y) |
| UseItem (Button) | `F` | `buttonWest` (X) |
| Cancel (Button) | `Escape` | `buttonEast` (B) |
| Hotbar1..9 (Button) | `<Keyboard>/1` … `/9` (e `numpad1..9`) | — |
| HotbarNext/Prev (Button) | `<Mouse>/scroll/y` como Value (sinal) | `rightShoulder` / `leftShoulder` (RB/LB) |

Trecho de exemplo:
```csharp
_move = new InputAction("Move", InputActionType.Value, expectedControlType: "Vector2");
_move.AddCompositeBinding("2DVector").With("Up", "<Keyboard>/w").With("Down", "<Keyboard>/s")
     .With("Left", "<Keyboard>/a").With("Right", "<Keyboard>/d");
_move.AddBinding("<Gamepad>/leftStick");
```
No editor, a tecla Esc libera o cursor do Game view. Isso é inofensivo.

## 8. Câmera

**O `CameraFollow.cs` do pacote não é recomendado.** Lido o código:
- Faz `Vector3.Lerp(pos, alvo, lerpSpeed * Time.deltaTime)` em `Update`. Isso depende do framerate e roda antes do `LateUpdate`, o que pode gerar tremor com a interpolação.
- Não tem limite de bordas.
- Calcula o `offset` no `Start` a partir da posição relativa inicial. Se o jogador nascer longe de (0; 0,6), a câmera fica descentrada para sempre.
- Não tem como teleportar no respawn.

**Seguidor próprio (`CameraFollow2D`, Codexia.Core), ~40 linhas:**
- `LateUpdate`. Alvo = jogador + offset **(0; 0,5)**, centrado no tronco.
- Suavização exponencial independente de framerate: `pos = Lerp(pos, alvo, 1 − exp(−dt / τ))`, com **τ = 0,12 s**.
- Z fixo em −10. `SnapToTarget()` no início e no respawn.
- **Limitar às bordas: sim.**
  - meiaAltura = 8,4375; meiaLargura = 8,4375 × aspect, recalculada a cada frame para acompanhar o redimensionamento. Em 16:9, ≈ 15,0.
  - Faixa: x ∈ [−27 + meiaLargura, 27 − meiaLargura] = **[−12; 12]**; y ∈ [−12 + 8,4375, 12 − 8,4375] = **[−3,5625; 3,5625]**.
  - Se a meia-largura passar de 27 (aspect > 3,2), centralizar em x = 0.
  - Os limites vêm de um SO ou de campos `Rect`, para reaproveitar em outros mapas.
- Opcional: arredondar a posição final para 1/64 u, que equivale a 1 px de tela em 1080p. Isso reduz o brilho trêmulo do tile sem "degraus" de 2 px. O tamanho 8,4375 dá exatamente 2 px de tela por pixel de arte em 1080p.

## 9. Fora do escopo desta versão

- Combate do jogador (ataque, armas), inimigos e IA.
- Salvar e carregar, incluindo o estado do baú e dos itens coletados.
- Arrastar e soltar, trocar slots, descartar ou dividir pilhas, baús de armazenamento, crafting e economia.
- Diálogos ramificados, escolhas, retratos, sistema de localização, lore e nomes definitivos.
- Áudio e SFX (não há assets de som no pacote), vibração do gamepad.
- Menu de pausa, tela de game over, opções, rebinding de controles e ícones de botões.
- Pixel Perfect Camera, Cinemachine, look-ahead, transições de cena ou de mapa, minimapa.
- NPCs com sprite e movimento, missões, ciclo de dia e noite.
- Interação com escadas e camadas (`StairsLayerTrigger`) além do que o pacote já faz.
- Altar (`PropsAltar`) como mecânica.
- Arte nova de qualquer tipo, incluindo molduras e ícones de UI.

## Histórico

- **0.2 (12/11/2026):** coleta por Enter com item flutuando, testada em Play Mode: o item só entrou na hotbar depois do Enter, com 0 erros no console.
