# SIGEPI — Sistema de Gestão de Entrega de EPIs

> Plataforma web responsiva para registrar estoque, entrega, aceite ou recusa de Equipamentos de Proteção Individual e manter evidências consultáveis para auditoria.

## Sobre o projeto

O SIGEPI propõe substituir controles fragmentados em planilhas, fichas de papel e arquivos dispersos por um fluxo digital rastreável. O sistema organiza o cadastro de servidores e EPIs, movimentações de estoque, entregas, recusas justificadas, assinaturas, evidências e relatórios por servidor, secretaria, equipamento e lote.

O projeto é direcionado inicialmente a prefeituras e órgãos públicos de pequeno e médio porte. Este repositório documenta a proposta e contém um protótipo navegável de interface. Ainda não é um sistema de produção: autenticação, banco de dados, armazenamento seguro de fotos e emissão oficial de documentos precisam ser implementados e validados.

### Objetivos

- Centralizar o histórico de fornecimento de EPIs.
- Tornar o registro de entrega e recusa fácil de consultar e exportar.
- Dar visibilidade ao estoque, lotes, compras e previsão de reposição.
- Apoiar o trabalho do Técnico de Segurança do Trabalho (SST), almoxarifado, gestores e RH.
- Criar uma experiência simples em computador, tablet e celular.

### Escopo inicial (MVP)

1. Cadastro e consulta de servidores, secretarias e departamentos.
2. Catálogo de EPIs com fabricante, modelo, CA, lote, validade e referência de compra/licitação.
3. Entradas, saídas e saldo de estoque por item e lote.
4. Entrega com aceite por item ou recusa parcial/total, motivo obrigatório e observação opcional.
5. Assinatura desenhada na tela e geração de termo PDF associado ao registro.
6. Histórico e relatórios básicos com filtros por servidor, secretaria, período, EPI e lote.
7. Perfis e permissões: SST, almoxarifado, gestor de secretaria e RH.

### Evolução planejada

- Captura de fotos, alertas de reposição e vencimento, geolocalização opcional, modo de baixa conectividade e integrações.
- Sugestões de EPIs por atividade, baseadas em regras configuradas e aprovadas pelo SST. A ferramenta deve apoiar a decisão, nunca determinar sozinha o EPI legalmente exigido. A seleção depende dos riscos ocupacionais e dos documentos de SST aplicáveis.

## Fluxo principal de entrega

1. O operador identifica o servidor e confirma secretaria, cargo e local.
2. Seleciona os EPIs, quantidades e lotes disponíveis.
3. O servidor confirma individualmente cada item como aceito ou recusado.
4. Para cada recusa, o motivo é obrigatório: equipamento em boas condições, incompatibilidade, recusa pessoal ou outro. A explicação livre é opcional.
5. O operador revisa os dados; o servidor assina na tela; o sistema finaliza o termo e registra o evento.
6. O histórico fica disponível conforme o perfil de acesso e pode ser exportado para auditoria.

## Perfis e permissões

| Perfil | Acesso previsto |
|---|---|
| Técnico de SST | Catálogo, recomendações revisadas, entregas, termos, evidências e relatórios de SST |
| Almoxarifado | Recebimentos, compras/lotes, ajustes justificados e consulta de saldo |
| Gestor da secretaria | Consulta das entregas da própria equipe e indicadores permitidos |
| RH | Consulta do histórico funcional e de entregas necessário às suas atribuições |
| Administrador | Configuração institucional, usuários, perfis e trilhas de auditoria |

As permissões devem seguir o princípio do menor privilégio. CPF completo, fotos, assinatura e coordenadas não devem aparecer em telas ou relatórios para quem não precisa desses dados.

## Tecnologias, ferramentas e infraestrutura

### Stack proposta

| Camada | Tecnologia | Uso |
|---|---|---|
| Interface | Next.js + React + TypeScript | Aplicação responsiva, acessível e preparada para instalação como PWA |
| Componentes visuais | Material UI | Componentes consistentes e adaptação a telas móveis |
| API | Java + Spring Boot | Regras de negócio, validações, permissões e geração de documentos |
| Persistência | PostgreSQL | Dados relacionais e consultas auditáveis |
| Fotos e PDFs | Armazenamento de objetos privado compatível com S3 | Arquivos separados do banco, com URLs temporárias e controle de acesso |
| Identidade | OpenID Connect/OAuth 2.0 | Integração futura com provedor institucional de identidade |
| Empacotamento | Docker e Docker Compose | Ambiente local reproduzível e preparação para implantação |
| Automação | GitHub Actions | Verificações automatizadas de qualidade e publicação após aprovação |
| Design | Figma ou protótipo HTML deste repositório | Fluxos, componentes e validação inicial com usuários |

A proposta começa como uma aplicação web responsiva/PWA. Um aplicativo Flutter ou React Native pode ser avaliado depois de validar a necessidade de captura offline e recursos nativos; não é obrigatório no MVP.

### Infraestrutura e ambientes

- **Desenvolvimento:** Docker Compose com API, interface e PostgreSQL, usando dados fictícios.
- **Homologação:** ambiente separado para validação por SST, almoxarifado, RH e gestores.
- **Produção:** hospedagem institucional ou serviço contratado, com TLS, backups criptografados, monitoramento, gestão de segredos e armazenamento privado de anexos.
- **Arquivos:** imagens e PDFs não devem ficar em repositório público nem em diretório web público.
- **Continuidade:** definir RPO/RTO, rotina de restauração de backup e prazo de retenção com o órgão antes da implantação.

### Segurança, privacidade e auditoria

O sistema poderá tratar CPF, assinatura, imagem e localização vinculados a servidores. Antes do uso real, o órgão deve definir finalidade, base legal, transparência, retenção, atendimento aos titulares e responsabilidades de controlador/operador com seu encarregado e assessoria competente. Este README não substitui análise jurídica nem avaliação de segurança.

Requisitos de projeto:

- HTTPS, criptografia em trânsito e proteção de dados armazenados.
- Acesso por perfil, sessões seguras e autenticação institucional quando disponível.
- Auditoria append-only de eventos relevantes: ator, data/hora, ação, registro afetado e origem, sem permitir apagar silenciosamente um termo finalizado.
- Retificação por evento complementar, preservando a trilha anterior.
- Coleta mínima: CPF mascarado nas telas; localização e foto somente quando justificadas e configuradas.
- Política de retenção e exclusão de anexos aprovada pelo órgão; acesso a arquivos com autorização e expiração.
- Logs técnicos sem CPF, conteúdo de assinatura, imagem ou coordenadas desnecessárias.
- Proteção contra upload malicioso, validação de tipo/tamanho e backup testado.

## Metodologia de desenvolvimento

### Abordagem: Scrum leve com entregas incrementais e prototipação

O problema envolve perfis e rotinas diferentes, e detalhes operacionais serão descobertos com usuários reais. Por isso, o desenvolvimento será dividido em ciclos curtos, com demonstração ao final de cada ciclo e revisão do backlog. A prototipação antecipada reduz dúvidas sobre o fluxo de entrega antes de investir na implementação.

Uma abordagem puramente cascata exigiria fechar cedo requisitos que podem mudar após a validação com SST, almoxarifado, RH e gestores. O modelo espiral será usado como referência para tratar riscos relevantes — privacidade, integridade do histórico, assinatura, fotos e disponibilidade — com análise e validação antes de ampliar o escopo.

### Ciclo de trabalho sugerido

1. **Descoberta:** mapear fluxo atual, usuários, regras locais, dados e riscos.
2. **Protótipo:** desenhar telas e validar tarefas com representantes dos perfis.
3. **Implementação incremental:** priorizar estoque e entrega; depois histórico, relatórios e integrações.
4. **Revisão:** demonstrar a entrega, registrar feedback e ajustar o backlog.
5. **Preparação para implantação:** homologação, migração, treinamento, segurança e operação.

### Backlog inicial

- **Épico 1 — Acesso e cadastros:** usuários, perfis, servidores, secretarias e departamentos.
- **Épico 2 — Catálogo e estoque:** EPIs, lotes, recebimentos, compras e saldo.
- **Épico 3 — Entregas:** seleção, aceite/recusa por item, justificativa e assinatura.
- **Épico 4 — Evidências e histórico:** termo, anexos autorizados e trilha auditável.
- **Épico 5 — Consulta e relatórios:** pesquisa, filtros, exportação e indicadores.
- **Épico 6 — Alertas e apoio SST:** regras configuráveis, revisão humana e notificações.

### Critérios gerais de pronto

- Regra de negócio documentada e revisada pelo perfil responsável.
- Interface utilizável por teclado e em telas pequenas.
- Permissões conferidas para cada operação e consulta.
- Erros e alterações relevantes registrados sem expor dados desnecessários.
- Revisão de código concluída; verificações automatizadas aprovadas.
- Instruções atualizadas e evidência de validação em homologação quando aplicável.

## Como será usado o Git e o GitHub

- `main` contém versões estáveis e protegidas; alterações entram por Pull Request.
- Criar branches curtas com nomes como `feat/registro-entrega`, `fix/validacao-recusa` e `docs/fluxo-git`.
- Commits pequenos, no imperativo e com escopo claro, por exemplo: `feat: registrar recusa por item`.
- Abrir Pull Request com objetivo, contexto, evidências visuais, riscos e checklist de revisão.
- Relacionar PRs a Issues; usar Projects para organizar backlog e marcos.
- Solicitar revisão de pelo menos uma pessoa antes de integrar; resolver comentários e manter histórico.
- Usar GitHub Actions para lint, compilação e verificações automatizadas. Proteger segredos em GitHub Secrets; nunca versionar credenciais ou dados reais.
- Marcar entregas com tags de versão, por exemplo `v0.1.0`, e manter notas de versão.

## Projeto de interface

O protótipo navegável está em [`docs/prototipo/index.html`](docs/prototipo/index.html). Abra o arquivo localmente no navegador. Ele ilustra três estados principais: painel geral, estoque e fluxo de nova entrega. Os números e nomes são dados fictícios, e as ações são demonstrativas; não existe persistência nem captura real de assinatura, foto ou geolocalização.

Telas previstas para o produto:

1. **Entrar:** autenticação institucional e mensagens de acesso.
2. **Painel:** saldo, entregas recentes, recusas e alertas.
3. **Estoque:** filtros por EPI, lote, CA e validade; entrada e ajuste justificado.
4. **Nova entrega:** identificação do servidor, itens, aceite/recusa por item e revisão.
5. **Assinatura e termo:** conferência, assinatura e comprovante PDF.
6. **Histórico:** busca e linha do tempo por servidor.
7. **Relatórios:** exportação conforme perfil e secretaria autorizada.

## Modelo conceitual inicial

Entidades principais: `Servidor`, `Secretaria`, `Departamento`, `EPI`, `Lote`, `Compra`, `MovimentacaoEstoque`, `Entrega`, `ItemEntrega`, `Evidencia`, `Usuario`, `Perfil` e `EventoAuditoria`.

Uma entrega contém vários itens; cada item aponta para um EPI/lote e registra quantidade, decisão (aceito/recusado), justificativa e data. Eventos posteriores corrigem ou complementam o histórico sem substituir o registro original.

## Estrutura do repositório

```text
.
├── README.md
└── docs/
    └── prototipo/
        └── index.html
```

## Como visualizar o protótipo

1. Clone ou baixe este repositório.
2. Abra `docs/prototipo/index.html` em um navegador moderno.
3. Use o menu lateral para alternar entre painel, estoque e entrega.

## Referências

- [NR-6 — Equipamento de Proteção Individual (Ministério do Trabalho e Emprego)](https://www.gov.br/trabalho-e-emprego/pt-br/acesso-a-informacao/participacao-social/conselhos-e-orgaos-colegiados/comissao-tripartite-partitaria-permanente/normas-regulamentadora/normas-regulamentadoras-vigentes/norma-regulamentadora-no-6-nr-6) — consultar texto vigente e requisitos aplicáveis.
- [Perguntas e respostas sobre EPI (Ministério do Trabalho e Emprego)](https://www.gov.br/trabalho-e-emprego/pt-br/assuntos/inspecao-do-trabalho/seguranca-e-saude-no-trabalho/equipamentos-de-protecao-individual-epi/perguntas_e_respostas).
- [Perguntas frequentes sobre a LGPD (ANPD)](https://www.gov.br/anpd/pt-br/acesso-a-informacao/perguntas-frequentes).
- Projetos de inspiração informados: [UniMove](https://github.com/Femadur0/UniMove), [Projeto Integrador](https://github.com/Angela-Leao/projeto-integrador--semestre-1), [AbasteceJá](https://github.com/AleSabino963/AbasteceJa), [ProjectGuilda](https://github.com/WhitemanKMKZ/ProjectGuilda-JLLPV), [Repositório Remy](https://github.com/AleSabino963/Repositorio-Remy) e [Descarte Inteligente](https://github.com/alexromagnoli/Descarte-inteligente).

## Licença

A licença ainda deve ser escolhida pelo responsável pelo projeto antes da publicação. Evite indicar uma licença sem confirmar que todos os autores concordam com seus termos.


## Próximos passos:

1. Orientação e treinamento no momento da entrega. Registrar quais instruções foram dadas sobre uso, limitações, ajuste, conservação e manutenção do EPI, além de guardar treinamentos quando aplicáveis. A NR-6 prevê essas informações e treinamentos conforme as características do equipamento e da atividade. 
2. Recomendação por atividade e risco, não só pelo cargo. Vincular cada recomendação aos riscos avaliados e à referência usada, como o PGR, e registrar quem do SST revisou e aprovou. A NR-6 considera atividade, riscos, eficácia, adequação e compatibilidade dos EPIs na seleção. Isso também dá uma base mais sólida para o módulo de IA: ele sugere e explica; o responsável valida. 
3. Ciclo de vida do equipamento. Criar registros para EPI danificado, perdido, devolvido, em manutenção ou descartado, com motivo e movimentação correspondente no estoque. Isso ajuda a evitar que um equipamento impróprio continue aparecendo como disponível.
4. QR Code para lotes e movimentações. Uma leitura rápida no celular poderia localizar o EPI, CA, lote e validade durante o recebimento ou a entrega, reduzindo erros de digitação.
5. Uso em locais com internet instável. Permitir preparar registros no celular e sincronizá-los depois, indicando claramente o que ainda não foi enviado ao sistema.
