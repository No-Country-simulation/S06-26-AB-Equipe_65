# 05 — API

Contrato **REST** do backend (FastAPI). Este documento define convenções, autenticação e os endpoints por módulo. É uma especificação de referência para guiar a implementação — caminhos e payloads podem evoluir.

---

## 🧭 Convenções

- **Base URL:** `/api/v1`
- **Formato:** JSON (request e response).
- **Estilo:** REST, recursos no plural (`/trilhas`, `/vagas`).
- **Datas:** ISO 8601 (UTC).
- **IDs:** UUID.
- **Paginação:** query params `?page=1&limit=20`.
- **Erros:** envelope padrão:

```json
{
  "erro": {
    "codigo": "recurso_nao_encontrado",
    "mensagem": "Trilha não encontrada."
  }
}
```

| Código HTTP | Significado |
|-------------|-------------|
| 200 / 201 | Sucesso / criado |
| 400 | Requisição inválida |
| 401 | Não autenticado |
| 403 | Sem permissão |
| 404 | Não encontrado |
| 422 | Erro de validação (Pydantic) |
| 500 | Erro interno |

---

## 🔐 Autenticação

JWT (Bearer). O cliente envia o token no header:

```
Authorization: Bearer <token>
```

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/api/v1/auth/registro` | Cria conta. |
| POST | `/api/v1/auth/login` | Autentica e retorna o token JWT. |
| GET | `/api/v1/auth/eu` | Retorna o usuário autenticado. |

---

## 👤 Perfil

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/api/v1/perfil` | Retorna o perfil do usuário autenticado. |
| PUT | `/api/v1/perfil` | Cria/atualiza o perfil (objetivo, área, nível, localização, habilidades, consentimento). |

---

## 🤖 Agente de Orientação (IA)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/api/v1/agente/conversas` | Inicia uma nova conversa. |
| POST | `/api/v1/agente/conversas/{id}/mensagens` | Envia mensagem e recebe a resposta do agente (**suporta streaming** via SSE). |
| GET | `/api/v1/agente/conversas/{id}` | Recupera o histórico de uma conversa. |

**Exemplo — enviar mensagem**

```http
POST /api/v1/agente/conversas/abc-123/mensagens
Authorization: Bearer <token>
Content-Type: application/json

{ "conteudo": "Por onde eu começo para ser dev front-end?" }
```

```json
{
  "papel": "assistente",
  "conteudo": "Com base no seu perfil iniciante, sugiro começar por...",
  "recomendacoes": [
    { "tipo": "trilha", "referencia_id": "trilha-front-01", "justificativa": "Alinhada ao seu objetivo e nível." }
  ]
}
```

---

## 🎓 Trilhas

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/api/v1/trilhas` | Lista trilhas (filtros: `?area=&nivel=&baixo_consumo=`). |
| GET | `/api/v1/trilhas/{id}` | Detalha uma trilha e suas etapas. |
| GET | `/api/v1/trilhas/recomendadas` | Trilhas recomendadas pela IA para o usuário. |
| POST | `/api/v1/trilhas/{id}/inscricao` | Inscreve o usuário na trilha. |
| PATCH | `/api/v1/inscricoes/{id}` | Atualiza progresso (`etapa_atual`, `status`). |

---

## 💼 Empregabilidade

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/api/v1/vagas` | Lista vagas (filtros: `?area=&nivel=&modalidade=`). |
| GET | `/api/v1/vagas/recomendadas` | Vagas compatíveis com o perfil. |
| POST | `/api/v1/empregabilidade/analise-gaps` | Retorna lacunas entre o perfil e um objetivo/vaga + ações sugeridas. |
| POST | `/api/v1/vagas/{id}/candidatura` | Registra candidatura. |

---

## 🤝 Mentoria & Networking

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/api/v1/mentores/recomendados` | Mentores sugeridos por afinidade. |
| GET | `/api/v1/mentores/{id}` | Detalhe do mentor. |
| POST | `/api/v1/mentorias` | Agenda sessão de mentoria. |
| GET | `/api/v1/mentorias` | Lista as sessões do usuário. |

---

## 🌟 Histórias Inspiradoras

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/api/v1/historias` | Feed de histórias (filtros: `?area=&tag=`). |
| GET | `/api/v1/historias/recomendadas` | Histórias por afinidade com o perfil. |
| POST | `/api/v1/historias/{id}/salvar` | Salva/favorita uma história. |

---

## 🧠 Bem-estar & Check-in emocional

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/api/v1/bem-estar/checkin` | Registra um check-in emocional. |
| GET | `/api/v1/bem-estar/historico` | Evolução emocional ao longo do tempo. |
| GET | `/api/v1/bem-estar/recursos-apoio` | Retorna canais de apoio (ex.: **CVV 188**) — sempre disponível. |

**Exemplo — check-in**

```json
{ "humor": "ansioso", "intensidade": 4, "nota": "semana difícil de estudos" }
```

> Se a resposta detectar `sinal_risco = true`, o cliente deve exibir imediatamente os recursos de apoio (ver guardrails em [03](03-arquitetura-tecnica.md#guardrails-de-saúde-mental)).

---

## 📌 Notas de implementação

- **Validação:** schemas Pydantic em todas as entradas/saídas.
- **Streaming:** o endpoint de mensagens do agente deve suportar **Server-Sent Events (SSE)** para exibir a resposta token a token.
- **Rate limiting:** proteger endpoints de IA contra abuso/custo.
- **Documentação viva:** o FastAPI gera **OpenAPI/Swagger** automaticamente em `/docs` — manter como fonte da verdade dos contratos.
