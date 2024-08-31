---
lab:
  title: Usar tabelas Delta no Apache Spark
  module: Work with Delta Lake tables in Microsoft Fabric
---

# Usar tabelas Delta no Apache Spark

As tabelas de um lakehouse do Microsoft Fabric são baseadas no formato *Delta Lake* de código aberto para o Apache Spark. O Delta Lake adiciona suporte para a semântica relacional em operações de dados em lote e streaming e permite a criação de uma arquitetura de Lakehouse na qual o Apache Spark pode ser usado para processar e consultar dados em tabelas baseadas em arquivos subjacentes em um data lake.

Este exercício levará aproximadamente **40** minutos para ser concluído

> **Observação**: Você precisará de uma [avaliação do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir esse exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [página inicial do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric) no `https://app.fabric.microsoft.com/home?experience=fabric`, selecione **Engenharia de Dados do Synapse**.
2. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
3. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
4. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um lakehouse e carregar dados

Agora que você tem um espaço de trabalho, é hora de criar um data lakehouse para os dados que serão analisados.

1. Na home page da **Engenharia de Dados do Synapse**, crie um **Lakehouse** com um nome de sua escolha.

    Após alguns minutos, um lakehouse vazio. Você precisa ingerir alguns dados no data lakehouse para análise. Há várias maneiras de fazer isso, mas neste exercício, você apenas baixará um arquivo de texto no computador local (ou na VM de laboratório, se aplicável) e o carregará no lakehouse.

1. Baixe o [arquivo de dados](https://github.com/MicrosoftLearning/dp-data/raw/main/products.csv) para este exercício em `https://github.com/MicrosoftLearning/dp-data/raw/main/products.csv` e salve-o como **products.csv** no computador local (ou na VM de laboratório, se aplicável).

1. Volte à guia do navegador da Web que contém o lakehouse e, no menu **…** da pasta **Arquivos** no painel do **Explorer**, selecione **Nova subpasta** e crie uma pasta chamada **products**.

1. No menu **…** da pasta **products**, selecione **Carregar** e **Carregar arquivos** e carregue o arquivo **products.csv** do computador local (ou da VM de laboratório, se aplicável) para o lakehouse.
1. Depois que o arquivo for carregado, selecione a pasta **products** e verifique se o arquivo **products.csv** foi carregado, conforme mostrado aqui:

    ![Captura de tela do arquivo products.csv carregado em um lakehouse.](./Images/products-file.png)

## Explorar os dados em um dataframe

1. Na **Home page**, ao exibir o conteúdo da pasta **products** no data lake, no menu **Abrir notebook**, selecione **Novo notebook**.

    Após alguns segundos, um novo notebook que contém uma só *célula* será aberto. Os notebooks são compostos por uma ou mais células que podem conter um *código* ou um *markdown* (texto formatado).

2. Selecione a célula existente no notebook, que contém um código simples e use o ícone **&#128465;** (*Excluir*) dele no canto superior direito para removê-lo. Você não precisará desse código.
3. No painel do **Explorer**, expanda **Lakehouses** e, em seguida, expanda a lista de **Arquivos** do seu lakehouse e selecione a pasta **produtos** para revelar um novo painel mostrando o arquivo **products.csv** que você já carregou:

    ![Captura de tela de um notebook com um painel Arquivos.](./Images/notebook-products.png)

4. No menu **…** de **products.csv**, selecione **Carregar dados** > **Spark**. Uma nova célula de código que contém o seguinte código deve ser adicionada ao notebook:

    ```python
   df = spark.read.format("csv").option("header","true").load("Files/products/products.csv")
   # df now is a Spark DataFrame containing CSV data from "Files/products/products.csv".
   display(df)
    ```

    > **Dica**: oculte o painel que contém os arquivos à esquerda usando o ícone **<<** . Isso ajudará você a se concentrar no notebook.

5. Use o botão **&#9655;** (*Executar célula*) à esquerda da célula para executá-la.

    > **Observação**: como esta é a primeira vez que você executa qualquer código Spark neste notebook, uma sessão do Spark precisa ser iniciada. Isso significa que a primeira execução pode levar alguns minutos para ser concluída. As execuções seguintes serão mais rápidas.

6. Quando o comando de célula for concluído, analise a saída abaixo da célula, que deve ser semelhante a essa:

    | Índice | ProductID | ProductName | Categoria | ListPrice |
    | -- | -- | -- | -- | -- |
    | 1 | 771 | Mountain-100 Silver, 38 | Mountain bikes | 3399.9900 |
    | 2 | 772 | Mountain-100 Silver, 42 | Mountain bikes | 3399.9900 |
    | 3 | 773 | Mountain-100 Silver, 44 | Mountain bikes | 3399.9900 |
    | ... | ... | ... | ... | ... |

## Criar tabelas Delta

Você pode salvar o dataframe como uma tabela delta usando o método `saveAsTable`. O Delta Lake dá suporte à criação de tabelas *gerenciadas* e *externas*.

### Criar uma tabela *gerenciada*

Tabelas *gerenciadas* são tabelas para as quais os metadados de esquema e os arquivos de dados são gerenciados pelo Fabric. Os arquivos de dados da tabela são criados na pasta **Tabelas**.

1. Nos resultados retornados pela primeira célula de código, use o ícone **+ Código** para adicionar uma nova célula de código caso ainda não exista uma.

    > **Dica**: Para ver o ícone **+ Código**, posicione o mouse um pouco abaixo e à esquerda da saída da célula atual. Como alternativa, na barra de menus, na guia **Editar**, selecione **+ Adicionar célula de código**.

2. Insira o seguinte código na nova célula e execute-o:

    ```python
   df.write.format("delta").saveAsTable("managed_products")
    ```

3. No painel do **Lakehouse Explorer**, no menu **…** da pasta **Tabelas**, selecione **Atualizar**. Em seguida, expanda o nó **Tabelas** e verifique se a tabela **managed_products** foi criada.

### Criar uma tabela *externa*

Crie também tabelas *externas* para as quais os metadados de esquema são definidos no metastore para o lakehouse, mas os arquivos de dados são armazenados em um local externo.

1. Adicione outra nova célula de código e adicione o seguinte código a ela:

    ```python
   df.write.format("delta").saveAsTable("external_products", path="abfs_path/external_products")
    ```

2. No painel do **Lakehouse Explorer**, no menu **…** da pasta **Arquivos**, selecione **Copiar caminho do ABFS**.

    O caminho do ABFS é o caminho totalmente qualificado para a pasta **Arquivos** no armazenamento OneLake do lakehouse, semelhante a este:

    *abfss://workspace@tenant-onelake.dfs.fabric.microsoft.com/lakehousename.Lakehouse/Files*

3. No código que você inseriu na célula de código, substitua **abfs_path** pelo caminho que você copiou para a área de transferência para que o código salve o dataframe como uma tabela externa com arquivos de dados em uma pasta chamada **external_products** no local da pasta **Files**. O caminho completo será parecido com este:

    *abfss://workspace@tenant-onelake.dfs.fabric.microsoft.com/lakehousename.Lakehouse/Files/external_products*

4. No painel do **Lakehouse Explorer**, no menu **…** da pasta **Tabelas**, selecione **Atualizar**. Em seguida, expanda o nó **Tabelas** e verifique se a tabela **external_products** foi criada.

5. No painel do **Lakehouse Explorer**, no menu **…** da pasta **Arquivos**, selecione **Atualizar**. Em seguida, expanda o nó **Arquivos** e verifique se a pasta **external_products** foi criada para os arquivos de dados da tabela.

### Comparar tabelas *gerenciadas* e *externas*

Vamos explorar as diferenças entre as tabelas gerenciadas e externas.

1. Adicione outra célula de código e execute o seguinte código:

    ```sql
   %%sql

   DESCRIBE FORMATTED managed_products;
    ```

    Nos resultados, veja a propriedade **Location** da tabela, que deve ser um caminho para o armazenamento OneLake do lakehouse que termina com **/Tables/managed_products** (talvez seja necessário ampliar a coluna **Tipo de dados** para ver o caminho completo).

2. Modifique o comando `DESCRIBE` para mostrar os detalhes da tabela **external_products** conforme mostrado aqui:

    ```sql
   %%sql

   DESCRIBE FORMATTED external_products;
    ```

    Nos resultados, veja a propriedade **Location** da tabela, que deve ser um caminho para o armazenamento OneLake do lakehouse que termina com **/Files/external_products** (talvez seja necessário ampliar a coluna **Tipo de dados** para ver o caminho completo).

    Os arquivos da tabela gerenciada são armazenados na pasta **Tabelas** no armazenamento OneLake do lakehouse. Nesse caso, uma pasta chamada **managed_products** foi criada para armazenar os arquivos Parquet e a pasta **delta_log** para a tabela que você criou.

3. Adicione outra célula de código e execute o seguinte código:

    ```sql
   %%sql

   DROP TABLE managed_products;
   DROP TABLE external_products;
    ```

4. No painel do **Lakehouse Explorer**, no menu **…** da pasta **Tabelas**, selecione **Atualizar**. Em seguida, expanda o nó **Tabelas** e verifique se nenhuma tabela está listada.

5. No painel do **Lakehouse Explorer**, expanda a pasta **Arquivos** e verifique se **external_products** não foi excluído. Selecione essa pasta para ver os arquivos de dados Parquet e a pasta **_delta_log** dos dados que estavam anteriormente na tabela **external_products**. Os metadados da tabela externa foram excluídos, mas os arquivos não foram afetados.

### Usar o SQL para criar uma tabela

1. Adicione outra célula de código e execute o seguinte código:

    ```sql
   %%sql

   CREATE TABLE products
   USING DELTA
   LOCATION 'Files/external_products';
    ```

2. No painel do **Lakehouse Explorer**, no menu **…** da pasta **Tabelas**, selecione **Atualizar**. Em seguida, expanda o nó **Tabelas** e verifique se uma nova tabela chamada **products** está listada. Em seguida, expanda a tabela para verificar se o esquema corresponde ao dataframe original que foi salvo na pasta **external_products**.

3. Adicione outra célula de código e execute o seguinte código:

    ```sql
   %%sql

   SELECT * FROM products;
   ```

## Explorar o controle de versão de tabela

O histórico de transações das tabelas delta é armazenado em arquivos JSON na pasta **delta_log**. Você pode usar esse log de transações para gerenciar o controle de versão de dados.

1. Adicione uma nova célula de código ao notebook e execute o seguinte código:

    ```sql
   %%sql

   UPDATE products
   SET ListPrice = ListPrice * 0.9
   WHERE Category = 'Mountain Bikes';
    ```

    Esse código implementa uma redução de 10% no preço das mountain bikes.

2. Adicione outra célula de código e execute o seguinte código:

    ```sql
   %%sql

   DESCRIBE HISTORY products;
    ```

    Os resultados mostram o histórico de transações registradas para a tabela.

3. Adicione outra célula de código e execute o seguinte código:

    ```python
   delta_table_path = 'Files/external_products'

   # Get the current data
   current_data = spark.read.format("delta").load(delta_table_path)
   display(current_data)

   # Get the version 0 data
   original_data = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
   display(original_data)
    ```

    Os resultados mostram dois dataframes: um contendo os dados após a redução de preço e o outro mostrando a versão original dos dados.

## Usar tabelas delta para transmitir dados

O Delta Lake dá suporte a dados de streaming. As tabelas delta podem ser um *coletor* ou uma *fonte* para fluxos de dados criados por meio da API de Streaming Estruturado do Spark. Neste exemplo, você usará uma tabela delta como um coletor para alguns dados de streaming em um cenário simulado de IoT (Internet das Coisas).

1. Adicione uma nova célula de código no notebook. Em seguida, na nova célula, adicione o seguinte código e execute-o:

    ```python
   from notebookutils import mssparkutils
   from pyspark.sql.types import *
   from pyspark.sql.functions import *

   # Create a folder
   inputPath = 'Files/data/'
   mssparkutils.fs.mkdirs(inputPath)

   # Create a stream that reads data from the folder, using a JSON schema
   jsonSchema = StructType([
   StructField("device", StringType(), False),
   StructField("status", StringType(), False)
   ])
   iotstream = spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger", 1).json(inputPath)

   # Write some event data to the folder
   device_data = '''{"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev2","status":"error"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"error"}
   {"device":"Dev2","status":"ok"}
   {"device":"Dev2","status":"error"}
   {"device":"Dev1","status":"ok"}'''
   mssparkutils.fs.put(inputPath + "data.txt", device_data, True)
   print("Source stream created...")
    ```

    Verifique se a mensagem *Fluxo de origem criado…* está impressa. O código que você acabou de executar criou uma fonte de dados de streaming com base em uma pasta na qual alguns dados foram salvos, representando leituras de dispositivos IoT hipotéticos.

2. Em uma nova célula de código, adicione e execute o seguinte código:

    ```python
   # Write the stream to a delta table
   delta_stream_table_path = 'Tables/iotdevicedata'
   checkpointpath = 'Files/delta/checkpoint'
   deltastream = iotstream.writeStream.format("delta").option("checkpointLocation", checkpointpath).start(delta_stream_table_path)
   print("Streaming to delta sink...")
    ```

    Esse código grava os dados do dispositivo de streaming no formato delta em uma pasta chamada **iotdevicedata**. Como o caminho para o local da pasta na pasta **Tabelas**, uma tabela será criada automaticamente para ela.

3. Em uma nova célula de código, adicione e execute o seguinte código:

    ```sql
   %%sql

   SELECT * FROM IotDeviceData;
    ```

    Esse código consulta a tabela **IotDeviceData**, que contém os dados do dispositivo da fonte de streaming.

4. Em uma nova célula de código, adicione e execute o seguinte código:

    ```python
   # Add more data to the source stream
   more_data = '''{"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"ok"}
   {"device":"Dev1","status":"error"}
   {"device":"Dev2","status":"error"}
   {"device":"Dev1","status":"ok"}'''

   mssparkutils.fs.put(inputPath + "more-data.txt", more_data, True)
    ```

    Esse código grava mais dados hipotéticos do dispositivo na fonte de streaming.

5. Execute novamente a célula que contém o seguinte código:

    ```sql
   %%sql

   SELECT * FROM IotDeviceData;
    ```

    Esse código consulta a tabela **IotDeviceData** novamente, que agora incluirá os dados extras que foram adicionados à fonte de streaming.

6. Em uma nova célula de código, adicione e execute o seguinte código:

    ```python
   deltastream.stop()
    ```

    Esse código interrompe o fluxo.

## Limpar os recursos

Neste exercício, você aprendeu a trabalhar com tabelas delta no Microsoft Fabric.

Se você tiver terminado de explorar seu lakehouse, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
