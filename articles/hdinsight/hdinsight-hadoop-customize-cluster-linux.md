---
title: Anpassen von Azure HDInsight-Clustern mithilfe von Skriptaktionen
description: Hier erfahren Sie, wie Sie Linux-basierten HDInsight-Clustern mithilfe Skriptaktionen benutzerdefinierte Komponenten hinzufügen. Bei Skriptaktionen handelt es sich um Bash-Skripts. Sie können zum Anpassen der Clusterkonfiguration oder zum Hinzufügen zusätzlicher Dienste und Hilfsprogramme wie Hue, Solr oder R verwendet werden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 03fcbb0216d85e337b4161aa24ceeb7d3a2bdebe
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479466"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Anpassen von Azure HDInsight-Clustern mithilfe von Skriptaktionen

HDInsight verfügt über eine Konfigurationsmethode namens **Skriptaktionen**, bei der benutzerdefinierte Skripts zum Anpassen des Clusters aufgerufen werden. Diese Skripts werden auch zum Installieren weitere Komponenten und zum Ändern von Konfigurationseinstellungen verwendet. Skriptaktionen können während oder nach der Clustererstellung verwendet werden.

Skriptaktionen können auch als HDInsight-Anwendung im Azure Marketplace veröffentlicht werden. Weitere Informationen zu HDInsight-Anwendungen finden Sie unter [Veröffentlichen von HDInsight-Anwendungen im Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Berechtigungen

Für einen in eine Domäne eingebundenen HDInsight-Cluster sind zwei Ambari-Berechtigungen erforderlich, um Skriptaktionen mit dem Cluster verwenden zu können:

* **AMBARI.RUN\_CUSTOM\_COMMAND:** Die Rolle „Ambari-Administrator“ verfügt standardmäßig über diese Berechtigung.
* **CLUSTER.RUN\_CUSTOM\_COMMAND:** Sowohl der HDInsight-Clusteradministrator als auch der Ambari-Administrator verfügt standardmäßig über diese Berechtigung.

Weitere Informationen zur Verwendung von Berechtigungen mit in eine Domäne eingebundenem HDInsight finden Sie unter [Verwalten von HDInsight-Clustern mit dem Enterprise-Sicherheitspaket](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Zugriffssteuerung

Wenn Sie nicht der Administrator oder Besitzer des Azure-Abonnements sind, muss Ihr Konto mindestens Zugriff vom Typ „Mitwirkender“ auf die Ressourcengruppe haben, die den HDInsight-Cluster enthält.

Wenn Sie einen HDInsight-Cluster erstellen, muss der Anbieter für HDInsight bereits von einer Person registriert worden sein, die mindestens über Zugriff vom Typ „Mitwirkender“ auf das Azure-Abonnement verfügt. Die Anbieterregistrierung findet statt, wenn ein Benutzer, der über Abonnementzugriff vom Typ „Mitwirkender“ verfügt, erstmals eine Ressource für das Abonnement erstellt. Dies kann auch ohne Erstellung einer Ressource erreicht werden, wenn Sie [einen Anbieter mithilfe von REST registrieren](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Weitere Informationen zur Verwendung der Zugriffsverwaltung finden Sie hier:

* [Erste Schritte mit der Zugriffsverwaltung im Azure-Portal](../role-based-access-control/overview.md)
* [Verwenden von Rollenzuweisungen zum Verwalten Ihrer Azure-Abonnementressourcen](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Grundlegendes zu Skriptaktionen

Eine Skriptaktion ist ein Bash-Skript, das auf den Knoten in einem HDInsight-Cluster ausgeführt wird. Skriptaktionen verfügen über folgende Merkmale und Features:

* Sie müssen als URI gespeichert werden, der für den HDInsight-Cluster zugänglich ist. Dies sind zwei mögliche Speicherorte:
    
    * Für normale Cluster:
    
      * ADLS Gen1: Der Dienstprinzipal, der von HDInsight zum Zugreifen auf Data Lake Storage genutzt wird, muss über Lesezugriff auf das Skript verfügen. Für in Data Lake Storage Gen1 gespeicherte Skripts wird das folgende URI-Format verwendet: `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.
      
      * Ein Blob in einem Azure Storage-Konto, das entweder das primäre oder ein zusätzliches Speicherkonto für den HDInsight-Cluster darstellt. HDInsight wird während der Clustererstellung Zugriff auf beide Typen von Speicherkonten gewährt.

      * Ein öffentlicher Dateifreigabedienst, auf den über „http://“-Pfade zugegriffen werden kann. Beispiele sind Azure Blob, GitHub und OneDrive.

        Beispiel-URIs finden Sie unter [Beispielskripts für Skriptaktionen](#example-script-action-scripts).

     * Für Cluster mit ESP:
         
         * URIs vom Typ „wasb://“ oder „wasbs://“ oder „http[s]://“ werden unterstützt.
            
* Die Ausführung kann auf bestimmte Knotentypen beschränkt werden. Beispiele wären etwa Hauptknoten und Workerknoten.

* Sie können permanent oder ad-hoc sein.

    Permanente Skripts werden verwendet, um mithilfe von Skalierungsvorgängen neue Workerknoten anzupassen, die dem Cluster hinzugefügt wurden. Bei Skalierungsvorgängen kann ein permanentes Skript auch Änderungen auf einen anderen Knotentyp anwenden. Ein Beispiel wäre etwa ein Hauptknoten.

  > [!IMPORTANT]  
  > Permanente Skriptaktionen müssen einen eindeutigen Namen haben.

    Ad-hoc-Skripts werden nicht beibehalten. Sie werden nicht auf Workerknoten angewendet, die dem Cluster hinzugefügt werden, nachdem das Skript ausgeführt wurde. Sie können ein Ad-hoc-Skript aber nachträglich in ein permanentes Skript oder ein permanentes Skript in ein Ad-hoc-Skript umwandeln.

  > [!IMPORTANT]  
  > Skriptaktionen, die während der Clustererstellung verwendet werden, werden automatisch zu permanenten Skripts.
  >
  > Skripts, deren Ausführung nicht erfolgreich ist, werden nicht zu permanenten Skripts. Dies gilt auch, wenn Sie speziell angeben, dass dies der Fall sein soll.

* Sie können Parameter akzeptieren, die von den Skripts während der Ausführung verwendet werden.

* Sie werden mit Stammebenenberechtigungen auf den Clusterknoten ausgeführt.

* Sie können über das Azure-Portal, Azure PowerShell, die klassische Azure-Befehlszeilenschnittstelle oder das HDInsight .NET SDK verwendet werden.

Der Cluster protokolliert den Verlauf aller Skripts, die ausgeführt wurden. Der Verlauf ist hilfreich, wenn Sie die ID eines Skripts für die Herauf- oder Herabstufung von Vorgängen benötigen.

> [!IMPORTANT]  
> Die von einer Skriptaktion vorgenommenen Änderungen können nicht automatisch rückgängig gemacht werden. Sie können die Änderungen entweder manuell zurückzusetzen oder ein Skript angeben, das sie zurücksetzt.

### <a name="script-action-in-the-cluster-creation-process"></a>Skriptaktionen im Clustererstellungsvorgang

Während der Clustererstellung verwendete Skriptaktionen unterscheiden sich geringfügig von Skriptaktionen, die in einem vorhandenen Cluster ausgeführt wurden:

* Das Skript wird automatisch dauerhaft gespeichert.

* Ein Fehler im Skript kann dazu führen, dass die Clustererstellung nicht erfolgreich ist.

Das folgende Diagramm veranschaulicht, wann Skriptaktionen während des Erstellungsvorgangs ausgeführt werden:

![HDInsight-Clusteranpassung und Phasen während der Clustererstellung][img-hdi-cluster-states]

Das Skript wird ausgeführt, während HDInsight konfiguriert wird. Das Skript wird parallel auf allen angegebenen Knoten im Cluster ausgeführt. Die Ausführung erfolgt dabei mit Stammberechtigungen für die Knoten.

> [!NOTE]  
> Sie können Dienste beenden und starten, einschließlich Diensten in Zusammenhang mit Apache Hadoop. Achten Sie beim Beenden von Diensten darauf, dass der Ambari-Dienst und andere Hadoop-bezogene Dienste ausgeführt werden, bevor die Ausführung des Skripts abgeschlossen ist. Diese Dienste werden benötigt, um die Integrität und den Zustand des Clusters erfolgreich zu ermitteln, während dieser erstellt wird.


Während der Clustererstellung können mehrere Skriptaktionen gleichzeitig verwendet werden. Diese Skripts werden in der Reihenfolge aufgerufen, in der sie angegeben wurden.

> [!IMPORTANT]  
> Skriptaktionen müssen innerhalb von 60 Minuten abgeschlossen werden. Andernfalls tritt ein Timeout auf. Während der Clusterbereitstellung wird das Skript gleichzeitig mit anderen Einrichtungs- und Konfigurationsprozessen ausgeführt. Der Wettbewerb um Ressourcen wie CPU-Zeit oder Netzwerkbandbreite kann dazu führen, dass es länger als in Ihrer Entwicklungsumgebung dauert, bis das Skript abgeschlossen ist.
>
> Um die Ausführungsdauer des Skripts zu minimieren, vermeiden Sie Aufgaben wie das Herunterladen und Kompilieren von Anwendungen aus der Quelle. Kompilieren Sie Anwendungen vor, und speichern Sie die Binärdateien in Azure Storage.


### <a name="script-action-on-a-running-cluster"></a>Skriptaktion in einem ausgeführten Cluster

Ein Fehler in einem Skript, das in einem bereits ausgeführten Cluster ausgeführt wird, führt nicht automatisch dazu, dass der Cluster in einen Fehlerzustand versetzt wird. Nach Abschluss eines Skripts sollte der Cluster wieder in den Ausführungszustand zurückkehren.

> [!IMPORTANT]  
> Auch wenn sich der Cluster im Zustand „Wird ausgeführt“ befindet, kann es sein, dass das fehlerhafte Skript etwas beschädigt hat. Ein Skript kann beispielsweise vom Cluster benötigte Dateien löschen.
>
> Skriptaktionen werden mit Stammberechtigungen ausgeführt. Stellen Sie also sicher, dass Sie die Auswirkungen eines Skripts verstehen, bevor Sie es auf den Cluster anwenden.

Wenn Sie ein Skript auf einem Cluster anwenden, ändert sich der Clusterzustand von **Wird ausgeführt** in **Akzeptiert**. Danach ändert sich der Zustand in **HDInsight-Konfiguration**, bevor er schließlich wieder **Wird ausgeführt** lautet (bei einem erfolgreichen Skript). Der Skriptstatus wird im Skriptaktionsverlauf protokolliert. Diese Angabe gibt Aufschluss darüber, ob das Skript erfolgreich ausgeführt wurde. Das PowerShell-Cmdlet `Get-AzHDInsightScriptActionHistory` zeigt beispielsweise den Status eines Skripts an. Die zurückgegebenen Informationen sehen in etwa wie folgt aus:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Wenn Sie nach dem Erstellen des Clusters das Kennwort für den Clusterbenutzer (Admin) ändern, sind Skriptaktionen, die für diesen Cluster ausgeführt werden, möglicherweise nicht mehr erfolgreich. Falls Sie über permanente Skriptaktionen für Workerknoten verfügen, sind diese unter Umständen nicht erfolgreich, wenn Sie den Cluster skalieren.

## <a name="example-script-action-scripts"></a>Beispielskripts für Skriptaktionen

Skripts für Skriptaktionen können über die folgenden Hilfsprogramme verwendet werden:

* Das Azure-Portal
* Azure PowerShell
* Die klassische Azure-Befehlszeilenschnittstelle
* Ein HDInsight .NET SDK

HDInsight verfügt über Skripts zum Installieren der folgenden Komponenten auf HDInsight-Clustern:

| NAME | Skript |
| --- | --- |
| Hinzufügen eines Azure Storage-Kontos |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](hdinsight-hadoop-add-storage.md). |
| Installieren von Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Weitere Informationen finden Sie unter [Installieren und Verwenden von Hue in HDInsight Hadoop-Clustern](hdinsight-hadoop-hue-linux.md). |
| Installieren von Presto |`https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`. Weitere Informationen finden Sie unter [Installieren und Verwenden von Presto in HDInsight Hadoop-Clustern](hdinsight-hadoop-install-presto.md). |
| Installieren von Giraph |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Weitere Informationen finden Sie unter [Installieren von Apache Giraph in HDInsight Hadoop-Clustern und Verwenden von Giraph zur Verarbeitung großer Diagramme](hdinsight-hadoop-giraph-install-linux.md). |
| Vorabladen von Hive-Bibliotheken |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Weitere Informationen finden Sie unter [Hinzufügen benutzerdefinierter Apache Hive-Bibliotheken beim Erstellen des HDInsight-Clusters](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Verwenden einer Skriptaktion während der Clustererstellung

In diesem Abschnitt werden die verschiedenen Verwendungsmöglichkeiten von Skriptaktionen beim Erstellen eines HDInsight-Clusters erläutert.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Verwenden einer Skriptaktion während der Clustererstellung im Azure-Portal

1. Beginnen Sie mit der Erstellung eines Clusters, wie unter [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.](hdinsight-hadoop-provision-linux-clusters.md) beschrieben. Im Rahmen der Clustererstellung gelangen Sie zur Seite __Clusterzusammenfassung__. Wählen Sie auf der Seite __Clusterübersicht__ für __Erweiterte Einstellungen__ den Link __Bearbeiten__ aus.

    ![Link „Erweiterte Einstellungen“](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Wählen Sie im Abschnitt __Erweiterte Einstellungen__ die Option __Skriptaktionen__ aus. Wählen Sie im Abschnitt __Skriptaktionen__ die Option __+ Neue übermitteln__ aus.

    ![Skriptaktion „Neue übermitteln“](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Wählen Sie über den Eintrag __Skript auswählen__ ein vorgefertigtes Skript aus. Wählen Sie __Benutzerdefiniert__ aus, wenn Sie ein Skript verwenden möchten. Geben Sie dann __Name__ und __Bash-Skript-URI__ für Ihr Skript an.

    ![Hinzufügen eines Skripts im Formular „Skript auswählen“](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    Die folgende Tabelle beschreibt die Elemente des Formulars:

    | Eigenschaft | Wert |
    | --- | --- |
    | Auswählen eines Skripts | Wählen Sie __Benutzerdefiniert__ aus, wenn Sie ein eigenes Skript verwenden möchten. Wählen Sie andernfalls eines der bereitgestellten Skripts aus. |
    | NAME |Geben Sie einen Namen für die Skriptaktion an. |
    | Bash-Skript-URI |Geben Sie den URI des Skripts an. |
    | Haupt-/Worker-/Zookeeper-Knoten |Geben Sie die Knoten an, auf denen das Skript ausgeführt wird: **Hauptknoten**, **Worker** oder **ZooKeeper**. |
    | Parameter |Geben Sie die Parameter an, sofern dies für das Skript erforderlich ist. |

    Verwenden Sie den Eintrag __Speichern Sie diese Skriptaktion__, um sicherzustellen, dass das Skript bei Skalierungsvorgängen angewendet wird.

5. Wählen Sie __Erstellen__ aus, um das Skript zu speichern. Anschließend können Sie __+ Neue übermitteln__ verwenden, um ein weiteres Skript hinzufügen.

    ![Mehrere Skriptaktionen](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Wenn Sie das Hinzufügen von Skripts abgeschlossen haben, gelangen Sie über die Schaltflächen __Auswählen__ und __Weiter__ wieder zum Abschnitt __Clusterübersicht__.

3. Wählen Sie im Abschnitt __Clusterübersicht__ die Option __Erstellen__ aus, um den Cluster zu erstellen.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Verwenden einer Skriptaktion über Azure Resource Manager-Vorlagen

Skriptaktionen können mit Azure Resource Manager-Vorlagen verwendet werden. Ein Beispiel finden Sie unter [Create HDInsight Linux Cluster and run a script action](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/) (Erstellen eines HDInsight-Linux-Clusters und Ausführen einer Skriptaktion).

In diesem Beispiel wird die Skriptaktion mithilfe des folgenden Codes hinzugefügt:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Weitere Informationen zum Bereitstellen einer Vorlage finden Sie hier:

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Verwenden einer Skriptaktion während der Clustererstellung mit Azure PowerShell

In diesem Abschnitt verwenden Sie das Cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction), um Skripts zum Anpassen eines Clusters aufzurufen. Installieren und konfigurieren Sie zunächst Azure PowerShell. Um diese PowerShell-Befehle verwenden zu können, benötigen Sie das [AZ-Modul](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Das folgende Skript zeigt, wie Sie eine Skriptaktion anwenden, wenn Sie einen Cluster mithilfe von PowerShell erstellen:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Verwenden einer Skriptaktion während der Clustererstellung im HDInsight .NET SDK

Das HDInsight .NET SDK enthält Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen. Ein Codebeispiel finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mit dem .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster

In diesem Abschnitt erfahren Sie, wie Sie Skriptaktionen auf einen ausgeführten Cluster anwenden.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster über das Azure-Portal

Navigieren Sie zum [Azure-Portal](https://portal.azure.com):

1. Wählen Sie im Menü links **Alle Dienste** aus.

1. Wählen Sie unter **ANALYSEN** die Option **HDInsight clusters** (HDInsight-Cluster) aus.

1. Wählen Sie in der Liste Ihren Cluster aus. Daraufhin wird die Standardansicht geöffnet.

1. Klicken Sie in der Standardansicht unter **Einstellungen** auf **Script actions** (Skriptaktionen).

1. Wählen Sie oben auf der Seite **Script actions** (Skriptaktionen) die Option **+ Submit new** (+ Neue übermitteln) aus.

    ![Hinzufügen eines Skripts zu einem ausgeführten Cluster](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Wählen Sie über den Eintrag __Skript auswählen__ ein vorgefertigtes Skript aus. Wählen Sie __Benutzerdefiniert__ aus, wenn Sie ein Skript verwenden möchten. Geben Sie dann __Name__ und __Bash-Skript-URI__ für Ihr Skript an.

    ![Hinzufügen eines Skripts im Formular „Skript auswählen“](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    Die folgende Tabelle beschreibt die Elemente des Formulars:

    | Eigenschaft | Wert |
    | --- | --- |
    | Auswählen eines Skripts | Wählen Sie __Benutzerdefiniert__ aus, wenn Sie ein eigenes Skript verwenden möchten. Wählen Sie andernfalls ein bereitgestelltes Skript aus. |
    | NAME |Geben Sie einen Namen für die Skriptaktion an. |
    | Bash-Skript-URI |Geben Sie den URI des Skripts an. |
    | Haupt-/Worker-/Zookeeper-Knoten |Geben Sie die Knoten an, auf denen das Skript ausgeführt wird: **Hauptknoten**, **Worker** oder **ZooKeeper**. |
    | Parameter |Geben Sie die Parameter an, sofern dies für das Skript erforderlich ist. |

    Verwenden Sie den Eintrag __Speichern Sie diese Skriptaktion__, um sicherzustellen, dass das Skript bei Skalierungsvorgängen angewendet wird.

5. Wählen Sie abschließend die Schaltfläche **Erstellen** aus, um das Skript auf den Cluster anzuwenden.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster mit Azure PowerShell

Um diese PowerShell-Befehle verwenden zu können, benötigen Sie das [AZ-Modul](https://docs.microsoft.com/powershell/azure/overview).

Das folgende Beispiel zeigt, wie Sie eine Skriptaktion auf einen ausgeführten Cluster anwenden:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Nach Abschluss des Vorgangs sollten Informationen angezeigt werden, die in etwa wie folgt aussehen:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster über die Azure-Befehlszeilenschnittstelle (CLI)

Installieren und konfigurieren Sie zunächst die Azure-Befehlszeilenschnittstelle. Weitere Informationen finden Sie unter [Installieren der klassischen Azure CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Wechseln Sie in den Azure Resource Manager-Modus:

    ```bash
    azure config mode arm
    ```

2. Authentifizieren Sie sich bei Ihrem Azure-Abonnement:

    ```bash
    azure login
    ```

3. Wenden Sie eine Skriptaktion auf einen ausgeführten Cluster an:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Falls Sie die Parameter für diesen Befehl weglassen, werden Sie zur Angabe der Parameter aufgefordert. Wenn das mit `-u` angegebene Skript Parameter akzeptiert, können Sie diese mit dem Parameter `-p` angeben.

    Gültige Knotentypen sind `headnode`, `workernode` und `zookeeper`. Wenn das Skript auf mehrere Knotentypen angewendet werden soll, geben Sie die gewünschten Typen an, und trennen Sie sie jeweils durch ein Semikolon `;`. Beispiel: `-n headnode;workernode`.

    Fügen Sie `--persistOnSuccess` hinzu, um das Skript dauerhaft zu speichern. Sie können das Skript auch zu einem späteren Zeitpunkt mithilfe von `azure hdinsight script-action persisted set` dauerhaft speichern.

    Nach Abschluss des Auftrags wird eine Ausgabe wie die folgende zurückgegeben:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster mithilfe einer REST-API

Weitere Informationen finden Sie unter [Cluster REST API in Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx) (Cluster-REST-API in Azure HDInsight).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster über das HDInsight .NET SDK

Ein Beispiel für die Anwendung von Skripts auf einen Cluster mithilfe des .NET SDK finden Sie unter [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Anwenden einer Skriptaktion für einen ausgeführten Linux-basierten HDInsight-Cluster).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Anzeigen des Verlaufs und Höherstufen und Tieferstufen von Skriptaktionen

### <a name="the-azure-portal"></a>Das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Menü links **Alle Dienste** aus.

1. Wählen Sie unter **ANALYSEN** die Option **HDInsight clusters** (HDInsight-Cluster) aus.

1. Wählen Sie in der Liste Ihren Cluster aus. Daraufhin wird die Standardansicht geöffnet.

1. Klicken Sie in der Standardansicht unter **Einstellungen** auf **Script actions** (Skriptaktionen).

4. Im Abschnitt mit den Skriptaktionen wird ein Verlauf der Skripts für diesen Cluster angezeigt. Zu diesen Informationen gehört auch eine Liste der gespeicherten Skripts. Der folgende Screenshot zeigt, dass das Solr-Skript für diesen Cluster ausgeführt wurde. Auf dem Screenshot sind keine permanenten Skripts zu sehen.

    ![Skriptaktionen](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Wählen Sie ein Skript aus dem Verlauf aus, um den Abschnitt **Eigenschaften** für dieses Skript anzuzeigen. Oben im Fenster können Sie das Skript erneut ausführen oder höherstufen.

    ![Skriptaktionen, Eigenschaften](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Sie können im Abschnitt mit den Skriptaktionen auch die Auslassungspunkte ( **...** ) rechts neben den Einträgen auswählen, um Aktionen auszuführen.

    ![Skriptaktionen, Auslassungspunkte](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet | Funktion |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Abrufen von Informationen zu permanenten Skriptaktionen |
| `Get-AzHDInsightScriptActionHistory` |Abrufen eines Verlaufs der auf den Cluster angewendeten Skriptaktionen oder von Details zu einem bestimmten Skript |
| `Set-AzHDInsightPersistedScriptAction` |Höherstufen einer Ad-hoc-Skriptaktion zu einer permanenten Skriptaktion |
| `Remove-AzHDInsightPersistedScriptAction` |Tieferstufen einer permanenten Skriptaktion zu einer Ad-hoc-Aktion |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` macht die durch ein Skript ausgeführten Aktionen nicht rückgängig. Dieses Cmdlet entfernt nur das Flag für die dauerhafte Speicherung.

Das folgende Beispielskript veranschaulicht, wie die Cmdlets zum Höherstufen und anschließend zum Tieferstufen eines Skripts verwendet werden.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Die klassische Azure-Befehlszeilenschnittstelle

| Cmdlet | Funktion |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Abrufen einer Liste mit permanenten Skriptaktionen |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Abrufen von Informationen zu einer bestimmten permanenten Skriptaktion |
| `azure hdinsight script-action history list <clustername>` |Abrufen eines Verlaufs der auf den Cluster angewendeten Skriptaktionen |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Abrufen von Informationen zu einer bestimmten Skriptaktion |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Höherstufen einer Ad-hoc-Skriptaktion zu einer permanenten Skriptaktion |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Tieferstufen einer permanenten Skriptaktion zu einer Ad-hoc-Aktion |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` macht die durch ein Skript ausgeführten Aktionen nicht rückgängig. Dieses Cmdlet entfernt nur das Flag für die dauerhafte Speicherung.

### <a name="the-hdinsight-net-sdk"></a>Das HDInsight .NET SDK

Ein Beispiel für die Verwendung des .NET SDK zum Abrufen des Skriptverlaufs aus einem Cluster sowie zum Höherstufen oder Tieferstufen von Skripts finden Sie unter [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Anwenden einer Skriptaktion für einen ausgeführten Linux-basierten HDInsight-Cluster).

> [!NOTE]  
> Dieses Beispiel veranschaulicht auch die Installation einer HDInsight-Anwendung mithilfe des .NET SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Unterstützung für Open-Source-Software in HDInsight-Clustern

Der Microsoft Azure HDInsight-Dienst verwendet eine Reihe von Open-Source-Technologien für Apache Hadoop. Microsoft Azure bietet lediglich allgemeinen Support für Open-Source-Technologien. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/) im Abschnitt **Supportumfang**. Der HDInsight-Dienst bietet zusätzliche Unterstützung für integrierte Komponenten.

Im HDInsight-Dienst sind zwei Arten von Open-Source-Komponenten verfügbar:

* **Integrierte Komponenten:** Diese Komponenten sind in HDInsight-Clustern vorinstalliert und stellen Kernfunktionen des Clusters bereit. Zu dieser Kategorie gehören folgende Komponenten:

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Ressourcen-Manager)
  * Die Hive-Abfragesprache [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)
  * [Apache Mahout](https://mahout.apache.org/) 
    
    Eine vollständige Liste der Clusterkomponenten finden Sie unter [Welche Apache Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](hdinsight-component-versioning.md).

* **Benutzerdefinierte Komponenten:** Als Benutzer des Clusters können Sie in Ihrer Workload eine beliebige, in der Community verfügbare oder von Ihnen erstellte Komponente installieren oder verwenden.

> [!WARNING]  
> Mit dem HDInsight-Cluster bereitgestellte Komponenten werden vollständig unterstützt. Der Microsoft-Support unterstützt Sie beim Isolieren und Beheben von Problemen im Zusammenhang mit diesen Komponenten.
>
> Für benutzerdefinierte Komponenten steht wirtschaftlich angemessener Support für eine weiterführende Behandlung des Problems zur Verfügung. Unter Umständen kann Ihnen der Microsoft-Support bei der Lösung des Problems helfen. Möglicherweise werden Sie auch gebeten, verfügbare Kanäle für die Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. Hierzu kommen zahlreiche Communitywebsites in Frage. Beispiele wären etwa das [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) und [Stack Overflow](https://stackoverflow.com). 
>
> Für Apache-Projekte stehen auf der [Apache-Website](https://apache.org) auch Projektwebsites zur Verfügung. Ein Beispiel hierfür ist [Hadoop](https://hadoop.apache.org/).

Der HDInsight-Dienst bietet mehrere Möglichkeiten, benutzerdefinierte Komponenten zu verwenden. Der Supportumfang ist unabhängig davon, wie die Komponente verwendet oder im Cluster installiert wird. Die folgende Liste enthält die gängigsten Möglichkeiten für die Verwendung benutzerdefinierter Komponenten in HDInsight-Clustern:

1. **Auftragsübermittlung:** Hadoop-Aufträge und andere Auftragstypen, die benutzerdefinierte Komponenten ausführen oder verwenden, können an den Cluster übermittelt werden.

2. **Clusteranpassung:** Während der Clustererstellung können Sie zusätzliche Einstellungen und benutzerdefinierte Komponenten angeben, die auf den Clusterknoten installiert werden.

3. **Beispiele:** Für beliebte benutzerdefinierte Komponenten stellen Microsoft und andere Anbieter ggf. Beispiele für die Verwendung dieser Komponenten in HDInsight-Clustern bereit. Für diese Beispiele wird kein Support bereitgestellt.

## <a name="troubleshooting"></a>Problembehandlung

Über die Ambari-Webbenutzeroberfläche können Sie Informationen anzeigen, die von Skriptaktionen protokolliert wurden. Wenn das Skript bei der Clustererstellung einen Fehlers verursacht hat, sind die Protokolle auch im Standardspeicherkonto verfügbar, das dem Cluster zugeordnet ist. Dieser Abschnitt enthält Informationen zum Abrufen der Protokolle mit den beiden folgenden Optionen:

### <a name="the-apache-ambari-web-ui"></a>Apache Ambari-Webbenutzeroberfläche

1. Navigieren Sie im Browser zu https://CLUSTERNAME.azurehdinsight.net. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres HDInsight-Clusters.

    Geben Sie bei entsprechender Aufforderung den Namen (**admin**) und das Kennwort für den Cluster ein. Unter Umständen müssen die Administratoranmeldeinformationen in einem Webformular erneut eingegeben werden.

2. Wählen Sie in der Leiste oben auf der Seite die Option **ops** aus. Daraufhin wird eine Liste mit aktuellen und vorherigen Vorgängen angezeigt, die über Ambari für den Cluster ausgeführt wurden.

    ![Ambari-Webbenutzeroberfläche mit ausgewählter Option "ops"](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Suchen Sie nach den Einträgen, die **run\_customscriptaction** in der Spalte **Vorgänge** enthalten. Diese Einträge werden erstellt, wenn die Skriptaktionen ausgeführt werden.

    ![Screenshot von Vorgängen](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Wählen Sie den Eintrag **run\customscriptaction** aus, und navigieren Sie durch die Links, um die Ausgabe für **STDOUT** und **STDERR** anzuzeigen. Diese Ausgabe wird beim Ausführen des Skripts generiert und kann hilfreiche Informationen enthalten.

### <a name="access-logs-from-the-default-storage-account"></a>Rufen Sie Protokolle über das Standardspeicherkonto auf.

Für den Fall, dass die Clustererstellung aufgrund eines Fehlers in einem Skript nicht erfolgreich ist, finden Sie die Protokolle im Clusterspeicherkonto.

* Die Speicherprotokolle stehen unter `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` zur Verfügung.

    ![Screenshot von Vorgängen](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    In diesem Verzeichnis sind die Protokolle separat nach **Hauptknoten**, **Workerknoten** und **ZooKeeper-Knoten** strukturiert. Hierzu folgende Beispiele:

    * **Hauptknoten:** `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Workerknoten:** `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **ZooKeeper-Knoten:** `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Alle Ausgaben vom Typ **stdout** und **stderr** des entsprechenden Hosts werden in das Speicherkonto hochgeladen. Für die einzelnen Skriptaktionen sind jeweils die Dateien **output-\*.txt** und **errors-\*.txt** vorhanden. Die Datei **output-*.txt** enthält Informationen zum URI des Skripts, das auf dem Host ausgeführt wurde. Der folgende Text ist ein Beispiel für diese Informationen:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Es kann sein, dass Sie wiederholt eine Skriptaktion mit demselben Namen erstellen. In diesem Fall können Sie die relevanten Protokolle anhand des **Datums** im Ordnernamen unterscheiden. Die Ordnerstruktur für einen an verschiedenen Tagen erstellten Cluster namens **mycluster** ähnelt beispielsweise den folgenden Protokolleinträgen:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04``\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Wenn Sie am gleichen Tag einen Skriptaktionscluster mit demselben Namen erstellen, können Sie die relevanten Protokolldateien anhand des eindeutigen Präfixes ermitteln.

* Wenn Sie gegen 24:00 Uhr (Mitternacht) einen Cluster erstellen, umfassen die Protokolldateien unter Umständen zwei Tage. In diesem Fall sehen Sie für den gleichen Cluster zwei Ordner mit unterschiedlichen Datumsangaben.

* Das Hochladen von Protokolldateien in den Standardcontainer kann insbesondere bei großen Clustern bis zu fünf Minuten dauern. Wenn Sie also auf die Protokolle zugreifen möchten, sollten Sie nicht sofort den Cluster löschen, falls eine Skriptaktion nicht erfolgreich war.

### <a name="ambari-watchdog"></a>Ambari-Watchdog

> [!WARNING]  
> Lassen Sie das Kennwort für die Ambari-Watchdog-Komponente (hdinsightwatchdog) in Ihrem Linux-basierten HDInsight-Cluster unverändert. Wenn Sie das Kennwort für dieses Konto ändern, ist es nicht mehr möglich, im HDInsight-Cluster neue Skriptaktionen auszuführen.

### <a name="cant-import-name-blobservice"></a>„Cannot import name BlobService“ (Name BlobService kann nicht importiert werden)

__Symptome:__ Bei der Skriptaktion tritt ein Fehler auf. Eine Fehlermeldung ähnlich der folgenden wird angezeigt, wenn Sie den Vorgang in Ambari anzeigen:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Ursache__. Dieser Fehler tritt auf, wenn Sie den Python Azure Storage-Client aktualisieren, der im HDInsight-Cluster enthalten ist. HDInsight erwartet Azure Storage-Client 0.20.0.

__Lösung__. Stellen Sie zur Behebung dieses Fehlers mithilfe von `ssh` manuell eine Verbindung mit den einzelnen Clusterknoten her. Führen Sie den folgenden Befehl aus, um die korrekte Storage-Clientversion neu zu installieren:

```bash
sudo pip install azure-storage==0.20.0
```

Informationen zum Herstellen einer SSH-Verbindung mit dem Cluster finden Sie unter [Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Verlauf zeigt die Skripts nicht an, die bei der Clustererstellung verwendet wurden

Wenn der Cluster vor dem 15. März 2016 erstellt wurde, wird im Verlauf der Skriptaktionen unter Umständen kein Eintrag angezeigt. Durch das Ändern der Größe des Clusters werden die Skripts im Skriptaktionsverlaufs angezeigt.

Hierfür gelten zwei Ausnahmen:

* Ihr Cluster wurde vor dem 1. September 2015 erstellt. Dies ist das Datum, an dem die Skriptaktionen eingeführt wurden. Für Cluster, die vor diesem Datum erstellt wurden, können also keine Skriptaktionen für die Clustererstellung verwendet worden sein.

* Sie haben bei der Clustererstellung mehrere Skriptaktionen verwendet. Oder: Sie haben für mehrere Skripts den gleichen Namen oder den gleichen Namen und URI, aber unterschiedliche Parameter verwendet. In diesen Fällen erhalten Sie eine Fehlermeldung wie die folgende:

    Aufgrund von in Konflikt stehenden Skriptnamen in vorhandenen Skripts können in diesem Cluster keine neuen Skriptaktionen ausgeführt werden. Alle bei der Clustererstellung angegebenen Skriptnamen müssen eindeutig sein. Vorhandene Skripts werden beim Ändern der Größe ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

* [Entwickeln von Skriptaktionsskripts für HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installieren und Verwenden von Apache Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md)
* [Hinzufügen von zusätzlichem Speicher zu einem HDInsight-Cluster](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Phasen während der Clustererstellung"
