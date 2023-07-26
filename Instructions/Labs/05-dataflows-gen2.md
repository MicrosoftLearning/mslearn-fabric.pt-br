---
lab:
  title: Criar e usar Fluxos de Dados (Gen2) no Microsoft Fabric
  module: Ingest Data with Dataflows Gen2 in Microsoft Fabric
---

# Criar um Fluxo de Dados (Gen2) no Microsoft Fabric

No Microsoft Fabric, os Fluxos de Dados (Gen2) se conectam a várias fontes de dados e executam transformações no Power Query Online. Em seguida, eles podem ser usados em pipelines de dados para ingerir dados em um lakehouse ou em outro repositório analítico ou para definir um conjunto de dados para um relatório do Power BI.

Este laboratório foi projetado para introduzir os diferentes elementos dos Fluxos de Dados (Gen2) e não criar uma solução complexa que possa existir em uma empresa. Ele leva **cerca de 30 minutos** para ser concluído.

> **Observação**: você precisará ter uma licença do Microsoft Fabric para concluir este exercício. Confira [Introdução ao Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para obter detalhes de como habilitar uma licença de avaliação gratuita do Fabric. Você precisará ter uma conta *corporativa* ou de *estudante* da Microsoft para fazer isso. Caso não tenha uma, [inscreva-se em uma avaliação do Microsoft Office 365 E3 ou superior](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Entre no [Microsoft Fabric](https://app.fabric.microsoft.com) em `https://app.fabric.microsoft.com` e selecione **Power BI**.
2. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
3. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
4. Quando o novo workspace for aberto, ele deverá estar vazio, conforme mostrado aqui:

    ![Captura de tela de um workspace vazio no Power BI.](./Images/new-workspace.png)

## Criar um lakehouse

Agora que você tem um workspace, é hora de alternar para a experiência de **Engenharia de Dados** no portal e criar um data lakehouse no qual você vai ingerir dados.

1. No canto inferior esquerdo do portal do Power BI, selecione o ícone do **Power BI** e alterne para a experiência de **Engenharia de Dados**.

2. Na home page de **Engenharia de dados**, crie um **Lakehouse** com um nome de sua escolha.

    Após alguns minutos, um lakehouse vazio será criado.

 ![Novo lakehouse.](./Images/new-lakehouse.png)

## Criar um Fluxo de Dados (Gen2) para ingerir dados

Agora que você tem um lakehouse, você precisa ingerir alguns dados nele. Uma forma de fazer isso é definir um fluxo de dados que encapsula um processo de ETL (*extração, transformação e carregamento*).

1. Na home page do workspace, selecione **Novo Fluxo de Dados Gen2**. Após alguns segundos, o editor do Power Query do novo fluxo de dados será aberto, conforme mostrado aqui.

 ![Novo fluxo de dados.](./Images/new-dataflow.png)

2. Selecione **Importar de um arquivo de Texto/CSV** e crie uma fonte de dados com as seguintes configurações:
 - **Vincular ao arquivo**: *Selecionado*
 - **Caminho ou URL do arquivo**: `https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/orders.csv`
 - **Conexão**: crie uma conexão
 - **gateway de dados**: (nenhum)
 - **Tipo de autenticação**: Anônimo

3. Selecione **Avançar** para visualizar os dados do arquivo e **Criar** para criar a fonte de dados. O editor do Power Query mostra a fonte de dados e um conjunto inicial de etapas de consulta para formatar os dados, conforme mostrado aqui:

 ![Consulta no editor do Power Query.](./Images/power-query.png)

4. Na faixa de opções da barra de ferramentas, selecione a guia **Adicionar coluna**. Em seguida, selecione **Coluna personalizada** e crie uma coluna chamada **MonthNo** que contém um número baseado na fórmula `Date.Month([OrderDate])`, conforme mostrado aqui:

 ![Coluna personalizada no editor do Power Query.](./Images/custom-column.png)

 A etapa usada para adicionar a coluna personalizada é adicionada à consulta e a coluna resultante é exibida no painel de dados:

 ![Consulta com uma etapa de coluna personalizada.](./Images/custom-column-added.png)

> **Dica:** no painel Configurações de Consulta no lado direito, observe que as **Etapas Aplicadas** incluem cada etapa de transformação. Na parte inferior, você também pode ativar o botão **Fluxo de diagrama** para ativar o Diagrama Visual das etapas.
>
> As etapas podem ser movidas para cima ou para baixo e editadas com a seleção do ícone de engrenagem, e você pode selecionar cada etapa para ver as transformações aplicadas no painel de visualização.

## Adicionar destino de dados ao fluxo de dados

1. Na faixa de opções da barra de ferramentas, selecione a guia **Página Inicial**. Em seguida, no menu suspenso **Adicionar destino de dados**, selecione **Lakehouse**.

   > **Observação:** se essa opção está esmaecida, talvez você já tenha um conjunto de destino de dados. Verifique o destino de dados na parte inferior do painel Configurações de consulta no lado direito do editor do Power Query. Se um destino já estiver definido, você poderá alterá-lo usando a engrenagem.

2. Na caixa de diálogo **Conectar-se ao destino de dados**, edite a conexão e conecte-se usando sua conta organizacional do Power BI para definir a identidade usada pelo fluxo de dados para acessar o lakehouse.

 ![Página de configuração de destino de dados.](./Images/dataflow-connection.png)

3. Selecione **Avançar** e, na lista de workspaces disponíveis, localize seu workspace e selecione o lakehouse que você criou nele no início deste exercício. Em seguida, especifique uma nova tabela chamada **orders**:

   ![Página de configuração de destino de dados.](./Images/data-destination-target.png)

   > **Observação:** na página **Configurações de destino**, observe como OrderDate e MonthNo não são selecionados no Mapeamento de coluna e há uma mensagem informativa: *Alterar para data/hora*.

   ![Página de configurações de destino de dados.](./Images/destination-settings.png)

1. Cancele essa ação e volte às colunas OrderDate e MonthNo no Power Query Online. Clique com o botão direito do mouse no cabeçalho da coluna e em **Alterar Tipo**.

    - OrderDate = Data/Hora
    - MonthNo = Número inteiro

1. Agora, repita o processo descrito anteriormente para adicionar um destino de lakehouse.

8. Na página **Configurações de destino**, selecione **Acrescentar** e salve as configurações.  O destino de **Lakehouse** é indicado como um ícone na consulta no editor do Power Query.

   ![Consulta com um destino de lakehouse.](./Images/lakehouse-destination.png)

9. Selecione **Publicar** para publicar o fluxo de dados. Em seguida, aguarde até que o fluxo de dados **Fluxo de dados 1** seja criado no seu workspace.

1. Após a publicação, clique com o botão direito do mouse no fluxo de dados do workspace, selecione **Propriedades** e renomeie o fluxo de dados.

## Adicionar um fluxo de dados a um pipeline

Você pode incluir um fluxo de dados como uma atividade em um pipeline. Os pipelines são usados para orquestrar as atividades de ingestão e processamento de dados, permitindo que você combine fluxos de dados com outros tipos de operação em um processo agendado unificado. Os pipelines podem ser criados em algumas experiências diferentes, incluindo a experiência do Data Factory.

1. No workspace habilitado para o Fabric, verifique se você ainda está na experiência de **Engenharia de Dados**. Selecione **Novo**, **Pipeline de dados** e, quando solicitado, crie um pipeline chamado **Carregar dados**.

   O editor de pipeline será aberto.

   ![Pipeline de dados vazio.](./Images/new-pipeline.png)

   > **Dica**: se o assistente Copiar Dados for aberto automaticamente, feche-o.

2. Selecione **Adicionar atividade de pipeline** e adicione uma atividade **Fluxo de Dados** ao pipeline.

3. Com a nova atividade **Fluxo de dados1** selecionada, na guia **Configurações**, na lista suspensa **Fluxo de dados**, selecione **Fluxo de dados 1** (o fluxo de dados já criado)

   ![Pipeline com uma atividade de fluxo de dados.](./Images/dataflow-activity.png)

4. Na guia **Página Inicial**, salve o pipeline usando o ícone **&#128427;** (*Salvar*).
5. Use o botão **&#9655; Executar** para executar o pipeline e aguarde até que ele seja concluído. Isso pode levar alguns minutos.

   ![Pipeline com um fluxo de dados que foi concluído com sucesso.](./Images/dataflow-pipeline-succeeded.png)

6. Na barra de menus na borda esquerda, selecione o lakehouse.
7. No menu **…** de **Tabelas**, selecione **Atualizar**. Em seguida, expanda **Tabelas** e selecione a tabela **orders**, que foi criada pelo fluxo de dados.

   ![Tabela carregada por um fluxo de dados.](./Images/loaded-table.png)

> **Dica**: use o *conector de Fluxos de dados* do Power BI Desktop para se conectar diretamente às transformações de dados feitas com o fluxo de dados.
>
> Você também pode fazer transformações adicionais, publicá-las como um novo conjunto de dados e distribuí-las com o público-alvo pretendido para conjuntos de dados especializados.
>
>![Conectores de fonte de dados do Power BI](Images/pbid-dataflow-connectors.png)

## Limpar os recursos

Se você terminou de explorar os fluxos de dados no Microsoft Fabric, exclua o workspace criado para este exercício.

1. Navegue até o Microsoft Fabric no navegador.
1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
1. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
1. Na seção **Outros**, selecione **Remover este workspace**.
1. Não salve as alterações no Power BI Desktop nem exclua o arquivo .pbix se ele já estiver salvo.
