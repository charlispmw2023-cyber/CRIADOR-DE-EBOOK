# Design Summary — Gerador de E-books com IA

## 1) Visão do Produto

**Problema**
Criar e-books profissionais consome tempo: pesquisa, estrutura, escrita, diagramação, capa, sumário e padronização por formato.

**Solução**
App web que, a partir de um briefing guiado, gera um esboço de 15 capítulos, escreve o conteúdo (30–90 páginas), cria capa, insere 10–20 imagens/infográficos gerados por IA, monta sumário automático e exporta para PDF, ePub, DOCX e HTML/Markdown — em múltiplos idiomas.

**Públicos**
Criadores, infoprodutores, autores independentes, times de marketing/educação, agências, PMEs.

**Proposta de Valor**
Qualidade editorial + velocidade: do briefing ao arquivo final padronizado em minutos, com edição visual amigável e controle fino sobre tom, voz e design.

**Métricas de Sucesso**

- TTFX (tempo até primeira exportação) < 15 min
- Taxa de conclusão de rascunho > 70%
- Satisfação (CSAT/NPS) por exportação
- Retenção mensal e nº de projetos por usuário
- % de exportações sem erros de validação

## 2) Arquitetura da Informação & Navegação

**Topo (global)**: seletor de workspace, seletor de idioma, busca global, botão “Novo Projeto”, ajuda/perfil.

**Sidebar**: Projetos, Modelos, Biblioteca de Mídia, Configurações, Faturamento.

**Rotas (SPA)**:

- `/auth/login`, `/auth/register`, `/auth/recover`, `/auth/2fa`
- `/onboarding` (wizard de preferências iniciais)
- `/projects` (lista)
- `/projects/new` (assistente de brief)
- `/projects/:id` → Editor com abas:
  - Briefing (tema/objetivo/público/voz/idioma/keywords)
  - Esboço (estrutura de 15 capítulos)
  - Escrita (capítulo a capítulo, IA assistida)
  - Imagens (10–20 imagens/infográficos gerados por IA)
  - Design (tema, tipografia, margens, paginação)
  - Capa (layout, título, subtítulo, autor, arte IA)
  - Sumário (TOC auto)
  - Metadados (autor, descrição, direitos, keywords)
  - Revisão (checagens, leitura paginada)
  - Exportar (PDF/ePub/DOCX/HTML/MD)
- `/templates` (modelos de conteúdo e temas)
- `/assets` (mídia/figuras geradas e upload)
- `/settings/profile`, `/settings/security`, `/settings/preferences`
- `/billing` (plano, uso, faturas)
- `/help` (docs/FAQ/contato)

## 3) Inventário de Telas

- Autenticação: Login, Cadastro, Recuperar senha, 2FA.
- Onboarding: Idioma padrão, voz/tono, área de interesse, preferências de formatação.
- Dashboard (Projetos): Cards com status (Rascunho, Em edição, Pronto p/ exportar), busca/filtros.
- Novo Projeto (Wizard de Brief): Tema, objetivo, público, idioma(s), tom/voz, tamanho alvo (30–90 págs), 15 capítulos, 10–20 imagens/infográficos, palavras-chave, referências.
- Editor por Abas: ver rotas acima.
- Templates: Modelos de esboço, capas e temas tipográficos.
- Assets: Galeria com variações de imagens, geração por prompt, legendas e texto alternativo.
- Configurações: Perfil, segurança, preferências de idioma e layout.
- Faturamento: Plano, limites de uso, histórico.
- Ajuda: Guias rápidos, atalho para atalho de teclado.

## 4) Propósito e Blocos por Tela (com CTAs & Estados)

### Wizard de Briefing

**Campos**: título do e-book, objetivo, público-alvo, idioma, tom/voz, palavras-chave, estrutura (15 capítulos), tamanho (30–90 págs), imagens (10–20), referências.

**CTAs**: Gerar Esboço (primário), Voltar (secundário).

**Estados**: carregando (skeleton), erro de validação, sucesso → avança para Esboço.

### Aba Esboço

Visualização em lista/drag-and-drop de capítulos e subitens.

Ações IA: “Refinar tópicos”, “Adicionar capítulo/ seção”.

CTA: Gerar Texto de Todos os Capítulos ou “Gerar capítulo atual”.

### Aba Escrita

Editor rico (markdown + barra de ferramentas), histórico de versões, comentários.

Ações IA por seleção: Reescrever, Expandir, Resumir, Simplificar, Mudar tom, Traduzir.

Limites de tamanho e barra de progresso de páginas/palavras.

### Aba Imagens

Geração por prompt (estilo, paleta, proporção), infográficos (tabelas → gráficos), grade com 10–20 slots.

Variações e “melhorar qualidade”; metadados: legenda e alt-text obrigatórios.

Upload opcional para misturar com IA.

### Aba Design

Temas (tipografia/parágrafos/citações), grid, margens, cabeçalho/rodapé, numeração, quebras de página, hifenização por idioma.

Pré-visualização paginada.

### Aba Capa

Layouts prontos, título/subtítulo/autor, geração de arte com IA, paleta herdada do tema, contracapa (sobre o autor).

Guia de segurança de áreas (sangria/miolo para PDF).

### Aba Sumário (TOC)

TOC auto com níveis e links; editar rótulos; checagem de consistência.

### Aba Metadados

Autor(es), descrição, palavras-chave, data, direitos, categoria.

Validação para ePub (campos obrigatórios).

### Aba Revisão

Checklist automatizado: ortografia, links de TOC, imagens com alt-text, recuos, viúvas/órfãs (PDF), consistência de títulos.

### Aba Exportar

**PDF**: tamanho (A4/Letter), margens/cabeçalho/rodapé, qualidade de imagens, incorporação de fontes.

**ePub**: capa, spine, CSS limpo, metadados.

**DOCX**: estilos de parágrafo e títulos; imagens embutidas.

**HTML/Markdown**: assets acompanhados e manifest.

**Botões**: Exportar (primário), “Baixar amostra” (secundário).

**Estado**: fila de exportação com progresso, logs e download.

## 5) Fluxos (Happy Path & Bifurcações)

**Happy Path**

Criar Projeto → Briefing → Gerar Esboço (15 caps) → Gerar Texto (todos) → Imagens (10–20) → Design → Capa → TOC → Metadados → Revisão → Exportar (PDF/ePub/DOCX/HTML/MD).

**Iterações locais**: reescrever capítulo/trecho, mover capítulos, trocar imagens, alterar tema.

**Bifurcações/Exceções**:

- Falha de geração → retry com seed/variação.
- Excedeu 20 imagens → aviso + fila.
- Menos de 10 imagens → lembrete antes de exportar.
- Inconsistências no TOC → correção assistida.
- Idioma trocado após escrita → oferecer tradução integral com preservação de layout.

## 6) Hierarquia de Botões & Componentes

**Primário**: Gerar Esboço, Gerar Capítulos, Exportar.

**Secundário**: Pré-visualizar, Refinar, Variações de Imagem, Aplicar Tema.

**Terciário**: Duplicar Projeto, Histórico, Comparar Versões.

**Perigo**: Excluir Projeto, Descartar Variação.

**Componentes**: AppShell, Sidebar, Topbar, DataList, Kanban/Ordenação, RichText/Markdown, MediaGrid, PromptPanel, ThemePicker, CoverDesigner, TOCEditor, ExportQueue, Toasts/Modals/Drawers.

## 7) Formulários & Validações

**Obrigatórios no Brief**: título, objetivo, público, idioma, tamanho alvo (30–90 págs), 15 capítulos (fixo por padrão), imagens (10–20).

**Restrições**:

- Idioma suportado (multi-idioma; fallback PT-BR).
- Imagens: mínimo 10, máximo 20 (warning ao ultrapassar).
- Capítulos: 15 por padrão (podem ser renomeados/reordenados).

**Erros comuns**: campos vazios, tamanho fora do intervalo, metadados ePub ausentes, imagens sem alt-text.

## 8) Sistema de Design

**Cores (por função)**:

- `--brand`: #5B8DEF
- `--bg`: #FFFFFF / `--bg-elev`: #F7F8FA
- `--text`: #0F172A / `--text-muted`: #475569
- `--border`: #E5E7EB
- Estados: `--success` #10B981, `--warning` #F59E0B, `--danger` #EF4444, `--info` #06B6D4

*(Forneceremos modo escuro com tokens equivalentes.)*

**Tipografia**:

- Exibição: 34/28/22px (H1/H2/H3)
- Corpo: 16px, linha 1.6; Pequeno: 13–14px
- Monoespaçada para código/figuras quando preciso.

**Espaçamento (4-pt)**: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72.

**Raio**: 16px (cards/botões), 12px (inputs).

**Elevação**: sombras suaves (x-sm, sm, md, lg) com foco visível (outline 2–3px).

**Motion**: transições 150–250ms (opacity/transform), overshoot mínimo; respeitar `prefers-reduced-motion`.

## 9) Responsividade

**Breakpoints**: 360, 768, 1024, 1280, 1536.

**Mobile**: bottom-nav para abas do editor; editor de texto em tela cheia; exportações como fila modal.

**Desktop**: layout em 2–3 colunas (sidebar + conteúdo + painel IA).

Reflows estáveis; tipografia fluida entre 360–1280.

## 10) Acessibilidade (A11y)

- Contraste ≥ 4.5:1 para texto padrão.
- Focus ring sempre visível; navegação por teclado completa.
- ARIA para diálogos, tabs, toasts e progress bars.
- Alt-text obrigatório para todas as imagens; legendas de figuras.
- Headings hierárquicos (H1→H2→H3).
- Labels associados e mensagens de erro descritivas.

## 11) Modelo de Dados (simplificado)

```
User{id, name, email, locale, plan, createdAt}
Workspace{id, name, ownerId, members[]}
Project{id, workspaceId, title, language, audience, objective, tone, keywords[], targetPages(30–90), chaptersCount(15), targetImages(10–20), status, createdAt, updatedAt}
Chapter{id, projectId, order, title, summary, contentMarkdown, wordCount, images[] (ids), lastEditedAt}
ImageAsset{id, projectId, chapterId?, prompt, type(image|infographic), caption, altText, width, height, seed, variationOf?, createdAt}
Template{id, name, category(content|design|cover), payload}
Theme{id, tokens{fonts, sizes, colors, spacing, radii}, page{size, margins, header/footer}}
Cover{id, projectId, title, subtitle, author, artAssetId?, layout, palette}
TOC{projectId, items[{chapterId, label, level, page?}]}
Metadata{projectId, authors[], description, rights, keywords[], category}
ExportJob{id, projectId, type(pdf|epub|docx|html|md), status, progress, fileUrl, log[]}
Notification{id, userId, type, message, createdAt}
Billing{userId, plan, usage{tokens, images}, invoices[]}
```

## 12) Dataset de Demonstração (realista)

**Projeto**: “Guia Prático de Finanças Pessoais” (PT-BR)

**15 capítulos**

1. Planejamento
2. Orçamento 50/30/20
3. Fundo de Emergência
4. Dívidas
5. Cartões
6. Investimentos Iniciais
7. Renda Fixa
8. Renda Variável
9. Diversificação
10. Aposentadoria
11. Impostos
12. Seguro
13. Grandes Compras
14. Psicologia do Dinheiro
15. Plano de Ação

**10–20 figuras**: gráficos de pizza (orçamento), linhas (evolução do patrimônio), tabelas (categorias), ilustrações estilizadas.

**Metadados** preenchidos, TOC consistente, capa com arte IA.

**Projeto 2 (EN)**: “Beginner’s Digital Marketing Playbook”

**Projeto 3 (ES)**: “Hábitos Saludables en 30 Días”

## 13) Regras de IA (prompts & controles)

- Gerar Esboço (15 caps): considerar objetivo, público, voz, palavras-chave e idioma.
- Escrever Capítulo: manter consistência de voz, evitar repetição, sugerir exemplos, chamadas visuais e caixas de destaque.
- Tradução/Localização: preservar termos técnicos/localizar exemplos/valores.
- Imagens/Infográficos: prompt estruturado (tema, estilo, composição, legenda, contraste), opção de variação/seed.
- Segurança/Qualidade: verificação de plágio superficial, citações opcionais, disclaimers.

## 14) Estados do Sistema

- **Vazio**: instruções amigáveis e CTA primário.
- **Carregando**: skeletons por aba; progresso de geração.
- **Erro**: mensagem clara + ação de retry, preservando rascunho.
- **Sucesso**: toasts e “Ver Exportações”.

## 15) Planos & Limites (exemplo para UX)

- **Free/Trial**: 1 projeto ativo, 1 export por formato, 10 imagens.
- **Pro**: projetos ilimitados, 5 exportações por projeto, 20 imagens, temas personalizados.
- **Enterprise**: SSO, limites ampliados, controles de compliance.

## 16) Plano de Testes (QA)

**Objetivo**: validar se o usuário consegue completar o fluxo fim a fim com qualidade editorial, acessibilidade e exportações consistentes.

### Testes Funcionais (Happy Path)

1. **Briefing completo**: preencher título, objetivo, público, idioma, tom/voz, keywords, tamanho alvo (30–90) e imagens (10–20).
2. **Gerar esboço**: validar 15 capítulos e edição por drag-and-drop.
3. **Gerar texto**: gerar todos os capítulos e revisar contagem de páginas/palavras.
4. **Imagens**: gerar 10–20 imagens, validar legenda e alt-text obrigatórios.
5. **Design/Capa/TOC/Metadados**: confirmar consistência visual e validações de ePub.
6. **Exportações**: PDF/ePub/DOCX/HTML/MD, com downloads íntegros.

### Testes de Validação

- Campos obrigatórios do briefing com mensagens claras.
- Aviso para imagens > 20 e bloqueio suave para < 10 antes de exportar.
- TOC com links válidos e capítulos correspondentes.
- Metadados obrigatórios para ePub preenchidos.

### Testes de Acessibilidade

- Navegação por teclado completa.
- Contraste mínimo 4.5:1.
- Focus ring visível em todos os controles.

### Testes de Internacionalização

- Idioma padrão PT-BR e troca de idioma mantendo layout.
- Tradução integral com preservação de hierarquia e estilos.
