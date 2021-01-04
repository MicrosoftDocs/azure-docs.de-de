---
title: Regionsübergreifende Replikation von Azure NetApp Files-Volumes | Microsoft-Dokumentation
description: In diesem Artikel werden die Funktionsweise der regionsübergreifenden Azure NetApp Files-Replikation sowie unterstützte Regionspaare, Ziele auf Dienstebene, die Dauerhaftigkeit von Daten und das Kostenmodell erläutert.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: db1e8ce2e4fe9ae81bc2299ae70e9ff83941b5b4
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95245873"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Regionsübergreifende Replikation von Azure NetApp Files-Volumes

Die Replikationsfunktion für Azure NetApp Files bietet Datenschutz durch regionsübergreifende Volumereplikation. Sie können Daten asynchron aus einem Azure NetApp Files-Volume (Quelle) in einer Region in ein anderes Azure NetApp Files-Volume (Ziel) in einer anderen Region replizieren.  Diese Funktion ermöglicht es Ihnen, bei einem regionsweiten Ausfall oder Notfall ein Failover für Ihre kritische Anwendung durchzusetzen.

> [!IMPORTANT]
> Das Features für die regionsübergreifende Replikation befindet sich derzeit in der öffentlichen Vorschauversion. Sie müssen eine Wartelistenanforderung für den Zugriff auf das Feature über die [Seite für Wartelistenanforderungen für die regionsübergreifende Replikation von Azure NetApp Files-Volumes](https://aka.ms/anfcrrpreviewsignup) übermitteln. Warten Sie auf eine offizielle Bestätigungs-E-Mail des Azure NetApp Files-Teams, bevor Sie das Feature für die regionsübergreifende Replikation verwenden.

## <a name="supported-region-pairs"></a>Unterstützte Regionspaare

Die Azure NetApp Files-Volumereplikation ist derzeit in den folgenden festen Regionspaaren verfügbar:  

* USA, Westen und USA, Osten
* USA, Westen 2 und USA, Osten 
* USA, Süden-Mitte und USA, Mitte 
* USA, Süden-Mitte und USA, Osten
* USA, Süden-Mitte und USA, Osten 2 
* USA, Osten 2 und USA, Mitte 
* Europa, Norden und Europa, Westen
* Vereinigtes Königreich, Süden und Vereinigtes Königreich, Westen
* Australien, Osten und Australien, Südosten
* Kanada, Mitte und Kanada, Osten
* Japan, Osten und Japan, Westen
* Vereinigtes Königreich, Süden und Deutschland, Westen-Mitte
* Asien, Südosten und Australien, Osten
* „Indien, Mitte“ und „Indien, Süden“

## <a name="service-level-objectives"></a>Servicelevel-Zielpunkte (SLO)

Die Recovery Point Objective (RPO), oder der maximal akzeptable Datenverlust, wird als doppelt so groß wie der Replikationsplan definiert.  Der tatsächliche RPO-Wert hängt von Faktoren wie der Gesamtgröße des Datasets zusammen mit der Änderungsrate, dem Prozentsatz der Datenüberschreitungen und der für die Übertragung verfügbaren Replikationsbandbreite ab.   

* Für den Replikationszeitplan von zehn Minuten beträgt der maximale RPO-Wert 20 Minuten.  
* Der maximale RPO-Wert für den stündlichen Replikationszeitplan beträgt zwei Stunden.  
* Der maximale RPO-Wert für den täglichen Replikationszeitplan beträgt zwei Tage.  

Die Wiederherstellungszeit-Zielsetzung (Recovery Time Objective, RTO), oder die maximal akzeptable Downtime für Geschäftsanwendungen, wird durch Faktoren beim Aufrufen der Anwendung und der Bereitstellung des Zugriffs auf die Daten am zweiten Standort bestimmt. Der Speicherteil der RTO zum Unterbrechen der Peeringbeziehung zum Aktivieren des Zielvolumes und zum Bereitstellen von Lese- und Schreibzugriff auf Daten am zweiten Standort soll innerhalb einer Minute abgeschlossen sein.

## <a name="cost-model-for-cross-region-replication"></a>Kostenmodell für die regionsübergreifende Replikation  

Bei der regionsübergreifenden Replikation von Azure NetApp Files-Volumes zahlen Sie nur für die Menge der Daten, die Sie replizieren. Es fallen keine Einrichtungsgebühren oder die Mindestnutzungsgebühr an. Der Replikationspreis basiert auf der Replikationshäufigkeit sowie der Region des *Zielvolumes*, das Sie während der Erstkonfiguration der Replikation auswählen. Weitere Informationen finden Sie auf der Seite für die [Azure NetApp Files-Preise](https://azure.microsoft.com/pricing/details/netapp/).  

Die reguläre Gebühr für die Azure NetApp Files-Speicherkapazität gilt für das Replikationszielvolume (auch *Datenschutzvolume* genannt). 

### <a name="pricing-examples"></a>Preisbeispiele

Der in einem Monat in Rechnung gestellte Betrag für die regionsübergreifende Replikation basiert auf der Menge der Daten, die über das regionsübergreifende Replikationsfeature während dieses Monats repliziert werden. Die Menge der replizierten Daten wird in GiB gemessen. Dieser Wert stellt die Summe der Daten dar, die über zwei Regionen während allen regulären Replikationen von den Quellvolumes zu den Zielvolumes und während allen Neusynchronisierungsreplikationen von den Zielvolumes zu den Quellvolumes repliziert werden.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Beispiel 1: Baselinereplikation und inkrementelle Replikationen in Monat 1

Stellen Sie sich folgendes Szenario vor:

* Ihr *Quellvolume* stammt aus der *Premium*-Dienstebene von Azure NetApp Files. Die Größe des Volumekontingents beträgt 1000 GiB und die vom Volume genutzte Größe beträgt 500 GiB zu Beginn des ersten Tages eines Monats. Das Volume befindet sich in der Region *USA, Süden-Mitte*.
* Ihr *Zielvolume* stammt aus der *Standard*-Dienstebene von Azure NetApp Files. Es befindet sich in der Region *USA, Osten 2*.
* Sie haben eine regionsübergreifende Replikation zwischen den beiden oben genannten Volumes *auf Stundenbasis* konfiguriert. Daher beträgt der Preis der Replikation 0,12 USD pro GiB.
* Gehen Sie aus Gründen der Einfachheit davon aus, dass für das Quellvolume eine stündliche konstante Datenänderung von 0,5 GiB vorliegt, aber sich die insgesamt verbrauchte Größe des Volumes nicht erhöht (sie bleibt bei 500 GiB). 

Nach der ersten Einrichtung erfolgt die Baselinereplikation sofort.  

* Betrag der bei der Baselinereplikation replizierten Datenmenge: `500 GiB`
* Gebühren für die Baselinereplikation: `500 GiB * $0.12 = $60`

Nach der Baselinereplikation werden nur geänderte Blöcke repliziert. Aus diesem Grund werden in den nachfolgenden inkrementellen Replikationen stündlich nur 0,5 GiB an Daten repliziert.

* Summe der Datenmenge, die für einen Monat mit einer Länge von 30 Tagen über inkrementelle Replikationen repliziert wird: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Gebühren für die inkrementelle Replikation: `360 GiB * $0.12 = $43.2`

Am Ende des Monats 1 lautet die Gesamtgebühr für die regionsübergreifende Replikation wie folgt:  

*  Gesamtgebühr für die regionsübergreifende Replikation aus Monat 1: `$60 + $43.2 = $103.2`

Die reguläre Gebühr für die Azure NetApp Files-Speicherkapazität gilt für das Zielvolume. Das Zielvolume kann jedoch eine Speicherebene verwenden, die sich von der Quellvolume-Ebene unterscheidet (und günstiger ist).

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Beispiel 2: Inkrementelle Replikationen und Neusynchronisierungsreplikationen für Monat 2  

Angenommen, Sie verfügen über ein Quellvolume, ein Zielvolume und eine Replikationsbeziehung zwischen den beiden wie in Beispiel 1 beschriebenen eingerichteten Replikationen. Für 29 Tage des zweiten Monats (der ebenfalls 30 Tage lang ist) traten die stündlichen Replikationen erwartungsgemäß auf.

* Summe der Datenmenge, die für eine Länge von 29 Tagen über inkrementelle Replikationen repliziert wird: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Nehmen Sie an, dass am letzten Tag des Monats ein ungeplanter Ausfall in der Quellregion aufgetreten ist und Sie ein Failover zum Zielvolume durchgeführt haben. Nach zwei Stunden wurde die Quellregion wieder hergestellt, und Sie haben eine Neusynchronisierungsreplikation vom Zielvolume zum Quellvolume durchgeführt. Während der zwei Stunden trat beim Zielvolume eine Datenänderung von 0,8 GiB auf, und diese musste erneut mit der Quelle synchronisiert werden.

* Summe der Datenmenge, die für 22 Stunden am letzten Tag über reguläre Replikationen repliziert wurde: `0.5 GiB * 22 hours = 11 GiB`
* Betrag der bei einer Neusynchronisierungsreplikation replizierten Datenmenge: `0.8 GiB`

Aus diesem Grund lautet am Ende des Monats 2 die Gesamtgebühr für die regionsübergreifende Replikation wie folgt:  

* Gesamtgebühr für die regionsübergreifende Replikation aus Monat 2: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

Die reguläre Gebühr für die Azure NetApp Files-Speicherkapazität für Monat 2 gilt für das Zielvolume.

## <a name="next-steps"></a>Nächste Schritte
* [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md)
* [Erstellen der Volumereplikation](cross-region-replication-create-peering.md)
* [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md)
* [Verwalten der Notfallwiederherstellung](cross-region-replication-manage-disaster-recovery.md)
* [Metriken für die Volumereplikation](azure-netapp-files-metrics.md#replication)
* [Löschen von Volumereplikationen oder Volumes](cross-region-replication-delete.md)
* [Problembehandlung für die regionsübergreifende Replikation](troubleshoot-cross-region-replication.md)


