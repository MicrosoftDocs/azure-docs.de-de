---
title: 'Gewusst wie: Konfigurieren der Überwachung in Azure Digital Twins | Microsoft-Dokumentation'
description: 'Vorgehensweise: Konfigurieren der Überwachung in Azure Digital Twins.'
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 3c35633c9b25eafdb738ed591c7c7022fbd8149a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967821"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Gewusst wie: Konfigurieren der Überwachung in Azure Digital Twins

Azure Digital Twins bietet eine zuverlässige Protokollierung, Überwachung und Analyse. Lösungsentwickler können mit Azure Monitor-Protokollen, Diagnoseprotokollen, der Aktivitätsprotokollierung und anderen Diensten den komplexen Überwachungsanforderungen einer IoT-App gerecht werden. Protokollierungsoptionen können kombiniert werden, um Datensätze aus mehreren Diensten abzufragen oder anzuzeigen und eine maßgeschneiderte Protokollierung für eine Vielzahl von Diensten zur Verfügung zu stellen.

In diesem Artikel werden die Protokollierungs- und Überwachungsoptionen sowie die Option beschrieben, diese auf für Azure Digital Twins spezifische Weise zu kombinieren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Überprüfen von Aktivitätsprotokollen

Azure-[Aktivitätsprotokolle](../azure-monitor/platform/activity-logs-overview.md) bieten für jede Azure-Dienstinstanz einen kompakten Überblick über Ereignis- und Vorgangsverläufe auf Abonnementebene.

Zu Ereignissen auf Abonnementebene zählen Folgende:

* Ressourcenerstellung
* Ressourcenentfernung
* Erstellen von App-Geheimnissen
* Integration in andere Dienste

Die Aktivitätsprotokollierung für Azure Digital Twins ist standardmäßig aktiviert und kann durch folgende Schritte über das Azure-Portal aufgerufen werden:

1. Wählen Sie Ihre Azure Digital Twins-Instanz aus.
1. Klicken Sie auf **Aktivitätsprotokoll**, um den Anzeigebereich zu öffnen:

    ![Aktivitätsprotokoll][1]

Für die erweiterte Aktivitätsprotokollierung:

1. Klicken Sie auf die Option **Protokolle**, um die **Übersicht über die Log Analytics-Aktivität** anzuzeigen:

    ![Auswahl][2]

1. In der **Übersicht über die Log Analytics-Aktivität** werden wichtige Aktivitätsprotokolldaten zusammengefasst:

    ![Übersicht über die Log Analytics-Aktivität][3]

>[!TIP]
>Gewinnen Sie anhand von **Aktivitätsprotokollen** einen schnellen Einblick in Ereignisse auf Abonnementebene.

## <a name="enable-customer-diagnostic-logs"></a>Aktivieren von Kundendiagnoseprotokollen

Für jede Azure-Instanz können Azure-[Diagnoseeinstellungen](../azure-monitor/platform/diagnostic-logs-overview.md) als Ergänzung zur Aktivitätsprotokollierung festgelegt werden. Aktivitätsprotokolle beziehen sich auf Ereignisse auf Abonnementebene, während die Diagnoseprotokollierung Einblicke in den Verwendungsverlauf der Ressourcen selbst bietet.

Die Diagnoseprotokollierung umfasst z.B. folgende Angaben:

* Die Ausführungszeit für eine benutzerdefinierte Funktion
* Den Antwortstatuscode einer erfolgreichen API-Anforderung
* Abrufen eines App-Schlüssels von einem Tresor

Führen Sie folgende Schritte aus, um Diagnoseprotokolle für eine Instanz zu aktivieren:

1. Öffnen Sie die Ressource im Azure-Portal.
1. Klicken Sie auf **Diagnoseeinstellungen**.

    ![Diagnoseeinstellungen 1][4]

1. Klicken Sie auf **Diagnose aktivieren**, um Daten zu sammeln (sofern noch nicht aktiviert).
1. Füllen Sie die angeforderten Felder aus, und wählen Sie aus, wie und wo Daten gespeichert werden:

    ![Diagnoseeinstellungen 2][5]

    Diagnoseprotokolle werden häufig mit [Azure File Storage](../storage/files/storage-files-deployment-guide.md) gespeichert und für [Azure Monitor-Protokolle](../azure-monitor/log-query/get-started-portal.md) freigegeben. Beide Optionen können ausgewählt werden.

>[!TIP]
>Verwenden Sie **Diagnoseprotokolle**, um Einblicke in Ressourcenvorgänge zu gewinnen.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor und Log Analytics

IoT-Anwendungen führen verschiedenartige Ressourcen, Geräte, Standorte und Daten zusammen. Eine detaillierte Protokollierung enthält ausführliche Informationen zu den einzelnen Bereichen, Diensten oder Komponenten der gesamten Anwendungsarchitektur, wobei für Wartungs- und Debugzwecke jedoch oftmals ein einheitlicher Überblick erforderlich ist.

Azure Monitor umfasst den leistungsstarken Log Analytics-Dienst, der die Protokollierung von Quellen für die Darstellung und Analyse über eine zentrale Plattform ermöglicht. Aus diesem Grund empfiehlt sich Azure Monitor besonders für die Analyse von Protokollen in anspruchsvollen IoT-Apps.

Beispiele hierfür sind Folgende:

* Abfragen von mehreren Diagnoseprotokollverläufen
* Anzeigen von Protokollen für mehrere benutzerdefinierte Funktionen
* Anzeigen von Protokollen für mindestens zwei Dienste innerhalb eines bestimmten Zeitraums

Eine vollständige Protokollabfrage wird über [Azure Monitor-Protokolle](../azure-monitor/log-query/log-query-overview.md) bereitgestellt. Um diese leistungsstarken Features einzurichten, gehen Sie wie folgt vor:

1. Suchen Sie im Azure-Portal nach **Log Analytics**.
1. Ihre verfügbaren **Log Analytics-Arbeitsbereich**-Instanzen werden angezeigt. Wählen Sie eine aus, und klicken Sie zur Abfrage auf **Protokolle**:

    ![Log Analytics][6]

1. Wenn Sie noch keine **Log Analytics-Arbeitsbereich**-Instanz besitzen, können Sie einen Arbeitsbereich erstellen, indem Sie auf die Schaltfläche **Hinzufügen** klicken:

    ![Erstellen eines OMS-Arbeitsbereichs][7]

Sobald Ihre **Log Analytics-Arbeitsbereich**-Instanz bereitgestellt ist, können Sie leistungsstarke Abfragen erstellen, um nach Einträgen in verschiedenen Protokollen zu suchen oder Suchen mittels spezifischer Kriterien über die **Protokollverwaltung** durchzuführen:

   ![Protokollverwaltung][8]

Weitere Informationen zu leistungsstarken Abfragevorgängen finden Sie unter [Erste Schritte mit Abfragen in Log Analytics](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Wenn Sie zum ersten Mal Ereignisse an den **Log Analytics-Arbeitsbereich** senden, tritt unter Umständen eine Verzögerung von 5 Minuten auf.

Azure Monitor-Protokolle bieten außerdem leistungsstarke Benachrichtigungsdienste für Fehler und Warnungen, die durch Klicken auf **Diagnose und Problembehandlung** angezeigt werden können:

   ![Benachrichtigungen für Warnungen und Fehler][9]

>[!TIP]
>Mit dem **Log Analytics-Arbeitsbereich** können Sie Protokollverläufe für verschiedene App-Funktionen, Abonnements und Dienste abfragen.

## <a name="other-options"></a>Weitere Optionen

Azure Digital Twins unterstützt zudem eine anwendungsspezifische Protokollierung und Sicherheitsüberwachung. Einen umfassenden Überblick über alle Azure-Protokollierungsoptionen, die für Ihre Azure Digital Twins-Instanz verfügbar sind, finden Sie im Artikel [Protokollierung und Überwachung in Azure](../security/azure-log-audit.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure-Aktivitätsprotokolle](../azure-monitor/platform/activity-logs-overview.md).

- Setzen Sie sich ausführlicher mit Azure-Diagnoseeinstellungen auseinander (siehe [Sammeln und Verwenden von Protokolldaten von Ihren Azure-Ressourcen](../azure-monitor/platform/diagnostic-logs-overview.md)).

- Weitere Informationen finden Sie unter [Azure Monitor-Protokolle](../azure-monitor/log-query/get-started-portal.md).

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png
