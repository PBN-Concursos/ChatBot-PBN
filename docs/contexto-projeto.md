# Contexto do Projeto — ChatBot PBN

> Documento-mãe. Quem chegar agora no projeto lê este arquivo primeiro.

## 1. Resumo em uma frase

Construir um chatbot de WhatsApp que **vende como o Bruno Nascimento vende**, para
atender e converter os leads do PBN Concursos sem ele precisar responder um por um.

## 2. Cliente e produto

- **Cliente:** Bruno Nascimento — dono do **PBN Concursos**.
- **Produto:** curso preparatório de **Educação Física para concursos públicos** (prefeituras do Estado do Rio de Janeiro, e também outros estados como MG).
- **Site:** https://www.pbnconcursos.com.br
- **Instagram:** @pbnconcursos
- **Sede (presencial):** Rua Lucília, 64 — Campo Grande, Rio de Janeiro/RJ.
- **Posicionamento central:** "nº 1 em aprovações na Educação Física em todo o Estado do RJ", com aprovações em **+70 das 92 prefeituras** e **quase 20 anos** de atuação ininterrupta.

### Os dois produtos
- **Curso online** — acesso liberado para todas as prefeituras; parcelamento em até **12x sem juros**; cancelamento em até **7 dias**.
- **Curso presencial** — **R$ 350,00/mês**, aulas aos **sábados das 8h às 13h**, material impresso incluso + acesso ao curso online liberado.

## 3. O problema que o bot resolve

O Bruno anuncia no **Meta Ads** e **Google Ads** quando sai um concurso novo. Os leads
caem no WhatsApp perguntando sobre o curso. Hoje **só o Bruno consegue vender** — ele tem
uma técnica de persuasão própria e não consegue delegar. O bot precisa **clonar essa técnica**
para converter o lead ali no chat, liberando o tempo dele.

## 4. Decisões já tomadas

| Tema | Decisão |
|---|---|
| Conexão com o WhatsApp | **Evolution API** (não-oficial) — mesmo número do Bruno, roda no droplet, sem custo por mensagem, permite o Bruno assumir a conversa pelo próprio celular. Risco aceito: é contra os termos do WhatsApp (risco gerenciável de bloqueio). |
| Onde o bot roda | **Droplet DigitalOcean** já existente (será "rebuildado" do zero — Ubuntu novo). |
| Cérebro / IA | **API do Claude** (modelos Claude mais recentes). |
| Banco de dados | **Supabase** (Postgres + pgvector para a busca de exemplos vencedores). |
| Painel de acompanhamento | **App no Vercel**, lendo o Supabase. Módulos escolhidos: (1) lista de leads + status, (2) conversas completas, (3) métricas de conversão, (4) controle do bot (pausar/assumir). |
| Aprendizado | **Supervisionado** — o bot aprende com vendas que fecharam e com as intervenções do Bruno, nunca com os próprios erros. Ver `sistema-de-aprendizado.md`. |

## 5. Próximos passos (ordem sugerida)

1. **(Sessão nova, rede liberada)** Abrir e ler o site inteiro (`pbnconcursos.com.br`): páginas de curso, **preços**, **checkout/pagamento**, depoimentos. Atualizar `metodo-de-vendas.md` com a oferta real.
2. **Formatar o droplet:** guiar o Bruno/usuário no "Rebuild Droplet" (Ubuntu limpo) pelo painel da DigitalOcean.
3. **Subir a Evolution API** no droplet e conectar o número do WhatsApp (QR code).
4. **Criar o projeto no Supabase** com o schema (ver `sistema-de-aprendizado.md`).
5. **Backend do bot:** receber webhook da Evolution → montar contexto → chamar Claude com o playbook do Bruno → responder.
6. **Painel no Vercel:** leads, conversas, métricas, controle.
7. **Loop de aprendizado:** captura de vitórias + intervenções do Bruno + aprovação de exemplos no painel.

## 6. Informações que ainda faltam (pendências)

> Pedir ao usuário quando possível.

- [ ] **Preços exatos** do curso online (valor à vista e parcelado) — confirmar no site.
- [ ] **Link de checkout/pagamento** que o Bruno manda pro lead fechar.
- [ ] **Concursos abertos/previstos** agora (pra urgência ser real e específica).
- [ ] **Tamanho do droplet** (RAM/CPU) — define o que cabe rodar nele.
- [ ] **Volume de leads/dia** (estimativa) — dimensiona custo e infra.
- [ ] **Acessos** (entregar com segurança, fora do chat público se sensível): SSH do droplet, número de WhatsApp dedicado ao bot, chave da API do Claude, projeto Supabase, conta Vercel.
- [ ] **Prints/áudios** de conversas reais do Bruno — ele perdeu o histórico, mas se aparecer qualquer um, é ouro para o aprendizado.

## 7. Nota importante sobre o ambiente (rede)

Este projeto roda no Claude Code na web (container efêmero). O acesso de rede estava em
**"confiável"** e foi alterado para **"completo"**, mas essa mudança **só vale para sessões
novas**. Por isso a leitura do site `pbnconcursos.com.br` ficou para a próxima sessão, que
sobe já com a rede liberada. Toda a fundação foi salva neste repositório justamente para
não perder contexto na troca de sessão.
