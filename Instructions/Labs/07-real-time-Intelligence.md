---
lab:
  title: Introdução à inteligência em tempo real no Microsoft Fabric
  module: Get started with Real-Time Intelligence in Microsoft Fabric
---

# Introdução à inteligência em tempo real no Microsoft Fabric

O Microsoft Fabric fornece inteligência em tempo real, permitindo criar soluções analíticas para fluxos de dados em tempo real. Neste exercício, você usará os recursos de inteligência em tempo real no Microsoft Fabric para ingerir, analisar e visualizar um fluxo em tempo real de dados do mercado de ações.

Este laboratório leva cerca de **30** minutos para ser concluído.

> **Observação**: para concluir este exercício, você precisa de um [locatário do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial).

## Criar um workspace

Antes de trabalhar com os dados no Fabric, você precisa criar um espaço de trabalho em um locatário com a funcionalidade do Fabric habilitada.

1. Navegue até a [home page do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric) em `https://app.fabric.microsoft.com/home?experience=fabric` em um navegador e entre com suas credenciais do Fabric.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um fluxo de eventos

Agora você está pronto para encontrar e ingerir dados em tempo real de uma fonte de streaming. Para fazer isso, você começará no hub em tempo real do Fabric.

> **Dica**: na primeira vez que você usar o hub em tempo real, algumas dicas de *introdução* podem ser exibidas. Você pode fecha-las.

1. Na barra de menus à esquerda, selecione o hub em **tempo real**.

    O hub em tempo real fornece uma maneira fácil de encontrar e gerenciar fontes de dados de streaming.

    ![Captura de tela do hub em tempo real no Fabric.](./Images/real-time-hub.png)

1. No hub em tempo real, na seção **Conectar-se a**, selecione **Fontes de dados**.
1. Encontre a fonte de dados de amostra **Mercado de Ações** e selecione **Conectar**. Em seguida, no assistente **Conexão**, nomeie a fonte como `stock` e edite o nome padrão do eventstream para alterá-lo para `stock-data`. O fluxo padrão associado a esses dados será automaticamente denominado *stock-data-stream*:

    ![Captura de tela de um novo eventstream.](./Images/name-eventstream.png)

1. Selecione **Avançar**, em seguida, **Conectar** e aguarde a criação da fonte e do eventstream. Em seguida, selecione **Abrir eventstream**. O eventstream mostrará a fonte da **ação** e o **stock-data-stream** na tela de design:

   ![Captura de tela da tela do eventstream.](./Images/new-stock-stream.png)

## Criar uma eventhouse

O eventstream ingere os dados de ações em tempo real, mas atualmente não faz nada com eles. Vamos criar um eventhouse onde podemos armazenar os dados capturados em uma tabela.

1. Na barra de menus à esquerda, selecione **Criar**. Na página *Novo*, na seção *Inteligência em tempo real*, selecione **Eventhouse**. Dê um nome exclusivo de sua preferência.

    >**Observação**: se a opção **Criar** não estiver fixada na barra lateral, você precisará selecionar a opção de reticências (**...**) primeiro.

    Feche todas as dicas ou prompts exibidos até ver o novo eventhouse vazio.

    ![Captura de tela de um novo eventhouse](./Images/create-eventhouse.png)

1. No painel à esquerda, o eventhouse contém um banco de dados KQL com o mesmo nome do eventhouse. Você pode criar tabelas para seus dados em tempo real neste banco de dados ou criar bancos de dados adicionais conforme necessário.
1. Selecione o banco de dados e observe que há um *queryset* associado. Esse arquivo contém algumas consultas KQL de exemplo que você pode usar para começar a consultar as tabelas em seu banco de dados.

    No entanto, atualmente não há tabelas para consultar. Vamos resolver esse problema obtendo dados do eventstream em uma nova tabela.

1. Na página principal do banco de dados KQL, selecione **Obter dados**.
1. Para a fonte de dados, selecione **Eventstream** > **Eventstream existente**.
1. No painel **Selecionar ou criar uma tabela de destino**, crie uma nova tabela chamada `stock`. Em seguida, no painel **Configurar a fonte de dados**, selecione seu espaço de trabalho e o eventstream **stock-data** e nomeie a conexão `stock-table`.

   ![Captura de tela da configuração para carregar uma tabela de um eventstream.](./Images/configure-destination.png)

1. Use o botão **Avançar** para concluir as etapas para inspecionar os dados e, em seguida, concluir a configuração. Em seguida, feche a janela de configuração para ver seu eventhouse com a tabela de ações.

   ![Captura de tela de um eventhouse com uma tabela.](./Images/eventhouse-with-table.png)

    A conexão entre o fluxo e a tabela foi criada. Vamos verificar isso no eventstream.

1. Na barra de menus à esquerda, selecione o hub em **tempo real**. No menu **...** do fluxo **stock-data-stream**, selecione **Abrir eventstream**.

    O eventstream agora mostra um destino para o fluxo:

   ![Captura de tela de um eventstream com um destino.](./Images/eventstream-destination.png)

    > **Dica**: selecione o destino na tela de design e, se nenhuma visualização de dados for mostrada abaixo dele, selecione **Atualizar**.

    Neste exercício, você criou um eventstream muito simples que captura dados em tempo real e os carrega em uma tabela. Em uma solução real, você normalmente adicionaria transformações para agregar os dados em janelas temporais (por exemplo, para capturar o preço médio de cada ação em períodos de cinco minutos).

    Agora vamos explorar como você pode consultar e analisar os dados capturados.

## Consultar os dados capturados

O eventstream captura dados do mercado de ações em tempo real e os carrega em uma tabela em seu banco de dados KQL. Você pode consultar esta tabela para ver os dados capturados.

1. Na barra de menus à esquerda, selecione o banco de dados da sua casa de eventos.
1. Selecione o *queryset* do banco de dados.
1. No painel de consulta, modifique a primeira consulta de exemplo, conforme mostrado aqui:

    ```kql
    stock
    | take 100
    ```

1. Selecione o código de consulta e execute-o para ver 100 linhas de dados na tabela.

    ![Captura de tela de uma consulta KQL.](./Images/kql-stock-query.png)

1. Revise os resultados e modifique a consulta para recuperar o preço médio de cada símbolo de ação nos últimos cinco minutos:

    ```kql
    stock
    | where ["time"] > ago(5m)
    | summarize avgPrice = avg(todecimal(bidPrice)) by symbol
    | project symbol, avgPrice
    ```

1. Realce a consulta modificada e execute-a para ver os resultados.
1. Aguarde alguns segundos e execute-a novamente, observando que a média de preços muda à medida que novos dados são adicionados à tabela a partir do fluxo em tempo real.

## Criar um painel em tempo real

Agora que você tem uma tabela que está sendo preenchida por fluxo de dados, pode usar um painel em tempo real para visualizar os dados.

1. No editor de consultas, selecione a consulta KQL usada para recuperar os preços médios das ações nos últimos cinco minutos.
1. Na barra de ferramentas, selecione **Salvar no painel**. Em seguida, fixe a consulta **em um novo painel** com as seguintes configurações:
    - **Nome do painel**: `Stock Dashboard`
    - **Nome do bloco**: `Average Prices`
1. Crie o painel e abra-o. Ele deverá ser parecido com:

    ![Captura de tela de um novo painel.](./Images/stock-dashboard-table.png)

1. Na parte superior direita do painel, alterne de modo de **Visualização** para modo de **Edição**.
1. Clique no ícone **Editar** (*lápis*) do bloco **Preços médios**.
1. No painel **Formatação visual**, altere **Visual** de *Tabela* para *Coluna*:

    ![Captura de tela de um bloco de painel sendo editado.](./Images/edit-dashboard-tile.png)

1. Na parte superior do painel, selecione **Aplicar alterações** e exiba o painel modificado:

    ![Captura de tela de um painel com um bloco de gráfico.](./Images/stock-dashboard-chart.png)

    Agora você tem uma visualização ao vivo de seus dados de ações em tempo real.

## Criar um alerta

A Inteligência em tempo real no Microsoft Fabric inclui uma tecnologia chamada *Ativador*, que pode disparar ações com base em eventos em tempo real. Vamos usá-la para alertar você quando o preço médio das ações aumentar em um valor específico.

1. Na janela do painel que contém a visualização do preço das ações, na barra de ferramentas, selecione **Definir alerta**.
1. No painel **Definir alerta**, crie um alerta com as seguintes configurações:
    - **Executar consulta a cada**: cinco minutos.
    - **Verificar**: em cada evento agrupado por
    - **Campo de agrupamento**: símbolo
    - **Quando**: avgPrice
    - **Condição**: Aumenta em
    - **Valor**: 100
    - **Ação**: enviar um email para mim
    - **Salvar localização**
        - **Workspace**: *seu workspace*
        - **Item**: criar item
        - **Nome de novo item**: *um nome exclusivo de sua preferência*

    ![Captura de tela das configurações do alerta.](./Images/configure-activator.png)

1. Crie o alerta e aguarde até que ele seja salvo. Em seguida, feche o painel confirmando que ele foi criado.
1. Na barra de menus à esquerda, selecione a página do workspace (salvando as alterações não salvas no painel, se solicitado).
1. Na página do workspace, exiba os itens que você criou neste exercício, incluindo o ativador do alerta.
1. Abra o ativador e, em sua página, no nó **avgPrice** , selecione o identificador exclusivo para o alerta. Em seguida, exiba aguia **Histórico** .

    Seu alerta pode não ter sido disparado, caso em que o histórico não conterá dados. Se o preço médio das ações mudar em mais de 100, o ativador enviará um e-mail e o alerta será registrado no histórico.

## Limpar os recursos

Neste exercício, você criou um eventhouse, ingeriu dados em tempo real usando um eventstream, consultou os dados ingeridos em uma tabela de banco de dados KQL, criou um painel em tempo real para visualizar os dados em tempo real e configurou um alerta usando o Ativador.

Se você terminou de explorar a Inteligêmcia em tempo real no Fabric, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do seu workspace.
2. Na barra de ferramentas, selecione **Configurações do espaço de trabalho**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
