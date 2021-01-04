---
title: Anpassen von Azure HDInsight-Clustern mithilfe von Skriptaktionen
description: Hier erfahren Sie, wie Sie HDInsight-Clustern mithilfe von Skriptaktionen benutzerdefinierte Komponenten hinzufügen. Skriptaktionen sind Bash-Skripts, die zum Anpassen der Clusterkonfiguration verwendet werden können. Sie können auch zusätzliche Dienste und Hilfsprogramme wie Hue, Solr oder R hinzufügen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli
ms.date: 09/02/2020
ms.openlocfilehash: 35c3901e9a48523a10c1a6aacbc52e6c165e278f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009788"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Anpassen von Azure HDInsight-Clustern mithilfe von Skriptaktionen

Azure HDInsight verfügt über eine Konfigurationsmethode namens **Skriptaktionen**, bei der benutzerdefinierte Skripts zum Anpassen des Clusters aufgerufen werden. Diese Skripts werden auch zum Installieren weitere Komponenten und zum Ändern von Konfigurationseinstellungen verwendet. Skriptaktionen können während oder nach der Clustererstellung verwendet werden.

Skriptaktionen können auch als HDInsight-Anwendung im Azure Marketplace veröffentlicht werden. Weitere Informationen zu HDInsight-Anwendungen finden Sie unter [Veröffentlichen von HDInsight-Anwendungen im Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Berechtigungen

Für einen in eine Domäne eingebundenen HDInsight-Cluster sind zwei Ambari-Berechtigungen erforderlich, um Skriptaktionen mit dem Cluster verwenden zu können:

* **AMBARI.RUN\_CUSTOM\_COMMAND:** Die Rolle „Ambari-Administrator“ verfügt standardmäßig über diese Berechtigung.
* **CLUSTER.RUN\_CUSTOM\_COMMAND:** Sowohl der HDInsight-Clusteradministrator als auch der Ambari-Administrator verfügt standardmäßig über diese Berechtigung.

Weitere Informationen zur Verwendung von Berechtigungen mit in eine Domäne eingebundenem HDInsight finden Sie unter [Verwalten von HDInsight-Clustern mit dem Enterprise-Sicherheitspaket](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Zugriffssteuerung

Wenn Sie nicht der Administrator oder Besitzer des Azure-Abonnements sind, muss Ihr Konto mindestens Zugriff vom Typ „Mitwirkender“ auf die Ressourcengruppe haben, die den HDInsight-Cluster enthält.

Ein Benutzer, der für das Azure-Abonnement mindestens über Zugriff der Stufe „Mitwirkender“ verfügt, muss den Anbieter bereits registriert haben. Die Anbieterregistrierung findet statt, wenn ein Benutzer, der über Abonnementzugriff der Stufe „Mitwirkender“ verfügt, eine Ressource erstellt. Informationen zur Registrierung ohne Erstellung einer Ressource finden Sie unter [Registrieren eines Anbieters mithilfe von REST](/rest/api/resources/providers#Providers_Register).

Weitere Informationen zur Verwendung der Zugriffsverwaltung finden Sie hier:

* [Erste Schritte mit der Zugriffsverwaltung im Azure-Portal](../role-based-access-control/overview.md)
* [Verwenden von Rollenzuweisungen zum Verwalten Ihrer Azure-Abonnementressourcen](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Grundlegendes zu Skriptaktionen

Eine Skriptaktion ist ein Bash-Skript, das auf den Knoten in einem HDInsight-Cluster ausgeführt wird. Skriptaktionen verfügen über folgende Merkmale und Features:

* Sie müssen als URI gespeichert werden, der für den HDInsight-Cluster zugänglich ist. Dies sind zwei mögliche Speicherorte:

    * Für normale Cluster:

      * ADLS Gen1: Der Dienstprinzipal, der von HDInsight zum Zugreifen auf Data Lake Storage genutzt wird, muss über Lesezugriff auf das Skript verfügen. Für in Data Lake Storage Gen1 gespeicherte Skripts wird das folgende URI-Format verwendet: `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Ein Blob in einem Azure Storage-Konto, das entweder das primäre oder ein zusätzliches Speicherkonto für den HDInsight-Cluster darstellt. HDInsight wird während der Clustererstellung Zugriff auf beide Typen von Speicherkonten gewährt.

        > [!IMPORTANT]  
        > Rotieren Sie den Speicherschlüssel für dieses Azure Storage-Konto nicht, da dies dazu führt, dass nachfolgende Skriptaktionen mit darin gespeicherten Skripts fehlschlagen.

      * Ein öffentlicher Dateifreigabedienst, auf den über „http://“-Pfade zugegriffen werden kann. Beispiele sind Azure Blob, GitHub und OneDrive. Beispiel-URIs finden Sie unter [Beispielskripts für Skriptaktionen](#example-script-action-scripts).

     * Für Cluster mit ESP werden URIs vom Typ „wasb://“ oder „wasbs://“ oder „http[s]://“ unterstützt.

* Die Ausführung kann auf bestimmte Knotentypen beschränkt werden. Beispiele wären etwa Hauptknoten und Workerknoten.

* Sie können permanent oder `ad hoc` sein.

    Permanente Skriptaktionen müssen einen eindeutigen Namen haben. Permanente Skripts werden verwendet, um mithilfe von Skalierungsvorgängen neue Workerknoten anzupassen, die dem Cluster hinzugefügt wurden. Bei Skalierungsvorgängen kann ein permanentes Skript auch Änderungen auf einen anderen Knotentyp anwenden. Ein Beispiel wäre etwa ein Hauptknoten.

    `Ad hoc`-Skripts werden nicht beibehalten. Skriptaktionen, die während der Clustererstellung verwendet werden, werden automatisch zu permanenten Skripts. Sie werden nicht auf Workerknoten angewendet, die dem Cluster hinzugefügt werden, nachdem das Skript ausgeführt wurde. Sie können dann ein `ad hoc`-Skript in ein permanentes Skript oder ein permanentes Skript in ein `ad hoc`-Skript umwandeln. Skripts, deren Ausführung nicht erfolgreich ist, werden nicht zu permanenten Skripts. Dies gilt auch, wenn Sie speziell angeben, dass dies der Fall sein soll.

* Sie können Parameter akzeptieren, die von den Skripts während der Ausführung verwendet werden.

* Sie werden mit Stammebenenberechtigungen auf den Clusterknoten ausgeführt.

* Sie können über das Azure-Portal, Azure PowerShell, die Azure CLI oder das HDInsight .NET SDK verwendet werden.

* Skriptaktionen zum Entfernen oder Ändern von Dienstdateien auf der VM können die Dienstintegrität und -verfügbarkeit beeinträchtigen.

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

Sie können Dienste beenden und starten, einschließlich Diensten in Zusammenhang mit Apache Hadoop. Wenn Sie Dienste beenden, stellen Sie sicher, dass Ambari und andere Hadoop-bezogene Dienste ausgeführt werden, bevor die Ausführung des Skripts abgeschlossen ist. Diese erforderlichen Dienste ermitteln die Integrität und den Zustand des Clusters, während dieser erstellt wird.

Während der Clustererstellung können mehrere Skriptaktionen gleichzeitig verwendet werden. Diese Skripts werden in der Reihenfolge aufgerufen, in der sie angegeben wurden.

> [!IMPORTANT]  
> Skriptaktionen müssen innerhalb von 60 Minuten abgeschlossen werden. Andernfalls tritt ein Timeout auf. Während der Clusterbereitstellung wird das Skript gleichzeitig mit anderen Einrichtungs- und Konfigurationsprozessen ausgeführt. Der Wettbewerb um Ressourcen wie CPU-Zeit oder Netzwerkbandbreite kann dazu führen, dass es länger als in Ihrer Entwicklungsumgebung dauert, bis das Skript abgeschlossen ist.
>
> Um die Ausführungsdauer des Skripts zu minimieren, vermeiden Sie Aufgaben wie das Herunterladen und Kompilieren von Anwendungen aus der Quelle. Kompilieren Sie Anwendungen vor, und speichern Sie die Binärdateien in Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Skriptaktion in einem ausgeführten Cluster

Ein Skriptfehler in einem bereits ausgeführten Cluster führt nicht automatisch dazu, dass der Cluster in einen Fehlerzustand versetzt wird. Nach Abschluss eines Skripts sollte der Cluster wieder in den Ausführungszustand zurückkehren. Auch wenn sich der Cluster im Zustand „Wird ausgeführt“ befindet, kann es sein, dass das fehlerhafte Skript etwas beschädigt hat. Ein Skript kann beispielsweise vom Cluster benötigte Dateien löschen.

Skriptaktionen werden mit Stammberechtigungen ausgeführt. Stellen Sie also sicher, dass Sie die Auswirkungen eines Skripts verstehen, bevor Sie es auf den Cluster anwenden.

Wenn Sie ein Skript auf einem Cluster anwenden, ändert sich der Clusterzustand von **Wird ausgeführt** in **Akzeptiert**. Danach ändert sich der Zustand in **HDInsight-Konfiguration**, bevor er schließlich wieder **Wird ausgeführt** lautet (bei einem erfolgreichen Skript). Der Skriptstatus wird im Skriptaktionsverlauf protokolliert. Diese Angabe gibt Aufschluss darüber, ob das Skript erfolgreich ausgeführt wurde. Das PowerShell-Cmdlet `Get-AzHDInsightScriptActionHistory` zeigt beispielsweise den Status eines Skripts an. Die zurückgegebenen Informationen sehen in etwa wie folgt aus:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Wenn Sie nach dem Erstellen des Clusters das Kennwort für den Clusterbenutzer (Admin) ändern, sind Skriptaktionen, die für diesen Cluster ausgeführt werden, möglicherweise nicht mehr erfolgreich. Falls Sie über permanente Skriptaktionen für Workerknoten verfügen, sind diese unter Umständen nicht erfolgreich, wenn Sie den Cluster skalieren.

## <a name="example-script-action-scripts"></a>Beispielskripts für Skriptaktionen

Skripts für Skriptaktionen können über die folgenden Hilfsprogramme verwendet werden:

* Azure-Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET-SDK

HDInsight verfügt über Skripts zum Installieren der folgenden Komponenten auf HDInsight-Clustern:

| Name | Skript |
| --- | --- |
| Hinzufügen eines Azure Storage-Kontos |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](hdinsight-hadoop-add-storage.md). |
| Installieren von Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Weitere Informationen finden Sie unter [Installieren und Verwenden von Hue in HDInsight Hadoop-Clustern](hdinsight-hadoop-hue-linux.md). |
| Vorabladen von Hive-Bibliotheken |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Weitere Informationen finden Sie unter [Hinzufügen benutzerdefinierter Apache Hive-Bibliotheken beim Erstellen des HDInsight-Clusters](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Skriptaktion während der Clustererstellung

In diesem Abschnitt werden die verschiedenen Verwendungsmöglichkeiten von Skriptaktionen beim Erstellen eines HDInsight-Clusters erläutert.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Verwenden einer Skriptaktion während der Clustererstellung im Azure-Portal

1. Beginnen Sie mit dem Erstellen eines Clusters wie unter [Erstellen von Linux-basierten Clustern in HDInsight mit dem Azure-Portal](hdinsight-hadoop-create-linux-clusters-portal.md) beschrieben. Wählen Sie in der Registerkarte **Konfiguration + Preise****+ Skriptaktion hinzufügen** aus.

    ![Azure-Portal – Aktion „Clusterskript“](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Wählen Sie über den Eintrag __Skript auswählen__ ein vorgefertigtes Skript aus. Wählen Sie __Benutzerdefiniert__ aus, wenn Sie ein Skript verwenden möchten. Geben Sie dann __Name__ und __Bash-Skript-URI__ für Ihr Skript an.

    ![Hinzufügen eines Skripts im Formular „Skript auswählen“](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Die folgende Tabelle beschreibt die Elemente des Formulars:

    | Eigenschaft | Wert |
    | --- | --- |
    | Auswählen eines Skripts | Wählen Sie __Benutzerdefiniert__ aus, wenn Sie ein eigenes Skript verwenden möchten. Wählen Sie andernfalls eines der bereitgestellten Skripts aus. |
    | Name |Geben Sie einen Namen für die Skriptaktion an. |
    | Bash-Skript-URI |Geben Sie den URI des Skripts an. |
    | Haupt-/Workerknoten/Zookeeper |Geben Sie die Knoten an, auf denen das Skript ausgeführt wird: **Hauptknoten**, **Worker** oder **ZooKeeper**. |
    | Parameter |Geben Sie die Parameter an, sofern dies für das Skript erforderlich ist. |

    Verwenden Sie den Eintrag __Speichern Sie diese Skriptaktion__, um sicherzustellen, dass das Skript bei Skalierungsvorgängen angewendet wird.

1. Wählen Sie __Erstellen__ aus, um das Skript zu speichern. Anschließend können Sie __+ Neue übermitteln__ verwenden, um ein weiteres Skript hinzufügen.

    ![HDInsight – Mehrere Skriptaktionen](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Kehren Sie zur Registerkarte **Konfiguration + Preise** zurück, wenn Sie alle gewünschten Skripts hinzugefügt haben.

1. Führen Sie die verbleibenden Schritte der Clustererstellung wie gewohnt durch.

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

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Verwenden einer Skriptaktion während der Clustererstellung mit Azure PowerShell

In diesem Abschnitt verwenden Sie das Cmdlet [Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction), um Skripts zum Anpassen eines Clusters aufzurufen. Installieren und konfigurieren Sie zunächst Azure PowerShell. Um diese PowerShell-Befehle verwenden zu können, benötigen Sie das [AZ-Modul](/powershell/azure/).

Das folgende Skript zeigt, wie Sie eine Skriptaktion anwenden, wenn Sie einen Cluster mithilfe von PowerShell erstellen:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Verwenden einer Skriptaktion während der Clustererstellung im HDInsight .NET SDK

Das HDInsight .NET SDK enthält Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen. Ein Codebeispiel finden Sie unter [Skriptaktionen](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Skriptaktion auf einem ausgeführten Cluster

In diesem Abschnitt erfahren Sie, wie Sie Skriptaktionen auf einen ausgeführten Cluster anwenden.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie den Cluster.

1. Klicken Sie in der Standardansicht unter **Einstellungen** auf **Script actions** (Skriptaktionen).

1. Wählen Sie oben auf der Seite **Script actions** (Skriptaktionen) die Option **+ Submit new** (+ Neue übermitteln) aus.

    ![Hinzufügen eines Skripts zu einem ausgeführten Cluster](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Wählen Sie über den Eintrag __Skript auswählen__ ein vorgefertigtes Skript aus. Wählen Sie __Benutzerdefiniert__ aus, wenn Sie ein Skript verwenden möchten. Geben Sie dann __Name__ und __Bash-Skript-URI__ für Ihr Skript an.

    ![Hinzufügen eines Skripts im Formular „Skript auswählen“](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    Die folgende Tabelle beschreibt die Elemente des Formulars:

    | Eigenschaft | Wert |
    | --- | --- |
    | Auswählen eines Skripts | Wählen Sie __Benutzerdefiniert__ aus, wenn Sie ein eigenes Skript verwenden möchten. Wählen Sie andernfalls ein bereitgestelltes Skript aus. |
    | Name |Geben Sie einen Namen für die Skriptaktion an. |
    | Bash-Skript-URI |Geben Sie den URI des Skripts an. |
    | Haupt-/Worker-/Zookeeper-Knoten |Geben Sie die Knoten an, auf denen das Skript ausgeführt wird: **Hauptknoten**, **Worker** oder **ZooKeeper**. |
    | Parameter |Geben Sie die Parameter an, sofern dies für das Skript erforderlich ist. |

    Verwenden Sie den Eintrag __Speichern Sie diese Skriptaktion__, um sicherzustellen, dass das Skript bei Skalierungsvorgängen angewendet wird.

1. Wählen Sie abschließend die Schaltfläche **Erstellen** aus, um das Skript auf den Cluster anzuwenden.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster mit Azure PowerShell

Um diese PowerShell-Befehle verwenden zu können, benötigen Sie das [AZ-Modul](/powershell/azure/). Das folgende Beispiel zeigt, wie Sie eine Skriptaktion auf einen ausgeführten Cluster anwenden:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Nach Abschluss des Vorgangs sollten Informationen angezeigt werden, die in etwa wie folgt aussehen:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster über die Azure-Befehlszeilenschnittstelle (CLI)

Installieren und konfigurieren Sie zunächst die Azure-Befehlszeilenschnittstelle. Stellen Sie sicher, dass Sie über die aktuelle Version verfügen. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

1. Authentifizieren Sie sich bei Ihrem Azure-Abonnement:

    ```azurecli
    az login
    ```

1. Wenden Sie eine Skriptaktion auf einen ausgeführten Cluster an:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Gültige Rollen sind `headnode`, `workernode`, `zookeepernode`, `edgenode`. Wenn das Skript auf mehrere Knotentypen angewendet werden soll, trennen Sie die Rollen durch ein Leerzeichen. Beispiel: `--roles headnode workernode`.

    Fügen Sie `--persist-on-success` hinzu, um das Skript dauerhaft zu speichern. Sie können das Skript auch zu einem späteren Zeitpunkt mithilfe von `az hdinsight script-action promote` dauerhaft speichern.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster mithilfe einer REST-API

Weitere Informationen finden Sie unter [Cluster REST API in Azure HDInsight](/rest/api/hdinsight/hdinsight-cluster) (Cluster-REST-API in Azure HDInsight).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Anwenden einer Skriptaktion auf einen ausgeführten Cluster über das HDInsight .NET SDK

Ein Beispiel für die Anwendung von Skripts auf einen Cluster mithilfe des .NET SDK finden Sie unter [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Anwenden einer Skriptaktion für einen ausgeführten Linux-basierten HDInsight-Cluster).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Anzeigen des Verlaufs und Höherstufen und Tieferstufen von Skriptaktionen

### <a name="the-azure-portal"></a>Das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie den Cluster.

1. Klicken Sie in der Standardansicht unter **Einstellungen** auf **Script actions** (Skriptaktionen).

1. Im Abschnitt mit den Skriptaktionen wird ein Verlauf der Skripts für diesen Cluster angezeigt. Zu diesen Informationen gehört auch eine Liste der gespeicherten Skripts. Der folgende Screenshot zeigt, dass das Solr-Skript für diesen Cluster ausgeführt wurde. Auf dem Screenshot sind keine permanenten Skripts zu sehen.

    ![Skriptaktionen des Portals – Übermittlungsverlauf](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Wählen Sie ein Skript aus dem Verlauf aus, um den Abschnitt **Eigenschaften** für dieses Skript anzuzeigen. Oben im Fenster können Sie das Skript erneut ausführen oder höherstufen.

    ![Skriptaktionen – Eigenschaften höherstufen](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Sie können im Abschnitt mit den Skriptaktionen auch die Auslassungspunkte ( **...** ) rechts neben den Einträgen auswählen, um Aktionen auszuführen.

    ![Permanente Skriptaktionen – Löschen](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet | Funktion |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Abrufen von Informationen zu permanenten Skriptaktionen Mit diesem Cmdlet werden die mit einem Skript durchgeführten Aktionen nicht rückgängig gemacht, sondern nur das Persistenzflag entfernt.|
| `Get-AzHDInsightScriptActionHistory` |Abrufen eines Verlaufs der auf den Cluster angewendeten Skriptaktionen oder von Details zu einem bestimmten Skript |
| `Set-AzHDInsightPersistedScriptAction` |Höherstufen einer `ad hoc`-Skriptaktion zu einer permanenten Skriptaktion |
| `Remove-AzHDInsightPersistedScriptAction` |Tieferstufen einer permanenten Skriptaktion zu einer `ad hoc`-Aktion |

Das folgende Beispielskript veranschaulicht, wie die Cmdlets zum Höherstufen und anschließend zum Tieferstufen eines Skripts verwendet werden.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Get-Help | BESCHREIBUNG |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |Löscht eine angegebene permanente Skriptaktion des Clusters. Mit diesem Befehl werden die mit einem Skript durchgeführten Aktionen nicht rückgängig gemacht, sondern nur das Persistenzflag entfernt.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|Ausführen von Skriptaktionen für den angegebenen HDInsight-Cluster|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |Listet alle permanenten Skriptaktionen für den angegebenen Cluster auf. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|Listet den Ausführungsverlauf aller Skripts für den angegebenen Cluster auf.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|Stuft die angegebene Ad-hoc-Skriptausführung auf ein permanentes Skript hoch.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|Ruft die Details zur Skriptausführung für die angegebene Skriptausführungs-ID ab.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET-SDK

Ein Beispiel für die Verwendung des .NET SDK zum Abrufen des Skriptverlaufs aus einem Cluster sowie zum Höherstufen oder Tieferstufen von Skripts finden Sie unter [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Anwenden einer Skriptaktion für einen ausgeführten Linux-basierten HDInsight-Cluster).

> [!NOTE]  
> Dieses Beispiel veranschaulicht auch die Installation einer HDInsight-Anwendung mithilfe des .NET SDK.

## <a name="next-steps"></a>Nächste Schritte

* [Entwickeln von Skriptaktionsskripts für HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Hinzufügen von zusätzlichem Speicher zu einem HDInsight-Cluster](hdinsight-hadoop-add-storage.md)
* [Problembehandlung für Skriptaktionen](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Phasen während der Clustererstellung"
