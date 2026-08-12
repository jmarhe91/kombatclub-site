# kombatclub-site

Site institucional estático do KombatClub (kombatclub.com.br), servido pelo Cloudflare Pages.

Não é Next.js. Não tem build step. Não tem Supabase. Não tem migration. É HTML/CSS/JS puro em um único arquivo.

O app (app.kombatclub.com.br) é outro repositório, hospedado no Railway — não é tocado por este repo.

## Como editar

Abra [index.html](index.html) direto e edite. Não há build: o que está no arquivo é o que vai pro ar.

## Como publicar

`git push` para a branch principal. O Cloudflare Pages detecta o push e faz o deploy automaticamente — sem passo manual.

## TODOs abertos

- **og.png não existe.** As meta tags `og:image` em [index.html](index.html) apontam para `https://kombatclub.com.br/og.png`, mas o arquivo ainda não foi criado. Enquanto isso não for resolvido, links do site compartilhados no WhatsApp aparecem sem thumbnail. Bloqueante para divulgação.
- **Token do Cloudflare Web Analytics é placeholder.** O script no fim do [index.html](index.html) usa `__CF_BEACON_TOKEN__` literal. Pegue o token real no painel Cloudflare em Analytics & Logs → Web Analytics → Add a site, e substitua.
- **Nameservers do domínio kombatclub.com.br ainda não apontam para o Cloudflare.** Sem isso o Cloudflare Pages não serve o domínio custom.
