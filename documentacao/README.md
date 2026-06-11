# 📚 Documentação — App BiT

Documentação **funcional** e **técnica** do App BiT — plataforma inteligente que usa IA para reduzir barreiras enfrentadas por pessoas de grupos sub-representados na tecnologia, unindo **formação, empregabilidade, mentoria, histórias inspiradoras e bem-estar emocional** em um único ambiente.

> _"Porque talento existe em todos os lugares, mas oportunidades nem sempre."_

---

## 🗂️ Índice

| # | Documento | O que contém |
|---|-----------|--------------|
| 01 | [Visão Geral](01-visao-geral.md) | Problema, solução, público-alvo, personas, objetivos e métricas |
| 02 | [Especificação Funcional](02-especificacao-funcional.md) | Módulos, funcionalidades, user stories, jornada e requisitos não-funcionais |
| 03 | [Arquitetura Técnica](03-arquitetura-tecnica.md) | Stack, diagramas, camada de IA, segurança e escalabilidade |
| 04 | [Modelo de Dados](04-modelo-de-dados.md) | Entidades, relacionamentos e esquemas das tabelas |
| 05 | [API](05-api.md) | Convenções REST, autenticação e endpoints por módulo |
| 06 | [Roadmap & MVP](06-roadmap-mvp.md) | Escopo do MVP, fases de entrega e critérios de aceitação |

---

## 🧱 Stack do projeto

| Camada | Tecnologia |
|--------|-----------|
| **Frontend** | React |
| **Backend / API** | Python + FastAPI |
| **Inteligência Artificial** | Claude (Anthropic) — agente de orientação |
| **Banco de dados** | PostgreSQL |
| **Cache / filas** | Redis _(opcional no MVP)_ |

A escolha da stack está justificada em [03 — Arquitetura Técnica](03-arquitetura-tecnica.md).

---

## 📁 Estrutura do repositório

```
S06-26-AB-Equipe_65/
├── frontend/        # Aplicação React
├── backend/         # API FastAPI + integração de IA
├── documentacao/    # Esta documentação
└── README.md        # Pitch do produto
```

---

## ✍️ Convenções desta documentação

- Documentos em **português (pt-BR)** e formato **Markdown**.
- Diagramas em **Mermaid** (renderizam direto no GitHub).
- Cada documento é autônomo, mas se referencia entre si.
- Status do projeto: **🚧 em definição (pré-MVP)** — esta documentação precede a implementação.

> Sugestão de leitura para quem chega agora: comece pelo [01 — Visão Geral](01-visao-geral.md), depois [02 — Especificação Funcional](02-especificacao-funcional.md).
