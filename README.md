# Will Prime Veículos

Site da concessionária Will Prime Veículos, com vitrine de veículos, galeria de fotos, links de contato e painel administrativo.

## Como executar

Requer Node.js 22 ou versão compatível com Vite 8.

```bash
npm install
npm run dev
```

O build de produção é gerado com:

```bash
npm run build
npm run preview
```

## Configuração do Supabase

Para publicar alterações de estoque, configure as variáveis no ambiente de execução:

- `VITE_SUPABASE_URL`
- `VITE_SUPABASE_PUBLISHABLE_KEY`
- `SUPABASE_URL`
- `SUPABASE_PUBLISHABLE_KEY`

O painel exige que os dois telefones autorizados estejam cadastrados no Supabase Auth com autenticação por telefone e senha (use a senha definida para o painel) e que seus usuários tenham a função `admin` em `public.user_roles`. O bucket de armazenamento utilizado é `vehicle-images`.

A migração `supabase/migrations/20261016000000_admin_phone_allowlist.sql` restringe a função de administrador aos dois telefones autorizados. Aplique-a ao projeto Supabase antes de publicar alterações no painel. Depois de criar os dois usuários de telefone no Supabase Auth, execute no SQL Editor para lhes atribuir o papel administrativo:

```sql
insert into public.user_roles (user_id, role)
select id, 'admin'::public.app_role
from auth.users
where right(regexp_replace(coalesce(phone, ''), '[^0-9]', '', 'g'), 11) in ('11961252181', '11972866740')
on conflict (user_id, role) do nothing;
```

O gate do site autentica pelo Supabase; os números e senhas precisam existir no serviço de autenticação. Sem a configuração do Supabase, a página pública abre normalmente, mas o painel não publica alterações de estoque.

## Estrutura

- `src/routes` — páginas inicial, veículos e painel de administração
- `src/components` — elementos compartilhados
- `src/assets` e `public` — imagens locais otimizadas
- `supabase/migrations` — regras de segurança do banco de dados
