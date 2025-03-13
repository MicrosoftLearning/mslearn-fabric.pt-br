---
lab:
  title: Consultar um data warehouse no Microsoft Fabric
  module: Query a data warehouse in Microsoft Fabric
---

# Consultar um data warehouse no Microsoft Fabric

No Microsoft Fabric, um data warehouse fornece um banco de dados relacional para análise em larga escala. O conjunto avançado de experiências incorporadas ao workspace do Microsoft Fabric permite que os clientes reduzam seu tempo para insights, tendo um modelo semântico facilmente consumível e sempre conectado integrado ao Power BI no modo DirectLake. 

Este laboratório levará aproximadamente **30** minutos para ser concluído.

> **Observação**: Você precisará uma [avaliação gratuita do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir esse exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Navegue até a [home page do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric) em `https://app.fabric.microsoft.com/home?experience=fabric` em um navegador e entre com suas credenciais do Fabric.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um data warehouse de exemplo

Agora que você tem um espaço de trabalho, é hora de criar um data warehouse.

1. Na barra de menus à esquerda, selecione **Criar**. Na página *Novo*, na seção *Data Warehouse*, selecione **Warehouse de amostra** e crie um novo data warehouse chamado **sample-dw**.

    >**Observação**: se a opção **Criar** não estiver fixada na barra lateral, você precisará selecionar a opção de reticências (**...**) primeiro.

    Após um minuto ou mais, um novo warehouse será criado e preenchido com dados de exemplo para um cenário de análise de corrida de táxi.

    ![Captura de tela de um novo warehouse.](./Images/sample-data-warehouse.png)

## Consultar o data warehouse

O editor de consulta SQL dá suporte para IntelliSense, preenchimento de código, realce de sintaxe, análise no lado do cliente e validação. Você pode executar instruções de DDL (Linguagem de Definição de Dados), DML (Linguagem de Manipulação de Dados) e DCL (Linguagem de Controle de Dados).

1. Na página do data warehouse **sample-dw**, na lista suspensa **Nova consulta SQL**, selecione **Nova consulta SQL**.

1. No novo painel de consulta em branco, insira o seguinte código Transact-SQL:

    ```sql
    SELECT 
    D.MonthName, 
    COUNT(*) AS TotalTrips, 
    SUM(T.TotalAmount) AS TotalRevenue 
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.MonthName;
    ```

1. Use o botão **&#9655; Executar** para executar o script SQL e exibir os resultados, que mostram o número total de viagens e a receita total por mês.

1. Insira o seguinte código Transact-SQL:

    ```sql
   SELECT 
    D.DayName, 
    AVG(T.TripDurationSeconds) AS AvgDuration, 
    AVG(T.TripDistanceMiles) AS AvgDistance 
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.DayName;
    ```

1. Execute a consulta modificada e exiba os resultados, que mostram a duração média da viagem e a distância por dia da semana.

1. Insira o seguinte código Transact-SQL:

    ```sql
    SELECT TOP 10 
    G.City, 
    COUNT(*) AS TotalTrips 
    FROM dbo.Trip AS T
    JOIN dbo.Geography AS G
        ON T.PickupGeographyID=G.GeographyID
    GROUP BY G.City
    ORDER BY TotalTrips DESC;
    
    SELECT TOP 10 
        G.City, 
        COUNT(*) AS TotalTrips 
    FROM dbo.Trip AS T
    JOIN dbo.Geography AS G
        ON T.DropoffGeographyID=G.GeographyID
    GROUP BY G.City
    ORDER BY TotalTrips DESC;
    ```

1. Execute a consulta modificada e exiba os resultados, que mostram os 10 locais de retirada e entrega mais populares.

1. Feche todas as guias de consulta.

## Verificar a consistência dos dados

Verificar a consistência de dados é importante para garantir que os dados sejam precisos e confiáveis para análise e tomada de decisões. Dados inconsistentes podem levar a análises incorretas e resultados enganosos. 

Vamos consultar o data warehouse para verificar se há consistência.

1. Na lista suspensa **Nova consulta SQL**, selecione **Nova consulta SQL**.

1. No novo painel de consulta em branco, insira o seguinte código Transact-SQL:

    ```sql
    -- Check for trips with unusually long duration
    SELECT COUNT(*) FROM dbo.Trip WHERE TripDurationSeconds > 86400; -- 24 hours
    ```

1. Execute a consulta modificada e exiba os resultados, que mostram detalhes de todas as viagens com duração extraordinariamente longa.

1. Na lista suspensa **Nova consulta SQL**, selecione **Nova consulta SQL** para adicionar uma segunda guia de consulta. Em seguida, na nova guia de consulta vazia, execute o seguinte código:

    ```sql
    -- Check for trips with negative trip duration
    SELECT COUNT(*) FROM dbo.Trip WHERE TripDurationSeconds < 0;
    ```

1. No novo painel de consulta em branco, insira e execute o seguinte código Transact-SQL:

    ```sql
    -- Remove trips with negative trip duration
    DELETE FROM dbo.Trip WHERE TripDurationSeconds < 0;
    ```

    > **Observação:** Há várias maneiras de lidar com dados inconsistentes. Em vez de removê-los, uma alternativa é substituí-los por um valor diferente, como a média ou a mediana.

1. Feche todas as guias de consulta.

## Salvar como exibição

Suponha que você precise filtrar determinadas viagens para um grupo de usuários que usarão os dados para gerar relatórios.

Vamos criar uma exibição com base na consulta que usamos anteriormente e adicionar um filtro a ela.

1. Na lista suspensa **Nova consulta SQL**, selecione **Nova consulta SQL**.

1. No novo painel de consulta em branco, insira novamente e execute o seguinte código Transact-SQL:

    ```sql
    SELECT 
        D.DayName, 
        AVG(T.TripDurationSeconds) AS AvgDuration, 
        AVG(T.TripDistanceMiles) AS AvgDistance 
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    GROUP BY D.DayName;
    ```

1. Modifique a consulta para adicionar `WHERE D.Month = 1`. Isso filtrará os dados para incluir apenas registros do mês de janeiro. A consulta final deve ter esta aparência:

    ```sql
    SELECT 
        D.DayName, 
        AVG(T.TripDurationSeconds) AS AvgDuration, 
        AVG(T.TripDistanceMiles) AS AvgDistance 
    FROM dbo.Trip AS T
    JOIN dbo.[Date] AS D
        ON T.[DateID]=D.[DateID]
    WHERE D.Month = 1
    GROUP BY D.DayName
    ```

1. Selecione o texto da instrução SELECT em sua consulta. Em seguida, ao lado do botão **&#9655; Executar**, selecione **Salvar como exibição**.

1. Crie uma nova exibição chamada **vw_JanTrip**.

1. No **Explorer**, navegue até **Esquemas >> dbo >> Exibições**. Observe a exibição *vw_JanTrip* que você acabou de criar.

1. Feche todas as guias de consulta.

> **Mais informações**: Confira [Consultar usando o editor de consultas SQL](https://learn.microsoft.com/fabric/data-warehouse/sql-query-editor) na documentação do Microsoft Fabric para obter mais informações sobre como consultar um data warehouse.

## Limpar os recursos

Neste exercício, você usou consultas para obter insights dos dados em um data warehouse do Microsoft Fabric.

Se você tiver terminado de explorar seu data warehouse, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
1. Clique em **Configurações do espaço de trabalho** e, na seção **Geral**, role para baixo e selecione **Remover este espaço de trabalho**.
1. Clique em **Excluir** para excluir o espaço de trabalho.
