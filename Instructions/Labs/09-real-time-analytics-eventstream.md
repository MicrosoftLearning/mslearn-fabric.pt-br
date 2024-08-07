---
lab:
  title: Introdução ao Eventstream no Microsoft Fabric
  module: Get started with Eventstream in Microsoft Fabric
---
# Comece a usar o Eventstream na Inteligência em Tempo Real

O Eventstream é um recurso do Microsoft Fabric que captura, transforma e roteia eventos em tempo real para diversos destinos com uma experiência sem código. Você pode adicionar fontes de dados de evento, destinos de roteamento e o processador de eventos ao fluxo de eventos quando a transformação for necessária. O EventStore do Microsoft Fabric é uma opção de monitoramento que mantém os eventos do cluster e fornece uma maneira de entender o estado do cluster ou da carga de trabalho em um determinado momento. O serviço EventStore pode ser consultado para eventos que estão disponíveis para cada entidade e tipo de entidade em seu cluster. Isso significa que você pode consultar eventos em diferentes níveis, como clusters, nós, aplicativos, serviços, partições e réplicas de partição. O serviço EventStore também tem a capacidade de correlacionar eventos em seu cluster. Ao observar eventos que foram gravados ao mesmo tempo de diferentes entidades que podem ter impactado umas às outras, o serviço EventStore pode vincular esses eventos para ajudar a identificar as causas das atividades em seu cluster. Outra opção de monitoramento e diagnóstico de clusters do Microsoft Fabric é agregar e coletar eventos usando o fluxo de eventos.

Este laboratório leva cerca de **30** minutos para ser concluído.

> **Observação**: Você precisa de uma [avaliação do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir esse exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Entre no [Microsoft Fabric](https://app.fabric.microsoft.com) em `https://app.fabric.microsoft.com` e selecione **Power BI**.
2. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
3. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
4. Quando o novo workspace for aberto, ele deverá estar vazio, conforme mostrado aqui:

   ![Captura de tela de um workspace vazio no Power BI.](./Images/new-workspace.png)
5. No canto inferior esquerdo do portal do Power BI, selecione o ícone do **Power BI** e alterne para a experiência de **Inteligência em Tempo Real**.

## Cenário

Com os fluxos de eventos do Fabric, é possível gerenciar facilmente dados de eventos em um só lugar. Você pode coletar, transformar e enviar dados de eventos em tempo real para destinos diferentes no formato desejado. Também é possível conectar fluxos de eventos com Hubs de Eventos do Azure, bancos de dados KQL e Lakehouses com facilidade.

Este laboratório se baseia em dados de streaming de exemplo chamados Dados do Mercado de Ações. Os dados de exemplo de Mercado de Ações são um conjunto de dados de uma bolsa de valores com uma coluna de esquema predefinida, como hora, símbolo, preço, volume, entre outros. Você usará esses dados de exemplo para simular eventos em tempo real de preços de ações e analisá-los com vários destinos, como o banco de dados KQL.

Use os recursos de streaming e consulta da Inteligência em Tempo Real para responder às principais perguntas sobre as estatísticas de estoque. Nesse cenário, vamos aproveitar ao máximo o assistente em vez de criar manualmente alguns componentes de maneira independente, como o Banco de Dados KQL.

Neste tutorial, você aprenderá como:

- Criar um Eventhouse
- Criar um banco de dados KQL
- Habilitar a cópia de dados para o OneLake
- Criar um fluxo de eventos
- Transmitir dados de um fluxo de eventos para o banco de dados KQL
- Explorar dados com o KQL e o SQL\

## Criar um Eventhouse de Inteligência em Tempo Real

1. Selecione a opção inteligência em Tempo Real no Microsoft Fabric.
1. Selecione Eventhouse na barra de menus e dê um nome ao seu eventhouse.
    
    ![Imagem da criação de um Eventhouse](./Images/create-eventhouse.png)

## Criar um banco de dados KQL

1. No Painel **Eventhouse de Inteligência em Tempo Real**, selecione a caixa **Banco de Dados KQL +**.
1. Você terá a opção de nomear seu banco de dados e selecionar um **Novo banco de dados (padrão)** ou criar um **Novo banco de dados de atalho (seguidor)**.
1. Selecione **Criar**.

     >**Observação:** o recurso de banco de dados seguidor permite anexar um banco de dados localizado em outro cluster ao cluster do Azure Data Explorer. O banco de dados seguidor é anexado no modo somente leitura, possibilitando exibir os dados e executar consultas sobre os dados que foram ingeridos no banco de dados líder. O banco de dados seguidor sincroniza as alterações nos bancos de dados líderes. Devido à sincronização, há um atraso de dados de alguns segundos a alguns minutos na disponibilidade dos dados. O tamanho do atraso depende do tamanho geral dos metadados do banco de dados líder. Os bancos de dados líder e seguidor usam a mesma conta de armazenamento para buscar os dados. O armazenamento pertence ao banco de dados líder. O banco de dados seguidor visualiza os dados sem precisar ingeri-los. Como o banco de dados anexado é um banco de dados somente leitura, os dados, as tabelas e as políticas do banco de dados não podem ser modificados, exceto a política de cache, as entidades de segurança e as permissões.

   ![Imagem de escolher kqldatabase](./Images/create-kql-database-eventhouse.png)

4. Você precisará **Nomear** o banco de dados KQL

   ![Imagem do nome kqldatabase](./Images/name-kqldatabase.png)

5. Dê ao banco de dados KQL um nome do qual você se lembrará, como **Eventhouse-HR**, e pressione **Criar**.

6. No painel **Detalhes do banco de dados**, selecione o ícone de lápis para ativar a disponibilidade no OneLake.

   [ ![Imagem de habilitar onlake](./Images/enable-onelake-availability.png) ](./Images/enable-onelake-availability-large.png)

7. Alterne o botão para **Ativo** e selecione **Concluído**.

   ![Imagem da alternância habilitar onelake](./Images/enable-onelake-toggle.png)

## Criar um fluxo de eventos

1. Na barra de menus, selecione **Inteligência em Tempo Real** (o ícone é semelhante ao ![logotipo de inteligência em tempo real](./Images/rta_logo.png))
2. Em **Novo**, selecione **EventStream**.

   ![Imagem de escolher fluxo de eventos](./Images/select-eventstream.png)

3. Será solicitado a você **Nomear** o fluxo de eventos. Dê ao EventStream um nome que você se lembre, como **MyStockES**, selecione a opção **Recursos Avançados (versão prévia)** e escolha **Criar** .

   ![Imagem do nome eventstream](./Images/name-eventstream.png)

     >**Observação:** a criação do novo fluxo de eventos no workspace será concluída em apenas alguns instantes. Depois de estabelecido, você será redirecionado automaticamente para o editor primário, pronto para começar a integrar fontes ao fluxo de eventos.

## Estabelecer uma origem para um eventstream

1. Na tela do Fluxo de eventos, selecione **Nova fonte** na lista suspensa e selecione **Dados de Exemplo**.

    [ ![imagem do uso de dados do Sampel](./Images/eventstream-select-sample-data.png)](./Images/eventstream-select-sample-data-large.png#lightbox)

2.  Em **Adicionar fonte**, dê um nome à sua fonte e selecione **Bicicletas (compatível com Reflex)**
3.  Selecione o botão **Adicionar**.

    ![Selecionar e nomear o fluxo de eventos de dados de exemplo](./Images/eventstream-sample-data.png)

4. Depois de selecionar o botão **Adicionar**, o fluxo será mapeado e você será redirecionado automaticamente para a tela **fluxo de eventos**.

   [ ![Examinar a tela da barra de eventos](./Images/real-time-intelligence-eventstream-sourced.png) ](./Images/real-time-intelligence-eventstream-sourced-large.png#lightbox)
 
 > **Observação:** depois de criar a fonte de dados de exemplo, ela será adicionada ao Eventstream na tela do modo de edição. Para implementar essa amostra de dados recém-adicionada, selecione **Publicar**.

## Adicionar eventos de transformação ou adicionar atividade de destino

1. Após a publicação, você pode selecionar **Transformar eventos ou adicionar destino** e, em seguida, selecionar **Banco de Dados KQL** como uma opção.

   [ ![definir o Banco de Dados KQL como destino de fluxo de eventos](./Images/select-kql-destination.png) ](./Images/select-kql-destination-large.png)


2. Você verá um novo painel lateral aberto que oferece várias opções. Insira os detalhes necessários do Banco de Dados KQL.

   [ ![Fluxo de eventos do Banco de Dados KQL com modos de ingestão](./Images/kql-database-event-processing-before-ingestion.png) ](./Images/kql-database-event-processing-before-ingestion.png)

    - **Modo de ingestão de dados:** Há duas maneiras de ingerir dados no Banco de Dados KQL:
        - ***Ingestão direta***: ingerir dados diretamente em uma tabela KQL sem nenhuma transformação.
        - ***Processamento de eventos antes da ingestão***: transforme os dados com o Processador de Eventos antes de enviar para uma tabela KQL.      
        
        > **Aviso**: Você **NÃO PODE** editar o modo de ingestão depois que o destino do banco de dados KQL é adicionado ao fluxo de eventos.     

   - **Nome do destino**: insira um nome para esse destino do Eventstream, como "kql-dest".
   - **Workspace**: o workspace onde o banco de dados KQL está localizado.
   - **Banco de Dados KQL**: nome do Banco de Dados KQL.
   - **Tabela de destino**: nome da tabela KQL. Você também pode inserir um nome para criar uma nova tabela, por exemplo, "contagem de bicicletas".
   - **Formato de dados de entrada:** Escolha JSON como o formato de dados da tabela KQL.


3. Selecione **Salvar**. 
4. Selecione **Publicar**.

## Transformar os eventos

1. Na tela **fluxo de eventos**, selecione **Transformar eventos**.

    ![Adicionar agrupar por ao evento de transformação. ](./Images/eventstream-add-aggregates.png)

    R. Selecione **Agrupar por**.

    B. Selecione **Editar** ilustrado pelo ícone de ***lápis***.

    C. Depois de criar o evento de transformação **Agrupar por**, você precisará conectá-lo do **Fluxo de eventos** a **Agrupar por**. Você pode fazer isso sem o uso do código clicando no ponto no lado direito de **fluxo de eventos** e arrastando-o para o ponto no lado esquerdo da nova caixa **Agrupar por**. 

    ![Adicionar link entre Eventstream e agrupar por. ](./Images/group-by-drag-connectors.png)    

2. Preencha as propriedades da seção de configurações **Agrupar por**:
    - **Nome da operação:** insira um nome para esse evento de transformação
    - **Tipo de agregação:** soma
    - **Campo:** No_Bikes
    - **Nome:** SUM_No_Bikes
    - **Agrupar agregações por**: rua
      
3. Selecione **Adicionar** e, em seguida, **Salvar**.

4. Da mesma maneira, você pode passar o mouse sobre a seta entre o **fluxo de eventos** e o ***kql_dest*** e selecionar a ***lixeira** Em seguida, você pode conectar o evento **Agrupar por** ao **kql-dest**.

   [ ![Remover um link entre dois eventos](./Images/delete-flow-arrows.png) ](./Images/delete-flow-arrows-large.png)

    > **Observação:** sempre que você adicionar ou remover conectores, precisará configurar novamente os objetos de destino.

5. Selecione o lápis em **kql-dest** e crie uma nova tabela de destino chamada **Bike_sum** que receberá a saída do evento **Agrupar por**.

## Consultas KQL

O KQL (Linguagem de Consulta Kusto) é uma solicitação somente leitura para processar dados e retornar resultados. A solicitação é declarada em texto sem formatação que é fácil de ler, criar e automatizar. As consultas sempre são executadas no contexto de uma tabela ou de um banco de dados específico. No mínimo, uma consulta consiste em uma referência de dados de origem e em um ou mais operadores de consulta aplicados em sequência, indicados visualmente pelo uso de um caractere de barra vertical (|) para delimitar os operadores. Para saber mais sobre a Linguagem de Consulta Kusto, confira [Visão geral do KQL (Linguagem de Consulta Kusto)](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext)

> **Observação**: o editor KQL é fornecido com realce de sintaxe e Inellisense, o que permite conhecer rapidamente a KQL (Linguagem de Consulta Kusto).

1. Navegue até o banco de dados KQL recém-criado e hidratado:

    R.  Selecione **kql-dest** 

    B. Selecione o hiperlink **Abrir item** localizado na linha **Item relacionado**

   [ ![Remover um link entre dois eventos](./Images/navigate-to-data.png) ](./Images/navigate-to-data-large.png)

1. Na árvore Dados, selecione o menu Mais […] na tabela ***Bike_sum***. Em seguida, selecione Tabela de consulta > Mostrar quaisquer 100 registros.

   [ ![Remover um link entre dois eventos](./Images/kql-query-sample.png) ](./Images/kql-query-sample-large.png)

3. A consulta de exemplo será aberta no painel **Explorar seus dados** com o contexto de tabela já preenchido. Essa primeira consulta utiliza o operador `take` para retornar um número de registros de exemplo e é útil para dar uma primeira olhada na estrutura de dados e nos valores possíveis. Os exemplos de consultas preenchidas automaticamente são executados automaticamente. Você poderá ver os resultados da consulta no painel de resultados.

   ![Imagem dos resultados da consulta KQL](./Images/kql-query-results.png)

4. Retorne à árvore de dados para selecionar a próxima consulta **Resumir ingestão por hora**, que usa o operador `summarize` para contar o número de registros ingeridos em um intervalo determinado.

   ![Imagem dos resultados da consulta KQL](./Images/kql-query-results-15min-intervals.png)

> **Observação**: Você poderá ver um aviso de que excedeu os limites de consulta. Esse comportamento vai variar conforme o volume de dados transmitidos para o banco de dados.

Você pode continuar navegando com as funções de consulta internas para se familiarizar com seus dados.

## Consultar com o Copilot

O editor de consultas dá suporte ao uso do T-SQL, além do KQL (Linguagem de Consulta Kusto) de consulta primária. O T-SQL pode ser útil para ferramentas que não podem usar o KQL. Para obter mais informações, confira [Consultar dados usando o T-SQL](https://learn.microsoft.com/en-us/azure/data-explorer/t-sql)

1. De volta à árvore Dados, selecione o **menu Mais** […] na tabela MyStockData. Selecione **Consultar tabela > SQL > Mostrar quaisquer 100 registros**.

   [ ![Imagem do exemplo de consulta SQL](./Images/sql-query-sample.png) ](./Images/sql-query-sample-large.png)

2. Coloque o cursor em algum lugar dentro da consulta e selecione **Executar** ou pressione **SHIFT + ENTER**.

   ![Imagem dos resultados da consulta SQL](./Images/sql-query-results.png)

Você pode continuar navegando usando as funções de build e se familiarizar com os dados usando o SQL ou o KQL. 

## Recursos com o conjunto de consultas

Os conjuntos de consultas em bancos de dados KQL (Linguagem de Consulta Kusto) são usados para diversas finalidades, principalmente para executar consultas, exibir e personalizar resultados de consulta em dados de um banco de dados KQL. Eles são um componente fundamental nas funcionalidades de consulta de dados do Microsoft Fabric, permitindo aos usuários:

 - **Executar Consultas:** Executar consultas KQL para recuperar dados de um banco de dados KQL.
 - **Personalizar Resultados:** Exibir e modificar os resultados da consulta, facilitando a análise e a interpretação dos dados.
 - **Salvar e Compartilhar Consultas:** Criar várias guias em um conjunto de consultas para salvar consultas para uso posterior ou compartilhá-las com outras pessoas para exploração de dados colaborativas.
 - **Suporte a Funções SQL:** Ao usar o KQL para criar consultas, os conjuntos de consultas também dão suporte a muitas funções SQL, proporcionando flexibilidade na consulta de dados.
 - **Aproveitar o Copilot:** Depois de salvar consultas como um conjunto de consultas KQL, você poderá exibir

Salvar um conjunto de consultas é simples e tem algumas abordagens. 

1. Em seu **banco de dados KQL** ao usar a ferramenta **Explorar seus dados**, basta selecionar **Salvar como conjunto de consultas KQL**

   ![Salvar o conjunto de consultas KQL de Explorar seus dados](./Images/save-as-queryset.png)

2. Outra abordagem é na página de aterrissagem da Inteligência em Tempo Real selecionando o botão **Conjunto de Consultas KQL** na página e, em seguida, nomeando seu **conjunto de consultas**

   ![Criar um novo Conjunto de Consultas KQL na página de aterrissagem da Inteligência em Tempo Real](./Images/select-create-new-queryset.png)

3. Depois de estar na **Página de aterrissagem do conjunto de consultas** você verá um botão **Copilot** na barra de ferramentas. Selecione-o para abrir o **Painel do Copilot** para fazer perguntas sobre os dados.

    [ ![Abrir o Copilot na barra de menus](./Images/open-copilot-in-queryset.png) ](./Images/open-copilot-in-queryset-large.png)

4. No painel do **Copilot**, digite sua pergunta e o **Copilot** gerará a consulta KQL e permitirá que você ***copie*** ou ***insira** a consulta na janela do conjunto de consultas. 

    [ ![gravar consulta do copilot fazendo uma pergunta](./Images/copilot-queryset-results.png) ](./Images/copilot-queryset-results-large.png)

5. A partir deste ponto, você tem a opção de fazer consultas individuais e usá-las em painéis ou Relatórios do Power BI usando o **Fixar no painel** ou **Criar Relatório do PowerBI**.

## Limpar os recursos

Neste exercício, você criou um banco de dados KQL e configurou o streaming contínuo com fluxo de eventos. Depois disso, você consultou os dados usando o KQL e o SQL. Depois de explorar o banco de dados KQL, exclua o workspace criado para este exercício.
1. Na barra à esquerda, selecione o ícone do seu workspace.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
.
