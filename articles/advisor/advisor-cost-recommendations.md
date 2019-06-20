---
title: Reduzieren der Dienstkosten mithilfe von Azure Advisor | Microsoft-Dokumentation
description: Nutzen Sie Azure Advisor, um die Kosten Ihrer Azure-Bereitstellungen zu optimieren.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: fef7b82e6969de16d1815250d2373c99021b0e86
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254727"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduzieren der Dienstkosten mithilfe von Azure Advisor

Advisor hilft Ihnen beim Optimieren und Senken Ihrer Gesamtausgaben für Azure, indem Ressourcen im Leerlauf bzw. zu gering ausgelastete Ressourcen ermittelt werden. Kostenempfehlungen finden Sie auf dem Advisor-Dashboard auf der Registerkarte **Kosten**.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimieren der Kosten für virtuelle Computer durch Ändern der Größe oder Herunterfahren von zu gering ausgelasteten Instanzen 

Obwohl bestimmte Anwendungsszenarien zielgerichtet eine geringe Auslastung bewirken, können Sie häufig Kosten sparen, indem Sie Größe und Anzahl Ihrer virtuellen Computer steuern. Der Advisor überwacht 7 Tage die Verwendung Ihrer virtuellen Computer und ermittelt virtuelle Computer mit geringer Auslastung. Virtuelle Computer gelten als gering ausgelastet, wenn ihre CPU-Auslastung höchstens 5% beträgt und ihre Netzwerkauslastung unter 2% liegt oder die aktuelle Workload von einer kleineren VM-Größe bewältigt werden kann.

Der Advisor gibt die geschätzten Kosten für die weitere Ausführung des virtuellen Computers an, sodass Sie entscheiden können, ob Sie diesen herunterfahren oder seine Größe ändern.

Wenn Sie konsequenter zu gering ausgelastete virtuelle Computer ermitteln möchten, können Sie die Regel für durchschnittliche CPU-Auslastung auf Abonnementbasis anpassen.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Kostensenkung durch die Beseitigung nicht bereitgestellter ExpressRoute-Verbindungen

Der Advisor identifiziert ExpressRoute-Verbindungen, die sich für mehr als einen Monat im Anbieterstatus *Nicht bereitgestellt* befunden haben, und empfiehlt, die Verbindung zu löschen, wenn Sie nicht beabsichtigen, die Verbindung durch Ihren Dienstanbieter bereitzustellen.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Verringern der Kosten durch Löschen oder Neukonfigurieren von Gateways für virtuelle Netzwerke im Leerlauf

Der Advisor identifiziert virtuelle Netzwerkgates, die mehr als 90 Tage lang im Leerlauf waren. Da diese Gateways pro Stunde in Rechnung gestellt werden, sollten Sie sie neu konfigurieren oder löschen, wenn Sie nicht mehr beabsichtigen, diese zu verwenden. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Erwerben reservierter VM-Instanzen, um gegenüber dem nutzungsbasierten Modell Kosten einzusparen

Der Advisor überprüft Ihre VM-Nutzung der letzten 30 Tage und ermittelt, ob der Erwerb einer Azure-Reservierung für Sie günstiger wäre. Der Advisor zeigt die Regionen und Größen mit dem größten Einsparpotenzial sowie die voraussichtlichen Einsparungen an, die sich durch den Erwerb von Reservierung erzielen lassen. Mit Azure-Reservierungen können Sie die Grundkosten für Ihre virtuellen Computer vorab begleichen. Rabatte gelten automatisch für neue oder vorhandene virtuelle Computer mit der gleichen Größe und Region wie Ihre Reservierungen. Weitere Informationen zu Azure Reserved Virtual Machine Instances finden Sie [hier](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Advisor benachrichtigt Sie auch, wenn reservierte Instanzen in den nächsten 30 Tagen ablaufen, und empfiehlt Ihnen, neue reservierte Instanzen zu kaufen, damit Sie eine nutzungsbasierte Bezahlung vermeiden.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Löschen nicht zugeordneter öffentlicher IP-Adressen zum Einsparen von Kosten

Advisor ermittelt öffentliche IP-Adressen, die derzeit keinen Azure-Ressourcen wie Load Balancers oder VMs zugeordnet sind. Für diese öffentliche IP-Adressen fällt eine Schutzgebühr an. Wenn Sie nicht planen, sie zu verwenden, können Sie Kosten sparen, indem Sie sie löschen.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Löschen fehlerhafter Azure Data Factory-Pipelines

Azure Advisor erkennt Azure Data Factory-Pipelines, bei denen wiederholt Fehler auftreten, und empfiehlt, die Probleme zu beheben oder die fehlerhaften Pipelines zu löschen, falls diese nicht mehr benötigt werden. Für diese Pipelines fallen auch dann Kosten an, wenn sie sich in einem Fehlerzustand befinden und nicht genutzt werden können. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Verwenden von Standardmomentaufnahmen für verwaltete Datenträger
Es empfiehlt sich, Momentaufnahmen unabhängig vom Speichertyp des übergeordneten Datenträgers in Storage Standard zu speichern, um 60 Prozent der Kosten zu sparen. Dies ist die Standardoption für Managed Disks-Momentaufnahmen. Azure Advisor erkennt in Storage Premium gespeicherte Momentaufnahmen und empfiehlt, Ihre Momentaufnahme von Storage Premium zu Storage Standard zu migrieren. Weitere Informationen zu den Preisen für verwaltete Datenträger finden Sie [hier](https://aka.ms/aa_manageddisksnapshot_learnmore).

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Zugreifen auf Kostenempfehlungen im Azure Advisor

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klicken Sie auf dem Advisor-Dashboard auf die Registerkarte **Kosten**.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie weitere Informationen zu Empfehlungen des Advisor:
* [Einführung in Advisor](advisor-overview.md)
* [Erste Schritte](advisor-get-started.md)
* [Advisor-Empfehlungen zur Leistung](advisor-cost-recommendations.md)
* [Advisor-Empfehlungen für Hochverfügbarkeit](advisor-cost-recommendations.md)
* [Advisor-Empfehlungen zur Sicherheit](advisor-cost-recommendations.md)
