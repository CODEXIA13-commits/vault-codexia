---
tipo: documento
categoria: arquitetura-tecnica
projeto: CODEXIA
status: em-revisao
versao: 0.1
idioma: pt-BR
tags:
  - codexia
  - unity
  - versao-jogavel
---

> Documento gerado na preparação da versão jogável (12/11/2026) a partir do trabalho dos subagentes (scripts Core/Shared e integração HUD/cena). Relacionado: [[CODEXIA - GDD]], [[Diretrizes de Arquitetura e Padrões C#]], [[Relatório de Validação Técnica]]. Aguarda revisão do usuário.

# CODEXIA — 03 API do Core para a UI (v0.2)

Código em `V:\Programação\CODEXIA Unity\CODEXIA\Assets/CODEXIA/Scripts/`. Dados de máquina (GUIDs, campos serializados, YAML) em `(arquivo de trabalho) 03_manifest.json`.

## 0. Assemblies, namespaces e como a UI se conecta

| Assembly | GUID do .asmdef | Referências | Namespaces |
|---|---|---|---|
| `Codexia.Core` | `d7af373b8839a6c1105c469ac06fd989` | `Unity.InputSystem`, `Codexia.Shared` | `Codexia.Core.Input`, `.Player`, `.Health`, `.Inventory`, `.Interaction`, `.Dialogue`, `.Feedback` |
| `Codexia.Shared` | `74c77b9bc248534495ad37e6627720ba` | nenhuma | `Codexia.Shared` |

- **`Codexia.UI.asmdef` deve referenciar `"Codexia.Core"`** (e `"Codexia.Shared"` só se usar `CameraFollow2D`). Não precisa de `Unity.InputSystem`: a API pública do Core não expõe nenhum tipo do Input System.
- **Armadilha de nomes (importante):** existem os namespaces `Codexia.Core.Health`, `Codexia.Core.Inventory`, `Codexia.Core.Dialogue`, `Codexia.Core.Input`, `Codexia.Core.Player`. Por isso as classes se chamam `CharacterHealth`, `InventoryState`, `PlayerInventory`, `DialogueRunner`, `GameInput` (nunca `Health`/`Inventory`). Na UI (`namespace Codexia.UI...`) os `using Codexia.Core.X;` funcionam normalmente. **Não crie na UI um tipo chamado `Health`, `Inventory`, `Dialogue`, `Input` ou `Player`**, e não declare a UI dentro de `namespace Codexia.Core...`.
- Padrão recomendado em cada widget:
  ```csharp
  private PlayerContext _player;
  private void Awake()  { _player = Object.FindAnyObjectByType<PlayerContext>(); }   // pode ser null
  private void OnEnable()  { if (_player != null) { _player.Health.Changed += OnHealthChanged; } }
  private void OnDisable() { if (_player != null) { _player.Health.Changed -= OnHealthChanged; } }
  private void Start()  { if (_player != null) OnHealthChanged(_player.Health.Current, _player.Health.Max); } // estado inicial
  ```
  Todas as propriedades do `PlayerContext` e o `PlayerInventory.State` são resolvidos sob demanda, então são seguros mesmo se o Awake do jogador ainda não rodou. Os eventos são campos C#: assinar antes do Awake do jogador funciona. **Leia o estado inicial em `Start`.**
- Teste null em cada propriedade se o widget puder existir sem jogador (ex.: cena de teste). Use `!= null` normal (são `UnityEngine.Object`); **não use `?.` com componentes Unity**.

---

## 1. `Codexia.Core.Player`

### `PlayerContext : MonoBehaviour` (no PF Player)
```csharp
public GameInput Input { get; }
public PlayerMovement Movement { get; }
public CharacterHealth Health { get; }
public PlayerInventory Inventory { get; }
public PlayerInteractor Interactor { get; }
public DialogueRunner Dialogue { get; }
public bool IsControlLocked { get; }   // diálogo ativo || painel aberto || desmaiado
```

### `PlayerMovement : MonoBehaviour` — `[RequireComponent(typeof(Rigidbody2D))]`
```csharp
public event Action<FacingDirection> FacingChanged;
public FacingDirection Facing { get; }
public Vector2 FacingVector { get; }
public bool IsMoving { get; }          // entrada > MovingThreshold (alimenta "IsMoving")
public bool IsKnockedBack { get; }
public Vector2 Velocity { get; }
public PlayerMovementConfig Config { get; }
public void ApplyKnockback(Vector2 velocity, float duration);
public void Teleport(Vector2 position);
public void SetFacing(FacingDirection direction);
```
### `enum FacingDirection { South = 0, North = 1, East = 2, West = 3 }` + `static Vector2 ToVector(this FacingDirection)`
### `CharacterAnimatorBridge : MonoBehaviour` — escreve `Direction` (int) e `IsMoving` (bool) no Animator a cada Update (`[DefaultExecutionOrder(10)]`).
### `PlayerMovementConfig : ScriptableObject` — `MaxSpeed 4.5`, `Acceleration 60`, `Deceleration 80`, `MovingThreshold 0.1`, `FacingHysteresis 0.1` (só getters).

**Fluxo:** `Update` lê `GameInput.Move` (zero se `IsControlLocked` ou em recuo) → calcula `IsMoving` e a direção (histerese, diagonal mantém o eixo atual) → `FixedUpdate` faz `linearVelocity = MoveTowards(v, input*MaxSpeed, rate*dt)`; no recuo, `v = knockVel * (restante/duração)`.

---

## 2. `Codexia.Core.Input`

### `GameInput : MonoBehaviour` (no PF Player)
```csharp
public const int HotbarKeyCount = 9;
public event Action InteractPressed;        // E / botão sul
public event Action AdvancePressed;         // E, Espaço, Enter, Enter do numpad / botão sul
public event Action CancelPressed;          // Esc / botão leste
public event Action InventoryTogglePressed; // I, Tab / botão norte
public event Action UseItemPressed;         // Q, F / botão oeste
public event Action<int> HotbarSlotPressed; // teclas 1–9 e numpad 1–9 → 0..8
public event Action<int> HotbarCyclePressed;// +1/−1: RB/LB, roda do mouse (para cima = −1)
public event Action<bool> DeviceChanged;    // true = passou a usar gamepad
public bool UsingGamepad { get; }
public Vector2 Move { get; }                // WASD, setas, leftStick, dpad; magnitude ≤ 1
```
A UI **não precisa** assinar ações para nada do gameplay (o Core já trata). Use só `UsingGamepad`/`DeviceChanged` para mostrar `[E]` ou `[A]`.

---

## 3. `Codexia.Core.Health`

### `CharacterHealth : MonoBehaviour`
```csharp
public event Action<int, int> Changed;      // (atual, máximo) após dano, cura e respawn
public event Action<int, Vector2> Damaged;  // (dano aplicado, direção normalizada fonte→alvo)
public event Action<int> Healed;            // quanto curou (> 0)
public event Action Died;
public event Action Revived;                // respawn com vida cheia (não dispara Healed)
public HealthConfig Config { get; }
public int Current { get; }
public int Max { get; }
public float Normalized { get; }            // Current / Max
public bool IsDead { get; }
public bool IsFull { get; }
public bool IsInvincible { get; }
public bool TakeDamage(int amount, Vector2 sourcePosition);  // false se invencível/morto
public int Heal(int amount);                // retorna o curado
public int HealFull();
public void Revive();
public void SetInvincible(float seconds);
```
Ordem dos eventos no dano: `Changed` → `Damaged` → (`Died` se zerou). A invencibilidade já está ativa quando `Damaged` dispara.

### `HealthConfig : ScriptableObject`
`MaxHealth 10`, `StartHealth 10`, `InvincibilityDuration 1`, `KnockbackSpeed 7`, `KnockbackDuration 0.15`, `FlashColor (1, .35, .35, 1)`, `FlashDuration 0.1`, `BlinkInterval 0.08`, `BlinkAlpha 0.35`, `RespawnDelay 1.5`, `RespawnInvincibility 2`.

### `DamageZone : MonoBehaviour` (trigger)
Fere ao entrar (`_damage`, padrão 2) e repete em `FixedUpdate` enquanto o alvo está dentro (a invencibilidade de 1 s limita). Acha o alvo por `attachedRigidbody` → `CharacterHealth`.

### `DamageFlash : MonoBehaviour` — `[RequireComponent(typeof(CharacterHealth))]`
Em `LateUpdate`: tinta vermelha por 0,1 s após o dano; depois alterna alfa 1/0,35 a cada 0,08 s até acabar a invencibilidade (também no respawn). Restaura a cor original. Só muda `SpriteRenderer.color` da instância.

### `PlayerHealthResponse : MonoBehaviour`
```csharp
public Vector2 SpawnPosition { get; }
public bool IsRespawning { get; }
public void Respawn();
```
**Fluxo:** `Damaged` → toast `"-N Vida"` (Damage) + `PlayerMovement.ApplyKnockback(dir*7, 0.15)`. `Healed` → `"+N Vida"` (Heal). `Died` → `"Você desmaiou..."` (Damage), fecha diálogo e painel, espera 1,5 s → `Teleport(spawn)` → `Revive()` → invencível 2 s → `CameraFollow2D.SnapToTarget()`. A UI **não** deve gerar esses toasts de novo; basta ouvir o `GameFeedback`. O flash vermelho de tela cheia e o tremor da barra a UI faz ouvindo `CharacterHealth.Damaged`.

---

## 4. `Codexia.Core.Inventory`

### `PlayerInventory : MonoBehaviour`
```csharp
public event Action<bool> PanelToggled;     // true = abriu
public event Action<int> CursorMoved;       // índice 0..Capacity-1
public InventoryConfig Config { get; }
public InventoryState State { get; }        // slots e eventos de slot
public bool IsPanelOpen { get; }
public int CursorIndex { get; }
public int PanelColumns { get; }            // = HotbarSize (9). Linha 0 = hotbar, desenhada EM CIMA
public bool TryAdd(ItemDefinition item, int quantity, out int leftover);
public bool TryAdd(ItemDefinition item, int quantity);
public bool CanAdd(ItemDefinition item, int quantity);
public bool CanAddAll(IReadOnlyList<ItemAmount> entries);
public bool UseSelected();                  // painel aberto: slot do cursor; senão: slot selecionado da hotbar
public bool UseSlot(int index);
public void TogglePanel();
public void SetPanelOpen(bool open);
public void SetCursor(int index);
public void NotifyInventoryFull();          // toast "Inventário cheio", no máx. 1 a cada 2 s
```
O **estado do painel e o cursor ficam no Core**: I/Tab/Y alterna, Esc/B fecha, Move (digital, limiar 0,5, repetição 0,2 s unscaled) move o cursor, Q/F/X usa. A UI só desenha `IsPanelOpen` e `CursorIndex` e a descrição de `State.GetSlot(CursorIndex)`. Cursor: `col = index % PanelColumns`, `row = index / PanelColumns`; "para cima" diminui `row` (hotbar em cima, mochila embaixo). Ao abrir, o cursor vai para o slot selecionado da hotbar (o `CursorMoved` só dispara se o índice mudar; leia `CursorIndex` em `PanelToggled(true)`).

### `InventoryState` (C# puro)
```csharp
public event Action<int> SlotChanged;
public event Action<int> SelectionChanged;
public event Action<ItemDefinition, int> ItemAdded;   // quantidade realmente adicionada
public event Action<ItemDefinition> AddFailedFull;
public int HotbarSize { get; }               // 9
public int BackpackSize { get; }             // 9
public int Capacity { get; }                 // 18
public IReadOnlyList<InventorySlot> Slots { get; }
public int SelectedHotbarIndex { get; }
public InventorySlot SelectedSlot { get; }
public InventorySlot GetSlot(int index);     // null fora da faixa
public bool IsHotbarIndex(int index);
public bool TryAdd(ItemDefinition item, int quantity, out int leftover);
public bool TryAdd(ItemDefinition item, int quantity);
public bool CanAdd(ItemDefinition item, int quantity);
public bool CanAddAll(IReadOnlyList<ItemAmount> entries);
public int Remove(int index, int quantity);
public int CountOf(ItemDefinition item);
public void SelectHotbar(int index);
public void CycleHotbar(int delta);
```
### `InventorySlot`
```csharp
public ItemDefinition Item { get; }   // null se vazio
public int Quantity { get; }
public bool IsEmpty { get; }
```
### `ItemDefinition : ScriptableObject` — `[CreateAssetMenu(menuName = "CODEXIA/Item")]`
```csharp
public string Id { get; }  public string DisplayName { get; }  public string Description { get; }
public Sprite Icon { get; }  public bool Stackable { get; }  public int MaxStack { get; }
public int HealAmount { get; }  public bool IsConsumable { get; }   // HealAmount > 0
```
### `struct ItemAmount` — `ItemDefinition Item`, `int Quantity`, construtor `ItemAmount(ItemDefinition, int)`.
### `InventoryConfig : ScriptableObject` — `HotbarSize 9`, `BackpackSize 9`, `CursorRepeatDelay 0.2`, `FullMessageCooldown 2`.
### `ItemPickup : MonoBehaviour` — `ItemDefinition Item { get; }`, `int Remaining { get; }`.

**Fluxos.**
- Coleta: `OnTriggerEnter2D` → `attachedRigidbody.TryGetComponent<PlayerInventory>` → `TryAdd(item, restante, out leftover)`. Se `leftover == 0`, `SetActive(false)` no visual e no próprio GO. Senão guarda o resto; o `AddFailedFull` gera "Inventário cheio" (com intervalo de 2 s).
- Adição: completa as pilhas existentes (na ordem dos slots, hotbar primeiro) e depois ocupa o primeiro slot vazio. Cada slot alterado dispara `SlotChanged(i)`; ao final, `ItemAdded(def, n)` → toast `"+n Nome"` (Pickup).
- Uso: sem item → nada. Item sem efeito → "Não é possível usar isso agora." Vida cheia → "Sua vida já está cheia" (não consome). Senão `Heal(HealAmount)` + `Remove(i, 1)`. Bloqueado em diálogo e desmaio.
- Hotbar: teclas 1–9 → `SelectHotbar`; RB/LB/roda → `CycleHotbar` (com volta). Bloqueado em diálogo e desmaio.

---

## 5. `Codexia.Core.Interaction`

```csharp
public interface IInteractable
{
    string Prompt { get; }        // verbo PT-BR: "Falar", "Ler", "Abrir", "Beber", "Olhar". A UI monta "[E] " + Prompt
    bool CanInteract { get; }
    void Interact(PlayerInteractor interactor);
}
public abstract class Interactable : MonoBehaviour, IInteractable   // _prompt serializado (padrão "Falar")
public sealed class DialogueInteractable : Interactable             // falas + DialogueEndAction (None/HealFull/GiveItem)
public sealed class ChestInteractable : Interactable                // bool IsOpened; Prompt = "Olhar" depois de aberto
public enum DialogueEndAction { None = 0, HealFull = 1, GiveItem = 2 }
```
### `PlayerInteractor : MonoBehaviour`
```csharp
public event Action<IInteractable> TargetChanged;  // null = sem alvo
public PlayerContext Context { get; }
public bool HasTarget { get; }
public IInteractable CurrentTarget { get; }        // null se não houver
public string CurrentPrompt { get; }               // "" se não houver
public bool CanInteractNow { get; }                // false com diálogo/painel/desmaio/bloqueio de 0,2 s
public bool TryInteract();
```
**Fluxo:** `FixedUpdate` → ponto = `rb.position + (0; 0,2) + facing × 0,35` → `Physics2D.OverlapCircle(ponto, 0,75, filtro com triggers, lista)` → para cada collider, `GetComponentInParent<Interactable>()` (com cache) com `CanInteract` → escolhe o menor `ClosestPoint`. `TargetChanged` só dispara quando o alvo muda. Interact (E/A) → `TryInteract()`.
**Prompt na UI:** mostrar quando `HasTarget && !Context.IsControlLocked && !Context.Dialogue.IsInputBlocked` (ou simplesmente `CanInteractNow && HasTarget`). Como o prompt do baú muda ao abrir sem trocar o alvo, **leia `CurrentPrompt` a cada frame** (ou ao receber `TargetChanged`/`Dialogue.Ended`).

- `DialogueInteractable` + `HealFull`: com vida não cheia, as falas normais e `HealFull()` na última fala (gera "+N Vida"). Com a vida cheia, `_alternateLines` (sem cura).
- `DialogueInteractable` + `GiveItem`: na última fala, entrega `_item × _itemQuantity` (se couber; senão "Inventário cheio"). Com `_giveOnce`, depois usa `_alternateLines`.
- `ChestInteractable`: fechado → se o conteúdo não couber, só "Inventário cheio" (não abre); se couber, falas fechadas e, na última fala, adiciona o conteúdo, troca o sprite (e a sombra, se configurada) e marca aberto. Aberto → `_openedLines`. Estado não persiste.

---

## 6. `Codexia.Core.Dialogue`

### `DialogueRunner : MonoBehaviour` (no PF Player)
```csharp
public event Action<string> Started;          // falante
public event Action<int, string> LineShown;   // (índice, texto completo) no início de cada fala
public event Action<string> TextRevealed;     // texto visível (máquina de escrever, 40 car/s unscaled)
public event Action Ended;
public bool IsActive { get; }
public string Speaker { get; }
public int LineIndex { get; }
public int LineCount { get; }
public string CurrentLine { get; }
public string VisibleText { get; }
public bool IsTyping { get; }
public bool IsLastLine { get; }
public bool IsInputBlocked { get; }           // ativo ou fechado há < 0,2 s
public bool StartDialogue(string speaker, IReadOnlyList<string> lines, Action onLastLine = null);
public void Advance();
public void Close();
```
**Fluxo:** Interact → `StartDialogue` → `Started` → `LineShown(0)` → `TextRevealed("")`… `TextRevealed(texto)`. `AdvancePressed`: se digitando, completa (`TextRevealed(completo)`); senão próxima fala; na última, `Close()` → `Ended`. `CancelPressed` fecha a qualquer momento (o `onLastLine` só roda quando a última fala aparece). O toque que abriu não avança (verificação por `Time.frameCount`); depois de fechar, a interação fica bloqueada por 0,2 s. A UI: mostra a caixa em `Started`, atualiza o nome com `Speaker`, o texto com `TextRevealed`, pisca `[E]`/`[A]` quando `!IsTyping`, esconde em `Ended`.

---

## 7. `Codexia.Core.Feedback`

```csharp
public enum FeedbackKind { Info = 0, Pickup = 1, Damage = 2, Heal = 3 }
public static class GameFeedback
{
    public static event Action<string, FeedbackKind> MessagePosted;
    public static void Post(string message, FeedbackKind kind = FeedbackKind.Info);
}
```
Mensagens emitidas pelo Core (todas ASCII + acentos Latin-1, sem "−" nem "…"):

| Texto | Kind | Origem |
|---|---|---|
| `+{n} {DisplayName}` (ex.: `+1 Flor do campo`) | Pickup | `PlayerInventory` (qualquer adição: coleta, baú, presente) |
| `Inventário cheio` | Info | `PlayerInventory.NotifyInventoryFull` (máx. 1 a cada 2 s) |
| `Não é possível usar isso agora.` | Info | uso de item sem efeito |
| `Sua vida já está cheia` | Info | Pote de água com vida cheia |
| `-{n} Vida` | Damage | `PlayerHealthResponse` |
| `+{n} Vida` | Heal | `PlayerHealthResponse` (poço e pote) |
| `Você desmaiou...` | Damage | `PlayerHealthResponse` |

**Fusão "+3 Flor do campo":** mensagens Pickup têm sempre o formato `"+<n> <nome>"`; a UI pode fundir duas Pickup com o mesmo `<nome>` em menos de 2 s somando `<n>`. Alternativa mais robusta: ouvir `Inventory.State.ItemAdded(def, n)` para a fusão e **ignorar** `FeedbackKind.Pickup` no `MessagePosted` (escolha uma das duas, não as duas). `MessagePosted` é estático: assine em `OnEnable` e desassine em `OnDisable`. Os assinantes são limpos em `SubsystemRegistration` (Domain Reload desligado).

---

## 8. `Codexia.Shared`

### `CameraFollow2D : MonoBehaviour` — `[RequireComponent(typeof(Camera))]`
```csharp
public Transform Target { get; }
public Rect Bounds { get; }
public void SetTarget(Transform target, bool snap = true);
public void SetBounds(Rect bounds, bool clamp = true);
public void SnapToTarget();
```
`LateUpdate`: alvo + (0; 0,5), `Lerp(pos, alvo, 1 − exp(−dt/0,12))`, Z = −10, limitado ao retângulo (−27, −12, 54, 24) usando `orthographicSize` e `aspect` a cada frame (centraliza se a vista for maior que o mapa), arredonda para 1/64 u (só na saída; o estado interno não é arredondado). `Start` e o respawn chamam `SnapToTarget()`. Não usar junto com o `CameraFollow` do pacote.

---

## 9. Montagem na cena (resumo; detalhes e YAML no manifest)

- **PF Player** (instância): remover `TopDownCharacterController` (`m_RemovedComponents`) e adicionar, nesta ordem: `GameInput`, `PlayerContext`, `PlayerMovement`, `CharacterAnimatorBridge`, `CharacterHealth`, `PlayerHealthResponse`, `DamageFlash`, `PlayerInventory`, `DialogueRunner`, `PlayerInteractor`. Configs: `_config` de `PlayerMovement`, `CharacterHealth` e `PlayerInventory` (opcionais; sem eles, padrão + aviso no Console). O Rigidbody2D do prefab já tem gravityScale 0, Interpolate, Continuous e FreezeRotation.
- **Main Camera:** `CameraFollow2D` com `_target` = Transform stripped do jogador; posicione a câmera em (spawn.x; spawn.y + 0,5; −10).
- **Objetos que falam / baú:** GO irmão comum (layer 0) com `CircleCollider2D` isTrigger + `DialogueInteractable`/`ChestInteractable`. O baú recebe `_visualRoot` = Transform stripped `900000322` e os sprites `{21300000}`/`{21300040}` (TX Props) e sombra aberta `{21300008}` (TX Shadow).
- **Coletáveis:** GO irmão com `CircleCollider2D` isTrigger r = 0,35 + `ItemPickup` (`_visualRoot` = Transform stripped do prop).
- **Perigo:** GO irmão do Rune Pillar X2 com `CircleCollider2D` isTrigger r = 0,8 (offset y 0,25) + `DamageZone`.
- **Limites:** `EdgeCollider2D` em laço no retângulo (−27, −12)–(27, 12), sem script.
- **SOs** (`.asset` + `.meta` NativeFormatImporter, `mainObjectFileID: 11400000`): 5 itens e 3 configs, valores no manifest (`itemAssets`, `configAssets`).
- **Não crie `Assets/CODEXIA.meta`**: a pasta já existe no projeto do usuário com outro GUID. Os .meta entregues começam em `Assets/CODEXIA/Scripts.meta`. Se `Assets/CODEXIA/Scripts` já existir lá, apague o `Scripts.meta` entregue antes de copiar.

---

## 10. Autoexame de compilação (arquivo por arquivo)

Sem compilador neste container: revisão manual + script estrutural (`(arquivo de trabalho)`: chaves/parênteses balanceados, 1 tipo de topo por arquivo, nome do arquivo = nome do tipo, nenhuma ocorrência de `.velocity`, `FindObjectOfType`, `Input.Get*`, `Arial.ttf`). Todos passaram. APIs conferidas na doc do Unity 6.x e do Input System 1.8 (as assinaturas usadas existem sem mudança até a 1.20).

| Arquivo | usings | Tipo = arquivo | APIs Unity 6 / pontos verificados |
|---|---|---|---|
| Shared/CameraFollow2D.cs | UnityEngine | ✔ | `Camera.orthographicSize`, `aspect`, `Mathf.Exp`, `Rect.xMin/xMax`; `[Min]`, `[RequireComponent(typeof(Camera))]` |
| Core/Input/GameInput.cs | System, UnityEngine, UnityEngine.InputSystem | ✔ | `new InputActionMap(string)`, `map.AddAction(name, InputActionType)`, `action.expectedControlType` (setter), `AddCompositeBinding("2DVector").With(...)`, `AddBinding(path)`, `performed`, `map.actionTriggered`, `ctx.performed`, `ctx.control.device is Gamepad`, `ReadValue<Vector2/float>()`, `Enable/Disable/Dispose` |
| Core/Feedback/FeedbackKind.cs | — | ✔ | enum |
| Core/Feedback/GameFeedback.cs | System, UnityEngine | ✔ | `[RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.SubsystemRegistration)]` em método estático privado |
| Core/Player/FacingDirection.cs | — | ✔ | enum, valores = Animator |
| Core/Player/FacingDirectionExtensions.cs | UnityEngine | ✔ | método de extensão em classe estática |
| Core/Player/PlayerMovementConfig.cs | UnityEngine | ✔ | `[CreateAssetMenu]`, `[Min]`, `[Range]` |
| Core/Player/PlayerMovement.cs | System, Codexia.Core.Input, UnityEngine | ✔ | `Rigidbody2D.linearVelocity`, `Rigidbody2D.position`, `Vector2.MoveTowards`, `ScriptableObject.CreateInstance<T>()` |
| Core/Player/CharacterAnimatorBridge.cs | UnityEngine | ✔ | `Animator.StringToHash`, `SetInteger/SetBool(int, …)`, `runtimeAnimatorController`, `[DefaultExecutionOrder]` |
| Core/Player/PlayerContext.cs | 5 namespaces do Core, UnityEngine | ✔ | propriedades com o mesmo nome de namespaces (`Health`, `Inventory`, `Input`, `Dialogue`) são legais: dentro da classe o membro tem precedência |
| Core/Health/HealthConfig.cs | UnityEngine | ✔ | `Color` em inicializador de campo (struct puro) |
| Core/Health/CharacterHealth.cs | System, UnityEngine | ✔ | `Time.time`; eventos `Action<int,int>` etc. |
| Core/Health/DamageZone.cs | System.Collections.Generic, UnityEngine | ✔ | `OnTriggerEnter2D/Exit2D(Collider2D)`, `attachedRigidbody`, `TryGetComponent(out T)`, `GetComponentInParent<T>()` |
| Core/Health/DamageFlash.cs | UnityEngine | ✔ | `Color * Color`, `SpriteRenderer.color` |
| Core/Health/PlayerHealthResponse.cs | System.Collections, Feedback, Player, Codexia.Shared, UnityEngine | ✔ | `FindAnyObjectByType<T>()`, `StartCoroutine/StopCoroutine`, `WaitForSeconds` |
| Core/Inventory/ItemDefinition.cs | UnityEngine | ✔ | `[TextArea]`, `name` |
| Core/Inventory/ItemAmount.cs | System, UnityEngine | ✔ | `[Serializable] struct` com `[SerializeField] private` e construtor que atribui todos os campos |
| Core/Inventory/InventorySlot.cs | — | ✔ | setters `internal` (mesmo assembly) |
| Core/Inventory/InventoryConfig.cs | UnityEngine | ✔ | |
| Core/Inventory/InventoryState.cs | System, System.Collections.Generic, UnityEngine | ✔ | `InventorySlot[]` → `IReadOnlyList<InventorySlot>` (conversão implícita) |
| Core/Inventory/PlayerInventory.cs | System, Generic, Feedback, Health, Input, Player, UnityEngine | ✔ | `Vector2Int ==/!=`, `Time.unscaledTime` (295 linhas) |
| Core/Inventory/ItemPickup.cs | UnityEngine | ✔ | `body.TryGetComponent(out PlayerInventory inventory)`, `SetActive(false)` dentro do callback de trigger |
| Core/Interaction/IInteractable.cs | — | ✔ | |
| Core/Interaction/Interactable.cs | UnityEngine | ✔ | classe abstrata MonoBehaviour (não pode ir em cena) |
| Core/Interaction/DialogueEndAction.cs | — | ✔ | |
| Core/Interaction/DialogueInteractable.cs | System, Generic, Health, Inventory, Player, UnityEngine | ✔ | `List<string>` → `IReadOnlyList<string>`; lambda `() => health.HealFull()` como `Action` (valor descartado); `case` com chaves |
| Core/Interaction/ChestInteractable.cs | Generic, Inventory, Player, UnityEngine | ✔ | `Transform.Find("Shadow")`, `override Prompt` chamando `base.Prompt` |
| Core/Interaction/PlayerInteractor.cs | System, Generic, Input, Player, UnityEngine | ✔ | `Physics2D.OverlapCircle(Vector2, float, ContactFilter2D, List<Collider2D>)` (existe no 6000.x, sem obsolescência), `new ContactFilter2D { useTriggers = true }` + `SetLayerMask`, `Collider2D.ClosestPoint(Vector2)`, `LayerMask _x = ~0` (conversão implícita de int) |
| Core/Dialogue/DialogueRunner.cs | System, Generic, Codexia.Core.Input, UnityEngine | ✔ | `Time.unscaledDeltaTime`, `Time.frameCount` |

### Armadilhas e riscos conhecidos
1. **Namespace × tipo:** se alguém criar uma classe `Health` ou `Inventory` dentro do Core, ou escrever `Health.X` sem qualificador dentro de `namespace Codexia.Core.*`, dá CS0118 ("is a namespace but is used like a type"). Mantidos os nomes `CharacterHealth`/`InventoryState`.
2. **`Unity.InputSystem` por nome no asmdef:** funciona; o Unity pode reescrever como `GUID:75469ad4d38634e559750d17036d5f7c` ao salvar pelo Inspector. Normal.
3. **Avisos esperados (não erros):** CS0649 em campos `[SerializeField]` nunca atribuídos em código (o Unity costuma suprimir), aviso no Console se um `_config` estiver vazio.
4. **Rodas do mouse:** o valor de `scroll/y` muda de escala entre plataformas/versões; o código usa só o sinal.
5. **`ContactFilter2D` padrão:** sem `useTriggers = true` os triggers de interação seriam ignorados. Já tratado.
6. **Ordem de callbacks E (Interact + Advance no mesmo toque):** resolvida por `Time.frameCount` na abertura e pelo bloqueio de 0,2 s no fechamento.
7. **Teleporte com Interpolate:** `Teleport` define `transform.position` e `rb.position` e zera a velocidade; a câmera faz `SnapToTarget` logo depois.
8. **OnTriggerStay2D não é usado** (para de chegar com o corpo dormindo); o `DamageZone` repete o dano em `FixedUpdate`.
9. **`DialogueRunner.OnDisable` chama `Close()`**, que dispara `Ended` ao descarregar a cena; a UI deve desassinar em `OnDisable` (padrão acima) para não receber eventos depois de destruída.
10. **`FindAnyObjectByType`** pode retornar null se a UI for carregada antes do jogador em outra cena (aditiva). Nesta versão a cena é única.


---

# Anexo — Integração na cena e HUD

# CODEXIA: 04 Integração da v0.2 (UI, dados e cena)

Entregas em `V:\Programação\CODEXIA Unity\CODEXIA\Assets/CODEXIA/` (mesma árvore do projeto):

| Pasta | Conteúdo |
|---|---|
| `Scripts/UI/` (+ `Scripts/UI.meta`) | `Codexia.UI.asmdef` e 13 scripts uGUI (namespace `Codexia.UI`) |
| `Data/Items/` | 5 `ItemDefinition` (.asset + .meta) |
| `Data/Config/` | `PlayerMovementConfig`, `HealthConfig`, `InventoryConfig` |
| `Data.meta`, `Data/Items.meta`, `Data/Config.meta` | pastas novas |
| `Scenes/Validation/Validacao - Area Inicial.unity` (+ .meta) | cena v0.2, mesmo GUID `f12ed1588fa422716749dc17b4f392d1` |

**Não entregamos `.meta` de `Assets/CODEXIA/Scenes` nem de `Scenes/Validation`.** As pastas já existem no projeto com GUID próprio. Basta sobrescrever a cena e o `.meta` dela.

**Core e Shared: nenhuma alteração.** Foram revisados de novo e não há erro de compilação nem bug de comportamento que exija correção (detalhes na seção 6). A API pública ficou intacta.

GUIDs: `md5("codexia:" + caminho)`, a mesma regra do Core. Os IDs de cena novos começam em 910000000.

---

## 1. UI (`Codexia.UI`)

`Codexia.UI.asmdef` referencia `Codexia.Core`, `Codexia.Shared` e `UnityEngine.UI` (GUID `1e9ece23aafb40416be77760d7edeea5`, rootNamespace `Codexia.UI`). Não referencia `Unity.InputSystem`, porque a API do Core usada pela UI não expõe tipos dele.

| Arquivo | Papel |
|---|---|
| `HudBootstrap.cs` | Único componente na cena (GO "HUD"). Em `Start`, cria "HUD Canvas" com Canvas Screen Space Overlay (sortingOrder 100) e CanvasScaler Scale With Screen Size 640×360 com match 0,5. **Não cria GraphicRaycaster nem EventSystem.** Depois adiciona os widgets e chama `Initialize(PlayerContext)`. O campo `_player` vem ligado ao `PlayerContext` do jogador; se estiver vazio, usa `FindAnyObjectByType`. |
| `HudWidget.cs` | Base abstrata. `Build()` roda uma vez. `Bind()`/`Unbind()` assinam e desassinam os eventos do Core em OnEnable/OnDisable e **também em OnDestroy** (idempotente). `Refresh()` lê o estado inicial. |
| `HudStyle.cs` | Paleta e medidas (01_mecanicas §2 e §5): fundo `#1A1A1A`, rastro `#E8D9A0`, verde/amarelo/vermelho, slot preto α0,55, moldura `#F2D16B`, diálogo `#101018` α0,9 etc. |
| `UiFactory.cs` | Cria `Image` sem sprite (retângulo de cor), `Text` com `Resources.GetBuiltinResource<Font>("LegacyRuntime.ttf")` e `Outline` preto, além de helpers de âncora. A barra usa `anchorMax.x = fração` (não usa `Image.Type.Filled`). |
| `HealthBarView.cs` | Canto superior esquerdo, 120×10, margem 8 e texto "HP x/y" à direita (tamanho 9). O rastro espera 0,35 s e desce em 0,3 s. Treme ±2 px por 0,15 s no `Damaged`. A cor depende da fração (>50%, 25–50%, ≤25%); a ≤25% a barra **pulsa** a 2 Hz. Tudo em tempo unscaled. |
| `ScreenDamageFlashView.cs` | Véu vermelho em tela cheia, α0,22 → 0 em 0,25 s, a cada `Damaged`. |
| `ItemSlotView.cs` | Slot de 28×28 (classe C# simples): moldura atrás (+2 px), fundo, ícone `preserveAspect`, quantidade (tamanho 8, só se >1) e tecla (tamanho 7, α0,6). O ícone escala **inteiro** até caber em 22 px (flor 6×5 → ×3, pedra ×2, tijolo ×1). Só reduz se o sprite for maior que o slot (pote 21×34 → 13,6×22). |
| `HotbarView.cs` | 9 slots embaixo ao centro (gap 2, margem 6). Ouve `SlotChanged` e `SelectionChanged`. |
| `InventoryPanelView.cs` | Painel central com borda dourada de 1 px, título "Inventário", linha 0 = hotbar e linha 1 = mochila (com divisória). O cursor tem moldura branca e o selecionado da hotbar, dourada. Mostra nome (×qtd) e descrição do item sob o cursor, mais a dica "WASD/setas mover cursor · Q/F usar · I/Esc fechar". O Core guarda o estado aberto/fechado e o cursor; a view só desenha. |
| `InteractionPromptView.cs` | "[E] Falar" / "[A] …" **fixo acima da hotbar**, por ser mais robusto que converter world→screen (não depende da câmera nem de offsets do sprite). Lê `Interactor.CurrentPrompt` a cada `LateUpdate`, porque o verbo do baú muda sem trocar o alvo. Aparece só quando `HasTarget && CanInteractNow`. |
| `DialogueBoxView.cs` | Caixa inferior de 560×74 acima da hotbar, com falante amarelo (10) e fala (10, até 3 linhas). A máquina de escrever **é do Core** (`TextRevealed`); a view só exibe. O resto da fala é desenhado transparente (`<color=#00000000>`), para a quebra de linha não "pular" durante a digitação. O indicador pisca a 2 Hz quando a digitação termina: "[E] >" ou "[E] fechar" na última fala, e "[A]" no gamepad. **Usamos ASCII em vez de "▼"** (01 §5, risco 5: glifo fora do Latin-1). |
| `ToastView.cs` | Canto superior direito, empilhando para baixo. Máximo de 4 avisos (o mais antigo é reciclado). Cada aviso dura 2,5 s e some com fade de 0,35 s (CanvasGroup), em tempo unscaled. Cores: coleta branca, dano `#FF6B6B`, cura `#7CFF7C`, informação `#F2D16B`. **Fusão:** só `MessagePosted`; mensagens Pickup "+n Nome" com o mesmo nome em menos de 2 s somam ("+3 Flor do campo"). O `ItemAdded` não é usado, para não duplicar. |
| `ControlsHintView.cs` | Canto inferior esquerdo, tamanho 7, α0,6, em duas linhas: "WASD mover · E interagir · I inventário" / "1-9 hotbar · Q usar · Esc fechar". Troca para os botões do gamepad no `DeviceChanged`. |

Regras atendidas: sem TextMeshPro, sem EventSystem, sem sprite de UI e sem arte nova. Todos os `raycastTarget` estão desligados. Arquivos de 53 a 224 linhas. Nenhum tipo novo se chama `Health`, `Inventory`, `Dialogue`, `Input` ou `Player` (a propriedade da base chama-se `Context`).

## 2. Dados (ScriptableObjects)

| Asset | GUID | Valores |
|---|---|---|
| `Item - Flor do campo` | `01d7549420d23a69682bb5cc95602620` | flor_campo, pilha de 20, ícone TX Plant 21300174 |
| `Item - Pedrinha` | `bae8a41e6a838b625506bc200065be6e` | 20, TX Props 21300102 |
| `Item - Tijolo antigo` | `f3e2fa002d23fe44b399cf14450486ef` | 10, 21300152 |
| `Item - Pote de agua` | `03a306c9416a58740ddc9e22ce8cf389` | 5, cura 3, 21300090 |
| `Item - Fragmento runico` | `03210ab885cc53221f0abcea9386b427` | não empilha, 21300064 |
| `PlayerMovementConfig` | `e4a066cfc9c08097655ae230573699e9` | 4,5 / 60 / 80 / 0,1 / 0,1 |
| `HealthConfig` | `e2f79db3217c0a1fa42493ae78d20cdc` | 10/10, invencibilidade 1 s, recuo 7 u/s por 0,15 s, flash e respawn conforme 03 |
| `InventoryConfig` | `6b84d82681ff0262c8604ac431357f7a` | 9+9, cursor 0,2 s, aviso a cada 2 s |

Formato: `--- !u!114 &11400000`, `m_EditorClassIdentifier: Codexia.Core::<FullName>` e `.meta` NativeFormatImporter com mainObjectFileID 11400000. As strings estão em UTF-8 sem aspas (estilo do Unity), com aspas simples só quando têm ": ".

## 3. Cena: o que foi montado

O gerador foi salvo em `V:\Programação\CODEXIA Unity\Ferramentas\gerador-cena\` (o original `(arquivo de trabalho)` não foi tocado; a cena v1 continua em arquivo de trabalho). Para regenerar tudo: `(ferramenta de trabalho) run_all.sh`, que roda gen_assets → placement → build_scene_v2 → preview_v2 → validate_scene. O resultado é determinístico (mesmo md5 em duas execuções).

**Base preservada:** os 477 documentos da v1 continuam com os mesmos IDs 9000xxxxx. Só mudaram:
- a Main Camera (componente CameraFollow2D acrescentado e posição);
- a Global Light 2D (`m_ApplyToSortingLayers`);
- a lista `m_Children` de "Objetos";
- os 227 PrefabInstances, que passaram ao **formato Unity 6** (`m_Modification.serializedVersion: 3` + `m_RemovedGameObjects`/`m_AddedGameObjects`/`m_AddedComponents`), necessário para os componentes adicionados no jogador. O conteúdo das modificações é o mesmo.

Sem `SceneRoots`: a ordem das raízes vem de `m_RootOrder` (Camera 0, Light 1, AREA INICIAL 2, Jogador 3, Limites 4, HUD 5). O Unity cria o bloco ao salvar.

Os IDs das instâncias existentes (estátua, poço, placas, baú, flores, pedra) são achados **em tempo de geração** por (nome do prefab, posição) no `layout.json` (`find_instance`). Se o layout mudar, a geração falha com uma mensagem clara.

| Item | Montagem | IDs principais |
|---|---|---|
| **Global Light 2D** | `m_ApplyToSortingLayers: 0000000003819490c93960fdb391b5f9`. A codificação foi conferida: int32 little-endian = 0, −1869315837, −44025399, −105541197, o mesmo valor das cenas do patch. | 619394801 |
| **Jogador** | Instância de `PF Player` na raiz, nome "Jogador", em (−0,5; −2,6). `m_RemovedComponents` = TopDownCharacterController (5628018141983903119). `m_AddedComponents` = 10 componentes do Core, na ordem do manifest, num GO stripped. As configs vêm ligadas aos SOs, e `PlayerHealthResponse._cameraFollow` aponta para o CameraFollow2D. Tag Player e layer 20 vêm do prefab. | PI 910000000, GO stripped 910000001, TR stripped 910000002, GameInput …003, **PlayerContext …004**, PlayerMovement …005, Bridge …006, CharacterHealth …007, PlayerHealthResponse …008, DamageFlash …009, PlayerInventory …010, DialogueRunner …011, PlayerInteractor …012 |
| **Câmera** | `CameraFollow2D` na Main Camera, com `_target` = TR stripped do jogador, bounds (−27, −12, 54, 24), τ 0,12 e snap 1/64. A câmera começa em (−0,5; −2,1; −10), já no spawn e dentro do clamp de 16:9. | 910000013 |
| **Limites** | GO "Limites" (layer 20) com 4 BoxCollider2D de 1 u de espessura **do lado de fora** das bordas x ±27 e y ±12: centros (±27,5; 0) com 1×26 e (0; ±12,5) com 56×1. | GO 910000014, caixas …016–019 |
| **Objetos que falam** | GOs irmãos sob "Objetos" (layer 20), cada um com CircleCollider2D trigger e o componente. Estátua, poço, placas O/L e baú, com centro e raio da sugestão de 02, e falas PT-BR do 01/manifest. Poço: `_endAction 1` (HealFull), com falas alternativas para vida cheia, prompt "Beber". Placas: "Ler". Baú: `ChestInteractable` com `_visualRoot` = TR stripped 900000322, **`_chestRenderer` = SpriteRenderer stripped novo** (910000039 → 2055102311959635368 do Chest 01), sprites fechado/aberto, sombra aberta e conteúdo 2 potes + 1 fragmento. | Estátua 910000025, Poço …029, Placa O …033, Placa L …037, Baú …042 (comp. …041) |
| **Perigos** | 2 instâncias de `PF Props - Rune Pillar X2` (brilho ciano pulsante do pacote) em (−6,5; 4,0) e (−14,5; 1,5), as "ruínas" a oeste do 01. Cada uma tem o irmão "Perigo - Pilar Runico (n)" com Circle trigger r 0,8 (offset 0; 0,25) e `DamageZone` (2 HP, repetição ligada). | PIs 910000044 e …050; DamageZone …047 e …053 |
| **Coletáveis (10)** | Irmão "Coleta - <item> (n)" com Circle trigger r 0,35 (offset 0; 0,1) e `ItemPickup` (qtd 1, `_visualRoot` = TR stripped da instância visual). Lista abaixo. | ver tabela |
| **HUD** | GO "HUD" (layer 5) com `HudBootstrap` (`_player` = 910000004). Não precisa de GO de sistema: o DialogueRunner está no jogador. | GO 910000020, comp. …022 |

Coletáveis (posições validadas por `placement.py`):

| Item | Visual | Posição |
|---|---|---|
| Flor do campo ×3 | instâncias **existentes** de Flower 01 (PIs 900003157, 900002009, 900002583) | (−1,85; −4,60) logo ao sul do spawn; (−10,30; −0,33); (15,71; −0,39) |
| Pedrinha ×2 | Stone 01 existente (900000385) + Stone 01 nova | (−11,2; 2,7); (−17,4; 2,9) |
| Tijolo antigo ×3 | Brick 01 novos | (−8,3; 4,4); (−11,8; 1,9); (−17,6; 1,4) |
| Pote de água ×1 | Pot 01 novo **com Rigidbody2D e PolygonCollider2D removidos na instância** (`m_RemovedComponents`), para não ser empurrado e deixar o trigger para trás | (14,5; 2,9), perto do depósito |
| Fragmento rúnico ×1 | GO com SpriteRenderer do pacote (TX Props Rune Pillar Broken Glow 21300064, MT FX Rune Glow, ciano) + `SpriteColorAnimation` do pacote (gradiente do X2) para pulsar. `_visualRoot` vazio: o próprio GO some. | (−4,6; 3,3) |

As outras 10 flores decorativas continuam não coletáveis. Para torná-las coletáveis, basta acrescentar entradas `kind='existing'` em `v02_layout.py`.

### Decisões

1. **Jogador = instância do PF Player com `m_AddedComponents`** (e não um GO "Player" pai com o prefab como filho). Motivos:
   - Rigidbody2D, BoxCollider2D, SpriteRenderer e Animator estão na raiz do prefab, e o Core foi escrito para isso: `GetComponent<Rigidbody2D/Animator/SpriteRenderer>` no mesmo GO, `[RequireComponent(typeof(Rigidbody2D))]`, `attachedRigidbody.TryGetComponent<PlayerInventory>` nos pickups e `hit.attachedRigidbody == _rb` no interactor.
   - A alternativa exigiria remover o RB e o colisor do filho e recriá-los no pai, além de ligar Animator e SpriteRenderer por referências stripped. Seriam mais documentos à mão e mais pontos de falha.
   - O formato Unity 6 usado (GO stripped + MonoBehaviour com `m_GameObject` nele + lista `m_AddedComponents` com `targetCorrespondingSourceObject`/`insertIndex: -1`/`addedObject`) é o que o próprio editor grava. O validador confere cada ligação.
2. **Triggers e limites no layer 20 ("Layer 1")**, o mesmo do jogador e dos props. Assim funcionam mesmo que a matriz de colisão 2D do projeto tenha sido ajustada para as escadas do pacote. A busca do `PlayerInteractor` usa todas as layers, então isso não a afeta.
3. **Colliders novos no formato Unity 6** (Collider2D `serializedVersion: 3`, com `m_CompositeOperation` e as máscaras), copiado do TilemapCollider2D/CompositeCollider2D das cenas do patch. É o único YAML de componente nativo escrito à mão sem exemplo exato de Circle/Box no Unity 6 (veja o checklist).
4. **HUD sem `HudConfig` SO** nesta versão: a paleta fica em `HudStyle` (estática). Se quiser editar no Inspector, dá para migrar para um SO sem mudar os widgets.
5. **Prompt fixo acima da hotbar** (01 §5) e **toasts por `MessagePosted`** (opção 1 do 03 §7).

## 4. Controles (definidos no Core, `GameInput`)

| Ação | Teclado | Gamepad |
|---|---|---|
| Mover | WASD / setas | analógico esquerdo / d-pad |
| Interagir | E | A |
| Avançar diálogo | E / Espaço / Enter | A |
| Fechar diálogo/painel | Esc | B |
| Inventário | I / Tab | Y |
| Usar item (hotbar ou cursor) | Q / F | X |
| Selecionar slot | 1–9 (e numpad) | LB/RB (roda do mouse também) |

## 5. Validação feita aqui

- **`validate_scene.py`** (reutilizável; sem argumentos valida a cena e todos os .asset de Data): **0 erros, 0 avisos** em 585 documentos + 8 assets. Ele confere:
  - IDs únicos e referências internas;
  - GO ↔ componentes e Transform pai/filhos, inclusive raízes de PrefabInstance;
  - m_RootOrder;
  - alvos de modificações, removidos e adicionados no prefab;
  - stripped ↔ prefab;
  - guids externos (scripts e assets do CODEXIA, prefabs e sprites do pacote, built-ins conhecidos);
  - campos do YAML × campos `[SerializeField]` do .cs, incluindo a classe base `Interactable`.

  Teste negativo: com 5 erros injetados (referência inexistente, sprite inexistente, addedObject errado, filho fora de m_Children, campo com nome errado), ele acusa todos.
- **`placement.py`**: o spawn está livre. Um flood fill com a caixa dos pés do jogador (passo de 0,1 u) sobre todos os Box/Polygon do layout, dos pilares e dos limites alcança 120.997 células. Todos os triggers (5 interações, 2 perigos e 10 coletáveis) são alcançáveis. Os coletáveis ficam fora do calçamento, de colisores e de copas de árvore, e a ≥1,8 u dos perigos. Os perigos não invadem a estrada.
- **Prévia:** `(arquivo de trabalho) 04_preview.png` mostra o mapa inteiro com marcações (ciano = interação, vermelho = perigo, amarelo = coletável, verde = spawn, branco = câmera inicial). `04_preview_cam.png` é a vista limpa da câmera no spawn. Conferidas visualmente.
- **Código:** checagem estrutural (`(arquivo de trabalho)`) OK nos 42 arquivos. Revisão manual de toda a UI contra a API real do Core:
  - membros e assinaturas de eventos;
  - `UnityEngine.Random` sem `using System`;
  - `Color?` em condicional;
  - `string.CompareOrdinal`;
  - enums `HorizontalWrapMode`, `VerticalWrapMode` e `TextAnchor` (namespace UnityEngine);
  - `CanvasScaler.ScaleMode`/`ScreenMatchMode`;
  - `AddWidget<T> where T : HudWidget`.

  **Não há compilador C# neste container** (a instalação do .NET foi bloqueada pelo proxy), então a compilação da UI precisa ser confirmada aí.

## 6. Revisão do Core/Shared (sem mudanças)

Já compilaram aí. Revisei também o comportamento, e nada exigiu correção. Observações não bloqueantes, para uma versão futura:
- `HotbarScroll` é uma ação Value: dois "cliques" seguidos da roda com o mesmo valor em quadros consecutivos podem gerar só um `performed`. É raro e afeta só a roda do mouse.
- `ItemPickup` só tenta coletar em `OnTriggerEnter2D`. Se o inventário estava cheio, o jogador precisa sair e entrar de novo depois de liberar espaço.
- Na primeira interação o jogador nasce virado para o sul, então o prompt do poço só aparece depois de virar para o norte (W). Isso é esperado.

## 7. Checklist para o coordenador no editor

1. Copiar `Scripts/UI` (+ `UI.meta`), `Data` (+ `Data.meta`) e a cena com o `.meta` dela. Não copiar `.meta` de pastas que já existem.
2. Esperar a compilação: **Codexia.UI.dll sem erros**. Se houver erro, devolva a mensagem exata (arquivo e linha).
3. Abrir a cena. O Console não deve mostrar "Missing script", "Missing Prefab", "The referenced script is missing" nem "Unknown/unsupported serialized version" (sobretudo em CircleCollider2D/BoxCollider2D). Selecione "Limites" e um "Coleta - …" e confira no Inspector o raio, o offset e o isTrigger.
4. Hierarquia:
   - **Jogador**: TopDownCharacterController ausente e os 10 componentes em azul (adicionados); Rigidbody2D com gravity 0.
   - **Main Camera**: CameraFollow2D com o Target = Jogador.
   - **HUD**: HudBootstrap com o Player = Jogador.
   - **Global Light 2D**: "Target Sorting Layers" = todas.
5. Os 3 configs ligados (PlayerMovement/CharacterHealth/PlayerInventory) e nenhum aviso "sem …Config".
6. Play:
   - **Movimento**: WASD e setas, 4 direções de sprite, colisão com poço, estátua, árvores e limites; não dá para sair pela ponta da estrada.
   - **Câmera**: segue o jogador, sem tremor, e para nas bordas.
   - **HUD**: barra "HP 10/10", hotbar 1–9 e dica de controles visíveis, nítidas em 1080p (escala 3×) e 720p (2×).
   - **Coleta**: andar até a flor ao sul do spawn → toast "+1 Flor do campo" e ícone no slot 1. Pegar duas flores em menos de 2 s → "+2 Flor do campo".
   - **Perigo**: encostar num pilar rúnico (oeste) → "-2 Vida", recuo, véu vermelho, barra treme e o rastro desce. Repetir até ≤2 HP → a barra pulsa. Em 0 HP → "Você desmaiou...", reaparece no spawn com vida cheia.
   - **Poço**: virado para o poço → "[E] Beber" → diálogo com digitação; E completa a fala, E avança, "[E] fechar" na última fala → "+N Vida". Com a vida cheia → falas alternativas.
   - **Placas e estátua**: "[E] Ler" / "[E] Falar" e as falas corretas. Esc fecha a qualquer momento.
   - **Baú**: "[E] Abrir" → 2 falas → toasts "+2 Pote de água" e "+1 Fragmento rúnico", o sprite e a sombra trocam e o prompt vira "[E] Olhar" → "O baú está vazio."
   - **Inventário**: I/Tab abre o painel e o jogador para. WASD move o cursor e mostra nome e descrição. Q/F no pote cura 3 (ou mostra "Sua vida já está cheia"). Em outros itens: "Não é possível usar isso agora." I ou Esc fecha.
   - **Gamepad** (se houver): a dica e o prompt passam para "[A]".
7. Salvar a cena uma vez (Ctrl+S). O Unity reserializa: grava SceneRoots, remove `m_RootOrder` dos Transforms e reordena os campos. É normal e esperado.
