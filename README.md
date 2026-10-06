# AULA - Input Output e Artefatos no Workflow
Repositorio do curso DevOps Pro aula de Github e GitHub-Actions


# GitHub Actions — Gerenciamento de Inputs

> Guia prático baseado na transcrição da aula sobre gerenciamento de **Inputs** em workflows do GitHub Actions.

## 📌 Visão geral

Até este ponto, o workflow pode trabalhar com informações fornecidas pelo próprio GitHub Actions, incluindo:

- Contextos;
- Contexto `github`;
- Environment Variables;
- GitHub Actions Variables;
- Secrets.

Nesta etapa, o foco passa a ser trabalhar com **dados inseridos pelo usuário no momento em que o workflow é executado**.

Esse mecanismo é chamado de **Input**.

O uso de Inputs permite inserir informações dinamicamente em um workflow, tornando a automação mais reutilizável.

---

# 🎯 Por que utilizar Inputs?

Quando conseguimos inserir informações dinamicamente em um workflow ou pipeline, podemos reutilizar a mesma automação para diferentes situações.

Um exemplo apresentado na aula é um workflow de **deploy em diferentes ambientes**.

Em vez de criar um workflow separado para cada ambiente, podemos utilizar um Input para informar onde o deploy deverá ocorrer:

```text
Workflow de Deploy
       │
       ├── Desenvolvimento
       ├── Homologação
       └── Produção
```

O usuário escolhe ou informa o ambiente durante a execução.

Isso permite:

- reaproveitar automações;
- reduzir duplicação;
- economizar tempo;
- reduzir esforço;
- utilizar uma mesma pipeline para diferentes cenários.

---

# 🧩 Tipos de Inputs

A aula apresenta quatro tipos principais:

| Tipo | Finalidade |
|---|---|
| `string` | Texto |
| `number` | Número |
| `boolean` | Verdadeiro ou falso |
| `choice` | Seleção entre opções predefinidas |

---

## 📝 `string`

Utilizado para valores textuais.

```yaml
type: string
```

Exemplo:

```text
1.0.0
dev
release-2026
```

---

## 🔢 `number`

Utilizado para valores numéricos.

```yaml
type: number
```

No exemplo da aula, é utilizado para representar o número de um card ou tarefa:

```text
34
10
100
```

---

## ✅ `boolean`

Utilizado para valores verdadeiros ou falsos.

```yaml
type: boolean
```

Exemplo:

```text
true
false
```

Na aula, esse tipo é utilizado para representar se o deploy deve ou não ser executado.

---

## 📋 `choice`

O tipo `choice` apresenta opções previamente definidas para seleção.

Exemplo:

```yaml
type: choice
```

Com opções:

```yaml
options:
  - desenvolvimento
  - homologação
  - produção
```

Isso evita que o usuário informe livremente um valor que não corresponda a um ambiente válido.

---

# 🌎 Inputs e Environments

A aula também menciona a possibilidade de trabalhar com Inputs relacionados a **Environments**.

A ideia é permitir que ambientes como:

```text
desenvolvimento
homologação
produção
```

possuam comportamentos ou configurações específicas.

Esse assunto será tratado posteriormente em um capítulo específico.

Nesta etapa, o foco está nos quatro tipos:

```text
string
number
boolean
choice
```

---

# ⚙️ Parâmetros de um Input

Ao declarar um Input, alguns parâmetros são utilizados:

| Parâmetro | Finalidade |
|---|---|
| `type` | Define o tipo do Input |
| `required` | Define se é obrigatório |
| `default` | Define um valor padrão |
| `description` | Descreve o Input |
| `options` | Define as opções de um `choice` |

---

## `type`

Define o tipo de valor:

```yaml
type: string
```

```yaml
type: number
```

```yaml
type: boolean
```

```yaml
type: choice
```

---

## `required`

Define se o Input é obrigatório:

```yaml
required: true
```

---

## `default`

Define um valor padrão.

Exemplo:

```yaml
default: dev
```

Se o usuário não alterar o valor, o workflow utilizará:

```text
dev
```

---

## `description`

Define uma descrição para orientar o usuário:

```yaml
description: Versão da nova release
```

---

## `options`

Utilizado com `choice`:

```yaml
options:
  - desenvolvimento
  - homologação
  - produção
```

---

# 🚀 `workflow_dispatch`

No exemplo da aula, o workflow será executado manualmente.

Para isso é utilizado:

```yaml
on:
  workflow_dispatch:
```

O `workflow_dispatch` permite executar o workflow através da interface do GitHub.

É nesse momento que os Inputs aparecem para o usuário preencher.

---

# 🏗️ Estrutura básica

Os Inputs são declarados dentro de `workflow_dispatch`:

```yaml
name: Exemplo Input

run-name: Exemplo workflow para input

on:
  workflow_dispatch:
    inputs:
      ...
```

---

# 🧪 Exemplo completo

Um exemplo baseado na demonstração da aula:

```yaml
name: Exemplo Input

run-name: Exemplo workflow para input executado por ${{ github.actor }}

on:
  workflow_dispatch:
    inputs:

      versao:
        type: string
        required: true
        default: dev
        description: Versão da nova release

      card:
        type: number
        required: true
        description: Número do card da tarefa

      deploy:
        type: boolean
        required: true
        description: Deve fazer deploy?

      ambiente:
        type: choice
        required: true
        description: Ambiente de deploy
        options:
          - desenvolvimento
          - homologação
          - produção

jobs:
  valores-input:
    runs-on: ubuntu-latest

    steps:
      - name: Exibir valores dos inputs
        run: |
          echo "Esses são os valores passados nos inputs:"
          echo "Versão: ${{ inputs.versao }}"
          echo "Card: ${{ inputs.card }}"
          echo "Deve fazer deploy: ${{ inputs.deploy }}"
          echo "Ambiente: ${{ inputs.ambiente }}"
```

---

# 🔍 Acessando os Inputs

Depois que os Inputs são declarados, eles podem ser acessados através do contexto:

```yaml
inputs
```

Para acessar um valor individual:

```yaml
${{ inputs.nome_do_input }}
```

Exemplos:

```yaml
${{ inputs.versao }}
```

```yaml
${{ inputs.card }}
```

```yaml
${{ inputs.deploy }}
```

```yaml
${{ inputs.ambiente }}
```

---

# 📦 Contexto completo `inputs`

A aula também demonstra a utilização de `toJson` para visualizar o contexto completo.

Exemplo:

```yaml
- name: Exibir contexto dos inputs
  run: echo '${{ toJson(inputs) }}'
```

Isso permite visualizar as informações recebidas pelo workflow através do contexto `inputs`.

---

# 🖥️ Executando o workflow

Como foi utilizado:

```yaml
on:
  workflow_dispatch:
```

o workflow não será executado automaticamente.

Na interface do GitHub será disponibilizada a opção:

```text
Run workflow
```

Ao executar, o GitHub apresenta os Inputs definidos no YAML.

No exemplo da aula, são apresentados:

```text
Versão
Número do card
Deve fazer deploy
Ambiente de deploy
```

---

# 🧪 Exemplo de execução

Uma execução pode receber:

```text
Versão: 1.0.0
Número do card: 34
Fazer deploy: true
Ambiente: homologação
```

O workflow poderá exibir:

```text
Esses são os valores passados nos inputs:
Versão: 1.0.0
Card: 34
Deve fazer deploy: true
Ambiente: homologação
```

Em outra execução, podemos utilizar:

```text
Versão: dev
Número do card: 10
Fazer deploy: false
Ambiente: produção
```

O workflow permanece o mesmo; somente os valores de entrada são alterados.

---

# 👤 `run-name` e `github.actor`

A aula também apresenta a diferença entre:

```yaml
name:
```

e:

```yaml
run-name:
```

### `name`

É o nome do workflow:

```yaml
name: Exemplo Input
```

### `run-name`

É o nome específico de uma execução:

```yaml
run-name: Exemplo workflow para input executado por ${{ github.actor }}
```

O contexto:

```yaml
${{ github.actor }}
```

permite utilizar o usuário que executou o workflow.

---

# 🔄 Reutilização

Uma das principais vantagens dos Inputs é transformar um workflow fixo em uma automação parametrizada.

Sem Inputs, poderíamos ter:

```text
deploy-dev.yml
deploy-hml.yml
deploy-prod.yml
```

Com Inputs, podemos trabalhar com:

```text
deploy.yml
```

e escolher o ambiente durante a execução:

```text
desenvolvimento
homologação
produção
```

Representação:

```text
                 deploy.yml
                     │
             ┌───────┼───────┐
             │       │       │
             ▼       ▼       ▼
            DEV     HML     PROD
```

---

# 🛡️ `choice` para controlar valores

Quando existe uma lista conhecida de valores, o `choice` permite limitar as opções disponíveis.

Exemplo:

```yaml
ambiente:
  type: choice
  required: true
  description: Ambiente de deploy
  options:
    - desenvolvimento
    - homologação
    - produção
```

Assim, o usuário seleciona uma opção válida em vez de digitar qualquer valor.

---

# 🧩 Inputs em diferentes Jobs

Os Inputs pertencem ao contexto do workflow e podem ser utilizados a partir dos jobs.

Exemplo conceitual:

```text
Workflow
│
├── inputs
│   ├── versao
│   ├── card
│   ├── deploy
│   └── ambiente
│
├── Job 1
│   └── inputs.versao
│
├── Job 2
│   └── inputs.ambiente
│
└── Job 3
    └── inputs.deploy
```

O acesso continua sendo:

```yaml
${{ inputs.nome }}
```

---

# 🧠 Fluxo completo

```text
Usuário
   │
   ▼
Run workflow
   │
   ▼
Preenchimento dos Inputs
   │
   ├── Versão
   ├── Número do card
   ├── Deploy
   └── Ambiente
   │
   ▼
Workflow
   │
   ▼
Contexto inputs
   │
   ├── inputs.versao
   ├── inputs.card
   ├── inputs.deploy
   └── inputs.ambiente
   │
   ▼
Jobs / Steps
```

---

# 🛠️ Modelo de declaração

Um padrão de declaração pode ser:

```yaml
on:
  workflow_dispatch:
    inputs:

      nome_input:
        type: string
        required: true
        default: valor
        description: Descrição do Input
```

Para `choice`:

```yaml
ambiente:
  type: choice
  required: true
  description: Ambiente de deploy
  options:
    - desenvolvimento
    - homologação
    - produção
```

---

# ⚠️ Pontos importantes

## 1. O Input precisa ser declarado

Para utilizar:

```yaml
${{ inputs.versao }}
```

o Input precisa estar previamente declarado no workflow.

## 2. `workflow_dispatch` permite execução manual

O exemplo utiliza:

```yaml
on:
  workflow_dispatch:
```

Por isso, a execução é iniciada manualmente pela interface do GitHub.

## 3. Utilize `choice` quando os valores forem conhecidos

Quando existe uma lista controlada, `choice` ajuda a limitar as entradas.

## 4. Utilize `default` quando existir um valor padrão

Exemplo:

```yaml
default: dev
```

---

# 📋 Checklist

Antes de criar um workflow com Inputs:

- [ ] O workflow utiliza `workflow_dispatch` quando a execução será manual?
- [ ] Os Inputs estão dentro de `workflow_dispatch`?
- [ ] O `type` está correto?
- [ ] O Input é obrigatório?
- [ ] Existe um valor `default` quando necessário?
- [ ] A `description` explica o objetivo?
- [ ] Foi utilizado `choice` quando os valores são controlados?
- [ ] As `options` estão corretas?
- [ ] O workflow utiliza `${{ inputs.nome }}` para acessar os valores?
- [ ] O contexto `inputs` está sendo utilizado corretamente?

---

# 🚀 Aplicação em DevOps

Inputs são especialmente úteis em automações de DevOps porque permitem parametrizar uma mesma pipeline.

Exemplo:

```text
Deploy Application
       │
       ├── versão
       ├── card
       ├── executar deploy?
       └── ambiente
```

Uma execução:

```text
Versão: 1.0.0
Card: 34
Deploy: true
Ambiente: homologação
```

Outra execução:

```text
Versão: 1.1.0
Card: 42
Deploy: true
Ambiente: produção
```

O workflow permanece o mesmo e os parâmetros mudam conforme a necessidade.

---

# 📚 Resumo

Os **Inputs** permitem que um workflow receba informações dinamicamente no momento da execução.

Tipos apresentados:

```text
string
number
boolean
choice
```

Parâmetros principais:

```text
type
required
default
description
options
```

Declaração:

```yaml
workflow_dispatch:
  inputs:
```

Acesso:

```yaml
${{ inputs.nome_do_input }}
```

Visualização do contexto:

```yaml
${{ toJson(inputs) }}
```

---

# 🎯 Conceito principal

> **Inputs transformam workflows estáticos em automações parametrizadas e reutilizáveis.**

Em vez de criar uma automação diferente para cada situação, podemos criar um workflow único e permitir que o usuário informe os parâmetros necessários no momento da execução.

```text
                 WORKFLOW
                    │
             ┌──────┴──────┐
             │    INPUTS   │
             └──────┬──────┘
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
    Versão        Deploy      Ambiente
       │            │            │
       ▼            ▼            ▼
    1.0.0          true      homologação
                    │
                    ▼
                EXECUÇÃO
```

Essa abordagem aumenta o reaproveitamento das automações e reduz a necessidade de duplicar workflows.

---

## 📖 Referência

Conteúdo estruturado a partir da transcrição fornecida da aula sobre **Gerenciamento de Inputs no GitHub Actions**, preservando os conceitos, exemplos e nomenclatura apresentados na aula.
