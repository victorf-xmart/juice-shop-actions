# XGuardian Security Scan Action 🔍

Este GitHub Action executa varreduras de segurança automatizadas usando o XGuardian, permitindo análises de segurança contínuas diretamente nos seus workflows do GitHub. Integre facilmente análises de vulnerabilidades no seu pipeline de desenvolvimento.

> **Recursos disponíveis:**
>
> - 🛡️ **SAST** (Static Application Security Testing): Análise estática do código-fonte
> - 📦 **SCA** (Software Composition Analysis): Análise de dependências e componentes
> - 🌐 **DAST** (Dynamic Application Security Testing): Análise dinâmica de aplicações web em execução
> - 📊 **Relatórios detalhados**: Visualize vulnerabilidades e receba recomendações de correção
> - 📩 **Notificações**: Integre com Microsoft Teams ou Slack para receber atualizações sobre os resultados dos scans

## Tópicos 📚

- [XGuardian Security Scan Action 🔍](#xguardian-security-scan-action-)
  - [Tópicos 📚](#tópicos-)
  - [Pré-requisitos 📋](#pré-requisitos-)
    - [Credenciais Necessárias](#credenciais-necessárias)
    - [Parâmetros de Configuração](#parâmetros-de-configuração)
      - [Configurações da Aplicação](#configurações-da-aplicação)
      - [Configurações SAST](#configurações-sast)
      - [Configurações SCA](#configurações-sca)
      - [Configurações DAST](#configurações-dast)
      - [Configurações Adicionais](#configurações-adicionais)
  - [Exemplos de Uso 🚀](#exemplos-de-uso-)
    - [Exemplo Básico](#exemplo-básico)
    - [Exemplo de Scan SAST](#exemplo-de-scan-sast)
    - [Exemplo de Scan SCA](#exemplo-de-scan-sca)
    - [Exemplo de Scan DAST](#exemplo-de-scan-dast)
    - [Exemplo Combinando Múltiplos Scans](#exemplo-combinando-múltiplos-scans)
    - [Exemplo em Pull Requests](#exemplo-em-pull-requests)
  - [Outputs Disponíveis 📤](#outputs-disponíveis-)
    - [Uso dos Outputs](#uso-dos-outputs)
    - [Integração com Microsoft Teams](#integração-com-microsoft-teams)
    - [Integração com Slack](#integração-com-slack)
  - [Modo de Desenvolvimento](#modo-de-desenvolvimento)
  - [Suporte](#suporte)

## Pré-requisitos 📋

### Credenciais Necessárias

> **Simplificado!** Agora você precisa apenas de credenciais básicas para começar a usar o XGuardian.

| Segredo        | Descrição                              | Obrigatório |
| -------------- | -------------------------------------- | :---------: |
| `API_EMAIL`    | Email de acesso à plataforma XGuardian |     ✅      |
| `API_PASSWORD` | Senha de acesso à plataforma XGuardian |     ✅      |

Para adicionar esses segredos ao seu repositório:

1. Acesse as configurações do seu repositório
2. Navegue até "Secrets and variables" > "Actions"
3. Clique em "New repository secret"
4. Adicione cada segredo com seu respectivo valor

[📚 Documentação oficial sobre segredos no GitHub Actions](https://docs.github.com/pt/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository)

### Parâmetros de Configuração

> Os parâmetros são configuráveis conforme sua necessidade. Quando não especificados, valores padrão serão utilizados.

#### Configurações da Aplicação

| Parâmetro            | Descrição                                  | Valor Padrão                                               |
| -------------------- | ------------------------------------------ | ---------------------------------------------------------- |
| `app_name`           | Nome da aplicação                          | Nome do repositório                                        |
| `team_id`            | ID(s) da(s) equipe(s) no formato JSON      | `[1]`                                                      |
| `languages`          | Linguagem(ns) da aplicação no formato JSON | `["JavaScript"]`                                           |
| `description`        | Descrição da aplicação                     | `"Aplicação criada através do GitHub Actions - XGuardian"` |
| `microservices`      | Indica se a aplicação possui microserviços | `"false"`                                                  |
| `microservices_data` | Dados dos microserviços no formato JSON    | `[{"name": "MS1", "language": ["JavaScript"]}]`            |

#### Configurações SAST

| Parâmetro     | Descrição           | Valor Padrão |
| ------------- | ------------------- | ------------ |
| `sast`        | Ativa o scan SAST   | `"false"`    |
| `policy_sast` | ID da política SAST | `0`          |

#### Configurações SCA

| Parâmetro    | Descrição          | Valor Padrão |
| ------------ | ------------------ | ------------ |
| `sca`        | Ativa o scan SCA   | `"false"`    |
| `policy_sca` | ID da política SCA | `0`          |

#### Configurações DAST

| Parâmetro        | Descrição                              | Valor Padrão |
| ---------------- | -------------------------------------- | ------------ |
| `dast`           | Ativa o scan DAST                      | `"false"`    |
| `policy_dast`    | ID da política DAST                    | `0`          |
| `site_url`       | URL completa do site para análise DAST | `""`         |
| `auth_url`       | URL da página de login                 | `""`         |
| `logout_url`     | URL da página de logout                | `""`         |
| `auth_exist`     | Indica se o site requer autenticação   | `false`      |
| `user_login`     | Nome de usuário para autenticação      | `""`         |
| `password_login` | Senha para autenticação                | `""`         |

#### Configurações Adicionais

| Parâmetro        | Descrição                               | Valor Padrão |
| ---------------- | --------------------------------------- | ------------ |
| `translate`      | Traduz o relatório para português       | `"false"`    |
| `exclude`        | Padrões a serem excluídos do scan       | `""`         |
| `pdf`            | Gera relatório PDF detalhado            | `"false"`    |
| `scan_directory` | Diretório específico para análise       | `.`          |
| `get_scan_id`    | Busca o ID do scan após o upload        | `"false"`    |
| `save_vulns`     | Salva vulnerabilidades no banco         | `"false"`    |
| `is_development` | Usa URLs de ambiente de desenvolvimento | `"false"`    |

## Exemplos de Uso 🚀

### Exemplo Básico

```yaml
name: XGuardian Security Scan
on:
  push:
    branches: [main]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Executar scan de segurança
        uses: xmart-xguardian/xguardian-actions@main
        with:
          api_email: ${{ secrets.API_EMAIL }}
          api_password: ${{ secrets.API_PASSWORD }}
          app_name: ${{ github.event.repository.name }}
          sast: "true"
```

### Exemplo de Scan SAST

```yaml
- name: XGuardian SAST Scan
  uses: xmart-xguardian/xguardian-actions@main
  with:
    api_email: ${{ secrets.API_EMAIL }}
    api_password: ${{ secrets.API_PASSWORD }}
    app_name: "minha-aplicacao"
    team_id: "[1]"
    languages: '["JavaScript", "TypeScript"]'
    description: "Aplicação web front-end"
    sast: "true"
    policy_sast: "0"
    scan_directory: "src"
    pdf: "true"
```

### Exemplo de Scan SCA

```yaml
- name: XGuardian SCA Scan
  uses: xmart-xguardian/xguardian-actions@main
  with:
    api_email: ${{ secrets.API_EMAIL }}
    api_password: ${{ secrets.API_PASSWORD }}
    app_name: "minha-aplicacao"
    sca: "true"
    policy_sca: "0"
    scan_directory: "."
    exclude: "node_modules/,dist/,tests/"
```

### Exemplo de Scan DAST

```yaml
- name: XGuardian DAST Scan
  uses: xmart-xguardian/xguardian-actions@main
  with:
    api_email: ${{ secrets.API_EMAIL }}
    api_password: ${{ secrets.API_PASSWORD }}
    app_name: "meu-website"
    dast: "true"
    policy_dast: "0"
    site_url: "https://meu-site.com"
    auth_url: "https://meu-site.com/login"
    logout_url: "https://meu-site.com/logout"
    auth_exist: true
    user_login: "usuario_teste"
    password_login: ${{ secrets.SITE_PASSWORD }}
```

### Exemplo Combinando Múltiplos Scans

```yaml
- name: XGuardian Full Security Scan
  id: xguardian-scan
  uses: xmart-xguardian/xguardian-actions@main
  with:
    api_email: ${{ secrets.API_EMAIL }}
    api_password: ${{ secrets.API_PASSWORD }}
    app_name: "aplicacao-completa"
    team_id: "[1]"
    languages: '["JavaScript", "Python"]'

    # Habilitar múltiplos tipos de scan
    sast: "true"
    sca: "true"
    dast: "true"

    # Configurações DAST
    site_url: "https://meu-site.com"
    auth_exist: false

    # Configurações adicionais
    pdf: "true"
    translate: "true"
    get_scan_id: "true"
```

### Exemplo em Pull Requests

Configure o scan para executar em pull requests, ajudando a garantir que novo código não introduza vulnerabilidades:

```yaml
name: XGuardian Security Check
on:
  pull_request:
    branches: [main, develop]
    paths-ignore:
      - "**.md"
      - "docs/**"

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Importante para análises que comparam com versões anteriores

      - name: XGuardian Security Scan
        id: xguardian
        uses: xmart-xguardian/xguardian-actions@main
        with:
          api_email: ${{ secrets.API_EMAIL }}
          api_password: ${{ secrets.API_PASSWORD }}
          app_name: ${{ github.event.repository.name }}
          sast: "true"
          sca: "true"
          get_scan_id: "true"

      - name: Comentar no Pull Request
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v6
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `## 🔍 Análise de Segurança XGuardian
              
              A análise de segurança foi concluída para este PR.
              
              - **App ID**: ${{ steps.xguardian.outputs.app_id }}
              - **Scan ID**: ${{ steps.xguardian.outputs.scan_id }}
              - **Versão**: ${{ steps.xguardian.outputs.scan_version }}
              
              [📊 Ver Resultados Completos](${{ steps.xguardian.outputs.scan_url }})`
            })
```

## Outputs Disponíveis 📤

| Output       | Descrição                                               |
| ------------ | ------------------------------------------------------- |
| app_id       | ID da aplicação no XGuardian                            |
| scan_id      | ID do scan executado                                    |
| scan_url     | URL para visualizar os resultados do scan               |
| scan_version | Nome/versão do scan (nome da aplicação + SHA do commit) |

### Uso dos Outputs

```yaml
- name: XGuardian Security Scan
  id: xguardian
  uses: xmart-xguardian/xguardian-actions@main
  with:
    api_email: ${{ secrets.API_EMAIL }}
    api_password: ${{ secrets.API_PASSWORD }}
    app_name: "minha-aplicacao"
    sast: "true"
    get_scan_id: "true"

- name: Verificar resultados
  run: |
    echo "✅ Scan iniciado com sucesso!"
    echo "🆔 App ID: ${{ steps.xguardian.outputs.app_id }}"
    echo "📝 Scan ID: ${{ steps.xguardian.outputs.scan_id }}"
    echo "🔖 Versão: ${{ steps.xguardian.outputs.scan_version }}"
    echo "📊 Resultados: ${{ steps.xguardian.outputs.scan_url }}"
```

### Integração com Microsoft Teams

```yaml
- name: Notificar no Microsoft Teams
  uses: aliencube/microsoft-teams-actions@v0.8.0
  with:
    webhook_uri: ${{ secrets.MS_TEAMS_WEBHOOK_URI }}
    title: "Scan de Segurança XGuardian"
    summary: "Resultados do scan de segurança concluído"
    theme_color: "0076D7"
    sections: |
      [{
        "activityTitle": "Scan de Segurança Finalizado",
        "activitySubtitle": "${{ github.repository }} - ${{ github.ref_name }}",
        "facts": [
          { "name": "Status", "value": "${{ job.status }}" },
          { "name": "App ID", "value": "${{ steps.xguardian.outputs.app_id }}" },
          { "name": "Scan ID", "value": "${{ steps.xguardian.outputs.scan_id }}" },
          { "name": "Versão", "value": "${{ steps.xguardian.outputs.scan_version }}" }
        ],
        "potentialAction": [
          {
            "@type": "OpenUri",
            "name": "Ver Resultados",
            "targets": [{ "os": "default", "uri": "${{ steps.xguardian.outputs.scan_url }}" }]
          }
        ]
      }]
```

### Integração com Slack

```yaml
- name: Notificar no Slack
  uses: rtCamp/action-slack-notify@v2
  env:
    SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
    SLACK_TITLE: "XGuardian Security Scan"
    SLACK_MESSAGE: |
      *Scan de segurança concluído*
      • Repositório: ${{ github.repository }}
      • Branch: ${{ github.ref_name }}
      • App ID: ${{ steps.xguardian.outputs.app_id }}
      • Scan ID: ${{ steps.xguardian.outputs.scan_id }}
      • Resultados: ${{ steps.xguardian.outputs.scan_url }}
    SLACK_COLOR: ${{ job.status == 'success' && 'good' || 'danger' }}
```

## Modo de Desenvolvimento

Para utilizar o ambiente de desenvolvimento da plataforma XGuardian, adicione o parâmetro is_development: "true" à configuração do action. Isso redirecionará as requisições para o ambiente de desenvolvimento em vez do ambiente de produção.

```yaml
- name: XGuardian Security Scan (Dev)
  uses: xmart-xguardian/xguardian-actions@main
  with:
    api_email: ${{ secrets.API_EMAIL }}
    api_password: ${{ secrets.API_PASSWORD }}
    is_development: "true"
    app_name: "teste-dev"
    sast: "true"
```

## Suporte

Para dúvidas ou problemas relacionados a este action, entre em contato com a equipe de suporte XGuardian ou abra uma issue no repositório do GitHub.
