---
title: Problembehandlung bei fehlenden Daten in Aktivitätsprotokollen | Microsoft-Dokumentation
description: Bietet Ihnen eine Lösung für fehlende Daten in Azure Active Directory-Aktivitätsprotokollen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f48c26a65314e2f23513fba155f07db3805a516
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123063"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Problembehandlung: Fehlende Daten in Azure Active Directory-Aktivitätsprotokollen 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Ich kann keine Überwachungsprotokolle für die letzten Aktionen im Azure-Portal finden.

### <a name="symptoms"></a>Symptome

Ich habe einige Aktionen im Azure-Portal ausgeführt und erwartet, die Überwachungsprotokolle für diese Aktionen auf dem Blatt `Activity logs > Audit Logs` zu finden. Ich kann sie jedoch nicht finden.

 ![Screenshot: Einträge im Überwachungsprotokoll](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Ursache

Aktionen werden nicht sofort in den Aktivitätsprotokollen angezeigt. In der folgenden Tabelle sind unsere Latenzzahlen für Aktivitätsprotokolle aufgezählt. 

| Bericht | Wartezeit (P95) | Wartezeit (P99) |
|--------|---------------|---------------|
| Verzeichnisüberwachung | 2 Min. | 5 Min. |
| Anmeldeaktivität | 2 Min. | 5 Min. |

### <a name="resolution"></a>Lösung

Warten Sie 15 Minuten bis zwei Stunden, und überprüfen Sie dann, ob die Aktionen im Protokoll angezeigt werden. Wenn die Protokolle auch nach zwei Stunden nicht angezeigt werden, [erstellen Sie ein Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), und wir nehmen uns der Sache an.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Ich kann vor Kurzem durchgeführte Benutzeranmeldungen nicht im Aktivitätsprotokoll zu den Azure Active Directory-Anmeldungen finden

### <a name="symptoms"></a>Symptome

Ich habe mich kürzlich beim Azure-Portal angemeldet und erwartet, die Anmeldeprotokolle für diese Aktionen auf dem Blatt `Activity logs > Sign-ins` zu finden. Ich kann sie jedoch nicht finden.

 ![Screenshot: Anmeldevorgänge im Aktivitätsprotokoll](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Ursache

Aktionen werden nicht sofort in den Aktivitätsprotokollen angezeigt. In der folgenden Tabelle sind unsere Latenzzahlen für Aktivitätsprotokolle aufgezählt. 

| Bericht | Wartezeit (P95) | Wartezeit (P99) |
|--------|---------------|---------------|
| Verzeichnisüberwachung | 2 Min. | 5 Min. |
| Anmeldeaktivität 2 Min. | 5 Min. |

### <a name="resolution"></a>Lösung

Warten Sie 15 Minuten bis zwei Stunden, und überprüfen Sie dann, ob die Aktionen im Protokoll angezeigt werden. Wenn die Protokolle auch nach zwei Stunden nicht angezeigt werden, [erstellen Sie ein Supportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), und wir nehmen uns der Sache an.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Ich kann für die Berichtsdaten nicht mehr als 30 Tage im Azure-Portal anzeigen.

### <a name="symptoms"></a>Symptome

Ich kann nur die Anmelde- und Überwachungsdaten der letzten 30 Tage aus dem Azure-Portal anzeigen. Warum? 

 ![Screenshot: Menu „Datum“](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Ursache

Je nach Lizenz gelten für die Speicherung von Aktivitätsberichten durch Azure Active Directory-Aktionen die folgenden Dauern:

| Bericht           | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| Verzeichnisprüfbericht  |  7 Tage       | 30 Tage             | 30 Tage             |
| Benutzeranmeldeaktivität | Nicht verfügbar. Auf dem jeweiligen Benutzerprofilblatt können Sie auf Ihre eigenen Anmeldungen der letzten 7 Tage zugreifen. | 30 Tage | 30 Tage             |

Weitere Informationen finden Sie unter [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](reference-reports-data-retention.md).  

### <a name="resolution"></a>Lösung

Sie haben zwei Möglichkeiten, um die Daten länger als 30 Tage beibehalten. Mithilfe der [Azure AD-Berichterstellungs-APIs](concept-reporting-api.md) können Sie die Daten programmgesteuert abrufen und in einer Datenbank speichern. Alternativ können Sie Überwachungsprotokolle in das SIEM-System eines Drittanbieters wie z. B. Splunk oder SumoLogic integrieren.

## <a name="next-steps"></a>Nächste Schritte

* [Vermerkdauer für Azure AD-Berichte](reference-reports-data-retention.md)
* [Latenzen bei Azure Active Directory-Berichten](reference-reports-latencies.md)
* [Häufig gestellte Fragen zu Azure Active Directory-Berichten](reports-faq.md)

