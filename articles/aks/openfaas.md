---
title: Verwenden von OpenFaaS mit Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie OpenFaaS in einem Azure Kubernetes Service-Cluster (AKS) bereitstellen und verwenden, um serverlose Funktionen mit Containern zu erstellen.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747767"
---
# <a name="using-openfaas-on-aks"></a>Verwenden von OpenFaaS mit AKS

[OpenFaaS][open-faas] ist ein Framework zum Erstellen serverloser Funktionen mithilfe von Containern. Das Open-Source-Projekt erfreut sich in der Community großer Beliebtheit. In diesem Dokument werden die Installation und Verwendung von OpenFaaS in einem Azure Kubernetes Service-Cluster (AKS) beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um die Schritte in diesem Artikel ausführen zu können.

* Grundlegende Kenntnisse von Kubernetes
* Einen AKS-Cluster (Azure Kubernetes Service) und konfigurierte AKS-Anmeldeinformationen in Ihrem Entwicklungssystem
* Eine Installation der Azure CLI auf Ihrem Entwicklungssystem.
* Auf Ihrem System installierte Git-Befehlszeilentools

## <a name="add-the-openfaas-helm-chart-repo"></a>Hinzufügen des OpenFaaS-Helm-Diagrammrepositorys

Navigieren Sie zu [https://shell.azure.com](https://shell.azure.com), um Azure Cloud Shell in Ihrem Browser zu öffnen.

OpenFaaS verwaltet eigene Helm-Diagramme, um bei allen Änderungen auf dem neuesten Stand zu bleiben.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>Bereitstellen von OpenFaaS

Es empfiehlt sich, OpenFaaS und OpenFaaS Funktionen in einem eigenen Kubernetes-Namespace zu speichern.

Erstellen Sie einen Namespace für das OpenFaaS-System und die Funktionen:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Generieren Sie ein Kennwort für die Benutzeroberfläche des OpenFaaS-Portals und die REST-API:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

Sie können den Wert des Geheimnisses mit `echo $PASSWORD` abrufen.

Das hier erstellte Kennwort wird vom Helm-Diagramm verwendet, um eine Standardauthentifizierung auf dem OpenFaaS-Gateway zu ermöglichen, das über einen Cloudlastenausgleich im Internet verfügbar gemacht wird.

Ein Helm-Diagramm für OpenFaaS ist im geklonten Repository enthalten. Verwenden Sie dieses Diagramm, um OpenFaaS in Ihrem AKS-Cluster bereitzustellen.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Ausgabe:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Es wird eine öffentliche IP-Adresse für den Zugriff auf das OpenFaaS-Gateway erstellt. Sie rufen diese IP-Adresse mit dem Befehl [kubectl get service][kubectl-get] ab. Es dauert möglicherweise einige Zeit, bis die IP-Adresse dem Dienst zugewiesen wird.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Ausgabe

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Browsen Sie zum Testen des OpenFaaS-Systems zur externen IP-Adresse an Port 8080 – in diesem Beispiel `http://52.186.64.52:8080`. Sie werden aufgefordert, sich anzumelden. Um Ihr Kennwort abzurufen, geben Sie `echo $PASSWORD` ein.

![OpenFaaS-Benutzeroberfläche](media/container-service-serverless/openfaas.png)

Installieren Sie abschließend die OpenFaaS-Befehlszeilenschnittstelle. In diesem Beispiel wird brew verwendet. Weitere Optionen finden Sie in der [Dokumentation zur OpenFaaS-Befehlszeilenschnittstelle][open-faas-cli].

```console
brew install faas-cli
```

Legen Sie `$OPENFAAS_URL` auf die öffentliche IP-Adresse fest, die weiter oben angegeben ist.

Anmelden mit der Azure-Befehlszeilenschnittstelle:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Erstellen der ersten Funktion

Nachdem OpenFaaS einsatzbereit ist, erstellen Sie eine Funktion über das OpenFaaS-Portal.

Klicken Sie auf **Neue Funktion bereitstellen** , und suchen Sie nach **Figlet** . Wählen Sie die Figlet-Funktion aus, und klicken Sie auf **Bereitstellen** .

![Screenshot, der das Dialogfeld „Neue Funktion bereitstellen“ mit in der Suchzeile eingegebenem Text „figlet“ zeigt.](media/container-service-serverless/figlet.png)

Verwenden Sie zum Aufrufen der Funktion cURL. Ersetzen Sie die IP-Adresse im folgenden Beispiel durch die Ihres OpenFaaS-Gateways.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Ausgabe:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Erstellen der zweiten Funktion

Erstellen Sie nun eine zweite Funktion. Dieses Beispiel wird mithilfe der OpenFaaS-Befehlszeilenschnittstelle bereitgestellt. Es enthält ein benutzerdefiniertes Containerimage und umfasst auch das Abrufen von Daten aus einer Cosmos DB-Instanz. Es müssen mehrere Elemente konfiguriert werden, bevor Sie die Funktion erstellen.

Erstellen Sie zunächst eine neue Ressourcengruppe für Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Stellen Sie eine Cosmos DB-Instanz vom Typ `MongoDB` bereit. Die Instanz benötigt einen eindeutigen Namen. Ersetzen Sie daher `openfaas-cosmos` durch einen in Ihrer Umgebung eindeutigen Wert.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Rufen Sie die Verbindungszeichenfolge für die Cosmos-Datenbank ab, und speichern Sie sie in einer Variable.

Ändern Sie den Wert des `--resource-group`-Arguments in den Namen der Ressourcengruppe und den des `--name`-Arguments in den Namen Ihrer Cosmos DB-Instanz.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Füllen Sie nun die Cosmos DB-Instanz mit Testdaten auf. Erstellen Sie eine Datei namens `plans.json`, und fügen Sie den folgenden JSON-Code ein.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Verwenden Sie das Tool *mongoimport* , um Daten in die Cosmos DB-Instanz zu laden.

Installieren Sie ggf. die MongoDB-Tools. Im folgenden Beispiel werden diese Tools mit brew installiert. Weitere Optionen finden Sie in der [MongoDB-Dokumentation][install-mongo].

```console
brew install mongodb
```

Laden Sie die Daten in die Datenbank.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Ausgabe:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Führen Sie den folgenden Befehl aus, um die Funktion zu erstellen. Ersetzen Sie den Wert des `-g`-Arguments durch die Adresse Ihres OpenFaaS-Gateways.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Nach der Bereitstellung sollte der neu erstellte OpenFaaS-Endpunkt für die Funktion angezeigt werden.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testen Sie die Funktion mit cURL. Ersetzen Sie die IP-Adresse durch die Adresse Ihres OpenFaaS-Gateways.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Ausgabe:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Sie können die Funktion auch auf der OpenFaaS-Benutzeroberfläche testen.

![alt text](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Nächste Schritte

Sie können weiterhin mit dem OpenFaaS-Workshop üben und dabei einen Satz von Praxislabs nutzen, die Themen wie das Erstellen eines eigenen GitHub-Bots, das Verarbeiten von Geheimnissen, das Anzeigen von Metriken und das automatische Skalieren abdecken.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
