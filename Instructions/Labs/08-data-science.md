---
lab:
  title: Treinar um modelo de classificação para prever a rotatividade de clientes
  module: Get started with data science in Microsoft Fabric
---

# Usar notebooks para treinar um modelo no Microsoft Fabric

Neste laboratório, usaremos o Microsoft Fabric para criar um notebook e treinar um modelo de machine learning para prever a rotatividade de clientes. Usaremos o Scikit-learn para treinar o modelo e o MLflow para acompanhar o desempenho dele. A rotatividade de clientes é um problema comercial crítico que muitas empresas enfrentam, e a previsão de quais clientes provavelmente vão gerar rotatividade pode ajudar as empresas a reter os clientes e aumentar a receita. Ao concluir este laboratório, você ganhará experiência prática em machine learning e acompanhamento de modelos e aprenderá a usar o Microsoft Fabric para criar um notebook para seus projetos.

Este laboratório levará aproximadamente **45** minutos para ser concluído.

> **Observação**: você precisará ter uma licença do Microsoft Fabric para concluir este exercício. Confira [Introdução ao Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para obter detalhes de como habilitar uma licença de avaliação gratuita do Fabric. Você precisará ter uma conta *corporativa* ou de *estudante* da Microsoft para fazer isso. Caso não tenha uma, [inscreva-se em uma avaliação do Microsoft Office 365 E3 ou superior](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Entre no [Microsoft Fabric](https://app.fabric.microsoft.com) em `https://app.fabric.microsoft.com` e selecione **Power BI**.
2. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
3. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
4. Quando o novo workspace for aberto, ele deverá estar vazio, conforme mostrado aqui:

    ![Captura de tela de um workspace vazio no Power BI.](./Images/new-workspace.png)

## Criar um lakehouse e carregar arquivos

Agora que você tem um workspace, é hora de alternar para a experiência de *Ciência de dados* no portal e criar um data lakehouse para os arquivos de dados que você vai analisar.

1. No canto inferior esquerdo do portal do Power BI, selecione o ícone do **Power BI** e alterne para a experiência de **Engenharia de Dados**.
1. Na home page de **Engenharia de dados**, crie um **Lakehouse** com um nome de sua escolha.

    Após alguns minutos, um lakehouse sem **Tabelas** nem **Arquivos** será criado. Você precisa ingerir alguns dados no data lakehouse para análise. Há várias maneiras de fazer isso, mas neste exercício, você apenas baixará e extrairá uma pasta de arquivos de texto no computador local (ou na VM de laboratório, se aplicável) e os carregará no lakehouse.

1. Baixe e salve o arquivo CSV `churn.csv` deste exercício de [https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/churn.csv](https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/churn.csv).


1. Volte à guia do navegador da Web que contém o lakehouse e, no menu **…** do nó **Arquivos** no painel **Exibição do lake**, selecione **Carregar** e **Carregar arquivos** e carregue o arquivo **churn.csv** do computador local (ou da VM de laboratório, se aplicável) no lakehouse.
6. Depois que os arquivos forem carregados, expanda **Arquivos** e verifique se o arquivo CSV foi carregado.

## Criar um notebook

Para treinar um modelo, você pode criar um *notebook*. Os notebooks fornecem um ambiente interativo no qual você pode escrever e executar um código (em várias linguagens) como *experimentos*.

1. No canto inferior esquerdo do portal do Power BI, selecione o ícone **Engenharia de dados** e alterne para a experiência de **Ciência de dados**.

1. Na home page de **Ciência de dados**, crie um **Notebook**.

    Após alguns segundos, um novo notebook que contém uma só *célula* será aberto. Os notebooks são compostos por uma ou mais células que podem conter um *código* ou um *markdown* (texto formatado).

1. Selecione a primeira célula (que atualmente é uma célula de *código*) e na barra de ferramentas dinâmica no canto superior direito, use o botão **M&#8595;** para converter a célula em uma célula *markdown*.

    Quando a célula for alterada para uma célula markdown, o texto que ela contém será renderizado.

1. Use o botão **&#128393;** (Editar) para alternar a célula para o modo de edição, exclua o conteúdo e insira o seguinte texto:

    ```text
   # Train a machine learning model and track with MLflow

   Use the code in this notebook to train and track models.
    ``` 

## Carregar dados em um dataframe

Agora você está pronto para executar o código para preparar dados e treinar um modelo. Para trabalhar com os dados, você usará *dataframes*. Os dataframes no Spark são semelhantes aos dataframes do Pandas no Python e fornecem uma estrutura comum para trabalhar com os dados em linhas e colunas.

1. No painel **Adicionar lakehouse**, selecione **Adicionar** para adicionar um lakehouse.
1. Selecione **Lakehouse existente** e selecione **Adicionar**.
1. Selecione o lakehouse que você criou em uma seção anterior.
1. Expanda a pasta **Arquivos** para que o arquivo CSV seja listado ao lado do editor do notebook.
1. No menu **…** de **churn.csv**, selecione **Carregar dados** > **Pandas**. Uma nova célula de código que contém o seguinte código deve ser adicionada ao notebook:

    ```python
   import pandas as pd
   # Load data into pandas DataFrame from "/lakehouse/default/" + "Files/churn.csv"
   df = pd.read_csv("/lakehouse/default/" + "Files/churn.csv")
   display(df)
    ```

    > **Dica**: oculte o painel que contém os arquivos à esquerda usando o ícone **<<** . Isso ajudará você a se concentrar no notebook.

1. Use o botão **&#9655; Executar célula** à esquerda da célula para executá-la.

    > **Observação**: como esta é a primeira vez que você executa qualquer código Spark nesta sessão, o Pool do Spark precisa ser iniciado. Isso significa que a primeira execução na sessão pode levar um minuto para ser concluída. As execuções seguintes serão mais rápidas.

1. Quando o comando de célula for concluído, analise a saída abaixo da célula, que deve ser semelhante a esta:

    |Índice|CustomerID|years_with_company|total_day_calls|total_eve_calls|total_night_calls|total_intl_calls|average_call_minutes|total_customer_service_calls|age|rotatividade|
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    |1|1\.000.038|0|117|88|32|607|43,90625678|0,810828179|34|0|
    |2|1\.000.183|1|164|102|22|40|49,82223317|0,294453889|35|0|
    |3|1\.000.326|3|116|43|45|207|29,83377967|1,344657937|57|1|
    |4|1\.000.340|0|92|24|11|37|31,61998183|0,124931779|34|0|
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    A saída mostra as linhas e as colunas de dados do cliente do arquivo churn.csv.

## Treinar um modelo de machine learning

Agora que carregou os dados, você poderá usá-los para treinar um modelo de machine learning e prever a rotatividade de clientes. Você treinará um modelo usando a biblioteca do Scikit-learn e acompanhará o modelo com o MLflow. 

1. Use o ícone **+ Código** abaixo da saída da célula para adicionar uma nova célula de código ao notebook e insira o seguinte código nela:

    ```python
   from sklearn.model_selection import train_test_split

   print("Splitting data...")
   X, y = df[['years_with_company','total_day_calls','total_eve_calls','total_night_calls','total_intl_calls','average_call_minutes','total_customer_service_calls','age']].values, df['churn'].values
   
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)
    ```

1. Execute a célula de código adicionada e observe que você está omitindo 'CustomerID' do conjunto de dados e dividindo os dados em um conjunto de dados de treinamento e de teste.
1. Adicione outra nova célula de código ao notebook e insira o seguinte código nela, executando-a:
    
    ```python
   import mlflow
   experiment_name = "experiment-churn"
   mlflow.set_experiment(experiment_name)
    ```
    
    O código cria um experimento do MLflow chamado `experiment-churn`. Seus modelos serão rastreados neste experimento.

1. Adicione outra nova célula de código ao notebook e insira o seguinte código nela, executando-a:

    ```python
   from sklearn.linear_model import LogisticRegression
   
   with mlflow.start_run():
       mlflow.autolog()

       model = LogisticRegression(C=1/0.1, solver="liblinear").fit(X_train, y_train)

       mlflow.log_param("estimator", "LogisticRegression")
    ```
    
    O código treina um modelo de classificação usando a regressão logística. Os parâmetros, as métricas e os artefatos são registrados em log automaticamente no MLflow. Além disso, você está registrando em log um parâmetro chamado `estimator`, com o valor `LogisticRegression`.

1. Adicione outra nova célula de código ao notebook e insira o seguinte código nela, executando-a:

    ```python
   from sklearn.tree import DecisionTreeClassifier
   
   with mlflow.start_run():
       mlflow.autolog()

       model = DecisionTreeClassifier().fit(X_train, y_train)
   
       mlflow.log_param("estimator", "DecisionTreeClassifier")
    ```

    O código treina um modelo de classificação usando o classificador de árvore de decisão. Os parâmetros, as métricas e os artefatos são registrados em log automaticamente no MLflow. Além disso, você está registrando em log um parâmetro chamado `estimator`, com o valor `DecisionTreeClassifier`.

## Usar o MLflow para pesquisar e ver seus experimentos

Ao treinar e acompanhar modelos com o MLflow, você pode usar a biblioteca do MLflow para recuperar seus experimentos e os detalhes.

1. Para listar todos os experimentos, use o seguinte código:

    ```python
   import mlflow
   experiments = mlflow.search_experiments()
   for exp in experiments:
       print(exp.name)
    ```

1. Para recuperar um experimento específico, você pode obtê-lo pelo nome:

    ```python
   experiment_name = "experiment-churn"
   exp = mlflow.get_experiment_by_name(experiment_name)
   print(exp)
    ```

1. Usando um nome de experimento, recupere todos os trabalhos desse experimento:

    ```python
   mlflow.search_runs(exp.experiment_id)
    ```

1. Para comparar com mais facilidade as execuções e as saídas do trabalho, configure a pesquisa para ordenar os resultados. Por exemplo, a seguinte célula ordena os resultados por `start_time` e mostra, no máximo, `2` resultados: 

    ```python
   mlflow.search_runs(exp.experiment_id, order_by=["start_time DESC"], max_results=2)
    ```

1. Por fim, você pode plotar as métricas de avaliação de vários modelos um ao lado do outro para comparar os modelos com facilidade:

    ```python
   import matplotlib.pyplot as plt
   
   df_results = mlflow.search_runs(exp.experiment_id, order_by=["start_time DESC"], max_results=2)[["metrics.training_accuracy_score", "params.estimator"]]
   
   fig, ax = plt.subplots()
   ax.bar(df_results["params.estimator"], df_results["metrics.training_accuracy_score"])
   ax.set_xlabel("Estimator")
   ax.set_ylabel("Accuracy")
   ax.set_title("Accuracy by Estimator")
   for i, v in enumerate(df_results["metrics.training_accuracy_score"]):
       ax.text(i, v, str(round(v, 2)), ha='center', va='bottom', fontweight='bold')
   plt.show()
    ```

    A saída será parecida com a seguinte imagem:

    ![Captura de tela das métricas de avaliação plotadas.](./Images/plotted-metrics.png)

## Explorar seus experimentos

O Microsoft Fabric acompanhará todos os seus experimentos e permitirá que você os explore visualmente.

1. Acesse a home page **Ciência de Dados**.
1. Selecione o experimento `experiment-churn` para abri-lo.

    > **Dica:** caso não veja nenhuma execução de experimento registrada em log, atualize a página.

1. Selecione a guia **Exibir**.
1. Selecione **Executar lista**. 
1. Selecione as duas execuções mais recentes marcando cada caixa.
    Como resultado, as duas últimas execuções serão comparadas entre si no painel **Comparação de métricas**. Por padrão, as métricas são plotadas por nome de execução. 
1. Selecione o botão **&#128393;** (Editar) do grafo visualizando a precisão de cada execução. 
1. Altere o **tipo de visualização** para `bar`. 
1. Altere o **eixo X** para `estimator`. 
1. Selecione **Substituir** e explore o novo grafo.

Ao plotar a precisão por avaliador registrado em log, você pode analisar qual algoritmo resultou em um modelo melhor.

## Salvar o modelo

Depois de comparar os modelos de machine learning que você treinou em execuções de experimentos, você poderá escolher o modelo de melhor desempenho. Para usar o modelo de melhor desempenho, salve o modelo e use-o para gerar previsões.

1. Na visão geral do experimento, verifique se a guia **Exibir** está selecionada.
1. Selecione **Executar detalhes**.
1. Selecione a execução com a maior precisão. 
1. Selecione **Salvar** na caixa **Salvar como modelo**.
1. Selecione **Criar um modelo** na janela pop-up recém-aberta.
1. Dê ao modelo o nome `model-churn` e selecione **Criar**. 
1. Selecione **Exibir modelo** na notificação exibida no canto superior direito da tela quando o modelo é criado. Você também pode atualizar a janela. O modelo salvo está vinculado em **Versão registrada**. 

Observe que o modelo, o experimento e a execução do experimento estão vinculados, permitindo que você analise como o modelo é treinado. 

## Salvar o notebook e encerrar a sessão do Spark

Agora que você concluiu o treinamento e a avaliação dos modelos, salve o notebook com um nome significativo e encerre a sessão do Spark.

1. Na barra de menus do notebook, use o ícone ⚙️ de **Configurações** para ver as configurações do notebook.
2. Defina o **Nome** do notebook como **Treinar e comparar modelos** e feche o painel de configurações.
3. No menu do notebook, selecione **Parar sessão** para encerrar a sessão do Spark.

## Limpar os recursos

Neste exercício, você criou um notebook e treinou um modelo de machine learning. Você usou o Scikit-learn para treinar o modelo e o MLflow para acompanhar o desempenho dele.

Se você tiver terminado de explorar o modelo e os experimentos, exclua o workspace criado para este exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Outros**, selecione **Remover este workspace**.
