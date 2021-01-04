---
title: Konfigurieren der Überwachung für das Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)
description: Es wird beschrieben, wie Sie die Standardüberwachung für das Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau) über das Azure-Portal ändern.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: f41a43e76993a03554d32fc7f3ce3149848989a9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686500"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>Konfigurieren der Überwachung für das Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)
Mit dem Feature „Gastintegrität“ von Azure Monitor für VMs können Sie Informationen zur Integrität eines virtuellen Computers anzeigen. Dies wird anhand von verschiedenen Leistungsmessungen definiert, für die in regelmäßigen Abständen Stichproben genommen werden. In diesem Artikel wird beschrieben, wie Sie die Standardüberwachung mit dem Azure-Portal ändern können. Darüber hinaus enthält er auch eine Beschreibung der grundlegenden Konzepte von „Monitoren“ (Überwachungskomponenten), die zum [Konfigurieren der Überwachung per Datensammlungsregel](vminsights-health-configure-dcr.md) erforderlich sind.

## <a name="open-monitor-configuration"></a>Öffnen der Monitorkonfiguration
Öffnen Sie im Azure-Portal die Monitorkonfiguration, indem Sie den Monitor und dann die Registerkarte **Konfiguration** auswählen.

[![Monitordetails: Konfiguration](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Aktivieren oder Deaktivieren von Monitoren
Sowohl Einheitenmonitore als auch Aggregatmonitore verfügen über die Einstellung **Status der Integritätsüberwachung**, mit der Sie die Option aktivieren und deaktivieren können. Wenn ein Monitor aktiviert ist, wird seine Integrität angezeigt und zum Festlegen der Integrität der VM verwendet. Wenn ein Monitor deaktiviert ist, wird seine Integrität nicht berechnet und nicht verwendet, um die Integrität der VM festzulegen.

| Einstellung | BESCHREIBUNG |
|:---|:---|
| Aktiviert | Der Monitor wird unabhängig von der Einstellung des übergeordneten Elements aktiviert. |
| Disabled | Der Monitor wird unabhängig von der Einstellung des übergeordneten Elements deaktiviert. |
| Mit übergeordnetem Element identisch | Der Monitor wird in Abhängigkeit der Einstellung des übergeordneten Elements aktiviert bzw. deaktiviert. |

Wenn ein Monitor deaktiviert ist, werden alle Kriterien als nicht verfügbar angezeigt. Dies ist im Beispiel unten dargestellt.

![Deaktivierter Monitor](media/vminsights-health-configure/disabled-monitor.png)

## <a name="enable-or-disable-virtual-machine"></a>Aktivieren oder Deaktivieren eines virtuellen Computers
Sie können die Überwachung für einen virtuellen Computer deaktivieren, um alle Monitore vorübergehend anzuhalten. Ein Beispiel für die Deaktivierung der Überwachung einer VM ist die Durchführung von Wartungsarbeiten.

| Einstellung | BESCHREIBUNG |
|:---|:---|
| Aktiviert  | Der Integritätsstatus des Computers wird angezeigt. |
| Disabled | Der Integritätsstatus des Computers wird als **Deaktiviert** angezeigt. Es werden keine Warnungen erstellt. |

## <a name="health-state-logic"></a>Integritätsstatuslogik
Mit der Integritätsstatuslogik eines Einheitenmonitors werden die Kriterien zum Festlegen des Integritätsstatus definiert. Sie können angeben, wie viele Integritätsstatus ein Monitor hat und welcher Schwellenwert für die Berechnung des jeweiligen Integritätsstatus gelten soll.

![Beispiel für Integritätskriterien](media/vminsights-health-configure/sample-health-criteria.png)

Bei Aggregatmonitoren wird keine Integritätsstatuslogik angegeben. Der Integritätsstatus wird hierbei von den zugehörigen untergeordneten Einheitenmonitoren gemäß dem jeweiligen Integritätsrollup festgelegt.

Einheitenmonitore verfügen jeweils über zwei oder drei Integritätsstatus. Der Status „Fehlerfrei“ ist immer vorhanden, und optional ist zusätzlich der Status „Warnung“ oder „Kritisch“ vorhanden (oder beide). Für die Status „Warnung“ und „Kritisch“ sind jeweils eindeutige Kriterien erforderlich, mit denen definiert wird, wann der Monitor auf den jeweiligen Status festgelegt wird. Für den Status „Fehlerfrei“ sind keine Kriterien vorhanden. Er wird festgelegt, wenn die Kriterien für die anderen Status nicht erfüllt sind.

Im folgenden Beispiel wird die CPU-Auslastung auf die folgenden Integritätsstatus festgelegt:

- „Kritisch“, wenn der Wert größer als 90 % ist.
- „Warnung“, wenn der Wert größer oder gleich 80 % ist.
- „Fehlerfrei“, wenn der Wert unter 80 % liegt. Dies ist logisch, weil dies der Status ist, in dem keine der anderen Bedingungen zutrifft.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Monitoren im großen Stil mit Datensammlungsregeln](vminsights-health-configure-dcr.md)