---
title: Problembehandlung für Apache Oozie in Azure HDInsight
description: Behandeln Sie Probleme für bestimmte Apache Oozie-Fehler in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: edbe5274de8576fccb29e1e69d260a6531d4ab05
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287400"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Problembehandlung für Apache Oozie in Azure HDInsight

Mithilfe der Apache Oozie-Benutzeroberfläche können Sie die Oozie-Protokolle anzeigen. Die Oozie-Benutzeroberfläche enthält darüber hinaus Links zu den JobTracker-Protokollen für die MapReduce-Aufgaben, die vom Workflow gestartet werden. Im Allgemeinen sollten Sie zur Problembehandlung wie folgt vorgehen:

1. Zeigen Sie den Auftrag auf der Oozie-Webbenutzeroberfläche an.

2. Wenn eine bestimmte Aktion nicht erfolgreich war, wählen Sie die Aktion aus, um festzustellen, ob das Feld **Fehlermeldung** weitere Informationen zum Fehler enthält.

3. Falls verfügbar, verwenden Sie die URL der Aktion, um weitere Details (z.B. JobTracker-Protokolle) für die Aktion anzuzeigen.

Im Folgenden sehen Sie Fehlermeldungen, die auftreten können, und Möglichkeiten zur Behebung.

## <a name="ja009-cant-initialize-cluster"></a>JA009: Cluster kann nicht initialisiert werden.

### <a name="issue"></a>Problem

Der Auftragsstatus ändert sich in **SUSPENDED**. In den Auftragsdetails wird der Status von `RunHiveScript` als **START_MANUAL** angezeigt. Bei Auswahl der Aktion wird die folgende Fehlermeldung angezeigt:

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>Ursache

Die in der Datei **job.xml** verwendeten Azure-Blobspeicheradressen enthalten nicht den Namen des Speichercontainers oder des Speicherkontos. Das Format der Blob-Speicheradresse muss `wasbs://containername@storageaccountname.blob.core.windows.net` sein.

### <a name="resolution"></a>Lösung

Ändern Sie die Blobspeicheradressen für den Auftrag.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie darf nicht die Identität von &lt;USER&gt; annehmen.

### <a name="issue"></a>Problem

Der Auftragsstatus ändert sich in **SUSPENDED**. In den Auftragsdetails wird der Status von `RunHiveScript` als **START_MANUAL** angezeigt. Wenn Sie die Aktion auswählen, wird die folgende Fehlermeldung angezeigt:

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>Ursache

Die aktuellen Berechtigungseinstellungen lassen nicht zu, dass Oozie die Identität des angegebenen Benutzerkontos annimmt.

### <a name="resolution"></a>Lösung

Oozie kann die Identität von Benutzern in der Gruppe **`users`** annehmen. Verwenden Sie `groups USERNAME` , um die Gruppen anzuzeigen, denen das Benutzerkonto als Mitglied angehört. Wenn der Benutzer nicht Mitglied der Gruppe **`users`** ist, verwenden Sie den folgenden Befehl, um den Benutzer der Gruppe hinzuzufügen:

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> Es kann einige Minuten dauern, bis HDInsight erkennt, dass der Benutzer der Gruppe hinzugefügt wurde.

---

## <a name="launcher-error-sqoop"></a>Launcher ERROR (Sqoop)

### <a name="issue"></a>Problem

Der Auftragsstatus ändert sich in **KILLED**. In den Auftragsdetails wird der Status von `RunSqoopExport` als **ERROR** (Fehler) angezeigt. Wenn Sie die Aktion auswählen, wird die folgende Fehlermeldung angezeigt:

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>Ursache

Sqoop kann den Datenbanktreiber nicht laden, der für den Zugriff auf die Datenbank erforderlich ist.

### <a name="resolution"></a>Lösung

Bei Verwendung von Sqoop in einem Oozie-Auftrag müssen Sie den Datenbanktreiber zusammen mit den anderen vom Auftrag verwendeten Ressourcen (beispielsweise die Datei „workflow.xml“) angeben. Verweisen Sie im Abschnitt `<sqoop>...</sqoop>` von „workflow.xml“ auf das Archiv mit dem Datenbanktreiber.

Für das Auftragsbeispiel aus [Verwenden von Hadoop Oozie-Workflows](hdinsight-use-oozie-linux-mac.md) würden Sie z. B. die folgenden Schritte verwenden:

1. Kopieren Sie die Datei `mssql-jdbc-7.0.0.jre8.jar` in das Verzeichnis **/tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Ändern Sie die Datei `workflow.xml`, indem Sie den folgenden XML-Code einer neuen Zeile oberhalb von `</sqoop>` hinzufügen:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]