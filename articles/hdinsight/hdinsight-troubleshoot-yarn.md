---
title: Problembehandlung für YARN in Azure HDInsight
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zum Arbeiten mit Apache Hadoop YARN und Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 8396f682558b71ca99af845bd51f7b2c8059f79b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072016"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Problembehandlung für Apache Hadoop YARN mit Azure HDInsight

Lernen Sie die wichtigsten Probleme und ihre Lösungen bei der Arbeit mit Apache Hadoop YARN-Nutzlasten in Apache Ambari kennen.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Wie erstelle ich eine neue YARN-Warteschlange in einem Cluster?

### <a name="resolution-steps"></a>Lösungsschritte 

Erstellen Sie mit den folgenden Schritten in Ambari eine neue YARN-Warteschlange, und teilen Sie die Kapazitätszuordnung dann gleichmäßig auf alle Warteschlangen auf. 

In diesem Beispiel wird die Kapazität zweier vorhandener Warteschlangen (**default** und **thriftsvr**) von 50% in 25% geändert, sodass die neue Warteschlange (spark) 50% Kapazität bekommt.

| Warteschlange | Capacity | Maximale Kapazität |
| --- | --- | --- |
| die Standardeinstellung | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Wählen Sie das **Ambari-Ansichten**-Symbol und dann das Gittermuster. Wählen Sie als Nächstes **YARN Queue Manager**.

    ![Ambari-Ansichten-Symbol auswählen](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Wählen Sie die Warteschlange **default** aus.

    ![Warteschlange „default“ auswählen](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Ändern Sie für die **default**-Warteschlange **capacity** von 50% in 25%. Ändern Sie für die **thriftsvr**-Warteschlange **capacity** in 25%.

    ![Ändern der Kapazität in 25% für die Warteschlangen „default“ und „thriftsvr“](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Wählen Sie zum Erstellen einer neuen Warteschlange **Add Queue**.

    ![„Add Queue“ wählen](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Benennen Sie die neue Warteschlange.

    ![Neue Warteschlange „spark“ nennen](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Lassen Sie die **capacity**-Werte auf 50%, und wählen Sie die Schaltfläche **Actions**.

    ![Schaltfläche „Actions“ wählen](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Wählen Sie **Save and Refresh Queues**.

    ![„Save and Refresh Queues“ wählen](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Diese Änderungen werden sofort auf der YARN-Scheduler-Benutzeroberfläche angezeigt.

### <a name="additional-reading"></a>Zusätzliche Lektüre

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) (Apache Hadoop YARN-Kapazitätsplaner)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Wie lade ich YARN-Protokolle aus einem Cluster herunter?


### <a name="resolution-steps"></a>Lösungsschritte 

1. Stellen Sie mit einem Secure Shell-Client (SSH) eine Verbindung mit dem HDInsight-Cluster her. Weitere Informationen finden Sie unter [Zusätzliche Lektüre](#additional-reading-2).

2. Um alle Anwendungs-IDs der aktuell ausgeführten YARN-Anwendungen aufzulisten, verwenden Sie folgenden Befehl:

    ```apache
    yarn top
    ```
    Die IDs werden in der Spalte **APPLICATIONID** aufgelistet. Sie können Protokolle aus der Spalte **APPLICATIONID** herunterladen.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

3. Um YARN-Containerprotokolle für alle Anwendungsmaster herunterzuladen, verwenden Sie folgenden Befehl:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Dieser Befehl erstellt eine Protokolldatei namens „amlogs.txt“. 

4. Um YARN-Containerprotokolle nur für den neuesten Anwendungsmaster herunterzuladen, verwenden Sie folgenden Befehl:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Dieser Befehl erstellt eine Protokolldatei namens „latestamlogs.txt“. 

4. Um YARN-Containerprotokolle für die ersten beiden Anwendungsmaster herunterzuladen, verwenden Sie folgenden Befehl:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Dieser Befehl erstellt eine Protokolldatei namens „first2amlogs.txt“. 

5. Um alle YARN-Containerprotokolle herunterzuladen, verwenden Sie folgenden Befehl:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Dieser Befehl erstellt eine Protokolldatei namens „logs.txt“. 

6. Um das YARN-Containerprotokoll für einen bestimmten Container herunterzuladen, verwenden Sie folgenden Befehl:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Dieser Befehl erstellt eine Protokolldatei namens „containerlogs.txt“.

### <a name="additional-reading-2"></a>Zusätzliche Lektüre

- [Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN concepts and applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow) (Apache Hadoop Yarn – Konzepte und -Anwendungen)


### <a name="see-also"></a>Siehe auch
[Beheben von Problemen mit Azure HDInsight](hdinsight-troubleshoot-guide.md)
