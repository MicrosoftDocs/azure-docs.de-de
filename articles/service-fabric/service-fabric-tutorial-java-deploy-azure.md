---
title: Bereitstellen einer Java-App in einem Service Fabric-Cluster in Azure
description: In diesem Tutorial wird beschrieben, wie Sie eine Java Service Fabric-Anwendung in einem Azure Service Fabric-Cluster bereitstellen.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 89c49ae530b7a4716bc6e8bf0ea6ccb011847eb8
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738911"
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Tutorial: Bereitstellen einer Java-Anwendung in einem Service Fabric-Cluster in Azure

Dieses Tutorial ist der dritte Teil einer Reihe. Es wird beschrieben, wie Sie eine Service Fabric-Anwendung in einem Cluster in Azure bereitstellen.

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines sicheren Linux-Clusters in Azure
> * Übermitteln einer Anwendung an den Cluster

In dieser Tutorialserie lernen Sie Folgendes:

> [!div class="checklist"]
> * [Erstellen einer Service Fabric Reliable Services-Java-Anwendung](service-fabric-tutorial-create-java-app.md)
> * [Bereitstellen und Debuggen der Anwendung in einem lokalen Cluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * Bereitstellen der Anwendung in einem Azure-Cluster
> * [Einrichten der Überwachung und Diagnose für die Anwendung](service-fabric-tutorial-java-elk.md)
> * [Richten Sie CI/CD ein.](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Installieren des Service Fabric SDK für [Mac](service-fabric-get-started-mac.md) oder [Linux](service-fabric-get-started-linux.md)
* [Installieren von Python 3](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Erstellen eines Service Fabric-Clusters in Azure

Mit den folgenden Schritten werden die erforderlichen Ressourcen erstellt, die zum Bereitstellen Ihrer Anwendung in einem Service Fabric-Cluster benötigt werden. Außerdem werden die erforderlichen Ressourcen zum Überwachen der Integrität Ihrer Lösung per ELK-Stack (Elasticsearch, Logstash, Kibana) eingerichtet. [Event Hubs](https://azure.microsoft.com/services/event-hubs/) wird beispielsweise als Senke für Protokolle aus Service Fabric verwendet. Dies wird so konfiguriert, dass Protokolle aus dem Service Fabric-Cluster an Ihre Logstash-Instanz gesendet werden.

1. Öffnen Sie ein Terminal, und laden Sie das folgende Paket herunter, das die benötigten Hilfsskripts und die Vorlagen zum Erstellen der Ressourcen in Azure enthält.

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Anmelden bei Ihrem Azure-Konto

    ```azurecli
    az login
    ```

3. Legen Sie Ihr Azure-Abonnement fest, das Sie zum Erstellen der Ressourcen verwenden möchten.

    ```azurecli
    az account set --subscription [SUBSCRIPTION-ID]
    ```

4. Führen Sie im Ordner *service-fabric-java-quickstart/AzureCluster* den folgenden Befehl aus, um in Key Vault ein Clusterzertifikat zu erstellen. Dieses Zertifikat wird verwendet, um Ihren Service Fabric-Cluster zu schützen. Geben Sie die Region (muss der Region Ihres Service Fabric-Clusters entsprechen), den Namen der Schlüsseltresor-Ressourcengruppe, den Namen des Schlüsseltresors, das Zertifikatkennwort und den Cluster-DNS-Namen an.

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]

    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    Mit dem obigen Befehl werden die folgenden Informationen zurückgegeben, die Sie sich zur späteren Verwendung notieren sollten.

    ```output
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Erstellen einer Ressourcengruppe für das Speicherkonto, in dem Ihre Protokolle gespeichert sind

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Erstellen eines Speicherkontos, das zum Speichern der erstellten Protokolle verwendet wird

    ```azurecli
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage

    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. Greifen Sie auf das [Azure-Portal](https://portal.azure.com) zu, und navigieren Sie zur Registerkarte **Shared Access Signature** für Ihr Storage-Konto. Generieren Sie das SAS-Token wie folgt.

    ![Generieren der SAS für Storage](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Kopieren Sie die Konto-SAS-URL, und bewahren Sie sie für die Erstellung Ihres Service Fabric-Clusters auf. Die URL lautet in etwa wie folgt:

    ```output
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Erstellen Sie eine Ressourcengruppe, in der die Event Hub-Ressourcen enthalten sind. Event Hubs wird zum Senden von Nachrichten von Service Fabric an den Server verwendet, auf dem die ELK-Ressourcen ausgeführt werden.

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name testeventhubsrg
    ```

10. Erstellen Sie mit dem folgenden Befehl eine Event Hubs-Ressource. Befolgen Sie die Anweisungen in den Aufforderungen, um Details für namespaceName, eventHubName, consumerGroupName, sendAuthorizationRule und receiveAuthorizationRule einzugeben.

    ```azurecli
    az group deployment create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json

    Example:
    az group deployment create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Kopieren Sie den Inhalt des Felds **output** in der JSON-Ausgabe des vorherigen Befehls. Die Absenderinformationen werden beim Erstellen des Service Fabric-Clusters verwendet. Der Empfängername und der Schlüssel sollten zur Verwendung im nächsten Tutorial gespeichert werden, wenn der Logstash-Dienst konfiguriert wird, um Nachrichten vom Event Hub zu erhalten. Das folgende Blob ist eine JSON-Beispielausgabe:

    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. Führen Sie das Skript *eventhubssastoken.py* aus, um die SAS-URL für die erstellte EventHubs-Ressource zu generieren. Diese SAS-URL wird vom Service Fabric-Cluster verwendet, um Protokolle an Event Hubs zu senden. Das Ergebnis ist, dass die **sender** -Richtlinie zum Generieren der URL verwendet wird. Das Skript gibt die SAS-URL für die Event Hubs-Ressource zurück, die im folgenden Schritt verwendet wird:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Kopieren Sie den Wert des Felds **sr** in der zurückgegebenen JSON-Ausgabe. Der Wert des Felds **sr** ist das SAS-Token für EventHubs. Die folgende URL ist ein Beispiel für das Feld **sr** :

    ```output
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    Ihre SAS-URL für EventHubs hat die folgende Struktur: `https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>`. Zum Beispiel, `https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender`

12. Öffnen Sie die Datei *sfdeploy.parameters.json* , und ersetzen Sie den folgenden Inhalt aus den vorherigen Schritten: [SAS-URL-STORAGE-ACCOUNT] aus Schritt 8. [SAS-URL-EVENT-HUBS] aus Schritt 11.

    ```json
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. Öffnen Sie **sfdeploy.parameters.json** . Ändern Sie die folgenden Parameter, und speichern Sie anschließend die Datei:
    - **clusterName** . Verwenden Sie nur Kleinbuchstaben und Zahlen.
    - **adminUserName** (darf kein leerer Wert sein)
    - **adminPassword** (darf kein leerer Wert sein)

14. Führen Sie den folgenden Befehl aus, um Ihren Service Fabric-Cluster zu erstellen:

    ```azurecli
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>Bereitstellen Ihrer Anwendung im Cluster

1. Vor dem Bereitstellen der Anwendung müssen Sie der Datei *Voting/VotingApplication/ApplicationManifest.xml* den folgenden Codeausschnitt hinzufügen. Das Feld **X509FindValue** ist der Fingerabdruck, der in Schritt 4 des Abschnitts **Erstellen eines Service Fabric-Clusters in Azure** zurückgegeben wird. Dieser Codeausschnitt ist unter dem Feld **ApplicationManifest** (root-Feld) geschachtelt.

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. Zum Bereitstellen Ihrer Anwendung in diesem Cluster müssen Sie SFCTL verwenden, um eine Verbindung mit dem Cluster herzustellen. Für SFCTL wird eine PEM-Datei mit dem öffentlichen und privaten Schlüssel benötigt, um eine Verbindung mit dem Cluster herzustellen. Führen Sie daher den folgenden Befehl aus, um eine PEM-Datei mit dem öffentlichen und privaten Schlüssel zu erstellen. 

    ```bash
    openssl pkcs12 -in <clustername>.<region>.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Führen Sie den folgenden Befehl aus, um eine Verbindung mit dem Cluster herzustellen.

    ```bash
    sfctl cluster select --endpoint https://<clustername>.<region>.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Navigieren Sie zum Bereitstellen Ihrer Anwendung zum Ordner *Voting/Scripts* , und führen Sie das Skript **install.sh** aus.

    ```bash
    ./install.sh
    ```

5. Öffnen Sie zum Zugreifen auf Service Fabric Explorer Ihren bevorzugten Browser, und geben Sie `https://testlinuxcluster.westus.cloudapp.azure.com:19080` ein. Wählen Sie im Zertifikatspeicher das Zertifikat aus, das Sie zum Herstellen einer Verbindung mit diesem Endpunkt verwenden möchten. Wenn Sie einen Linux-Computer verwenden, müssen die Zertifikate, die mit dem Skript *new-service-fabric-cluster-certificate.sh* generiert wurden, in Chrome importiert werden, um Service Fabric Explorer anzuzeigen. Wenn Sie einen Macintosh verwenden, müssen Sie die PFX-Datei in Ihrem Schlüsselbund installieren. Sie sehen, dass die Anwendung im Cluster installiert wurde.

    ![SFX Java Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Geben Sie `https://testlinuxcluster.westus.cloudapp.azure.com:8080` ein, um auf Ihre Anwendung zuzugreifen.

    ![Abstimmungs-App – Java Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Führen Sie zum Deinstallieren Ihrer Anwendung aus dem Cluster das Skript *uninstall.sh* im Ordner **Scripts** aus.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines sicheren Linux-Clusters in Azure
> * Erstellen von erforderlichen Ressourcen für die Überwachung per ELK

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Einrichten der Überwachung und Diagnose](service-fabric-tutorial-java-elk.md)
