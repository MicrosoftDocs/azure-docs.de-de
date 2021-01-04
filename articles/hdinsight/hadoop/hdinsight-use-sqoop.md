---
title: Ausführen von Apache Sqoop-Aufträgen mit Azure HDInsight (Apache Hadoop)
description: Erfahren Sie, wie Sie Azure PowerShell auf einer Arbeitsstation verwenden können, um Sqoop-Importe und -Exporte zwischen einem Hadoop-Cluster und einer Azure SQL-Datenbank auszuführen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: c09a910ea5f6ac5c66a94c73bda49d1f65630708
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545224"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Verwenden von Apache Sqoop mit Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Sie Apache Sqoop in HDInsight zum Importieren und Exportieren von Daten zwischen einem HDInsight-Cluster und Azure SQL-Datenbank verwenden.

Auch wenn Apache Hadoop die beste Wahl für die Verarbeitung unstrukturierter und halbstrukturierter Daten wie z.B. Protokolle und Dateien ist, besteht oft auch Bedarf für die Verarbeitung strukturierter Daten, die in relationalen Datenbanken gespeichert werden.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) ist ein Tool zum Übertragen von Daten zwischen Hadoop-Clustern und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle in das verteilte Dateisystem von Hadoop (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Apache Hive transformieren und sie anschließend wieder in ein RDBMS exportieren. In diesem Artikel verwenden Sie Azure SQL-Datenbank für Ihre relationale Datenbank.

> [!IMPORTANT]  
> Im Artikel wird eine Testumgebung für die Datenübertragung eingerichtet. Sie wählen dann eine Datenübertragungsmethode für diese Umgebung aus einer der Methoden im Abschnitt [Ausführen von Sqoop-Aufträgen](#run-sqoop-jobs) weiter unten aus.

Informationen zu den in HDInsight-Clustern unterstützten Sqoop-Versionen finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](../hdinsight-component-versioning.md).

## <a name="understand-the-scenario"></a>Das Szenario

Der HDInsight-Cluster wird mit einigen Beispieldaten geliefert. Sie verwenden die beiden folgenden Beispiele:

* Eine Apache-Protokolldatei namens Log4j, die sich unter `/example/data/sample.log` befindet. Die folgenden Protokolle werden aus der Datei extrahiert:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Eine Hive-Tabelle namens `hivesampletable`, die auf die Datendatei in `/hive/warehouse/hivesampletable` verweist. Die Tabelle enthält einige Mobilgerätedaten.
  
  | Feld | Datentyp |
  | --- | --- |
  | clientid |Zeichenfolge |
  | querytime |Zeichenfolge |
  | market |Zeichenfolge |
  | deviceplatform |Zeichenfolge |
  | devicemake |Zeichenfolge |
  | devicemodel |Zeichenfolge |
  | state |Zeichenfolge |
  | country |Zeichenfolge |
  | querydwelltime |double |
  | sessionid |BIGINT |
  | sessionpagevieworder |BIGINT |

In diesem Artikel verwenden Sie diese beiden Datasets zum Testen des Sqoop-Imports- und -Exports.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Einrichten der Testumgebung

Der Cluster, die SQL-Datenbank und andere Objekte werden über das Azure-Portal mithilfe einer Azure Resource Manager-Vorlage erstellt. Die Vorlage finden Sie in den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Die Resource Manager-Vorlage ruft ein BACPAC-Paket auf, um die Tabellenschemas in einer SQL-Datenbank bereitzustellen.  Das BACPAC-Paket befindet sich in einem öffentlichen Blobcontainer, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Wenn Sie einen privaten Container für die bacpac-Dateien verwenden möchten, verwenden Sie die folgenden Werte in der Vorlage:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Der Import über eine Vorlage oder das Azure-Portal unterstützt nur das Importieren einer BACPAC-Datei aus Azure Blob Storage.

1. Wählen Sie das folgende Bild aus, um die Resource Manager-Vorlage im Azure-Portal zu öffnen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Geben Sie die folgenden Eigenschaften ein:

    |Feld |Wert |
    |---|---|
    |Subscription |Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus.|
    |Resource group |Wählen Sie in der Dropdownliste Ihre Ressourcengruppe aus, oder erstellen Sie eine neue Gruppe.|
    |Standort |Wählen Sie in der Dropdownliste eine Region aus.|
    |Clustername |Geben Sie einen Namen für den Hadoop-Cluster ein. Verwenden Sie nur Kleinbuchstaben.|
    |Benutzername für Clusteranmeldung |Behalten Sie den vorgegebenen Wert `admin` bei.|
    |Kennwort für Clusteranmeldung |Geben Sie ein Kennwort ein.|
    |SSH-Benutzername |Behalten Sie den vorgegebenen Wert `sshuser` bei.|
    |SSH-Kennwort |Geben Sie ein Kennwort ein.|
    |SQL-Administratoranmeldung |Behalten Sie den vorgegebenen Wert `sqluser` bei.|
    |SQL-Administratorkennwort |Geben Sie ein Kennwort ein.|
    |„_artifacts“-Speicherort | Verwenden Sie den Standardwert, sofern Sie nicht eine eigene BACPAC-Datei an einem anderen Speicherort verwenden möchten.|
    |SAS-Token für „_artifacts“-Speicherort |Lassen Sie dieses Feld leer.|
    |BACPAC-Dateiname |Verwenden Sie den Standardwert, sofern Sie nicht eine eigene BACPAC-Datei verwenden möchten.|
    |Standort |Verwenden Sie den Standardwert.|

    Der Name des [logischen SQL-Servers](../../azure-sql/database/logical-servers.md) lautet `<ClusterName>dbserver`. Der Datenbankname lautet `<ClusterName>db`. Der Name des Standardspeicherkontos lautet `e6qhezrh2pdqu`.

3. Aktivieren Sie **Ich stimme den oben genannten Geschäftsbedingungen zu** .

4. Wählen Sie die Option **Kaufen** . Daraufhin wird eine neue Kachel mit der Bezeichnung „Bereitstellung für Vorlagenbereitstellung wird gesendet“ angezeigt. Das Erstellen des Clusters und der SQL-Datenbank dauert ca. 20 Minuten.

## <a name="run-sqoop-jobs"></a>Ausführen von Sqoop-Aufträgen

HDInsight kann Sqoop-Aufträge mit verschiedenen Methoden ausführen. Die folgende Tabelle hilft Ihnen bei der Entscheidung, welche Methode für Sie geeignet ist. Folgen Sie anschließend dem Link für eine exemplarische Vorgehensweise.

| **Verwenden Sie dies** , wenn Sie Folgendes wünschen: | ... eine **interaktive** Shell | ... **Batchverarbeitung** | ...von diesem **Clusterbetriebssystem** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X oder Windows |
| [.NET SDK für Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (vorläufig) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Einschränkungen

* Massenexport: Bei Linux-basiertem HDInsight unterstützt der zum Exportieren von Daten nach Microsoft SQL Server oder SQL-Datenbank verwendete Sqoop-Connector derzeit keine Masseneinfügungen.
* Batchverarbeitung: Wenn Sie in Linux-basiertem HDInsight zum Ausführen von Einfügevorgängen den Schalter `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen.

## <a name="next-steps"></a>Nächste Schritte

Sie haben nun gelernt, wie Sie Sqoop verwenden. Weitere Informationen finden Sie unter:

* [Verwenden von Apache Hive mit HDInsight](./hdinsight-use-hive.md)
* [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md): Andere Methoden zum Hochladen von Daten in HDInsight/Azure Blob Storage.
* [Importieren und Exportieren von Daten zwischen Apache Hadoop unter HDInsight und einer SQL-Datenbank mithilfe von Apache Sqoop](./apache-hadoop-use-sqoop-mac-linux.md)