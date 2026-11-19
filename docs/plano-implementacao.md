# Plano de implementação

| Fase | Entrega e verificação |
| --- | --- |
| 1. Ambiente e Git | Diagnosticar ferramentas e integrações, inicializar o Git e validar estrutura e regras de exclusão. |
| 2. Arquitetura e modelagem | Desenhar os modelos transacional e dimensional. Documentar grãos, chaves, cardinalidades, dicionário de dados e decisões técnicas. Revisar como os filtros de período e região atingirão vendas e metas. |
| 3. SQL e migrações | Criar migrações incrementais e consultas analíticas. Verificar a reprodução do banco e a integridade referencial em ambiente de teste. |
| 4. Pipeline Python | Gerar dados sintéticos, validar regras e carregar o PostgreSQL. Verificar repetibilidade por semente, idempotência da carga e relatórios de qualidade. |
| 5. Integração com Power BI | Validar a conexão com credenciais de leitura e a abertura do projeto local. Conferir tipos e atualização das tabelas. |
| 6. Modelo semântico e DAX | Configurar calendário, relações e medidas. Comparar resultados DAX e SQL em recortes equivalentes. |
| 7. Painel executivo | Implementar a referência visual em 16:9. Testar navegação, filtros, tooltips e legibilidade. Comparar a página final à imagem fornecida. |
| 8. Testes e publicação | Consolidar testes, GitHub Actions e documentação. Avaliar opções de demonstração e compartilhamento antes de publicar. |

## Versionamento

O repositório começou em `main`. A fase 2 usa a branch local `feature/modelagem-dados`. A estratégia proposta para integração usa `develop` e branches `feature/*` por entrega, com Conventional Commits e pull requests. Publicações remotas dependem de aprovação.

As pastas reservadas têm um arquivo `.gitkeep` porque o Git não versiona diretórios vazios.
