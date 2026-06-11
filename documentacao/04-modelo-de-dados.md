# 04 — Modelo de Dados

Modelo de dados **conceitual** do App BiT. Serve de base para a modelagem física no PostgreSQL — nomes e tipos finais podem ser ajustados na implementação.

---

## 🗺️ Diagrama de entidades (ER)

```mermaid
erDiagram
    USUARIO ||--|| PERFIL : possui
    USUARIO ||--o{ INSCRICAO_TRILHA : inscreve
    TRILHA  ||--o{ INSCRICAO_TRILHA : recebe
    TRILHA  ||--o{ ETAPA_TRILHA : contem
    USUARIO ||--o{ RECOMENDACAO : recebe
    USUARIO ||--o{ CHECKIN_EMOCIONAL : registra
    USUARIO ||--o{ CONVERSA : mantem
    CONVERSA ||--o{ MENSAGEM : contem
    USUARIO ||--o{ SESSAO_MENTORIA : participa
    MENTOR  ||--o{ SESSAO_MENTORIA : conduz
    USUARIO ||--o{ CANDIDATURA : realiza
    VAGA    ||--o{ CANDIDATURA : recebe
    USUARIO ||--o{ HISTORIA_SALVA : salva
    HISTORIA ||--o{ HISTORIA_SALVA : referenciada

    USUARIO {
        uuid id PK
        string email
        string nome
        string senha_hash
        string papel
        timestamp criado_em
    }
    PERFIL {
        uuid id PK
        uuid usuario_id FK
        string objetivo
        string area_interesse
        string nivel
        string escolaridade
        string localizacao
        string conectividade
        jsonb habilidades
        boolean consentimento_dados
    }
    TRILHA {
        uuid id PK
        string titulo
        string area
        string nivel
        text descricao
        boolean baixo_consumo
    }
    ETAPA_TRILHA {
        uuid id PK
        uuid trilha_id FK
        int ordem
        string titulo
        string tipo_conteudo
        string url_conteudo
    }
    INSCRICAO_TRILHA {
        uuid id PK
        uuid usuario_id FK
        uuid trilha_id FK
        string status
        int etapa_atual
        timestamp atualizado_em
    }
    VAGA {
        uuid id PK
        string titulo
        string empresa
        string area
        string nivel
        string modalidade
        string localizacao
        jsonb requisitos
    }
    CANDIDATURA {
        uuid id PK
        uuid usuario_id FK
        uuid vaga_id FK
        string status
        timestamp criado_em
    }
    MENTOR {
        uuid id PK
        uuid usuario_id FK
        string area_atuacao
        text bio
        jsonb disponibilidade
    }
    SESSAO_MENTORIA {
        uuid id PK
        uuid mentor_id FK
        uuid usuario_id FK
        timestamp agendada_para
        string status
    }
    HISTORIA {
        uuid id PK
        string titulo
        text conteudo
        string area
        jsonb tags
    }
    HISTORIA_SALVA {
        uuid id PK
        uuid usuario_id FK
        uuid historia_id FK
    }
    CHECKIN_EMOCIONAL {
        uuid id PK
        uuid usuario_id FK
        string humor
        int intensidade
        text nota
        boolean sinal_risco
        timestamp criado_em
    }
    RECOMENDACAO {
        uuid id PK
        uuid usuario_id FK
        string tipo
        uuid referencia_id
        text justificativa
        timestamp criado_em
    }
    CONVERSA {
        uuid id PK
        uuid usuario_id FK
        timestamp criado_em
    }
    MENSAGEM {
        uuid id PK
        uuid conversa_id FK
        string papel
        text conteudo
        timestamp criado_em
    }
```

---

## 📋 Principais entidades

### USUARIO
Conta na plataforma. `papel` ∈ `{ usuario, mentor, admin }`.

| Campo | Tipo | Notas |
|-------|------|-------|
| id | UUID (PK) | |
| email | string | único |
| nome | string | |
| senha_hash | string | nunca armazenar senha em texto puro |
| papel | enum | `usuario` / `mentor` / `admin` |
| criado_em | timestamp | |

### PERFIL
Dados que personalizam a jornada (relação 1:1 com USUARIO).

| Campo | Tipo | Notas |
|-------|------|-------|
| objetivo | enum | `primeiro_emprego` / `transicao` / `recolocacao` / `crescimento` |
| area_interesse | string | ex.: front-end, dados, QA |
| nivel | enum | `iniciante` / `intermediario` / `avancado` |
| localizacao | string | para adaptar oportunidades |
| conectividade | enum | `boa` / `limitada` — ativa modo de baixo consumo |
| habilidades | JSONB | autoavaliação (técnicas e comportamentais) |
| consentimento_dados | boolean | LGPD — obrigatório |

### TRILHA / ETAPA_TRILHA / INSCRICAO_TRILHA
Trilha é o caminho de estudo; ETAPA são seus passos ordenados; INSCRICAO liga o usuário à trilha e guarda o progresso (`etapa_atual`, `status` ∈ `{em_andamento, concluida, abandonada}`). `baixo_consumo` marca conteúdos leves para conectividade limitada.

### VAGA / CANDIDATURA
Oportunidades de emprego e o vínculo do usuário a elas. `modalidade` ∈ `{remoto, presencial, hibrido}`; `requisitos` em JSONB alimenta a análise de gaps.

### MENTOR / SESSAO_MENTORIA
Perfil de mentor (estende USUARIO) e agendamento de sessões. `status` ∈ `{agendada, realizada, cancelada}`.

### HISTORIA / HISTORIA_SALVA
Biblioteca de histórias inspiradoras e os favoritos do usuário.

### CHECKIN_EMOCIONAL
Registro do estado emocional. `sinal_risco` é um booleano calculado para disparar os guardrails de segurança (ver [03](03-arquitetura-tecnica.md#guardrails-de-saúde-mental)).

> ⚠️ Tabela com **dado sensível (LGPD)** — acesso restrito, criptografia e política de retenção específicas.

### RECOMENDACAO
Recomendações geradas pela IA. `tipo` ∈ `{trilha, vaga, mentor, historia, acao}`; `referencia_id` aponta para o item; `justificativa` guarda o "porquê" exibido ao usuário.

### CONVERSA / MENSAGEM
Histórico do chat com o agente. `papel` da mensagem ∈ `{usuario, assistente, sistema}`.

> Os endpoints que manipulam essas entidades estão em [05 — API](05-api.md).
