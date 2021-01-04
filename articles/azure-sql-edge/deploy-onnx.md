---
title: Bereitstellen und Erstellen von Vorhersagen mit ONNX
titleSuffix: SQL machine learning
description: Hier erfahren Sie, wie Sie ein Modell trainieren, es in ONNX konvertieren, anschließend in Azure SQL Edge oder Azure SQL Managed Instance (Vorschau) bereitstellen und dann mithilfe des hochgeladenen ONXX-Modells PREDICT (nativ) für Daten ausführen.
keywords: SQL Edge bereitstellen
ms.prod: sql
ms.technology: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 10/13/2020
ms.openlocfilehash: 6dd7715292470d186806443d0a0b05bdbb084a43
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392179"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-and-sql-machine-learning"></a>Bereitstellen eines ONNX-Modells sowie von maschinellem Lernen in SQL und Treffen von Vorhersagen damit

In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Modell trainieren, es in ONNX konvertieren, anschließend in [Azure SQL Edge](onnx-overview.md) oder [Azure SQL Managed Instance (Vorschau)](../azure-sql/managed-instance/machine-learning-services-overview.md) bereitstellen und dann mithilfe des hochgeladenen ONXX-Modells PREDICT (nativ) für Daten ausführen.

Dieser Schnellstart basiert auf **SciKit-learn** und verwendet das [Boston Housing-Dataset](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html).

## <a name="before-you-begin"></a>Voraussetzungen

* Wenn Sie Azure SQL Edge verwenden und kein Azure SQL Edge-Modul bereitgestellt haben, führen Sie die Schritte unter [Bereitstellen von Azure SQL Edge](deploy-portal.md) aus.

* Installieren Sie [Azure Data Studio](/sql/azure-data-studio/download).

* Installieren Sie die für diese Schnellstartanleitung erforderlichen Python-Pakete.

  1. Öffnen Sie [Neues Notebook](/sql/azure-data-studio/sql-notebooks), das mit dem Python 3-Kernel verbunden ist. 
  1. Klicken Sie auf **Manage Packages** (Pakete verwalten).
  1. Suchen Sie auf der Registerkarte **Installiert** in der Liste der installierten Pakete nach den folgenden Python-Paketen. Ist eines dieser Pakete nicht installiert, wählen Sie die Registerkarte **Neu hinzufügen** aus, suchen Sie nach dem Paket, und klicken Sie auf **Installieren**.
     - **scikit-learn**
     - **numpy**
     - **onnxmltools**
     - **onnxruntime**
     - **pyodbc**
     - **setuptools**
     - **skl2onnx**
     - **sqlalchemy**

* Geben Sie jeden der nachstehenden Skriptteile in eine Zelle im Azure Data Studio-Notebook ein, und führen Sie die Zelle aus.

## <a name="train-a-pipeline"></a>Trainieren einer Pipeline

Teilen Sie das Dataset auf, um Features zum Vorhersagen des Medians für ein Haus verwenden zu können.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])
 
target_column = 'MEDV'
 
# Split the data frame into features and target
x_train = pd.DataFrame(df.drop([target_column], axis = 1))
y_train = pd.DataFrame(df.iloc[:,df.columns.tolist().index(target_column)])

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n**_ Training dataset y\n")
print(y_train.head())
```

_*Ausgabe:**

```text
**_ Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

_*_ Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

Erstellen Sie eine Pipeline zum Trainieren des „LinearRegression“-Modells. Sie können auch andere Regressionsmodelle verwenden.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Überprüfen Sie die Genauigkeit des Modells, und berechnen Sie dann den R2-Score und den mittleren quadratischen Fehler.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('_*_ Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('_*_ Scikit-learn MSE: {}'.format(sklearn_mse))
```

_*Ausgabe:**

```text
**_ Scikit-learn r2 score: 0.7406426641094094
_*_ Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>Konvertieren des Modells in ONNX

Konvertieren Sie die Datentypen in die unterstützten SQL-Datentypen. Diese Konvertierung ist auch bei anderen DataFrames erforderlich.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

Konvertieren Sie das „LinearRegression“-Modell mithilfe von `skl2onnx` in das ONNX-Format, und speichern Sie es lokal.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train), final_types=[('variable1',FloatTensorType([1,1]))])
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>Testen des ONNX-Modells

Nachdem Sie das Modell in das ONNX-Format konvertiert haben, bewerten Sie es, um zu zeigen, dass die Leistung wenig bis gar nicht beeinträchtigt wurde.

> [!NOTE]
> Weil ONNX-Runtime „float“- statt „double“-Werte verwendet, sind geringfügige Abweichungen möglich.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('_*_ Onnx r2 score: {}'.format(onnx_r2_score))
print('_*_ Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

_*Ausgabe:**

```text
**_ Onnx r2 score: 0.7406426691136831
_*_ Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>Einfügen des ONNX-Modells

Speichern Sie das Modell in Azure SQL Edge oder Azure SQL Managed Instance, und zwar in einer `models`-Tabelle in einer Datenbank vom Typ `onnx`. Geben Sie in der Verbindungszeichenfolge die _*Serveradresse**, den **Benutzernamen** und das **Kennwort** an.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>Laden der Daten

Laden Sie die Daten in SQL.

Erstellen Sie zuerst zwei Tabellen – **Features** und **Ziel** – zum Speichern von Teilmengen des Boston Housing-Datasets.

* **Features** enthält alle Daten, die zum Vorhersagen des Ziels, „Median“, verwendet werden. 
* **Ziel** enthält den Median für jeden Datensatz im Dataset. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Verwenden Sie schließlich `sqlalchemy` zum Einfügen der Pandas-DataFrames `x_train` und `y_train` in die Tabellen `features` bzw. `target`. 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

Jetzt können Sie die Daten in der Datenbank anzeigen.

## <a name="run-predict-using-the-onnx-model"></a>Ausführen von PREDICT mithilfe des ONNX-Modells

Führen Sie mit dem Modell in SQL PREDICT (nativ) für die Daten aus, und verwenden Sie dazu das hochgeladene ONNX-Modell.

> [!NOTE]
> Ändern Sie den Notebook-Kernel in SQL, um die verbleibende Zelle auszuführen.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input, RUNTIME=ONNX) WITH (variable1 FLOAT) AS p;
```

## <a name="next-steps"></a>Nächste Schritte

* [Machine Learning und KI mit ONNX in SQL Edge](onnx-overview.md)
* [Machine Learning Services in Azure SQL Managed Instance (Vorschauversion)](../azure-sql/managed-instance/machine-learning-services-overview.md)