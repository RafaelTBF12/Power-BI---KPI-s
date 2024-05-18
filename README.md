
# Integração do Microsoft Planner no Power BI para Acompanhamento de Entregas

## Entendimento do Problema

### Dashboard para Acompanhamento de Entregas Integrado com Microsoft Planner

Este dashboard ajuda a empresa a entender melhor as suas operações de entregas, integrando o Microsoft Planner ao Power BI através de uma API criada utilizando o Microsoft Graph. O objetivo é criar um dashboard que facilite a visualização das informações presentes no Planner e acompanhe as entregas de cada membro da equipe. Para realizar essa integração, foram seguidos os seguintes passos:

### Passos Seguidos

1. **Planejamento e Criação do Planner**:

- Levantamento de Tarefas: Identificação e documentação de todas as tarefas necessárias para o processo de entrega.
- Criação de Cronograma: Estabelecimento de um cronograma para a execução das tarefas, definindo prazos e responsáveis.
- Definição de Responsáveis: Atribuição das tarefas aos membros da equipe no Microsoft Planner.

<p align="center">
   <img src= "PLANNER OSU.jpeg">
   
2. **Carregar dados no Power BI Desktop**: Integração com Power BI
   
- Obtenção dos Dados: Utilização da API do Microsoft Graph para extrair dados do Microsoft Planner.
- Conexão com API Web: Configuração do Power BI para se conectar ao banco de dados do Planner utilizando o conector de API web.

<p align="center">
   <img src= "API GRAPH.jpeg">


3. **Preparação dos Dados no Power BI Desktop**:

- Abrir Power Query Editor: No Power Query Editor, ativar as opções "distribuição de coluna", "qualidade da coluna" e "perfil da coluna" na guia "Exibir" para uma melhor visualização dos dados.
- Verificação de Erros: Inspecionar as colunas para identificar e tratar erros ou valores vazios. Identificou-se que a coluna "Arrival Delay" apresentava valores inconsistentes.

4. **Colunas Condicionais**: Para calcular a curva S alguns formulas foram desenvolvidas:

    
    STATUS = 
        
        Table.AddColumn(Source, "Status", each if [percentComplete] = 0 then "Não Iniciada" else if [percentComplete] = 50 then "Em andamento" else if [percentComplete] = 100 then "Concluída" else null)


     S/A PLAN = 
        
       Table.AddColumn(#"Semana Real", "SA Plan", each Text.Combine({Text.From([Ano Plan], "pt-BR"), Text.From([Semana Plan], "pt-BR")}, "-"), type text)
   
     S/A REAL = 
        
        Table.AddColumn(#"SA Plan", "SA Real", each Text.Combine({Text.From([Ano Real], "pt-BR"), Text.From([Semana Real], "pt-BR")}, "-"), type text)
   
     TASKS ENCERRADAS = 
        
        Table.DuplicateColumn(#"Added Conditional Column1", "dueDateTime", "dueDateTime - Copy")   
   
     Calendar_suporte = 
        
        FILTER(
    DISTINCT(
        UNION(
            SELECTCOLUMNS('Consolidado';"SA";'Consolidado'[SA Plan]);
            SELECTCOLUMNS('Consolidado';"SA"; Consolidado[SA Real])
        )
    );
[SA] <> BLANK()
)

5. **Correlação de Queries**: As Queries foram integradas para atender as necessidades supracitadas, dessa forma posibilitamos a criação de visual que relacionacem as atividades aos usuários, data, bucktes do planner, áreas das atividades e ao status dela:

   <p align="center">
   <img height="240" right="130" src= "QUERIES.jpeg">  <img height="240" right="130" src= "VIEW MODELO.jpeg">


6. **Curva S**: Adicionar filtros visuais para campos relevantes, como "Região de Entrega", "Tipo de Produto", "Tipo de Cliente" e "Método de Entrega".


     Cálculo de Tarefas Concluídas e Planejadas Acumuladas = 
        
         CALCULATE([Total acumulado de SA Real]; FILTER(ALL('Calendar_suporte'[SA]); 'Calendar_suporte'[SA]<=MAX('Calendar_suporte'[SA])))
         CALCULATE([Soma de quantidade por SA Plam]; FILTER(ALL('Calendar_suporte'[SA]); 'Calendar_suporte'[SA]<=MAX('Calendar_suporte'[SA])))


     Cálculo das Proporções Acumuladas = 
        
         DIVIDE('consolidado'[S Curve (EEM) - sum cumulative complete tasks]; 'Consolidado'[S Curve - sum of all planner tasks accumulated];
BLANK())
         DIVIDE('Consolidado'[S Curve (EEM) - sum cumulative planned tasks]; 'Consolidado'[S Curve (EEM) - sum of all planned tasks accumulated];
BLANK())


Resultado final:

   <p align="center">
   <img src= "CURVA S.jpeg">

7. **Visuais Gerais**: Foram criados os visuais de tasks realizadas, atrasadas e planejadas, além do ranking de execução e tasks atrasadas:

   <p align="center">
   <img src= "IND GERAIS.jpeg">

8. **CRONOGRAMA**: Esse foi um gráfico criado para acompanhamentos das tasks em andamento no dia atual (Today), porém serve para vizualizar as proximas ações bem como as que já foram executadas:

   <p align="center">
   <img src= "CRONOGRAMA.jpeg">

09. **FOLLOW UP**: Por fim um follow up do planner facilita a contextualização do arquivo

   <p align="center">
   <img src= "FOLLOW UP.jpeg">

10. **Customização do Relatório**:

Inserção de Imagens e Formas: Adição de elementos gráficos, como retângulos e logotipo da empresa, para melhorar o design do relatório.

## Resultado Final

O dashboard criado fornece uma visão abrangente das operações de entrega, permitindo um acompanhamento eficiente das tarefas e facilitando a tomada de decisões estratégicas. Com a integração do Microsoft Planner ao Power BI, a empresa consegue monitorar o progresso das entregas e a performance da equipe em tempo real, promovendo uma gestão mais eficaz e proativa.
