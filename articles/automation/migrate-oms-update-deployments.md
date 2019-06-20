---
title: Migrieren von OMS-Updatebereitstellungen zu Azure
description: In diesem Artikel wird beschrieben, wie Sie Ihre vorhandenen OMS-Updatebereitstellungen zu Azure migrieren.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4d11dfcb66a545cbecc80b6bdad558ca6d328ed2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499210"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrieren von OMS-Updatebereitstellungen zu Azure

Das Portal der Operations Management Suite (OMS) wird gerade [eingestellt](../azure-monitor/platform/oms-portal-transition.md). Alle Funktionen des OMS-Portals für die Updateverwaltung sind ab jetzt im Azure-Portal verfügbar. Dieser Artikel enthält alles Wissenswerte zur Migration zum Azure-Portal.

## <a name="key-information"></a>Wichtige Informationen

* Vorhandene Bereitstellungen sind weiterhin funktionsfähig. Nachdem Sie die Bereitstellung in Azure neu erstellt haben, können Sie die alte Bereitstellung in der OMS löschen.
* Alle vorhandenen Features aus der OMS sind in Azure verfügbar. Weitere Informationen zur Updateverwaltung finden Sie unter [Übersicht über die Updateverwaltung](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Zugriff auf fas Azure-Portal

Klicken Sie in Ihrem OMS-Arbeitsbereich auf **In Azure öffnen**. So gelangen Sie zum Log Analytics-Arbeitsbereich, den die OMS verwendet hat.

![In Azure öffnen – OMS-Portal](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Klicken Sie im Azure-Portal auf **Automation-Konto**.

![Azure Monitor-Protokolle](media/migrate-oms-update-deployments/log-analytics.png)

Klicken Sie in Ihrem Automation-Konto auf **Updateverwaltung**, um „Updateverwaltung“ zu öffnen.

![Updateverwaltung](media/migrate-oms-update-deployments/azure-automation.png)

Von nun an können Sie im Azure-Portal direkt zu **Alle Dienste** navigieren, unter **Verwaltungsprogramme** auf **Automation-Konten** klicken, das entsprechende Automation-Konto auswählen und auf **Updateverwaltung** klicken.

## <a name="recreate-existing-deployments"></a>Neuerstellen vorhandener Bereitstellungen

Alle im OMS-Portal erstellten Updatebereitstellungen verfügen über eine [gespeicherte Suche](../azure-monitor/platform/computer-groups.md), auch Computergruppe genannt. Diese weist denselben Namen wie die vorhandene Updatebereitstellung auf. Die gespeicherte Suche enthält die Liste der Computer, für die die Updatebereitstellung geplant wurde.

![Updateverwaltung](media/migrate-oms-update-deployments/oms-deployment.png)

Wenn Sie diese vorhandene gespeicherte Suche verwenden möchten, gehen Sie folgendermaßen vor:

Wechseln Sie zum Erstellen einer neuen Updatebereitstellung zum Azure-Portal, wählen Sie das verwendete Automation-Konto aus, und klicken Sie auf **Updateverwaltung**. Klicken Sie auf **Updatebereitstellung planen**.

![Updatebereitstellung planen](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Der Bereich **Neue Updatebereitstellung** wird geöffnet. Geben Sie Werte für die Eigenschaften ein, die in der folgenden Tabelle beschrieben werden, und klicken Sie auf **Erstellen**:

Wählen Sie für zu aktualisierende Computer die gespeicherte Suche aus, die von der vorhandenen OMS-Bereitstellung verwendet wird.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
|NAME |Eindeutiger Name zum Identifizieren der Updatebereitstellung |
|Betriebssystem| Wählen Sie zwischen **Linux** und **Windows** aus.|
|Zu aktualisierende Computer |Wählen Sie eine gespeicherte Suche oder eine importierte Gruppe aus, oder wählen Sie im Dropdownmenü „Computer“ und dann einzelne Computer aus. Bei Auswahl von **Computer** wird die Bereitschaft des Computers in der Spalte **BEREITSCHAFT DES UPDATE-AGENTS** angezeigt.</br> Weitere Informationen zu den verschiedenen Methoden zum Erstellen von Computergruppen in Azure Monitor-Protokollen finden Sie unter [Computergruppen in Azure Monitor-Protokollen](../azure-monitor/platform/computer-groups.md). |
|Updateklassifizierungen|Wählen Sie alle benötigten Updateklassifizierungen aus. CentOS unterstützt dies nicht standardmäßig.|
|Auszuschließende Updates|Geben Sie die auszuschließenden Updates ein. Geben Sie für Windows den KB-Artikel ohne das Präfix **KB** ein. Geben Sie für Linux den Paketnamen ein, oder verwenden Sie ein Platzhalterzeichen.  |
|Zeitplaneinstellungen|Wählen Sie den Startzeitpunkt aus, und wählen Sie dann unter „Wiederholung“ **Einmal** oder **Serie** aus. | 
| Wartungsfenster |Festgelegte Minutenanzahl für Updates Der Wert darf nicht kleiner als 30 Minuten oder größer als 6 Stunden sein. |
| Neustartsteuerung| Legt fest, wie Neustarts behandelt werden sollen.</br>Die verfügbaren Optionen lauten wie folgt:</br>Neu starten bei Bedarf (Standard)</br>Immer neu starten</br>Nie neu starten</br>Nur neu starten – Updates werden nicht installiert|

Klicken Sie auf **Geplante Updatebereitstellungen**, um den Status der neu erstellten Updatebereitstellung anzuzeigen.

![Neue Updatebereitstellung](media/migrate-oms-update-deployments/new-update-deployment.png)

Wenn Sie die neuen Bereitstellungen über das Azure-Portal konfiguriert haben, können die vorhandenen Bereitstellungen, wie bereits erwähnt, aus dem OMS-Portal entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Updateverwaltung in Azure finden Sie unter [Lösung für die Updateverwaltung in Azure](automation-update-management.md).
