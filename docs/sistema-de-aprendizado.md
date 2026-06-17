# Sistema de Aprendizado — Como o bot melhora ao longo do tempo

> Requisito do cliente: *"o chatbot tem que aprender conforme ele conversa e vende, para ir
> ficando mais profissional e qualificado para converter."*

## 1. Princípio central

O bot **não se re-treina sozinho** com tudo que conversa — e isso é proposital. Se aprendesse
com todas as conversas, aprenderia também com as que **perdeu**, repetindo os erros que afastam o
lead. A regra é: **aprender só com o que dá certo, e sempre ancorado no método do Bruno.**

Analogia: um **vendedor novato treinado pelo mestre**. Ele estuda as melhores vendas já fechadas,
observa o mestre fechar, o mestre entra na hora certa, e o time revisa periodicamente o que funciona.

## 2. Os 4 mecanismos de aprendizado

### Mecanismo 1 — Memória de vitórias (RAG)
- Toda conversa que **resultou em venda** é marcada e indexada (embeddings) no Supabase (pgvector).
- Quando chega um lead novo, o bot busca as **conversas vencedoras mais parecidas** (mesma cidade,
  mesmo perfil, mesma objeção) e usa como **exemplos vivos** para responder.
- Quanto mais vende, mais exemplos vencedores acumula → mais afiado fica. **Sem re-treinar o modelo.**

### Mecanismo 2 — Aprender com o Bruno (o mais poderoso aqui)
- Quando o Bruno **assume um lead** e fecha, ou **corrige/edita** uma resposta do bot, essas
  mensagens dele são salvas como **"exemplos de ouro"**.
- O botão de **controle do bot** (handoff) é, na prática, a **principal ferramenta de treino**:
  no início, quanto mais o Bruno entra e corrige, mais rápido o bot converge para o estilo dele.

### Mecanismo 3 — Métricas como juiz
- As métricas de conversão funcionam como **função de aptidão**: se uma forma de abrir a conversa
  ou de responder uma objeção converte mais, o sistema **prioriza** a que vende mais.
- Permite testes A/B simples de aberturas e respostas de objeção, deixando o dado decidir.

### Mecanismo 4 — Evolução do playbook com aprovação humana
- Periodicamente, o sistema sugere no painel: *"estas N conversas venderam — adicionar como exemplo
  do bot?"* O usuário/Bruno **aprova com um clique**.
- Garante que o bot **só evolui na direção certa**, sem pegar vício e sem fugir do tom do Bruno.
- O playbook (prompt de sistema) é **versionado** — dá pra ver o que mudou e reverter.

## 3. Esquema de dados (rascunho — Supabase)

> Rascunho inicial. Será refinado na fase de construção.

- **`leads`** — telefone, nome, é_educação_física, cidade/bairro, origem (anúncio/indicação),
  status (novo/qualificado/negociação/fechou/perdido), produto de interesse (online/presencial),
  timestamps.
- **`conversations`** — lead_id, canal, estado da máquina de vendas, resultado (venda/perda), valor.
- **`messages`** — conversation_id, autor (lead/bot/bruno), conteúdo, timestamp, foi_intervenção_humana.
- **`winning_examples`** — trecho de conversa vencedora, embedding (vector), tags (objeção, cidade,
  perfil), origem (venda fechada ou intervenção do Bruno), aprovado (bool).
- **`playbook_versions`** — versão do prompt/sistema, diff, autor, data, ativo (bool).
- **`metrics`** (ou views) — leads por dia, vendas, taxa de conversão, por concurso/anúncio/origem.
- **`interventions`** — registro de quando o Bruno assumiu, mensagens, virou exemplo de ouro (bool).

## 4. O que NÃO fazer

- ❌ **Fine-tuning automático** em cima de toda conversa (caro, lento, rígido e arriscado — aprende vício).
- ❌ Deixar o bot "evoluir o tom" sem aprovação (risco de drift, sair do estilo do Bruno).
- ❌ Aprender com conversas perdidas como se fossem boas.

> Fine-tuning de um modelo no estilo do Bruno só faria sentido lá na frente, com **centenas** de
> conversas vencedoras de alta qualidade — e mesmo assim, RAG + bom prompt costuma resolver com
> mais flexibilidade e menos custo. Decisão adiada.

## 5. Resultado percebido

De fora, parece exatamente o pedido: **o bot fica mais profissional e converte mais conforme
conversa.** Por dentro, é **controlado, seguro e sempre ancorado no método do Bruno** — aprende com
as vitórias e com o próprio Bruno, nunca com os erros.
