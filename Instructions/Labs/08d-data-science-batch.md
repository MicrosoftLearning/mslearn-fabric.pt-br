---
lab:
  title: Gere previsões em lote usando um modelo implantado no Microsoft Fabric
  module: Generate batch predictions using a deployed model in Microsoft Fabric
---

# Gere previsões em lote usando um modelo implantado no Microsoft Fabric

Neste laboratório, você usará um modelo de machine learning para prever uma medida quantitativa do diabetes.

Ao concluir esse laboratório, você ganhará experiência prática na geração de previsões e na visualização dos resultados.

Este laboratório levará aproximadamente **20** minutos para ser concluído.

> **Observação**: Você precisará uma [avaliação gratuita do Microsoft Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial) para concluir este exercício.

## Criar um workspace

Antes de trabalhar com os dados no Fabric, crie um workspace com a avaliação do Fabric habilitada.

1. Navegue até a [home page do Microsoft Fabric](https://app.fabric.microsoft.com/home?experience=fabric) em `https://app.fabric.microsoft.com/home?experience=fabric` em um navegador e entre com suas credenciais do Fabric.
1. Na barra de menus à esquerda, selecione **Workspaces** (o ícone é semelhante a &#128455;).
1. Crie um workspace com um nome de sua escolha selecionando um modo de licenciamento que inclua a capacidade do Fabric (*Avaliação*, *Premium* ou *Malha*).
1. Quando o novo workspace for aberto, ele estará vazio.

    ![Captura de tela de um espaço de trabalho vazio no Fabric.](./Images/new-workspace.png)

## Criar um notebook

Você usará um *notebook* para treinar e usar um modelo neste exercício.

1. Na barra de menus à esquerda, selecione **Criar**. Na página *Novo*, na seção *Ciência de Dados*, selecione **Notebook**. Dê um nome exclusivo de sua preferência.

    >**Observação**: se a opção **Criar** não estiver fixada na barra lateral, você precisará selecionar a opção de reticências (**...**) primeiro.

    Após alguns segundos, um novo notebook que contém uma só *célula* será aberto. Os notebooks são compostos por uma ou mais células que podem conter um *código* ou um *markdown* (texto formatado).

1. Selecione a primeira célula (que atualmente é uma célula de *código*) e na barra de ferramentas dinâmica no canto superior direito, use o botão **M&#8595;** para converter a célula em uma célula *markdown*.

    Quando a célula for alterada para uma célula markdown, o texto que ela contém será renderizado.

1. Se necessário, use o botão **&#128393;** (Editar) para alternar a célula para o modo de edição e, em seguida, exclua o conteúdo e insira o texto a seguir:

    ```text
   # Train and use a machine learning model
    ```

## Treinar um modelo de machine learning

Primeiro, vamos treinar um modelo de machine learning que usa um algoritmo *regressão* para prever a resposta de interesse para pacientes com diabetes (uma medida quantitativa da progressão da doença um ano após a linha de base)

1. No seu notebook, utilize o ícone **+ Código** abaixo da última célula para adicionar uma nova célula de código ao notebook.

    > **Dica**: Para ver o ícone **+ Código**, posicione o mouse um pouco abaixo e à esquerda da saída da célula atual. Como alternativa, na barra de menus, na guia **Editar**, selecione **+ Adicionar célula de código**.

1. Insira o código a seguir para carregar e preparar dados e usá-los para treinar um modelo.

    ```python
   import pandas as pd
   import mlflow
   from sklearn.model_selection import train_test_split
   from sklearn.tree import DecisionTreeRegressor
   from mlflow.models.signature import ModelSignature
   from mlflow.types.schema import Schema, ColSpec

   # Get the data
   blob_account_name = "azureopendatastorage"
   blob_container_name = "mlsamples"
   blob_relative_path = "diabetes"
   blob_sas_token = r""
   wasbs_path = f"wasbs://%s@%s.blob.core.windows.net/%s" % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set("fs.azure.sas.%s.%s.blob.core.windows.net" % (blob_container_name, blob_account_name), blob_sas_token)
   df = spark.read.parquet(wasbs_path).toPandas()

   # Split the features and label for training
   X, y = df[['AGE','SEX','BMI','BP','S1','S2','S3','S4','S5','S6']].values, df['Y'].values
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)

   # Train the model in an MLflow experiment
   experiment_name = "experiment-diabetes"
   mlflow.set_experiment(experiment_name)
   with mlflow.start_run():
       mlflow.autolog(log_models=False)
       model = DecisionTreeRegressor(max_depth=5)
       model.fit(X_train, y_train)
       
       # Define the model signature
       input_schema = Schema([
           ColSpec("integer", "AGE"),
           ColSpec("integer", "SEX"),\
           ColSpec("double", "BMI"),
           ColSpec("double", "BP"),
           ColSpec("integer", "S1"),
           ColSpec("double", "S2"),
           ColSpec("double", "S3"),
           ColSpec("double", "S4"),
           ColSpec("double", "S5"),
           ColSpec("integer", "S6"),
        ])
       output_schema = Schema([ColSpec("integer")])
       signature = ModelSignature(inputs=input_schema, outputs=output_schema)
   
       # Log the model
       mlflow.sklearn.log_model(model, "model", signature=signature)
    ```

1. Use o botão **&#9655; Executar célula** à esquerda da célula para executá-la. Como alternativa, você pode pressionar **SHIFT** + **ENTER** no teclado para executar uma célula.

    > **Observação**: como esta é a primeira vez que você executa qualquer código Spark nesta sessão, o Pool do Spark precisa ser iniciado. Isso significa que a primeira execução na sessão pode levar um minuto para ser concluída. As execuções seguintes serão mais rápidas.

1. Use o ícone **+ Código** abaixo da saída da célula para adicionar uma nova célula de código ao notebook e insira o seguinte código para registrar o modelo que foi treinado pelo experimento na célula anterior:

    ```python
   # Get the most recent experiement run
   exp = mlflow.get_experiment_by_name(experiment_name)
   last_run = mlflow.search_runs(exp.experiment_id, order_by=["start_time DESC"], max_results=1)
   last_run_id = last_run.iloc[0]["run_id"]

   # Register the model that was trained in that run
   print("Registering the model from run :", last_run_id)
   model_uri = "runs:/{}/model".format(last_run_id)
   mv = mlflow.register_model(model_uri, "diabetes-model")
   print("Name: {}".format(mv.name))
   print("Version: {}".format(mv.version))
    ```

    Seu modelo agora está salvo em seu espaço de trabalho como **modelo-de-diabetes**. Opcionalmente, você pode usar o recurso de navegação em seu espaço de trabalho para localizar o modelo no espaço de trabalho e explorá-lo usando a interface do usuário.

## Crie um conjunto de dados de teste em um lakehouse

Para usar o modelo, você precisará de um conjunto de dados de detalhes de pacientes para os quais você precisa prever um diagnóstico de diabetes. Você criará esse conjunto de dados como uma tabela em um Microsoft Fabric Lakehouse.

1. No editor do Notebook, no painel do **Explorer** do lado esquerdo, selecione **+ Fontes de dados** para adicionar um lakehouse.
1. Selecione **Novo Lakehouse** e **Adicionar** e crie um nov **Lakehouse** com um nome válido de sua escolha.
1. Quando solicitado a interromper a sessão atual, selecione **Parar agora** para reiniciar o notebook.
1. Quando o lakehouse for criado e anexado ao notebook, adicione uma nova célula de código e execute o seguinte código para criar um conjunto de dados e salvá-lo em uma tabela no lakehouse:

    ```python
   from pyspark.sql.types import IntegerType, DoubleType

   # Create a new dataframe with patient data
   data = [
       (62, 2, 33.7, 101.0, 157, 93.2, 38.0, 4.0, 4.8598, 87),
       (50, 1, 22.7, 87.0, 183, 103.2, 70.0, 3.0, 3.8918, 69),
       (76, 2, 32.0, 93.0, 156, 93.6, 41.0, 4.0, 4.6728, 85),
       (25, 1, 26.6, 84.0, 198, 131.4, 40.0, 5.0, 4.8903, 89),
       (53, 1, 23.0, 101.0, 192, 125.4, 52.0, 4.0, 4.2905, 80),
       (24, 1, 23.7, 89.0, 139, 64.8, 61.0, 2.0, 4.1897, 68),
       (38, 2, 22.0, 90.0, 160, 99.6, 50.0, 3.0, 3.9512, 82),
       (69, 2, 27.5, 114.0, 255, 185.0, 56.0, 5.0, 4.2485, 92),
       (63, 2, 33.7, 83.0, 179, 119.4, 42.0, 4.0, 4.4773, 94),
       (30, 1, 30.0, 85.0, 180, 93.4, 43.0, 4.0, 5.3845, 88)
   ]
   columns = ['AGE','SEX','BMI','BP','S1','S2','S3','S4','S5','S6']
   df = spark.createDataFrame(data, schema=columns)

   # Convert data types to match the model input schema
   df = df.withColumn("AGE", df["AGE"].cast(IntegerType()))
   df = df.withColumn("SEX", df["SEX"].cast(IntegerType()))
   df = df.withColumn("BMI", df["BMI"].cast(DoubleType()))
   df = df.withColumn("BP", df["BP"].cast(DoubleType()))
   df = df.withColumn("S1", df["S1"].cast(IntegerType()))
   df = df.withColumn("S2", df["S2"].cast(DoubleType()))
   df = df.withColumn("S3", df["S3"].cast(DoubleType()))
   df = df.withColumn("S4", df["S4"].cast(DoubleType()))
   df = df.withColumn("S5", df["S5"].cast(DoubleType()))
   df = df.withColumn("S6", df["S6"].cast(IntegerType()))

   # Save the data in a delta table
   table_name = "diabetes_test"
   df.write.format("delta").mode("overwrite").saveAsTable(table_name)
   print(f"Spark dataframe saved to delta table: {table_name}")
    ```

1. Quando o código estiver concluído, selecione **...** ao lado de **Tabelas** no painel **Lakehouse Explorer** e selecione **Atualizar**. A tabela **diabetes_test** deve aparecer.
1. Expanda a tabela **diabetes_test** no painel esquerdo para ver todos os campos que ela inclui.

## Aplicar o modelo para gerar previsões

Agora você pode usar o modelo treinado anteriormente para gerar previsões de progressão do diabetes para as linhas de dados do paciente em sua tabela.

1. Adicione uma nova célula de código e execute o seguinte código:

    ```python
   import mlflow
   from synapse.ml.predict import MLFlowTransformer

   ## Read the patient features data 
   df_test = spark.read.format("delta").load(f"Tables/{table_name}")

   # Use the model to generate diabetes predictions for each row
   model = MLFlowTransformer(
       inputCols=["AGE","SEX","BMI","BP","S1","S2","S3","S4","S5","S6"],
       outputCol="predictions",
       modelName="diabetes-model",
       modelVersion=1)
   df_test = model.transform(df)

   # Save the results (the original features PLUS the prediction)
   df_test.write.format('delta').mode("overwrite").option("mergeSchema", "true").saveAsTable(table_name)
    ```

1. Após a conclusão do código, selecione **...** ao lado da tabela **diabetes_test** no painel **Explorador do Lakehouse** e selecione **Atualizar**. Um novo campo **previsões** foi adicionado.
1. Adicione uma nova célula de código ao notebook e arraste a tabela **diabetes_test** para ela. O código necessário para exibir o conteúdo da tabela aparecerá. Execute a célula para exibir os dados.

## Limpar os recursos

Neste exercício, você usou um modelo para gerar previsões em lote.

Caso tenha terminado de explorar o notebook, exclua o workspace que você criou para esse exercício.

1. Na barra à esquerda, selecione o ícone do workspace para ver todos os itens que ele contém.
2. No menu **…** da barra de ferramentas, selecione **Configurações do workspace**.
3. Na seção **Geral**, selecione **Remover este espaço de trabalho**.
