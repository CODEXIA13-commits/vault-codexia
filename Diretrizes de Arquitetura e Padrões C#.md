---
tipo: documento
categoria: arquitetura-tecnica
projeto: CODEXIA
status: diretriz-registrada
versao: 0.1
idioma: pt-BR
tags:
  - codexia
  - csharp
  - arquitetura
  - padroes
---

# CODEXIA — Diretrizes de Arquitetura e Padrões de Programação C#

> Diretrizes para o desenvolvimento **futuro**. Nesta etapa **não** devem ser criadas pastas de código, Assembly Definitions, namespaces ou scripts próprios. O objetivo é registrar e compreender os padrões que serão usados quando a implementação for autorizada.

## 1. Organização arquitetural futura (proposta, não aprovada)

| Área | Responsabilidade |
|---|---|
| **Core** | Lógica de gameplay e regras do jogo. Nunca depende da UI. |
| **Shared** | Utilitários e componentes reutilizáveis, não exclusivos de uma funcionalidade. |
| **UI** | Elementos de interface e comunicação entre a interface e os sistemas de gameplay. |

- A UI pode ouvir eventos emitidos pelo Core e chamar métodos públicos ou interfaces dos sistemas de gameplay.
- O Core não conhece nem depende da UI.

### Restrição atual
- Não criar as pastas Core, Shared ou UI.
- Não criar Assembly Definitions.
- Não criar namespaces.
- Não criar scripts ou classes para preencher a estrutura.
- Não reorganizar arquivos existentes.
- Não modificar a estrutura original dos assets de terceiros.

Quando chegar o momento de implementar código próprio, deverá ser apresentada uma proposta detalhada (dependências entre assemblies, namespaces e responsabilidades) e aguardada a aprovação antes da execução.

## 2. ScriptableObjects

Priorizar ScriptableObjects quando forem adequados para:
- dados editáveis de itens, habilidades e equipamentos;
- configurações de personagens e sistemas;
- parâmetros de balanceamento;
- definições reutilizáveis de conteúdo;
- comportamentos configuráveis, quando houver justificativa arquitetural.

Não transformar tudo em ScriptableObject. Diferenciar **dados compartilhados**, **estado de execução** e **comportamento**, para evitar referências indevidas e alterações inesperadas durante a execução.

## 3. SOLID e composição

- Aplicar SRP, OCP, LSP, ISP e DIP.
- Priorizar composição em vez de heranças profundas.
- Usar interfaces quando realmente desacoplarem responsabilidades ou permitirem implementações intercambiáveis.
- Evitar abstrações, interfaces e camadas desnecessárias. Sem overengineering.

## 4. Responsabilidade e tamanho dos scripts

- Cada classe com uma responsabilidade bem definida.
- Scripts com cerca de **600 linhas ou mais** devem ser revisados quanto à separação de responsabilidades.
- Não dividir classes artificialmente só para reduzir linhas.
- Priorizar coesão, legibilidade e manutenção.

## 5. Convenções de nomenclatura

| Elemento | Convenção |
|---|---|
| Campos privados | `_camelCase` |
| Variáveis locais | `camelCase` |
| Parâmetros | `camelCase` |
| Métodos e propriedades públicas | `PascalCase` |
| Classes, interfaces e ScriptableObjects | `PascalCase` |
| Constantes | `PascalCase`, salvo justificativa |

```csharp
public class PlayerStats
{
    private float _currentHealth;
    private float _maximumHealth;

    public float CurrentHealth => _currentHealth;

    public void TakeDamage(float damageAmount)
    {
        float finalDamage = damageAmount;
        _currentHealth -= finalDamage;
    }
}
```

## 6. Comentários e documentação

- Comentários objetivos e úteis; resumos XML concisos.
- No máximo duas linhas por comentário ou resumo, de preferência.
- Não comentar o óbvio; explicar decisões complexas e comportamentos não evidentes.
- Nomes claros para reduzir a necessidade de comentários.

## 7. Regras de implementação futura

Quando a implementação for autorizada:
- respeitar a arquitetura aprovada;
- não alterar scripts ou arquivos originais de terceiros;
- não duplicar funcionalidades existentes;
- não criar dependências desnecessárias;
- verificar compatibilidade com Unity 6.3 e URP 2D;
- apresentar a arquitetura antes de implementar sistemas importantes;
- testar e relatar os resultados reais.

## 8. Regra de prioridade documental

Antes de qualquer decisão ou implementação:
1. Ler o [[CODEXIA - GDD|GDD]] atualizado.
2. Consultar os documentos técnicos relacionados.
3. Verificar as decisões já aprovadas.
4. Identificar conflitos ou informações ainda não definidas.
5. Apresentar propostas quando necessário e aguardar aprovação.

Se estas diretrizes conflitarem com alguma decisão aprovada no GDD, o conflito deve ser apresentado para avaliação, sem escolha unilateral.

## 9. Objetivo

Construir um projeto organizado, modular, sustentável e fácil de expandir, evitando decisões prematuras e retrabalho.

## 10. Observações de compatibilidade (registradas na adoção)

- O projeto usa **somente o novo Input System** (1.20.1). Código futuro de entrada deve usá-lo, não a API `Input` antiga.
- `Rigidbody2D.velocity` está obsoleta na Unity 6; usar `linearVelocity` em código próprio.
- Nenhum conflito encontrado entre estas diretrizes e as decisões atuais do GDD.

## 11. Histórico

- **0.1:** registro das diretrizes enviadas pelo usuário em 12/11/2026, sem criação de qualquer estrutura de código.
