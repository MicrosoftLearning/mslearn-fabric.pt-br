---
lab:
  title: "Monitorar um data warehouse\_no Microsoft Fabric"
  module: Monitor a data warehouse in Microsoft Fabric
---

# Monitorar um data warehouse no Microsoft Fabric

No Microsoft Fabric, um data warehouse fornece um banco de dados relacional para análise em larga escala. Os data warehouses no Microsoft Fabric incluem exibições de gerenciamento dinâmico que você pode usar para monitorar a atividade e as consultas.

Este laboratório levará aproximadamente **30** minutos para ser concluído.

> **Observação**: Você precisará uma [avaliação gratuita do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir esse exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [página inicial do Microsoft Fabric](https://app.fabric.microsoft.com), selecione **Data Warehouse do Synapse**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um data warehouse de exemplo

Agora que você tem um espaço de trabalho, é hora de criar um data warehouse.

1. No canto inferior esquerdo, certifique-se de que a experiência do **Data Warehouse** esteja selecionada.
1. Na **Página inicial**, selecione **Warehouse de amostra** e crie um novo data warehouse chamado **sample-dw**.

    Após um minuto ou mais, um novo warehouse será criado e preenchido com dados de exemplo para um cenário de análise de corrida de táxi.

    ![Captura de tela de um novo warehouse.](./Images/sample-data-warehouse.png)

## Explorar as exibições dinâmicas de gerenciamento

Os data warehouses do Microsoft Fabric incluem exibições de gerenciamento dinâmico (DMVs), que você pode usar para identificar a atividade atual na instância do data warehouse.

1. Na página **sample-dw** do data warehouse, na lista suspensa **Nova consulta SQL**, selecione **Nova consulta SQL**.
1. No novo painel de consulta em branco, insira o seguinte código Transact-SQL para consultar o **sys.dm_exec_connections** DMV:

    ```sql
   SELECT * FROM sys.dm_exec_connections;
    ```

1. Use o botão **&#9655; Executar** para executar o script SQL e exibir os resultados, que incluem os detalhes de todas as conexões com o data warehouse.
1. Modifique o código SQL para consultar o DMV **sys.dm_exec_sessions** DMV, da seguinte forma:

    ```sql
   SELECT * FROM sys.dm_exec_sessions;
    ```

1. Execute a consulta modificada e visualize os resultados, que mostram detalhes de todas as sessões autenticadas.
1. Modifique o código SQL para consultar o DMV **sys.dm_exec_requests** DMV, da seguinte forma:

    ```sql
   SELECT * FROM sys.dm_exec_requests;
    ```

1. Execute a consulta modificada e exiba os resultados, que mostram os detalhes de todas as solicitações que estão sendo executadas no data warehouse.
1. Modifique o código SQL para unir as DMVs e retornar informações sobre as solicitações em execução no momento no mesmo banco de dados, da seguinte forma:

    ```sql
   SELECT connections.connection_id,
    sessions.session_id, sessions.login_name, sessions.login_time,
    requests.command, requests.start_time, requests.total_elapsed_time
   FROM sys.dm_exec_connections AS connections
   INNER JOIN sys.dm_exec_sessions AS sessions
       ON connections.session_id=sessions.session_id
   INNER JOIN sys.dm_exec_requests AS requests
       ON requests.session_id = sessions.session_id
   WHERE requests.status = 'running'
       AND requests.database_id = DB_ID()
   ORDER BY requests.total_elapsed_time DESC;
    ```

1. Execute a consulta modificada e exiba os resultados, que mostram os detalhes de todas as consultas em execução no banco de dados (incluindo esta).
1. Na lista suspensa **Nova consulta SQL**, selecione **Nova consulta SQL** para adicionar uma segunda guia de consulta. Em seguida, na nova guia de consulta vazia, execute o seguinte código:

    ```sql
   WHILE 1 = 1
       SELECT * FROM Trip;
    ```

1. Deixe a consulta em execução e retorne à guia que contém o código para consultar as DMVs e execute-a novamente. Dessa vez, os resultados devem incluir a segunda consulta que está sendo executada na outra guia. Observe o tempo decorrido para essa consulta.
1. Aguarde alguns segundos e execute novamente o código para consultar as DMVs novamente. O tempo decorrido para a consulta na outra guia deveria ter aumentado.
1. Retorne à segunda guia de consulta, na qual a consulta ainda está em execução, e selecione **X Cancelar** para cancelá-la.
1. De volta à guia com o código para consultar as DMVs, execute novamente a consulta para confirmar que a segunda consulta não está mais em execução.
1. Fechar todas as guias de consulta.

> **Informações adicionais**: Consulte [Monitorar conexões, sessões e solicitações utilizando DMVs](https://learn.microsoft.com/fabric/data-warehouse/monitor-using-dmv) na documentação do Microsoft Fabric para obter mais informações sobre o uso de DMVs.

## Explorar insights de consulta

Os data warehouses do Microsoft Fabric fornecem *insight de consulta*, um conjunto especial de exibições que fornecem detalhes sobre as consultas que estão sendo executadas em seu data warehouse.

1. Na página **sample-dw** do data warehouse, na lista suspensa **Nova consulta SQL**, selecione **Nova consulta SQL**.
1. No novo painel de consulta em branco, insira o seguinte código Transact-SQL para consultar a exibição **exec_requests_history**:

    ```sql
   SELECT * FROM queryinsights.exec_requests_history;
    ```

1. Use o botão **&#9655; Executar** para executar o script SQL e exibir os resultados, que incluem detalhes de consultas executadas anteriormente.
1. Modifique o código SQL para consultar a exibição **frequently_run_queries**, da seguinte forma:

    ```sql
   SELECT * FROM queryinsights.frequently_run_queries;
    ```

1. Execute a consulta modificada e exiba os resultados, que mostram detalhes de consultas executadas com frequência.
1. Modifique o código SQL para consultar a exibição **long_running_queries**, da seguinte forma:

    ```sql
   SELECT * FROM queryinsights.long_running_queries;
    ```

1. Execute a consulta modificada e exiba os resultados, que mostram os detalhes de todas as consultas e suas durações.

> **Informações adicionais**: Consulte [Insights de consulta no data warehouse do Fabric](https://learn.microsoft.com/fabric/data-warehouse/query-insights) na documentação do Microsoft Fabric para obter mais informações sobre o uso de insights de consulta.


## Limpar os recursos

Neste exercício, você usou exibições de gerenciamento dinâmico e insights de consultas para monitorar a atividade em um data warehouse do Microsoft Fabric.

Se você tiver terminado de explorar seu data warehouse, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Outros**, selecione **Remover este workspace**.
