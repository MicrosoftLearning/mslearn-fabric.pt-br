---
lab:
  title: Consultar dados no Banco de Dados KQL
  module: Query data from a Kusto Query database in Microsoft Fabric
---

# Introdução à consulta de um banco de dados Kusto no Microsoft Fabric

Um Conjunto de Consultas KQL é uma ferramenta que permite executar consultas, além de modificar e exibir resultados de consultas de um banco de dados KQL. Você pode vincular cada guia no Conjunto de Consultas KQL a um banco de dados KQL diferente, além de salvar suas consultas para uso futuro ou compartilhá-las com outras pessoas para fins de análise de dados. Você também pode alternar o banco de dados KQL para qualquer guia, de modo a poder comparar os resultados de consultas de diferentes fontes de dados.

Para criar consultas, o Conjunto de Consultas KQL usa a Linguagem de Consulta Kusto, que é compatível com diversas funções SQL. Para obter mais informações sobre a [Linguagem de Consulta Kusto (KQL) ](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext).

Este laboratório leva cerca de **25** minutos para ser concluído.

## Cenário

Neste cenário, você é um analista encarregado de consultar um conjunto de dados de exemplo de corridas de táxi de NYC de métricas brutas no qual você efetua pull de estatísticas resumidas (criação de perfil) dos dados do ambiente do Fabric. Você usa a KQL para consultar esses dados e coletar informações para obter informações informativas sobre os dados.

> **Observação**: você precisa de uma conta Microsoft de *estudante* ou *corporativa* para concluir este exercício. Caso não tenha uma, [inscreva-se em uma avaliação do Microsoft Office 365 E3 ou superior](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [página inicial do Microsoft Fabric](https://app.fabric.microsoft.com), selecione **Análise em Tempo Real**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

Nesse laboratório, você usa a Análise de Dados em Tempo Real (RTA) do Fabric para criar um banco de dados KQL a partir de uma amostra de fluxo de eventos. A Análise em Tempo Real fornece convenientemente um conjunto de dados de amostra que você pode utilizar para explorar as funcionalidades do RTA. Use esses dados de exemplo para criar consultas KQL/SQL e conjuntos de consultas que analisam dados em tempo real e permitem outros usos em processos downstream.

## Criar um banco de dados KQL

1. Na **Análise em Tempo Real**, selecione a caixa **Banco de Dados KQL**.

   ![Imagem de escolher Banco de Dados KQL](./Images/select-kqldatabase.png)

1. Você é solicitado a **Nomear** o banco de dados KQL

   ![Imagem do nome do Banco de Dados KQL](./Images/name-kqldatabase.png)

1. Dê ao banco de dados KQL um nome do qual você se lembrará, como **MyStockData**, e pressione **Criar**.

1. No painel **Detalhes do banco de dados**, selecione o ícone de lápis para ativar a disponibilidade no OneLake.

   ![Imagem de habilitar onelake](./Images/enable-onelake-availability.png)

   Em seguida, use o controle deslizante para habilitar a disponibilidade.

   ![Imagem da seleção do controle deslizante no Data Lake](./Images/data-availability-data-lake.png)
   
1. Selecione a caixa de **dados de exemplo** nas opções de ***Iniciar obtendo dados***.

   ![Imagem de opções de seleção com dados de exemplo realçados](./Images/load-sample-data.png)

   Em seguida, escolha a caixa **Análise de operações automotivas** nas opções de dados de exemplo.

   ![Imagem da escolha de dados de análise para laboratório](./Images/create-sample-data.png)

1. Depois que os dados terminarem de ser carregados, podemos verificar se o Banco de Dados KQL está preenchido.

   ![Dados sendo carregados no Banco de Dados KQL](./Images/choose-automotive-operations-analytics.png)

1. Depois que os dados forem carregados, verifique se eles foram carregados no banco de dados KQL. Você pode realizar essa operação selecionando as reticências à direita da tabela, navegando até a **Tabela de consulta** e selecionando **Mostrar quaisquer 100 registros**.

    ![Imagem da seleção dos 100 principais arquivos da tabela RawServerMetrics](./Images/rawservermetrics-top-100.png)

   > **OBSERVAÇÃO**: na primeira vez que você executar isso, pode levar vários segundos para alocar recursos de computação.

    ![Imagem dos 100 registros dos dados](./Images/explore-with-kql-take-100.png)

## Introdução à Linguagem de Consulta Kusto (KQL) e sua sintaxe

A Linguagem de Consulta Kusto (KQL) é uma linguagem de consulta usada para analisar dados no Microsoft Azure Data Explorer, que faz parte do Azure Fabric. A KQL foi projetada para ser simples e intuitiva, facilitando o aprendizado e o uso para iniciantes. Ao mesmo tempo, ela também é altamente flexível e personalizável, permitindo que usuários avançados executem consultas e análises complexas.

A KQL é baseada em uma sintaxe semelhante ao SQL, mas com algumas diferenças importantes. Por exemplo, a KQL usa um operador de pipe (|) em vez de um ponto e vírgula (;) para separar comandos e usa um conjunto diferente de funções e operadores para filtrar e manipular dados.

Um dos principais recursos da KQL é sua capacidade de lidar com grandes volumes de dados de forma rápida e eficiente. Essa funcionalidade o torna ideal para analisar logs, dados de telemetria e outros tipos de Big Data. A KQL também dá suporte a uma ampla variedade de fontes de dados, incluindo dados estruturados e não estruturados, tornando-os uma ferramenta versátil para análise de dados.

No contexto do Microsoft Fabric, a KQL pode ser usada para consultar e analisar dados de várias fontes, como logs de aplicativo, métricas de desempenho e eventos do sistema. Isso pode ajudá-lo a obter insights sobre a integridade e o desempenho de seus aplicativos e infraestrutura e identificar problemas e oportunidades de otimização.

De modo geral, a KQL é uma linguagem de consulta poderosa e flexível que pode ajudar você a obter insights sobre seus dados de forma rápida e fácil, esteja você trabalhando com o Microsoft Fabric ou outras fontes de dados. Com sua sintaxe intuitiva e funcionalidades poderosas, a KQL definitivamente vale a pena explorar ainda mais.

Nesse módulo, nos concentramos nos conceitos básicos de consultas a um Banco de Dados KQL usando primeiro KQL e, em seguida, T-SQL. Vamos nos concentrar nos elementos básicos da sintaxe T-SQL que são usados para consultas, incluindo:

Consultas **SELECT**, que são usadas para recuperar dados de uma ou mais tabelas. Por exemplo, você pode usar uma consulta SELECT para obter os nomes e salários de todos os funcionários em uma empresa.

Consultas **WHERE**, que são usadas para filtrar os dados com base em determinadas condições. Por exemplo, você pode usar uma consulta WHERE para obter os nomes dos funcionários que trabalham em um departamento específico ou que têm um salário acima de um determinado valor.

Consultas **GROUP BY**, que são usadas para agrupar os dados por uma ou mais colunas e executar funções de agregação nelas. Por exemplo, você pode usar uma consulta GROUP BY para obter o salário médio dos funcionários por departamento ou por país.

Consultas **ORDER BY**, que são usadas para classificar os dados por uma ou mais colunas em ordem crescente ou decrescente. Por exemplo, você pode usar uma consulta ORDER BY para obter os nomes dos funcionários classificados por seus salários ou por seus sobrenomes.

   > **AVISO:** não é possível criar relatórios do Power BI a partir de conjuntos de consultas com **T-SQL** porque o Power BI não dá suporte ao T-SQL como uma fonte de dados. O **Power BI só dá suporte a KQL como a linguagem de consulta nativa para conjuntos de consultas**. Se você quiser usar o T-SQL para consultar seus dados no Microsoft Fabric, precisará usar o ponto de extremidade T-SQL que emula o Microsoft SQL Server e permite executar consultas T-SQL em seus dados. No entanto, o ponto de extremidade T-SQL tem algumas limitações e diferenças em relação ao SQL Server nativo e não dá suporte à criação ou publicação de relatórios no Power BI.

> **OBSERVAÇÃO**: além da abordagem para efetuar pull de uma janela de consulta conforme mostrado anteriormente, você sempre pode pressionar o botão **Explorar seus dados** no painel principal do Banco de Dados KQL..

   ![Imagem do botão Explorar seus dados](./Images/explore-your-data.png)

## Dados ```SELECT``` de nosso conjunto de dados de exemplo usando KQL

1. Nesta consulta, extraímos 100 registros da tabela Viagens. Usamos a palavra-chave ```take``` para pedir ao mecanismo que retorne 100 registros.

    ```kusto
    
    Trips
    | take 100
    ```

    > **OBSERVAÇÃO:** o caractere pipe ```|``` é usado para duas finalidades na KQL, incluindo a utilização de operadores de consulta separados em uma instrução de expressão tabular. Ele também é usado como um operador OR lógico dentro de colchetes ou colchetes redondos para indicar que você pode especificar um dos itens separados pelo caractere de pipe.

1. Podemos ser mais precisos adicionando atributos específicos que gostaríamos de consultar usando a palavra-chave ```project``` e, em seguida, usando a palavra-chave ```take``` para informar ao mecanismo quantos registros retornar.

    > **OBSERVAÇÃO:** o uso de ```//``` denota comentários usados na ferramenta de consulta ***Explorar seus dados*** do Microsoft Fabric.

    ```kusto
    
    // Use 'project' and 'take' to view a sample number of records in the table and check the data.
    Trips 
    | project vendor_id, trip_distance
    | take 10
    ```

1. Outra prática comum na análise é renomear colunas em nosso conjunto de consultas para torná-las mais amigáveis. Isso pode ser feito usando o novo nome de coluna seguido pelo sinal de igual e a coluna que desejamos renomear.

    ```kusto
    
    Trips 
    | project vendor_id, ["Trip Distance"] = trip_distance
    | take 10
    ```

1. Talvez também queiramos resumir as viagens para ver quantas milhas foram percorridas:

    ```kusto
    
    Trips
    | summarize ["Total Trip Distance"] = sum(trip_distance)
    ```

## Dados ```GROUP BY``` de nosso conjunto de dados de exemplo usando KQL

1. Em seguida, talvez queiramos ***agrupar por*** local de retirada, o que fazemos com o operador ```summarize```. Também podemos usar o operador ```project```, que nos permite selecionar e renomear as colunas que você deseja incluir na saída. Nesse caso, nos agrupamos por bairro dentro do sistema de táxis de NY para fornecer aos nossos usuários a distância total percorrida de cada bairro.

```kusto

Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = pickup_boroname, ["Total Trip Distance"]
```

1. Nesse caso, temos um valor em branco, o que nunca é bom para análise, e podemos usar a função ```case``` junto com as funções ```isempty``` e ```isnull``` para categorizar em uma categoria ***Não identificada*** para acompanhamento.

```kusto

Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
```

## Dados ```ORDER BY``` de nosso conjunto de dados de exemplo usando KQL

Para dar mais sentido aos nossos dados, normalmente os ordenamos por uma coluna, e esse processo é feito na KQL com um operador ```sort by``` ou ```order by``` e eles agem da mesma maneira.
 
```kusto

// using the sort by operators
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc 

// order by operator has the same result as sort by
Trips
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc 
```

## Cláusula ```WHERE``` para filtrar dados em nossa consulta KQL de exemplo

Ao contrário do SQL, nossa cláusula WHERE é imediatamente chamada em nossa consulta KQL. Ainda podemos usar os operadores lógicos ```and``` e ```or``` dentro da cláusula where, e é avaliado como verdadeiro ou falso em relação à tabela, e pode ser simples ou uma expressão complexa que pode envolver várias colunas, operadores e funções.

```kusto

// let's filter our dataset immediately from the source by applying a filter directly after the table.
Trips
| where pickup_boroname == "Manhattan"
| summarize ["Total Trip Distance"] = sum(trip_distance) by pickup_boroname
| project Borough = case(isempty(pickup_boroname) or isnull(pickup_boroname), "Unidentified", pickup_boroname), ["Total Trip Distance"]
| sort by Borough asc

```

## Usar o T-SQL para consultar informações de resumo

O Banco de Dados KQL não dá suporte ao T-SQL nativamente, mas fornece um ponto de extremidade T-SQL que emula o Microsoft SQL Server e permite que você execute consultas T-SQL em seus dados. No entanto, o ponto de extremidade T-SQL tem algumas limitações e diferenças em relação ao SQL Server nativo. Por exemplo, ele não dá suporte à criação, alteração ou remoção de tabelas ou à inserção, atualização ou exclusão de dados. Ele também não dá suporte a algumas funções T-SQL e sintaxe que não são compatíveis com KQL. Ele foi criado para permitir que sistemas que não dão suporte à KQL usem o T-SQL para consultar os dados em um Banco de Dados KQL. Portanto, é recomendável usar o KQL como a linguagem de consulta primária para o Banco de Dados KQL, pois ele oferece mais recursos e desempenho do que o T-SQL. Você também pode usar algumas funções SQL compatíveis com KQL, como count, sum, avg, min, max, etc. 

## Dados ```SELECT``` de nosso conjunto de dados de exemplo usando T-SQL

1. Nesta consulta, efetuamos pull dos primeiros 100 registros da tabela **Viagens** usando a cláusula ```TOP```. 

    ```sql
    // We can use the TOP clause to limit the number of records returned
    
    SELECT TOP 100 * from Trips
    ```

1. Se você usar o ```//```, que é um comentário na ferramenta ***Explorar seus dados** dentro do banco de dados KQL, não poderá realçá-lo ao executar consultas T-SQL; em vez disso, você deve usar a notação de comentários SQL padrão ```--```. esse hífen duplo também dirá ao mecanismo KQL para esperar T-SQL no Azure Data Explorer.

    ```sql
    -- instead of using the 'project' and 'take' keywords we simply use a standard SQL Query
    SELECT TOP 10 vendor_id, trip_distance
    FROM Trips
    ```

1. Novamente, você pode ver que os recursos padrão do T-SQL funcionam bem com a consulta em que renomeamos trip_distance para um nome mais amigável.

    ```sql
    
    -- No need to use the 'project' or 'take' operators as standard T-SQL Works
    SELECT TOP 10 vendor_id, trip_distance as [Trip Distance]
    from Trips
    ```

1. Talvez também queiramos resumir as viagens para ver quantas milhas foram percorridas:

    ```sql
    Select sum(trip_distance) as [Total Trip Distance]
    from Trips
    ```
     >**OBSERVAÇÃO:** o uso de aspas não é necessário no T-SQL em comparação com a consulta KQL. Observe também que os comandos `summarize` e `sort by` não estão disponíveis no T-SQL.

## Dados ```GROUP BY``` de nosso conjunto de dados de exemplo usando T-SQL

1. Em seguida, talvez queiramos ***agrupar por*** local de retirada, o que fazemos com o operador ```GROUP BY```. Também podemos usar o operador ```AS```, que nos permite selecionar e renomear as colunas que você deseja incluir na saída. Nesse caso, nos agrupamos por bairro dentro do sistema de táxis de NY para fornecer aos nossos usuários a distância total percorrida de cada bairro.

    ```sql
    SELECT pickup_boroname AS Borough, Sum(trip_distance) AS [Total Trip Distance]
    FROM Trips
    GROUP BY pickup_boroname
    ```

1. Nesse caso, temos um valor em branco, o que nunca é bom para análise, e podemos usar a função ```CASE``` junto com a função ```IS NULL``` e o valor vazio ```''``` para categorizar em uma categoria ***não identificada*** para acompanhamento. 

    ```sql
    
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'Unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Trips
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'Unidentified'
               ELSE pickup_boroname
             END;
    ```

## Dados ```ORDER BY``` de nosso conjunto de dados de exemplo usando T-SQL

1. Para dar mais sentido aos nossos dados, normalmente os ordenamos por uma coluna, e esse processo é feito no T-SQL com um operador ```ORDER BY```. Não existe um operador ***CLASSIFICAR POR*** em T-SQL
 
    ```sql
    -- Group by pickup_boroname and calculate the summary statistics of trip_distance
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Trips
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
               ELSE pickup_boroname
             END
    -- Add an ORDER BY clause to sort by Borough in ascending order
    ORDER BY Borough ASC;
    ```
    ## Cláusula ```WHERE``` para filtrar dados em nossa consulta T-SQL de exemplo
    
1. Ao contrário do KQL, nossa cláusula ```WHERE``` iria para o final da Instrução T-SQL; no entanto, neste caso, temos uma cláusula ```GROUP BY```, que exige que usemos a instrução ```HAVING```, e usamos o novo nome da coluna, neste caso **Burgo**, como o nome da coluna para filtrar.

    ```sql
    -- Group by pickup_boroname and calculate the summary statistics of trip_distance
    SELECT CASE
             WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
             ELSE pickup_boroname
           END AS Borough,
           SUM(trip_distance) AS [Total Trip Distance]
    FROM Trips
    GROUP BY CASE
               WHEN pickup_boroname IS NULL OR pickup_boroname = '' THEN 'unidentified'
               ELSE pickup_boroname
             END
    -- Add a having clause due to the GROUP BY statement
    HAVING Borough = 'Manhattan'
    -- Add an ORDER BY clause to sort by Borough in ascending order
    ORDER BY Borough ASC;
    
    ```

## Limpar os recursos

Neste exercício, você criou um banco de dados KQL e configurou um conjunto de dados de exemplo para consulta. Depois disso, você consultou os dados usando o KQL e o SQL. Depois de explorar o banco de dados KQL, exclua o workspace criado para este exercício.
1. Na barra à esquerda, selecione o **ícone** do seu workspace.
2. No ... menu da barra de ferramentas, selecione **Configurações do Espaço de Trabalho**.
3. Na seção **Outros**, selecione **Remover este workspace**.
