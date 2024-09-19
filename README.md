# GitHub Actions - Merge Automático com Pull Request

Este repositório contém um workflow de GitHub Actions que automatiza o processo de criação, aprovação e mesclagem de pull requests da branch `main` para a branch `homolog`. O objetivo é simplificar a integração contínua das mudanças na branch de desenvolvimento.

## Índice

- [Visão Geral](#visão-geral)
- [Pré-requisitos](#pré-requisitos)
- [Configuração](#configuração)
  - [1. Criar um Personal Access Token (PAT)](#1-criar-um-personal-access-token-pat)
  - [2. Adicionar o PAT como um Secret](#2-adicionar-o-pat-como-um-secret)
- [Como Funciona](#como-funciona)
- [Configuração do Workflow](#configuração-do-workflow)
- [Licença](#licença)
- [Contato](#contato)

## Visão Geral

Este workflow de GitHub Actions é acionado automaticamente sempre que há um push na branch `main`. Ele realiza as seguintes etapas:

1. Faz o checkout do código.
2. Cria um pull request da branch `main` para a branch `homolog`.
3. Mescla o pull request automaticamente, utilizando o usuário `github-actions[bot]`.

## Pré-requisitos

Antes de começar, você precisará:

1. Um repositório no GitHub com as branches `main` e `homolog`.
2. Permissões de escrita para criar e mesclar pull requests no repositório.
3. GitHub CLI (`gh`) disponível no ambiente de execução do GitHub Actions (já incluído por padrão).

## Configuração

### 1. Criar um Personal Access Token (PAT)

- Vá para [Configurações de Tokens do GitHub](https://github.com/settings/tokens).
- Crie um novo token com as seguintes permissões:
  - `repo` (Acesso completo ao repositório).
  - `workflow` (Gerenciar workflows do GitHub Actions).
  - `write:repo_hook` (Gerenciar webhooks e serviços do repositório).

### 2. Adicionar o PAT como um Secret

1. No seu repositório GitHub, vá para `Settings` > `Secrets and variables` > `Actions`.
2. Clique em `New repository secret`.
3. Nomeie o secret como `PERSONAL_ACCESS_TOKEN` e cole o token criado na etapa anterior.

## Como Funciona

O workflow consiste nos seguintes passos:

1. **Checkout do Código:** O GitHub Actions faz o checkout do código na branch `main`.
2. **Configuração do Git:** Configura as informações de usuário para o commit.
3. **Busca por Branches:** Faz um fetch de todas as branches do repositório.
4. **Criação de Pull Request:** Cria um pull request da branch `main` para a branch `homolog` utilizando a GitHub CLI.
5. **Mescla do Pull Request:** Obtém o número do pull request criado e mescla o PR utilizando a opção `--squash`.

## Configuração do Workflow

O arquivo de workflow (`.github/workflows/auto-merge.yml`) está configurado da seguinte forma:

```yaml
name: Auto Create and Approve PR

on:
  push:
    branches:
      - main # Branch de origem

jobs:
  create-and-merge-pr:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Setup Git
      run: |
        git config --global user.name 'github-actions[bot]'
        git config --global user.email 'github-actions[bot]@users.noreply.github.com'

    - name: Fetch all branches
      run: git fetch --all

    - name: Create Pull Request
      id: create_pr
      run: |
        # Utilizando a GitHub CLI para criar o PR
        gh pr create --base homolog --head main --title "Auto Merge: main -> homolog" --body "Merge automático de main para homolog."
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

    - name: Approve and Merge Pull Request
      id: merge_pr
      run: |
        # Extrair o número do PR criado
        PR_NUMBER=$(gh pr list --state open --head main --base homolog --json number -q '.[0].number')

        # Mesclar o PR
        gh pr merge $PR_NUMBER --squash --admin
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Explicação dos Passos:

1. **Evento de Push:** O workflow é acionado quando há um push na branch `main`.
2. **Criação e Mesclagem do PR:** Cria automaticamente um pull request e mescla as mudanças na branch `homolog`.

## Licença

Este projeto está licenciado sob a [MIT License](LICENSE).

## Contato

Para mais informações, entre em contato:

- **Nome:** Renan Balthazar
- **Email:** rlb.desenvolvimento@gmail.com
- **LinkedIn:** [Seu LinkedIn](https://www.linkedin.com/in/renan-balthazar-32b874169/)
