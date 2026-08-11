# Prompt Lovable — Encaixe 🖤

**Como usar:** cole o bloco inteiro abaixo como **primeira mensagem** de um projeto novo no Lovable. Quando ele pedir, ative o **Lovable Cloud** e o **Lovable AI** (sem API key). Depois itere um problema por vez.

---

Crie o **Encaixe**, um app em português do Brasil que faz o matchmaking entre o currículo do usuário e vagas de emprego, e gera versões do currículo otimizadas para ATS sob medida para cada vaga. Construa 100% dentro do Lovable: use **Lovable Cloud** (auth por e-mail/senha + banco com RLS por usuário) e **Lovable AI** para toda a inteligência — nada de serviços externos ou API keys.

## Produto
- Tese: ninguém deveria mandar o mesmo currículo para dez vagas diferentes. O usuário mantém UM **Currículo Mestre** completo; para cada vaga, o app mede a aderência e gera uma versão sob medida que passa pelos robôs de triagem (ATS) sem perder a verdade.
- Regra de ouro (inegociável, deve estar nos prompts internos da IA): **nunca inventar experiências, empregos, formações ou números**. A IA apenas seleciona, reordena, reescreve e destaca o que já existe no Currículo Mestre. O que falta vira recomendação ao usuário, nunca conteúdo fabricado.
- Micro-copy da marca: "O Encaixe não inventa a sua história — ele a traduz para a língua da vaga."

## Design system
- **shadcn/ui** como base de todos os componentes: Card, Button, Badge, Tabs, Table, Dialog, Progress, Textarea, Select, Skeleton, Sonner (toasts).
- Paleta estritamente monocromática — **preto, branco e cinza claro** (escala zinc): fundo branco #FFFFFF, texto #09090B, superfícies e cartões #FAFAFA, bordas #E4E4E7, texto secundário #71717A. Botão primário preto com texto branco. **Nenhuma cor de destaque** (nada de azul/verde/vermelho): hierarquia se faz com peso tipográfico, tamanho, ícones Lucide e preenchido vs. outline.
- Tipografia Geist (fallback Inter). Números de score com font-variant-numeric: tabular-nums.
- Estética: minimalismo tipo Vercel/Linear — muito respiro, cantos arredondados, divisórias finas, zero ilustrações de banco de imagem.
- Desktop-first com sidebar de navegação (Currículo Mestre · Nova análise · Minhas vagas), responsivo no mobile.

## Telas

1. **Onboarding / Currículo Mestre:** o usuário cola o currículo atual em um textarea (ou preenche do zero). A IA estrutura o texto em seções editáveis: dados de contato, resumo profissional, experiências (cargo, empresa, período, bullets), formação, habilidades técnicas, idiomas, certificações e projetos. Tudo editável em cards com edição inline; salvar no banco. Estado vazio bem desenhado explicando o conceito.

2. **Nova análise:** formulário com título da vaga, empresa (opcional) e textarea para colar a descrição completa da vaga. Botão "Analisar aderência" com Skeleton de carregamento.

3. **Resultado da análise:**
   - Score de aderência 0–100 em número gigante preto + barra Progress + veredito em uma frase ("Encaixe forte — vale personalizar e aplicar").
   - Duas colunas: **"O que já encaixa"** (requisitos da vaga cobertos pelo currículo, com a evidência: qual experiência/skill cobre) e **"O que falta"** (requisitos sem cobertura, com recomendação honesta: "se você tem isso, adicione ao Currículo Mestre").
   - **Palavras-chave do ATS** extraídas da vaga como badges: preenchida (preta) = presente no currículo, outline = ausente.
   - CTA principal: "Gerar currículo para esta vaga", com Select de idioma da versão (Português ou Inglês).

4. **Currículo gerado:** layout em duas áreas —
   - À esquerda, o **preview do currículo ATS** em uma coluna única, editável por seção, já adaptado: resumo reescrito com o vocabulário da vaga, bullets reordenados por relevância (verbo de ação + resultado), palavras-chave da vaga incorporadas onde forem verdadeiras (sigla e termo por extenso), títulos de seção padrão.
   - À direita, o painel **"Checklist ATS"** com checks vivos: coluna única sem tabelas/imagens/ícones; títulos de seção convencionais; datas consistentes (MM/AAAA); contato no corpo (não em cabeçalho); bullets iniciando com verbo; palavras-chave da vaga presentes; comprimento (1–2 páginas).
   - Ações: **Copiar texto**, **Baixar .txt** e **Imprimir/Salvar PDF** — implementar via window.print() com @media print caprichado: esconder toda a UI, currículo em Arial 11pt preto sobre branco, margens de 2cm.

5. **Minhas vagas:** Table shadcn com todas as análises — vaga, empresa, score, status (Select inline: Analisada / Apliquei / Entrevista / Proposta / Encerrada) e data. Clicar abre o detalhe com a análise e o(s) currículo(s) gerado(s).

## Chamadas de IA (Lovable AI, respostas em JSON estruturado)
1. **Estruturar currículo:** texto colado → JSON do Currículo Mestre por seções.
2. **Analisar vaga:** descrição → requisitos obrigatórios/desejáveis, senioridade, palavras-chave de ATS (termos exatos que um recrutador filtraria).
3. **Calcular aderência:** Currículo Mestre + análise da vaga → score 0–100 com pesos (skills obrigatórias > desejáveis > senioridade > formação/idioma), lista "encaixa" com evidências, lista "falta" com recomendações. Proibido sugerir mentir.
4. **Gerar currículo ATS:** Currículo Mestre + análise + idioma → currículo completo em Markdown simples (títulos de seção em PT ou EN conforme o idioma), usando apenas fatos do Mestre, + o resultado do checklist ATS item a item.

## Dados (Lovable Cloud, RLS por user_id)
- resumes: user_id, data (jsonb do Currículo Mestre), updated_at
- jobs: id, user_id, title, company, description, analysis (jsonb), score, status, created_at
- generated_resumes: id, job_id, language, content_md, checklist (jsonb), created_at

## Fora do escopo (NÃO construir)
- Upload/parsing de arquivos PDF ou DOCX (entrada é sempre texto colado), scraping de links de vaga, envio automático de candidaturas, integração com LinkedIn, planos pagos, tema escuro.
