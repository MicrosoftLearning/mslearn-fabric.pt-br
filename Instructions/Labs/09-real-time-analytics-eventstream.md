---
lab:
  title: Introdução à análise em tempo real no Microsoft Fabric
  module: Get started with Real-Time Analytics in Microsoft Fabric
---
# Introdução aos fluxos de eventos na RTA (análise em tempo real)

Os fluxos de eventos são um recurso do Microsoft Fabric que captura, transforma e roteia eventos em tempo real para diversos destinos com uma experiência sem código. Você pode adicionar fontes de dados de evento, destinos de roteamento e o processador de eventos quando a transformação for necessária ao fluxo de eventos. O EventStore do Microsoft Fabric é uma opção de monitoramento que mantém os eventos do cluster e fornece uma maneira de entender o estado dele ou das cargas de trabalho em um determinado momento. O serviço EventStore pode ser consultado para eventos que estão disponíveis para cada entidade e tipo de entidade em seu cluster. Isso significa que você pode consultar eventos em diferentes níveis, como cluster, nós, aplicativos, serviços, partições e réplicas de partição. O serviço EventStore também tem a capacidade de correlacionar eventos em seu cluster. Examinando os eventos que foram gravados ao mesmo tempo de diferentes entidades que podem ter se afetado entre si, o serviço EventStore é capaz de vincular esses eventos para ajudar a identificar as causas de atividades em seu cluster. Outra opção de monitoramento e diagnóstico de clusters do Microsoft Fabric é agregar e coletar eventos usando o fluxo de eventos.

<!--

SL comments - I can't find anything in the documentation about **EventStore** or **EventFlow**. Is this a feature that isn't released yet? Here's the doc I referred to for monitoring event streams: https://learn.microsoft.com/fabric/real-time-analytics/event-streams/monitor

Does that fit here?

-->

Este laboratório leva cerca de **30** minutos para ser concluído.

> **Observação**: você precisará ter uma licença do Microsoft Fabric para concluir este exercício. Confira [Introdução ao Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para obter detalhes de como habilitar uma licença de avaliação gratuita do Fabric. Você precisará ter uma conta *corporativa* ou de *estudante* da Microsoft para fazer isso. Caso não tenha uma, [inscreva-se em uma avaliação do Microsoft Office 365 E3 ou superior](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Entre no [Microsoft Fabric](https://app.fabric.microsoft.com) em `https://app.fabric.microsoft.com` e selecione **Power BI**.
2. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
3. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
4. Quando o novo workspace for aberto, ele deverá estar vazio, conforme mostrado aqui:

   ![Captura de tela de um workspace vazio no Power BI.](./Images/new-workspace.png)
5. No canto inferior esquerdo do portal do Power BI, selecione o ícone do **Power BI** e alterne para a experiência de **Análise em tempo real**.

## Cenário

Com os fluxos de eventos do Fabric, é possível gerenciar facilmente dados de eventos em um só lugar. Você pode coletar, transformar e enviar dados de eventos em tempo real para destinos diferentes no formato desejado. Também é possível conectar fluxos de eventos com Hubs de Eventos do Azure, bancos de dados KQL e Lakehouses com facilidade.

Este laboratório se baseia em dados de streaming de exemplo chamados Dados do Mercado de Ações. Os dados de exemplo de Mercado de Ações são um conjunto de dados de uma bolsa de valores com uma coluna de esquema predefinida, como hora, símbolo, preço, volume, entre outros. Você usará esses dados de exemplo para simular eventos em tempo real de preços de ações e analisá-los com vários destinos, como o banco de dados KQL.

Use os recursos de streaming e consulta da análise em tempo real para responder às principais perguntas sobre as estatísticas de estoque. Nesse cenário, vamos aproveitar ao máximo o assistente em vez de criar manualmente alguns componentes de maneira independente, como o Banco de Dados KQL.

<!--

I removed the piece on Power BI reports because we don't have them do that in this lab.

-->

Neste tutorial, você aprenderá como:

- Criar um banco de dados KQL
- Habilitar a cópia de dados para o OneLake
- Criar um fluxo de eventos
- Transmitir dados de um fluxo de eventos para o banco de dados KQL
- Explorar dados com o KQL e o SQL

<!--

For "enable data copy to OneLake" - are you adding a lakehouse as a destination? The word copy confuses me.

-->

## Criar um banco de dados KQL

1. Na **Análise em Tempo Real**, selecione a caixa **Banco de Dados KQL**.

   ![escolher banco de dados KQL](./Images/select-kqldatabase.png)

2. Você precisará **Nomear** o banco de dados KQL

   ![nomear o banco de dados KQL](./Images/name-kqldatabase.png)

3. Dê ao banco de dados KQL um nome do qual você se lembrará, como **MyStockData**, e pressione **Criar**.

1. No painel **Detalhes do banco de dados**, selecione o ícone de lápis para ativar a disponibilidade no OneLake.

   ![habilitar o OneLake](./Images/enable-onelake-availability.png)

2. Alterne o botão para **Ativo** e selecione **Concluído**.

   ![habilitar a alternância do OneLake](./Images/enable-onelake-toggle.png)

## Criar um fluxo de eventos

1. Na barra de menus, selecione **Análise em Tempo Real** (o ícone é semelhante ao ![logotipo da RTA](./Images/rta_logo.png))
2. Em **Novo**, selecione **Fluxo de Eventos (Versão Prévia)**

   ![escolher fluxo de eventos](./Images/select-eventstream.png)

3. Será solicitado a você **Nomear** o fluxo de eventos. Dê ao Fluxo de Eventos um nome do qual você se lembrará, como ***MyStockES**, e pressione o botão **Criar**.

   ![nomear o fluxo de eventos](./Images/name-eventstream.png)

## Estabelecer uma origem e um destino para um fluxo de eventos

1. Na tela do Fluxo de eventos, selecione **Nova fonte** na lista suspensa e selecione **Dados de Exemplo**.

   ![Tela do Fluxo de eventos](./Images/real-time-analytics-canvas.png)

2. Insira os valores dos Dados de Exemplo, conforme mostrado na tabela a seguir, e selecione **Adicionar e Configurar**.

   | Campo       | Valor recomendado |
   | ----------- | ----------------- |
   | Nome de origem | StockData         |
   | Dados de exemplo | Mercado de Ações      |

3. Agora, adicione um destino selecionando **Novo destino** e clique em **Banco de dados KQL**.

   ![Destino do Fluxo de eventos](./Images/new-kql-destination.png)

4. Na configuração do Banco de Dados KQL, use a tabela a seguir para concluir a configuração.

   | Campo            | Valor recomendado                              |
   | ---------------- | ---------------------------------------------- |
   | Nome do destino | MyStockData                                    |
   | Workspace        | O workspace em que você criou um banco de dados KQL |
   | Banco de dados KQL     | MyStockData                                    |

3. Selecione **Adicionar e configurar**.

## Configurar a ingestão de dados

1. Na página da caixa de diálogo **Ingerir dados**, selecione a **Nova Tabela** e insira MyStockData.

   ![inserir os dados de ações](./Images/ingest-stream-data-to-kql.png)

2. Selecione **Avançar: origem**.
3. Na página **Origem**, confirme o **Nome da conexão de dados** e selecione **Avançar: Esquema**.

   ![nome da fonte de dados](./Images/ingest-data.png)

4. Os dados de entrada são descompactados para os dados de exemplo, ou seja, mantenha o tipo de compactação como descompactado.
5. Na lista suspensa **Formato de Dados**, selecione **JSON**.

   ![Alterar para JSON](./Images/injest-as-json.png)

6. Depois disso, pode ser necessário alterar alguns ou todos os tipos de dados do fluxo de entrada para as tabelas de destino.
7. Realize essa tarefa selecionando a **seta para baixo > Alterar tipo de dados**. Em seguida, verifique se as colunas refletem o tipo de dados correto:

   ![alterar tipos de dados](./Images/change-data-type-in-es.png)

8. Quando terminar, selecione **Avançar: Resumo**

Aguarde até que todas as etapas sejam marcadas com marcas de seleção verde. Você verá o título da página **Ingestão contínua do fluxo de eventos estabelecida.** Depois disso, selecione **Fechar** para voltar à página Fluxo de eventos.

> **Observação**: pode ser necessário atualizar a página para exibir a tabela depois que a conexão do fluxo de eventos for criada e estabelecida

## Consultas KQL

O KQL (Linguagem de Consulta Kusto) é uma solicitação somente leitura para processar dados e retornar resultados. A solicitação é declarada em texto sem formatação que é fácil de ler, criar e automatizar. As consultas sempre são executadas no contexto de uma tabela ou de um banco de dados específico. No mínimo, uma consulta consiste em uma referência de dados de origem e em um ou mais operadores de consulta aplicados em sequência, indicados visualmente pelo uso de um caractere de barra vertical (|) para delimitar os operadores. Para saber mais sobre a Linguagem de Consulta Kusto, confira [Visão geral do KQL (Linguagem de Consulta Kusto)](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/?context=%2Ffabric%2Fcontext%2Fcontext)

> **Observação**: o editor KQL é fornecido com realce de sintaxe e Inellisense, o que permite conhecer rapidamente a KQL (Linguagem de Consulta Kusto).

1. Navegue até o banco de dados KQL recém-criado e hidratado chamado ***MyStockData***.
2. Na árvore Dados, selecione o menu Mais […] na tabela MyStockData. Em seguida, selecione Tabela de consulta > Mostrar quaisquer 100 registros.

   ![Conjunto de consultas KQL](./Images/kql-query-sample.png)

3. A consulta de exemplo será aberta no painel **Explorar seus dados** com o contexto de tabela já preenchido. Essa primeira consulta usa o operador take para retornar um exemplo de número de registros e é útil para dar uma primeira olhada na estrutura de dados e nos valores possíveis. Os exemplos de consultas preenchidas automaticamente são executados automaticamente. Você poderá ver os resultados da consulta no painel de resultados.

   ![Resultados da consulta KQL](./Images/kql-query-results.png)

4. Volte à árvore de dados para selecionar a próxima consulta, que usa os operadores where e between para retornar os registros ingeridos nas últimas 24 horas.

   ![Resultados da consulta KQL nas últimas 24 horas](./Images/kql-query-results-last24.png)

> **Observação**: observe que os volumes dos dados de streaming excedem os limites da consulta. Esse comportamento poderá variar conforme o volume de dados transmitidos para o banco de dados.

Você pode continuar navegando com as funções de consulta internas para se familiarizar com seus dados.

## Exemplos de consultas SQL

O editor de consultas dá suporte ao uso do T-SQL, além do KQL (Linguagem de Consulta Kusto) de consulta primária. O T-SQL pode ser útil para ferramentas que não podem usar o KQL. Para obter mais informações, confira [Consultar dados usando o T-SQL](https://learn.microsoft.com/en-us/azure/data-explorer/t-sql)

1. De volta à árvore Dados, selecione o **menu Mais** […] na tabela MyStockData. Selecione **Consultar tabela > SQL > Mostrar quaisquer 100 registros**.

   ![exemplo de consulta SQL](./Images/sql-query-sample.png)

2. Coloque o cursor em algum lugar dentro da consulta e selecione **Executar** ou pressione **SHIFT + ENTER**.

   ![resultados da consulta sql](./Images/sql-query-results.png)

Você pode continuar navegando usando as funções de build e se familiarizar com os dados usando o SQL ou o KQL. Isso encerra a lição.

## Limpar os recursos

Neste exercício, você criou um banco de dados KQL e configurou o streaming contínuo com fluxo de eventos. Depois disso, você consultou os dados usando o KQL e o SQL. Depois de explorar o banco de dados KQL, exclua o workspace criado para este exercício.
1. Na barra à esquerda, selecione o ícone do workspace.
2. No menu … da barra de ferramentas, selecione Configurações do workspace.
3. Na seção Outros, selecione Remover este workspace.

<!--

Overall notes: 
- screenshot alt text needs to be more descriptive and start with the words "screenshot of"
- 

-->