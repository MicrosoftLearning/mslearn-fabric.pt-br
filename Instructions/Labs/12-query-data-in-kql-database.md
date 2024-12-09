---
lab:
  title: Introdução à consulta de um banco de dados KQL no Microsoft Fabric
  module: Query data from a KQL database in Microsoft Fabric
---

# Introdução à consulta de um banco de dados KQL no Microsoft Fabric

Um Conjunto de Consultas KQL é uma ferramenta que permite executar consultas, além de modificar e exibir resultados de consultas de um banco de dados KQL. Você pode vincular cada guia no Conjunto de Consultas KQL a um banco de dados KQL diferente, além de salvar suas consultas para uso futuro ou compartilhá-las com outras pessoas para fins de análise de dados. Você também pode alternar o banco de dados KQL para qualquer guia, de modo a poder comparar os resultados de consultas de diferentes fontes de dados.

Neste exercício, você desempenhará a função de um analista encarregado de consultar um conjunto de dados de corridas de táxi em Nova York. Você usa KQL para consultar esses dados, coletar informações e obter insights informativos sobre os dados.

> **Dica**: para criar consultas, o Conjunto de Consultas KQL usa a Linguagem de Consulta Kusto, que é compatível com diversas funções SQL. Para saber mais sobre KQL, consulte [Visão geral do KQL (Linguagem de Consulta Kusto)](https://learn.microsoft.com/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext).

Este laboratório leva cerca de **25** minutos para ser concluído.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um espaço de trabalho com a capacidade do Fabric habilitada.

1. Na [home page do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric), em `https://app.fabric.microsoft.com/home?experience=fabric`, selecione **Inteligência em Tempo Real**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um Eventhouse

1. Na página inicial da **Inteligência em Tempo Real**, crie um novo **Eventhouse** com um nome de sua escolha. Quando o eventhouse tiver sido criado, feche todos os prompts ou dicas exibidos até ver a página do eventhouse:

   ![Captura de tela de um novo eventhouse.](./Images/create-eventhouse.png)
   
1. No menu **...** do banco de dados KQL que foi criado no eventhouse, selecione **Obter dados** > **Amostra**. Em seguida, escolha os dados de amostra **Análise de operações automotivas**.

1. Depois que os dados terminarem de carregar, verifique se uma tabela **Automotiva** foi criada.

   ![Captura de tela da tabela Automotiva em um banco de dados do eventhouse.](./Images/choose-automotive-operations-analytics.png)

## Consultar dados usando o KQL

O KQL (Linguagem de Consulta Kusto) é uma linguagem intuitiva e abrangente que você pode usar para consultar um banco de dados KQL.

### Recuperar dados de uma tabela com o KQL

1. No painel esquerdo da janela do eventhouse, no banco de dados KQL, selecione o arquivo de **conjunto de dados** padrão. Este arquivo contém alguns exemplos de consultas KQL para você começar.
1. Modifique a primeira consulta de exemplo da seguinte maneira.

    ```kql
    Automotive
    | take 100
    ```

    > **OBSERVAÇÃO:** o caractere de barra vertical ( | ) é usado para duas finalidades no KQL, incluindo a utilização de operadores de consulta separados em uma instrução de expressão tabular. Ele também é usado como um operador OR lógico dentro de colchetes ou colchetes redondos para indicar que você pode especificar um dos itens separados pelo caractere de pipe.

1. Selecione o código de consulta e execute-o para retornar 100 linhas da tabela.

   ![Captura de tela do editor de consultas KQL.](./Images/kql-take-100-query.png)

    Podemos ser mais precisos adicionando atributos específicos que gostaríamos de consultar usando a palavra-chave `project` e, em seguida, usando a palavra-chave `take` para informar ao mecanismo quantos registros retornar.

1. Digite, selecione e execute a seguinte consulta:

    ```kql
    // Use 'project' and 'take' to view a sample number of records in the table and check the data.
    Automotive 
    | project vendor_id, trip_distance
    | take 10
    ```

    > **OBSERVAÇÃO:** o uso de // denota um comentário.

    Outra prática comum na análise é renomear colunas em nosso conjunto de consultas para torná-las mais amigáveis.

1. Experimente a seguinte consulta:

    ```kql
    Automotive 
    | project vendor_id, ["Trip Distance"] = trip_distance
    | take 10
    ```

### Resumir os dados usando KQL

Você pode usar a palavra-chave *resumir* com uma função para agregar e manipular dados.

1. Tente a seguinte consulta, que usa a função de **soma** para resumir os dados da viagem a fim de ver quantas milhas foram percorridas no total:

    ```kql

    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance)
    ```

    Você pode agrupar os dados resumidos por uma coluna ou expressão especificada.

1. Faça a consulta a seguir para agrupar as distâncias de viagem por bairro dentro do sistema de táxi de NY para determinar a distância total percorrida de cada bairro.

    ```kql
    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = pickup_boroname, ["Total Trip Distance"]
    ```

    Os resultados incluem um valor em branco, o que nunca é bom para análise.

1. Modifique a consulta conforme mostrado aqui para usar a função *case* junto com as funções *isempty* e *isnull* para agrupar todas as viagens para as quais o bairro é desconhecido em uma categoria ***Não identificada*** para acompanhamento.

    ```kql
    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
    ```

### Classificar dados usando KQL

Para dar mais sentido aos nossos dados, normalmente os ordenamos por uma coluna, e esse processo é feito no KQL com um operador *classificar por* ou *ordernar por* (eles agem da mesma maneira).

1. Experimente a seguinte consulta:

    ```kql
    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
    | sort by Borough asc
    ```

1. Modifique a consulta da seguinte maneira e execute-a novamente, e observe que o operador *ordenar por* funciona da mesma forma que *classificar por*:

    ```kql
    Automotive
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
    | order by Borough asc 
    ```

### Filtrar dados usando KQL

No KQL, a cláusula *where* é usada para filtrar dados. Você pode combinar condições em uma cláusula *where* usando operadores lógicos *and* e *or*.

1. Execute a seguinte consulta para filtrar os dados de viagem para incluir apenas viagens originadas em Manhatten:

    ```kql
    Automotive
    | where pickup_boroname == "Manhattan"
    | summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
    | project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
    | sort by Borough asc
    ```

## Consultar dados usando Transact-SQL

O Banco de Dados KQL não dá suporte ao Transact-SQL nativamente, mas fornece um ponto de extremidade T-SQL que emula o Microsoft SQL Server e permite que você execute consultas T-SQL em seus dados. O ponto de extremidade T-SQL tem algumas limitações e diferenças em relação ao SQL Server nativo. Por exemplo, ele não dá suporte à criação, alteração ou remoção de tabelas ou à inserção, atualização ou exclusão de dados. Ele também não dá suporte a algumas funções T-SQL e sintaxe que não são compatíveis com KQL. Ele foi criado para permitir que sistemas que não dão suporte à KQL usem o T-SQL para consultar os dados em um Banco de Dados KQL. Portanto, é recomendável usar o KQL como a linguagem de consulta primária para o Banco de Dados KQL, pois ele oferece mais recursos e desempenho do que o T-SQL. Você também pode usar algumas funções SQL compatíveis com KQL, como count, sum, avg, min, max, entre outras.

### Recuperar dados de uma tabela usando o Transact-SQL

1. Em seu conjunto de consultas, adicione e execute a seguinte consulta Transact-SQL: 

    ```sql  
    SELECT TOP 100 * from Automotive
    ```

1. Modifique a consulta da seguinte maneira para recuperar colunas específicas

    ```sql
    SELECT TOP 10 vendor_id, trip_distance
    FROM Automotive
    ```

1. Modifique a consulta para atribuir um alias que renomeie **trip_distance** para um nome mais amigável.

    ```sql
    SELECT TOP 10 vendor_id, trip_distance as [Trip Distance]
    from Automotive
    ```

### Resumir dados usando o Transact-SQL

1. Execute a seguinte consulta para encontrar a distância total percorrida:

    ```sql
    SELECT sum(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    ```

1. Modifique a consulta para agrupar a distância total por bairro de coleta:

    ```sql
    SELECT pickup_boroname AS Borough, Sum(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    GROUP BY pickup_boroname
    ```

1. Modifique ainda mais a consulta para usar uma instrução *CASE* para agrupar viagens com origem desconhecida em uma categoria ***Não Identificada*** para acompanhamento. 

    ```sql
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'Unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'Unidentified'
               ELSE pickup_boroname
             END;
    ```

### Classificar dados usando o Transact-SQL

1. Execute a consulta a seguir para ordenar os resultados agrupados por bairro
 
    ```sql
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
               ELSE pickup_boroname
             END
    ORDER BY Borough ASC;
    ```

### Filtrar dados usando Transact-SQL
    
1. Execute a consulta a seguir para filtrar os dados agrupados para que apenas as linhas com um bairro de "Manhattan" sejam incluídas nos resultados

    ```sql
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Automotive
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
               ELSE pickup_boroname
             END
    HAVING Borough = 'Manhattan'
    ORDER BY Borough ASC;
    ```

## Limpar os recursos

Neste exercício, você criou um eventhouse e consultou dados usando KQL e SQL.

Depois de explorar o banco de dados KQL, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do seu workspace.
2. Na barra de ferramentas, selecione **Configurações do espaço de trabalho**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
