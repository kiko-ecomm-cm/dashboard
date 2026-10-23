# BI Construmarques V2

Dashboard de marketing com **Google Ads + Meta Ads**, histórico mensal no Supabase, login restrito e biblioteca de artes com miniaturas ampliáveis.

## Recursos
- Upload manual Excel/CSV e prévia antes da gravação
- Campanhas por canal, objetivo e mês
- Valores de conversão Google exibidos como **Retorno**
- Alcance, compras e mensagens Meta quando presentes
- Miniaturas, galeria ampliada e upload de JPG/PNG/WEBP
- Armazenamento privado, login por e-mail, RLS e papéis `viewer`/`editor`
- CSV do período filtrado
- Sem IA, sem integração em tempo real

## Instalação
Leia **docs/IMPLANTACAO.md**. Execute o SQL antes de subir a aplicação.

## Conteúdo
- `src/`: frontend React/Vite
- `src/parse.js`: leitor de exportações Google/Meta
- `supabase/schema.sql`: tabelas, políticas RLS, armazenamento
- `wrangler.jsonc`: referência de implantação Cloudflare
- `.env.example`: variáveis públicas

Não há credenciais, senhas ou segredos no pacote.
