---
title: Versionsverlauf des Azure Site Recovery-Bereitstellungsplaners
description: Informiert über Fehlerbehebungen und bekannte Einschränkungen bei verschiedenen Versionen des Site Recovery-Bereitstellungsplaners zusammen mit den jeweiligen Veröffentlichungsdaten.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64928671"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Versionsverlauf des Azure Site Recovery-Bereitstellungsplaners

Dieser Artikel enthält den Verlauf aller Versionen des Azure Site Recovery-Bereitstellungsplaners zusammen mit den Fehlerbehebungen, den jeweiligen bekannten Einschränkungen und den Veröffentlichungsdaten.

## <a name="version-24"></a>Version 2.4

**Veröffentlichungsdatum: 17. April 2019**

**Fehlerbehebungen:**

- Die Kompatibilität des Betriebssystems wurde verbessert, insbesondere bei der Behandlung von Fehlern, die auf der Lokalisierung basieren.
- VMs mit einer Datenänderungsrate von bis zu 20 MBit/s wurden zur Kompatibilitätsprüfliste hinzugefügt.
- Verbesserte Fehlermeldungen
  - Unterstützung für vCenter 6.7 wurde hinzugefügt.
  - Unterstützung für Windows Server 2019 und Red Hat Enterprise Linux (RHEL)-Arbeitsstation wurde hinzugefügt.



## <a name="version-23"></a>Version 2.3

**Veröffentlichungsdatum: 3. Dezember 2018**

**Fehlerbehebungen:**

- Ein Problem wurde behoben, das verhinderte, dass der Bereitstellungsplaner einen Bericht mit dem vorgesehenen Zielspeicherort und Abonnement generierte.

## <a name="version-22"></a>Version 2.2 

**Veröffentlichungsdatum: 25. April 2018**

**Fehlerbehebungen:**

- GetVMList-Vorgänge:
  - Ein Problem wurde behoben, das zum Fehlschlagen von GetVMList führte, wenn der angegebene Ordner nicht vorhanden ist. Jetzt wird entweder das Standardverzeichnis oder das im Parameter „outputfile“ angegebene Verzeichnis erstellt.
  - Detailliertere Fehlerursachen für GetVMList wurden hinzugefügt.
- VM-Typinformationen wurden als eine Spalte im Blatt mit kompatiblen VMs des Bereitstellungsplaner-Berichts hinzugefügt.
- Hyper-V zu Azure-Notfallwiederherstellung:
  - VMs mit freigegebenen virtuellen Festplatten (VHDs) und Pass-Through-Datenträgern wurden aus der Profilerstellung ausgeschlossen. Der StartProfiling-Vorgang zeigt die Liste ausgeschlossener VMs in der Konsole an.
  - VMs mit mehr als 64 Datenträgern wurden zur Liste von nicht kompatiblen VMs hinzugefügt.
  - Der Komprimierungsfaktor für Erstreplikation (Initial Replication, IR) und Deltareplikation (DR) wurde aktualisiert.
  - Eingeschränkte Unterstützung für SMB-Speicher wurde hinzugefügt.

## <a name="version-21"></a>Version 2.1

**Veröffentlichungsdatum: 3. Januar 2018**

**Fehlerbehebungen:**

- Der Excel-Bericht wurde aktualisiert.
- Fehler im GetThroughput-Vorgang wurden behoben.
- Eine Option zum Einschränken der Anzahl von VMs, mit denen Profile oder Berichte erstellt werden können, wurde hinzugefügt. Der Standardgrenzwert lautet 1.000 VMs.
- VMware zu Azure-Notfallwiederherstellung:
  - Ein Problem der Windows Server 2016-VM, das in die nicht kompatible Tabelle eingeht, wurde behoben. 
  - Kompatibilitätsmeldungen zu Extensible Firmware Interface (EFI) Windows-VMs wurden aktualisiert.
- VMware zu Azure und Hyper-V zu Azure, VM-Datenänderungsrate pro VM wurde aktualisiert. 
- Die Zuverlässigkeit beim Analysieren von Dateien in der VM-Liste wurde verbessert.

## <a name="version-201"></a>Version 2.0.1

**Veröffentlichungsdatum: 7. Dezember 2017**

**Fehlerbehebungen:**

- Eine Empfehlung zum Optimieren der Netzwerkbandbreite wurde hinzugefügt.

## <a name="version-20"></a>Version 2.0

**Veröffentlichungsdatum: 28. November 2017**

**Fehlerbehebungen:**

- Unterstützung für Hyper-V zu Azure-Notfallwiederherstellung wurde hinzugefügt.
- Kostenrechner wurde hinzugefügt.
- Überprüfung der Betriebssystemversion für VMware zu Azure-Notfallwiederherstellung wurde hinzugefügt, um zu ermitteln, ob die VM für den Schutz kompatibel oder nicht kompatibel ist. Das Tool verwendet die Zeichenfolge mit der Betriebssystemversion, die vom vCenter-Server für diese VM zurückgegeben wird. Dabei handelt es sich um die Version des Gastbetriebssystems, die der jeweilige Benutzer beim Erstellen der VM in VMware ausgewählt hat.

**Bekannte Einschränkungen:**

- Bei der Hyper-V zu Azure-Notfallwiederherstellung wird eine VM, deren Namen Zeichen wie `,`, `"`, `[`, `]` und ``` ` ``` enthält, nicht unterstützt. Wenn ein Profil erstellt wird, schlägt die Berichtgenerierung fehl oder führt zu einem falschen Ergebnis.
- Bei der VMware zu Azure-Notfallwiederherstellung wird eine VM, deren Name ein Komma enthält, nicht unterstützt. Wenn ein Profil erstellt wird, schlägt die Berichtgenerierung fehl oder führt zu einem falschen Ergebnis.

## <a name="version-131"></a>Version 1.3.1

**Veröffentlichungsdatum: 19. Juli 2017** 

**Fehlerbehebungen:**

- Unterstützung für große Datenträger (> 1 TB) bei der Berichtgenerierung wurde hinzugefügt. Mit dem Bereitstellungsplaner können Sie jetzt die Replikation für virtuelle Computer planen, deren Datenträger größer als 1 TB (bis zu 4095 GB) sind.
Weitere Informationen finden Sie unter [Azure Site Recovery now supports large disk sizes in Azure](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/) (Azure Site Recovery unterstützt nun große Datenträger in Azure).

## <a name="version-13"></a>Version 1.3

**Veröffentlichungsdatum: 9. Mai 2017**

**Fehlerbehebungen:**

- Unterstützung für verwaltete Datenträger bei der Berichtgenerierung wurde hinzugefügt. Die Anzahl der VMs, die in einem einzelnen Speicherkonto enthalten sein können, wird basierend auf der Auswahl eines verwalteten Datenträgers für das Failover bzw. Testfailover berechnet.

## <a name="version-12"></a>Version 1.2

**Veröffentlichungsdatum: 7. April 2017**

**Fehlerbehebungen:**

- Überprüfungen des Starttyps (BIOS oder EFI) für jede VM wurde hinzugefügt, um zu ermitteln, ob die VM für den Schutz kompatibel oder nicht kompatibel ist.
- Informationen zum Betriebssystemtyp wurden für jeden virtuellen Computer in den Arbeitsblättern für kompatible VMs und nicht kompatible VMs hinzugefügt.
- Unterstützung für GetThroughput-Vorgang für die Microsoft Azure-Regionen „US Government“ und „China“ wurde hinzugefügt.
- Einige zusätzliche Voraussetzungsüberprüfungen für vCenter- und ESXi-Server wurden hinzugefügt.
- Ein Problem wurde behoben, bei dem ein falscher Bericht generiert wurde, wenn nicht englische Einstellungen für ein Gebietsschema festgelegt wurden.

## <a name="version-11"></a>Version 1.1

**Veröffentlichungsdatum: 9. März 2017**

**Fehlerbehebungen:**

- Ein Problem wurde behoben, das die Profilerstellung bei VMs verhinderte, wenn es zwei oder mehr VMs mit demselben Namen oder derselben IP-Adresse für verschiedene vCenter ESXi-Hosts gibt.
- Ein Problem wurde behoben, das dazu führte, dass Kopieren und Durchsuchen bei den Arbeitsblättern für kompatible VMs und nicht kompatible VMs deaktiviert wurde.

## <a name="version-10"></a>Version 1.0

**Veröffentlichungsdatum: 23. Februar 2017**

**Bekannte Einschränkungen:**

- Unterstützt nur Notfallwiederherstellungsszenarien des Typs „VMware zu Azure“. Verwenden Sie für Notfallwiederherstellungsszenarien des Typs „Hyper-V zu Azure“ das [Hyper-V Capacity Planner-Tool](./site-recovery-capacity-planning-for-hyper-v-replication.md).
- Der GetThroughput-Vorgang wird für die Microsoft Azure-Regionen „US Government“ und „China“ nicht unterstützt.
- Das Tool kann keine Profile für VMs erstellen, wenn der vCenter-Server über zwei oder mehr VMs mit demselben Namen oder derselben IP-Adresse für verschiedene ESXi-Hosts verfügt.
In dieser Version überspringt das Tool die Profilerstellung für doppelte Namen und IP-Adressen von VMs in der VMListFile. Die Problemumgehung besteht darin, die Profilerstellung für die VMs nicht mit dem vCenter-Server durchzuführen, sondern mit einem ESXi-Host. Stellen Sie sicher, dass für jeden ESXi-Host eine Instanz ausgeführt wird.
