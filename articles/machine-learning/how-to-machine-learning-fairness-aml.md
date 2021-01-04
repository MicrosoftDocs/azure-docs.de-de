---
title: Bewerten der Fairness von ML-Modellen in Python (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe von Fairlearn und dem Python-SDK für Azure Machine Learning die Fairness Ihrer Machine Learning-Modelle beurteilen und minimieren können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3fbd4990fd330960bb8dbce2e2a8d1bcb578cf2a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701183"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Verwenden von Azure Machine Learning mit dem Open-Source-Paket Fairlearn zur Bewertung der Fairness von ML-Modellen (Vorschau)

In dieser Schrittanleitung erfahren Sie, wie Sie das Open-Source-Python-Paket [Fairlearn](https://fairlearn.github.io/) mit Azure Machine Learning verwenden, um die folgenden Aufgaben auszuführen:

* Bewerten Sie die Fairness Ihrer Modellvorhersagen. Weitere Informationen zur Fairness im Bereich maschinelles Lernen finden Sie im Artikel [Fairness bei Machine Learning-Modellen](concept-fairness-ml.md).
* Laden Sie Erkenntnisse aus der Fairnessbewertung in Azure Machine Learning Studio hoch, listen Sie sie dort auf, und laden Sie sie dort herunter.
* Sehen Sie sich ein Fairnessbewertungsdashboard in Azure Machine Learning Studio an, um mit den Erkenntnissen zur Fairness für Ihr(e) Modell(e) zu interagieren.

>[!NOTE]
> Eine Fairnessbewertung ist keine rein technische Angelegenheit. **Dieses Paket kann Ihnen dabei helfen, die Fairness eines Machine Learning-Modells zu bewerten, aber nur Sie können das Modell konfigurieren und Entscheidungen in Bezug auf dessen Leistung treffen.**  Zwar hilft es dabei, quantitative Metriken zur Bewertung der Fairness zu identifizieren, Entwickler von Machine Learning-Modellen müssen jedoch auch eine qualitative Analyse durchführen, um die Fairness ihrer Modelle zu bewerten.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning-Fairness-SDK 

Das Azure Machine Learning-Fairness-SDK (`azureml-contrib-fairness`) integriert das Open-Source-Python-Paket [Fairlearn](http://fairlearn.github.io) in Azure Machine Learning. Weitere Informationen zur Integration von Fairlearn in Azure Machine Learning finden Sie in diesen [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Weitere Informationen zu Fairlearn finden Sie in der [Anleitung mit Beispielen](https://fairlearn.github.io/master/auto_examples/) und den [Beispielnotebooks](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Verwenden Sie die folgenden Befehle, um die Pakete `azureml-contrib-fairness` und `fairlearn` zu installieren:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>Hochladen von Erkenntnissen zur Fairness für ein einzelnes Modell

Im folgenden Beispiel wird gezeigt, wie Sie das Fairnesspaket verwenden, um Erkenntnisse zur Modellfairness in Azure Machine Learning hochzuladen und das Fairnessbewertungsdashboard in Azure Machine Learning Studio anzuzeigen.

1. Trainieren Sie ein Beispielmodell in einem Jupyter-Notebook. 

    Als Dataset verwenden Sie das bekannte Zensusdataset mit Angaben zu Erwachsenen, das Sie (der Einfachheit halber) über `shap` laden. Für die Zwecke dieses Beispiels wird dieses Dataset als Kreditentscheidungsproblem behandelt, und es wird vorgegeben, dass die Bezeichnung angibt, ob die jeweilige Person in der Vergangenheit einen Kredit zurückgezahlt hat oder nicht. Sie verwenden diese Daten zum Trainieren eines Prädiktors, um vorherzusagen, ob zuvor nicht bekannte Personen einen Kredit zurückzahlen werden oder nicht. Es wird davon ausgegangen, dass die Modellvorhersagen verwendet werden, um zu entscheiden, ob einer Person ein Kredit angeboten werden soll.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Melden Sie sich bei Azure Machine Learning an, und registrieren Sie Ihr Modell.
   
    Das Fairnessdashboard kann mit registrierten oder nicht registrierten Modellen integriert werden. Registrieren Sie wie folgt Ihr Modell in Azure Machine Learning:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Berechnen Sie die Fairnessmetriken im Voraus.

    Erstellen Sie mit dem Paket `metrics` von Fairlearn ein Dashboardwörterbuch. Die `_create_group_metric_set`-Methode verfügt über ähnliche Argumente wie der Dashboard-Konstruktor, mit dem Unterschied, dass die sensiblen Attribute als Wörterbuch übergeben werden (um sicherzustellen, dass Namen verfügbar sind). Außerdem müssen Sie beim Aufruf dieser Methode den Vorhersagetyp angeben (in diesem Fall Binärklassifizierung).

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Laden Sie die im Voraus berechneten Fairnessmetriken hoch.
    
    Importieren Sie nun das Paket `azureml.contrib.fairness`, um den Upload auszuführen:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Erstellen Sie ein Experiment, dann eine Ausführung, und laden Sie das Dashboard dort hoch:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Überprüfen Sie das Fairnessdashboard in Azure Machine Learning Studio.

    Wenn Sie die obigen Schritte ausführen (Hochladen generierter Erkenntnisse zur Fairness in Azure Machine Learning), können Sie das Fairnessdashboard in [Azure Machine Learning Studio](https://ml.azure.com) anzeigen. Dieses Dashboard ist das gleiche Visualisierungsdashboard, das auch in Fairlearn bereitgestellt wird, mit dem Sie die Unterschiede zwischen den Untergruppen Ihres sensiblen Attributs analysieren können (z. B. männlich vs. weiblich).
    Sie haben zwei Möglichkeiten, um auf das Visualisierungsdashboard in Azure Machine Learning-Studio zuzugreifen:

    * **Bereich „Experimente“ (Vorschau)**
    1. Klicken Sie im linken Bereich auf **Experimente**, damit Ihnen eine Liste mit Experimenten angezeigt wird, die Sie in Azure Machine Learning ausgeführt haben.
    1. Wählen Sie ein bestimmtes Experiment aus, um alle Ausführungen in diesem Experiment anzuzeigen.
    1. Klicken Sie auf eine Ausführung und dann auf die Registerkarte **Fairness**, um das Dashboard zur Erklärungsvisualisierung anzuzeigen.


    [![Fairnessdashboard](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Bereich „Modelle“**
    1. Wenn Sie Ihr ursprüngliches Modell mit den obigen Schritten registriert haben, können Sie im linken Bereich auf **Modelle** klicken, um es anzuzeigen.
    1. Klicken Sie auf ein Modell und dann auf die Registerkarte **Fairness**, um das Dashboard zur Erklärungsvisualisierung anzuzeigen.

    Weitere Informationen zum Visualisierungsdashboard und den enthaltenen Funktionen finden Sie im [Fairlearn-Benutzerhandbuch](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard).

## <a name="upload-fairness-insights-for-multiple-models"></a>Hochladen von Erkenntnissen zur Fairness für mehrere Modelle

Wenn Sie mehrere Modelle vergleichen und sehen möchten, wie sich deren Fairnessbewertungen unterscheiden, können Sie mehr als ein Modell an das Visualisierungsdashboard übergeben und durch die jeweiligen Kompromisse zwischen Leistung und Fairness navigieren.

1. Trainieren Sie Ihre Modelle:
    
    Zusätzlich zum vorherigen logistischen Regressionsmodell erstellen Sie nun einen zweiten Klassifizierer basierend auf einem Support-Vector-Machine-Schätzer und laden mithilfe des Pakets `metrics` von Fairlearn ein Fairnessdashboard-Wörterbuch hoch. Beachten Sie, dass hier die Schritte zum Laden und Vorverarbeiten der Daten übersprungen werden und direkt zur Modelltrainingsphase übergegangen wird.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. Registrieren Sie Ihre Modelle.

    Registrieren Sie als Nächstes beide Modelle in Azure Machine Learning. Speichern Sie die Ergebnisse zur Vereinfachung für nachfolgende Methodenaufrufe in einem Wörterbuch, das die `id` des registrierten Modells (Zeichenfolge im Format `name:version`) dem Prädiktor selbst zuordnet:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Laden Sie das Fairlearn-Dashboard lokal.

    Bevor Sie die Erkenntnisse zur Fairness in Azure Machine Learning hochladen, können Sie die Vorhersagen in einem lokal aufgerufenen Fairlearn-Dashboard untersuchen. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Berechnen Sie die Fairnessmetriken im Voraus.

    Erstellen Sie mit dem Paket `metrics` von Fairlearn ein Dashboardwörterbuch.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Laden Sie die im Voraus berechneten Fairnessmetriken hoch.
    
    Importieren Sie nun das Paket `azureml.contrib.fairness`, um den Upload auszuführen:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Erstellen Sie ein Experiment, dann eine Ausführung, und laden Sie das Dashboard dort hoch:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Wie im vorherigen Abschnitt können Sie auf eine der oben beschriebenen Arten (über **Experimente** oder **Modelle**) in Azure Machine Learning Studio auf das Visualisierungsdashboard zugreifen und die beiden Modelle hinsichtlich der Fairness und Leistung vergleichen.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Hochladen von nicht entschärften und entschärften Erkenntnissen zur Fairness

Sie können die [Entschärfungsalgorithmen](https://fairlearn.github.io/master/user_guide/mitigation.html) von Fairlearn verwenden, die von diesen generierten entschärften Modelle mit dem ursprünglichen, nicht entschärften Modell vergleichen und durch die jeweiligen Kompromisse zwischen Leistung und Fairness der verglichenen Modelle navigieren.

Ein Beispiel für die Verwendung des [Rastersuche-Entschärfungsalgorithmus](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) (der mehrere entschärfte Modelle mit unterschiedlichen Kompromissen zwischen Fairness und Leistung erstellt) finden Sie in diesem [Beispielnotebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Das Hochladen von Erkenntnissen zur Fairness für mehrere Modelle in einer einzigen Ausführung ermöglicht einen Vergleich der Modelle in Bezug auf die Fairness und Leistung. Sie können auf jedes der im Modellvergleichsdiagramm angezeigten Modelle klicken, um die Erkenntnisse zur Fairness des jeweiligen Modells im Detail anzuzeigen.


[![Fairlearn-Dashboard für Modellvergleich](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr über Modellfairness.](concept-fairness-ml.md)

[Sehen Sie sich Azure Machine Learning-Beispielnotebooks zur Fairness an.](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
