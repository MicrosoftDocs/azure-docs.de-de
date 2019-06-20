---
title: Leistungsbenchmarks für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt die Ergebnisse von Leistungsbenchmarktests für Azure NetApp Files auf Volumenebene.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871223"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Leistungsbenchmarks für Azure NetApp Files

In diesem Artikel werden die Ergebnisse von Leistungsbenchmarktests für Azure NetApp Files auf Volumenebene beschrieben. 

## <a name="sample-application-used-for-the-tests"></a>Beispielanwendung für die Tests

Die Leistungstests wurden mit einer Beispielanwendung unter Verwendung von Azure NetApp Files durchgeführt. Die Anwendung weist die folgenden Merkmale auf: 

* Eine für die Cloud entwickelte Linux-basierte Anwendung
* Kann mit zusätzlichen virtuellen Computern (VMs) linear skaliert werden, um die Computeleistung bei Bedarf zu erhöhen
* Erfordert schnellen Zugriff auf den Data Lake
* Weist E/A-Muster auf, die manchmal zufällig und manchmal sequenziell sind 
    * Ein zufälliges Muster erfordert eine geringe Wartezeit für große E/A-Mengen 
    * Ein sequenzielles Muster erfordert eine große Bandbreite 

## <a name="about-the-workload-generator"></a>Informationen zum Workload-Generator

Die Ergebnisse stammen aus Vdbench-Übersichtsdateien. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) ist ein Befehlszeilenprogramm, das Datenträger-E/A-Workloads zur Überprüfung der Speicherleistung erzeugt. Die verwendete Client-Server-Konfiguration ist skalierbar.  Sie umfasst einen einzelnen gemischten Master/Client und 14 dedizierte Client-VMs.

## <a name="about-the-tests"></a>Informationen zu den Tests

Die Tests wurden entwickelt, um die Grenzwerte, die die Beispielanwendung möglicherweise aufweist, und die Reaktionszeit zu ermitteln, die sich bis zu den Grenzwerten entwickelt.  

Die folgenden Tests wurden durchgeführt: 

* 100 % 8 KiB zufälliger Lesevorgang
* 100 % 8 KiB zufälliger Schreibvorgang
* 100 % 64 KiB sequenzieller Lesevorgang
* 100 % 64 KiB sequenzieller Schreibvorgang
* 50 % 64 KiB sequenzieller Lesevorgang, 50 % 64 KiB sequenzieller Schreibvorgang
* 50 % 8 KiB zufälliger Lesevorgang, 50 % 8 KiB zufälliger Schreibvorgang

## <a name="bandwidth"></a>Bandbreite

Azure NetApp Files bietet mehrere [Dienstebenen](azure-netapp-files-service-levels.md). Jede Dienstebene bietet eine andere Bandbreite pro TiB bereitgestellter Kapazität (Volumekontingent). Das Bandbreitenlimit für ein Volume wird basierend auf der Kombination aus Dienstebene und Volumekontingent bereitgestellt. Beachten Sie, dass das Bandbreitenlimit nur ein Faktor bei der Bestimmung des tatsächlichen Durchsatzes ist, der erreicht wird.  

Derzeit sind 4.500 MiB der höchste Durchsatz, der von einer Workload mit nur einem Volume im Test erreicht wurde.  Mit der Dienstebene „Premium“ bietet ein Volumenkontingent von 70,31 TiB ausreichend Bandbreite, um diesen Durchsatz gemäß der folgenden Berechnung zu realisieren: 

![Bandbreitenformel](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kontingent und Dienstebene](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Durchsatzintensive Workloads

Der Durchsatztest erfolgte mit Vdbench und einer Kombination aus 12 x D32s V3 Speicher-VMs. Das Beispielvolume im Test erreichte folgende Durchsatzwerte:

![Durchsatztest](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>E/A-intensive Workloads

Der E/A-Test erfolgte mit Vdbench und einer Kombination aus 12 x D32s V3 Speicher-VMs. Das Beispielvolume im Test erreichte die folgenden E/A-Werte:

![E/A-Test](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latency

Die Entfernung zwischen den Test-VMs und dem Azure NetApp Files-Volume hat Auswirkungen auf die E/A-Leistung.  Das folgende Diagramm vergleicht die IOPS- und Wartezeitantwortkurven für zwei verschiedene VM-Gruppen.  Ein VM-Gruppe befindet sich in der Nähe von Azure NetApp Files und die andere Gruppe ist weiter entfernt.  Beachten Sie, dass sich die erhöhte Wartezeit für die weiter entfernte VM-Gruppe auf die IOPS-Menge auswirkt, die bei einem bestimmten Parallelitätsgrad erreicht wird.  Unabhängig davon können die Messwerte für ein Volume 300.000 IOPS überschreiten, wie unten dargestellt: 

![Studie zur Wartezeit](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Zusammenfassung

Wartezeitsensitive Workloads (Datenbanken) können eine Antwortzeit von einer Millisekunde aufweisen. Die Transaktionsleistung kann über 300.000 IOPS für ein einzelnes Volume betragen.

Durchsatzsensitive Anwendungen (für Streaming und Bildverarbeitung) können einen Durchsatz von 4,5 GiB/s aufweisen.
