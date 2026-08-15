# CLAUDE.md

Guia para trabalhar neste repositório.

## O que é este repo

Site institucional estático do KombatClub (kombatclub.com.br), servido pelo Cloudflare Pages. Repositório separado do luta-tracker (o app, em app.kombatclub.com.br, Railway) de propósito: a landing muda por motivo de marketing (alta frequência, risco zero), o app muda por motivo de produto. Acoplar os dois faria uma troca de copy disparar build e deploy do app em produção.

- Sem framework, sem build step, sem bundler.
- Sem Supabase, sem banco de dados, sem migration.
- Um único arquivo, [index.html](index.html), com CSS e JS inline. Editar é abrir o arquivo e mexer direto.
- Deploy é automático via Cloudflare Pages a cada push — não há passo manual de build.

**app.kombatclub.com.br é outro repositório e outro deploy (luta-tracker, Railway). Não deve ser tocado a partir deste repo.**

## Ordem das seções

A ordem das seções segue a lógica de uma conversa de venda, não é arbitrária:

1. `.hero` — abre com a dor (o aluno some devagar)
2. `#problema` — aprofunda a dor, dá nome ao sinal
3. `#quem` — autoridade: responde "e você lá sabe disso?" antes de explicar a solução
4. `#funciona` — a solução, como o produto resolve o que foi descrito
5. `#recursos` — detalhamento da solução
6. `#tecnica` (evolução técnica) — o diferencial central: nenhum concorrente de mercado acompanha isso, por isso a seção fecha o argumento de solução antes do corte
7. `.corte` (desqualificação) — filtra quem não é o público antes do preço, pra quem chega em planos já estar convencido
8. `#planos` — preço, com a ancoragem de valor (`.ancora`) logo no topo do bloco
9. `#faq` — objeções de quem ainda hesita
10. `.ig-sec` — retenção de quem não vai comprar hoje; fica **depois** do preço de propósito, porque é o único link que tira o visitante do site — antes do preço ele interromperia a decisão
11. `.final` — CTA de fechamento

Seção nova não entra em qualquer posição: decida onde ela se encaixa nessa sequência (dor → autoridade → solução → desqualificação → preço → objeções → retenção → CTA) antes de inserir.

## Posicionamento de marca e metadados

**"KombatClub" nunca aparece sozinho em `<title>`, `og:title`, `og:site_name` ou `meta name="description"`** — sempre colado a um qualificador de categoria ("acompanhamento de treino para academias de luta" ou equivalente).

**Por quê:** testes de descoberta em LLMs mostraram 4+ entidades homônimas mais antigas disputando o termo "KombatClub". Sem o qualificador, menções ao nome puro são absorvidas por essas entidades em vez do produto.

O `og:description` é a única exceção deliberada — mantém "KombatClub" solto porque funciona como gancho de compartilhamento, não como campo que define categoria para indexação.

**Termos a evitar em título e meta description:** "gestão", "matrícula", "cobrança" e similares de ERP/administração de academia. O produto tem módulo financeiro (mensalidade, repasse) e ele continua na página (seção `#recursos`, card "Gestão financeira"), mas não é o que define a categoria do produto nesses campos — LLMs testados posicionam esses termos na categoria de sistemas de gestão administrativa (matrícula, catraca, cobrança), onde o produto não compete.

## Seção "Evolução técnica" (`#tecnica`)

Diferencial central da página: acompanhamento de evolução técnica do aluno (o que ele treinou — pad, clinch, sparring — não só se ele veio). Em testes de descoberta, nenhum sistema de mercado citado pelos LLMs faz esse tipo de acompanhamento; é o único argumento da página sem concorrente direto, por isso a seção existe e fica logo depois de `#recursos`, antes do corte de desqualificação.

## Mobile

Breakpoint principal de mobile: `max-width:520px` (nav, toggle de planos, padding lateral de cards). Há também um degrau intermediário em `max-width:1080px` (nav desktop mais compacta) e outro em `max-width:900px`, onde a nav vira menu hambúrguer (ver abaixo). O mosaico da seção `#quem` usa `max-width:560px` à parte, porque a coluna precisa de um pouco mais de largura antes de compactar.

Espaçamento vertical de seção (`section`, `.hero`, `.final`) usa `clamp()` em vez de valor fixo em px — desktop-first com padding fixo deixa vazio demais entre seções em tela estreita. Ao adicionar padding vertical novo em bloco de largura total, prefira `clamp(mín,vw,máx)` a um valor único.

### Nav no celular

Abaixo de 900px a nav vira **menu hambúrguer** (`.menu-btn`, três `<span>`, ícone vira "X" via `aria-expanded` — sem classe extra) que abre um overlay de tela cheia (`.menu-mobile`, `id="menu-mobile"`). Antes disso o site usou uma faixa de links rolável horizontalmente em vez de hambúrguer; foi descartada porque com 5+ links a faixa cortava nas pontas e às vezes já abria rolada, escondendo os primeiros itens — cortado é pior que ausente, e hambúrguer é padrão que o visitante já sabe usar.

**Link de nav novo entra em dois lugares, não um só**: dentro de `.nav-links` (desktop) *e* dentro de `#menu-mobile` (overlay mobile). Esquecer o segundo deixa o menu do celular desatualizado silenciosamente — nada quebra, só falta o link.

O JS do menu (toggle, fechar ao clicar em link, fechar com Esc, travar scroll do body) fica no `<script>` existente no fim do arquivo, junto com o script do toggle de planos.

### Mosaico da seção "Quem faz"

O mosaico mantém o arranjo 1+2 (uma foto larga em cima, duas embaixo) em **todas** as larguras, inclusive abaixo de 560px — não vira coluna única no mobile. O que muda por breakpoint é só a altura das linhas do grid (`grid-template-rows`), pra caber sem obrigar várias telas de scroll.

## Painel do app (`.phone`) — duplicado no HTML, hero no desktop e `#tecnica` no mobile

O `.phone` reproduz uma tela real do app e simula moldura de celular via pseudo-elementos: `::before` é o alto-falante (barra escura no topo), `::after` é a barra de gesto (linha clara na base). Não são elementos HTML — ajuste de moldura (posição, espessura, cor) se faz nesses dois seletores no CSS, sem tocar a estrutura do painel.

**O bloco `.phone` existe duplicado no HTML**: uma cópia dentro do `.hero`, outra dentro de `.phone-tecnica` na seção `#tecnica`. Só uma aparece por vez — CSS alterna por breakpoint (`.hero .phone{display:none}` / `.phone-tecnica{display:block}` abaixo de `900px`, e o inverso acima). Isso existe porque no mobile o painel solto no hero (depois dos dois CTAs, sem nenhuma ligação com o que veio antes) não funcionava mesmo cortado em altura — o problema era falta de contexto, não tamanho. Mover por JS conforme a largura foi descartado de propósito (reflow, ponto de falha a mais para um problema puramente visual); duplicar HTML e resolver em CSS foi a troca aceita.

**Qualquer alteração no conteúdo do painel (texto, alunos de exemplo, badges) precisa ser feita nas duas cópias**, ou desktop e mobile divergem silenciosamente — nada quebra, só ficam diferentes. Isso inclui o bloco `.insights` (badge "Sem clinch"), que hoje aparece inteiro nas duas cópias — ele é o argumento central que a seção `#tecnica` desenvolve, por isso não é mais truncado ou escondido no mobile como em uma versão anterior. `.phone-cap` (a legenda) fica no **rodapé** do painel nas duas cópias — no hero porque o painel ali é só reforço visual ao lado do texto, na seção `#tecnica` porque o H2 e o parágrafo já dão contexto antes do painel aparecer.

## Seção "Quem faz" (`#quem`)

Seção de credibilidade logo após `#problema` (ver [Ordem das seções](#ordem-das-seções)), com mosaico de fotos do fundador (ringue, corner, treino na Tailândia). As imagens ficam em `fotos/` na raiz do repo, versionadas em **WebP** (largura máxima 1400px, qualidade ~82, abaixo de 200 KB cada) — PNG não serve para foto, incha o peso sem ganho de qualidade perceptível.

Os arquivos originais (PNG, sem redimensionar) ficam em `fotos/originais/`, fora do versionamento (`.gitignore`) — servem só de backup local, nunca sobem para o repo.

Qualquer imagem nova adicionada ao site segue o mesmo padrão: original preservado em `fotos/originais/`, versão de produção convertida para WebP em `fotos/` (máx. 1400px de largura, abaixo de 200 KB), e `loading="lazy"` na tag `<img>`.

O recorte de cada foto no mosaico é controlado por `object-position` via uma classe por `<figure>` (`.m-ringue`, `.m-corner`, `.m-tailandia`). Não há legenda sobre as fotos — o ponto de interesse fica mais alto que o centro em foto de luta, então os valores de `object-position` são um ponto de partida e esperam ajuste manual olhando o resultado renderizado.

## Duplicação consciente: preços

Os preços exibidos nos cards de planos em [index.html](index.html) (seção `#planos`) são cópia manual de `TIER_PRICES` em `lib/constants/tiers.ts`, no repositório luta-tracker. Não há sincronização automática entre os dois.

Qualquer mudança de preço em `TIER_PRICES` exige atualizar os valores aqui à mão (`data-m` e `data-a` de cada `.plano .val`, e o texto em `.price-note`). Este é o único ponto de duplicação consciente do projeto — não tente "corrigir" criando um mecanismo de sincronia sem alinhar com o time antes.

## Trial acima da grade de planos

O trial (14 dias, sem cartão) é apresentado como faixa (`.trial-faixa`) **acima** da grade `.planos`, não como um card de plano — é a oferta de menor atrito da página e não deve competir visualmente com o card Pro (`.plano.dest`) nem obrigar o visitante a passar por cinco preços antes de descobrir que pode começar de graça. A grade `.planos` tem exatamente **5 cards de preço**: Solo, Pro, Equipe Starter, Equipe Pro, Equipe Unlimited. Não devolva o trial para dentro da grade.

## Cloudflare Web Analytics

O analytics usa o snippet JS manual do Cloudflare (perto do `</body>` em [index.html](index.html)), não a integração automática do Pages. Trocar o token exige editar esse script diretamente — não há variável de ambiente nem painel de configuração neste repo. Atenção às aspas do `data-cf-beacon`: simples por fora, duplas dentro do JSON — invertido, o beacon não carrega e o analytics fica sem dados silenciosamente.

## og.png

`og.png`, na raiz do repo, foi gerado por um script descartável (satori + `@resvg/resvg-js`, JSX→SVG→PNG) que **não faz parte do repositório** — foi apagado depois de gerar a imagem, junto com `node_modules/`, `package.json` e `package-lock.json`. A imagem **não é regenerada automaticamente**: se a headline do hero ou a identidade visual mudar, alguém precisa recriar o script (mesmo padrão: satori + resvg, fontes TTF do Google Fonts, 1200×630) e rodar de novo à mão.

Este repositório **não tem dependências Node e não deve ganhar nenhuma** — nenhuma ferramenta usada para gerar assets (imagens, ícones, etc.) deve deixar `package.json`, lockfile ou `node_modules/` versionados ou residentes no repo depois de usada.

## Esquema de UTM

Todo link que aponta para `https://app.kombatclub.com.br` deve levar query string de UTM, para permitir separar qual bloco do site converte. Base fixa:

```
?utm_source=landing&utm_medium=site&utm_campaign=landing_v1&utm_content=<posição>
```

`utm_content` por posição já em uso:

| Posição no HTML | utm_content |
|---|---|
| Botão "Entrar" na nav | `nav` |
| CTA "Testar 14 dias grátis" no overlay do menu mobile | `menu_mobile` |
| CTA "Testar 14 dias grátis" no hero | `hero` |
| Botão "Pegar meu link" na faixa de vínculo | `vinculo` |
| Card do plano Solo | `plano_solo` |
| Card do plano Pro | `plano_pro` |
| Card Equipe Starter | `plano_equipe_starter` |
| Card Equipe Pro | `plano_equipe_pro` |
| Card Equipe Unlimited | `plano_equipe_unlimited` |
| Faixa de trial (`.trial-faixa`, acima da grade) | `trial_faixa` |
| CTA "Criar conta grátis" no final | `cta_final` |
| Link app.kombatclub.com.br no rodapé | `footer` |

Qualquer CTA novo apontando para app.kombatclub.com.br segue o mesmo padrão: base fixa + `utm_content` novo e descritivo da posição.

Links de WhatsApp (`wa.me/5547999452015`) não usam UTM — não sobrevive ao redirect do WhatsApp. Em vez disso, o parâmetro `text` de cada link é diferente por posição, para identificar a origem pela mensagem que chega. Ao adicionar um novo CTA de WhatsApp, dê um texto de abertura distinto e mantenha o encoding de URL correto (espaços como `%20`, acentos como UTF-8 percent-encoded, `!` pode ficar literal).

**O número de WhatsApp nunca aparece em texto visível** — só dentro do `href="https://wa.me/55..."`. Número escrito numa página pública é coletado por bot e vira spam, além de obrigar a pessoa a copiar e colar em vez de tocar num botão. Todo link de WhatsApp exibe o ícone `.ico-wa` (SVG inline, reutilizado nos quatro pontos do site — hero, nota de plano personalizado, CTA final e rodapé) antes de um rótulo genérico ("WhatsApp" ou "Falar no WhatsApp"/"Chama no WhatsApp"), nunca o número. Link novo de WhatsApp segue o mesmo padrão: ícone + rótulo, número só no `href`.
