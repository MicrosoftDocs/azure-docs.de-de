---
title: Grundlegendes zur Anwendung des Rabatts für Reservierungen auf Azure Data Explorer
description: Hier erfahren Sie, wie der Rabatt für Reservierungen auf die Verbrauchseinheit vom Typ Azure Data Explorer-Aufschlag angewendet wird.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: b222e4ff519fbe2fbb4fa58b39555ac9fead0917
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352914"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Grundlegendes zur Anwendung des Rabatts für Reservierungen auf Azure Data Explorer

Nachdem Sie eine reservierte Azure Data Explorer-Kapazität erworben haben, wird der Reservierungsrabatt automatisch auf Azure Data Explorer-Ressourcen angewendet, die den Attributen und der Menge der Reservierung entsprechen. Eine Reservierung beinhaltet die Azure Data Explorer-Aufschlagsgebühren. Compute-, Netzwerk- und Speicherressourcen sowie andere Azure-Ressourcen für den Betrieb des Azure Data Explorer-Clusters sind dagegen nicht enthalten. Reservierungen für diese Ressourcen müssen separat erworben werden.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Nicht in Anspruch genommener Reservierungsrabatt *geht verloren*. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Wenn keine übereinstimmenden Ressourcen im angegebenen Reservierungsumfang gefunden werden, gehen die reservierten Stunden *verloren*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Auf Azure Data Explorer-Cluster angewendeter Rabatt für Reservierungen

Reservierungsrabatte werden auf Stundenbasis auf den Azure Data Explorer-Aufschlagsverbrauch angewendet. Bei Azure Data Explorer-Ressourcen, die nicht über die gesamte Stunde hinweg ausgeführt werden, wird der Reservierungsrabatt automatisch auf andere Azure Data Explorer-Ressourcen angewendet, die den Reservierungsattributen entsprechen. Der Rabatt kann auf gleichzeitig ausgeführte Azure Data Explorer-Ressourcen angewendet werden. Wenn Sie über keine Azure Data Explorer-Ressourcen verfügen, die über die gesamte Stunde hinweg ausgeführt werden und den Reservierungsattributen entsprechen, profitieren Sie nicht in vollem Umfang vom Reservierungsrabatt für diese Stunde.

> [!NOTE]
> * Es wird **dringend empfohlen**, [Reservierungskapazität](../../virtual-machines/prepay-reserved-vm-instances.md) für die virtuellen Computer zu erwerben, die für den Azure Data Explorer-Cluster verwendet werden, um die Einsparungen durch reservierte Kapazität zu maximieren.
> * Bei dem Reservierungskauf werden Rabatte auf alle Regionen angewendet.

## <a name="examples"></a>Beispiele

Die folgenden Beispiele veranschaulichen, wie der Rabatt für reservierte Azure Data Explorer-Kapazität angewendet wird – abhängig davon, wie viele Aufschlagseinheiten Sie erworben haben und wann sie ausgeführt werden.
Bei einem Engine-Cluster mit **zwei virtuellen Computern vom Typ „D11_v2“** werden Ihnen beispielsweise pro Stunde On-Demand-Gebühren für vier Einheiten der Verbrauchseinheit vom Typ Azure Data Explorer-Aufschlag in Rechnung gestellt.

**Szenario 1**

Sie erwerben reservierte Azure Data Explorer-Kapazität für acht Azure Data Explorer-Aufschlagseinheiten. Sie führen einen Engine-Cluster mit zwei virtuellen Computern vom Typ „D13_v2“ mit insgesamt 16 Kernen aus, für den pro Stunde 16 Azure Data Explorer-Aufschlagseinheiten abgerechnet werden und der den restlichen Attributen der Reservierung entspricht. Acht Kerne der Azure Data Explorer-Computenutzung werden Ihnen zum Preis für die nutzungsbasierte Bezahlung in Rechnung gestellt, und Sie erhalten den Reservierungsrabatt für eine Stunde der Nutzung von acht Kernen der Azure Data Explorer-Aufschlagseinheit.

Bei den übrigen Beispielen wird davon ausgegangen, dass die reservierte Azure Data Explorer-Kapazität, die Sie erwerben, für einen Azure Data Explorer-Cluster mit 16 Kernen gilt und die restlichen Reservierungsattribute dem ausgeführten Azure Data Explorer-Cluster entsprechen.

**Szenario 2**

Sie führen eine Stunde lang zwei Azure Data Explorer-Engine-Cluster mit acht Kernen in zwei verschiedenen Regionen aus. Der Reservierungsrabatt für 16 Kerne wird auf beide Cluster und die 16 verbrauchten Azure Data Explorer-Aufschlagseinheiten angewendet.

**Szenario 3**

Sie führen einen Azure Data Explorer-Engine-Cluster mit 16 Kernen von 13 Uhr bis 13:30 Uhr aus. Sie führen einen weiteren Azure Data Explorer-Engine-Cluster mit 16 Kernen von 13:30 Uhr bis 14 Uhr aus. Beide Instanzen sind durch den Reservierungsrabatt abgedeckt.

**Szenario 4**

Sie führen einen Azure Data Explorer-Engine-Cluster mit 16 Kernen von 13 Uhr bis 13:45 Uhr aus. Sie führen einen weiteren Azure Data Explorer-Engine-Cluster mit 16 Kernen von 13:30 Uhr bis 14 Uhr aus. Die 15-minütige Überschneidung wird zum Preis der nutzungsbasierten Bezahlung abgerechnet. Für den restlichen Zeitraum wird der Reservierungsrabatt auf die Azure Data Explorer-Aufschlagsnutzung angewendet.

Die Anwendung Ihrer Azure-Reservierungen in Abrechnungsnutzungsberichten wird unter [Abrufen von Reservierungskosten und Nutzung laut Enterprise Agreement](understand-reserved-instance-usage-ea.md) näher erläutert und veranschaulicht.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

* [Vorauszahlen für Azure Data Explorer-Aufschlagseinheiten mit reservierter Azure Data Explorer-Kapazität](/azure/data-explorer/pricing-reserved-capacity)  
* [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)  
* [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)  
* [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)
* [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
* [Grundlegendes zur Verwendung von Azure-Reservierungen für CSP-Abonnements](/partner-center/azure-reservations)