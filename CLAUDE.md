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
6. `.corte` (desqualificação) — filtra quem não é o público antes do preço, pra quem chega em planos já estar convencido
7. `#planos` — preço, com a ancoragem de valor (`.ancora`) logo no topo do bloco
8. `#faq` — objeções de quem ainda hesita
9. `.ig-sec` — retenção de quem não vai comprar hoje; fica **depois** do preço de propósito, porque é o único link que tira o visitante do site — antes do preço ele interromperia a decisão
10. `.final` — CTA de fechamento

Seção nova não entra em qualquer posição: decida onde ela se encaixa nessa sequência (dor → autoridade → solução → desqualificação → preço → objeções → retenção → CTA) antes de inserir.

## Mobile

Breakpoint principal de mobile: `max-width:520px` (nav, toggle de planos, padding lateral de cards). O mosaico da seção `#quem` usa `max-width:560px` à parte, porque a coluna única de fotos empilhadas precisa de um pouco mais de largura antes de virar layout mobile.

Espaçamento vertical de seção (`section`, `.hero`, `.final`) usa `clamp()` em vez de valor fixo em px — desktop-first com padding fixo deixa vazio demais entre seções em tela estreita. Ao adicionar padding vertical novo em bloco de largura total, prefira `clamp(mín,vw,máx)` a um valor único.

## Seção "Quem faz" (`#quem`)

Seção de credibilidade logo após `#problema` (ver [Ordem das seções](#ordem-das-seções)), com mosaico de fotos do fundador (ringue, corner, treino na Tailândia). As imagens ficam em `fotos/` na raiz do repo, versionadas em **WebP** (largura máxima 1400px, qualidade ~82, abaixo de 200 KB cada) — PNG não serve para foto, incha o peso sem ganho de qualidade perceptível.

Os arquivos originais (PNG, sem redimensionar) ficam em `fotos/originais/`, fora do versionamento (`.gitignore`) — servem só de backup local, nunca sobem para o repo.

Qualquer imagem nova adicionada ao site segue o mesmo padrão: original preservado em `fotos/originais/`, versão de produção convertida para WebP em `fotos/` (máx. 1400px de largura, abaixo de 200 KB), e `loading="lazy"` na tag `<img>`.

O recorte de cada foto no mosaico é controlado por `object-position` via uma classe por `<figure>` (`.m-ringue`, `.m-corner`, `.m-tailandia`). Não há legenda sobre as fotos — o ponto de interesse fica mais alto que o centro em foto de luta, então os valores de `object-position` são um ponto de partida e esperam ajuste manual olhando o resultado renderizado.

## Duplicação consciente: preços

Os preços exibidos nos cards de planos em [index.html](index.html) (seção `#planos`) são cópia manual de `TIER_PRICES` em `lib/constants/tiers.ts`, no repositório luta-tracker. Não há sincronização automática entre os dois.

Qualquer mudança de preço em `TIER_PRICES` exige atualizar os valores aqui à mão (`data-m` e `data-a` de cada `.plano .val`, e o texto em `.price-note`). Este é o único ponto de duplicação consciente do projeto — não tente "corrigir" criando um mecanismo de sincronia sem alinhar com o time antes.

## Esquema de UTM

Todo link que aponta para `https://app.kombatclub.com.br` deve levar query string de UTM, para permitir separar qual bloco do site converte. Base fixa:

```
?utm_source=landing&utm_medium=site&utm_campaign=landing_v1&utm_content=<posição>
```

`utm_content` por posição já em uso:

| Posição no HTML | utm_content |
|---|---|
| Botão "Entrar" na nav | `nav` |
| CTA "Testar 14 dias grátis" no hero | `hero` |
| Botão "Pegar meu link" na faixa de vínculo | `vinculo` |
| Card do plano Solo | `plano_solo` |
| Card do plano Pro | `plano_pro` |
| Card Equipe Starter | `plano_equipe_starter` |
| Card Equipe Pro | `plano_equipe_pro` |
| Card Equipe Unlimited | `plano_equipe_unlimited` |
| Card Teste (grátis) | `plano_trial` |
| CTA "Criar conta grátis" no final | `cta_final` |
| Link app.kombatclub.com.br no rodapé | `footer` |

Qualquer CTA novo apontando para app.kombatclub.com.br segue o mesmo padrão: base fixa + `utm_content` novo e descritivo da posição.

Links de WhatsApp (`wa.me/5547999452015`) não usam UTM — não sobrevive ao redirect do WhatsApp. Em vez disso, o parâmetro `text` de cada link é diferente por posição, para identificar a origem pela mensagem que chega. Ao adicionar um novo CTA de WhatsApp, dê um texto de abertura distinto e mantenha o encoding de URL correto (espaços como `%20`, acentos como UTF-8 percent-encoded, `!` pode ficar literal).
