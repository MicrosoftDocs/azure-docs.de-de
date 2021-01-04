---
title: Ausführen des Spracherkennungscontainers in Kubernetes Service
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Stellen Sie den Sprachenerkennungscontainer mit einem ausgeführten Beispiel in Azure Kubernetes Service bereit, und testen Sie ihn in einem Webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2a6e7ee125cac66b48e666a766c39813aa38fb1a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968501"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Bereitstellen des Spracherkennungscontainers der Textanalyse in Azure Kubernetes Service

Hier erfahren Sie, wie Sie den Sprachenerkennungscontainer bereitstellen. In dem Verfahren wird gezeigt, wie Sie die lokalen Docker-Container erstellen, Container per Push in Ihre eigene private Containerregistrierung übertragen, den Container in einem Kubernetes-Cluster ausführen und ihn in einem Webbrowser testen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Verfahren müssen mehrere Tools lokal installiert und ausgeführt werden. Verwenden Sie nicht Azure Cloud Shell.

* Verwenden Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services) erstellen, bevor Sie beginnen.
* [Git](https://git-scm.com/downloads) für Ihr Betriebssystem, um das in diesem Verfahren verwendete [Beispiel](https://github.com/Azure-Samples/cognitive-services-containers-samples) klonen zu können.
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Docker-Engine](https://www.docker.com/products/docker-engine). (Vergewissern Sie sich, dass die Docker-Befehlszeilenschnittstelle in einem Konsolenfenster funktioniert.)
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Eine Azure-Ressource mit dem korrekten Tarif. Nicht alle Tarife können mit diesem Container verwendet werden:
  * Ressource **Textanalyse** nur mit F0- oder Standard-Tarif.
  * Ressource **Cognitive Services** mit dem S0-Tarif.

## <a name="running-the-sample"></a>Ausführen des Beispiels

Im Rahmen dieses Verfahrens wird das Cognitive Services-Containerbeispiel für die Sprachenerkennung geladen und ausgeführt. Das Beispiel enthält zwei Container: einen für die Clientanwendung und einen für den Cognitive Services-Container. Wir übertragen beide Images per Push in die Azure Container Registry. Nachdem sie sich in Ihrer eigenen Registrierung befinden, erstellen Sie eine Azure Kubernetes Service-Instanz, um auf diese Images zuzugreifen und die Container auszuführen. Wenn die Container ausgeführt werden, verwenden Sie die Befehlszeilenschnittstelle **kubectl**, um die Leistung der Container zu überwachen. Greifen Sie über eine HTTP-Anforderung auf die Clientanwendung zu, und sehen Sie sich die Ergebnisse an.

![Konzeptidee zum Ausführen von Beispielcontainern](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Die Beispielcontainer

Das Beispiel enthält zwei Containerimages, eines davon für die Front-End-Website. Bei dem zweiten Image handelt es sich um den Sprachenerkennungscontainer, der die erkannte Sprache (Kultur) eines Texts zurückgibt. Am Ende des Verfahrens kann auf beide Container über eine externe IP-Adresse zugegriffen werden.

### <a name="the-language-frontend-container"></a>Der Container „language-frontend“

Diese Website entspricht Ihrer eigenen clientseitigen Anwendung, die Anforderungen an den Endpunkt für die Sprachenerkennung richtet. Nach Abschluss des Verfahrens erhalten Sie die erkannte Sprache einer Zeichenfolge, indem Sie in einem Browser mit `http://<external-IP>/<text-to-analyze>` auf den Websitecontainer zugreifen. Diese URL kann beispielsweise wie folgt aussehen: `http://132.12.23.255/helloworld!`. Im Browser wird das Ergebnis `English` zurückgegeben.

### <a name="the-language-container"></a>Der Container „language“

In diesem speziellen Verfahren ist der Sprachenerkennungscontainer für beliebige externe Anforderungen verfügbar. Da der Container in keiner Weise verändert wurde, steht die standardmäßige, für Cognitive Services-Container spezifische Sprachenerkennungs-API zur Verfügung.

Für diesen Container handelt es sich bei der API um eine POST-Anforderung für die Sprachenerkennung. Wie bei allen Cognitive Services-Containern können Sie sich anhand der gehosteten Swagger-Informationen (`http://<external-IP>:5000/swagger/index.html`) ausführlicher über den Container informieren.

Der Port 5000 ist der Standardport für Cognitive Services-Container.

## <a name="create-azure-container-registry-service"></a>Erstellen des Azure Container Registry-Diensts

Damit der Container in Azure Kubernetes Service bereitgestellt werden kann, muss der Zugriff auf die Containerimages möglich sein. Erstellen Sie Ihren eigenen Azure Container Registry-Dienst, um die Images zu hosten.

1. Melden Sie sich bei der Azure-Befehlszeilenschnittstelle an.

    ```azurecli-interactive
    az login
    ```

1. Erstellen Sie eine Ressourcengruppe namens `cogserv-container-rg` für alle Ressourcen, die im Rahmen dieses Verfahrens erstellt werden.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Erstellen Sie Ihre eigene Azure Container Registry-Instanz mit einer Kombination aus Ihrem Namen und `registry` (Beispiel: `pattyregistry`). Fügen Sie keine Binde- oder Unterstriche in den Namen ein.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Speichern Sie die Ergebnisse, um die Eigenschaft **loginServer** zu erhalten. Sie wird später in der Datei `language.yml` als Teil der Adresse des gehosteten Containers verwendet.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Melden Sie sich bei Ihrer Containerregistrierung an. Sie müssen angemeldet sein, um Images per Push in die Registrierung übertragen zu können.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Abrufen des Docker-Images für die Website

1. Der in diesem Verfahren verwendete Beispielcode befindet sich im Repository mit den Cognitive Services-Containerbeispielen. Klonen Sie das Repository, um eine lokale Kopie des Beispiels zu erhalten.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Wenn sich das Repository auf Ihrem lokalen Computer befindet, suchen Sie im Verzeichnis [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) nach der Website. Diese Website fungiert als die Clientanwendung, die die im Sprachenerkennungscontainer gehostete Sprachenerkennungs-API aufruft.  

1. Erstellen Sie das Docker-Image für diese Website. Achten Sie beim Ausführen des folgenden Befehls darauf, dass sich die Konsole im Verzeichnis [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) mit dem Dockerfile befindet:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Wenn Sie die Version für Ihre Containerregistrierung nachverfolgen möchten, fügen Sie das Tag mit einem Versionsformat hinzu (beispielsweise `v1`).

1. Übertragen des Images mithilfe von Push an Ihre Containerregistrierung. Dies kann einige Minuten dauern.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Sollte ein Fehler vom Typ `unauthorized: authentication required` auftreten, melden Sie sich mit dem Befehl `az acr login --name <your-container-registry-name>` an. 

    Nach Abschluss des Prozesses sollten die Ergebnisse in etwa wie folgt aussehen:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Abrufen des Docker-Images für die Sprachenerkennung

1. Pullen Sie die neueste Version des Docker-Images auf den lokalen Computer. Dies kann einige Minuten dauern. Sollte eine neuere Version dieses Containers vorhanden sein, ändern Sie den Wert von `1.1.006770001-amd64-preview` in die neuere Version.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Markieren Sie das Image mit Ihrer Containerregistrierung. Suchen Sie die neueste Version, und ersetzen Sie die Version `1.1.006770001-amd64-preview`, falls Sie über eine neuere Version verfügen. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Übertragen des Images mithilfe von Push an Ihre Containerregistrierung. Dies kann einige Minuten dauern.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Abrufen von Anmeldeinformationen für die Containerregistrierung

Die folgenden Schritte sind erforderlich, um die erforderlichen Informationen für die Verbindungsherstellung zwischen Ihrer Containerregistrierung und der Azure Kubernetes Service-Instanz abzurufen, die Sie später in diesem Verfahren erstellen.

1. Erstellen Sie den Dienstprinzipal.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Speichern Sie den `appId`-Ergebniswert für den assignee-Parameter in Schritt 3 (`<appId>`). Speichern Sie das Kennwort (`password`) für den client-secret-Parameter im nächsten Abschnitt (`<client-secret>`).

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Rufen Sie Ihre Containerregistrierungs-ID ab.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Speichern Sie die Ausgabe für den scope-Parameterwert (`<acrId>`) im nächsten Schritt. Er sieht wie folgt aus:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Speichern Sie den vollständigen Wert für Schritt 3 in diesem Abschnitt.

1. Erstellen Sie eine Rollenzuweisung, um den korrekten Zugriff für den AKS-Cluster zu gewähren und die Verwendung von in der Containerregistrierung gespeicherten Images zu ermöglichen. Ersetzen Sie `<appId>` und `<acrId>` durch die Werte aus den beiden vorherigen Schritten.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Erstellen von Azure Kubernetes Service

1. Erstellen Sie den Kubernetes-Cluster. Mit Ausnahme des Namensparameters stammen alle Parameterwerte aus den vorherigen Abschnitten. Wählen Sie einen Namen, der Aufschluss über Ersteller und Zweck gibt (beispielsweise `patty-kube`).

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Dieser Schritt kann einige Minuten dauern. Es wird folgendes Ergebnis ausgegeben:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Der Dienst wird erstellt, verfügt aber noch nicht über den Websitecontainer oder den Sprachenerkennungscontainer.  

1. Rufen Sie die Anmeldeinformationen des Kubernetes-Clusters ab.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Laden der Orchestrierungsdefinition in Ihren Kubernetes-Dienst

In diesem Abschnitt wird die Befehlszeilenschnittstelle **kubectl** verwendet, um mit der Azure Kubernetes Service-Instanz zu kommunizieren.

1. Vergewissern Sie sich vor dem Laden der Orchestrierungsdefinition, dass **kubectl** Zugriff auf die Knoten hat.

    ```console
    kubectl get nodes
    ```

    Die Antwort sieht wie folgt aus:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Kopieren Sie die folgende Datei, und nennen Sie sie `language.yml`. Die Datei enthält die Abschnitte `service` und `deployment` für die beiden Containertypen (Websitecontainer `language-frontend` und Erkennungscontainer `language`).

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Ändern Sie die Bereitstellungszeilen für „language-frontend“ in der Datei `language.yml` auf der Grundlage der folgenden Tabelle, um die Imagenamen, das Clientgeheimnis und die Textanalyseeinstellungen Ihrer eigenen Containerregistrierung hinzufügen.

    Bereitstellungseinstellungen für „language-frontend“|Zweck|
    |--|--|
    |Zeile 32<br> `image`-Eigenschaft|Speicherort des Front-End-Images in Ihrer Containerregistrierung<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Zeile 44<br> `name`-Eigenschaft|Geheimnis der Containerregistrierung für das Image (weiter oben als `<client-secret>` bezeichnet).|

1. Ändern Sie die Bereitstellungszeilen für „language“ in der Datei `language.yml` auf der Grundlage der folgenden Tabelle, um die Imagenamen, das Clientgeheimnis und die Textanalyseeinstellungen Ihrer eigenen Containerregistrierung hinzufügen.

    |Bereitstellungseinstellungen für „language“|Zweck|
    |--|--|
    |Zeile 78<br> `image`-Eigenschaft|Speicherort des Sprachimages in Ihrer Containerregistrierung<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Zeile 95<br> `name`-Eigenschaft|Geheimnis der Containerregistrierung für das Image (weiter oben als `<client-secret>` bezeichnet).|
    |Zeile 91<br> `apiKey`-Eigenschaft|Der Schlüssel Ihrer Textanalyseressource.|
    |Zeile 92<br> `billing`-Eigenschaft|Der Abrechnungsendpunkt für Ihre Textanalyseressource.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Da **apiKey** und **Abrechnungsendpunkt** im Rahmen der Kubernetes-Orchestrierungsdefinition festgelegt werden, müssen sie dem Websitecontainer nicht bekannt sein und nicht zusammen mit der Anforderung übergeben werden. Der Websitecontainer verweist auf den Sprachenerkennungscontainer anhand des Orchestratornamens `language`.

1. Laden Sie die Orchestrierungsdefinitionsdatei für dieses Beispiel aus dem Ordner, in dem Sie die Datei `language.yml` erstellt und gespeichert haben.

    ```console
    kubectl apply -f language.yml
    ```

    Die Antwort lautet:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Abrufen der externen IP-Adressen von Containern

Vergewissern Sie sich für die beiden Container, dass die Dienste `language-frontend` und `language` ausgeführt werden, und rufen Sie die externe IP-Adresse ab.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Falls `EXTERNAL-IP` für den Dienst als ausstehend angezeigt wird, führen Sie den Befehl erneut aus, bis die IP-Adresse angezeigt wird, und fahren Sie erst dann mit dem nächsten Schritt fort.

## <a name="test-the-language-detection-container"></a>Testen des Sprachenerkennungscontainers

Navigieren Sie in einem Browser zu der externen IP-Adresse des Containers `language` aus dem vorherigen Abschnitt: `http://<external-ip>:5000/swagger/index.html`. Sie können das Feature `Try it` der API verwenden, um den Endpunkt für die Sprachenerkennung zu testen.

![Anzeigen der Swagger-Dokumentation des Containers](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testen des Clientanwendungscontainers

Ändern Sie die URL im Browser in die externe IP-Adresse des Containers `language-frontend`. Verwenden Sie dabei das folgende Format: `http://<external-ip>/helloworld`. Der Text `helloworld` aus der englischen Kultur wird als `English` vorhergesagt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Cluster nicht mehr benötigen, löschen Sie die Azure-Ressourcengruppe.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Verwandte Informationen

* [kubectl for Docker Users](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/) (kubectl für Docker-Benutzer)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Containerunterstützung in Azure Cognitive Services](../cognitive-services-container-support.md)