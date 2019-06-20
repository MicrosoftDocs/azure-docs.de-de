---
title: Failback während der Notfallwiederherstellung mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über verschiedene Arten von Failbacks sowie Vorsichtsmaßnahmen, die beim Durchführen von Failbacks zum lokalen Standort während der Notfallwiederherstellung mit dem Azure Site Recovery-Dienst zu berücksichtigen sind.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: raynew
ms.openlocfilehash: 1e5dc91018df822c72381e4a162c5af5d74ed83c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399473"
---
# <a name="failback-after-disaster-recovery-of-vmware-vms"></a>Failback nach Notfallwiederherstellung von virtuellen VMware-Computern

Nach einem Failover zu Azure im Rahmen des Notfallwiederherstellungsprozesses können Sie ein Failback zu Ihrem lokalen Standort durchführen. Bei Azure Site Recovery sind zwei verschiedene Arten von Failback möglich: 

- Failback zum ursprünglichen Speicherort 
- Failback zu einem alternativen Speicherort

Wenn Sie ein Failover für einen virtuellen VMware-Computer durchgeführt haben, können Sie das Failback zum gleichen virtuellen lokalen Quellcomputer ausführen, sofern er lokal noch vorhanden ist. In diesem Szenario werden nur die Änderungen zurückrepliziert. Dieses Szenario wird als **Wiederherstellung am ursprünglichen Speicherort** bezeichnet. Wenn der lokale virtuelle Computer nicht vorhanden ist, handelt es sich um ein Szenario mit **Wiederherstellung an einem alternativen Speicherort**.

> [!NOTE]
> Ein Failback kann nur zum ursprünglichen vCenter- und Konfigurationsserver erfolgen. Sie können keinen neuen Konfigurationsserver bereitstellen und diesen zum Ausführen eines Failbacks verwenden. Darüber hinaus können Sie dem vorhandenen Konfigurationsserver kein neues vCenter hinzufügen und ein Failback zu diesem neuen vCenter ausführen.

## <a name="original-location-recovery-olr"></a>Wiederherstellung am ursprünglichen Speicherort
Wenn Sie ein Failback zum ursprünglichen virtuellen Computer durchführen, müssen folgende Bedingungen erfüllt sein:

* Wenn der virtuelle Computer von einem vCenter-Server verwaltet wird, muss der ESX-Host des Masterziels Zugriff auf den Datenspeicher des virtuellen Computers haben.
* Wenn sich der virtuelle Computer auf einem ESX-Host befindet, jedoch nicht von vCenter verwaltet wird, muss sich die Festplatte des virtuellen Computers in einen Datenspeicher befinden, auf den der Host des Masterziels zugreifen kann.
* Wenn sich Ihr virtueller Computer auf einem ESX-Host befindet und nicht vCenter verwendet, müssen Sie für den ESX-Host des Masterziels eine Ermittlung ausführen, bevor Sie den erneuten Schutz ausführen. Dasselbe gilt auch bei einem Failback für physische Server.
* Sie können ein Failback zu einem virtuellen Storage Area Network (vSAN) oder auf einen Datenträger, durchführen, der auf einem direktem Geräteverweis (RDM) basiert, wenn die Datenträger bereits vorhanden und mit dem lokalen virtuellen Computer verbunden sind.

> [!IMPORTANT]
> Es ist wichtig, „disk.enableUUID= TRUE“ zu aktivieren, damit der Azure Site Recovery-Dienst während des Failbacks den ursprünglichen virtuellen Datenträger auf dem virtuellen Computer identifizieren kann, auf den die ausstehenden Änderungen geschrieben werden. Wenn dieser Wert nicht auf TRUE festgelegt ist, versucht der Dienst, auf Grundlage der besten Leistung den entsprechenden lokalen virtuellen Datenträger zu ermitteln. Wenn der richtige virtuelle Datenträger nicht gefunden wird, erstellt der Dienst einen neuen Datenträger, auf den die Daten geschrieben werden.

## <a name="alternate-location-recovery-alr"></a>Wiederherstellung an einem alternativen Speicherort
Wenn der lokale virtuelle Computer vor dem erneuten Schützen des virtuellen Computers nicht vorhanden ist, handelt es sich um ein Szenario mit Wiederherstellung an einem alternativen Speicherort. Mit dem Workflow zum erneuten Schützen wird der lokale virtuelle Computer neu erstellt. Außerdem wird ein vollständiger Download der Daten durchgeführt.

* Wenn Sie ein Failback zu einem alternativen Speicherort ausführen, wird der virtuelle Computer auf demselben ESX-Host wiederhergestellt, auf dem der Masterzielserver bereitgestellt ist. Der zum Erstellen des Datenträgers verwendete Datenspeicher ist derselbe Datenspeicher, der beim erneuten Schützen des virtuellen Computers ausgewählt wurde.
* Sie können nur ein Failback nur auf einen VMFS- (Dateisystem für virtuelle Computer) oder vSAN-Datenspeicher ausführen. Bei RDM funktionieren das erneute Schützen und das Failback nicht.
* Das erneute Schützen umfasst eine große anfängliche Datenübertragung gefolgt von den Änderungen. Dieser Prozess ist vorhanden, da der virtuelle Computer lokal nicht vorhanden ist. Die vollständigen Daten müssen zurückrepliziert werden. Dieser erneute Schutz dauert außerdem länger als die Wiederherstellung am ursprünglichen Speicherort.
* Ein Failback auf RDM-basierte Datenträger ist nicht möglich. Nur neue virtuelle Datenträger (VMDKs) können in einem VMFS-/vSAN-Datenspeicher erstellt werden.

> [!NOTE]
> Bei einem physischen Computer kann nach einem Failover zu Azure das Failback nur als virtueller VMware-Computer erfolgen. Der Workflow ist dabei der gleiche wie bei der Wiederherstellung an einem anderen Speicherort. Stellen Sie sicher, dass Sie mindestens einen Masterzielserver und die erforderlichen ESX/ESXi-Hosts ermitteln, auf die Sie ein Failback ausführen müssen.

## <a name="next-steps"></a>Nächste Schritte

Führen Sie die Schritte für den [Failbackvorgang](vmware-azure-failback.md) aus.

