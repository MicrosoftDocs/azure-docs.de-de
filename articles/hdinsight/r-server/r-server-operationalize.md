---
title: Operationalisieren von ML Services in HDInsight – Azure
description: Erfahren Sie, wie Sie Ihr Datenmodell operationalisieren, um Vorhersagen mit ML Services in Azure HDInsight zu treffen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/27/2018
ms.openlocfilehash: 20159cf911670eb70fd5757991c07b63b3f1776b
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536265"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operationalisieren eines ML Services-Clusters in Azure HDInsight

Nachdem Sie mit dem ML Services-Cluster Ihre Datenmodellierung in HDInsight vorgenommen haben, können Sie das Modell operationalisieren, um Vorhersagen zu treffen. Dieser Artikel enthält Anweisungen zur Durchführung dieser Aufgabe.

## <a name="prerequisites"></a>Voraussetzungen

* Ein ML Services-Cluster in HDInsight. Siehe [Erstellen von Apache Hadoop-Clustern im Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md), und wählen Sie **ML Services** für **Clustertyp** aus.

* Ein SSH-Client (Secure Shell): Ein SSH-Client wird verwendet, um Remoteverbindungen mit dem HDInsight-Cluster herzustellen und Befehle direkt im Cluster auszuführen. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operationalisieren des ML Services-Clusters mit der Konfiguration mit einem einzelnen Computer

> [!NOTE]  
> Die nachstehenden Schritte gelten für R Server 9.0 und ML Server 9.1. Für ML Server 9.3 finden Sie weitere Hinweise unter [Verwenden des Verwaltungstools zum Verwalten der Operationalisierungskonfiguration](/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Stellen Sie per SSH eine Verbindung mit dem Edgeknoten her.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Anweisungen zum Verwenden von SSH mit Azure HDInsight finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ändern Sie das Verzeichnis für die relevante Version, und rufen Sie die Dotnet-DLL per „sudo“ auf: 

    - Für Microsoft ML Server 9.1:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - Für Microsoft R Server 9.0:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. Die Optionen werden zur Auswahl angezeigt. Wählen Sie die erste Option wie im folgenden Screenshot gezeigt aus, um **ML Server für die Operationalisierung zu konfigurieren** .

    ![R Server-Verwaltungshilfsprogramm – Auswählen](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Sie können jetzt eine Option für die Operationalisierung von ML Server auswählen. Wählen Sie die erste der angebotenen Optionen durch Eingabe von **A** aus.

    ![R Server-Verwaltungshilfsprogramm – Operationalisieren](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Geben Sie bei Aufforderung das Kennwort für einen lokalen Administratorbenutzer ein bzw. erneut ein.

1. Daraufhin sollte ausgegeben werden, dass der Vorgang erfolgreich war. Sie werden außerdem aufgefordert, eine andere Option aus dem Menü auszuwählen. Wählen Sie „E“ aus, um zum Hauptmenü zurückzukehren.

    ![R Server-Verwaltungshilfsprogramm – erfolgreich abgeschlossen](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Optional können Sie die Diagnoseprüfungen ausführen, indem Sie wie folgt einen Diagnosetest ausführen:

    a. Wählen Sie im Hauptmenü **6** aus, um Diagnosetests auszuführen.

    ![R Server-Verwaltungshilfsprogramm – Diagnose](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. Wählen Sie im Diagnosetestmenü **A** aus. Wenn Sie aufgefordert werden, geben Sie das Kennwort ein, das Sie für den lokalen Administratorbenutzer bereitgestellt haben.

    ![R Server-Verwaltungshilfsprogramm – Test](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Überprüfen Sie, ob ausgegeben wird, dass die Gesamtintegrität einwandfrei ist.

    ![R Server-Verwaltungshilfsprogramm – Test bestanden](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. Geben Sie von den angebotenen Menüoptionen **E** ein, um zum Hauptmenü zurückzukehren, und geben Sie dann **8** ein, um das Verwaltungshilfsprogramm zu beenden.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Lange Verzögerungen bei der Nutzung des Webdiensts in Apache Spark

Wenn bei der Nutzung eines Webdiensts, der mit mrsdeploy-Funktionen in einem Apache Spark-Computekontext erstellt wurde, lange Verzögerungen auftreten, müssen Sie unter Umständen einige fehlende Ordner hinzufügen. Wenn die Spark-Anwendung von einem Webdienst mit mrsdeploy-Funktionen aufgerufen wird, gehört sie zu einem Benutzer namens *rserve2* . So können Sie dieses Problem umgehen:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

In dieser Phase ist die Konfiguration der Operationalisierung abgeschlossen. Nun können Sie das Paket `mrsdeploy` auf Ihrem RClient zum Herstellen einer Verbindung mit der Operationalisierung auf dem Edgeknoten verwenden und mit der Verwendung seiner Features wie [Remoteausführung](/machine-learning-server/r/how-to-execute-code-remotely) und [Webdienste](/machine-learning-server/operationalize/concept-what-are-web-services) beginnen. Je nachdem, ob Ihr Cluster in einem virtuellen Netzwerk eingerichtet ist, müssen Sie über die SSH-Anmeldung ein Tunneling für die Portweiterleitung einrichten. Die Einrichtung dieses Tunnels wird in den folgenden Abschnitten erläutert.

### <a name="ml-services-cluster-on-virtual-network"></a>ML Services-Cluster in virtuellem Netzwerk

Stellen Sie sicher, dass Sie Datenverkehr über Port 12800 zum Edgeknoten zulassen. Auf diese Weise können Sie den Edgeknoten zum Herstellen der Verbindung mit dem Feature „Operationalisierung“ verwenden.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Wenn über `remoteLogin()` keine Verbindung mit dem Edgeknoten hergestellt werden kann, Sie aber über SSH eine Verbindung mit dem Edgeknoten aufbauen können, müssen Sie überprüfen, ob die Regel zum Zulassen von Datenverkehr über Port 12800 ordnungsgemäß festgelegt wurde. Wenn das Problem weiterhin besteht, können Sie es umgehen, indem Sie über SSH das Tunneling der Portweiterleitung einrichten. Anweisungen finden Sie im folgenden Abschnitt:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>ML Services-Cluster in virtuellem Netzwerk nicht eingerichtet

Wenn Ihr Cluster nicht im VNET eingerichtet wurde oder Sie Probleme mit der Konnektivität über das VNET haben, können Sie das Tunneling der SSH-Portweiterleitung nutzen:

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Sobald Ihre SSH-Sitzung aktiv ist, wird der Datenverkehr in der SSH-Sitzung vom Port 12800 des lokalen Computers zum Port 12800 des Edgeknotens weitergeleitet. Stellen Sie sicher, dass Sie `127.0.0.1:12800` in der `remoteLogin()`-Methode verwenden. Hierdurch erfolgt eine Anmeldung bei der Operationalisierung des Edgeknotens über die Portweiterleitung.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skalieren operationalisierter Serverknoten auf HDInsight-Workerknoten

Um die Serverknoten zu skalieren, nehmen Sie die Workerknoten zuerst außer Betrieb und konfigurieren dann Serverknoten auf den außer Betrieb gesetzten Workerknoten.

### <a name="step-1-decommission-the-worker-nodes"></a>Schritt 1: Außerbetriebsetzen der Workerknoten

Der ML Services-Cluster wird nicht über [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) verwaltet. Wenn die Workerknoten nicht außer Betrieb gesetzt werden, funktioniert der YARN-Ressourcen-Manager nicht wie erwartet, da er die vom Server belegten Ressourcen nicht ermitteln kann. Um diese Situation zu vermeiden, empfehlen wir die Außerbetriebnahme der Workerknoten, bevor Sie die Serverknoten aufskalieren.

Führen Sie folgende Schritte zur Außerbetriebnahme der Workerknoten aus:

1. Melden Sie sich bei der Ambari-Konsole des Clusters an, und klicken Sie auf die Registerkarte **Hosts** .

1. Wählen Sie (außer Betrieb zu setzende) Workerknoten aus.

1. Klicken Sie auf **Aktionen** > **Ausgewählte Hosts** > **Hosts** > **Wartungsmodus EINSCHALTEN** . In der folgenden Abbildung haben wir z.B. „wn3“ und „wn4“ für die Außerbetriebnahme ausgewählt.  

   ![Apache Ambari – Aktivieren des Wartungsmodus](./media/r-server-operationalize/get-started-operationalization.png)  

* Wählen Sie **Aktionen** > **Ausgewählte Hosts** > **DataNodes** aus, und klicken Sie auf **Außer Betrieb setzen** .
* Wählen Sie **Aktionen** > **Ausgewählte Hosts** > **NodeManagers** aus, und klicken Sie auf **Außer Betrieb setzen** .
* Wählen Sie **Aktionen** > **Ausgewählte Hosts** > **DataNodes** aus, und klicken Sie auf **Beenden** .
* Wählen Sie **Aktionen** > **Ausgewählte Hosts** > **NodeManagers** aus, und klicken Sie auf **Beenden** .
* Wählen Sie **Aktionen** > **Ausgewählte Hosts** > **Hosts** aus, und klicken Sie auf **Alle Komponenten beenden** .
* Heben Sie die Auswahl der Workerknoten auf, und wählen Sie die Hauptknoten aus.
* Wählen Sie **Aktionen** > **Ausgewählte Hosts** > **Hosts** > **Alle Komponenten beenden** aus.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Schritt 2: Konfigurieren von Computeknoten auf allen außer Betrieb gesetzten Workerknoten

1. Stellen Sie per SSH eine Verbindung mit jedem außer Betrieb gesetzten Workerknoten her.

1. Führen Sie das Verwaltungshilfsprogramm mit der entsprechenden DLL für Ihren ML Services-Cluster aus. Führen Sie für ML Server 9.1 die folgenden Schritte aus:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Geben Sie **1** ein, um die Option **Configure ML Server for Operationalization** (ML Services für Operationalisierung konfigurieren) auszuwählen.

1. Geben Sie **C** zum Auswählen von Option `C. Compute node` ein. Hiermit wird der Serverknoten für den Workerknoten konfiguriert.

1. Beenden Sie das Verwaltungshilfsprogramm.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Schritt 3: Hinzufügen von Details zu Computeknoten auf Webknoten

Nachdem alle außer Betrieb gesetzten Workerknoten für die Ausführung als Serverknoten konfiguriert sind, kehren Sie zum Edgeknoten zurück und fügen die IP-Adressen der außer Betrieb gesetzten Workerknoten der Konfiguration des Webknotens von ML Server hinzu:

1. Stellen Sie per SSH eine Verbindung mit dem Edgeknoten her.

1. Führen Sie `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` aus.

1. Fügen Sie im Abschnitt „URIs“ die IP-Adress- und Portdetails des Workerknotens hinzu.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Manage ML Services cluster on HDInsight](r-server-hdinsight-manage.md) (Verwalten eines ML Services-Clusters in Azure HDInsight)
* [Compute context options for ML Services on HDInsight](r-server-compute-contexts.md) (Computekontextoptionen für ML Services in HDInsight)
* [Azure Storage solutions for ML Services on Azure HDInsight](r-server-storage.md) (Azure Storage-Lösungen für ML Services in Azure HDInsight)