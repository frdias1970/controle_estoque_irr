# 🏥 Sistema de Controle de Estoque Pro

Aplicação web moderna para gestão automatizada de inventário hospitalar e insumos gerais, substituindo planilhas e formulários em papel.

---

## 📋 Sumário Executivo

| Aspecto | Descrição |
|---------|-----------|
| **Propósito** | Gestão automatizada de estoque com rastreabilidade completa |
| **Arquitetura** | Client-side com sincronização em nuvem |
| **Armazenamento** | JSONBin.io (remoto) + localStorage (local) |
| **Usuários** | 3 perfis: Consulta, Operador e Administrador |
| **Status** | Funcional e pronto para produção com melhorias de segurança |

---

## 🏗️ Stack Tecnológico

### Frontend
- **Interface**: HTML5, CSS3, JavaScript ES6+
- **Design**: Responsivo (desktop, tablet, mobile)
- **Temas**: Claro e escuro

### Backend & Persistência
- **API Remota**: JSONBin.io (REST)
- **Armazenamento Local**: Web Storage API
- **Fallback**: 216 produtos embutidos (contingência)

---

## 👥 Perfis de Usuário & Permissões

### 🔍 Consulta / Visitante
Visualização apenas (read-only)
- ✅ Dashboards e indicadores
- ✅ Tabelas de estoque
- ✅ Relatórios
- ✅ Histórico de pedidos

### ⚙️ Operador
Consulta + movimentação básica
- ✅ Tudo do Consulta
- ✅ Registrar entradas/saídas/devoluções
- ✅ Criar pedidos de materiais

### 🔐 Administrador
Acesso completo
- ✅ Tudo do Operador
- ✅ Cadastrar/editar/deletar produtos
- ✅ Aprovar/rejeitar pedidos
- ✅ Gerenciar categorias
- ✅ Configurar API e senhas
- ✅ Gerenciar logs

---

## 📊 Modelo de Dados

### Entidades Principais

#### **Produtos**
```
id, nome, categoria, unidade, estoque_min, estoque_max, 
saldo_atual, preco_custo, preco_venda, fornecedor, 
localizacao, observacoes
```

#### **Movimentações**
```
id, produtoId, tipo (ENTRADA/SAÍDA/DEVOLUÇÃO/AJUSTE), 
qtd, data, usuario, destino, nf, obs
```

#### **Pedidos**
```
id, data, solicitante, setor, status (PENDENTE/APROVADO/REJEITADO),
itens[], aprovadoPor, dataAprovacao
```

#### **Logs**
```
id, data, usuario, acao, detalhes
```

---

## 🎯 Módulos Funcionais

### 1️⃣ Dashboard
Painel com KPIs em tempo real:
- Total de produtos cadastrados
- Itens em alerta de estoque
- Valor monetário total
- Taxa de movimentação

### 2️⃣ Gestão de Estoque
Central do sistema com:
- Busca textual e filtros por categoria
- Ordenação por múltiplos critérios
- Visualização de status (OK/Alerta/Crítico)
- Edição inline de dados

### 3️⃣ Registro de Movimentações
Interface para operações:
- Entradas de materiais
- Saídas/devoluções
- Ajustes de inventário
- ⚠️ Validação automática de saldo

### 4️⃣ Módulo de Pedidos
Requisição estruturada por setores:
- Seleção de produtos com filtros
- Detalhes do solicitante
- Rastreamento de status
- Histórico completo

### 5️⃣ Aprovação de Pedidos (Admin)
Gerenciamento do fluxo:
- Visualização em cards detalhados
- Aprovar → Baixa de estoque automática
- Rejeitar → Cancelamento e arquivo
- Identificação de responsáveis

### 6️⃣ Lista de Compras Sugerida
Geração automática de reposição:
- Identifica produtos abaixo do mínimo
- Calcula quantidade ideal
- Exportável para fornecedores

### 7️⃣ Relatórios & Análises
Ferramentas analíticas:
- **Curva ABC**: Classificação por valor (A: 70%, B: 20%, C: 10%)
- **Movimentação por Período**: Gráficos de 7/30/90/365 dias
- **Ranking de Produtos**: Volume de movimentação
- **Exportação**: PDF, CSV

### 8️⃣ Auditoria (Logs)
Rastreamento completo:
- Cronologia de operações
- Identificação de usuário
- Data/hora de cada ação
- Retenção de 1 ano

### 9️⃣ Cadastro de Produtos
Formulário estruturado:
- **Obrigatórios**: Nome, unidade, min, max, saldo inicial
- **Opcionais**: Categoria, fornecedor, localização, preços
- Validação em tempo real

### 🔟 Configurações do Sistema
Painel administrativo:
- Gerenciar categorias
- Backup/Importação de dados
- Alterar senha administrativa
- Configurar credenciais de API

---

## ⚙️ Regras de Negócio

| Regra | Comportamento |
|-------|---------------|
| **Validação de Estoque** | Saídas bloqueadas se saldo insuficiente |
| **Reserva por Pedido** | Produtos em pedidos pendentes não aparecem como disponíveis |
| **Status Automático** | Classificação OK/Alerta/Crítico baseada em limites |
| **Auditoria Obrigatória** | Todo registro gera log com usuário e timestamp |
| **Desfazer (Undo)** | Snapshot permite reverter última operação crítica |
| **Paginação** | Listagens em lotes de 10 registros |

---

## 🔄 Fluxo de Sincronização

```
┌──────────────┐        ┌─────────────────┐       ┌─────────────┐
│  Navegador   │───────▶│  JSONBin.io     │──────▶│   Nuvem     │
│  (Cliente)   │◀───────│  (API REST)     │◀──────│   (JSON)    │
└──────────────┘        └─────────────────┘       └─────────────┘
       │
       │ (sem conexão)
       ▼
┌──────────────────┐
│ Dados Embutidos  │
│ (216 produtos)   │
└──────────────────┘
```

**Comportamento**:
1. Tenta sincronizar com API remota
2. Em caso de falha → Carrega dados embutidos
3. Operações offline são enfileiradas
4. Sincronização automática ao restaurar conexão

---

## ⚠️ Segurança & Limitações

### Configuração Atual (Client-Side Only)
❌ **Não Implementado**:
- Criptografia de senhas
- Controle de sessão
- Tokens de autenticação
- Certificados SSL/TLS
- Isolamento de dados por usuário

⚠️ **Implicações**:
- Credenciais em texto plano no localStorage
- API Key visível no código-fonte
- Adequado apenas para ambiente **não-crítico** ou **intranet**

### ✅ Recomendações para Produção

Implementar back-end com:
- **Node.js, Python ou PHP**
- **Banco de dados**: MySQL, PostgreSQL
- **Autenticação**: OAuth 2.0, JWT
- **Hash de senhas**: bcrypt, Argon2
- **HTTPS obrigatório**
- **Controle de CORS**

---

## 🚀 Guia Rápido de Uso

### Primeiro Acesso
1. Abra a aplicação
2. Use senha padrão (definida em Configurações)
3. Selecione seu perfil

### Fluxo Operador
```
Dashboard → Gestão de Estoque → Registrar Movimentação → Criar Pedido
```

### Fluxo Administrador
```
Configurações → Cadastrar Produtos → Gerenciar Pedidos → Gerar Relatórios
```

---

## 📦 Exportação & Backup

- **Backup Completo**: JSON estruturado com todos os dados
- **Importação**: Recupera backup anterior
- **Relatórios**: Exportáveis em PDF/CSV
- **Frequência Recomendada**: Diária

---

## 🎓 Considerações Finais

**O que funciona bem:**
✅ Gestão visual e intuitiva  
✅ Sincronização em nuvem sem infraestrutura  
✅ Contingência com dados embutidos  
✅ Auditoria e rastreamento completo  

**Próximas melhorias:**
🔄 Migração para back-end seguro  
🔄 Autenticação robusta  
🔄 Banco de dados relacional  
🔄 API RESTful com OAuth  

---

**Versão**: 1.0  
**Última atualização**: Agosto 2026  
**Repositório**: [frdias1970/controle_estoque_irr](https://github.com/frdias1970/controle_estoque_irr)
