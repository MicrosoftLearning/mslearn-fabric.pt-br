---
lab:
  title: Explorar dados para ciência de dados com notebooks no Microsoft Fabric
  module: Explore data for data science with notebooks in Microsoft Fabric
---

# Explorar dados para ciência de dados com notebooks no Microsoft Fabric

Neste laboratório, usaremos notebooks para exploração de dados. Os notebooks são uma ferramenta poderosa para explorar e analisar dados de forma interativa. Durante este exercício, aprenderemos a criar e utilizar notebooks para explorar um conjunto de dados, gerar estatísticas resumidas e criar visualizações para entender melhor os dados. Ao final deste laboratório, você terá uma sólida compreensão de como utilizar notebooks para explorar e analisar dados.

Este laboratório leva cerca de **30** minutos para ser concluído.

> **Observação**: Você precisa de uma [avaliação do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir esse exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Navegue até a [home page do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric) em `https://app.fabric.microsoft.com/home?experience=fabric` em um navegador e entre com suas credenciais do Fabric.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um notebook

Para treinar um modelo, você pode criar um *notebook*. Os notebooks fornecem um ambiente interativo no qual você pode escrever e executar um código (em várias linguagens) como *experimentos*.

1. Na barra de menus à esquerda, selecione **Criar**. Na página *Novo*, na seção *Ciência de Dados*, selecione **Notebook**. Dê um nome exclusivo de sua preferência.

    >**Observação**: se a opção **Criar** não estiver fixada na barra lateral, você precisará selecionar a opção de reticências (**...**) primeiro.

    Após alguns segundos, um novo notebook que contém uma só *célula* será aberto. Os notebooks são compostos por uma ou mais células que podem conter um *código* ou um *markdown* (texto formatado).

1. Selecione a primeira célula (que atualmente é uma célula de *código*) e na barra de ferramentas dinâmica no canto superior direito, use o botão **M&#8595;** para converter a célula em uma célula *markdown*.

    Quando a célula for alterada para uma célula markdown, o texto que ela contém será renderizado.

1. Se necessário, use o botão **&#128393;** (Editar) para alternar a célula para o modo de edição e, em seguida, exclua o conteúdo e insira o texto a seguir:

    ```text
   # Perform data exploration for data science

   Use the code in this notebook to perform data exploration for data science.
    ```

## Carregar dados em um dataframe

Agora você está pronto para executar o código para obter dados. Você trabalhará com o [**conjunto de dados de diabetes**](https://learn.microsoft.com/azure/open-datasets/dataset-diabetes?tabs=azureml-opendatasets?azure-portal=true) do Azure Open Datasets. Após carregar os dados, você os converterá em um dataframe do Pandas, que é uma estrutura comum para trabalhar com dados em linhas e colunas.

1. No seu notebook, utilize o ícone **+ Código** abaixo da última célula para adicionar uma nova célula de código ao notebook.

    > **Dica**: Para ver o ícone **+ Código**, posicione o mouse um pouco abaixo e à esquerda da saída da célula atual. Como alternativa, na barra de menus, na guia **Editar**, selecione **+ Adicionar célula de código**.

1. Insira o código a seguir para carregar o conjunto de dados em um dataframe.

    ```python
   # Azure storage access info for open dataset diabetes
   blob_account_name = "azureopendatastorage"
   blob_container_name = "mlsamples"
   blob_relative_path = "diabetes"
   blob_sas_token = r"" # Blank since container is Anonymous access
    
   # Set Spark config to access  blob storage
   wasbs_path = f"wasbs://%s@%s.blob.core.windows.net/%s" % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set("fs.azure.sas.%s.%s.blob.core.windows.net" % (blob_container_name, blob_account_name), blob_sas_token)
   print("Remote blob path: " + wasbs_path)
    
   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
    ```

1. Use o botão **&#9655; Executar célula** à esquerda da célula para executá-la. Como alternativa, você pode pressionar **SHIFT** + **ENTER** no teclado para executar uma célula.

    > **Observação**: como esta é a primeira vez que você executa qualquer código Spark nesta sessão, o Pool do Spark precisa ser iniciado. Isso significa que a primeira execução na sessão pode levar um minuto para ser concluída. As execuções seguintes serão mais rápidas.

1. Use o ícone **+ Código** abaixo da saída da célula para adicionar uma nova célula de código ao notebook e insira o seguinte código nela:

    ```python
   display(df)
    ```

1. Quando o comando de célula for concluído, analise a saída abaixo da célula, que deve ser semelhante a essa:

    |IDADE|SEXO|BMI|BP|S1|S2|S3|S4|S5|S6|Y|
    |---|---|---|--|--|--|--|--|--|--|--|
    |59|2|32,1|101.0|157|93,2|38.0|4,0|4,8598|87|151|
    |48|1|21,6|87,0|183|103,2|70.0|3.0|3,8918|69|75|
    |72|2|30,5|93.0|156|93,6|41,0|4,0|4,6728|85|141|
    |24|1|25,3|84.0|198|131,4|49.0|5,0|4,8903|89|206|
    |50|1|23,0|101.0|192|125,4|52,0|4,0|4,2905|80|135|
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    A saída mostra as linhas e colunas do conjunto de dados do diabetes. Os dados consistem em dez variáveis de linha de base, idade, sexo, índice de massa corporal, pressão arterial média e seis medidas de soro do sangue para pacientes com diabetes, bem como a resposta de interesse (uma medida quantitativa da progressão da doença um ano após a linha de base), que é rotulada como **Y**.

1. Os dados são carregados como um DataFrame do Spark. O Scikit-learn esperará que o conjunto de dados de entrada seja um dataframe do Pandas. Execute o código abaixo para converter seu conjunto de dados em um dataframe do Pandas:

    ```python
   df = df.toPandas()
   df.head()
    ```

## Verificar a forma dos dados

Agora que carregou os dados, você pode verificar a estrutura do conjunto de dados, como o número de linhas e colunas, os tipos de dados e os valores ausentes.

1. Use o ícone **+ Código** abaixo da saída da célula para adicionar uma nova célula de código ao notebook e insira o seguinte código nela:

    ```python
   # Display the number of rows and columns in the dataset
   print("Number of rows:", df.shape[0])
   print("Number of columns:", df.shape[1])

   # Display the data types of each column
   print("\nData types of columns:")
   print(df.dtypes)
    ```

    O conjunto de dados contém **442 linhas** e **11 colunas**. Isso significa que você deve ter 442 amostras e 11 recursos ou variáveis no seu conjunto de dados. A variável **SEX** provavelmente contém dados categóricos ou cadeias de caracteres.

## Verifique se há dados ausentes

1. Use o ícone **+ Código** abaixo da saída da célula para adicionar uma nova célula de código ao notebook e insira o seguinte código nela:

    ```python
   missing_values = df.isnull().sum()
   print("\nMissing values per column:")
   print(missing_values)
    ```

    O código verifica se há valores ausentes. Observe que não existem dados ausentes no conjunto de dados.

## Gerar estatísticas descritivas para variáveis numéricas

Agora, vamos gerar estatísticas descritivas para entender a distribuição das variáveis numéricas.

1. Use o ícone **+ Código** abaixo da saída da célula para adicionar uma nova célula de código ao notebook e insira o seguinte código.

    ```python
   df.describe()
    ```

    A idade média é de aproximadamente 48,5 anos, com desvio padrão de 13,1 anos. A pessoa mais jovem tem 19 anos e a mais velha tem 79 anos. A BMI média é aproximadamente 26,4, o que se enquadra na categoria **sobrepeso** de acordo com os [padrões da OMS](https://www.who.int/health-topics/obesity#tab=tab_1). A BMI mínima é 18 e a máxima é 42,2.

## Plotar a distribuição dos dados

Vamos verificar o recurso BMI e plotar sua distribuição para entender melhor suas características.

1. Adicionar outra célula de código ao notebook. Em seguida, insira o seguinte código nessa célula e execute-o.

    ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import numpy as np
    
   # Calculate the mean, median of the BMI variable
   mean = df['BMI'].mean()
   median = df['BMI'].median()
   
   # Histogram of the BMI variable
   plt.figure(figsize=(8, 6))
   plt.hist(df['BMI'], bins=20, color='skyblue', edgecolor='black')
   plt.title('BMI Distribution')
   plt.xlabel('BMI')
   plt.ylabel('Frequency')
    
   # Add lines for the mean and median
   plt.axvline(mean, color='red', linestyle='dashed', linewidth=2, label='Mean')
   plt.axvline(median, color='green', linestyle='dashed', linewidth=2, label='Median')
    
   # Add a legend
   plt.legend()
   plt.show()
    ```

    A partir desse grafo, é possível observar o intervalo e a distribuição de BMI no conjunto de dados. Por exemplo, a maior parte da BMI está entre 23,2 e 29,2, e os dados estão distorcidos para a direita.

## Realizar a análise multivariada

Vamos gerar visualizações, como gráficos de dispersão e gráficos de caixa, para descobrir padrões e relacionamentos nos dados.

1. Use o ícone **+ Código** abaixo da saída da célula para adicionar uma nova célula de código ao notebook e insira o seguinte código.

    ```python
   import matplotlib.pyplot as plt
   import seaborn as sns

   # Scatter plot of BMI vs. Target variable
   plt.figure(figsize=(8, 6))
   sns.scatterplot(x='BMI', y='Y', data=df)
   plt.title('BMI vs. Target variable')
   plt.xlabel('BMI')
   plt.ylabel('Target')
   plt.show()
    ```

    Podemos ver que, à medida que a BMI aumenta, a variável de destino também aumenta, indicando uma relação linear positiva entre essas duas variáveis.

1. Adicionar outra célula de código ao notebook. Em seguida, insira o seguinte código nessa célula e execute-o.

    ```python
   import seaborn as sns
   import matplotlib.pyplot as plt
    
   fig, ax = plt.subplots(figsize=(7, 5))
    
   # Replace numeric values with labels
   df['SEX'] = df['SEX'].replace({1: 'Male', 2: 'Female'})
    
   sns.boxplot(x='SEX', y='BP', data=df, ax=ax)
   ax.set_title('Blood pressure across Gender')
   plt.tight_layout()
   plt.show()
    ```

    Essas observações sugerem que existem diferenças nos perfis de pressão arterial de pacientes do sexo masculino e feminino. Em média, as pacientes do sexo feminino têm uma pressão arterial mais alta do que os pacientes do sexo masculino.

1. A agregação dos dados pode torná-los mais gerenciáveis para visualização e análise. Adicionar outra célula de código ao notebook. Em seguida, insira o seguinte código nessa célula e execute-o.

    ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
    
   # Calculate average BP and BMI by SEX
   avg_values = df.groupby('SEX')[['BP', 'BMI']].mean()
    
   # Bar chart of the average BP and BMI by SEX
   ax = avg_values.plot(kind='bar', figsize=(15, 6), edgecolor='black')
    
   # Add title and labels
   plt.title('Avg. Blood Pressure and BMI by Gender')
   plt.xlabel('Gender')
   plt.ylabel('Average')
    
   # Display actual numbers on the bar chart
   for p in ax.patches:
       ax.annotate(format(p.get_height(), '.2f'), 
                   (p.get_x() + p.get_width() / 2., p.get_height()), 
                   ha = 'center', va = 'center', 
                   xytext = (0, 10), 
                   textcoords = 'offset points')
    
   plt.show()
    ```

    Esse gráfico mostra que a pressão arterial média é mais alta em pacientes do sexo feminino do que em pacientes do sexo masculino. Além disso, mostra que o Índice de Massa Corporal (IMC) médio é ligeiramente mais alto nas mulheres ao invés dos homens.

1. Adicionar outra célula de código ao notebook. Em seguida, insira o seguinte código nessa célula e execute-o.

    ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
    
   plt.figure(figsize=(10, 6))
   sns.lineplot(x='AGE', y='BMI', data=df, errorbar=None)
   plt.title('BMI over Age')
   plt.xlabel('Age')
   plt.ylabel('BMI')
   plt.show()
    ```

    A faixa etária de 19 a 30 anos tem os valores médios mais baixos de IMC, enquanto o IMC médio mais alto é encontrado na faixa etária de 65 a 79 anos. Além disso, observe que o IMC médio para a maioria das faixas etárias está dentro do intervalo de sobrepeso.

## Análise de correlação

Vamos calcular as correlações entre diferentes recursos para entender seus relacionamentos e dependências.

1. Use o ícone **+ Código** abaixo da saída da célula para adicionar uma nova célula de código ao notebook e insira o seguinte código.

    ```python
   df.corr(numeric_only=True)
    ```

1. Um mapa de calor é uma ferramenta útil para visualizar rapidamente a força e a direção das relações entre pares de variáveis. Ele pode destacar correlações fortes, positivas ou negativas, e identificar pares que não tenham nenhuma correlação. Para criar um mapa de calor, adicione outra célula de código ao notebook e insira o seguinte código.

    ```python
   plt.figure(figsize=(15, 7))
   sns.heatmap(df.corr(numeric_only=True), annot=True, vmin=-1, vmax=1, cmap="Blues")
    ```

    As variáveis S1 e S2 têm uma correlação positiva alta de **0,89**, indicando que elas se movem na mesma direção. Quando S1 aumenta, S2 também tende a aumentar, e vice-versa. Além disso, S3 e S4 têm uma forte correlação negativa de **-0,73**. Isso significa que, à medida que S3 aumenta, S4 tende a diminuir.

## Salvar o notebook e encerrar a sessão do Spark

Agora que você concluiu a exploração dos dados, pode salvar o notebook com um nome significativo e encerrar a sessão do Spark.

1. Na barra de menus do notebook, use o ícone ⚙️ de **Configurações** para ver as configurações do notebook.
2. Defina o **Nome** do notebook como **Explorar dados para ciência de dados** e feche o painel de configurações.
3. No menu do notebook, selecione **Parar sessão** para encerrar a sessão do Spark.

## Limpar os recursos

Neste exercício, você criou e utilizou notebooks para explorar dados. Você também executou o código para calcular estatísticas resumidas e criou visualizações para entender melhor os padrões e os relacionamentos nos dados.

Se você tiver terminado de explorar o modelo e os experimentos, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
