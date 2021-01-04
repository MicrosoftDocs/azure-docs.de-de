---
title: Behandeln von Problemen mit Azure Monitor für Windows Virtual Desktop (Vorschau)
description: Hier erfahren Sie mehr über das Behandeln von Problemen mit Azure Monitor für Windows Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465693"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Behandeln von Problemen mit Azure Monitor für Windows Virtual Desktop (Vorschau)

>[!IMPORTANT]
>Azure Monitor für Windows Virtual Desktop befindet sich derzeit in der öffentlichen Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel werden bekannte Probleme und Lösungen für häufig auftretende Probleme in Azure Monitor für Windows Virtual Desktop (Vorschau) beschrieben.

## <a name="the-configuration-workbook-isnt-working-properly"></a>Die Konfigurationsarbeitsmappe funktioniert nicht ordnungsgemäß

Wenn die Azure Monitor-Konfigurationsarbeitsmappe nicht funktioniert, können Sie die jeweiligen Bereiche mithilfe der folgenden Ressourcen manuell einrichten:

- Informationen zum manuellen Aktivieren der Diagnose oder zum Zugreifen auf den Log Analytics-Arbeitsbereich finden Sie unter [Senden der Windows Virtual Desktop-Diagnose an Log Analytics](diagnostics-log-analytics.md).
- Informationen zum manuellen Installieren der Log Analytics-Erweiterung auf einem Host finden Sie unter [Log Analytics-VM-Erweiterung für Windows](../virtual-machines/extensions/oms-windows.md).
- Eine Anleitung zum Einrichten eines neuen Log Analytics-Arbeitsbereichs finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md).
- Informationen zum Hinzufügen oder Entfernen von Leistungsindikatoren finden Sie unter [Konfigurieren von Leistungsindikatoren](../azure-monitor/platform/data-sources-performance-counters.md).
- Informationen zum Konfigurieren von Ereignissen für einen Log Analytics-Arbeitsbereich finden Sie unter [Datenquellen für das Sammeln von Windows-Ereignisprotokolldaten mit dem Log Analytics-Agent](../azure-monitor/platform/data-sources-windows-events.md).

Das Problem kann auch durch mangelnde Ressourcen oder fehlende erforderliche Berechtigungen verursacht werden.

Wenn das Abonnement keine Windows Virtual Desktop-Ressourcen aufweist, wird es nicht im Parameter *Abonnement* angezeigt.

Wenn Sie keinen Lesezugriff auf die richtigen Abonnements haben, werden diese nicht im Parameter *Abonnement* angezeigt, und die zugehörigen Daten sind nicht im Dashboard enthalten. Um dieses Problem zu beheben, wenden Sie sich an den Abonnementbesitzer und bitten Sie ihn um Lesezugriff.

## <a name="my-data-isnt-displaying-properly"></a>Meine Daten werden nicht ordnungsgemäß angezeigt

Wenn Ihre Daten nicht ordnungsgemäß angezeigt werden, ist möglicherweise während des Azure Monitor-Konfigurationsprozesses ein Fehler aufgetreten. Stellen Sie zunächst sicher, dass Sie alle Felder in der Konfigurationsarbeitsmappe ausgefüllt haben, wie es unter [Verwenden von Azure Monitor für Windows Virtual Desktop zum Überwachen der Bereitstellung](azure-monitor.md) beschrieben ist. Sie können die Einstellungen für neue und vorhandene Umgebungen jederzeit ändern. Wenn Leistungsindikatoren oder Ereignisse fehlen, werden die zugehörigen Daten nicht im Azure-Portal angezeigt.

Wenn keine Informationen fehlen, die Daten aber dennoch nicht ordnungsgemäß angezeigt werden, liegt möglicherweise ein Problem in der Abfrage oder den Datenquellen vor. 

Wenn Sie keine Einrichtungsfehler entdecken und die erwarteten Daten immer noch nicht angezeigt werden, sollten Sie 15 Minuten warten und den Feed aktualisieren. Azure Monitor weist eine Latenzzeit von 15 Minuten beim Auffüllen von Protokolldaten auf. Weitere Informationen finden Sie unter [Protokolldatenerfassungszeit in Azure Monitor](../azure-monitor/platform/data-ingestion-time.md).

Wenn keine Informationen fehlen, die Daten aber immer noch nicht angezeigt werden, liegt möglicherweise ein Problem in der Abfrage oder den Datenquellen vor. Wenn das der Fall ist, müssen Sie sich möglicherweise an den Support wenden, um das Problem zu beheben.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Ich möchte Azure Monitor für Windows Virtual Desktop anpassen

Azure Monitor für Windows Virtual Desktop verwendet Azure Monitor-Arbeitsmappen. Mit Arbeitsmappen können Sie eine Kopie der Windows Virtual Desktop-Arbeitsmappenvorlage speichern und eigene Anpassungen vornehmen.

Angepasste Vorlagen werden beim Aktualisieren der ursprünglichen Vorlage durch die Produktgruppe nicht aktualisiert. Dies ist im Arbeitsmappentool so vorgesehen. Sie müssen eine Kopie der aktualisierten Vorlage speichern und Ihre Anpassungen erneut durchführen, um Aktualisierungen zu übernehmen. Weitere Informationen finden Sie unter [Problembehandlung für arbeitsmappenbasierte Erkenntnisse](../azure-monitor/insights/troubleshoot-workbooks.md) und in der [Übersicht über Arbeitsmappen](../azure-monitor/platform/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Ich kann die Daten nicht interpretieren

Weitere Informationen zu den Datenbegriffen finden Sie im [Glossar für Azure Monitor für Window Virtual Desktop](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Die benötigten Daten sind nicht verfügbar

Sie können keinen Datenpunkt finden, der bei der Diagnose eines Problems hilft? Senden Sie uns Feedback!

- Informationen zum Bereitstellen von Feedback finden Sie unter [Problembehandlung von Übersicht, Feedback und Support für Windows Virtual Desktop](troubleshoot-set-up-overview.md).
- Sie können Feedback zu Windows Virtual Desktop auch auf dem [Windows Virtual Desktop-Feedback-Hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) oder im [UserVoice-Forum](https://windowsvirtualdesktop.uservoice.com/forums/921118-general) bereitstellen.

## <a name="known-issues"></a>Bekannte Probleme

Nachfolgend sind die Probleme aufgeführt, die uns derzeit bekannt sind und an deren Behebung wir arbeiten:

- Sie können derzeit nur ein Abonnement, eine Ressourcengruppe und einen Hostpool für die Überwachung auswählen. Daher müssen Sie bei Verwendung der Seite „Benutzerberichte“ zum Nachvollziehen der Benutzererfahrung sicherstellen, dass Sie über den richtigen Hostpool verfügen, den auch der Benutzer verwendet hat. Andernfalls werden die visuellen Elemente nicht mit den entsprechenden Daten aufgefüllt.

- Es ist derzeit nicht möglich, bevorzugte Einstellungen in Azure Monitor zu speichern, es sei denn, Sie speichern eine benutzerdefinierte Vorlage der Arbeitsmappe. Das bedeutet, dass IT-Administratoren den Abonnementnamen, die Ressourcengruppennamen und die Hostpooleinstellungen bei jedem Öffnen von Azure Monitor für Windows Virtual Desktop eingeben müssen.

- Es gibt derzeit keine Möglichkeit, Daten aus Azure Monitor für Windows Virtual Desktop in Excel zu exportieren.

- Alle Azure Monitor-Warnungen mit dem Schweregrad 1 für alle Produkte im ausgewählten Abonnement werden auf der Übersichtsseite angezeigt. Dies ist beabsichtigt, da Warnungen anderer Produkte im Abonnement Windows Virtual Desktop beeinträchtigen können. Derzeit ist die Abfrage auf Warnungen mit dem Schweregrad 1 beschränkt, wobei Warnungen mit dem Schweregrad 0 und hoher Priorität von der Übersichtsseite ausgenommen sind.

- Einige Fehlermeldungen sind nicht benutzerfreundlich formuliert, und nicht alle Fehlermeldungen sind in der Dokumentation beschrieben.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie nicht sicher sind, wie die Daten zu interpretieren sind, oder weitere Informationen zu häufig verwendeten Begriffen benötigen, sehen Sie sich das [Glossar für Azure Monitor für Windows Virtual Desktop](azure-monitor-glossary.md) an. Informationen zum Einrichten und Verwenden von Azure Monitor für Window Virtual Desktop finden Sie unter [Verwenden von Azure Monitor für Windows Virtual Desktop zum Überwachen der Bereitstellung](azure-monitor.md).