# 🚀 CI/CD Workflows - Guia Rápido

## 📁 Arquivos Criados

### Estrutura
```
├── version.txt                                    # Controle de versão semântica
└── .github/
    └── workflows/
        ├── feature-to-develop.yml                 # Feature/Fix → Develop
        ├── develop-to-release.yml                 # Develop → Release
        ├── release-to-main.yml                    # Release → Main
        └── main-deploy.yml                        # Main Deploy
```

## 🔄 Fluxo GitFlow Automatizado

### Etapa 1: Feature/Fix → Develop
```bash
git checkout -b feature/minha-feature
# ou
git checkout -b fix/meu-fix
git push origin feature/minha-feature
```
**Resultado:** PR automática criada para `develop`

### Etapa 2: Develop → Release
```bash
# Após merge da PR para develop
```
**Resultado:** 
- Branch `release/v{versao}` criada automaticamente
- `version.txt` atualizado
- PR automática criada

### Etapa 3: Release → Main
```bash
# Após merge da PR para release
```
**Resultado:** PR automática criada para `main`

### Etapa 4: Main Deploy
```bash
# Após merge da PR para main
```
**Resultado:**
- Tag Git criada (ex: `v0.2.0`)
- GitHub Release publicado
- Build de produção executado

## 📦 Versionamento Semântico

| Tipo de Branch | Incremento | Exemplo |
|----------------|------------|---------|
| `feature/*` | Minor | 0.1.0 → 0.2.0 |
| `fix/*` | Patch | 0.1.0 → 0.1.1 |

## ✅ Checklist de Validações

Cada workflow executa:

### feature-to-develop.yml
- ✅ `dotnet restore`
- ✅ `dotnet build --configuration Debug`
- ✅ `dotnet test` (não bloqueia se falhar)
- ✅ Detecção de conflitos com develop
- ✅ Verificação de PRs duplicadas

### develop-to-release.yml
- ✅ Leitura de `version.txt`
- ✅ Incremento automático de versão
- ✅ Criação de branch release
- ✅ Atualização de `version.txt`
- ✅ `dotnet build --configuration Release`

### release-to-main.yml
- ✅ Extração de versão
- ✅ `dotnet restore`
- ✅ `dotnet build --configuration Release`
- ✅ `dotnet test` (não bloqueia se falhar)
- ✅ Verificação de PRs duplicadas

### main-deploy.yml
- ✅ `dotnet build --configuration Release`
- ✅ `dotnet test` (não bloqueia se falhar)
- ✅ Criação de tag Git
- ✅ Geração de changelog
- ✅ Criação de GitHub Release

## 🔑 Requisitos

### Estrutura do Projeto
- Projeto .NET em: `src/Edoha.Application/Edoha.Application.csproj`
- .NET SDK: 8.0.x

### Branches Necessárias
- `main` - Produção
- `develop` - Desenvolvimento
- `feature/**` - Novas funcionalidades
- `fix/**` - Correções de bugs
- `release/**` - Releases (criadas automaticamente)

### Permissões
Os workflows usam `GITHUB_TOKEN` automático (nenhuma configuração adicional necessária)

## 🛠️ Personalização

### Alterar Versão do .NET
Edite todos os workflows e modifique:
```yaml
- name: Setup .NET
  uses: actions/setup-dotnet@v4
  with:
    dotnet-version: '8.0.x'  # Altere aqui
```

### Alterar Caminho do Projeto
Edite todos os workflows e modifique:
```yaml
src/Edoha.Application/Edoha.Application.csproj  # Altere aqui
```

### Desabilitar Testes em Alguma Etapa
Remova ou comente o step:
```yaml
- name: Executar testes
  run: dotnet test ...
  continue-on-error: true
```

## 📝 Notas Importantes

1. **version.txt**: Mantém apenas o número da versão (ex: `0.1.0`), sem prefixo "v"
2. **Tags**: Criadas automaticamente com prefixo "v" (ex: `v0.1.0`)
3. **Testes**: Configurados com `continue-on-error: true` para não bloquear o fluxo
4. **PRs Duplicadas**: Workflows verificam e evitam criar PRs duplicadas
5. **Conflitos**: Detectados automaticamente e reportados nas PRs

## 🎯 Exemplos de Uso

### Criar nova feature
```bash
git checkout develop
git pull origin develop
git checkout -b feature/autenticacao-jwt
# ... fazer alterações ...
git add .
git commit -m "feat: add JWT authentication"
git push origin feature/autenticacao-jwt
# ✅ PR automática para develop será criada
```

### Criar correção de bug
```bash
git checkout develop
git pull origin develop
git checkout -b fix/validacao-email
# ... fazer alterações ...
git add .
git commit -m "fix: email validation regex"
git push origin fix/validacao-email
# ✅ PR automática para develop será criada
```

### Fazer release
1. Revise e aprove a PR para develop
2. Faça o merge → ✅ Branch `release/v*` criada automaticamente
3. Revise e aprove a PR para release
4. Faça o merge → ✅ PR para main criada automaticamente
5. Revise e aprove a PR para main
6. Faça o merge → ✅ Tag e Release criados automaticamente

## 🐛 Troubleshooting

### Workflow não executou
- Verifique se a branch segue o padrão correto (`feature/**`, `fix/**`, etc.)
- Confirme que as permissões do repositório permitem Actions

### Build falhou
- Verifique se o caminho do projeto está correto
- Confirme que o projeto existe em `src/Edoha.Application/`
- Verifique se a versão do .NET está correta

### PR não foi criada
- Verifique se já existe uma PR aberta da mesma branch
- Confirme que o `GITHUB_TOKEN` tem permissões adequadas
- Verifique os logs do workflow no GitHub Actions

### Versão não foi incrementada
- Confirme que a branch de origem tem prefixo `feature/` ou `fix/`
- Verifique se o arquivo `version.txt` existe e está no formato correto
- Revise os logs do workflow `develop-to-release.yml`

## 📚 Referências

- [GitHub Actions Documentation](https://docs.github.com/actions)
- [Semantic Versioning](https://semver.org/)
- [GitFlow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [.NET CLI Documentation](https://docs.microsoft.com/dotnet/core/tools/)
