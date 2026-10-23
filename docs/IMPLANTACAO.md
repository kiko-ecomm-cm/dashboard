# BI Construmarques V2 — implantação

## Status
Código-fonte inicial da V2, pronto para configuração, build e testes. **Não está publicado nem conectado a contas reais.**

## Pré-requisitos
- Node.js 20.19+ ou 22.12+
- Projeto Supabase **novo**; conta Cloudflare existente
- Acesso de administrador das duas plataformas; 1 e-mail de editor autorizado

## 1. Banco de dados e segurança
1. No Supabase, crie um novo projeto, por exemplo `bi-construmarques-v2`.
2. Entre no SQL Editor; execute `supabase/schema.sql` integralmente, uma única vez, em projeto vazio.
3. Cadastre o e-mail do editor com SQL (substitua pelo e-mail real):
   ```sql
   insert into public.allowed_users(email,role) values ('editor@empresa.com','editor');
   ```
   Para outros usuários, use `viewer` ou `editor`.
4. Em Authentication > Providers, deixe o login por e-mail ativo. Desabilite inscrições públicas e configure convites somente pelo administrador. Convide os e-mails autorizados no painel Supabase Authentication > Users.
5. Em Authentication > URL Configuration, configure `Site URL` e `Redirect URLs` com o endereço efetivo da aplicação Cloudflare (após o deploy).
6. Confirme o bucket `campaign-creatives` como **privado**, não público, no Storage.
7. No Supabase Project Settings > API, obtenha a Project URL e a **publishable key** (`anon` legado, se aplicável). **Nunca coloque service_role / secret key no frontend.**

## 2. Aplicação local
1. Copie `.env.example` para `.env` e preencha SOMENTE as variáveis públicas:
   `VITE_SUPABASE_URL` e `VITE_SUPABASE_ANON_KEY`.
2. No terminal, execute:
   ```bash
   npm install
   npm run dev
   ```
3. Acesse o endereço local indicado, entre com o e-mail autorizado e teste com arquivos de setembro.
4. Teste a importação de **um canal/mês por vez**. Revise os totais do relatório antes de confirmar.

## 3. Cloudflare — publicação
Recomendação: deploy Git integrado ao Cloudflare Workers com assets estáticos ou fluxo Wrangler.
1. Publique o código em repositório privado próprio (não suba `.env`).
2. No Cloudflare Workers & Pages, crie projeto conectado ao repositório e configure build `npm run build`, saída `dist`.
3. Defina as variáveis de build `VITE_SUPABASE_URL` e `VITE_SUPABASE_ANON_KEY` com as **chaves públicas** do Supabase. Variáveis Vite entram no bundle público: não usar chaves privadas.
4. Gere o deploy. Use o endereço temporário atribuído pela Cloudflare; configure este endereço no Supabase Authentication > URL Configuration.
5. Faça login, importe as planilhas, envie uma imagem e valide permissões com usuário `viewer`.
6. Alternativa via CLI (ambiente configurado): `npm run build && npx wrangler deploy`.

## 4. Rotina mensal
1. Exporte Google Ads e Meta Ads no nível **campanha**, para o mês completo e na mesma moeda.
2. No BI, entre em Importar dados, escolha canal e mês; selecione um arquivo XLSX/CSV.
3. Verifique a prévia e clique Confirmar. O canal/mês será atualizado; dados de outros meses permanecem.
4. Envie imagens em cada campanha na seção Painel ou Biblioteca.
5. Valide o total de investimento com as plataformas.

## 5. Regras de métricas
- Google `Conversões`: exibidas como **Retornos registrados** (quantidade), podendo ser fracionárias; não presumir quantidade de vendas.
- Google `Valor conv.`: exibido como **Retorno em R$**, que corresponde ao valor de conversão informado pelo Google, não a receita financeira auditada.
- ROAS Google = soma dos valores de conversão / soma dos gastos, somente para registros com valor disponível.
- Meta `Compras`: quantidade de compras atribuídas quando presente. Não inventar receita/ROAS.
- Meta `Alcance` não é somado entre campanhas, para evitar dupla contagem de pessoas.
- `Resultados` Meta depende do indicador: não chamar automaticamente de compra.
- Campanhas repetidas na mesma planilha são agregadas por nome; para alcance em duplicatas, o dado fica indisponível porque não é aditivo.

## 6. Cuidados e limitações desta entrega
- Parser adaptado às planilhas de setembro/2026; novos cabeçalhos devem ser homologados antes de uso definitivo.
- Importação é substituição lógica por (canal, mês, nome normalizado). Não é transação única; se houver falha no meio, confira o período antes de repetir. Dados não possuem trilha de auditoria linha-a-linha.
- Permissão `editor` permite sobrescrever importações do mês. Recomenda-se backup do Supabase antes de cargas importantes.
- Imagens são associadas a canal + nome normalizado, persistindo entre meses. Mudanças de nome criam nova associação; melhoria futura: Campaign ID.
- Link assinado das imagens expira em 1 hora. Recarregue a página para renovar.
- Esta aplicação não consulta APIs do Ads, nem faz leitura contínua do Google Drive, nem usa IA.
- O sistema ainda não tem QA de produção, monitoramento, backups automáticos, revisão completa de acessibilidade ou testes de integração end-to-end.
- Ao ser usado corporativamente, restrinja convites e confirme requisitos de privacidade/LGPD antes de incluir dados pessoais.
