# 🖤 Encaixe

> **Cole a vaga. Veja seu score. Gere o currículo certo.** — matchmaking entre você e a vaga, sem nunca inventar a sua história.

![status](https://img.shields.io/badge/status-MVP-black) ![feito com](https://img.shields.io/badge/feito%20com-Lovable-black) ![design](https://img.shields.io/badge/design-shadcn%2Fui-black) ![idioma](https://img.shields.io/badge/idioma-pt--BR-lightgrey)

App de matchmaking de vagas de emprego criado **100% por prompt no Lovable**, usando Lovable Cloud (auth + banco) e Lovable AI (inteligência) — sem backend externo e sem API keys.

---

## 💼 O problema

Quem procura emprego hoje enfrenta dois filtros antes de qualquer humano: o **ATS** (o robô que descarta currículos sem as palavras-chave certas) e o próprio hábito de **mandar o mesmo currículo para dez vagas diferentes**. O resultado é conhecido: currículos bons morrem na triagem por causa de formato, vocabulário e falta de aderência aparente — não por falta de competência.

## ✨ A solução

O Encaixe parte de um princípio simples: **um Currículo Mestre, muitas versões sob medida.**

1. **Monte o Currículo Mestre** — cole seu currículo atual e a IA estrutura tudo em seções editáveis (experiências, formação, skills, idiomas, certificações, projetos). É a sua fonte única da verdade.
2. **Cole a descrição da vaga** — título, empresa e o texto completo do anúncio.
3. **Veja o score de aderência (0–100)** — com evidências, não achismo: *o que já encaixa* (e qual experiência sua cobre cada requisito), *o que falta* (com recomendação honesta) e as *palavras-chave de ATS* da vaga, marcadas como presentes ou ausentes no seu currículo.
4. **Gere o currículo para essa vaga** — em português ou inglês, no formato que passa pelos robôs: coluna única, títulos de seção convencionais, bullets de verbo de ação + resultado, vocabulário da vaga incorporado **onde for verdade**. Com checklist ATS ao vivo e exportação em texto ou PDF.

Cada análise fica salva em **Minhas vagas**, um tracker simples de candidaturas: Analisada → Apliquei → Entrevista → Proposta.

## 🤝 A regra de ouro

> *"O Encaixe não inventa a sua história — ele a traduz para a língua da vaga."*

A IA **nunca fabrica** experiências, empregos, formações ou números. Ela apenas seleciona, reordena, reescreve e destaca o que já existe no Currículo Mestre. Requisito que você não cobre vira **recomendação** ("se você tem isso, adicione ao seu Mestre") — nunca conteúdo falso. Essa regra está gravada nos prompts internos de todas as chamadas de IA.

## ✅ O que o Checklist ATS valida

- Coluna única, sem tabelas, imagens, ícones ou gráficos de habilidade
- Títulos de seção convencionais ("Experiência Profissional", "Formação"…)
- Datas consistentes no formato MM/AAAA
- Contato no corpo do documento (ATS costuma ignorar cabeçalhos)
- Bullets iniciando com verbo de ação, com resultado quantificado
- Palavras-chave da vaga presentes (sigla **e** termo por extenso)
- Comprimento de 1–2 páginas

## 🎨 Design

Interface **estritamente monocromática** — preto, branco e cinza claro (escala zinc) — construída sobre **shadcn/ui**: Card, Badge, Table, Tabs, Progress, Dialog, Skeleton e Sonner. Sem cores de destaque: a hierarquia nasce do peso tipográfico (Geist), do tamanho e do contraste preenchido vs. outline. Estética de referência: Vercel e Linear.

## 🖼️ Prints

<!-- Salvar os prints na pasta prints/ com estes nomes -->
| Currículo Mestre | Score de aderência | Currículo ATS gerado |
|---|---|---|
| ![Currículo Mestre](prints/01-curriculo-mestre.png) | ![Análise de aderência](prints/02-score-aderencia.png) | ![Currículo gerado](prints/03-curriculo-ats.png) |

## 🧱 Stack

| Camada | Tecnologia |
|---|---|
| Geração e front | [Lovable](https://lovable.dev) — React + Vite + Tailwind + **shadcn/ui** |
| Backend | Lovable Cloud — auth e-mail/senha, Postgres com RLS por usuário |
| Inteligência | Lovable AI — 4 chamadas com resposta em JSON estruturado |
| Tipografia / ícones | Geist (fallback Inter) · Lucide |

### As 4 chamadas de IA

1. **Estruturar currículo** — texto colado → JSON do Currículo Mestre
2. **Analisar vaga** — anúncio → requisitos, senioridade e palavras-chave de ATS
3. **Calcular aderência** — Mestre + vaga → score com pesos, evidências e recomendações
4. **Gerar currículo ATS** — Mestre + análise + idioma → currículo em Markdown + checklist

### Estrutura de dados

```sql
resumes            (user_id, data jsonb, updated_at)
jobs               (id, user_id, title, company, description, analysis jsonb, score, status, created_at)
generated_resumes  (id, job_id, language, content_md, checklist jsonb, created_at)
```

## 📝 Prompt final (PRD)

O app inteiro nasceu de **um único prompt**, estruturado como mini-PRD (produto → design system → telas → chamadas de IA → dados → fora de escopo). O texto completo está em [`prompt-lovable.md`](prompt-lovable.md).

A seção mais importante dele não é a que diz o que construir, e sim as que dizem **como decidir** e **o que não fazer**:

> - Regra de ouro (inegociável, deve estar nos prompts internos da IA): **nunca inventar experiências, empregos, formações ou números**. [...] O que falta vira recomendação ao usuário, nunca conteúdo fabricado.
> - **Fora do escopo (NÃO construir):** upload/parsing de PDF ou DOCX, scraping de links de vaga, envio automático de candidaturas, integração com LinkedIn, planos pagos, tema escuro.

## 🚀 Como gerar o seu

1. Abra um projeto novo no [Lovable](https://lovable.dev).
2. Cole o prompt de [`prompt-lovable.md`](prompt-lovable.md) como primeira mensagem.
3. Aceite a ativação do **Lovable Cloud** e do **Lovable AI** quando solicitado (sem API key).
4. Itere um problema por vez, apontando a chamada de IA específica quando o resultado vier genérico.

## 🗺️ Roadmap

- **v1.0** — MVP: Currículo Mestre, análise de aderência, gerador ATS (PT/EN), tracker de vagas ✅
- **v1.1** — Upload de PDF/DOCX do currículo (hoje a entrada é texto colado)
- **v1.2** — Carta de apresentação sob medida, gerada da mesma análise
- **v2.0** — Aprendizado de conversão: quais versões geraram entrevista, e o que elas têm em comum
- **v2.1** — Múltiplos Currículos Mestre por área de atuação

## 📄 Licença

[MIT](https://opensource.org/licenses/MIT) — use, adapte e boa sorte na vaga. 🖤
