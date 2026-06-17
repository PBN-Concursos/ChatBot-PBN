# ChatBot PBN — Assistente de Vendas no WhatsApp

Chatbot de WhatsApp focado em **conversão de vendas** para o **PBN Concursos**
(curso preparatório de Educação Física para concursos públicos — Bruno Nascimento, Rio de Janeiro).

O objetivo é **replicar o método de vendas do Bruno** para atender e converter os
leads que chegam pelo WhatsApp (vindos de anúncios no Meta Ads e Google Ads),
liberando ele de responder um por um.

## Visão geral da solução

- **Bot (Evolution API)** rodando num droplet da DigitalOcean, conectado ao número de WhatsApp do Bruno.
- **Cérebro (Claude API)** que gera as respostas imitando o estilo e o método do Bruno.
- **Banco de dados (Supabase)** com leads, conversas, status e os "exemplos vencedores".
- **Painel (Vercel)** para acompanhar leads, ler conversas, ver métricas e controlar o bot.
- **Sistema de aprendizado supervisionado** que faz o bot melhorar com base nas vendas que fecham e nas intervenções do Bruno.

## Documentação

| Arquivo | O que tem |
|---|---|
| [`docs/contexto-projeto.md`](docs/contexto-projeto.md) | Brief do projeto, decisões tomadas e o que ainda falta |
| [`docs/entrevista-bruno.md`](docs/entrevista-bruno.md) | Entrevista completa com o Bruno (a base do método) |
| [`docs/metodo-de-vendas.md`](docs/metodo-de-vendas.md) | O "DNA de vendedor" do Bruno destrinchado (playbook) |
| [`docs/arquitetura.md`](docs/arquitetura.md) | Arquitetura técnica e o fluxo das mensagens |
| [`docs/sistema-de-aprendizado.md`](docs/sistema-de-aprendizado.md) | Como o bot aprende e melhora ao longo do tempo |

## Status

🟡 **Fase de descoberta / fundação.** Ainda não há código de aplicação — estamos
consolidando o método de vendas e as decisões de arquitetura antes de construir.

➡️ Próximo passo: abrir `docs/contexto-projeto.md` → seção **"Próximos passos"**.
