# CLAUDE.md

Guia para trabalhar neste repositório.

## O que é este repo

Site institucional estático do KombatClub (kombatclub.com.br), servido pelo Cloudflare Pages. Repositório separado do luta-tracker (o app, em app.kombatclub.com.br, Railway) de propósito: a landing muda por motivo de marketing (alta frequência, risco zero), o app muda por motivo de produto. Acoplar os dois faria uma troca de copy disparar build e deploy do app em produção.

- Sem framework, sem build step, sem bundler.
- Sem Supabase, sem banco de dados, sem migration.
- Um único arquivo, [index.html](index.html), com CSS e JS inline. Editar é abrir o arquivo e mexer direto.
- Deploy é automático via Cloudflare Pages a cada push — não há passo manual de build.

**app.kombatclub.com.br é outro repositório e outro deploy (luta-tracker, Railway). Não deve ser tocado a partir deste repo.**

## Seção "Quem faz" (`#quem`)

Seção de credibilidade entre o Instagram e Planos, com mosaico de fotos do fundador (ringue, corner, treino na Tailândia). As imagens ficam em `fotos/` na raiz do repo, versionadas em **WebP** (largura máxima 1400px, qualidade ~82, abaixo de 200 KB cada) — PNG não serve para foto, incha o peso sem ganho de qualidade perceptível.

Os arquivos originais (PNG, sem redimensionar) ficam em `fotos/originais/`, fora do versionamento (`.gitignore`) — servem só de backup local, nunca sobem para o repo.

Qualquer imagem nova adicionada ao site segue o mesmo padrão: original preservado em `fotos/originais/`, versão de produção convertida para WebP em `fotos/` (máx. 1400px de largura, abaixo de 200 KB), e `loading="lazy"` na tag `<img>`.

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
