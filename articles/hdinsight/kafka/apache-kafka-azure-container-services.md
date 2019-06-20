---
title: Verwenden von Azure Kubernetes Service mit Kafka in HDInsight
description: Hier erfahren Sie, wie Sie über Containerimages, die in Azure Kubernetes Service (AKS) gehostet werden, Kafka in HDInsight verwenden.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 35ef708cdcedc2d7bafedb8bf3686e4b468177df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723683"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Verwenden von Azure Kubernetes Service mit Apache Kafka in HDInsight

Erfahren Sie, wie Sie Azure Kubernetes Service (AKS) mit [Apache Kafka](https://kafka.apache.org/) in HDInsight-Clustern verwenden. Für die Schritte in diesem Dokument wird eine in AKS gehostete Node.js-Anwendung verwendet, um die Konnektivität mit Kafka zu überprüfen. Diese Anwendung verwendet für die Kommunikation mit Kafka das Paket [kafka-node](https://www.npmjs.com/package/kafka-node). Für das ereignisgesteuertes Messaging zwischen dem Browser-Client und dem in AKS gehosteten Back-End-Client wird [Socket.io](https://socket.io/) verwendet.

[Apache Kafka](https://kafka.apache.org) ist eine verteilte Open Source-Streamingplattform, die zum Erstellen von Datenpipelines und Anwendungen mit Echtzeitstreaming verwendet werden kann. Azure Kubernetes Service verwaltet die gehostete Kubernetes-Umgebung und beschleunigt und vereinfacht die Bereitstellung von Containeranwendungen. Mit einem virtuellen Azure-Netzwerk können Sie die beiden Dienste verbinden.

> [!NOTE]  
> Der Schwerpunkt dieses Dokuments liegt auf den Schritten, die erforderlich sind, um die Kommunikation von Azure Kubernetes Service mit Kafka in HDInsight zu ermöglichen. Als eigentliches Beispiel dient nur ein Kafka-Basisclient, um die Funktionsweise der Konfiguration zu veranschaulichen.

## <a name="prerequisites"></a>Voraussetzungen

* [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Ein Azure-Abonnement

In diesem Dokument wird vorausgesetzt, dass Sie mit dem Erstellen und Verwenden der folgenden Azure-Dienste vertraut sind:

* Kafka in HDInsight
* Azure Kubernetes Service
* Virtuelle Azure-Netzwerke

Es wird ebenso vorausgesetzt, dass Sie das [Tutorial zu Azure Kubernetes Service](../../aks/tutorial-kubernetes-prepare-app.md) durchgearbeitet haben. In diesem Lernprogramm werden ein Containerdienst, ein Kubernetes-Cluster und eine Containerregistrierung erstellt. Außerdem wird das Hilfsprogramm `kubectl` konfiguriert.

## <a name="architecture"></a>Architecture

### <a name="network-topology"></a>Netzwerktopologie

HDInsight und AKS verwenden ein virtuelles Azure-Netzwerk als Container für Computerressourcen. Um die Kommunikation zwischen HDInsight und AKS zu ermöglichen, müssen Sie die Kommunikation zwischen diesen Netzwerken aktivieren. Bei den Schritten in diesem Dokument wird für die Netzwerke das VNET-Peering (Virtual Network Peering) verwendet. Andere Verbindungen (z. B. VPN) sollten ebenfalls funktionieren. Weitere Informationen zum Peering finden Sie im Dokument [Peering in virtuellen Netzwerken](../../virtual-network/virtual-network-peering-overview.md).


Das folgende Diagramm veranschaulicht die in diesem Dokument verwendete Netzwerktopologie:

![HDInsight befindet sich in einem virtuellen Netzwerk, AKS in einem anderen, und die Netzwerke sind über Peering miteinander verbunden](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Die Namensauflösung zwischen den beiden durch Peering verbundenen Netzwerken ist nicht aktiviert. Daher wird die IP-Adressierung verwendet. Standardmäßig ist Kafka in HDInsight so konfiguriert, dass anstelle von IP-Adressen Hostnamen zurückgegeben werden, wenn Clients eine Verbindung herstellen. Mit den Schritten in diesem Dokument wird Kafka so geändert, dass es stattdessen die Ankündigung von IP-Adressen verwendet.

## <a name="create-an-azure-kubernetes-service-aks"></a>Erstellen einer Azure Kubernetes Service-Instanz (AKS)

Wenn Sie nicht bereits über einen AKS-Cluster verfügen, verwenden Sie eines der folgenden Dokumente, um Informationen zum Erstellen zu erhalten:

* [Bereitstellen eines Azure Kubernetes Service-Clusters (AKS): Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Bereitstellen eines Azure Kubernetes Service-Clusters (AKS): CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]  
> Während der Installation erstellt AKS ein virtuelles Netzwerk. Dieses Netzwerk wird im nächsten Abschnitt über Peering mit dem für HDInsight erstellten Netzwerk verbunden.

## <a name="configure-virtual-network-peering"></a>Konfigurieren des VNET-Peerings

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option __Ressourcengruppen__ aus, und suchen Sie dann die Ressourcengruppe, die das virtuelle Netzwerk für Ihr AKS-Cluster enthält. Der Name der Ressourcengruppe lautet `MC_<resourcegroup>_<akscluster>_<location>`. Die Einträge `resourcegroup` und `akscluster` enthalten den Namen der Ressourcengruppe, in der Sie den Cluster erstellt haben, und den Namen des Clusters. `location` ist der Speicherort, an dem der Cluster erstellt wurde.

2. Wählen Sie in der Ressourcengruppe die Ressource __Virtuelles Netzwerk__ aus.

3. Wählen Sie __Adressraum__ aus. Notieren Sie sich den aufgelisteten Adressraum.

4. Wählen Sie zum Erstellen eines virtuellen Netzwerks für HDInsight __+ Ressource erstellen__, __Netzwerk__ und dann __Virtuelles Netzwerk__ aus.

    > [!IMPORTANT]  
    > Wenn Sie die Werte für das neue virtuelle Netzwerk eingeben, müssen Sie einen Adressraum verwenden, der sich nicht mit dem des AKS-Clusternetzwerks überschneidet.

    Verwenden Sie den gleichen __Speicherort__ für das virtuelle Netzwerk, den Sie für den AKS-Cluster verwendet haben.

    Warten Sie, bis das virtuelle Netzwerk erstellt wurde, bevor Sie mit dem nächsten Schritt fortfahren.

5. Um das Peering zwischen dem HDInsight-Netzwerk und dem AKS-Clusternetzwerk zu konfigurieren, wählen Sie das virtuelle Netzwerk und dann __Peerings__ aus. Wählen Sie __+ Hinzufügen__ aus, und verwenden Sie die folgenden Werte zum Ausfüllen des Formulars:

   * __Name__: Geben Sie einen eindeutigen Namen für diese Peeringkonfiguration ein.
   * __Virtuelles Netzwerk:__ In diesem Feld können Sie das virtuelle Netzwerk für den **AKS-Cluster** auswählen.

     Übernehmen Sie in allen anderen Feldern die Standardwerte, und wählen Sie dann __OK__ aus, um das Peering zu konfigurieren.

6. Um das Peering zwischen dem AKS-Clusternetzwerk und dem HDInsight-Netzwerk zu konfigurieren, wählen Sie das __virtuelle AKS-Clusternetzwerk__ und dann __Peerings__ aus. Wählen Sie __+ Hinzufügen__ aus, und verwenden Sie die folgenden Werte zum Ausfüllen des Formulars:

   * __Name__: Geben Sie einen eindeutigen Namen für diese Peeringkonfiguration ein.
   * __Virtuelles Netzwerk:__ In diesem Feld können Sie das virtuelle Netzwerk für den __HDInsight-Cluster__ auswählen.

     Übernehmen Sie in allen anderen Feldern die Standardwerte, und wählen Sie dann __OK__ aus, um das Peering zu konfigurieren.

## <a name="install-apache-kafka-on-hdinsight"></a>Installieren von Apache Kafka in HDInsight

Wenn Sie den Cluster „Kafka in HDInsight“ erstellen, müssen Sie dem zuvor für HDInsight erstellten virtuellen Netzwerk beitreten. Weitere Informationen zum Erstellen eines Kafka-Clusters finden Sie im Dokument [Erstellen eines Apache Kafka-Clusters](apache-kafka-get-started.md).

> [!IMPORTANT]  
> Beim Erstellen des Clusters müssen Sie die Option __Erweiterte Einstellungen__ verwenden, um dem für HDInsight erstellten virtuellen Netzwerk beizutreten.

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurieren von Apache Kafka zum Ankündigen der IP-Adresse

In den folgenden Schritten konfigurieren Sie Kafka so, dass anstelle von Domänennamen IP-Adressen angekündigt werden:

1. Wechseln Sie in Ihrem Webbrowsers zu https://CLUSTERNAME.azurehdinsight.net. Ersetzen Sie __CLUSTERNAME__ durch den Namen von Kafka im HDInsight-Cluster.

    Geben Sie bei entsprechender Aufforderung den HTTPS-Benutzernamen und das Kennwort für den Cluster ein. Die Ambari-Webbenutzeroberfläche für den Cluster wird angezeigt.

2. Wählen Sie zum Anzeigen von Informationen über Kafka aus der Liste auf der linken Seite __Kafka__ aus.

    ![Dienstliste, in der Kafka hervorgehoben ist](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Wählen Sie zum Anzeigen der Kafka-Konfiguration oben in der Mitte __Konfigurationen__ aus.

    ![Konfigurationslinks für Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Geben Sie zum Suchen der Konfiguration __kafka-env__ oben rechts in das Feld __Filter__ die Zeichenfolge `kafka-env` ein.

    ![Kafka-Konfiguration für kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Um Kafka zum Ankündigen von IP-Adressen zu konfigurieren, fügen Sie den folgenden Text am Ende des Felds __Vorlage für kafka-env__ hinzu:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Geben Sie zum Konfigurieren der Schnittstelle, an der Kafka lauscht, oben rechts in das Feld __Filter__ die Zeichenfolge `listeners` ein.

7. Um Kafka zum Lauschen an allen Netzwerkschnittstellen zu konfigurieren, ändern Sie den Wert im Feld __Listener__ in `PLAINTEXT://0.0.0.0:9092`.

8. Klicken Sie auf die Schaltfläche __Speichern__, um die Konfigurationsänderungen zu speichern. Geben Sie eine Textnachricht ein, die die Änderungen beschreibt. Wählen Sie __OK__ aus, nachdem die Änderungen gespeichert wurden.

    ![Schaltfläche zum Speichern der Konfiguration](./media/apache-kafka-azure-container-services/save-button.png)

9. Um Fehler beim Neustart von Kafka zu vermeiden, verwenden Sie die Schaltfläche __Dienstaktionen__, und wählen Sie __Wartungsmodus aktivieren__ aus. Wählen Sie „OK“ aus, um diesen Vorgang abzuschließen.

    ![Dienstaktionen, „Wartungsmodus aktivieren“ hervorgehoben](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Verwenden Sie zum Neustarten von Kafka die Schaltfläche __Neu starten__, und wählen Sie __Alle betroffenen Instanzen neu starten__ aus. Bestätigen Sie den Neustart, und verwenden Sie dann die Schaltfläche __OK__, wenn der Vorgang abgeschlossen ist.

    ![Schaltfläche „Neu starten“, Funktion zum Neustarten aller betroffenen Instanzen hervorgehoben](./media/apache-kafka-azure-container-services/restart-button.png)

11. Um den Wartungsmodus zu deaktivieren, verwenden die Schaltfläche __Dienstaktionen__, und wählen Sie __Wartungsmodus deaktivieren__ aus. Wählen Sie **OK** aus, um diesen Vorgang abzuschließen.

## <a name="test-the-configuration"></a>Testen der Konfiguration

An diesem Punkt kommunizieren Kafka und Azure Kubernetes Service über die durch Peering verbundenen virtuellen Netzwerke miteinander. Verwenden Sie die folgenden Schritte, um diese Verbindung zu testen:

1. Erstellen Sie ein Kafka-Thema, das von der Testanwendung verwendet wird. Weitere Informationen zum Erstellen von Kafka-Themen finden Sie im Dokument [Erstellen eines Apache Kafka-Clusters](apache-kafka-get-started.md).

2. Laden Sie die Beispielanwendung von [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) herunter.

3. Bearbeiten Sie die Datei `index.js`, und ändern Sie die folgenden Zeilen:

    * `var topic = 'mytopic'`: Ersetzen Sie `mytopic` durch den Namen des von dieser Anwendung verwendeten Kafka-Themas.
    * `var brokerHost = '176.16.0.13:9092`: Ersetzen Sie `176.16.0.13` durch die interne IP-Adresse eines der Brokerhosts für den Cluster.

        Informationen zum Suchen der internen IP-Adresse der Broker-Hosts (Workerknoten) im Cluster finden Sie im Dokument [Apache Ambari-REST-API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes). Wählen Sie die IP-Adresse eines der Einträge aus, bei welcher der Domänenname mit `wn` beginnt.

4. Installieren Sie über eine Befehlszeile im Verzeichnis `src` Abhängigkeiten, und erstellen Sie mit Docker ein Image für die Bereitstellung:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Die von dieser Anwendung benötigten Pakete werden in das Repository eingecheckt. Daher brauchen Sie für die Installation nicht das Hilfsprogramm `npm` zu verwenden.

5. Melden Sie sich bei der Containerregistrierung von Azure (Azure Container Registry, ACR) an, und suchen Sie den loginServer-Namen:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Wenn Sie den Azure Container Registry-Namen nicht kennen oder mit der Verwendung der Azure-Befehlszeilenschnittstelle zum Arbeiten mit Azure Kubernetes Service nicht vertraut sind, informieren Sie sich in den [Tutorials für AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Markieren Sie das lokale Image `kafka-aks-test` mit dem loginServer der Azure Container Registry (ACR). Fügen Sie am Ende auch `:v1` hinzu, um die Imageversion anzugeben:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Übertragen Sie das Image per Push in die Registrierung:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Dieser Vorgang dauert einige Minuten.

8. Bearbeiten Sie die Kubernetes-Manifestdatei (`kafka-aks-test.yaml`), und ersetzen Sie `microsoft` durch den Namen des ACR-loginServers, den Sie in Schritt 4 abgerufen haben.

9. Stellen Sie mit dem folgenden Befehl die Anwendungseinstellungen aus dem Manifest bereit:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Verwenden Sie den folgenden Befehl, um die `EXTERNAL-IP` der Anwendung zu überwachen:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Nachdem Sie eine externe IP-Adresse zugewiesen haben, verwenden Sie __STRG+C__, um die Überwachung zu beenden.

11. Öffnen Sie einen Webbrowser, und geben Sie die externe IP-Adresse für den Dienst ein. Sie gelangen zu einer Seite, die der folgenden Abbildung ähnelt:

    ![Abbildung der Webseite](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Geben Sie Text in das Feld ein, und wählen Sie dann die Schaltfläche __Senden__ aus. Die Daten werden an Kafka gesendet. Der Kafka-Kunde in der Anwendung liest die Nachricht und fügt sie dem Abschnitt __Nachrichten von Kafka__ hinzu.

    > [!WARNING]  
    > Sie können mehrere Kopien einer Nachricht erhalten. Dieses Problem tritt in der Regel auf, wenn Sie Ihren Browser aktualisieren, nachdem Sie mehrere Browserverbindungen zur Anwendung hergestellt oder geöffnet haben.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die folgenden Links, um Informationen zur Verwendung von Apache Kafka unter HDInsight zu erhalten:

* [Erste Schritte mit Apache Kafka in HDInsight](apache-kafka-get-started.md)

* [Verwenden von MirrorMaker zum Replizieren von Apache Kafka in HDInsight](apache-kafka-mirroring.md)

* [Verwenden von Apache Storm mit Apache Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Verwenden von Apache Spark mit Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Herstellen einer Verbindung mit Apache Kafka über ein virtuelles Azure-Netzwerk](apache-kafka-connect-vpn-gateway.md)
