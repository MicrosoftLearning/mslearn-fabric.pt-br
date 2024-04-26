---
lab:
  title: Introdução à ciência de dados no Microsoft Fabric
  module: Get started with data science in Microsoft Fabric
---

# Introdução à ciência de dados no Microsoft Fabric

Neste laboratório, você fará a ingestão de dados, explorará os dados em um notebook, processará os dados com o Estruturador de Dados treinará dois tipos de modelos. Ao executar todas essas etapas, você poderá explorar os recursos de ciência de dados no Microsoft Fabric.

Ao concluir este laboratório, você ganhará experiência prática em machine learning e acompanhamento de modelos, além de aprender a usar o Microsoft Fabric para trabalhar com *notebooks*, *Estruturador de Dados*, *experimentos* e *modelos* no Microsoft Fabric.

Este laboratório levará aproximadamente **20** minutos para ser concluído.

> **Observação**: Você precisará uma [avaliação gratuita do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir este exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Navegue até a página inicial do Microsoft Fabric em [https://app.fabric.microsoft.com](https://app.fabric.microsoft.com) em um navegador.
1. Selecione **Ciência de Dados do Synapse**.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um notebook

Para executar o código, você pode criar um *notebook*. Os notebooks fornecem um ambiente interativo no qual você pode escrever e executar código (em várias linguagens).

1. Na **página inicial de Ciência de Dados do Synapse**, crie um **Notebook**.

    Após alguns segundos, um novo notebook que contém uma só *célula* será aberto. Os notebooks são compostos por uma ou mais células que podem conter um *código* ou um *markdown* (texto formatado).

1. Selecione a primeira célula (que atualmente é uma célula de *código*) e na barra de ferramentas dinâmica no canto superior direito, use o botão **M&#8595;** para converter a célula em uma célula *markdown*.

    Quando a célula for alterada para uma célula markdown, o texto que ela contém será renderizado.

1. Use o botão **&#128393;** (Editar) para alternar a célula para o modo de edição, exclua o conteúdo e insira o seguinte texto:

    ```text
   # Data science in Microsoft Fabric
    ```

## Obter os dados

Agora você está pronto para executar o código para obter dados e treinar um modelo. Você trabalhará com o [conjunto de dados de diabetes](https://learn.microsoft.com/azure/open-datasets/dataset-diabetes?tabs=azureml-opendatasets?azure-portal=true) do Azure Open Datasets. Após carregar os dados, você os converterá em um dataframe do Pandas: uma estrutura comum para trabalhar com dados em linhas e colunas.

1. No seu notebook, utilize o ícone **+ Código** abaixo da última célula para adicionar uma nova célula de código ao notebook.

    > **Dica**: Para ver o ícone **+ Código**, posicione o mouse um pouco abaixo e à esquerda da saída da célula atual. Como alternativa, na barra de menus, na guia **Editar**, selecione **+ Adicionar célula de código**.

1. Insira o seguinte código na nova célula de código:

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

1. Use o botão **&#9655; Executar célula** à esquerda da célula para executá-la. Alternativamente, você pode pressionar `SHIFT` + `ENTER` no teclado para executar uma célula.

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

    A saída mostra as linhas e colunas do conjunto de dados do diabetes.

1. Há duas guias na parte superior da tabela renderizada: **Tabela** e **Gráfico**. Selecione **Gráfico**.
1. Selecione as **Opções de exibição** na parte superior direita do gráfico para alterar a visualização.
1. Altere o gráfico para as seguintes configurações:
    * **Tipo de Gráfico**: `Box plot`
    * **Chave**: *Deixar em branco*
    * **Valores**: `Y`
1. Selecione **Aplicar** para renderizar a nova visualização e explorar o resultado.

## Preparar os dados

Agora que você já ingeriu e explorou os dados, pode transformá-los. Você pode executar código em um notebook ou usar o Estruturador de Dados para gerar código para você.

1. Os dados são carregados como um DataFrame do Spark. Para iniciar o Estruturador de Dados, você precisa converter os dados em um dataframe do Pandas. Execute o código a seguir no notebook:

    ```python
   df = df.toPandas()
   df.head()
    ```

1. Selecione **Dados** na faixa de opções do notebook e, em seguida, selecione o menu suspenso **Transformar DataFrame no Data Wrangler**.
1. Selecione o conjunto de dados `df`. Quando o Data Wrangler é iniciado, ele gera uma visão geral descritiva do dataframe no painel **Resumo**.

    Atualmente, a coluna de rótulo é `Y`, que é uma variável contínua. Para treinar um modelo de machine learning que preveja Y, você precisa treinar um modelo de regressão. Os valores (previstos) de Y podem ser difíceis de interpretar. No entanto, podemos explorar o treinamento de um modelo de classificação que preveja se alguém tem um alto ou baixo risco de desenvolver diabetes. Para poder treinar um modelo de classificação, você precisa criar uma coluna de rótulo binário com base nos valores de `Y`.

1. Selecione a coluna `Y` no Estruturador de Dados. Observe que há uma diminuição na frequência do compartimento `220-240`. O 75º percentil `211.5` alinha-se aproximadamente à transição das duas regiões no histograma. Vamos usar esse valor como limite entre o risco baixo e o risco alto.
1. Navegue até o painel **Operações**, expanda as **Fórmulas** e, em seguida, selecione **Criar coluna a partir da fórmula**.
1. Crie uma nova coluna com as seguintes configurações:
    * **Nome da coluna**: `Risk`
    * **Fórmula da coluna**: `(df['Y'] > 211.5).astype(int)`
1. Analise a nova coluna `Risk` que foi adicionada à pré-visualização. Verifique se o número de linhas com valor `1` é de aproximadamente 25% de todas as linhas (já que é o 75º percentil de `Y`).
1. Escolha **Aplicar**.
1. Selecione **Adicionar código ao notebook**.
1. Execute a célula com o código gerado pelo Estruturador de Dados.
1. Execute código a seguir em uma nova célula para verificar se a coluna `Risk` está no formato esperado:

    ```python
   df_clean.describe()
    ```

## Compilar modelos de aprendizado de máquina

Com os dados preparados, você pode usá-los para treinar um modelo de machine learning para prever o diabetes. Podemos treinar dois tipos diferentes de modelos com nosso conjunto de dados: um modelo de regressão (previsão de `Y`) ou um modelo de classificação (previsão de `Risk`). Você treinará os modelos usando a biblioteca scikit-learn e acompanhará os modelos com o MLflow.

### Treinar o modelo de regressão

1. Execute o código a seguir para dividir os dados em um conjunto de dados de treinamento e teste e separar os recursos do rótulo `Y` que você deseja prever:

    ```python
   from sklearn.model_selection import train_test_split
    
   X, y = df_clean[['AGE','SEX','BMI','BP','S1','S2','S3','S4','S5','S6']].values, df_clean['Y'].values
    
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)
    ```

1. Adicione outra nova célula de código ao notebook e insira o seguinte código nela, executando-a:

    ```python
   import mlflow
   experiment_name = "diabetes-regression"
   mlflow.set_experiment(experiment_name)
    ```

    O código cria um experimento do MLflow chamado `diabetes-regression`. Seus modelos serão rastreados neste experimento.

1. Adicione outra nova célula de código ao notebook e insira o seguinte código nela, executando-a:

    ```python
   from sklearn.linear_model import LinearRegression
    
   with mlflow.start_run():
      mlflow.autolog()
    
      model = LinearRegression()
      model.fit(X_train, y_train)
    ```

    O código treina um modelo de regressão usando Regressão Linear. Os parâmetros, as métricas e os artefatos são registrados em log automaticamente no MLflow.

### Treinar um modelo de classificação

1. Execute o código a seguir para dividir os dados em um conjunto de dados de treinamento e teste e separar os recursos do rótulo `Risk` que você deseja prever:

    ```python
   from sklearn.model_selection import train_test_split
    
   X, y = df_clean[['AGE','SEX','BMI','BP','S1','S2','S3','S4','S5','S6']].values, df_clean['Risk'].values
    
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)
    ```

1. Adicione outra nova célula de código ao notebook e insira o seguinte código nela, executando-a:

    ```python
   import mlflow
   experiment_name = "diabetes-classification"
   mlflow.set_experiment(experiment_name)
    ```

    O código cria um experimento do MLflow chamado `diabetes-classification`. Seus modelos serão rastreados neste experimento.

1. Adicione outra nova célula de código ao notebook e insira o seguinte código nela, executando-a:

    ```python
   from sklearn.linear_model import LogisticRegression
    
   with mlflow.start_run():
       mlflow.sklearn.autolog()

       model = LogisticRegression(C=1/0.1, solver="liblinear").fit(X_train, y_train)
    ```

    O código treina um modelo de classificação usando a regressão logística. Os parâmetros, as métricas e os artefatos são registrados em log automaticamente no MLflow.

## Explorar seus experimentos

O Microsoft Fabric acompanhará todos os seus experimentos e permitirá que você os explore visualmente.

1. Navegue até seu espaço de trabalho na barra de menu do hub à esquerda.
1. Selecione o experimento `diabetes-regression` para abri-lo.

    > **Dica:** caso não veja nenhuma execução de experimento registrada em log, atualize a página.

1. Revise as **Métricas de execução** para explorar a precisão do seu modelo de regressão.
1. Navegue de volta para a home page e selecione o experimento `diabetes-classification` para abri-lo.
1. Revise as **Métricas de execução** para explorar a precisão do seu modelo de classificação. Observe que os tipos de métricas são diferentes, pois você treinou um tipo diferente de modelo.

## Salvar o modelo

Depois de comparar os modelos de machine learning que você treinou nos experimentos, você poderá escolher o modelo de melhor desempenho. Para usar o modelo de melhor desempenho, salve o modelo e use-o para gerar previsões.

1. Selecione **Salvar como modelo de ML** na faixa de opções do experimento.
1. Selecione **Criar um modelo de ML** na janela pop-up recém-aberta.
1. Selecione a pasta `model` .
1. Dê ao modelo o nome `model-diabetes` e selecione **Salvar**.
1. Selecione **Exibir modelo de ML** na notificação que aparece no canto superior direito da tela quando o modelo é criado. Você também pode atualizar a janela. O modelo salvo está vinculado nas **Versões do modelo de ML**.

Observe que o modelo, o experimento e a execução do experimento estão vinculados, permitindo que você analise como o modelo é treinado.

## Salvar o notebook e encerrar a sessão do Spark

Agora que você concluiu o treinamento e a avaliação dos modelos, salve o notebook com um nome significativo e encerre a sessão do Spark.

1. Na barra de menus do notebook, use o ícone ⚙️ de **Configurações** para ver as configurações do notebook.
2. Defina o **Nome** do notebook como **Treinar e comparar modelos** e feche o painel de configurações.
3. No menu do notebook, selecione **Parar sessão** para encerrar a sessão do Spark.

## Limpar os recursos

Neste exercício, você criou um notebook e treinou um modelo de machine learning. Você usou o scikit-learn para treinar o modelo e o MLflow para acompanhar o desempenho dele.

Se você tiver terminado de explorar o modelo e os experimentos, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este workspace**.
