# Arquitetura Técnica — ChatBot PBN

## 1. Visão geral

```
┌──────────────────┐      ┌─────────────────────────┐      ┌──────────────────┐
│  WhatsApp do     │◄────►│  DROPLET (DigitalOcean) │◄────►│  Claude API      │
│  Bruno (leads)   │      │  ┌───────────────────┐  │      │  (cérebro /      │
└──────────────────┘      │  │  Evolution API    │  │      │  estilo Bruno)   │
                          │  └─────────┬─────────┘  │      └──────────────────┘
                          │  ┌─────────▼─────────┐  │
                          │  │  Backend do bot   │  │
                          │  │  (lógica de venda)│  │
                          │  └─────────┬─────────┘  │
                          └────────────┼────────────┘
                                       │ grava/lê
                                       ▼
                          ┌─────────────────────────┐      ┌──────────────────┐
                          │  Supabase (Postgres +   │◄────►│  Painel (Vercel) │
                          │  pgvector)              │      │  acompanhamento  │
                          └─────────────────────────┘      └──────────────────┘
```

## 2. Componentes e responsabilidades

| Componente | Responsabilidade |
|---|---|
| **Evolution API** (droplet) | Conecta ao WhatsApp do Bruno (não-oficial, via QR code). Recebe mensagens dos leads e envia respostas. Emite webhooks para o backend. |
| **Backend do bot** (droplet) | Recebe o webhook → identifica/cria o lead → monta o contexto (histórico + exemplos vencedores) → chama a Claude API com o playbook do Bruno → envia a resposta pela Evolution → salva tudo no Supabase. Controla o estado da conversa e o handoff. |
| **Claude API** | Gera a resposta no estilo e método do Bruno, a partir do prompt de sistema (playbook) + contexto recuperado. |
| **Supabase** | Banco de dados (leads, conversas, mensagens, métricas) + **pgvector** para busca de exemplos de vendas que fecharam. |
| **Painel (Vercel)** | Interface web para o usuário/Bruno: leads + status, conversas, métricas e controle do bot. |

## 3. Por que Evolution API (não-oficial)

- ✅ Usa o **mesmo número** que o Bruno já usa (não precisa migrar para a API oficial).
- ✅ **Sem custo por mensagem.**
- ✅ Roda no **droplet que ele já tem**.
- ✅ **Handoff natural:** o Bruno assume a conversa pelo próprio celular quando quiser.
- ⚠️ **Risco assumido:** é contra os termos do WhatsApp → risco (gerenciável) de bloqueio do número.
  - Mitigações: aquecer o número, não disparar em massa, respeitar ritmo humano, responder só a quem inicia.

## 4. Fluxo de uma mensagem

1. Lead manda mensagem no WhatsApp do Bruno (veio de um anúncio).
2. **Evolution API** recebe e dispara **webhook** para o backend.
3. Backend identifica o lead (telefone) — cria registro se for novo; detecta a origem ("como conheceu").
4. Backend monta o **contexto**:
   - Histórico da conversa.
   - Estado atual (abertura / qualificação / objeção / fechamento).
   - **Exemplos vencedores** parecidos (busca vetorial no Supabase).
   - Playbook do Bruno (prompt de sistema) — ver `metodo-de-vendas.md`.
5. Chama a **Claude API** → resposta no estilo do Bruno.
6. Backend envia a resposta pela **Evolution API**.
7. Salva mensagem + estado + métricas no **Supabase**.
8. Se acionado o **handoff**, o bot pausa para aquele lead e o Bruno assume.

## 5. Handoff humano (controle do bot)

- O Bruno pode **assumir um lead** (pausa o bot só naquela conversa) pelo painel ou respondendo direto no celular.
- Pode **ligar/desligar** o bot globalmente.
- As mensagens que o Bruno escreve quando assume viram **material de treino** (ver `sistema-de-aprendizado.md`).

## 6. Painel no Vercel — módulos

1. **Lista de leads + status** — novo / qualificado / em negociação / fechou / perdido.
2. **Conversas completas** — ler o chat bot ↔ lead.
3. **Métricas de conversão** — nº de leads, vendas, taxa, por concurso/anúncio/origem.
4. **Controle do bot** — pausar/assumir lead, ligar/desligar, aprovar exemplos de aprendizado.

## 7. Stack / serviços

- **DigitalOcean** — droplet (Ubuntu, "rebuild" do zero). Evolution API + backend (provavelmente via Docker).
- **Supabase** — Postgres + pgvector + auth do painel.
- **Vercel** — hospedagem do painel (provável Next.js).
- **Claude API** — geração das respostas.
- **Evolution API** — ponte com o WhatsApp.

> Decisões de linguagem/framework do backend e do painel serão definidas na fase de
> construção. Candidatos naturais: backend em Node/TypeScript; painel em Next.js (casa bem com Vercel + Supabase).
