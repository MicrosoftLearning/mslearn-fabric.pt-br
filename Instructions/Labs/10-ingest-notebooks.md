---
lab:
  title: Ingerir dados com o Spark e os notebooks do Microsoft Fabric
  module: Ingest data with Spark and Microsoft Fabric notebooks
---

# Ingerir dados com o Spark e os notebooks do Microsoft Fabric

Neste laboratório, você criará um notebook do Microsoft Fabric e usará o PySpark para se conectar a um caminho de Armazenamento de Blobs do Azure e, em seguida, carregará os dados em um lakehouse utilizando otimizações de gravação.

Este laboratório levará aproximadamente **30** minutos para ser concluído.

Para essa experiência, você criará o código em várias células de código do notebook, o que pode não refletir como você fará o mesmo em seu ambiente; no entanto, isso pode ser útil para depuração.

Como você também está trabalhando com um conjunto de dados de exemplo, a otimização não reflete o que você pode ver na produção em escala; no entanto, você ainda pode ver melhorias e, quando cada milissegundo conta, a otimização é fundamental.

> **Observação**: Você precisará uma [avaliação gratuita do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir este exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [página inicial do Microsoft Fabric](https://app.fabric.microsoft.com), selecione **Engenheiros de Dados do Synapse**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar espaços de trabalho e destino do lakehouse

Comece criando um novo lakehouse e uma pasta de destino no lakehouse.

1. No seu espaço de trabalho, selecione **+ Novo > Lakehouse**, forneça um nome e **Criar**.

    > **Observação:** pode levar alguns minutos para criar um novo lakehouse sem **tabelas** ou **arquivos**.

    ![Captura de tela de um novo lakehouse](Images/new-lakehouse.png)

1. Em **Arquivos**, selecione o **[...]** para criar **Uma subpasta** chamada **RawData**.

1. No Lakehouse Explorer, dentro do lakehouse, selecione **RawData > ... > Propriedades**.

1. Copie o caminho **ABFS** na pasta **RawData** em um bloco de notas vazio para uso posterior, que deve ser semelhante a:  `abfss://{workspace_name}@onelake.dfs.fabric.microsoft.com/{lakehouse_name}.Lakehouse/Files/{folder_name}/{file_name}`

Agora você deve ter um espaço de trabalho com um lakehouse e uma pasta de destino RawData.

## Criar um notebook do Fabric e carregar dados externos

Crie um novo notebook do Fabric e conecte-se à fonte de dados externa com o PySpark.

1. No menu superior do Lakehouse, selecione **Abrir notebook > Novo notebook**, que será aberto uma vez criado.

    >  **Dica:** você tem acesso ao Lakehouse Explorer de dentro deste bloco de anotações e pode atualizar para ver o progresso ao concluir este exercício.

1. Na célula padrão, observe que o código está definido como **PySpark (Python)** .

1. Insira o código a seguir na célula de código, o que fará com que:
    - Declarar parâmetros para a cadeia de caracteres de conexão
    - Criar a cadeia de conexão
    - Ler os dados em um DataFrame

    ```Python
    # Azure Blob Storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    
    # Construct connection path
    wasbs_path = f'wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}'
    print(wasbs_path)
    
    # Read parquet data from Azure Blob Storage path
    blob_df = spark.read.parquet(wasbs_path)
    ```

1. Selecione **&#9655; Executar Célula** ao lado da célula do código para conectar e ler dados em um DataFrame.

    **Resultado esperado:**  seu comando deve ser bem-sucedido e imprimir `wasbs://nyctlc@azureopendatastorage.blob.core.windows.net/yellow`

    > **Observação:** uma sessão do Spark começa na primeira execução de código, portanto, pode levar mais tempo para ser concluída.

1. Para gravar os dados em um arquivo, agora você precisa do **Caminho ABFS** para sua pasta **RawData**.

1. Insira o seguinte código em uma **nova célula de código**:

    ```python
        # Declare file name    
        file_name = "yellow_taxi"
    
        # Construct destination path
        output_parquet_path = f"**InsertABFSPathHere**/{file_name}"
        print(output_parquet_path)
        
        # Load the first 1000 rows as a Parquet file
        blob_df.limit(1000).write.mode("overwrite").parquet(output_parquet_path)
    ```

1. Adicione seu caminho ABFS **RawData** e selecione **&#9655; Executar Célula** para gravar 1000 linhas em um arquivo yellow_taxi.parquet.

1. Seu **output_parquet_path** deve ser semelhante a:  `abfss://Spark@onelake.dfs.fabric.microsoft.com/DPDemo.Lakehouse/Files/RawData/yellow_taxi`

1. Para confirmar o carregamento de dados do Lakehouse Explorer, selecione **Arquivos > .... > Atualizar**.

Agora você deverá ver sua nova pasta **RawData** com um "arquivo" **yellow_taxi.parquet** " *que mostra uma pasta com arquivos de partição dentro dela*.

## Transformar e carregar dados em uma tabela Delta

É provável que sua tarefa de ingestão de dados não termine apenas com o carregamento de um arquivo. As tabelas Delta em um lakehouse permitem consultas e armazenamento escalonáveis e flexíveis, portanto, também criaremos uma.

1. Crie uma nova célula de código e insira o código a seguir:

    ```python
    from pyspark.sql.functions import col, to_timestamp, current_timestamp, year, month
    
    # Read the parquet data from the specified path
    raw_df = spark.read.parquet(output_parquet_path)   
    
    # Add dataload_datetime column with current timestamp
    filtered_df = raw_df.withColumn("dataload_datetime", current_timestamp())
    
    # Filter columns to exclude any NULL values in storeAndFwdFlag
    filtered_df = filtered_df.filter(raw_df["storeAndFwdFlag"].isNotNull())
    
    # Load the filtered data into a Delta table
    table_name = "yellow_taxi"
    filtered_df.write.format("delta").mode("append").saveAsTable(table_name)
    
    # Display results
    display(filtered_df.limit(1))
    ```

1. Selecione **&#9655; Executar Célula** ao lado da célula do código.

    - Isso adicionará uma coluna de carimbo de data/hora **dataload_datetime** para registrar em log quando os dados foram carregados em uma tabela Delta
    - Filtrar valores nulos em **storeAndFwdFlag**
    - Carregar os dados filtrados em uma tabela Delta
    - Exibir uma única linha para validação

1. Revise e confirme os resultados exibidos, algo semelhante à imagem a seguir:

    ![Captura de tela da saída bem-sucedida exibindo uma única linha](Images/notebook-transform-result.png)

Agora você se conectou com êxito a dados externos, gravou-os em um arquivo parquet, carregou os dados em um DataFrame, transformou os dados e os carregou em uma tabela Delta.

## Analisar os dados da tabela Delta com consultas SQL

Este laboratório se concentra na ingestão de dados, o que realmente explica o processo *extrair, transformar, carregar*, mas também é importante que você faça uma prévia dos dados.

1. Crie uma nova célula de código e insira o código abaixo:

    ```python
    # Load table into df
    delta_table_name = "yellow_taxi"
    table_df = spark.read.format("delta").table(delta_table_name)
    
    # Create temp SQL table
    table_df.createOrReplaceTempView("yellow_taxi_temp")
    
    # SQL Query
    table_df = spark.sql('SELECT * FROM yellow_taxi_temp')
    
    # Display 10 results
    display(table_df.limit(10))
    ```

1. Selecione **&#9655; Executar Célula** ao lado da célula do código.

     Muitos analistas de dados se sentem à vontade em trabalhar com a sintaxe SQL. O Spark SQL é uma API de linguagem SQL no Spark que você pode usar para executar instruções SQL ou até mesmo persistir dados em tabelas relacionais.

   O código que você acabou de executar cria uma *exibição* relacional dos dados em um dataframe e, em seguida, usa a biblioteca **spark.sql** para inserir a sintaxe do Spark SQL em seu código Python, consultando a exibição e retornando os resultados como um dataframe.

## Limpar os recursos

Neste exercício, você usou notebooks com o PySpark no Fabric para carregar dados e salvá-los no Parquet. Em seguida, você usou esse arquivo Parquet para transformar ainda mais os dados. Por fim, você usou o SQL para consultar as tabelas Delta.

Quando concluir a exploração, você poderá excluir o espaço de trabalho que criou nesse exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
