# Cosmic Bloom — diário de leitura online

App de leitura (livros, desafios, 12 meses, agenda, favoritos, bingo, cores, A–Z, roleta) que funciona igual no iPad, iPhone e computador, com os dados sincronizados pela sua conta Google.

**Sem configurar nada**, o app já abre e funciona, mas salva só no aparelho onde você está. Para sincronizar entre aparelhos, faça os 3 passos abaixo (uma vez só, ~15 min, tudo grátis).

## 1) Publicar o site (para ter um endereço)
O app é só uma pasta de arquivos. Escolha um jeito:
- **Netlify Drop (mais fácil):** acesse app.netlify.com/drop, arraste a pasta `cosmic-bloom` inteira. Você ganha um endereço `https://algo.netlify.app`.
- **GitHub Pages / Cloudflare Pages / Vercel:** também servem.

Anote o endereço final, você vai usá-lo no passo 2.

## 2) Criar o banco de dados (Supabase)
1. Crie uma conta em **supabase.com** e clique em **New project** (qualquer nome, escolha uma senha e a região *South America (São Paulo)*).
2. No menu **SQL Editor → New query**, cole o conteúdo de `supabase/schema.sql` e clique **Run**.
3. Em **Project Settings → API**, copie **Project URL** e a chave **anon public**.
4. Abra `js/config.js` e cole os dois valores:
   ```js
   export const CONFIG = {
     supabaseUrl: 'https://SEU-PROJETO.supabase.co',
     supabaseAnonKey: 'SUA-CHAVE-ANON',
   };
   ```
5. Publique de novo a pasta (passo 1) para valer a mudança.

## 3) Ligar o login com Google
1. Em **console.cloud.google.com**: crie um projeto → **APIs e serviços → Tela de consentimento OAuth** (tipo *Externo*, preencha nome e e-mail) → **Credenciais → Criar credenciais → ID do cliente OAuth** (tipo *Aplicativo da Web*).
2. Em *URIs de redirecionamento autorizados* coloque: `https://SEU-PROJETO.supabase.co/auth/v1/callback`
3. Copie o **Client ID** e o **Client Secret**.
4. No Supabase: **Authentication → Providers → Google** → ative e cole os dois.
5. No Supabase: **Authentication → URL Configuration**:
   - *Site URL*: o endereço do seu site (passo 1)
   - *Redirect URLs*: adicione o mesmo endereço (e `http://localhost:8080` se for testar no computador).

Pronto! Abra o site, vá em **Ajustes → Entrar com Google**. Faça o mesmo no iPhone: seus livros aparecem.

## Instalar como app
- **iPad/iPhone (Safari):** botão Compartilhar → *Adicionar à Tela de Início*.
- **Android/Chrome:** menu ⋮ → *Instalar app*.

## Como funciona
- Tudo é organizado **por ano**. Use as setas ao lado do ano (ou a tela de entrada) para voltar a 2025, 2026, 2027… Nada precisa ser apagado.
- Envie **PDF ou EPUB** ao adicionar um livro: a capa, o título, o autor e as páginas são lidos do arquivo. Uma foto de capa também funciona.
- Os arquivos vão para o seu armazenamento privado do Supabase (limite grátis: 1 GB no total e 50 MB por arquivo).
- Edição offline funciona; ao voltar a internet, sincroniza sozinho. Se editar o mesmo item em dois aparelhos, vale a edição mais recente.
- **Ajustes → Aparência** muda as cores do app inteiro. **Ajustes → Backup** exporta tudo num arquivo.

## Importante
- A chave `anon` pode ficar no site: a segurança é feita pelas regras (RLS) do `schema.sql`, que deixam cada pessoa ver só os próprios dados.
- Seus dados antigos (do `localStorage` do app anterior) não migram sozinhos, porque a estrutura mudou.

## Estrutura
```
index.html  manifest.json  sw.js
css/style.css
js/  app.js  store.js (dados+sync)  covers.js (PDF/EPUB)  bookkit.js  ui.js  theme.js  config.js
js/views/  home livros metas meses agenda favoritos bingo cores az roleta ajustes
supabase/schema.sql
```
