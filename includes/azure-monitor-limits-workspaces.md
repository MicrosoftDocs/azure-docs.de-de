---
title: include file
description: include file
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 86c5c6fff06f43bf66427ba1935852fcf97a71c6
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356209"
---
**Umfang und Aufbewahrung der Datensammlung** 

| Tarif | Grenzwert pro Tag | Beibehaltung von Daten | Comment |
|:---|:---|:---|:---|
| Current Per GB-Tarif<br>(Einführung: April 2018) | Keine Begrenzung | 30–730 Tage | Für eine Datenaufbewahrung von mehr als 31 Tagen fallen zusätzliche Gebühren an. Erfahren Sie mehr über die Preise für Azure Monitor. |
| Legacy Free-Tarife<br>(Einführung: April 2016) | 500 MB | 7 Tage | Wenn Ihr Arbeitsbereich das Tageslimit von 500 MB erreicht, wird die Datenerfassung beendet und am nächsten Morgen fortgesetzt. Ein Tag wird in UTC-Zeit ausgegeben. Beachten Sie, dass Daten, die von Azure Security Center erfasst werden, nicht im Tageslimit von 500 MB enthalten sind und auch noch darüber hinaus weiter erfasst werden.  |
| Legacy Standalone Per GB-Tarif<br>(Einführung: April 2016) | Keine Begrenzung | 30 bis 730 Tage | Für eine Datenaufbewahrung von mehr als 31 Tagen fallen zusätzliche Gebühren an. Erfahren Sie mehr über die Preise für Azure Monitor. |
| Legacy Per Node (OMS)<br>(Einführung: April 2016) | Keine Begrenzung | 30 bis 730 Tage | Für eine Datenaufbewahrung von mehr als 31 Tagen fallen zusätzliche Gebühren an. Erfahren Sie mehr über die Preise für Azure Monitor. |
| Legacy Standard-Tarif | Keine Begrenzung | 30 Tage  | Die Aufbewahrungsdauer kann nicht angepasst werden |
| Legacy Premium-Tarif | Keine Begrenzung | 365 Tage  | Die Aufbewahrungsdauer kann nicht angepasst werden |

**Anzahl von Arbeitsbereichen pro Abonnement**

| Tarif    | Arbeitsbereichsbegrenzung | Kommentare
|:---|:---|:---|
| Free-Tarif  | 10 | Dieser Grenzwert kann nicht erhöht werden. |
| Alle anderen Tarife | Keine Begrenzung | Sie sind durch die Anzahl der Ressourcen innerhalb einer Ressourcengruppe und die Anzahl der Ressourcengruppen pro Abonnement eingeschränkt. |

**Azure portal**

| Category | Begrenzung | Kommentare |
|:---|:---|:---|
| Maximale Anzahl von Datensätzen, die von einer Protokollabfrage zurückgegebenen werden | 10.000 | Reduziert die Ergebnisse durch die Verwendung eines Abfragebereichs, eines Zeitbereichs und von Filtern in der Abfrage. |


**Datensammler-API**

| Category | Begrenzung | Kommentare |
|:---|:---|:---|
| Maximale Größe für einen einzelnen Beitrag | 30 MB | Teilen Sie größere Volumen auf mehrere Beiträge auf. |
| Maximale Größe für Feldwerte  | 32 KB | Felder mit einer Länge von mehr als 32 KB werden abgeschnitten. |

**Search-API**

| Category | Begrenzung | Kommentare |
|:---|:---|:---|
| Maximale Anzahl von Datensätzen, die bei einer einzelnen Abfrage zurückgegeben werden | 500.000 | |
| Maximale Größe der zurückgegebenen Daten | 64.000.000 Byte (~61 MiB)| |
| Maximale Ausführungszeit der Abfrage | 10 Minuten | Weitere Informationen finden Sie unter [Timeouts (Zeitlimit)](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts).  |
| Maximale Anforderungsrate | 200 Anforderungen pro 30 Sekunden und Azure AD-Benutzer oder Client-IP-Adresse | Weitere Informationen finden Sie unter [Rate limits (Ratenlimits)](https://dev.loganalytics.io/documentation/Using-the-API/Limits). |

**Allgemeine Grenzwerte für Arbeitsbereiche**

| Category | Begrenzung | Kommentare |
|:---|:---|:---|
| Maximale Anzahl von Spalten in einer Tabelle         | 500 | |
| Maximale Anzahl an Zeichen für einen Spaltennamen | 500 | |
| Datenexport | Derzeit nicht verfügbar | Verwenden Sie Azure Functions oder Logic Apps, um Daten zu aggregieren und zu exportieren. | 

**<a name="data-ingestion-volume-rate">Rate für Datenerfassungsvolumen</a>**

Azure Monitor ist ein Hochleistungs-Datendienst, der Tausende Kunden bedient, die mit zunehmender Tendenz jeden Monat Terabytes von Daten senden. Mit der Volumenratenbegrenzung sollen Azure Monitor-Kunden vor plötzlichen Erfassungsspitzen in einer mehrinstanzenfähigen Umgebung isoliert werden. Ein Standardschwellenwert für die Erfassungsvolumenrate von 500 MB (komprimiert) ist in Arbeitsbereichen definiert. Dies entspricht ungefähr **6 GB/Minute** für nicht komprimierte Daten. Die tatsächliche Größe kann je nach Protokolllänge und Komprimierungsverhältnis zwischen Datentypen variieren. Das Ratenlimit für Volumen gilt für Daten, die über [Diagnoseeinstellungen](../articles/azure-monitor/platform/diagnostic-settings.md) aus Azure-Ressourcen erfasst werden. Wenn das Ratenlimit für Volumen erreicht ist, versucht ein Wiederholungsmechanismus, die Daten viermal in einem Zeitraum von 30 Minuten zu erfassen und wird beendet, wenn der Vorgang fehlschlägt. Dies gilt nicht für Daten, die von [Agents](../articles/azure-monitor/platform/agents-overview.md) oder der [Datensammler-API](../articles/azure-monitor/platform/data-collector-api.md) erfasst wurden.

Wenn Daten an einen Arbeitsbereich mit einer Volumenrate gesendet werden, die mehr als 80 Prozent des im Arbeitsbereich konfigurierten Schwellenwerts beträgt, wird alle sechs Stunden ein Ereignis an die Tabelle *Vorgang* im Arbeitsbereich gesendet, während der Schwellenwert weiterhin überschritten wird. Wenn die erfasste Volumenrate höher ist als der Schwellenwert, werden einige Daten gelöscht, und es wird alle sechs Stunden ein Ereignis an die Tabelle *Vorgang* im Arbeitsbereich gesendet, während der Schwellenwert weiterhin überschritten wird. Wenn die Erfassungsvolumenrate weiterhin den Schwellenwert überschreitet oder Sie ihn wahrscheinlich in Kürze erreichen werden, können Sie eine Erhöhung anfordern, indem Sie eine Supportanfrage öffnen. 

Unter [Überwachen der Integrität von Log Analytics-Arbeitsbereichen in Azure Monitor](../articles/azure-monitor/platform/monitor-workspace.md) erfahren Sie mehr über das Erstellen von Regeln, um proaktiv benachrichtigt zu werden, wenn Sie Erfassungsgrenzwerte erreichen.

>[!NOTE]
>Abhängig von Ihrer Log Analytics-Nutzungsdauer haben Sie ggf. Zugang zu Legacytarifen. Weitere Informationen zu Legacytarifen von Log Analytics finden Sie [hier](../articles/azure-monitor/platform/manage-cost-storage.md#legacy-pricing-tiers).