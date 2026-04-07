# Tutorial_ManagedIdentitys_viaKeyVault
Passo a passo de como autenticar via managed identitys, sem utilização de secrets.
# 🔐 Lab: Acesso seguro a secrets com Managed Identity + Key Vault (Azure)

## 🎯 Objetivo

Demonstrar como uma aplicação no Azure acessa secrets **sem usar senha**, utilizando:

* Managed Identity
* RBAC
* Key Vault References

---

## 🧠 Arquitetura

```
App Service
   ↓ (Managed Identity)
Microsoft Entra ID
   ↓
Azure Key Vault
   ↓
Secret (injetado como variável de ambiente)
```

---

## 🧱 Pré-requisitos

* Conta no Azure
* Resource Group criado
* Permissão para criar recursos

---

## 🚀 Passo 1 — Criar o Key Vault

1. Criar um Key Vault
2. **Importante:** usar modelo de permissão **RBAC** (não Access Policy)

---

## 🔑 Passo 2 — Criar um Secret

* Nome: `teste-secret`
* Valor: `123456`

Verifique se o secret aparece corretamente na lista.

---

## 🌐 Passo 3 — Criar o App Service

* Runtime: qualquer (Node.js, por exemplo)
* Plano: Free ou Basic
* Região: mesma do Key Vault (recomendado)

> ⚠️ Dica: Ignore configurações de deploy (Basic Auth, CI/CD, etc.)

---

## ⚡ Passo 4 — Ativar Managed Identity

1. Acesse o App Service
2. Menu lateral → **Identity**
3. Aba: **System assigned**
4. Ativar → **ON**
5. Salvar

> ⚠️ Atenção: Essa opção fica "escondida" e NÃO está no IAM.

---

## 🔐 Passo 5 — Dar acesso ao Key Vault

1. Acesse o Key Vault
2. Vá em **Access control (IAM)**
3. Clique em **Add role assignment**

Configuração:

* Role: `Key Vault Secrets User`
* Assign access to: **Managed Identity**
* Selecionar: App Service

> ⏱️ Pode levar alguns segundos para propagar

---

## ⚙️ Passo 6 — Configurar variável no App Service

1. Acesse o App Service
2. Vá em **Environment variables** (nova UI do Azure)
3. Clique em **Add**

### Configuração:

**Name:**

```
SECRET_TESTE
```

**Value:**

```
@Microsoft.KeyVault(SecretUri=https://SEU-KEYVAULT.vault.azure.net/secrets/teste-secret)
```

> ⚠️ NÃO usar URL do portal.azure.com

---

## 💾 Passo 7 — Salvar e reiniciar

* Salvar configurações
* O App Service será reiniciado automaticamente

---

## 🧪 Teste

* Verifique se não há erro na variável
* O valor será resolvido automaticamente pelo Azure

---

## 🧠 Conceitos importantes

### 🔑 Managed Identity

Permite que o App Service se autentique sem usar credenciais.

---

### 🔐 RBAC

Controle de acesso baseado em roles no nível do Key Vault.

---

### 📦 Key Vault Reference

Permite injetar secrets como variáveis de ambiente sem código.

---

## ⚠️ Erros comuns

### ❌ Secret não encontrado

* Nome incorreto
* URL mal formatada

---

### ❌ Forbidden / Access Denied

* Role não atribuída corretamente
* Tempo de propagação

---

### ❌ Secret "sumiu"

* Falta de permissão (RBAC)
* Não significa que foi deletado

---

## 💡 Boas práticas

* Não usar secrets hardcoded
* Preferir Managed Identity sempre que possível
* Separar Key Vault por ambiente (dev/prod)

---

## 💬 Conclusão

Este lab demonstra como eliminar credenciais estáticas e implementar um modelo seguro baseado em identidade no Azure.

---

