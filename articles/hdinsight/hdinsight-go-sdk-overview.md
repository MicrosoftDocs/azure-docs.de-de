---
title: Azure HDInsight SDK für Go
description: Referenzmaterial für die Verwendung des Azure HDInsight SDK für Go- und Apache Hadoop-Cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: 6fdd7b9e5fda92bd75e54ea5b4aad6a3ba6ecbea
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748754"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK für Go (Vorschau)

## <a name="overview"></a>Übersicht
Das HDInsight SDK für Go bietet Klassen und Funktionen, mit denen Sie Ihre HDInsight-Cluster verwalten können. Es enthält Vorgänge zum Erstellen, Löschen, Aktualisieren, Auflisten, Ändern der Größe, Ausführen von Skriptaktionen, Überwachen, Abrufen der Eigenschaften von HDInsight-Clustern und mehr.

> [!NOTE]  
>GoDoc-Referenzmaterial für dieses SDK ist auch [hier verfügbar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein [`go get`-Tool](https://github.com/golang/go/wiki/GoGetTools).
* [Go](https://golang.org/dl/).

## <a name="sdk-installation"></a>SDK-Installation

Führen Sie von GOPATH aus folgenden Befehl aus: `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Authentication

Das SDK muss zunächst für Ihr Azure-Abonnement authentifiziert werden.  Erstellen Sie anhand des Beispiels unten einen Dienstprinzipal, und verwenden Sie ihn für die Authentifizierung. Nachdem dies erfolgt ist, verfügen Sie über eine Instanz von `ClustersClient` mit vielen Funktionen (in den Abschnitten unten beschrieben), die zum Durchführen von Verwaltungsvorgängen verwendet werden können.

> [!NOTE]  
> Neben dem Beispiel unten gibt es noch andere Möglichkeiten der Authentifizierung, die für Ihre Anforderungen unter Umständen besser geeignet sind. Alle Funktionen werden im Folgenden beschrieben: [Authentifizierungsmethoden im Azure SDK für Go](/azure/go/azure-sdk-go-authorization).

### <a name="authentication-example-using-a-service-principal"></a>Beispiel für die Authentifizierung mit einem Dienstprinzipal

Melden Sie sich zuerst bei [Azure Cloud Shell](https://shell.azure.com/bash) an. Vergewissern Sie sich, dass Sie derzeit das Abonnement verwenden, in dem der Dienstprinzipal erstellt werden soll.

```azurecli-interactive
az account show
```

Die Informationen zu Ihrem Abonnement werden im JSON-Format angezeigt.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Wenn Sie nicht am richtigen Abonnement angemeldet sind, können Sie das richtige auswählen, indem Sie Folgendes ausführen: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Falls Sie den HDInsight-Ressourcenanbieter nicht bereits mit einer anderen Funktion registriert haben (z. B. durch das Erstellen eines HDInsight-Clusters über das Azure-Portal), müssen Sie dies vor dem Authentifizieren durchführen. Dies ist über [Azure Cloud Shell](https://shell.azure.com/bash) möglich, indem Sie den folgenden Befehl ausführen:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Wählen Sie als Nächstes einen Namen für Ihren Dienstprinzipal aus, und erstellen Sie ihn mit dem folgenden Befehl:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Die Dienstprinzipalinformationen werden im JSON-Format angezeigt.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Kopieren Sie den unten angegebenen Codeausschnitt, und geben Sie für `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET` und `SUBSCRIPTION_ID` die Zeichenfolgen aus dem JSON-Code ein, der nach dem Ausführen des Befehls zurückgegeben wurde, um den Dienstprinzipal zu erstellen.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Clusterverwaltung

> [!NOTE]  
> In diesem Abschnitt wird davon ausgegangen, dass Sie bereits eine `ClusterClient`-Instanz authentifiziert und in einer Variablen mit dem Namen `client` gespeichert haben. Eine Anleitung zum Authentifizieren und Abrufen eines `ClusterClient`-Elements finden Sie oben im Abschnitt „Authentifizierung“.

### <a name="create-a-cluster"></a>Erstellen eines Clusters

Sie können einen neuen Cluster erstellen, indem Sie `client.Create()` aufrufen. 

#### <a name="example"></a>Beispiel

In diesem Beispiel wird gezeigt, wie Sie einen [Apache Spark](https://spark.apache.org/)-Cluster mit zwei Hauptknoten und einem Workerknoten erstellen.

> [!NOTE]  
> Sie müssen zuerst wie unten beschrieben eine Ressourcengruppe und ein Speicherkonto erstellen. Wenn Sie diese Komponenten bereits erstellt haben, können Sie diese Schritte überspringen.

##### <a name="creating-a-resource-group"></a>Erstellen einer Ressourcengruppe

Sie können eine Ressourcengruppe erstellen, indem Sie mit [Azure Cloud Shell](https://shell.azure.com/bash) Folgendes ausführen:

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Erstellen eines Speicherkontos

Sie können ein Speicherkonto erstellen, indem Sie mit [Azure Cloud Shell](https://shell.azure.com/bash) Folgendes ausführen:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Führen Sie jetzt den folgenden Befehl aus, um den Schlüssel für Ihr Speicherkonto abzurufen (Sie benötigen ihn, um einen Cluster zu erstellen):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Mit dem unten angegebenen Go-Codeausschnitt wird ein Spark-Cluster mit zwei Hauptknoten und einem Workerknoten erstellt. Geben Sie gemäß den Anweisungen in den Kommentaren Werte in die leeren Variablen ein, und ändern Sie ggf. auch andere Parameter, um diese an Ihre Anforderungen anzupassen.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Abrufen von Clusterdetails

Rufen Sie die Eigenschaften für einen Cluster wie folgt ab:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Beispiel

Sie können `get` verwenden, um zu bestätigen, dass die Erstellung des Clusters erfolgreich war.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

Die Ausgabe sollte wie folgt aussehen:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Auflisten der Cluster

#### <a name="list-clusters-under-the-subscription"></a>Auflisten von Clustern unter dem Abonnement

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Auflisten von Clustern nach Ressourcengruppe

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Sowohl `List()` als auch `ListByResourceGroup()` geben eine `ClusterListResultPage`-Struktur zurück. Sie können `Next()` aufrufen, um die nächste Seite zu erhalten. Dies kann wiederholt werden, bis `ClusterListResultPage.NotDone()` den Wert `false` zurückgibt (wie im Beispiel unten zu sehen).

#### <a name="example"></a>Beispiel

Im folgenden Beispiel werden die Eigenschaften aller Cluster für das aktuelle Abonnement ausgegeben:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Löschen eines Clusters

Löschen Sie einen Cluster wie folgt:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Aktualisieren von Clustermarkierungen

Sie können die Markierungen eines Clusters wie folgt aktualisieren:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Beispiel

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Ändern der Clustergröße

Sie können die Anzahl von Workerknoten für einen Cluster ändern, indem Sie wie folgt eine neue Größe angeben:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Clusterüberwachung

Das HDInsight Management SDK kann auch verwendet werden, um die Überwachung Ihrer Cluster per Operations Management Suite (OMS) zu verwalten.

Auf ähnliche Weise wie Sie `ClusterClient` zur Verwendung für Verwaltungsvorgänge erstellt haben, müssen Sie `ExtensionClient` zur Verwendung für Überwachungsvorgänge erstellen. Wenn Sie den oben genannten Abschnitt zur Authentifizierung abgeschlossen haben, können Sie `ExtensionClient` wie folgt erstellen:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Bei den unten aufgeführten Überwachungsbeispielen wird vorausgesetzt, dass Sie bereits einen `ExtensionClient` namens `extClient` initialisiert und den zugehörigen `Authorizer` wie oben gezeigt festgelegt haben.

### <a name="enable-oms-monitoring"></a>Aktivieren der OMS-Überwachung

> [!NOTE]  
> Sie müssen über einen vorhandenen Log Analytics-Arbeitsbereich verfügen, um die OMS-Überwachung zu ermöglichen. Falls Sie diesen noch nicht erstellt haben, helfen Ihnen die Informationen im folgenden Artikel weiter: [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md).

Aktivieren Sie die OMS-Überwachung in Ihrem Cluster wie folgt:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Anzeigen des Status der OMS-Überwachung

Rufen Sie den Status von OMS in Ihrem Cluster wie folgt ab:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Deaktivieren der OMS-Überwachung

Deaktivieren Sie OMS in Ihrem Cluster wie folgt:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Skriptaktionen

HDInsight verfügt über eine Konfigurationsfunktion mit der Bezeichnung „Skriptaktionen“, mit der benutzerdefinierte Skripts zum Anpassen des Clusters aufgerufen werden.

> [!NOTE]  
> Weitere Informationen zur Verwendung von Skriptaktionen finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](./hdinsight-hadoop-customize-cluster-linux.md).

### <a name="execute-script-actions"></a>Ausführen von Skriptaktionen

Sie können Skriptaktionen für einen Cluster wie folgt ausführen:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

Für die Vorgänge „Skriptaktion löschen“ und „Permanente Skriptaktionen auflisten“ müssen Sie `ScriptActionsClient` erstellen – in ähnlicher Weise wie Sie `ClusterClient` für Verwaltungsvorgänge erstellt haben. Wenn Sie den oben genannten Abschnitt zur Authentifizierung abgeschlossen haben, können Sie `ScriptActionsClient` wie folgt erstellen:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Bei den unten aufgeführten Beispielen für Skriptaktionen wird vorausgesetzt, dass Sie bereits einen `ScriptActionsClient` namens `scriptActionsClient` initialisiert und den zugehörigen `Authorizer` wie oben gezeigt festgelegt haben.

### <a name="delete-script-action"></a>Löschen der Skriptaktion

Löschen Sie eine angegebene permanente Skriptaktion für einen Cluster wie folgt:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Auflisten von permanenten Skriptaktionen

> [!NOTE]  
> `ListByCluster()` gibt eine `ScriptActionsListPage`-Struktur zurück. Sie können `Next()` aufrufen, um die nächste Seite zu erhalten. Dies kann wiederholt werden, bis `ClusterListResultPage.NotDone()` den Wert `false` zurückgibt (wie im Beispiel unten zu sehen).

Listen Sie alle permanenten Skriptaktionen für den angegebenen Cluster wie folgt auf:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Beispiel

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Auflisten des Ausführungsverlaufs aller Skripts

Für diesen Vorgang müssen Sie `ScriptExecutionHistoryClient` erstellen. Die Vorgehensweise ist dabei ähnlich wie bei der Erstellung von `ClusterClient`. Wenn Sie den oben genannten Abschnitt zur Authentifizierung abgeschlossen haben, können Sie `ScriptActionsClient` wie folgt erstellen:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Bei den unten aufgeführten Vorgängen wird vorausgesetzt, dass Sie bereits einen `ScriptExecutionHistoryClient` namens `scriptExecutionHistoryClient` initialisiert und den zugehörigen `Authorizer` wie oben gezeigt festgelegt haben.

Listen Sie den Ausführungsverlauf aller Skripts für den angegebenen Cluster wie folgt auf:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Beispiel

In diesem Beispiel werden alle Details zu allen erfolgten Skriptausführungen ausgegeben.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Untersuchen des [GoDoc-Referenzmaterials](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight). GoDocs stellen eine Referenzdokumentation für alle Funktionen im SDK bereit.
