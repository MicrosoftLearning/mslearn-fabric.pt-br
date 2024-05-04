---
lab:
  title: Ingerir dados com um pipeline no Microsoft Fabric
  module: Use Data Factory pipelines in Microsoft Fabric
---

# Ingerir dados com um pipeline no Microsoft Fabric

Um data lakehouse é um armazenamento de dados analíticos comum para soluções de análise em escala de nuvem. Uma das principais tarefas de um engenheiro de dados é implementar e gerenciar a ingestão de dados de várias fontes de dados operacionais no lakehouse. No Microsoft Fabric, você pode implementar soluções de ETL (*extração, transformação e carregamento*) ou ELT (*extração, carregamento e transformação*) para a ingestão de dados por meio da criação de *pipelines*.

O Fabric também dá suporte ao Apache Spark, permitindo que você escreva e execute um código para processar dados em escala. Combinando as funcionalidades de pipeline e do Spark no Fabric, você pode implementar uma lógica de ingestão de dados complexa que copia dados de fontes externas para o armazenamento OneLake no qual o lakehouse se baseia e usa o código Spark para executar transformações de dados personalizadas antes de carregá-los em tabelas para análise.

Este laboratório levará aproximadamente **60** minutos para ser concluído.

> **Observação**: Você precisa de uma [avaliação do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir esse exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [página inicial do Microsoft Fabric](https://app.fabric.microsoft.com), selecione **Engenheiros de Dados do Synapse**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um lakehouse

Agora que você tem um espaço de trabalho, é hora de criar um data lakehouse no qual os dados serão ingeridos.

1. Na home page da **Engenharia de Dados do Synapse**, crie um **Lakehouse** com um nome de sua escolha.

    Após alguns minutos, um lakehouse sem **Tabelas** nem **Arquivos** será criado.

1. Na guia **Exibição do Lake** no painel à esquerda, no menu **…** do nó **Arquivos**, selecione **Nova subpasta** e crie uma subpasta chamada **new_data**.

## Criar um pipeline

Uma forma simples de ingerir dados é usar uma atividade **Copiar Dados** em um pipeline para extrair os dados de uma fonte e copiá-los para um arquivo no lakehouse.

1. Na página **Início** de sua casa no lago, selecione **Obter dados** e, em seguida, selecione **Novo pipeline de dados** e crie um novo pipeline de dados chamado **Ingerir dados de vendas**.
2. Se o assistente **Copiar Dados** não for aberto automaticamente, selecione **Copiar Dados** na página do editor de pipeline.
3. No assistente **Copiar Dados**, na página **Escolher uma fonte de dados**, na seção **fontes de dados**, selecione a guia **Protocolo genérico** e selecione **HTTP**.

    ![Captura de tela da página Escolher fonte de dados.](./Images/choose-data-source.png)

4. Escolha **Avançar**, selecione **Criar conexão** e insira as seguintes configurações para a conexão com a fonte de dados:
    - **URL**: `https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/sales.csv`
    - **Conexão**: crie uma conexão
    - **Nome da conexão**: *especifique um nome exclusivo*
    - **Tipo de autenticação**: Básica (*deixe o nome de usuário e a senha em branco*)
5. Selecione **Avançar**. Em seguida, verifique se as seguintes configurações estão selecionadas:
    - **URL Relativa**: *Deixar em branco*
    - **Método de solicitação**: GET
    - **Cabeçalhos adicionais**: *Deixar em branco*
    - **Cópia binária**: <u>Des</u>marcada
    - **Tempo limite de solicitação**: *Deixar em branco*
    - **Máximo de conexões simultâneas**: *Deixar em branco*
6. Selecione **Avançar**, aguarde a amostragem dos dados e verifique se as seguintes configurações estão selecionadas:
    - **Formato de arquivo**: DelimitedText
    - **Delimitador de colunas**: Vírgula (,)
    - **Delimitador de linha**: Alimentação de linha (\n)
    - **Primeira linha como cabeçalho**: Selecionada
    - **Tipo de compactação**: Nenhum
7. Selecione **Visualizar dados** para ver um exemplo dos dados que serão ingeridos. Em seguida, feche a visualização de dados e selecione **Avançar**.
8. Na página **Escolher destino de dados**, selecione o lakehouse existente. Em seguida, selecione **Avançar**.
9. Defina as seguintes opções de destino de dados e selecione **Avançar**:
    - **Pasta raiz**: Arquivos
    - **Nome do caminho da pasta**: new_data
    - **Nome do arquivo**: sales.csv
    - **Comportamento da cópia**: Nenhum
10. Defina as seguintes opções de formato de arquivo e selecione **Avançar**:
    - **Formato de arquivo**: DelimitedText
    - **Delimitador de colunas**: Vírgula (,)
    - **Delimitador de linha**: Alimentação de linha (\n)
    - **Adicionar cabeçalho ao arquivo**: Selecionado
    - **Tipo de compactação**: Nenhum
11. Na página **Copiar resumo**, analise os detalhes da operação de cópia e selecione **Salvar + Executar**.

    Um pipeline que contém uma atividade **Copiar Dados** será criado, conforme mostrado aqui:

    ![Captura de tela de um pipeline com uma atividade Copiar Dados.](./Images/copy-data-pipeline.png)

12. Quando o pipeline começar a ser executado, você poderá monitorar o status dele no painel **Saída** no designer de pipeline. Use o ícone **&#8635;** (*Atualizar*) para atualizar o status e aguarde até que ele tenha sido concluído com sucesso.

13. Na barra de menus à esquerda, selecione o lakehouse.
14. Na **Home page**, no painel do **Lakehouse Explorer**, expanda **Arquivos** e selecione a pasta **new_data** para verificar se o arquivo **sales.csv** foi copiado.

## Criar um notebook

1. Na **Home page** do lakehouse, no menu **Abrir notebook**, selecione **Novo notebook**.

    Após alguns segundos, um novo notebook que contém uma só *célula* será aberto. Os notebooks são compostos por uma ou mais células que podem conter um *código* ou um *markdown* (texto formatado).

2. Selecione a célula existente no notebook, que contém um código simples e substitua o código padrão pela declaração de variável a seguir.

    ```python
   table_name = "sales"
    ```

3. No menu **…** da célula (no canto superior direito), selecione **Alternar célula de parâmetro**. Isso configura a célula para que as variáveis declaradas nela sejam tratadas como parâmetros ao executar o notebook por meio de um pipeline.

4. Na célula de parâmetros, use o botão **+ Código** para adicionar uma nova célula de código. Em seguida, adicione o seguinte código a ela:

    ```python
   from pyspark.sql.functions import *

   # Read the new sales data
   df = spark.read.format("csv").option("header","true").load("Files/new_data/*.csv")

   ## Add month and year columns
   df = df.withColumn("Year", year(col("OrderDate"))).withColumn("Month", month(col("OrderDate")))

   # Derive FirstName and LastName columns
   df = df.withColumn("FirstName", split(col("CustomerName"), " ").getItem(0)).withColumn("LastName", split(col("CustomerName"), " ").getItem(1))

   # Filter and reorder columns
   df = df["SalesOrderNumber", "SalesOrderLineNumber", "OrderDate", "Year", "Month", "FirstName", "LastName", "EmailAddress", "Item", "Quantity", "UnitPrice", "TaxAmount"]

   # Load the data into a table
   df.write.format("delta").mode("append").saveAsTable(table_name)
    ```

    Esse código carrega os dados do arquivo sales.csv que foi ingerido pela atividade **Copiar Dados**, aplica uma lógica de transformação e salva os dados transformados como uma tabela, acrescentando os dados caso a tabela já exista.

5. Verifique se os notebooks são semelhantes a este e use o botão **&#9655; Executar tudo** na barra de ferramentas para executar todas as células que ele contém.

    ![Captura de tela de um notebook com uma célula de parâmetros e um código para transformar os dados.](./Images/notebook.png)

    > **Observação**: como esta é a primeira vez que você executa qualquer código Spark nesta sessão, o Pool do Spark precisa ser iniciado. Isso significa que a primeira célula pode levar alguns minutos para ser concluída.

6. Quando a execução do notebook for concluída, no painel do **Lakehouse Explorer** à esquerda, no menu **…** de **Tabelas**, selecione **Atualizar** e verifique se uma tabela **sales** foi criada.
7. Na barra de menus do notebook, use o ícone ⚙️ de **Configurações** para ver as configurações do notebook. Em seguida, defina o **Nome** do notebook como **Carregar Vendas** e feche o painel de configurações.
8. Na barra de menus do hub à esquerda, selecione o lakehouse.
9. No painel do **Explorer**, atualize a exibição. Em seguida, expanda **Tabelas** e selecione a tabela **sales** para ver uma visualização dos dados que ela contém.

## Modificar o pipeline

Agora que você implementou um notebook para transformar dados e carregá-los em uma tabela, incorpore o notebook em um pipeline para criar um processo de ETL reutilizável.

1. Na barra de menus do hub à esquerda, selecione o pipeline **Ingerir Dados de Vendas** criado anteriormente.
2. Na guia **Atividades**, na lista **Mais atividades**, selecione **Excluir dados**. Em seguida, posicione a nova atividade **Excluir dados**  à esquerda da atividade **Copiar dados** e conecte a saída **Ao concluir** à atividade **Copiar dados**, conforme mostrado aqui:

    ![Captura de tela de um pipeline com as atividades Excluir dados e Copiar dados.](./Images/delete-data-activity.png)

3. Selecione a atividade **Excluir dados** e, no painel abaixo da tela de design, defina as seguintes propriedades:
    - **Geral**:
        - **Nome**: Excluir arquivos antigos
    - **Origem**
        - **Tipo de armazenamento de dados**: Workspace
        - **Armazenamento de dados do workspace**: *seu lakehouse*
        - **Tipo de caminho de arquivo**: caminho do arquivo curinga
        - **Caminho da pasta**: Arquivos/**new_data**
        - **Nome do arquivo curinga**: *.csv        
        - **Recursivamente**: *Selecionado*
    - **Configurações de log**:
        - **Habilitar log**: *<u>Des</u>marcado*

    Essas configurações garantirão que todos os arquivos .csv existentes sejam excluídos antes da cópia do arquivo **sales.csv**.

4. No designer de pipeline, na guia **Atividades**, selecione **Notebook** para adicionar uma atividade **Notebook** ao pipeline.
5. Selecione a atividade **Copiar dados** e conecte a saída **Ao concluir** à atividade **Notebook**, conforme mostrado aqui:

    ![Captura de tela de um pipeline com as atividades Copiar Dados e Notebook.](./Images/pipeline.png)

6. Selecione a atividade **Notebook** e, no painel abaixo da tela de design, defina as seguintes propriedades:
    - **Geral**:
        - **Nome**: Carregar notebook de Vendas
    - **Configurações**:
        - **Notebook**: Carregar Vendas
        - **Parâmetros base**: *adicione um novo parâmetro com as seguintes propriedades:*
            
            | Nome | Tipo | Valor |
            | -- | -- | -- |
            | table_name | String | new_sales |

    O parâmetro **table_name** será transmitido para o notebook e substituirá o valor padrão atribuído à variável **table_name** na célula de parâmetros.

7. Na guia **Página Inicial**, use o ícone **&#128427;** (*Salvar*) para salvar o pipeline. Em seguida, use o botão **&#9655; Executar** para executar o pipeline e aguarde a conclusão de todas as atividades.

    ![Captura de tela de um pipeline com uma atividade Fluxo de Dados.](./Images/pipeline-run.png)

> Observação: Caso você receba a mensagem de erro *As consultas Spark SQL só são possíveis no contexto de uma lakehouse. Anexe uma casa do lago para prosseguir*: Abra seu notebook, selecione a casa do lago que você criou no painel esquerdo, selecione **Remover todas as casas do lago** e adicione-a novamente. Volte para o designer de pipeline e selecione **&#9655; Execute**.

8. Na barra de menus do hub na borda esquerda do portal, selecione o lakehouse.
9. No painel do **Explorer**, expanda **Tabelas** e selecione a tabela **new_sales** para ver uma visualização dos dados que ela contém. Essa tabela foi criada pelo notebook quando foi executada pelo pipeline.

Neste exercício, você implementou uma solução de ingestão de dados que usa um pipeline para copiar dados para o lakehouse de uma fonte externa e, depois, usa um notebook do Spark para transformar os dados e carregá-los em uma tabela.

## Limpar os recursos

Neste exercício, você aprendeu a implementar um pipeline no Microsoft Fabric.

Se você tiver terminado de explorar seu lakehouse, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
