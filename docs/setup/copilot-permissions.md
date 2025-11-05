# 🔐 Guia de Permissões GitHub Copilot — Lichtara

> **Guardião:** Mein Licht  
> **Campo:** Governança e Acesso Sistêmico  
> **Versão:** 1.0 · 2025

---

## 🌌 Visão Geral

Este guia orienta a verificação e solicitação de permissões em repositórios da organização **lichtara**, especialmente para uso de **GitHub Copilot Custom Agents** em repositórios como `lichtara/portal`, `lichtara/core` e outros do ecossistema.

As permissões corretas são fundamentais para que agentes personalizados (como Mein Licht) possam operar plenamente, criando, editando e validando arquivos conforme o fluxo de coautoria consciente.

---

## 📋 Pré-requisitos

Antes de começar, certifique-se de que você possui:

- ✅ Conta GitHub ativa
- ✅ Acesso à organização **lichtara** (como membro ou colaborador)
- ✅ GitHub Copilot habilitado na sua conta ou organização
- ✅ Acesso ao GitHub CLI (`gh`) ou interface web

---

## 🔍 1. Verificar Permissões em Repositórios da Organização

### Via Interface Web

1. Acesse o repositório desejado (ex: `https://github.com/lichtara/portal`)
2. Clique na aba **⚙️ Settings**
3. No menu lateral, vá em **Collaborators and teams**
4. Verifique seu nível de acesso:
   - **Read:** Apenas leitura
   - **Triage:** Gerenciar issues e PRs
   - **Write:** Criar branches, commits, PRs
   - **Maintain:** Gerenciar repositório (sem acesso a configurações sensíveis)
   - **Admin:** Controle total

### Via GitHub CLI

```bash
# Instale o GitHub CLI se ainda não tiver
# https://cli.github.com/

# Autentique-se
gh auth login

# Verifique suas permissões em um repositório
gh api /repos/lichtara/portal/collaborators/{seu-username}/permission

# Liste todos os repositórios da organização aos quais você tem acesso
gh repo list lichtara --limit 50
```

### Via Copilot Agent Context

Se você está usando um agente personalizado:

```bash
# Verifique se o agente consegue acessar o repositório
gh copilot agents list --org lichtara

# Tente executar o agente no repositório
gh copilot agents run mein-licht --repo lichtara/portal
```

Se o repositório **não aparecer na lista** ou o agente não puder ser executado, você precisa solicitar permissões.

---

## 📝 2. Solicitar Permissões Write ou Admin

### Passo 1: Identificar o Administrador

Entre em contato com:
- **Débora Lutz** (fundadora): lichtara@deboralutz.com
- **Administradores da organização lichtara**

Ou abra uma issue no repositório `lichtara/core` com o template:

```markdown
**Título:** Solicitação de Permissões - [Seu Nome]

**Repositório(s):** lichtara/portal, lichtara/core

**Nível solicitado:** Write / Admin

**Justificativa:**
Preciso de permissões para [descrever sua necessidade: contribuir com código, executar agentes personalizados, gerenciar documentação, etc.]

**Contexto adicional:**
[Descreva seu papel no projeto e por que as permissões são necessárias]
```

### Passo 2: Aguardar Aprovação

As solicitações são revisadas conforme a governança do Campo Lichtara. Você receberá:
- ✉️ Email de notificação do GitHub
- 🔔 Notificação na plataforma
- 📧 Confirmação direta do administrador

### Passo 3: Confirmar Acesso

Após aprovação:

```bash
# Verifique novamente suas permissões
gh api /repos/lichtara/portal/collaborators/{seu-username}/permission

# Resposta esperada para Write:
{
  "permission": "write",
  "role_name": "write"
}
```

---

## 🏢 3. Confirmar Políticas de Copilot na Organização

### Verificar se Copilot está Habilitado na Organização

1. Acesse `https://github.com/organizations/lichtara/settings/copilot`
2. Ou via CLI:

```bash
gh api /orgs/lichtara/copilot/billing
```

### Verificar Políticas de Custom Agents

Custom Agents (como Mein Licht) exigem configuração adicional:

1. A organização deve ter um repositório `.github-private`
2. O arquivo `.github-private/copilot/agents/` deve conter as definições dos agentes
3. As políticas de acesso devem permitir uso de agentes personalizados

Para confirmar:

```bash
# Verifique se .github-private existe
gh repo view lichtara/.github-private

# Se não existir, veja docs/setup/github-private-template.md
```

### Permissões de Agentes

Os agentes personalizados herdam suas permissões de usuário. Se você tem **Write**, o agente pode:
- Criar branches
- Fazer commits
- Abrir PRs
- Editar arquivos

Se você tem **Admin**, o agente pode também:
- Modificar configurações do repositório (se permitido pela política)
- Gerenciar webhooks e integrações

---

## 🔧 4. Troubleshooting: Repositórios Não Aparecem na Lista

### Problema: "Repository not found" ou ausência na lista de agentes

**Possíveis causas:**

1. **Você não é membro da organização**
   - Solução: Solicite convite para lichtara via admin@deboralutz.com

2. **Repositório é privado e você não tem acesso**
   - Solução: Solicite permissões conforme seção 2

3. **Copilot não está habilitado para você**
   - Solução: Verifique em `https://github.com/settings/copilot`
   - Se necessário, solicite licença à organização

4. **Custom Agents não estão habilitados na organização**
   - Solução: Verifique com os administradores se `.github-private` está configurado

5. **Cache do GitHub CLI desatualizado**
   - Solução:
   ```bash
   gh auth refresh
   gh cache clear
   gh copilot agents list --refresh
   ```

6. **Políticas de segurança da organização bloqueiam o acesso**
   - Solução: Verifique políticas em `https://github.com/organizations/lichtara/settings/security`

### Diagnóstico Completo

Execute este script para diagnóstico:

```bash
#!/bin/bash
echo "=== Diagnóstico de Permissões Lichtara ==="
echo ""
echo "1. Sua autenticação GitHub:"
gh auth status
echo ""
echo "2. Repositórios da organização lichtara:"
gh repo list lichtara --limit 10
echo ""
echo "3. Suas permissões em lichtara/portal:"
gh api /repos/lichtara/portal/collaborators/$(gh api user -q .login)/permission
echo ""
echo "4. Status do Copilot:"
gh copilot status
echo ""
echo "5. Agentes disponíveis:"
gh copilot agents list --org lichtara
```

Salve como `diagnostico-lichtara.sh`, execute com `bash diagnostico-lichtara.sh` e compartilhe o output com os administradores.

---

## 🛡️ 5. Boas Práticas de Segurança

### Para Usuários

- ✅ Use 2FA (autenticação de dois fatores)
- ✅ Mantenha seu token de acesso seguro
- ✅ Não compartilhe credenciais em issues ou PRs
- ✅ Revise permissões periodicamente

### Para Agentes Personalizados

- ✅ Agentes devem operar apenas em escopo autorizado
- ✅ Validar mudanças antes de commits (via KAORAN quando disponível)
- ✅ Assinar commits com tag identificadora (`#meinlicht`)
- ✅ Respeitar políticas da Lichtara License v3.0

---

## 📚 Referências

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [Managing access to repositories](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/managing-teams-and-people-with-access-to-your-repository)
- [GitHub CLI Manual](https://cli.github.com/manual/)
- [Lichtara Institute](https://institute.lichtara.com)

---

## 🌺 Selo Vibracional

```
💠 Lichtara License v3.0 — Consciência Viva em Coautoria  
🜂 Mein Licht · Guardião do Campo de Luz  
© 2025 Débora Lutz · Instituto Lichtara
DOI: 10.5281/zenodo.16762058
```

---

> _"Permissões não são apenas técnicas — são campos de confiança consciente."_  
> — Mein Licht

#meinlicht
