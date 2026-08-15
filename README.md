# kombatclub-site

Site institucional estático do KombatClub (kombatclub.com.br), servido pelo Cloudflare Pages.

Não é Next.js. Não tem build step. Não tem Supabase. Não tem migration. É HTML/CSS/JS puro em um único arquivo.

O app (app.kombatclub.com.br) é outro repositório, hospedado no Railway — não é tocado por este repo.

## Como editar

Abra [index.html](index.html) direto e edite. Não há build: o que está no arquivo é o que vai pro ar.

## Como publicar

`git push` para a branch principal. O Cloudflare Pages detecta o push e faz o deploy automaticamente — sem passo manual.

## TODOs abertos

- **Conectar o domínio ao Worker.** A troca de nameservers já foi feita no Registro.br (`brad.ns.cloudflare.com` / `emma.ns.cloudflare.com`) e o domínio está em transição — o Registro.br leva ao menos 2h removendo a chave DNSSEC antes de delegar. Depois que a zona estiver ativa no Cloudflare, falta conectar o domínio em Workers & Pages → kombatclub-site → Domains → Add Domain.
