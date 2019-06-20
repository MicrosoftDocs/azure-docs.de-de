---
title: Verwenden von Grafana in Azure HDInsight
description: Erfahren Sie, wie Sie auf Grafana in Azure HDInsight zugreifen.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 42429f0e8e541d0481f991761ead63e4751fcc3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719729"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Zugreifen auf Grafana in Azure HDInsight


[Grafana](https://grafana.com/) ist ein verbreiteter Open-Source-Generator für Diagramme und Dashboards. Mit dem umfangreichen Funktionssatz von Grafana können Benutzer nicht nur anpassbare und gemeinsam nutzbare Dashboards erstellen, sondern u.a. auch von Dashboards mit Vorlagen bzw. Skripts, LDAP-Integration und verschiedenen Datenquellen profitieren.

Derzeit wird Grafana in Azure HDInsight mit den Clustertypen Hbase und Interactive Query unterstützt.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-an-apache-hadoop-cluster"></a>Erstellen eines Apache Hadoop-Clusters

In diesem Abschnitt erstellen Sie einen Interactive Query-Cluster in HDInsight mit einer Azure Resource Manager-Vorlage. Für diesen Artikel ist keine Erfahrung mit Resource Manager-Vorlagen erforderlich. 

1. Klicken Sie unten auf die Schaltfläche **Bereitstellung in Azure**, um sich bei Azure anzumelden und die Resource Manager-Vorlage im Azure-Portal zu öffnen. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Geben Sie die Werte wie im folgenden Screenshot vorgeschlagen ein, oder wählen Sie sie aus:

    > [!NOTE]  
    > Die Werte, die Sie angeben, müssen eindeutig sein und sollten den Benennungsrichtlinien folgen. Die Vorlage führt keine Überprüfungen durch. Wenn die Werte, die Sie angeben, bereits verwendet werden oder nicht den Richtlinien entsprechen, erhalten Sie eine Fehlermeldung, nachdem Sie die Vorlage übermittelt haben.       
    > 
    >
    
    ![HDInsight unter Linux, Erste Schritte mit der Resource Manager-Vorlage im Portal](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Bereitstellen eines Hadoop-Clusters in HDInsight mithilfe des Azure-Portals und einer Resource Manager-Vorlage für Ressourcengruppen")

    Geben Sie folgende Werte ein bzw. wählen diese aus:
    
    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |**Abonnement**     |  Wählen Sie Ihr Azure-Abonnement. |
    |**Ressourcengruppe**     | Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus.  Bei einer Ressourcengruppe handelt es sich um einen Container mit Azure-Komponenten.  In diesem Fall enthält die Ressourcengruppe den HDInsight-Cluster und das abhängige Azure Storage-Konto. |
    |**Location**     | Wählen Sie den Azure-Speicherort aus, wo Sie Ihren Cluster erstellen möchten.  Je näher der Standort, desto besser die Leistung. |
    |**Clustertyp**     | Wählen Sie **hadoop** aus. |
    |**Clustername**     | Geben Sie einen Namen für den Apache Hadoop-Cluster ein. Da für alle Cluster in HDInsight gemeinsam derselbe DNS-Namespace genutzt wird, muss dieser Name eindeutig sein. Der Name kann aus bis zu 59 Zeichen mit Buchstaben, Zahlen und Bindestrichen bestehen. Das erste und das letzte Zeichen des Namens dürfen keine Bindestriche sein. |
    |**Clusteranmeldename und Kennwort**     | Der Standardanmeldename lautet **admin**. Das Kennwort muss mindestens zehn Zeichen lang sein und mindestens eine Ziffer, einen Groß- und einen Kleinbuchstaben sowie ein nicht alphanumerisches Zeichen enthalten (mit Ausnahme folgender Zeichen: ' " ` \)). Stellen Sie sicher, dass Sie **keine** häufig verwendeten Kennwörter wie „Pass@word1“ angeben.|
    |**SSH-Benutzername und Kennwort**     | Der Standardbenutzername lautet **sshuser**.  Sie können den SSH-Benutzernamen umbenennen.  Für das SSH-Benutzerkennwort gelten die gleichen Anforderungen wie für das Clusteranmeldekennwort.|
       
    Einige Eigenschaften wurden in der Vorlage hartcodiert.  Diese Werte können über die Vorlage konfiguriert werden. Eine ausführlichere Erläuterung dieser Eigenschaften finden Sie unter [Erstellen von Linux-basierten Apache Hadoop-Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Aktivieren Sie die Optionen **Ich stimme den oben genannten Geschäftsbedingungen zu** und **An Dashboard anheften**, und wählen Sie anschließend **Kaufen** aus. Im Portal-Dashboard wird die neue Kachel **Bereitstellung wird übermittelt** angezeigt. Das Erstellen eines Clusters dauert ca. 20 Minuten.

    ![Vorlagenbereitstellungsstatus](./media/hdinsight-grafana/deployment-progress-tile.png "Azure-Vorlagenbereitstellungsstatus")

4. Nachdem der Cluster erstellt wurde, wird die Beschriftung der Kachel in den von Ihnen angegebenen Ressourcengruppennamen geändert. Die Kachel listet auch den HDInsight-Cluster auf, der innerhalb der Ressourcengruppe erstellt wird. 
   
    ![HDInsight unter Linux, Erste Schritte mit der Ressourcengruppe](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Azure HDInsight-Clusterressourcengruppe")
    
5. Die Kachel listet auch den mit dem Cluster verbundenen Standardspeicher auf. Jeder Cluster verfügt über eine Abhängigkeit von einem [Azure Storage-Konto](../hdinsight-hadoop-use-blob-storage.md) oder einem [Azure Data Lake-Konto](../hdinsight-hadoop-use-data-lake-store.md). Es wird als Standardspeicherkonto bezeichnet. Der HDInsight-Cluster und das dazugehörige Speicherkonto müssen sich in derselben Azure-Region befinden. Beim Löschen von Clustern wird das Speicherkonto nicht gelöscht.
    

> [!NOTE]  
> Andere Methoden zur Erstellung von Clustern und Informationen zu den in diesem Tutorial verwendeten Eigenschaften finden Sie unter [Erstellen von HDInsight-Clustern](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Zugreifen auf das Grafana-Dashboard

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **HDInsight-Cluster** und im letzten Abschnitt dann den Namen des von Ihnen erstellten Clusters aus.

3. Klicken Sie unter **Quicklinks** auf **Clusterdashboard**.

    ![HDInsight-Clusterdashboard im Portal](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "HDInsight cluster dashboard on the portal")

4. Klicken Sie auf dem Dashboard auf die Kachel **Grafana**. Navigieren Sie alternativ zum `/grafana/`-Pfad Ihrer Cluster-URL. Beispiel: `https://<clustername>.azurehdinsight.net/grafana/`.

5. Geben Sie die Benutzeranmeldeinformationen für den Hadoop-Cluster ein.

6. Das Grafana-Dashboard wird angezeigt und sieht wie in diesem Beispiel aus:

    ![HDInsight-Cluster auf dem Grafana-Dashboard](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana dashboard")

   

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Nach Abschluss des Artikels kann es ratsam sein, den Cluster zu löschen. Mit HDInsight werden Ihre Daten im Azure-Speicher gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen. 

> [!NOTE]  
> Wenn Sie *sofort* mit dem nächsten Tutorial fortfahren, um zu erfahren, wie Sie ETL-Vorgänge mithilfe von Hadoop in HDInsight ausführen, können Sie den Cluster weiterhin ausführen. Ansonsten müssen Sie in diesem Tutorial erneut einen Hadoop-Cluster erstellen. Wenn Sie jedoch nicht direkt mit dem nächsten Tutorial fortfahren, sollten Sie den Cluster jetzt löschen.

**So löschen Sie den Cluster bzw. das Standardspeicherkonto**

1. Wechseln Sie zurück zur Browserregisterkarte für das Azure-Portal. Die Seite mit der Clusterübersicht sollte angezeigt werden. Klicken Sie auf **Löschen**, wenn Sie nur den Cluster löschen, aber das Standardspeicherkonto behalten möchten.

    ![HDInsight: Cluster löschen](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Löschen eines HDInsight-Clusters")

2. Wenn Sie sowohl den Cluster als auch das Standardspeicherkonto löschen möchten, wählen Sie den Ressourcengruppennamen (im vorherigen Screenshot markiert) aus, um die Seite für die Ressourcengruppe zu öffnen.

3. Klicken Sie auf **Ressourcengruppe löschen**, um die Ressourcengruppe zu löschen, die den Cluster und das Standardspeicherkonto enthält. Beachten Sie, dass das Speicherkonto beim Löschen der Ressourcengruppe ebenfalls gelöscht wird. Wenn Sie das Speicherkonto beibehalten möchten, müssen Sie auswählen, dass nur der Cluster gelöscht werden soll.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie mithilfe einer Resource Manager-Vorlage einen Linux-basierten HDInsight-Cluster erstellen und einfache Apache Hive-Abfragen ausführen. Im nächsten Artikel erfahren Sie, wie Sie mithilfe von Hadoop in HDInsight einen ETL-Vorgang zum Extrahieren, Transformieren und Laden von Daten ausführen.

> [!div class="nextstepaction"]
>[Extrahieren, Transformieren und Laden von Daten mithilfe von Apache Hive in HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)

Wenn Sie mit eigenen Daten arbeiten und mehr darüber wissen möchten, wie Daten in HDInsight gespeichert oder verwendet werden, finden Sie weitere Informationen in folgenden Artikeln:

* Informationen zur Verwendung von Azure Storage durch HDInsight finden Sie unter [Verwenden von Azure Storage mit HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Informationen zum Hochladen von Daten in HDInsight finden Sie im Artikel zum [Hochladen von Daten für Hadoop-Aufträge in HDInsight](../hdinsight-upload-data.md).

Weitere Informationen zur Datenanalyse mit HDInsight finden Sie in den folgenden Artikeln:

* Weitere Informationen zum Verwenden von Hive mit HDInsight (etwa zum Ausführen von Hive-Abfragen in Visual Studio) finden Sie unter [Verwenden von Apache Hive mit HDInsight](../hdinsight-use-hive.md).
* Informationen zu Pig (einer Sprache zum Transformieren von Daten) finden Sie unter [Verwenden von Apache Pig mit HDInsight](../hdinsight-use-pig.md).
* Informationen zu MapReduce (einer Möglichkeit zum Schreiben von Programmen, die Daten in Hadoop verarbeiten) finden Sie im Artikel zum [Verwenden von MapReduce mit HDInsight](../hdinsight-use-mapreduce.md).
* Informationen zur Verwendung der HDInsight-Tools für Visual Studio zum Analysieren von Daten in HDInsight finden Sie unter [Erste Schritte bei der Verwendung von Hadoop-Tools für Visual Studio für HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Weitere Informationen zum Erstellen und Verwalten von HDInsight-Clustern finden Sie in folgenden Artikeln:

* Informationen zum Verwalten eines Linux-basierten HDInsight-Clusters finden Sie unter [Verwalten von HDInsight-Clustern mit Ambari](../hdinsight-hadoop-manage-ambari.md).
* Informationen zu den Optionen, die Sie beim Erstellen eines HDInsight-Clusters auswählen können, finden Sie unter [Erstellen von HDInsight unter Linux mit benutzerdefinierten Optionen](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
