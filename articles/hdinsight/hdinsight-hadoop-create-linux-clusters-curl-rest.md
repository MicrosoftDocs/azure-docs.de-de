---
title: Erstellen von Apache Hadoop-Clustern mithilfe der Azure-REST-API – Azure
description: Erfahren Sie, wie Sie HDInsight-Cluster erstellen, indem Sie Azure Resource Manager-Vorlagen an die Azure-REST-API senden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 12/10/2019
ms.openlocfilehash: 3ce104e9340c3e93d64b68dcab6f5bd6d2f62493
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020625"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Erstellen von Apache Hadoop-Clustern mithilfe der Azure-REST-API

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Erfahren Sie, wie Sie einen HDInsight-Cluster mit einer Azure Resource Manager-Vorlage und der Azure-REST-API erstellen.

Mit der Azure-REST-API können Sie Verwaltungsvorgänge für Dienste durchführen, die auf der Azure-Plattform gehostet werden. Beispielsweise können Sie neue Ressourcen wie etwa HDInsight-Cluster erstellen.

> [!NOTE]  
> Bei den Schritten in diesem Dokument wird das Hilfsprogramm [curl (https://curl.haxx.se/)](https://curl.haxx.se/)) für die Kommunikation mit der Azure-REST-API verwendet.

## <a name="create-a-template"></a>Erstellen einer Vorlage

Bei Azure Resource Manager-Vorlagen handelt es sich um JSON-Dokumente, mit denen eine **Ressourcengruppe** und alle darin enthaltenen Ressourcen (z.B. HDInsight) beschrieben werden. Diese vorlagenbasierte Vorgehensweise ermöglicht Ihnen, die Ressourcen zu definieren, die Sie für HDInsight in einer Vorlage benötigen.

Das folgende JSON-Dokument ist eine Kombination aus den Vorlagen- und Parameterdateien von [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password). Damit wird ein Linux-basierter Cluster erstellt, bei dem das SSH-Benutzerkonto durch ein Kennwort geschützt ist.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Dieses Beispiel wird in den Schritten im vorliegenden Dokument verwendet. Ersetzen Sie die *value*-Beispiele im Abschnitt **Parameters** mit den Werten für Ihren Cluster.

> [!IMPORTANT]  
> Bei dieser Vorlage wird die Standardanzahl von Workerknoten (4) für einen HDInsight-Cluster verwendet. Wenn Sie mehr als 32 Workerknoten planen, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen.
>
> Weitere Informationen zu Knotengrößen und den damit verbundenen Kosten finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Melden Sie sich bei Ihrem Azure-Abonnement an.

Führen Sie die in [Erste Schritte mit der Azure CLI](/cli/azure/get-started-with-az-cli2) dokumentierten Schritte aus, und stellen Sie mithilfe des Befehls `az login` eine Verbindung mit Ihrem Abonnement her.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

> [!NOTE]  
> Diese Schritte stellen eine verkürzte Version des Abschnitts *Erstellen eines Dienstprinzipals mit Kennwort* des Dokuments [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe von Azure PowerShell](/cli/azure/create-an-azure-service-principal-azure-cli) dar. Mit diesen Schritten wird ein Dienstprinzipal erstellt, der verwendet wird, um die Azure-REST-API zu authentifizieren.

1. Führen Sie an einer Befehlszeile den folgenden Befehl aus, um Ihre Azure-Abonnements aufzulisten.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Wählen Sie in der Liste das Abonnement aus, das Sie verwenden möchten, und notieren Sie sich die Werte in den Spalten **Subscription_ID** und __Tenant_ID__. Speichern Sie diese Werte.

2. Führen Sie den folgenden Befehl zum Erstellen einer Anwendung in Azure Active Directory aus.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Ersetzen Sie die Werte für `--display-name`, `--homepage` und `--identifier-uris` durch Ihre eigenen Werte. Geben Sie ein Kennwort für den neuen Active Directory-Eintrag an.

   > [!NOTE]  
   > Die Werte `--home-page` und `--identifier-uris` müssen nicht auf eine tatsächlich im Internet gehostete Webseite verweisen. Sie müssen eindeutige URIs sein.

   Der von diesem Befehl zurückgegebene Wert ist die __App-ID__ für die neue Anwendung. Speichern Sie diesen Wert.

3. Führen Sie den folgenden Befehl aus, um mithilfe der **App-ID** einen Dienstprinzipal zu erstellen.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     Der von diesem Befehl zurückgegebene Wert ist die __Objekt-ID__. Speichern Sie diesen Wert.

4. Weisen Sie dem Dienstprinzipal mit dem **Objekt-ID**-Wert die Rolle **Besitzer** zu. Verwenden Sie die **Abonnement-ID**, die Sie zuvor erhalten haben.

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Abrufen eines Authentifizierungstokens

Führen Sie den folgenden Befehl aus, um ein Authentifizierungstoken abzurufen:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Legen Sie `$TENANTID`, `$APPID` und `$PASSWORD` auf die zuvor abgerufenen oder verwendeten Werte fest.

Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument enthält.

Das von dieser Anforderung zurückgegebene JSON-Dokument enthält ein Element mit dem Namen **access_token**. Der Wert von **access_token** wird verwendet, um Anforderungen an die REST-API zu authentifizieren.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Gehen Sie wie folgt vor, um eine Ressourcengruppe zu erstellen.

* Legen Sie `$SUBSCRIPTIONID` auf die Abonnement-ID fest, die Sie während der Erstellung des Dienstprinzipals erhalten haben.
* Legen Sie`$ACCESSTOKEN` auf das Zugriffstoken fest, das Sie im vorherigen Schritt erhalten haben.
* Ersetzen Sie `DATACENTERLOCATION` durch das Rechenzentrum, in dem Sie die Ressourcengruppe und die Ressourcen erstellen möchten. Beispiel: &quot;USA, Süden-Mitte&quot;.
* Legen Sie `$RESOURCEGROUPNAME` auf den Namen fest, den Sie für diese Gruppe verwenden möchten:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument mit Informationen zur Gruppe enthält. Das Element `"provisioningState"` enthält den Wert `"Succeeded"`.

## <a name="create-a-deployment"></a>Erstellen einer Bereitstellung

Stellen Sie mit dem folgenden Befehl die Vorlage für die Ressourcengruppe bereit.

* Legen Sie `$DEPLOYMENTNAME` auf den Namen fest, den Sie für diese Bereitstellung verwenden möchten.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Wenn Sie die Vorlage in einer Datei gespeichert haben, können Sie anstelle von `-d "{ template and parameters}"` den folgenden Befehl verwenden:
>
> `--data-binary "@/path/to/file.json"`

Wenn die Anforderung erfolgreich ist, erhalten Sie eine 2xx-Antwort, deren Text ein JSON-Dokument mit Informationen zum Bereitstellungsvorgang enthält.

> [!IMPORTANT]  
> Die Bereitstellung war bereits übermittelt, aber noch nicht abgeschlossen. Es kann mehrere Minuten dauern (in der Regel etwa 15), bis die Bereitstellung abgeschlossen ist.

## <a name="check-the-status-of-a-deployment"></a>Überprüfen des Bereitstellungsstatus

Verwenden Sie den folgenden Befehl, um den Status der Bereitstellung zu prüfen:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Dieser Befehl gibt ein JSON-Dokument mit Informationen zum Bereitstellungsvorgang zurück. Das Element `"provisioningState"` enthält den Status der Bereitstellung. Wenn dieses Element den Wert `"Succeeded"` aufweist, wurde die Bereitstellung erfolgreich abgeschlossen.

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](./hdinsight-hadoop-customize-cluster-linux.md#access-control) an.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einen HDInsight-Cluster erfolgreich erstellt haben, nutzen Sie die folgenden Informationen, um zu erfahren, wie Sie mit Ihrem Cluster arbeiten.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-Cluster

* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von MapReduce mit HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase Cluster

* [Erste Schritte mit Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Entwickeln von Java-Anwendungen für Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-Cluster

* [Entwickeln von Java-Topologien für Apache Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Verwenden von Python-Komponenten in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Bereitstellen und Überwachen von Topologien mit Apache Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
