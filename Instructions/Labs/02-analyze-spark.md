---
lab:
  title: Analisar dados com Apache Spark
  module: Use Apache Spark to work with files in a lakehouse
---

# Analisar dados com Apache Spark

O Apache Spark é um mecanismo de código aberto para processamento de dados distribuído e é amplamente usado para explorar, processar e analisar grandes volumes de dados no data lake storage. O Spark está disponível como uma opção de processamento em vários produtos de plataforma de dados, incluindo o Azure HDInsight, o Azure Databricks, o Azure Synapse Analytics e o Microsoft Fabric. Um dos benefícios do Spark é o suporte a uma ampla variedade de linguagens de programação, incluindo Java, Scala, Python e SQL, tornando o Spark uma solução muito flexível para cargas de trabalho de processamento de dados, incluindo limpeza e processamento de dados, análise estatística e machine learning, análise e visualização de dados.

Este laboratório levará aproximadamente **45** minutos para ser concluído.

> **Observação**: você precisa de uma conta Microsoft de *estudante* ou *corporativa* para concluir este exercício. Caso não tenha uma, [inscreva-se em uma avaliação do Microsoft Office 365 E3 ou superior](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Na [página inicial do Microsoft Fabric](https://app.fabric.microsoft.com) no `https://app.fabric.microsoft.com`, selecione **Engenharia de Dados do Synapse**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha, selecionando um modo de licenciamento na seção **Avançado** que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um lakehouse e carregar arquivos

Agora que você tem um espaço de trabalho, é hora de criar um data lakehouse para os arquivos de dados que serão analisados.

1. Na home page da **Engenharia de Dados do Synapse**, crie um **Lakehouse** com um nome de sua escolha.

    Após alguns minutos, um lakehouse vazio será criado. Você precisa ingerir alguns dados no data lakehouse para análise. Há várias maneiras de fazer isso, mas neste exercício, você apenas baixará e extrairá uma pasta de arquivos de texto no computador local (ou na VM de laboratório, se aplicável) e os carregará no lakehouse.

1. Baixe e extraia os [arquivos de dados](https://github.com/MicrosoftLearning/dp-data/raw/main/orders.zip) deste exercício em `https://github.com/MicrosoftLearning/dp-data/raw/main/orders.zip`.

1. Depois de extrair o arquivo compactado, verifique se você tem uma pasta chamada **orders** que contém arquivos CSV chamados **2019.csv**, **2020.csv** e **2021.csv**.
1. Volte à guia do navegador da Web que contém o lakehouse e, no menu **…** da pasta **Arquivos** no painel do **Explorer**, selecione **Carregar** e **Carregar pasta** e carregue a pasta **orders** do computador local (ou da VM de laboratório, se aplicável) para o lakehouse.
1. Depois que os arquivos forem carregados, expanda **Arquivos**, selecione a pasta **orders** e verifique se os arquivos CSV foram carregados, conforme mostrado aqui:

    ![Captura de tela dos arquivos carregados em um lakehouse.](./Images/uploaded-files.png)

## Criar um notebook

Para trabalhar com os dados no Apache Spark, você pode criar um *notebook*. Os notebooks fornecem um ambiente interativo no qual você pode escrever e executar o código (em várias linguagens) e adicionar anotações para documentá-lo.

1. Na **Home page**, ao exibir o conteúdo da pasta **orders** no data lake, no menu **Abrir notebook**, selecione **Novo notebook**.

    Após alguns segundos, um novo notebook que contém uma só *célula* será aberto. Os notebooks são compostos por uma ou mais células que podem conter um *código* ou um *markdown* (texto formatado).

2. Selecione a primeira célula (que atualmente é uma célula de *código*) e na barra de ferramentas dinâmica no canto superior direito, use o botão **M&#8595;** para converter a célula em uma célula *markdown*.

    Quando a célula for alterada para uma célula markdown, o texto que ela contém será renderizado.

3. Use o botão **&#128393;** (Editar) para alternar a célula para o modo de edição e modifique o markdown da seguinte maneira:

    ```
   # Sales order data exploration

   Use the code in this notebook to explore sales order data.
    ```

4. Clique em qualquer lugar no notebook fora da célula para parar de editá-lo e ver o markdown renderizado.

## Carregar dados em um dataframe

Agora você está pronto para executar o código que carrega os dados em um *dataframe*. Os dataframes no Spark são semelhantes aos dataframes do Pandas no Python e fornecem uma estrutura comum para trabalhar com os dados em linhas e colunas.

> **Observação**: o Spark dá suporte a várias linguagens de codificação, incluindo Scala, Java e outras. Neste exercício, usaremos o *PySpark*, que é uma variante otimizada para Spark do Python. O PySpark é uma das linguagens mais usadas no Spark e é a linguagem padrão nos notebooks do Fabric.

1. Com o notebook visível, no painel do **Explorer** expanda **Lakehouses** e, a seguir, expanda a lista **Arquivos** para o seu lakehouse e selecione a pasta **Pedidos** para que os arquivos CSV sejam listados ao lado do editor do notebook, assim:

    ![Captura de tela de um notebook com um painel Arquivos.](./Images/notebook-files.png)

1. No menu **…** de **2019.csv**, selecione **Carregar dados** > **Spark**. Uma nova célula de código que contém o seguinte código deve ser adicionada ao notebook:

    ```python
   df = spark.read.format("csv").option("header","true").load("Files/orders/2019.csv")
   # df now is a Spark DataFrame containing CSV data from "Files/orders/2019.csv".
   display(df)
    ```

    > **Dica**: oculte os painéis do Lakehouse Explorer à esquerda usando os ícones **<<** . Isso ajudará você a se concentrar no notebook.

1. Use o botão **&#9655; Executar célula** à esquerda da célula para executá-la.

    > **Observação**: como esta é a primeira vez que você executa qualquer código Spark, uma sessão do Spark precisa ser iniciada. Isso significa que a primeira execução na sessão pode levar um minuto para ser concluída. As execuções seguintes serão mais rápidas.

1. Quando o comando de célula for concluído, analise a saída abaixo da célula, que deve ser semelhante a essa:

    | Índice | SO43701 | 11 | 2019-07-01 | Christy Zhu | christy12@adventure-works.com | Mountain-100 Silver, 44 | 16 | 3399.99 | 271.9992 |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | 1 | SO43704 | 1 | 2019-07-01 | Julio Ruiz | julio1@adventure-works.com | Mountain-100 Black, 48 | 1 | 3374.99 | 269.9992 |
    | 2 | SO43705 | 1 | 2019-07-01 | Curtis Lu | curtis9@adventure-works.com | Mountain-100 Silver, 38 | 1 | 3399.99 | 271.9992 |
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    A saída mostra as linhas e as colunas de dados do arquivo 2019.csv. No entanto, observe que os cabeçalhos de coluna não parecem corretos. O código padrão usado para carregar os dados em um dataframe pressupõe que o arquivo CSV inclua os nomes de coluna na primeira linha, mas nesse caso o arquivo CSV inclui apenas os dados sem informações de cabeçalho.

1. Modifique o código para definir a opção **cabeçalho** como **false** da seguinte maneira:

    ```python
   df = spark.read.format("csv").option("header","false").load("Files/orders/2019.csv")
   # df now is a Spark DataFrame containing CSV data from "Files/orders/2019.csv".
   display(df)
    ```

1. Execute a célula novamente e analise a saída, que deve ser semelhante à esta:

   | Índice | _c0 | _c1 | _c2 | _c3 | _c4 | _c5 | _c6 | _c7 | _c8 |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | 1 | SO43701 | 11 | 2019-07-01 | Christy Zhu | christy12@adventure-works.com | Mountain-100 Silver, 44 | 16 | 3399.99 | 271.9992 |
    | 2 | SO43704 | 1 | 2019-07-01 | Julio Ruiz | julio1@adventure-works.com | Mountain-100 Black, 48 | 1 | 3374.99 | 269.9992 |
    | 3 | SO43705 | 1 | 2019-07-01 | Curtis Lu | curtis9@adventure-works.com | Mountain-100 Silver, 38 | 1 | 3399.99 | 271.9992 |
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    Agora, o dataframe inclui corretamente a primeira linha como valores de dados, mas os nomes de colunas são gerados automaticamente e não são muito úteis. Para entender os dados, você precisa definir explicitamente o esquema e o tipo de dados corretos para os valores de dados no arquivo.

1. Modifique o código da seguinte maneira para definir um esquema e aplicá-lo ao carregar os dados:

    ```python
   from pyspark.sql.types import *

   orderSchema = StructType([
       StructField("SalesOrderNumber", StringType()),
       StructField("SalesOrderLineNumber", IntegerType()),
       StructField("OrderDate", DateType()),
       StructField("CustomerName", StringType()),
       StructField("Email", StringType()),
       StructField("Item", StringType()),
       StructField("Quantity", IntegerType()),
       StructField("UnitPrice", FloatType()),
       StructField("Tax", FloatType())
       ])

   df = spark.read.format("csv").schema(orderSchema).load("Files/orders/2019.csv")
   display(df)
    ```

1. Execute a célula modificada e analise a saída, que deve ser semelhante à esta:

   | Índice | SalesOrderNumber | SalesOrderLineNumber | OrderDate | CustomerName | Email | Item | Quantidade | UnitPrice | Imposto |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | 1 | SO43701 | 11 | 2019-07-01 | Christy Zhu | christy12@adventure-works.com | Mountain-100 Silver, 44 | 16 | 3399.99 | 271.9992 |
    | 2 | SO43704 | 1 | 2019-07-01 | Julio Ruiz | julio1@adventure-works.com | Mountain-100 Black, 48 | 1 | 3374.99 | 269.9992 |
    | 3 | SO43705 | 1 | 2019-07-01 | Curtis Lu | curtis9@adventure-works.com | Mountain-100 Silver, 38 | 1 | 3399.99 | 271.9992 |
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    Agora, o dataframe inclui os nomes de colunas corretos (além do **Índice**, que é uma coluna interna em todos os dataframes com base na posição ordinal de cada linha). Os tipos de dados das colunas são especificados por meio de um conjunto padrão de tipos definidos na biblioteca do Spark SQL, que foram importados no início da célula.

1. O dataframe só inclui os dados do arquivo **2019.csv**. Modifique o código para que o caminho do arquivo use um curinga \* para ler os dados do pedido de vendas de todos os arquivos da pasta **orders**:

    ```python
    from pyspark.sql.types import *

    orderSchema = StructType([
       StructField("SalesOrderNumber", StringType()),
       StructField("SalesOrderLineNumber", IntegerType()),
       StructField("OrderDate", DateType()),
       StructField("CustomerName", StringType()),
       StructField("Email", StringType()),
       StructField("Item", StringType()),
       StructField("Quantity", IntegerType()),
       StructField("UnitPrice", FloatType()),
       StructField("Tax", FloatType())
       ])

    df = spark.read.format("csv").schema(orderSchema).load("Files/orders/*.csv")
    display(df)
    ```

1. Execute a célula de código modificada e analise a saída, que agora incluirá as vendas de 2019, 2020 e 2021.

    **Observação**: somente um subconjunto das linhas é exibido, ou seja, talvez você não consiga ver exemplos de todos os anos.

## Explorar os dados em um dataframe

O objeto de dataframe inclui uma ampla variedade de funções que você pode usar para filtrar, agrupar e processar os dados que ele contém.

### Filtrar um dataframe

1. Adicione uma nova célula de código usando o link **+ Código** que aparece ao mover o cursos sob o lado esquerdo da saída da célula atual (ou na barra de menu, na guia **Editar**, selecione **+ Adicionar célula de código**). A seguir insira nele o seguinte código:

    ```Python
   customers = df['CustomerName', 'Email']
   print(customers.count())
   print(customers.distinct().count())
   display(customers.distinct())
    ```

2. Execute a nova célula de código e analise os resultados. Observe os seguintes detalhes:
    - Quando você executa uma operação em um dataframe, o resultado é um novo dataframe (nesse caso, um dataframe **customers** é criado pela seleção de um subconjunto específico de colunas do dataframe **df**)
    - Os dataframes fornecem funções como **count** e **distinct** que podem ser usadas para resumir e filtrar os dados que eles contêm.
    - A sintaxe `dataframe['Field1', 'Field2', ...]` é uma forma abreviada de definir um subconjunto de colunas. Você também pode usar o método **select**, para que a primeira linha do código acima possa ser escrita como `customers = df.select("CustomerName", "Email")`

3. Modifique o código da seguinte maneira:

    ```Python
   customers = df.select("CustomerName", "Email").where(df['Item']=='Road-250 Red, 52')
   print(customers.count())
   print(customers.distinct().count())
   display(customers.distinct())
    ```

4. Execute o código modificado para visualizar os clientes que compraram o produto *Road-250 Red, 52*. Observe que você pode "encadear" várias funções para que a saída de uma função se torne a entrada da próxima. Nesse caso, o dataframe criado pelo método **select** é o dataframe de origem do método **where** usado para aplicar os critérios de filtragem.

### Agregar e agrupar dados em um dataframe

1. Adicione uma nova célula de código ao notebook e insira o seguinte código nela:

    ```Python
   productSales = df.select("Item", "Quantity").groupBy("Item").sum()
   display(productSales)
    ```

2. Execute a célula de código que você adicionou e observe que os resultados mostram a soma das quantidades de pedidos agrupadas por produto. O método **groupBy** agrupa as linhas por *Item*, e a função de agregação de **soma** seguinte é aplicada a todas as colunas numéricas restantes (nesse caso, *Quantidade*)

3. Adicione outra nova célula de código ao notebook e insira o seguinte código nela:

    ```Python
   from pyspark.sql.functions import *

   yearlySales = df.select(year(col("OrderDate")).alias("Year")).groupBy("Year").count().orderBy("Year")
   display(yearlySales)
    ```

4. Execute a célula de código que você adicionou e observe que os resultados mostram o número de pedidos de vendas por ano. Observe que o método **select** inclui uma função SQL **year** para extrair o componente de ano do campo *OrderDate* (razão pela qual o código inclui uma instrução **import** para importar funções da biblioteca do Spark SQL). Em seguida, ele usa um método de **alias** para atribuir um nome de coluna ao valor de ano extraído. Em seguida, os dados são agrupados pela coluna *Year* derivada, e a contagem de linhas em cada grupo é calculada antes de finalmente o método **orderBy** ser usado para classificar o dataframe resultante.

## Usar o Spark para transformar arquivos de dados

Uma tarefa comum para engenheiros de dados é ingerir os dados em uma estrutura ou em um formato específico e transformá-los para processamento ou análise downstream adicionais.

### Usar métodos e funções de dataframe para transformar dados

1. Adicione outra nova célula de código ao notebook e insira o seguinte código nela:

    ```Python
   from pyspark.sql.functions import *

   ## Create Year and Month columns
   transformed_df = df.withColumn("Year", year(col("OrderDate"))).withColumn("Month", month(col("OrderDate")))

   # Create the new FirstName and LastName fields
   transformed_df = transformed_df.withColumn("FirstName", split(col("CustomerName"), " ").getItem(0)).withColumn("LastName", split(col("CustomerName"), " ").getItem(1))

   # Filter and reorder columns
   transformed_df = transformed_df["SalesOrderNumber", "SalesOrderLineNumber", "OrderDate", "Year", "Month", "FirstName", "LastName", "Email", "Item", "Quantity", "UnitPrice", "Tax"]

   # Display the first five orders
   display(transformed_df.limit(5))
    ```

2. Execute o código para criar um dataframe com base nos dados do pedido original com as seguintes transformações:
    - Adicione as colunas **Year** e **Month** com base na coluna **OrderDate**.
    - Adicione as colunas **FirstName** e **LastName** com base na coluna **CustomerName**.
    - Filtre e reordene as colunas, removendo a coluna **CustomerName**.

3. Analise a saída e verifique se as transformações foram feitas nos dados.

    Use todo o potencial da biblioteca do Spark SQL para transformar os dados filtrando linhas, derivando, removendo, renomeando colunas e aplicando outras modificações de dados necessárias.

    > **Dica**: confira a [documentação do dataframe do Spark](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html) para saber mais sobre os métodos do objeto Dataframe.

### Salvar os dados transformados

1. Adicione uma nova célula com o seguinte código para salvar o dataframe transformado no formato Parquet (substituindo os dados se eles já existirem):

    ```Python
   transformed_df.write.mode("overwrite").parquet('Files/transformed_data/orders')
   print ("Transformed data saved!")
    ```

    > **Observação**: normalmente, o formato *Parquet* é preferencial para os arquivos de dados que você usará para análise ou ingestão posterior em um repositório analítico. O Parquet é um formato muito eficiente que é compatível com a maioria dos sistemas de análise de dados em grande escala. Na verdade, às vezes, seu requisito de transformação de dados pode ser apenas converter dados de outro formato (como CSV) em Parquet.

2. Execute a célula e aguarde a mensagem indicando que os dados foram salvos. Em seguida, no painel **Lakehouses** do lado esquerdo, no menu **…** do nó **Arquivos**, selecione **Atualizar** e selecione a pasta **Pedidos transformados** para verificar se contém uma nova pasta chamada **pedidos**, que, por sua vez, contém um ou mais arquivos Parquet.

    ![Captura de tela de uma pasta que contém os arquivos Parquet.](./Images/saved-parquet.png)

3. Adicione uma nova célula com o seguinte código para carregar um novo dataframe dos arquivos Parquet na pasta **transformed_orders/orders**:

    ```Python
   orders_df = spark.read.format("parquet").load("Files/transformed_data/orders")
   display(orders_df)
    ```

4. Execute a célula e verifique se os resultados mostram os dados do pedido que foram carregados dos arquivos Parquet.

### Salvar os dados em arquivos particionados

1. Adicione uma nova célula com o código a seguir, que salva o dataframe, particionando os dados por **Year** e **Month**:

    ```Python
   orders_df.write.partitionBy("Year","Month").mode("overwrite").parquet("Files/partitioned_data")
   print ("Transformed data saved!")
    ```

2. Execute a célula e aguarde a mensagem indicando que os dados foram salvos. Em seguida, no painel **Lakehouses** do lado esquerdo, no menu **…** do nó **Arquivos**, selecione **Atualizar**e expanda a pasta **Pedidos particionados** para verificar se contém uma hierarquia de pastas chamada **Ano=* xxxx***, cada uma contendo pastas chamadas **Mês=* xxxx***. Cada pasta mensal contém um arquivo Parquet com os pedidos desse mês.

    ![Captura de tela de uma hierarquia de arquivos de dados particionados.](./Images/partitioned-files.png)

    Particionar arquivos de dados é uma forma comum de otimizar o desempenho ao lidar com grandes volumes de dados. Essa técnica pode aprimorar consideravelmente o desempenho e facilitar a filtragem de dados.

3. Adicione uma nova célula com o seguinte código para carregar um novo dataframe por meio do arquivo **orders.parquet**:

    ```Python
   orders_2021_df = spark.read.format("parquet").load("Files/partitioned_data/Year=2021/Month=*")
   display(orders_2021_df)
    ```

4. Execute a célula e verifique se os resultados mostram os dados do pedido de vendas em 2021. Observe que as colunas de particionamento especificadas no caminho (**Year** e **Month**) não estão incluídas no dataframe.

## Trabalhar com tabelas e o SQL

Como você viu, os métodos nativos do objeto de dataframe permitem que você consulte e analise os dados de um arquivo com bastante eficiência. No entanto, muitos analistas de dados se sentem mais à vontade em trabalhar com tabelas que eles podem consultar usando a sintaxe SQL. O Spark fornece um *metastore* no qual você pode definir tabelas relacionais. A biblioteca do Spark SQL que fornece o objeto de dataframe também dá suporte ao uso de instruções SQL para consultar as tabelas no metastore. Usando essas funcionalidades do Spark, você pode combinar a flexibilidade de um data lake com o esquema de dados estruturado e as consultas baseadas em SQL de um data warehouse relacional, daí o termo "data lakehouse".

### Criar uma tabela

As tabelas em um metastore do Spark são abstrações relacionais em arquivos no data lake. As tabelas podem ser *gerenciadas* (nesse caso, os arquivos são gerenciados pelo metastore) ou *externas* (nesse caso, a tabela referencia um local de arquivo no data lake que você gerencia independentemente do metastore).

1. Adicione uma nova célula de código ao notebook e insira o seguinte código, que salva o dataframe dos dados do pedido de vendas como uma tabela chamada **salesorders**:

    ```Python
   # Create a new table
   df.write.format("delta").saveAsTable("salesorders")

   # Get the table description
   spark.sql("DESCRIBE EXTENDED salesorders").show(truncate=False)
    ```

    > **Observação**: vale a pena observar algumas coisas sobre este exemplo. Em primeiro lugar, nenhum caminho explícito é fornecido, ou seja, os arquivos da tabela serão gerenciados pelo metastore. Em segundo lugar, a tabela é salva no formato **delta**. Você pode criar tabelas com base em vários formatos de arquivo (incluindo CSV, Parquet, Avro e outros), mas o *Delta Lake* é uma tecnologia do Spark que adiciona funcionalidades de banco de dados relacional a tabelas; incluindo suporte para transações, controle de versão de linha e outros recursos úteis. A criação de tabelas no formato delta é preferencial para data lakehouses no Fabric.

2. Execute a célula de código e analise a saída, que descreve a definição da nova tabela.

3. No painel **Lakehouses**, no menu **…** da pasta **Tabelas**, selecione **Atualizar**. Em seguida, expanda o nó **Tabelas** e verifique se a tabela **salesorders** foi criada.

    ![Captura de tela da tabela salesorder no Explorer.](./Images/table-view.png)

5. No menu **…** da tabela **salesorders**, selecione **Carregar dados** > **Spark**.

    Uma nova célula de código que contém um código semelhante ao seguinte exemplo é adicionada ao notebook:

    ```Python
   df = spark.sql("SELECT * FROM [your_lakehouse].salesorders LIMIT 1000")
   display(df)
    ```

6. Execute o novo código, que usa a biblioteca do Spark SQL para inserir uma consulta SQL na tabela **salesorder** no código PySpark e carregar os resultados da consulta em um dataframe.

### Executar um código SQL em uma célula

Embora seja útil a inserção de instruções SQL em uma célula que contém um código PySpark, os analistas de dados costumam desejar apenas trabalhar diretamente no SQL.

1. Adicione uma nova célula de código ao notebook e insira o seguinte código nela:

    ```sql
   %%sql
   SELECT YEAR(OrderDate) AS OrderYear,
          SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue
   FROM salesorders
   GROUP BY YEAR(OrderDate)
   ORDER BY OrderYear;
    ```

2. Execute a célula e analise os resultados. Observe que:
    - A linha `%%sql` no início da célula (chamada *magic*) indica que o runtime da linguagem Spark SQL deve ser usado para executar o código nessa célula em vez do PySpark.
    - O código SQL referencia a tabela **salesorders** que você já criou.
    - A saída da consulta SQL é exibida automaticamente como o resultado abaixo da célula.

> **Observação**: para obter mais informações sobre o Spark SQL e os dataframes, confira a [documentação do Spark SQL](https://spark.apache.org/docs/2.2.0/sql-programming-guide.html).

## Visualizar os dados com o Spark

Como o provérbio diz, uma imagem vale mil palavras, e um gráfico geralmente é melhor do que mil linhas de dados. Embora os notebooks do Fabric incluam uma exibição de gráfico interna para dados exibidos em um dataframe ou em uma consulta Spark SQL, ele não foi projetado para gráficos abrangentes. No entanto, você pode usar bibliotecas de elementos gráficos do Python, como a **matplotlib** e a **seaborn**, para criar gráficos com base em dados em dataframes.

### Exibir os resultados como um gráfico

1. Adicione uma nova célula de código ao notebook e insira o seguinte código nela:

    ```sql
   %%sql
   SELECT * FROM salesorders
    ```

2. Execute o código e observe que ele retorna os dados da exibição **salesorders** que você já criou.
3. Na seção de resultados abaixo da célula, altere a opção **Exibir** de **Tabela** para **Gráfico**.
4. Use o botão **Personalizar gráfico** no canto superior direito do gráfico para exibir o painel de opções do gráfico. Em seguida, defina as opções da seguinte maneira e selecione **Aplicar**:
    - **Tipo de gráfico**: Gráfico de barras
    - **Chave**: Item
    - **Valores**: Quantidade
    - **Grupo de Séries**: *deixe em branco*
    - **Agregação**: Soma
    - **Empilhado**: *Não selecionado*

5. Verifique se o gráfico é parecido com este:

    ![Captura de tela de um gráfico de barras de produtos pela quantidade total de pedidos](./Images/notebook-chart.png)

### Introdução à **matplotlib**

1. Adicione uma nova célula de código ao notebook e insira o seguinte código nela:

    ```Python
   sqlQuery = "SELECT CAST(YEAR(OrderDate) AS CHAR(4)) AS OrderYear, \
                   SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue \
               FROM salesorders \
               GROUP BY CAST(YEAR(OrderDate) AS CHAR(4)) \
               ORDER BY OrderYear"
   df_spark = spark.sql(sqlQuery)
   df_spark.show()
    ```

2. Execute o código e observe se ele retorna um dataframe do Spark que contém a receita anual.

    Para visualizar os dados como um gráfico, começaremos usando a biblioteca **matplotlib** do Python. Essa biblioteca é a biblioteca de plotagem principal na qual muitas outras se baseiam e fornece muita flexibilidade na criação de gráficos.

3. Adicione uma nova célula de código ao notebook e adicione o seguinte código a ele:

    ```Python
   from matplotlib import pyplot as plt

   # matplotlib requires a Pandas dataframe, not a Spark one
   df_sales = df_spark.toPandas()

   # Create a bar plot of revenue by year
   plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'])

   # Display the plot
   plt.show()
    ```

4. Execute a célula e analise os resultados, que consistem em um gráfico de colunas com a receita bruta total de cada ano. Observe os seguintes recursos do código usado para produzir este gráfico:
    - A biblioteca **matplotlib** exige um dataframe do *Pandas*, ou seja, você precisa converter o dataframe do *Spark* retornado pela consulta Spark SQL nesse formato.
    - No núcleo da biblioteca **matplotlib** está o objeto **pyplot**. Essa é a base para a maioria das funcionalidades de plotagem.
    - As configurações padrão resultam em um gráfico utilizável, mas há um escopo considerável para personalizá-lo

5. Modifique o código para plotar o gráfico da seguinte maneira:

    ```Python
   from matplotlib import pyplot as plt

   # Clear the plot area
   plt.clf()

   # Create a bar plot of revenue by year
   plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')

   # Customize the chart
   plt.title('Revenue by Year')
   plt.xlabel('Year')
   plt.ylabel('Revenue')
   plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
   plt.xticks(rotation=45)

   # Show the figure
   plt.show()
    ```

6. Execute novamente a célula de código e veja os resultados. O gráfico agora inclui um pouco mais de informações.

    Tecnicamente, um gráfico está contido com uma **Figura**. Nos exemplos anteriores, a figura foi criada implicitamente, mas você pode criá-la de modo explícito.

7. Modifique o código para plotar o gráfico da seguinte maneira:

    ```Python
   from matplotlib import pyplot as plt

   # Clear the plot area
   plt.clf()

   # Create a Figure
   fig = plt.figure(figsize=(8,3))

   # Create a bar plot of revenue by year
   plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')

   # Customize the chart
   plt.title('Revenue by Year')
   plt.xlabel('Year')
   plt.ylabel('Revenue')
   plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
   plt.xticks(rotation=45)

   # Show the figure
   plt.show()
    ```

8. Execute novamente a célula de código e veja os resultados. A figura determina a forma e o tamanho do gráfico.

    Uma figura pode conter vários subgráficos, cada um em um *eixo* próprio.

9. Modifique o código para plotar o gráfico da seguinte maneira:

    ```Python
   from matplotlib import pyplot as plt

   # Clear the plot area
   plt.clf()

   # Create a figure for 2 subplots (1 row, 2 columns)
   fig, ax = plt.subplots(1, 2, figsize = (10,4))

   # Create a bar plot of revenue by year on the first axis
   ax[0].bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')
   ax[0].set_title('Revenue by Year')

   # Create a pie chart of yearly order counts on the second axis
   yearly_counts = df_sales['OrderYear'].value_counts()
   ax[1].pie(yearly_counts)
   ax[1].set_title('Orders per Year')
   ax[1].legend(yearly_counts.keys().tolist())

   # Add a title to the Figure
   fig.suptitle('Sales Data')

   # Show the figure
   plt.show()
    ```

10. Execute novamente a célula de código e veja os resultados. A figura contém os subgráficos especificados no código.

> **Observação**: para saber mais sobre a plotagem com a matplotlib, confira a [documentação da matplotlib](https://matplotlib.org/).

### Usar a biblioteca **seaborn**

Embora a **matplotlib** permita que você crie gráficos complexos de vários tipos, ele pode exigir um código complexo para obter os melhores resultados. Por esse motivo, ao longo dos anos, muitas bibliotecas foram criadas na base na matplotlib para abstrair a complexidade e aprimorar as funcionalidades. Uma dessas bibliotecas é a **seaborn**.

1. Adicione uma nova célula de código ao notebook e insira o seguinte código nela:

    ```Python
   import seaborn as sns

   # Clear the plot area
   plt.clf()

   # Create a bar chart
   ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
   plt.show()
    ```

2. Execute o código e observe que ele exibe um gráfico de barras usando a biblioteca seaborn.
3. Modifique o código da seguinte maneira:

    ```Python
   import seaborn as sns

   # Clear the plot area
   plt.clf()

   # Set the visual theme for seaborn
   sns.set_theme(style="whitegrid")

   # Create a bar chart
   ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
   plt.show()
    ```

4. Execute o código modificado e observe que a seaborn permite que você defina um tema de cor consistente para seus gráficos.

5. Modifique o código novamente da seguinte maneira:

    ```Python
   import seaborn as sns

   # Clear the plot area
   plt.clf()

   # Create a line chart
   ax = sns.lineplot(x="OrderYear", y="GrossRevenue", data=df_sales)
   plt.show()
    ```

6. Execute o código modificado para ver a receita anual como um gráfico de linhas.

> **Observação**: para saber mais sobre como fazer uma plotagem com a seaborn, confira a [documentação da seaborn](https://seaborn.pydata.org/index.html).

## Salvar o notebook e encerrar a sessão do Spark

Agora que terminou de trabalhar com os dados, salve o notebook com um nome significativo e encerre a sessão do Spark.

1. Na barra de menus do notebook, use o ícone ⚙️ de **Configurações** para ver as configurações do notebook.
2. Defina o **Nome** do notebook como **Explorar Pedidos de Vendas** e feche o painel de configurações.
3. No menu do notebook, selecione **Parar sessão** para encerrar a sessão do Spark.

## Limpar os recursos

Neste exercício, você aprendeu a usar o Spark para trabalhar com os dados no Microsoft Fabric.

Se você tiver terminado de explorar seu lakehouse, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Outros**, selecione **Remover este workspace**.
