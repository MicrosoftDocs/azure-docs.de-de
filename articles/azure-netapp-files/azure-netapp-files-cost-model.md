---
title: Kostenmodell für Azure NetApp Files | Microsoft-Dokumentation
description: Erfahren Sie mehr zum Kostenmodell für Azure NetApp Files für die Verwaltung von auf den Dienst bezogenen Ausgaben.
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65524056"
---
# <a name="cost-model-for-azure-netapp-files"></a>Kostenmodell für Azure NetApp Files 

Das Verstehen des Kostenmodells für Azure NetApp Files hilft Ihnen bei der Verwaltung von auf den Dienst bezogenen Ausgaben.

## <a name="calculation-of-capacity-consumption"></a>Berechnung der Kapazitätsnutzung

Azure NetApp Files wird nach bereitgestellter Speicherkapazität in Rechnung gestellt.  Die Zuweisung bereitgestellter Kapazität erfolgt durch Anlegen von Kapazitätspools.  Kapazitätspools werden basierend auf EUR/bereitgestellte GiB/Monat in stündlichen Schritten abgerechnet. Die Mindestgröße eines einzelnen Kapazitätspools beträgt 4 TiB, und Kapazitätspools können nachträglich in 1-TiB-Schritten vergrößert werden. Innerhalb von Kapazitätspools werden Volumes erstellt.  Jedem Volume ist ein Kontingent zugeordnet, das sich entsprechend der bereitgestellten Poolkapazität verringert. Das Kontingent, das den Volumes zugeordnet werden kann, reicht von mindestens 100 GiB bis maximal 92 TiB.  

Bei einem aktiven Volume basiert die Kapazitätsnutzung im Vergleich zum Kontingent auf der logischen (effektiven) Kapazität.

Wenn die tatsächliche Kapazitätsnutzung eines Volumes sein Speicherkontingent überschreitet, kann das Volume weiter anwachsen. Schreibvorgänge sind weiterhin zulässig, solange die tatsächliche Volumegröße kleiner als der Systemgrenzwert (100 TiB) ist.  

Die gesamte genutzte Kapazität in einem Kapazitätspool im Vergleich zu seinem bereitgestellten Umfang ist die Summe von entweder dem zugewiesenen Kontingent oder der tatsächlichen Nutzung aller Volumes innerhalb des Pools, je nachdem, welcher Wert größer ist: 

   ![Berechnung der insgesamt genutzten Kapazität](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Die folgende Abbildung veranschaulicht diese Konzepte.  
* Wir haben einen Kapazitätspool mit einer bereitgestellten Kapazität von 4 TiB.  Der Pool enthält drei Volumes.  
    * Volume 1 hat ein Kontingent von 2 TiB bei einer Nutzung von 800 GiB.  
    * Volume 2 hat ein Kontingent von 1 TiB bei einer Nutzung von 100 GiB.  
    * Volume 3 hat ein Kontingent von 500 GiB bei einer Nutzung von 800 GiB (Überschreitung).  
* Der Kapazitätspool hat eine bereitgestellte Größe von 4 TiB.  
    3,8 TiB der Kapazität werden genutzt (2 TiB und 1 TiB der Kontingente der Volumes 1 und 2 und 800 GiB tatsächlicher Nutzung durch Volume 3). 200 GiB Kapazität sind ungenutzt.

   ![Kapazitätspool mit drei Volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Überschreitung der Kapazitätsnutzung  

Wenn die insgesamt genutzte Kapazität eines Pools dessen bereitgestellte Kapazität überschreitet, sind Datenschreibvorgänge weiterhin zulässig.  Wenn nach Ablauf der Karenzzeit (eine Stunde) die genutzte Kapazität des Pools seine bereitgestellte Kapazität immer noch überschreitet, wird die Poolgröße automatisch in Schritten von 1 TiB erhöht, bis die bereitgestellte Kapazität größer als die gesamte genutzte Kapazität ist.  Wenn beispielsweise in der obigen Abbildung das Volume 3 weiter wächst und die tatsächliche Nutzung 1,2 TiB erreicht, wird der Pool nach Ablauf der Karenzzeit automatisch auf 5 TiB verkleinert.  Das Ergebnis ist, dass die bereitgestellten Poolkapazität (5 TiB) die genutzte Kapazität (4,2 TiB) überschreitet.  

## <a name="manual-changes-of-the-pool-size"></a>Manuelle Änderungen der Poolgröße  

Sie können die Poolgröße manuell erhöhen oder verringern. Es gelten jedoch die folgenden Einschränkungen:
* Minimale und maximale Grenzwerte für den Dienst  
    Siehe den Artikel zu [Ressourcenlimits](azure-netapp-files-resource-limits.md).
* Ein 1-TiB-Schritt nach anfänglichem Kauf der Mindestgröße von 4 TiB
* Mindestabrechnungsschritt von einer Stunde
* Die Größe des bereitgestellten Pools darf nicht kleiner sein als die gesamte im Pool genutzte Kapazität.

## <a name="behavior-of-maximum-size-pool-overage"></a>Verhalten bei Überschreitung der maximalen Poolgröße   

Die maximale Größe eines Kapazitätspools, den Sie anlegen oder dessen Größe Sie ändern können, beträgt 500 TiB.  Wenn die gesamte genutzte Kapazität in einem Kapazitätspool 500 TiB überschreitet, treten die folgenden Sachverhalte auf:
* Datenschreibvorgänge sind weiterhin zulässig (wenn das Volume unter dem Systemmaximum von 100 TiB liegt).
* Nach Ablauf der einstündigen Karenzzeit wird die Größe des Pools automatisch in 1-TiB-Schritten angepasst, bis die bereitgestellte Kapazität des Pools die gesamte genutzte Kapazität überschreitet.
* Die zusätzlich bereitgestellte und abgerechnete Poolkapazität, die 500 TiB übersteigt, kann nicht zur Zuteilung von Volumekontingenten verwendet werden. Sie kann nicht auch verwendet werden, um QoS-Grenzwerte zu erweitern.  
    Unter [Dienstebenen](azure-netapp-files-service-levels.md) erfahren Sie mehr zu Leistungsgrenzwerten und QoS-Größenanpassung.

Das folgende Diagramm veranschaulicht diese Konzepte:
* Wir haben einen Kapazitätspool mit einem Storage Premium-Tarif und einer Kapazität von 500 TiB. Der Pool enthält neun Volumes.
    * Den Volumes 1 bis 8 ist ein Kontingent von je 60 TiB zugewiesen.  Die insgesamt genutzte Kapazität ist 480 TiB.  
        Jedes Volume hat einen QoS-Grenzwert von 3,75 GiB/s Durchsatz (60 TiB x 64 MiB/s).  
    * Volume 9 ist ein Kontingent von 20 TiB zugewiesen.  
        Volume 9 hat einen QoS-Grenzwert von 1,25 GiB/s Durchsatz (60 TiB x 64 MiB/s).
* Volume 9 stellt ein Überschreitungsszenario dar. 25 TiB werden tatsächlich genutzt.  
    * Nach Ablauf der einstündigen Karenzzeit wird die Größe des Kapazitätspools in 505 TiB geändert.  
        Das heißt: insgesamt genutzte Kapazität = 8 x 60-TiB-Kontingent für die Volumes 1 bis 8 und 25 TiB tatsächliche Nutzung für Volume 9.
    * Die abgerechnete Kapazität ist 505 TiB.
    * Das Volumekontingent für Volume 9 kann nicht erhöht werden (da das insgesamt zugeteilte Kontingent für den Pool 500 TiB nicht überschreiten darf).
    * Zusätzlicher QoS-Durchsatz kann möglicherweise nicht zugeteilt werden (da der gesamte QoS-Wert für den Pool immer noch auf 500 TiB basiert).

   ![Kapazitätspool mit neun Volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Kapazitätsnutzung von Momentaufnahmen 

Die Kapazitätsnutzung von Momentaufnahmen in Azure NetApp Files wird mit dem Kontingent des übergeordneten Volumes verrechnet.  Daher gilt der gleiche Abrechnungstarif wie für den Kapazitätspool, zu dem das Volume gehört.  Im Gegensatz zum aktiven Volume wird die Nutzung von Momentaufnahmen jedoch anhand der genutzten inkrementellen Kapazität gemessen.  Azure NetApp Files-Momentaufnahmen sind standardmäßig differenziell. Je nach Änderungsrate der Daten belegen die Momentaufnahmen oft deutlich weniger Kapazität als die logische Kapazität des aktiven Volumes. Nehmen wir zum Beispiel an, dass Sie eine Momentaufnahme eines 500-GiB-Volumes haben, das nur 10 GiB differenzieller Daten enthält. Die Kapazität, die mit dem Volumekontingent für diese Momentaufnahme verrechnet wird, beträgt 10 GiB, nicht 500 GiB. 

## <a name="next-steps"></a>Nächste Schritte

* [Seite mit den Preisen von Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
