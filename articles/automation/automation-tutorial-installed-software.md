---
title: Ermitteln der auf Ihren VMs installierten Software mit Azure Automation | Microsoft-Dokumentation
description: In diesem Artikel wird die Software beschrieben, die auf VMs in Ihrer Umgebung installiert ist.
services: automation
keywords: Inventar, Inventur, Bestand, Automatisierung, Änderungsnachverfolgung
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593777"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Ermitteln der auf Ihren VMs installierten Software

In diesem Tutorial erfahren Sie, wie Sie mithilfe des Features „Azure Automation-Änderungsnachverfolgung und Bestand“ herausfinden können, welche Software in Ihrer Umgebung installiert ist. Sie können die Bestandsinformationen für Software, Dateien, Linux-Daemons, Windows-Dienste und Windows-Registrierungsschlüssel auf Ihren Computern sammeln und anzeigen. Durch das Nachverfolgen der Konfigurationen Ihrer Computer können Sie betriebsbezogene Problem in Ihrer Umgebung erkennen und ein besseres Verständnis des Zustands Ihrer Computer entwickeln.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren der Lösung für Änderungsnachverfolgung und Bestand
> * Aktivieren einer Azure-VM
> * Aktivieren einer Nicht-Azure-VM
> * Anzeigen von installierter Software
> * Durchsuchen von Bestandsprotokollen nach installierter Software

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* Ein [Automation-Konto](./index.yml) für die Watcher- und Aktionsrunbooks und den Watchertask.
* Ein [virtueller Computer](../virtual-machines/windows/quick-create-portal.md) zur Aktivierung für das Feature.

## <a name="log-in-to-azure"></a>Anmelden an Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="enable-change-tracking-and-inventory"></a>Aktivieren der Lösung für Änderungsnachverfolgung und Bestand

In diesem Tutorial müssen Sie zuerst die Änderungsnachverfolgung und die Bestandsfunktion aktivieren. Wenn Sie das Feature bereits aktiviert haben, ist dieser Schritt nicht erforderlich.

>[!NOTE]
>Wenn die Felder abgeblendet sind, ist ein anderes Automation-Feature für die VM aktiviert, und Sie müssen denselben Arbeitsbereich und dasselbe Automation-Konto verwenden.

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Konfigurationsverwaltung** entweder **Bestand** oder **Änderungsnachverfolgung** aus.

2. Wählen Sie den Arbeitsbereich [Log Analytics](../azure-monitor/logs/log-query-overview.md) aus. Dieser Arbeitsbereich sammelt Daten, die durch Features wie „Änderungsnachverfolgung“ und „Bestand“ generiert werden. Der Arbeitsbereich ist ein zentraler Ort zum Überprüfen und Analysieren von Daten aus mehreren Quellen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Wählen Sie das Automation-Konto aus, das Sie verwenden möchten.

4. Konfigurieren Sie den Speicherort für die Bereitstellung.

5. Klicken Sie auf **Aktivieren**, um das Feature für Ihre VM bereitzustellen. 

    ![Banner für die Bestandskonfiguration](./media/automation-tutorial-installed-software/enableinventory.png)

Während des Setups wird der virtuelle Computer mit dem Log Analytics-Agent für Windows und einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) bereitgestellt. Das Aktivieren der Änderungsnachverfolgung und des Bestands kann bis zu 15 Minuten dauern. Während dieses Zeitraums sollten Sie das Browserfenster nicht schließen.

Nachdem das Feature aktiviert wurde, werden Informationen zur installierten Software und Änderungen der VM-Datenflüsse zu Azure Monitor-Protokollen angezeigt. Es kann zwischen 30 Minuten und 6 Stunden dauern, bis die Daten für die Analyse verfügbar sind.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Hinzufügen einer Azure VM zur Änderungsnachverfolgung und zum Bestand

1. Navigieren Sie in Ihrem Automation-Konto unter **Konfigurationsverwaltung** zu **Bestand** oder **Änderungsnachverfolgung**.

2. Wählen Sie **+ Azure VM hinzufügen** aus.

3. Wählen Sie Ihren virtuellen Computer in der Liste der VMs aus. 

4. Klicken Sie auf **Aktivieren**, um die Änderungsnachverfolgung und den Bestand auf der VM zu aktivieren. Der Log Analytics-Agent für Windows wird auf der VM bereitgestellt und konfiguriert die VM so, dass sie mit dem Log Analytics-Arbeitsbereich kommuniziert. Der Einrichtungsvorgang kann einige Minuten dauern. 

5. An diesem Punkt können Sie ggf. eine neue VM aus der Liste auswählen, um sie für das Feature zu aktivieren.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Hinzufügen eines Nicht-Azure-Computers zur Änderungsnachverfolgung und zum Bestand

So aktivieren Sie Nicht-Azure-Computer für das Feature

1. Installieren Sie je nach Ihrem Betriebssystem den [Log Analytics-Agent für Windows](../azure-monitor/agents/agent-windows.md) oder den [Log Analytics-Agent für Linux](automation-linux-hrw-install.md). 

2. Navigieren Sie zu Ihrem Automation-Konto und dann unter **Konfigurationsverwaltung** entweder zu **Bestand** oder **Änderungsnachverfolgung**. 

3. Klicken Sie auf **Computer verwalten**. Ihnen wird eine Liste der Computer angezeigt, die in Ihrem Log Analytics-Arbeitsbereich Bericht erstatten und auf denen die Änderungsnachverfolgung und der Bestand nicht aktiviert sind. Wählen Sie die passende Option für Ihre Umgebung aus:

    * **Auf allen verfügbaren Computern aktivieren**: Diese Option aktiviert das Feature auf allen Computern, die derzeit mit Ihrem Log Analytics-Arbeitsbereich verknüpft sind.
    * **Auf allen verfügbaren und zukünftigen Computern aktivieren**: Diese Option aktiviert das Feature auf allen Computern, die derzeit mit Ihrem Log Analytics-Arbeitsbereich verknüpft sind, sowie auf allen Computern, die dem Arbeitsbereich noch hinzugefügt werden.
    * **Auf ausgewählten Computern aktivieren**: Diese Option aktiviert das Feature nur auf den ausgewählten Computern.

    ![Computer verwalten](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Anzeigen von installierter Software

Nachdem das Feature für die Änderungsnachverfolgung und den Bestand aktiviert wurde, können Sie die Ergebnisse auf der Seite „Bestand“ anzeigen.

1. Wählen Sie in Ihrem Automation-Konto unter **Konfigurationsverwaltung** die Option **Bestand** aus.

2. Klicken Sie auf der Seite „Bestand“ auf die Registerkarte **Software**.

3. Beachten Sie die Tabelle mit der Liste der gefundenen Software. Die Software ist nach Name und Version der Software gruppiert. Die allgemeinen Details zu den einzelnen Softwaredatensätzen können in der Tabelle angezeigt werden. Diese Details enthalten den Softwarenamen, die Version, den Herausgeber, den letzten Aktualisierungszeitpunkt (letzter Aktualisierungszeitpunkt, der von einem Computer der Gruppe gemeldet wurde) und die Computer (Anzahl von Computern mit der jeweiligen Software).

    ![Softwarebestand](./media/automation-tutorial-installed-software/inventory-software.png)

4. Klicken Sie auf eine Zeile, um die Eigenschaften des Softwaredatensatzes und die Namen der Computer mit dieser Software anzuzeigen.

5. Sie können im Textfeld direkt oberhalb der Softwareliste eine Suche durchführen, um nach einer bestimmten Software oder Softwaregruppe zu suchen.
Der Filter ermöglicht Ihnen die Suche anhand des Softwarenamens, der Version und des Herausgebers. Wenn Sie beispielsweise nach **Contoso** suchen, wird die gesamte Software zurückgegeben, deren Name, Herausgeber oder Version den Text **Contoso** enthält.

## <a name="search-inventory-logs-for-installed-software"></a>Durchsuchen von Bestandsprotokollen nach installierter Software

Bei Änderungsnachverfolgung und Bestand werden Protokolldaten generiert, die an Azure Monitor-Protokolle gesendet werden. Um die Protokolle per Ausführung von Abfragen zu durchsuchen, klicken Sie oben auf der Seite „Bestand“ auf **Protokollanalyse**. Die Bestandsdaten werden unter dem Typ `ConfigurationData` gespeichert.

Die folgende Log Analytics-Beispielabfrage gibt die Bestandsergebnisse für den Herausgeber „Microsoft Corporation“ zurück.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Weitere Informationen zur Ausführung von Abfragen und zum Durchsuchen von Protokolldateien in Azure Monitor-Protokollen finden Sie unter [Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Anzeigen des Softwarebestands für einen einzelnen Computer

Zum Anzeigen des Softwarebestands für einen einzelnen Computer können Sie über die Seite mit den Azure-VM-Ressourcen auf die Bestandsfunktion zugreifen oder Azure Monitor-Protokolle zum Filtern nach dem entsprechenden Computer verwenden. Mit der folgenden Log Analytics-Beispielabfrage wird eine Liste mit Software für einen Computer mit dem Namen **ContosoVM** zurückgegeben.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie den Softwarebestand anzeigen:

> [!div class="checklist"]
> * Aktivieren der Lösung für Änderungsnachverfolgung und Bestand
> * Aktivieren einer Azure-VM
> * Aktivieren einer Nicht-Azure-VM
> * Anzeigen von installierter Software
> * Durchsuchen von Bestandsprotokollen nach installierter Software

Fahren Sie mit der Übersicht über das Feature für die Änderungsnachverfolgung und den Bestand fort, um mehr darüber zu erfahren.

> [!div class="nextstepaction"]
> [Übersicht über Änderungsnachverfolgung und Bestand](change-tracking/overview.md)
