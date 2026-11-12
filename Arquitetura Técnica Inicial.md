---
tipo: documento
categoria: arquitetura-tecnica
projeto: CODEXIA
status: proposta-inicial
versao: 0.3
idioma: pt-BR
tags:
  - codexia
  - unity
  - arquitetura
  - urp-2d
---

# CODEXIA — Arquitetura Técnica Inicial

> Proposta de organização para o projeto Unity 6.3 com URP 2D. Não contém implementação de gameplay. A estrutura deverá ser revisada depois da inspeção no editor e antes de criar sistemas próprios.

## 1. Ambiente aprovado

- Unity 6.3.
- Universal Render Pipeline (URP).
- 2D Renderer.
- C#.
- Projeto criado a partir do template 2D (URP).

### 1.1 Ambiente verificado (12/11/2026)

- Editor: Unity 6.3 LTS **6000.3.28f1**; editor de C#: Visual Studio Community 2026.
- Projeto: `V:\Programação\CODEXIA Unity\CODEXIA` (template Universal 2D, projeto local).
- URP 17.3.0; `UniversalRP.asset` em todos os níveis de qualidade; renderer padrão `Renderer2D.asset`.
- Espaço de cor Linear; entrada pelo novo Input System (1.20.1), apenas.
- Pacote principal importado em `Assets/Cainos/Pixel Art Top Down - Basic/`, sem ProjectSettings de origem.
- Pasta própria criada com conteúdo real: `Assets/CODEXIA/Scenes/Validation/` (cena "Validacao - Area Inicial"). Nenhuma pasta de código foi criada.
- Camadas: Sorting Layers e camadas de física do pacote (Layer 1–3) importadas do `TagManager.asset` original.
- Renderer 2D: Transparency Sort Mode Custom Axis (0, 1, 1), igual ao do pacote; aguarda confirmação.
- Builds: `V:\Programação\CODEXIA Unity\Builds\`, fora da pasta do projeto.
- Padrões de código futuro: [[Diretrizes de Arquitetura e Padrões C#]].

## 2. Princípios

1. Separar recursos originais de terceiros dos arquivos próprios de CODEXIA.
2. Preservar o pacote original sem editar seus arquivos.
3. Evitar scripts e sistemas antes da definição de arquitetura.
4. Manter cenas demonstrativas do pacote como referência de inspeção, sem convertê-las diretamente nas cenas finais.
5. Construir a primeira cena própria com recursos existentes e adequados.
6. Não criar arte nova nem placeholders quando já houver assets adequados.

## 3. Estrutura de pastas proposta

A estrutura abaixo é uma proposta para as pastas próprias do projeto. A pasta do pacote deverá manter o caminho original após importação.

```text
Assets/
├── Cainos/
│   └── Pixel Art Top Down - Basic/
│       └── (conteúdo original do pacote — preservar)
└── CODEXIA/
    ├── Scenes/
    │   └── Validation/
    ├── Settings/
    ├── Materials/
    ├── Prefabs/
    ├── Tilemaps/
    ├── Scripts/
    └── Documentation/
```

As pastas vazias só deverão ser criadas quando houver conteúdo real para colocar nelas. Não é necessário criar scripts ou prefabs vazios para preencher a estrutura.

## 4. Cenas previstas

- Cena de validação ambiental: primeira cena própria, construída com tiles e prefabs existentes.
- Cenas futuras de regiões: não especificadas.
- Cenas de demonstração do pacote: manter como referência original; não salvar alterações sobre elas.

## 5. Renderização e pixel art

- URP com 2D Renderer.
- Preservar os metadados de importação originais do pacote.
- Validar o filtro Point, transparência, pixels por unidade e compressão sem sobrescrever fontes.
- Avaliar Pixel Perfect Camera apenas depois de medir os recursos e definir a escala; sua adoção não está decidida.
- Não assumir que todos os sprites possuem 32×32 px. A grade 32×32 do GDD é referência de organização, não tamanho obrigatório de todo recurso.

## 6. Organização visual a validar

- Tilemaps por função, conforme o conjunto de tiles existente.
- Sorting Layers e ordem de desenho de acordo com os prefabs reais.
- Sorting Groups quando necessário e comprovado pela composição.
- Colisores e limites de navegação apenas após avaliar cada asset.
- Câmera ortográfica top-down; tamanho e enquadramento pendentes de teste.

Nenhum valor final de pixels por unidade, câmera ou sorting order está fixado neste documento.

## 7. Dependência de assets

Pacote principal: **Pixel Art Top Down - Basic v1.2.3** — inventariado no [[Catálogo de Assets]].

Pacote complementar de água: **Top Down Kingdom 2D** — aguardando envio e inspeção.

A seleção final de assets da cena de validação deverá ser feita com base no catálogo e na inspeção no Unity, não por suposição baseada apenas nos nomes.

## 8. Próximas validações

1. Criar projeto pelo template 2D (URP).
2. Confirmar URP Asset e 2D Renderer ativos.
3. Importar o pacote original sem modificações.
4. Verificar materiais, shaders e renderização.
5. Conferir propriedades de sprites e tiles.
6. Inspecionar cenas e prefabs de demonstração.
7. Documentar os valores reais de escala, sorting, pivôs e colisores.
8. Atualizar esta arquitetura e o GDD antes de implementar scripts próprios.

## 9. Fora do escopo atual

- Arquitetura de combate, inventário, missões ou progressão.
- Implementação do protagonista.
- Sistemas de salvamento.
- Geração procedural em escala de mundo.
- Scripts próprios de gameplay.
- Arte nova.

## 10. Histórico

- **0.1:** proposta inicial de arquitetura para Unity 6.3 com URP 2D.
- **0.3:** pasta de cenas própria, camadas, ordenação e diretrizes de C#.
- **0.2:** registro do ambiente verificado após a criação do projeto e a importação do pacote.
