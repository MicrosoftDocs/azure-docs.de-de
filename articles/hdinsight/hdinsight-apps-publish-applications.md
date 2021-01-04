---
title: Veröffentlichen von Azure HDInsight-Anwendungen
description: Informationen zum Erstellen einer HDInsight-Anwendung und deren anschließender Veröffentlichung im Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: ca84cb6cdd6b47976eadbc5298701a46fe677426
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007136"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Veröffentlichen von HDInsight-Anwendungen im Azure Marketplace
Sie können eine Azure HDInsight-Anwendung auf einem Linux-basierten HDInsight-Cluster installieren. In diesem Artikel erfahren Sie, wie Sie eine HDInsight-Anwendung im Azure Marketplace veröffentlichen. Allgemeine Informationen zum Veröffentlichen im Azure Marketplace finden Sie unter [Veröffentlichen eines Angebots im Azure Marketplace](../marketplace/overview.md).

HDInsight-Anwendungen verwenden das *Bring-Your-Own-Lizenz*-Modell (BYOL). In einem BYOL-Szenario ist ein Anwendungsanbieter für die Lizenzierung der Anwendung für App-Benutzer verantwortlich. App-Benutzern werden nur die Azure-Ressourcen in Rechnung gestellt, die sie erstellen, z.B. der HDInsight-Cluster sowie die VMs und Knoten des Clusters. Die Abrechnung für die eigentliche Anwendung wird nicht über Azure abgewickelt.

Weitere Informationen finden Sie in diesen auf HDInsight-Anwendungen bezogenen Artikeln:

* [Installieren von HDInsight-Anwendungen](hdinsight-apps-install-applications.md). Erfahren Sie, wie Sie eine HDInsight-Anwendung auf Ihren Clustern installieren.
* [Installieren benutzerdefinierter HDInsight-Anwendungen](hdinsight-apps-install-custom-applications.md). Enthält Informationen zum Installieren und Testen benutzerdefinierter HDInsight-Anwendungen.

## <a name="prerequisites"></a>Voraussetzungen
Für die Übermittlung einer benutzerdefinierten Anwendung an den Marketplace [erstellen und testen Sie zuerst Ihre benutzerdefinierte Anwendung](hdinsight-apps-install-custom-applications.md).

Darüber hinaus müssen Sie auch Ihr Entwicklerkonto registrieren. Weitere Informationen hierzu finden Sie unter [Veröffentlichen eines Angebots im Azure Marketplace](../marketplace/overview.md) sowie unter [Erstellen eines Microsoft-Entwicklerkontos](../marketplace/overview.md).

## <a name="define-the-application"></a>Definieren der Anwendung
Die Veröffentlichung von Anwendungen im Marketplace erfolgt in zwei Schritten. Definieren Sie zuerst eine *createUiDef.json*-Datei. Die createUiDef.json-Datei gibt an, mit welchen Cluster Ihre Anwendung kompatibel ist. Veröffentlichen Sie dann die Vorlage aus dem Azure-Portal. Hier sehen Sie ein Beispiel für eine createUiDef.json-Datei:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Feld | BESCHREIBUNG | Mögliche Werte |
| --- | --- | --- |
| types |Die Clustertypen, mit denen die Anwendung kompatibel ist. |Hadoop, HBase, Storm, Spark (oder eine beliebige Kombination dieser Werte) |
| versions |Die HDInsight-Clustertypen, mit denen die Anwendung kompatibel ist. |3.4 |

## <a name="application-installation-script"></a>Anwendungsinstallationsskript
Wenn eine Anwendung auf einem Cluster (entweder einem bestehenden oder einem neuen) installiert ist, wird ein Edgeknoten erstellt. Das Installationsskript für die Anwendung wird auf dem Edgeknoten ausgeführt.

  > [!IMPORTANT]  
  > Der Name des Installationsskripts der Anwendung muss für einen bestimmten Cluster eindeutig sein. Der Skriptname muss das folgende Format aufweisen:
  > 
  > "name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > Der Skriptname besteht aus drei Teilen:
  > 
  > * Ein Skriptnamenpräfix, das entweder den Anwendungsnamen oder einen für die Anwendung relevanten Namen enthalten muss.
  > * Einen Bindestrich zur besseren Lesbarkeit.
  > * Eine eindeutige Zeichenfolgenfunktion mit dem Anwendungsnamen als Parameter.
  > 
  > In der Liste persistenter Skriptaktionen wird das vorhergehende Beispiel als **hue-install-v0-4wkahss55hlas** angezeigt. Hier sehen Sie eine [Beispiel-JSON-Nutzlast](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Das Installationsskript muss die folgenden Merkmale aufweisen:
* Das Skript ist idempotent. Mehrere Aufrufe des Skripts führen zum gleichen Ergebnis.
* Das Skript hat eine ordnungsgemäße Versionsangabe. Verwenden Sie beim Aktualisieren oder Testen von Änderungen einen anderen Speicherort für das Skript. Dadurch wird sichergestellt, dass Kunden, die die Anwendung installieren, von Ihren Updates oder Tests nicht betroffen sind. 
* Das Skript weist an jedem Punkt eine adäquate Protokollierung auf. Die Skriptprotokolle sind in der Regel die einzige Möglichkeit, Probleme bei der Installation von Anwendungen zu beheben.
* Für Aufrufe externer Dienste oder Ressourcen ist eine angemessene Anzahl von Wiederholungen vorgesehen, damit die Installation nicht von vorübergehenden Netzwerkproblemen betroffen ist.
* Wenn Ihr Skript Dienste auf den Knoten startet, werden die Dienste überwacht und so konfiguriert, dass sie bei Neustarts von Knoten automatisch gestartet werden.

## <a name="package-the-application"></a>Packen der Anwendung
Erstellen Sie eine ZIP-Datei mit allen Dateien, die für die Installation der HDInsight-Anwendung erforderlich sind. Sie verwenden die ZIP-Datei zum Veröffentlichen der Anwendung. Die ZIP-Datei enthält die folgenden Dateien:

* createUiDefinition.json
* mainTemplate.json (Ein Beispiel finden Sie unter [Installieren von benutzerdefinierten Hadoop-Anwendungen in Azure HDInsigh](hdinsight-apps-install-custom-applications.md).)
* Alle erforderlichen Skripts

> [!NOTE]  
> Sie können die Dateien der Anwendung (einschließlich aller Web-App-Dateien) auf allen öffentlich zugänglichen Endpunkten hosten.

## <a name="publish-the-application"></a>Veröffentlichen der Anwendung
So veröffentlichen Sie eine HDInsight-Anwendung:

1. Melden Sie sich für die [Azure-Veröffentlichung](https://publish.windowsazure.com/) an.
2. Wählen Sie im linken Menü **Lösungsvorlagen**.
3. Geben Sie einen Titel ein, und wählen Sie dann **Neue Lösungsvorlage erstellen**.
4. Wenn Sie Ihr Unternehmen noch nicht registriert haben, wählen Sie **Create Dev Center account and join the Azure program** (Dev Center-Konto erstellen und am Azure-Programm teilnehmen).  Weitere Informationen hierzu finden Sie unter [Erstellen eines Microsoft-Entwicklerkontos](../marketplace/overview.md).
5. Wählen Sie **Define some Topologies to get Started** (Definieren einiger Topologien für den Einstieg). Eine Lösungsvorlage ist allen zugehörigen Topologien übergeordnet. Sie können in einem Angebot/einer Lösungsvorlage mehrere Topologien definieren. Wenn ein Angebot in die Stagingumgebung überführt wird, werden alle Topologien einbezogen. 
6. Geben Sie einen Topologienamen ein, und wählen Sie dann **+** .
7. Geben Sie eine neue Version ein, und wählen Sie dann **+** .
8. Laden Sie die ZIP-Datei hoch, die Sie erstellt haben, als Sie die Anwendung gepackt haben.  
9. Wählen Sie **Zertifizierung anfordern**. Das Microsoft-Zertifizierungsteam prüft die Dateien und zertifiziert die Topologie.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie auf Ihren Clustern eine [HDInsight-Anwendung installieren](hdinsight-apps-install-applications.md).
* Lernen Sie das [Installieren von benutzerdefinierten Hadoop-Anwendungen in Azure HDInsight](hdinsight-apps-install-custom-applications.md), und wie Sie eine nicht veröffentlichte HDInsight-Anwendung in HDInsight bereitstellen.
* Lernen Sie das [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md) und das Hinzufügen weiterer Anwendungen. 
* Erfahren Sie etwas über das [Erstellen Linux-basierter Apache Hadoop-Cluster in HDInsight mithilfe von Azure Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Lernen Sie das [Verwenden leerer Edgeknoten in HDInsight](hdinsight-apps-use-edge-node.md) zum Zugreifen auf HDInsight-Cluster, Testen von HDInsight-Anwendungen und Hosten von HDInsight-Anwendungen.