# hb-cashier-web (Frontend) - Ecossistema Hubinity - Planned

> Parte integrante do ecossistema distribuído Hubinity.
> ⚠️ **Status atual: Planned** — código de implementação ainda não foi escrito. Este README descreve o papel arquitetural pretendido conforme PRD seção 4 e roadmap em `docs/phases/`.

---

## 💻 Visão Geral

- **O que faz:** Backoffice do caixa HiBit. Permite lançamentos manuais (entrada/saída avulsa com categoria), consulta de relatórios com filtros e gráficos por período/categoria/origem, e operação da sessão de caixa (abertura, fechamento, conciliação esperado vs realizado).
- **Problema que resolve:** dá ao operador uma UI dedicada para o livro-caixa centralizado, substituindo planilhas e permitindo visualização rápida das receitas automáticas que chegam do totem e dos chamados.
- **Posicionamento no Ecossistema:** consumidor único do `hb-cashier-service`. Janela operacional para os dados financeiros consolidados.

## 🏗️ Papel na Arquitetura

- **Tipo de Componente:** Single-Page Application Angular 22 (standalone + signals + control flow nativo).
- **Responsabilidades Principais (planejadas):**
  - Form de lançamento manual com seleção de categoria e validação de `description` (≥10 chars).
  - Tela de relatórios com filtros (`from`, `to`, `category`, `source`) e gráficos.
  - Operação da sessão de caixa (abertura → fechamento → conciliação).
  - Visualização de origem de cada lançamento (manual, totem, suporte).
- **Limites e Fronteiras (Boundaries):** não modifica receitas automáticas (entradas vindas via eventos são somente leitura); apenas cria lançamentos manuais e visualiza agregados.

## 🔗 Dependências e Comunicação (Planejadas)

### Serviços Internos da Hubinity

- **`hb-cashier-service`** — REST (`/api/v1/ledger/entries`, `/api/v1/ledger/balance`, `/api/v1/reports/**`, `/api/v1/sessions/**`, `/api/v1/categories`).
- **`platform-iam` (Keycloak)** — realm `hibit`, client público com PKCE; redirect URI `http://localhost:4202/*` em local. Roles: `admin`, `operador-caixa`.
- **`@hubinity/tailwind-preset`** — tokens compartilhados.

### Infraestrutura e Serviços Externos

- **Vercel** — hosting (plano Free).

## 🛠️ Tecnologias e Ferramentas (Stack Prevista)

| Camada | Tecnologia | Versão |
| :--- | :--- | :--- |
| Framework | Angular | 22 |
| Estilo | Tailwind CSS | 4 |
| Preset de tokens | `@hubinity/tailwind-preset` | última publicada |
| Primitives headless | `@spartan/ui` ou `ng-primitives` | última estável |
| Ícones | `lucide-angular` | última estável |
| Fonte | Inter via `@fontsource` | — |
| Charts | ApexCharts ou Chart.js (decisão na 2.7) | última estável |
| Auth | adapter Keycloak (PKCE) | — |
| E2E | Playwright | última estável |
| Hosting | Vercel | Free |

## 📐 Padrões de Projeto e Arquitetura do Código (Previstos)

- **Estilo Arquitetural:** SPA standalone + signals; visualização de dados agregados como cidadão de primeira classe.
- **Padrões Relevantes:**
  - **Reactive Forms** com validação de descrição mínima para lançamento manual.
  - **HTTP Interceptor** para JWT e refresh.
  - **Role Guards** distinguindo o que `admin` vs `operador-caixa` pode acessar.
  - **Memoização de filtros** via signals para evitar refetch em mudanças cosméticas.
  - Estilização **exclusivamente via Tailwind CSS 4** + preset compartilhado.

## 🗺️ Roadmap & Posição no Board

- **Fase do PRD:** Fase 2 — Caixa Manual (PRD seção 9).
- **Tasks no board:**
  - `2.6` — Bootstrap + telas de lançamento manual.
  - `2.7` — Tela de relatórios com filtros e gráficos.
  - `2.8` — Tela de sessão de caixa (abertura/fechamento/conciliação).
  - `2.9` — E2E + deploy.
- **Dependências bloqueadoras:** `hb-cashier-service` com endpoints CRUD do ledger (Fase 2.3) e relatórios (Fase 2.4) funcionando.

## ⚙️ Variáveis de Ambiente (Previstas)

```bash
NG_APP_CASHIER_API_BASE_URL=https://hb-cashier-service.up.railway.app
NG_APP_KEYCLOAK_URL=https://iam.hubinity.app
NG_APP_KEYCLOAK_REALM=hibit
NG_APP_KEYCLOAK_CLIENT_ID=hb-cashier-web
```

## 🚀 Como Será Executado (Quando Implementado)

### Pré-requisitos

- Node.js 22 LTS
- Acesso ao GitHub Packages para `@hubinity/tailwind-preset`
- Keycloak local com usuário operador-caixa de teste

### Execução (Será disponível após bootstrap da Fase 2.6)

```bash
npm ci
npm start         # dev server em http://localhost:4202
npm run build
npm test
npx playwright test
```
