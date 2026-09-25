# Vero ID — Design System v0.1

Especificação de implementação. Acompanha `prompt-kyc-whitelabel-multinicho.md`.
Nome do produto provisório (alternativas: Íntegro, Confirma, Aferi).

Feito por Gabriel Moreira · @GabrielTechDesign · https://gabrielmoreira.tech · https://www.linkedin.com/in/ogabriel-moreira/

**Stack alvo:** React 19 + Vite + TypeScript · Tailwind + shadcn/ui · CSS variables por tenant · react-i18next (PT-BR / EN) · Recharts · MediaPipe Face Landmarker · Cloudflare Worker · Supabase + RLS.

**Duas superfícies:** painel B2B (proprietário, analista, leitor) e fluxo B2C do titular (mobile-first, uma decisão por tela, com a marca da organização).

---

## 1. Regra de nomenclatura

| Camada | Prefixo | Quem controla | Exemplo |
|---|---|---|---|
| Marca | `--brand-*`, `--radius-*` | Brand Kit do tenant | `--brand-500` |
| Neutro | `--n-*` | Sistema | `--n-600` |
| Semântico | `--risk-*` | Sistema, nunca sobrescrito | `--risk-high-ink` |
| Foco | `--focus-*` | Sistema | `--focus-ring` |
| Motion | `--dur-*`, `--ease-*` | Sistema | `--dur-fast` |
| Layout | `--bp-*`, `--content-max`, `--target-*` | Sistema | `--target-mobile` |

Nomes de componente e de prop acompanham as colunas do banco: `organization`, `subject`, `verification_link`, `verification_session`, `risk_signal`, `risk_score`, `qualification_score`. Nunca usar "lead quente" na interface.

---

## 2. Tokens

### 2.1 Cor de marca — `brand.indigo`

| Passo | Hex | Uso |
|---|---|---|
| 50 | `#F0F2FB` | superfície de destaque, item de dropdown em hover |
| 100 | `#DDE1F6` | superfície, ilustração de estado vazio |
| 200 | `#BCC3EE` | borda de card informativo |
| 300 | `#939EE2` | primary no dark mode, barra do item ativo |
| 400 | `#6C79D4` | 4ª barra de funil, ícone decorativo |
| 500 | `#4756C9` | **accent**, anel de foco, 3ª barra de funil |
| 600 | `#3742A8` | hover do primário, 2ª barra de funil |
| 700 | `#2B3486` | link, texto de ação em superfície clara |
| 800 | `#232C6B` | **primary**, botão primário, item ativo, 1ª barra |
| 900 | `#171D46` | reserva |

### 2.2 Neutro — `neutral.slate`

| Passo | Hex | Uso |
|---|---|---|
| 50 | `#F5F6FA` | fundo da página, linha de tabela em hover, sidebar |
| 100 | `#EBEDF4` | skeleton, divisor interno, fundo de disabled |
| 200 | `#D7DAE8` | **border** padrão de card e tabela |
| 300 | `#B6BBD0` | borda de input e de botão secundário, borda tracejada |
| 400 | `#9198B5` | somente preenchimento gráfico. **Não usar como cor de texto** (≈3:1) |
| 500 | `#6A7192` | texto de apoio ≥12px, overline |
| 600 | `#4E5573` | texto secundário, legenda, rótulo pequeno (mínimo seguro) |
| 700 | `#3C4262` | corpo de texto |
| 800 | `#262C45` | borda no dark mode |
| 900 | `#10142B` | **ink**, título, tooltip, pre de código |

### 2.3 Semântico de risco (fixo, nunca white-label)

| Faixa | Base | Fundo | Texto | Decisão |
|---|---|---|---|---|
| low `0–30` | `#0F7A4A` | `#E6F2EB` | `#0B5C37` | aprovação automática se a organização permitir |
| review `31–70` | `#B06A00` | `#FBF0DF` | `#7A4900` | revisão manual |
| high `71–100` | `#B3261E` | `#FBE9E7` | `#8C1D18` | reprovação sugerida, sempre revisável |
| unknown | `#5A6570` | `#EEF0F2` | `#3D464F` | timeout, teto de IA, prova de vida indisponível |

Bordas dos cards e chips de risco: base com alpha `.24`–`.28`.

### 2.4 Dark mode (só painel admin)

```
--surface:#0C1024  --surface-raised:#151A33  --border:#262C45
--ink:#EDEFF7      --ink-muted:#B6BBD0       --brand-primary:var(--brand-300)
--focus-ring:var(--brand-300)
risk-low:    bg rgba(15,122,74,.18)  · borda rgba(74,196,141,.40)  · ink #7FDCAE
risk-review: bg rgba(176,106,0,.18)  · borda rgba(232,168,64,.42)  · ink #F0C077
risk-high:   bg rgba(179,38,30,.16)  · borda rgba(240,120,110,.45) · ink #FFB4AC
```

Botão primário no dark inverte: fundo `#939EE2`, tinta `#10142B`. Input no dark: fundo `#0C1024`, borda `#4E5573`. O fluxo do titular **não** tem dark mode (captura facial precisa de tela luminosa).

### 2.5 Tipografia

Sem serifada em nenhum lugar do sistema. Display: `'Libre Franklin', system-ui, sans-serif`. Texto: `'Geist', system-ui, sans-serif`.

| Papel | Família / peso | Tamanho / linha | Tracking | Uso |
|---|---|---|---|---|
| display | Libre Franklin 700 | 44 / 44 | -0.032em | landing, capa de relatório |
| h1 | Libre Franklin 700 | 32 / 35 | -0.028em | título de página |
| h2 | Libre Franklin 700 | 28 / 32 | -0.025em | seção de documento |
| h3 | Libre Franklin 600 | 24 / 28 | -0.022em | título de card grande |
| h4 | Geist 600 | 19 / 25 | 0 | subtítulo de card, cabeçalho de modal |
| body-lg | Geist 400 | 16 / 26 | 0 | fluxo do titular, consentimento |
| body | Geist 400 | 14 / 22 | 0 | corpo do painel, linha de tabela |
| label | Geist 500 | 13 / 18 | 0 | rótulo de campo, botão, chip, item de menu |
| caption | Geist 400 | 12 / 18 | 0 | ajuda, legenda, metadado |
| overline | Geist 600 | 11 / 13 | 0.1em, uppercase | rótulo de big number, eixo |
| micro | Geist 400 | 10 / 14 | 0 | rótulo de escala de cor, eixo de gráfico |
| number | Geist 600 | 24–30 | -0.02em | score, big number, valor em R$ |

`font-variant-numeric: tabular-nums` obrigatório em score, coluna de dado, valor em R$ e timer. Medida de linha: 35–60 caracteres no mobile, 60–75 no desktop. Corpo mínimo de 16px em input no mobile (evita auto-zoom do iOS).

**Oito famílias aprovadas no Brand Kit** (o proprietário escolhe uma de display e uma de texto): Libre Franklin, Geist, Plus Jakarta Sans, IBM Plex Sans, Work Sans, Figtree, Manrope, Atkinson Hyperlegible. Todas sans, com pesos 400/500/600 e algarismos tabulares.

### 2.6 Espaço — base 4

Escala: `4 · 8 · 12 · 16 · 24 · 32 · 48 · 64`.

| Contexto | Valor |
|---|---|
| gap interno de chip / ícone-texto | 6–8 |
| padding de chip | 5 vertical / 11 horizontal |
| padding de input e botão | 11 vertical / 12–18 horizontal |
| padding de card | 22–24 |
| gap entre itens de card | 12–16 |
| gap de seção | 28 |
| respiro entre blocos de página | 48–64 |
| padding de página | `clamp(20px, 5vw, 64px)` |
| largura máxima de conteúdo | 1240 |
| largura máxima do fluxo do titular | 520, centrado |

### 2.7 Raio

| Token | Valor | Onde |
|---|---|---|
| `--radius-sm` | 4px | input, select, textarea, barra de funil, skeleton |
| `--radius-md` | 6px | botão, item de menu, aviso, toast, linha de dropdown |
| `--radius-lg` | 12px | card, painel, modal, bloco de código |
| `--radius-pill` | 999px | chip, tag, avatar, barra de score, botão de idioma |

Conjuntos alternativos que o Brand Kit pode aplicar: reto `0/2/6`, suave `4/6/12` (padrão), arredondado `8/12/20`. Chip é sempre pill em qualquer conjunto.

### 2.8 Elevação

| Token | Valor | Onde |
|---|---|---|
| flat | `border: 1px solid #D7DAE8` | card, tabela, painel (padrão) |
| `--shadow-raised` | `0 1px 2px rgba(16,20,43,.08)` | card que sai do fluxo |
| `--shadow-overlay` | `0 4px 14px rgba(16,20,43,.1)` | dropdown, tooltip, toast |
| `--shadow-modal` | `0 14px 36px rgba(16,20,43,.16)` | modal, sheet |
| `--scrim` | `rgba(16,20,43,.48)` | véu atrás do modal |

Nada acima disso. No dark mode, use superfície mais clara (`--surface-raised`) em vez de sombra.

### 2.9 Foco

```
--focus-ring:var(--brand-500)  /* #939EE2 no dark e em botão destrutivo */
--focus-width:2px  --focus-offset:2px
```

`outline: var(--focus-width) solid var(--focus-ring); outline-offset: var(--focus-offset);` em `:focus-visible`. Nunca `outline: none` sem substituto. Em botão destrutivo o anel é `#10142B` para não confundir com a cor de perigo.

### 2.10 Motion

| Token | Duração | Onde |
|---|---|---|
| `--dur-instant` | 80ms | hover, foco, chip |
| `--dur-fast` | 160ms | dropdown, tooltip, toast, borda da moldura de captura |
| `--dur-base` | 240ms | modal, gaveta, troca de etapa |
| `--dur-slow` | 400ms | barra de score, funil ao entrar |

`--ease-in: cubic-bezier(.2,.8,.3,1)` (entrada) · `--ease-out: cubic-bezier(.4,0,1,1)` (saída). Só `opacity` e `transform`. Saída ≈60–70% da duração de entrada. Stagger de 30–50ms, no máximo 8 filhos. Sob `prefers-reduced-motion: reduce` todas as durações vão a `0ms`, o skeleton troca pulsação por fundo estático e o estado final é renderizado na hora. A moldura de captura facial não pisca.

### 2.11 Ícones

Traço 1.5, grade de 20 (`viewBox="0 0 20 20"`), `currentColor`, `stroke-linecap/join: round`. Tamanhos: 16 em chip, 18 em botão e linha de tabela, 20 em conjunto, 24 em estado vazio. Uma família só. Ícone decorativo leva `aria-hidden="true"`; ícone que carrega significado sozinho precisa de alternativa textual; botão só de ícone precisa de `aria-label` e alvo de 40×40 (44 no mobile).

Conjunto base: `approved`, `review`, `rejected`, `unknown`, `pending`, `capture`, `liveness`, `subject`, `search`, `filter`, `evidence`, `export`.

### 2.12 Breakpoints

| Nome | Largura | Grade | Alvo de toque |
|---|---|---|---|
| sm | < 600 | 4 col · gutter 16 · margem 20 | 44 |
| md | ≥ 600 | 8 col · gutter 20 · margem 32 · sidebar em gaveta | 44 |
| lg | ≥ 900 | 12 col · gutter 24 · sidebar fixa | 40 |
| xl | ≥ 1280 | 12 col · conteúdo travado em 1240 | 40 |

Zero scroll horizontal em 320 e 375. Zoom de 200% não pode esconder a ação primária: ela fica no fluxo, não em barra fixa. Escala de z-index: `0 / 10 / 20 / 40 / 100 / 1000`.

---

## 3. Componentes

Formato: altura · padding · raio · tipografia · estados.

### 3.1 Button

| Variante | Fundo | Texto | Borda | Hover |
|---|---|---|---|---|
| primary | `#232C6B` | `#FFFFFF` | — | `#3742A8` |
| secondary | `#FFFFFF` | `#10142B` | 1px `#B6BBD0` | borda `#232C6B` |
| ghost | transparente | `#2B3486` | — | fundo `#F0F2FB` |
| destructive | `#B3261E` | `#FFFFFF` | — | `#8C1D18` |
| disabled | `#EBEDF4` | `#6A7192` | 1px `#D7DAE8` | nenhum |

Altura 40 no desktop, 44 no mobile e no fluxo do titular. Padding `11px 18px` (ghost `11px 12px`). Raio `md` (6). Tipografia label (Geist 500 / 13). Ícone opcional de 18 com gap 8.

Regras: uma ação primária por tela. `disabled` é atributo real, não só opacidade, e vem sempre com texto ao lado dizendo o que falta para habilitar. Botão em operação assíncrona desabilita e mostra progresso. Ação destrutiva separada espacialmente da primária.

### 3.2 Icon button

40×40 (44 no mobile), raio `md`, ícone 18, borda 1px `#B6BBD0`, `aria-label` obrigatório.

### 3.3 Input / Select / Textarea

Altura 40 (44 no mobile), padding `11px 12px`, raio `sm` (4), borda 1px `#B6BBD0`, fundo `#FFFFFF`, texto 14 `#10142B`.

- Label visível acima, Geist 500 / 13, gap 6. Placeholder nunca substitui label.
- Helper text persistente abaixo: 12 `#4E5573`.
- Foco: borda `#4756C9` + `box-shadow: 0 0 0 3px rgba(71,86,201,.18)`.
- Erro: borda `#B3261E`, label e mensagem em `#8C1D18`, ícone de 14 antes da mensagem, `aria-invalid="true"` e `aria-describedby` apontando para a mensagem.
- Validação no blur, não a cada tecla. A mensagem diz causa e correção ("Dígito verificador não confere. Digite os 11 números novamente").
- `type` semântico (`tel`, `email`, `number`) e `autocomplete` para o teclado e o autofill certos.
- Máscara de CPF, CNPJ, CEP e celular no formato BR.

### 3.4 Checkbox de consentimento

18×18, `accent-color: #232C6B`, alinhado ao topo da primeira linha (`margin-top: 2px`), gap 10 para o texto (13 / 1.5). Bloco de consentimento: padding 14, fundo `#F0F2FB`, borda 1px `#BCC3EE`, raio `md`.

Biometria é obrigatória e marcada **Obrigatório**. Marketing é um checkbox separado, opcional, desmarcado por padrão, nunca agrupado com a biometria. Registrar a versão do texto aceito (`consent_version`).

### 3.5 Chip de estado e risco

Padding `5px 11px`, raio pill, borda 1px na cor base com alpha `.28`, texto 12 Geist 500 na cor `ink` da faixa, ícone de 16 com gap 6.

Toda variação carrega **ícone + palavra**. Cor nunca é o único indicador. Variantes: risk-low, risk-review, risk-high, unknown, e informativo (`#F0F2FB` / borda `#BCC3EE` / texto `#2B3486`) para estados de link como "Link aberto".

Quando a coleção estoura a largura, quebre em várias linhas; não encolha o texto. Overflow do tipo `+3` precisa ser um controle operável.

### 3.6 Barra de score

Altura 8, raio pill, gradiente `linear-gradient(90deg, #0F7A4A 0 30%, #B06A00 30% 70%, #B3261E 70% 100%)`. Marcador: 3×16, raio 2, `#10142B`, posicionado pelo valor. Acima: overline à esquerda e `84 / 100` à direita em Geist 600 tabular na cor da faixa. Abaixo: `0 auto · 30 · 70 · 100` em micro `#4E5573`.

### 3.7 Big number

Padding 16, raio 10, fundo `#F5F6FA`, borda 1px `#EBEDF4`. Overline `#4E5573` → número 24–28 Geist 600 tabular → delta 12 (`#0B5C37` positivo, `#8C1D18` negativo, `#4E5573` neutro). Valor ausente é travessão, nunca zero. Valor desatualizado usa `opacity: .68` com aviso acima.

### 3.8 Funil

Barras de 22 de altura, raio `sm`, gap 6, rótulo de 12 à direita em largura fixa de 124. Cores em degradê descendente: `#232C6B`, `#3742A8`, `#4756C9`, `#6C79D4`. **A etapa de permissão de câmera recebe `outline: 2px dashed #B06A00; outline-offset: 2px` em todo gráfico de funil.**

### 3.9 Alert inline

Padding 14, raio `md`, ícone de 18 no topo com gap 11, texto 13 / 1.55. Três variantes: informativo (`#F0F2FB` / `#BCC3EE`), alerta (`#FBF0DF` / `rgba(176,106,0,.28)`), neutro de honestidade técnica (`#EEF0F2` / `rgba(16,20,43,.16)`).

Texto fixo obrigatório na UI e no README: *"Protótipo de demonstração. A detecção usa visão computacional e IA generativa e não substitui prova de vida certificada (ISO/IEC 30107-3)."*

### 3.10 Toast

Largura 320–400, padding 14, raio `md`, fundo `#FFFFFF`, borda 1px (`#D7DAE8`, ou a cor semântica quando for erro), `--shadow-overlay`. Ícone 18 + título 13/500 + corpo 12 `#4E5573` + ação textual 12 `#2B3486`. Canto inferior direito, 6s, `aria-live="polite"` / `role="status"`, sem roubar foco. Nunca a única via de uma ação destrutiva.

### 3.11 Dropdown

Largura mínima 200 (padrão 240), padding 6, raio `md`+2, `--shadow-overlay`. Item: padding `9px 10px`, raio `md`, texto 13, ícone 16 com gap 9, hover `#F0F2FB`. Divisor de 1px `#EBEDF4` com margem `5px 4px`. Item destrutivo em `#8C1D18`, sempre depois do divisor.

### 3.12 Tooltip

Padding `9px 11px`, raio `md`, fundo `#10142B`, texto `#DDE1F6` 12 / 1.5, largura máxima 280, `--shadow-overlay`. Só reforço: nunca a única via de uma informação, e nunca dependente de hover.

### 3.13 Modal

Largura máxima 480 (560 para detalhe de sessão), padding 22, raio `lg`, `--shadow-modal`, véu `--scrim`. Estrutura: ícone semântico 20 + título h4 + corpo 13 → campos → ações alinhadas à direita com gap 10 → nota explicativa 12.

Prende o foco, devolve ao gatilho ao fechar, `Esc` sempre fecha, animação a partir do gatilho em `--dur-base`. Modal não é fluxo de navegação primário. Ação destrutiva exige motivo: o botão fica `disabled` até haver seleção.

### 3.14 Shell do painel

Sidebar de 232, padding `16px 12px`, fundo `#F5F6FA`, borda direita 1px `#D7DAE8`. Item: padding `9px 10px`, raio `md`, texto 13, ícone 18, gap 10. Ativo: fundo `#232C6B`, texto branco, `box-shadow: inset 3px 0 0 #939EE2`, `aria-current="page"`. Hover: `#EBEDF4`.

Topbar: padding `16px 20px`, breadcrumb 11 `#4E5573` (a partir de 3 níveis) + título 20 Libre Franklin 600, busca de 40 de altura à direita. Tabs: padding `11px 0`, gap 20, ativa com `box-shadow: inset 0 -2px 0 #232C6B` e peso 600.

Abaixo de 900px a sidebar colapsa em gaveta. Após transição de rota, mover o foco para o conteúdo principal; voltar restaura scroll, filtro e input.

### 3.15 Tabela de dados

Cabeçalho: padding `10px 20px`, fundo `#F5F6FA`, borda inferior 1px `#D7DAE8`, overline `#4E5573`, seta de ordenação de 12. Linha: 52 de altura (padding `14px 20px`), borda inferior 1px `#EBEDF4`, hover `#F5F6FA`. Sem zebra.

Colunas de referência: `minmax(0,2fr) minmax(0,1fr) 84px 130px` para titular / recebido / score / estado. Score alinhado à direita, 15 Geist 600 tabular, na cor da faixa. Estado é chip com ícone. Nome longo trunca com reticências e aparece completo no painel da sessão, nunca em tooltip só de mouse. Listas de 50+ itens virtualizadas.

Paginação: padding `12px 20px`, botões de 32 de altura, raio `md`, página atual com fundo `#232C6B`.

Abaixo de 900px a tabela vira lista de cards com titular, score e estado.

### 3.16 Estado vazio

Padding `26px 18px`, raio 10, borda tracejada 1px `#B6BBD0`, fundo `#F5F6FA`, centralizado. Ícone 24–30 `#6C79D4` → título 14/500 → corpo 12 `#4E5573` com máximo de 36ch → ação primária. Diz o que está vazio, por quê e qual é o próximo passo.

### 3.17 Skeleton

Barras de 12 de altura, raio `sm`, `#EBEDF4`, larguras variadas (70%, 92%, 48%), gap 8. Só acima de 1s de espera; abaixo de 300ms, nenhum indicador. Reservar a mesma altura do conteúdo final (CLS < 0.1).

### 3.18 Estados de tela obrigatórios

Toda região que carrega dado implementa cinco estados:

| Estado | Tratamento |
|---|---|
| carregando | skeleton com a geometria do conteúdo final |
| vazio | 3.16 |
| erro | bloco `#FBE9E7` / borda `rgba(179,38,30,.28)`, ícone 24, título 14/600 `#8C1D18`, causa + o que fazer, ação de recuperação |
| parcial / stale | aviso neutro `#EEF0F2` acima, valores velhos em `opacity: .68`, ausentes em travessão |
| pronto | conteúdo |

Tela de permissão de câmera negada (fluxo do titular): fundo `#FBF0DF`, ícone de câmera cortada 24, título 16/600, corpo 14/1.6, botão de 44 de altura, e uma alternativa em link ("conclua com um atendente"). Instruções específicas por navegador (iOS Safari, Android Chrome). Registrar o evento no funil.

---

## 4. Acessibilidade (constraint de build, não etapa de review)

- Contraste: 4.5:1 em texto normal, 3:1 em texto grande e em ícone ou limite significativo. Verificado em light **e** dark. `#9198B5` não é cor de texto.
- Foco visível em tudo, nunca coberto por sticky ou overlay.
- Ordem de tab = ordem visual. Sem `tabindex` positivo.
- Ordem no fluxo do titular: título da etapa → consentimento → checkbox obrigatório → checkbox opcional → ação primária → ação secundária.
- Cor nunca é o único indicador: sempre ícone ou palavra junto.
- Alvos ≥44px no mobile, ≥40px no desktop, gap mínimo de 8.
- Texto escala até 200% sem truncar conteúdo essencial.
- `prefers-reduced-motion` respeitado com estado final renderizado.
- Erro de submit com vários campos: sumário focável no topo linkando cada campo, mantendo os erros inline.
- Toda ação de arrasto tem equivalente por clique e por teclado.
- Autenticação permite gerenciador de senha e colar.

---

## 5. Microcopy

Português escrito primeiro, inglês revisado contra ele, nunca traduzido por máquina. `react-i18next` com PT-BR e EN.

Para o titular: segunda pessoa, frase curta, sem jargão de risco. Ele nunca vê score nem sinal de fraude. Para o analista: termo técnico exato. Botão nomeia o resultado.

| Escreva | Não escreva |
|---|---|
| Enviar link ao titular | Submeter |
| Aponte a câmera para o seu rosto e mantenha o celular parado. | Iniciando captura de prova de vida passiva. |
| Não conseguimos confirmar sua identidade agora. Um atendente vai te chamar em até 2 minutos. | Verificação reprovada. Score 84. |
| Dígito verificador não confere. Digite os 11 números novamente. | Entrada inválida. |

Mensagem de SMS: `"{Organização}: {Nome}, confirme sua identidade para {finalidade}. Link válido por 24h: {url}"`. Nunca dado sensível na mensagem.

---

## 6. White-label

**A organização controla:** logo claro e escuro, favicon, capa, cor primária, secundária e de destaque, uma das oito famílias aprovadas, o conjunto de raio, o tom de voz.

**O sistema controla:** cores de risco, sucesso, alerta e erro; escala tipográfica; espaçamento; alvo de toque; anel de foco; a regra de ícone junto de cor.

**Geração de escala:** as três cores de marca viram `--brand-50` a `--brand-900` no escopo do tenant. `--brand-primary` = passo 800, `--brand-accent` = passo 500, `--brand-primary` no dark = passo 300.

**Checagem de contraste ao salvar:** a primária é testada contra branco e contra o surface. Se falhar AA, o sistema propõe o passo mais próximo da escala gerada que passa e mostra a razão medida.

**Assets:**

| Asset | Requisito |
|---|---|
| `logo-light.svg` | SVG ou PNG 2x, fundo transparente, altura mínima 24px, clear space de metade da altura |
| `logo-dark.svg` | arquivo separado; logo claro invertido é recusado abaixo de 3:1 sobre `#0C1024` |
| favicon | SVG + fallback PNG 32×32, só o símbolo |

Tudo o que o titular vê usa a marca da organização, com um discreto "Verificação por Vero ID" no rodapé.

---

## 7. Gráficos

Recharts. Tipo por pergunta, não por estética.

| Pergunta | Gráfico |
|---|---|
| Onde o titular desiste? | funil (com a etapa de câmera destacada) |
| Como variou no tempo? | linha |
| Qual tipo de fraude é maior? | barra |
| Como o score se distribui? | histograma com as faixas 0–30 / 31–70 / 71–100 |
| Onde geograficamente? | mapa do Brasil por UF (`react-simple-maps` + TopoJSON local) |
| Quando acontece? | heatmap dia × hora |

Regras: nunca distinguir série só por matiz (somar estilo de linha, padrão ou rótulo direto); alternativa acessível sempre (tabela ou sumário de tendência); tooltip operável por teclado; eixos com unidade; dado vs fundo ≥3:1 e rótulo ≥4.5:1; gridline discreta; estados de loading, vazio e erro previstos; animação de entrada respeita reduced-motion e o dado é legível de imediato.

---

## 8. CSS variables (copiar para o tema global)

```css
:root {
  /* brand — sobrescrito por tenant */
  --brand-50:#F0F2FB; --brand-100:#DDE1F6; --brand-200:#BCC3EE;
  --brand-300:#939EE2; --brand-400:#6C79D4; --brand-500:#4756C9;
  --brand-600:#3742A8; --brand-700:#2B3486; --brand-800:#232C6B;
  --brand-900:#171D46;
  --brand-primary:var(--brand-800); --brand-accent:var(--brand-500);

  /* neutral */
  --n-50:#F5F6FA; --n-100:#EBEDF4; --n-200:#D7DAE8; --n-300:#B6BBD0;
  --n-400:#9198B5; --n-500:#6A7192; --n-600:#4E5573; --n-700:#3C4262;
  --n-800:#262C45; --n-900:#10142B;

  /* semantic — nunca sobrescrito */
  --risk-low:#0F7A4A;     --risk-low-bg:#E6F2EB;     --risk-low-ink:#0B5C37;
  --risk-review:#B06A00;  --risk-review-bg:#FBF0DF;  --risk-review-ink:#7A4900;
  --risk-high:#B3261E;    --risk-high-bg:#FBE9E7;    --risk-high-ink:#8C1D18;
  --risk-unknown:#5A6570; --risk-unknown-bg:#EEF0F2; --risk-unknown-ink:#3D464F;

  /* type — sem serifada no sistema */
  --font-display:'Libre Franklin', system-ui, sans-serif;
  --font-text:'Geist', system-ui, sans-serif;

  /* shape — conjunto escolhido no Brand Kit */
  --radius-sm:4px; --radius-md:6px; --radius-lg:12px; --radius-pill:999px;

  /* elevation */
  --shadow-raised:0 1px 2px rgba(16,20,43,.08);
  --shadow-overlay:0 4px 14px rgba(16,20,43,.1);
  --shadow-modal:0 14px 36px rgba(16,20,43,.16);
  --scrim:rgba(16,20,43,.48);

  /* focus — nunca removido */
  --focus-ring:var(--brand-500); --focus-width:2px; --focus-offset:2px;

  /* motion */
  --dur-instant:80ms; --dur-fast:160ms; --dur-base:240ms; --dur-slow:400ms;
  --ease-in:cubic-bezier(.2,.8,.3,1); --ease-out:cubic-bezier(.4,0,1,1);

  /* layout */
  --bp-md:600px; --bp-lg:900px; --bp-xl:1280px;
  --content-max:1240px; --flow-max:520px;
  --target-desktop:40px; --target-mobile:44px;

  /* icon */
  --icon-stroke:1.5; --icon-sm:16px; --icon-md:18px; --icon-lg:24px;
}

@media (prefers-reduced-motion: reduce) {
  :root { --dur-instant:0ms; --dur-fast:0ms; --dur-base:0ms; --dur-slow:0ms; }
}

[data-theme="dark"] {
  --brand-primary:var(--brand-300);
  --surface:#0C1024; --surface-raised:#151A33; --border:#262C45;
  --ink:#EDEFF7; --ink-muted:#B6BBD0;
  --focus-ring:var(--brand-300);
  --risk-low-bg:rgba(15,122,74,.18);    --risk-low-ink:#7FDCAE;
  --risk-review-bg:rgba(176,106,0,.18); --risk-review-ink:#F0C077;
  --risk-high-bg:rgba(179,38,30,.16);   --risk-high-ink:#FFB4AC;
}
```

---

## 9. Checklist antes de fechar qualquer tela

- [ ] Um estilo só, coerente com o resto do painel; nenhuma sombra ou raio inventado
- [ ] Tokens semânticos em uso; nenhum hex cru no componente
- [ ] Light e dark testados de verdade (o fluxo do titular só existe em light)
- [ ] Contraste 4.5:1 em texto, 3:1 em ícone significativo
- [ ] Foco visível e não obstruído; ordem de tab igual à ordem visual
- [ ] Alvos ≥40 desktop / ≥44 mobile, gap ≥8
- [ ] `disabled` semântico, com texto dizendo o que habilita
- [ ] Cinco estados desenhados: carregando, vazio, erro, parcial, pronto
- [ ] Erro diz causa e próximo passo, com caminho de recuperação
- [ ] Ação destrutiva com motivo obrigatório, confirmação e log de auditoria
- [ ] Sem scroll horizontal em 320 e 375
- [ ] Nenhuma animação de `width`, `height`, `top` ou `left`
- [ ] Imagens com dimensão declarada; CLS < 0.1
- [ ] Textos revisados em PT-BR e EN
