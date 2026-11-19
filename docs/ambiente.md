# Ambiente verificado em 19/11/2026

| Item | Resultado |
| --- | --- |
| Workspace | A pasta estava vazia e fora de um repositório. O Git foi inicializado localmente em `main`. |
| Git | 2.56.0.windows.4, com Git Credential Manager configurado. |
| GitHub | O conector autenticado retornou acesso administrativo ao repositório público, tamanho zero e branch padrão configurada como `main`. O endereço foi registrado como `origin` local. |
| Supabase | O conector encontrou o projeto BI de Vendas ativo, com PostgreSQL 17. A listagem de tabelas do esquema `public` retornou vazia. |
| Python e pip | O executável no PATH respondeu Python 3.15.2 e pip 26.2.2. O launcher `py -0p` não listou instalações. |
| Power BI Desktop | Aplicativo identificado no menu Iniciar e no executável instalado, versão 2.159.1231.0 (26.10). A abertura de um PBIP será validada na fase 5. |
| Node.js e npm | Node.js 26.13.0 e npm 11.20.0. |
| Ferramentas de linha de comando | GitHub CLI, Supabase CLI e `psql` não foram encontrados no PATH. |

A conexão do Supabase foi verificada pelo conector. O acesso PostgreSQL direto a partir de Python ou Power BI ainda precisa ser testado. Na fase 4, obtenha os parâmetros pelo painel do projeto e forneça as credenciais ao processo via variáveis de ambiente ou arquivo `.env` local ignorado pelo Git. Na fase 5, use o armazenamento de credenciais do Power BI com um usuário de leitura.

Antes de fixar a versão do Python para o pipeline, valide a disponibilidade das dependências para a versão instalada e alinhe a versão local à usada na integração contínua.
