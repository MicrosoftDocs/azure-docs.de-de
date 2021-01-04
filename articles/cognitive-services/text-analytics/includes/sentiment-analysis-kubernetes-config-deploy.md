---
title: Standpunktanalyse – Kubernetes – Schritte zum Konfigurieren und Bereitstellen
titleSuffix: Azure Cognitive Services
description: Standpunktanalyse – Kubernetes – Schritte zum Konfigurieren und Bereitstellen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ca8d4d725ff25687d1005ddab1964316a147c730
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017857"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Bereitstellen des Standpunktanalyse-Containers in einem AKS-Cluster

1. Öffnen Sie die Azure CLI, und melden Sie sich bei Azure an.

    ```azurecli
    az login
    ```

1. Melden Sie sich bei dem AKS-Cluster an. Ersetzen Sie `your-cluster-name` und `your-resource-group` durch die entsprechenden Werte.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Nachdem dieser Befehl ausgeführt wurde, wird eine Meldung ähnlich der folgenden ausgegeben:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Wenn Sie über mehrere Abonnements in Ihrem Azure-Konto verfügen und der `az aks get-credentials`-Befehl mit einem Fehler zurückgegeben wird, ist ein häufiges Problem, dass Sie das falsche Abonnement verwenden. Stellen Sie den Kontext Ihrer Azure CLI-Sitzung so ein, dass Sie das gleiche Abonnement verwenden, mit dem Sie die Ressourcen erstellt haben, und versuchen Sie es erneut.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Öffnen Sie den Text-Editor Ihrer Wahl. In diesem Beispiel wird Visual Studio Code verwendet.

    ```console
    code .
    ```

1. Erstellen Sie im Text-Editor eine neue Datei namens *sentiment.yaml*, und fügen Sie die folgende YAML darin ein. Achten Sie darauf, dass Sie `billing/value` und `apikey/value` durch Ihre eigenen Werte ersetzen.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Speichern Sie die Datei, und schließen Sie den Text-Editor.
1. Führen Sie den Kubernetes-Befehl `apply` mit der Datei *sentiment.yaml* als Ziel aus:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Nachdem der Befehl die Bereitstellungskonfiguration erfolgreich angewendet hat, wird eine Meldung ähnlich der folgenden Ausgabe angezeigt:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Überprüfen Sie, ob der Pod bereitgestellt wurde:

    ```console
    kubectl get pods
    ```

    Die Ausgabe für den Ausführungsstatus des Pods:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Überprüfen Sie, ob der Dienst verfügbar ist, und rufen Sie die IP-Adresse ab.

    ```console
    kubectl get services
    ```

    Die Ausgabe für den Ausführungsstatus des *sentiment*-Diensts im POD:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
