Sistema de Controle de Estoque Pro
Documentação Técnica e Funcional
1. Introdução e Escopo
O Controle de Estoque Pro é uma aplicação web desenvolvida para a gestão automatizada de inventário de materiais hospitalares e insumos gerais. Trata-se de um sistema de camada de apresentação (front-end) desenvolvido em HTML5, CSS3 e JavaScript puro, sem dependência de frameworks ou infraestrutura de servidor própria para execução. A persistência dos dados é realizada por meio da API REST do serviço JSONBin.io, que oferece armazenamento de documentos JSON na nuvem.
O sistema foi concebido para substituir processos manuais baseados em planilhas eletrônicas e formulários em papel, proporcionando rastreabilidade completa das operações, controle de permissões por perfil de usuário e sincronização de dados em tempo real entre múltiplos dispositivos.
2. Arquitetura Tecnológica
Planilhas
Camada	Tecnologia / Serviço	Descrição
Apresentação	HTML5, CSS3, JavaScript (ES6+)	Interface responsiva, com suporte a temas claro e escuro.
Persistência Remota	JSONBin.io (API REST)	Armazenamento e sincronização de dados estruturados em JSON.
Persistência Local	Web Storage API (localStorage)	Armazenamento de preferências do usuário (tema, credenciais de API, senha administrativa).
Dados de Contingência	Estrutura JSON embutida no código-fonte	Conjunto de 216 registros de produtos carregados automaticamente caso a API remota esteja indisponível.
Responsividade	Media Queries CSS3	Adaptação dinâmica da interface para dispositivos móveis (smartphones e tablets).
3. Modelo de Dados
A estrutura de dados principal do sistema é composta pelas seguintes entidades:
Planilhas
Entidade	Atributos Principais	Finalidade
Produtos	id, nome, categoria, unidade, estoque_min, estoque_max, saldo_atual, preco_custo, preco_venda, fornecedor, localizacao, observacoes	Cadastro completo dos itens do inventário.
Movimentações	id, produtoId, tipo (ENTRADA/SAÍDA/DEVOLUÇÃO/AJUSTE), qtd, data, usuario, destino, nf, obs	Registro histórico de todas as operações de entrada e saída.
Pedidos	id, data, solicitante, setor, status (PENDENTE/APROVADO/REJEITADO), itens[], aprovadoPor, dataAprovacao	Requisições de materiais por setores/filiais, com fluxo de aprovação.
Logs	id, data, usuario, acao, detalhes	Auditoria de todas as ações realizadas no sistema.
Categorias	Array de strings	Classificação dos produtos em grupos administrativos.
4. Controle de Acesso e Perfis de Usuário
O sistema implementa um modelo de controle de acesso baseado em três perfis hierárquicos:
Planilhas
Perfil	Permissões
Consulta / Visitante	Visualização de dashboards, tabelas de estoque, relatórios, lista de compras e histórico de pedidos. Não possui permissão para alterar dados.
Operador	Permissões de consulta + registro de movimentações de estoque (entradas, saídas e devoluções) + elaboração de pedidos de materiais.
Administrador	Acesso irrestrito: cadastro, edição e exclusão de produtos; aprovação e rejeição de pedidos; gerenciamento de categorias; configuração da API; alteração da senha administrativa.
A autenticação é realizada por meio de uma senha global única, armazenada no localStorage do navegador. O sistema não implementa criptografia de senhas nem autenticação por sessão, sendo adequado para ambientes de prototipagem e controle de acesso operacional, mas não para cenários que exijam segurança da informação conforme padrões corporativos.
5. Módulos Funcionais
5.1 Dashboard
Módulo de inteligência operacional que apresenta indicadores-chave de desempenho (KPIs) em tempo real: quantidade total de produtos cadastrados, itens em situação de alerta de estoque, valor monetário do inventário e volume de movimentações do dia corrente. Inclui visualizações gráficas de distribuição por categoria e histórico de movimentações dos últimos sete dias, além de listagem dos produtos com estoque crítico.
5.2 Gestão de Estoque
Módulo central do sistema, responsável pela visualização e manutenção do inventário. Oferece funcionalidades de busca textual, filtragem por categoria e status, ordenação por múltiplos critérios e paginação de resultados. Cada produto possui indicador visual de ocupação percentual em relação ao estoque máximo. O módulo contempla ainda a funcionalidade de edição em massa de categoria, permitindo a reclassificação simultânea de múltiplos itens, e exportação dos dados nos formatos CSV e JSON.
5.3 Registro de Movimentações
Interface para registro de operações de entrada, saída e devolução de produtos. O sistema realiza validação automática de saldo disponível antes de autorizar operações de saída, impedindo registros que resultariam em estoque negativo. Cada movimentação gera automaticamente um registro de auditoria no módulo de Logs.
5.4 Módulo de Pedidos (Requisições)
Subsistema desenvolvido para substituir o processo manual de solicitação de materiais por setores ou filiais. O usuário seleciona produtos em uma lista estruturada (com filtros por categoria e busca textual), informa as quantidades desejadas e preenche os dados do setor solicitante. O sistema calcula a disponibilidade real do estoque, descontando as quantidades já reservadas em pedidos pendentes de aprovação, e bloqueia o envio caso haja insuficiência.
5.5 Módulo de Aprovação de Pedidos
Interface exclusiva do perfil Administrador para gestão do fluxo de aprovação. Os pedidos pendentes são apresentados em cards detalhados, com informações do solicitante, setor, data, itens requisitados e saldo disponível atualizado. O administrador pode:
Aprovar: o sistema executa automaticamente a baixa do estoque e registra a movimentação de saída vinculada ao pedido.
Rejeitar: o pedido é cancelado e arquivado com a identificação do responsável pela rejeição.
5.6 Lista de Compras Sugerida
Módulo automatizado que gera lista de aquisição com base nos produtos cujo saldo atual encontra-se igual ou inferior ao estoque mínimo. Calcula a quantidade sugerida de reposição (estoque_máximo − saldo_atual) e o custo estimado total, com opção de exportação para CSV.
5.7 Relatórios e Análises
Conjunto de ferramentas analíticas que inclui:
Curva ABC: classificação dos produtos por valor acumulado em estoque (classe A: 70% do valor; classe B: 20%; classe C: 10%).
Movimentação por Período: gráfico de evolução de entradas e saídas com filtros de 7, 30, 90 e 365 dias.
Ranking de Produtos: ordenação decrescente por volume total de movimentação.
5.8 Auditoria (Logs)
Registro cronológico de todas as operações realizadas no sistema, incluindo cadastros, edições, exclusões, movimentações, ajustes de inventário e operações de pedidos. Os registros são limitados a 500 entradas, com remoção automática dos registros mais antigos.
5.9 Cadastro de Produtos
Formulário estruturado para inclusão de novos itens no inventário, com campos obrigatórios (nome, unidade, estoque mínimo, estoque máximo, saldo inicial) e campos opcionais (categoria, fornecedor, preços, localização, observações).
5.10 Configurações do Sistema
Módulo administrativo para gestão de categorias, exportação de backup completo em JSON, importação de dados a partir de arquivo, alteração da senha de acesso e configuração dos parâmetros da API de sincronização.
6. Regras de Negócio Implementadas
Planilhas
Regra	Descrição
Validação de Estoque	Operações de saída são bloqueadas quando o saldo é insuficiente.
Reserva por Pedido	Produtos em pedidos pendentes têm suas quantidades subtraídas do saldo disponível.
Status Automático	Produtos são classificados automaticamente como OK, Alerta ou Crítico com base nos limites de estoque.
Auditoria Obrigatória	Toda operção de alteração gera um registro de log com identificação do usuário, data/hora e descrição da ação.
Desfazer (Undo)	O sistema mantém um snapshot dos dados antes de operações críticas, permitindo a reversão da última ação.
Paginação	Listagens extensas (estoque e logs) são paginadas em lotes de 10 registros.
7. Fluxo de Sincronização de Dados
plain
┌─────────────────┐     ┌──────────────────────┐     ┌─────────────────┐
│   Navegador     │────▶│   JSONBin.io (API)   │────▶│   Nuvem         │
│   (Cliente)     │◄────│   (Persistência)     │◄────│   (JSON)        │
└─────────────────┘     └──────────────────────┘     └─────────────────┘
         │
         ▼ (fallback)
┌─────────────────┐
│ Dados Embutidos │
│ (216 produtos)  │
└─────────────────┘
Ao carregar, o sistema tenta sincronizar com a API remota. Em caso de indisponibilidade ou falha de autenticação, os dados embutidos no código-fonte são carregados como contingência, garantindo a operacionalidade do sistema mesmo offline.
8. Considerações sobre Segurança e Escalabilidade
O sistema, na presente configuração, opera inteiramente na camada de cliente (client-side). Isso implica que:
As credenciais de API (Master Key) e a senha administrativa são armazenadas em texto plano no código-fonte e no localStorage.
Não há criptografia de dados em trânsito além do TLS/SSL do protocolo HTTPS.
Não há controle de sessão, tokens de autenticação ou hash de senhas.
Para ambientes de produção, recomenda-se a migração para uma arquitetura com back-end próprio (Node.js, PHP, Python, etc.), banco de dados relacional (MySQL, PostgreSQL) e implementação de autenticação segura (JWT, OAuth 2.0).
9. Conclusão
O Controle de Estoque Pro representa uma solução funcional e de baixo custo para a digitalização da gestão de inventário. Seus principais diferenciais são a sincronização em nuvem sem infraestrutura própria, o fluxo de aprovação de pedidos com reserva automática de estoque e a interface responsiva que permite operação em desktops, tablets e smartphones. O sistema elimina a dependência de planilhas manuais, reduz erros de digitação, proporciona rastreabilidade completa e gera subsídios para decisões de compra baseadas em dados reais.
