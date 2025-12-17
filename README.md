# edoha-core

Sistema unificado multi-tenant de gerenciamento empresarial - API ASP.NET Core

## 🚀 CI/CD Workflows

Este repositório utiliza GitHub Actions para automatizar completamente o processo de integração e release seguindo uma abordagem GitFlow modificada com PRs automáticas.

### 📋 Fluxo Completo

```
feature/fix → develop → release/v* → main
     ↓            ↓          ↓         ↓
   PR Auto    PR Auto    PR Auto   Tag + Release
```

### 🔄 Workflows Disponíveis

#### 1. Feature/Fix → Develop (`feature-to-develop.yml`)

**Trigger:** Push em branches `feature/**` ou `fix/**`

**Ações:**
- Valida código com `dotnet build` e `dotnet test`
- Cria PR automática para `develop`
- Detecta conflitos
- Adiciona labels (`feature` ou `fix`)

#### 2. Develop → Release (`develop-to-release.yml`)

**Trigger:** Merge para `develop` de branches `feature/**` ou `fix/**`

**Ações:**
- Lê `version.txt` e incrementa versão
  - `feature/*`: incrementa **minor** (0.1.0 → 0.2.0)
  - `fix/*`: incrementa **patch** (0.1.0 → 0.1.1)
- Cria branch `release/v{versao}`
- Atualiza `version.txt` na branch release
- Valida com `dotnet build`
- Cria PR automática para a branch release

#### 3. Release → Main (`release-to-main.yml`)

**Trigger:** Merge para branch `release/**`

**Ações:**
- Extrai versão do `version.txt`
- Executa validação final com `dotnet build` e `dotnet test`
- Cria PR automática para `main`
- Adiciona label `production`

#### 4. Main Deploy (`main-deploy.yml`)

**Trigger:** Merge para `main` de branches `release/**`

**Ações:**
- Build de produção com `dotnet build --configuration Release`
- Executa testes
- Cria tag Git (ex: `v0.2.0`)
- Cria GitHub Release com changelog
- Exibe resumo do deploy

### 📦 Versionamento

O versionamento é controlado pelo arquivo `version.txt` na raiz do repositório.

**Formato:** `0.1.0` (apenas o número, sem prefixo "v")

**Incremento automático:**
- **Feature:** Minor version (0.1.0 → 0.2.0)
- **Fix:** Patch version (0.1.0 → 0.1.1)

### 🛠️ Requisitos

- .NET 8.0.x
- Estrutura do projeto: `src/Edoha.Application/Edoha.Application.csproj`
- Branches: `main`, `develop`, `feature/**`, `fix/**`, `release/**`

### 📝 Como Usar

1. **Criar uma feature:**
   ```bash
   git checkout develop
   git checkout -b feature/nova-funcionalidade
   # Faça suas alterações
   git push origin feature/nova-funcionalidade
   # ✅ PR automática criada para develop
   ```

2. **Criar um fix:**
   ```bash
   git checkout develop
   git checkout -b fix/correcao-bug
   # Faça suas alterações
   git push origin fix/correcao-bug
   # ✅ PR automática criada para develop
   ```

3. **Fazer merge para develop:**
   - Revise e aprove a PR
   - Faça o merge
   - ✅ Branch release criada automaticamente com versão incrementada
   - ✅ PR automática criada para a branch release

4. **Fazer merge para release:**
   - Revise a release
   - Faça o merge para a branch release
   - ✅ PR automática criada para main

5. **Fazer merge para main:**
   - Revise a PR de produção
   - Faça o merge para main
   - ✅ Tag criada automaticamente
   - ✅ GitHub Release publicado
   - ✅ Versão disponível em produção

### ⚡ Características

- ✅ PRs automáticas em cada etapa
- ✅ Versionamento semântico automático
- ✅ Build e testes em cada etapa
- ✅ Detecção de conflitos
- ✅ Changelog automático
- ✅ Tags Git automáticas
- ✅ GitHub Releases automáticos
- ✅ Verificação de PRs duplicadas

### 🔐 Permissões

Os workflows utilizam o `GITHUB_TOKEN` automático, sem necessidade de configurar secrets adicionais.