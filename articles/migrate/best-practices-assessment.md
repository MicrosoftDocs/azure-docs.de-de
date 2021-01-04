---
title: Best Practices für die Bewertung bei der Azure Migrate-Serverbewertung
description: Hier finden Sie Tipps zum Erstellen von Bewertungen mit der Azure Migrate-Serverbewertung.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: c1fff5b5b7f6450ad8d1977e55a1f6b255f3d668
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754316"
---
# <a name="best-practices-for-creating-assessments"></a>Bewährte Methoden für die Erstellung von Bewertungen

[Azure Migrate](./migrate-services-overview.md) stellt einen Hub mit Tools bereit, die Ihnen dabei helfen, Apps, Infrastrukturen und Workloads zu ermitteln, zu bewerten und zu Microsoft Azure zu migrieren. Der Hub umfasst Azure Migrate-Tools sowie Angebote von unabhängigen Drittanbietern (Independent Software Vendors, ISVs).

Dieser Artikel fasst die bewährten Methoden bei der Erstellung von Bewertungen mit dem Azure Migrate-Serverbewertungstool zusammen.

## <a name="about-assessments"></a>Informationen zu Bewertungen

Bewertungen, die Sie mit Azure Migrate-Serverbewertungen erstellen, sind Momentaufnahmen von Daten zu einem gewissen Zeitpunkt. Es gibt zwei Arten von Bewertungen, die Sie mit der Azure Serverbewertung:

**Bewertungstyp** | **Details**
--- | --- 
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md), [Hyper-V-VMs](how-to-set-up-appliance-hyper-v.md) und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Größenkriterien
Die Serverbewertung verfügt über zwei Optionen für Größenkriterien:

**Größenkriterien** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen, die Empfehlungen auf der Grundlage der erfassten Leistungsdaten aussprechen | **Bewertung vom Typ „Virtueller Azure-Computer“** : Die Empfehlung zur VM-Größe basiert auf CPU- und Speicherauslastungsdaten.<br/><br/> Die Empfehlung zum Datenträgertyp (HDD/SSD Standard oder Premium) basiert auf dem IOPS und dem Durchsatz der lokalen Datenträger.<br/><br/> **Bewertung vom Typ „Azure VMware Solution (AVS)“** : Die Empfehlung zu AVS-Knoten basiert auf CPU- und Speicherauslastungsdaten.
**Aktuelle lokale Umgebung** | Bewertungen, die keine Leistungsdaten verwenden, um Empfehlungen auszusprechen. | **Bewertung vom Typ „Virtueller Azure-Computer“** : Die Empfehlung zur VM-Größe basiert auf der lokalen VM-Größe<br/><br> Der empfohlene Datenträgertyp basiert auf dem, was Sie in der Speichertypeinstellung für die Bewertung auswählen.<br/><br/> **Bewertung vom Typ „Azure VMware Solution (AVS)“** : Die Empfehlung zu AVS-Knoten basiert auf der lokalen VM-Größe.

#### <a name="example"></a>Beispiel
Wenn Sie beispielsweise eine lokale VM mit vier Kernen bei 20 % Auslastung und einem Speicher von 8 GB bei 10 % Auslastung haben, sehen die Bewertungen der Azure-VM wie folgt aus:

- **Leistungsbasierte Bewertung**:
    - Identifiziert effektive Kerne und Speicher basierend auf dem Kern (4 x 0,20 = 0,8) und der Speicherauslastung (8 GB x 0,10 = 0,8).
    - Wendet den in den Bewertungseigenschaften angegebenen Komfortfaktor (z.B. 1,3x) an, um die für die Dimensionierung zu verwendenden Werte zu erhalten. 
    - Empfiehlt die nächstgelegene VM-Größe in Azure, die bis zu 1,04 Kerne (0,8 x 1,3) und bis zu 1,04 GB (0,8 x 1,3) Speicher unterstützen kann.

- **Aktuelle Bewertung (wie in lokaler Umgebung)** :
    -  Empfiehlt eine VM mit vier Kernen und 8 GB Speicher.


## <a name="best-practices-for-creating-assessments"></a>Bewährte Methoden für die Erstellung von Bewertungen

Die Azure Migrate-Appliance erfasst fortlaufend Ihre lokale Umgebung und sendet Metadaten und Leistungsdaten an Azure. Befolgen Sie diese bewährten Methoden für die Bewertung von Servern, die mit einer Appliance ermittelt wurden:

- **Erstellung von aktuellen Bewertungen**: Sie können aktuelle Bewertungen sofort erstellen, sobald Ihre Computer im Azure Migrate-Portal angezeigt werden.
- **Erstellen von leistungsbezogenen Bewertungen**: Nach der Einrichtung der Ermittlung empfehlen wir Ihnen, mindestens einen Tag zu warten, bevor Sie eine leistungsbezogene Bewertung durchführen:
    - Die Erfassung von Leistungsdaten braucht Zeit. Wenn Sie mindestens einen Tag warten, stellen Sie damit sicher, dass genügend Leistungsdatenpunkte vorhanden sind, bevor Sie die Bewertung durchführen.
    - Wenn Sie leistungsbezogene Bewertungen durchführen, stellen Sie sicher, dass Sie Profile für Ihre Umgebung für die Dauer der Bewertungen erstellen. Wenn Sie z.B. die Bewertung mit einer auf eine Woche festgelegten Leistungsdauer erstellen, müssen Sie mindestens eine Woche nach dem Start der Ermittlung warten, bis alle Datenpunkte erfasst wurden. Wenn Sie nicht so lange warten, wird die Bewertung nicht mit fünf Sternen versehen.
- **Neuberechnen von Bewertungen**: Da es sich bei den Bewertungen um Momentaufnahmen handelt, werden sie nicht automatisch mit den neuesten Daten aktualisiert. Um eine Bewertung mit den neuesten Daten zu aktualisieren, müssen Sie sie neu berechnen.

Befolgen Sie diese bewährten Methoden für die Bewertung von Servern, die über eine CSV-Datei in Azure Migrate importiert werden:

- **Erstellung von aktuellen Bewertungen**: Sie können aktuelle Bewertungen sofort erstellen, sobald Ihre Computer im Azure Migrate-Portal angezeigt werden.
- **Erstellen von leistungsbezogenen Bewertungen**: Dadurch erhalten Sie eine bessere Kostenschätzung, insbesondere dann, wenn die lokale Serverkapazität überdimensioniert wurde. Die Genauigkeit der leistungsbasierten Bewertung hängt jedoch von den Leistungsdaten ab, die für die Server angegeben werden. 
- **Neuberechnen von Bewertungen**: Da es sich bei den Bewertungen um Momentaufnahmen handelt, werden sie nicht automatisch mit den neuesten Daten aktualisiert. Um eine Bewertung mit den neuesten importierten Daten zu aktualisieren, müssen Sie eine Neuberechnung durchführen.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>FTT-Dimensionierungsparameter für die AVS-Bewertung

Die in AVS verwendete Speicher-Engine ist vSAN. Die Speicheranforderungen für Ihre virtuellen Computer werden mithilfe von vSAN-Speicherrichtlinien definiert. Diese Richtlinien steuern, wie Speicher dem virtuellen Computer zugeordnet wird, um die erforderliche Dienstebene für Ihre virtuellen Computer zu garantieren. Folgende FTT-RAID-Kombinationen sind verfügbar: 

**Zu tolerierende Fehler (Failures to Tolerate, FTT)** | **RAID-Konfiguration** | **Mindestens erforderliche Hostanzahl** | **Überlegungen zur Größe**
--- | --- | --- | --- 
1 | RAID-1 (Spiegelung) | 3 | Von einem virtuellen Computer mit 100 GB werden 200 GB beansprucht.
1 | RAID-5 (Erasure Coding) | 4 | Von einem virtuellen Computer mit 100 GB werden 133,33 GB beansprucht.
2 | RAID-1 (Spiegelung) | 5 | Von einem virtuellen Computer mit 100 GB werden 300 GB beansprucht.
2 | RAID-6 (Erasure Coding) | 6 | Von einem virtuellen Computer mit 100 GB werden 150 GB beansprucht.
3 | RAID-1 (Spiegelung) | 7 | Von einem virtuellen Computer mit 100 GB werden 400 GB beansprucht.


## <a name="best-practices-for-confidence-ratings"></a>Bewährte Methoden für Zuverlässigkeitsstufen

Wenn Sie leistungsbezogene Bewertungen durchführen, wird der Bewertung eine Zuverlässigkeitsstufe von 1 Stern (niedrigste) bis 5 Sterne (höchste) zugewiesen. So nutzen Sie Zuverlässigkeitsstufen effektiv:
- Azure Migrate-Serverbewertungen benötigen die Nutzungsdaten für CPU/Speicher der VM.
- Für jeden an den virtuellen lokalen Computer angefügten Datenträger werden Informationen zu den Lese-/Schreib-IOPS sowie zum Durchsatz benötigt.
- Für jeden an die VM angefügten Netzwerkadapter benötigen sie die Netzwerk-E/A-Informationen.

Abhängig vom Prozentsatz der für die gewählte Dauer verfügbaren Datenpunkte wird die Zuverlässigkeitsstufe für eine Bewertung wie in der folgenden Tabelle zusammengefasst angegeben.

   **Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
   --- | ---
   0 % bis 20 % | Ein Stern
   21 % bis 40 % | Zwei Sterne
   41 % bis 60 % | Drei Sterne
   61 % bis 80 % | Vier Sterne
   81 % bis 100 % | Fünf Sterne


## <a name="common-assessment-issues"></a>Häufe Bewertungsprobleme

Im Folgenden erfahren Sie, wie Sie einige häufig auftretenden Probleme mit der Umgebung lösen können, die sich auf die Bewertungen auswirken.

###  <a name="out-of-sync-assessments"></a>Nicht synchronisierte Bewertungen

Wenn Sie nach dem Erstellen einer Bewertung Computer einer Gruppe hinzufügen oder daraus entfernen, wird die von Ihnen erstellte Bewertung als **nicht synchron** gekennzeichnet. Führen Sie die Bewertung erneut durch (**Neu berechnen**), um die Gruppenänderungen zu berücksichtigen.

### <a name="outdated-assessments"></a>Veraltete Bewertungen

Wenn es lokale Änderungen an VMs gibt, die sich in einer Gruppe befinden, die bewertet wurde, wird die Bewertung als **veraltet** gekennzeichnet. Eine Bewertung kann aufgrund von Änderungen in den folgenden Eigenschaften als veraltet gekennzeichnet werden:

- Anzahl der Prozessorkerne
- Zugeordneter Arbeitsspeicher
- Starttyp oder Firmware
- Name, Version und Architektur des Betriebssystems
- Anzahl der Datenträger
- Anzahl der Netzwerkadapter
- Änderung der Datenträgergröße (zugeordnete GB)
- Aktualisierung der NIC-Eigenschaften Beispiel: Änderungen der MAC-Adresse, Hinzufügung einer IP-Adresse usw.

Führen Sie die Bewertung erneut durch (**Neu berechnen**), um die Änderungen zu berücksichtigen.

### <a name="low-confidence-rating"></a>Niedrige Zuverlässigkeitsstufe

Eine Bewertung kann aus verschiedenen Gründen nicht über alle Datenpunkte verfügen:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil Ihrer Umgebung erstellt. Wenn Sie z.B. eine *leistungsbasierte Bewertung* mit einer auf eine Woche festgelegten Leistungsdauer erstellen, müssen Sie mindestens eine Woche nach dem Start der Ermittlung warten, bis alle Datenpunkte erfasst wurden. Sie können jederzeit auf **Neu berechnen** klicken, um die neueste anwendbare Zuverlässigkeitsstufe anzuzeigen. Die Zuverlässigkeitsstufe ist nur beim Erstellen einer *leistungsbasierten* Bewertung anwendbar.

- Einige virtuelle Computer wurden während des Zeitraums, für den die Bewertung berechnet wird, heruntergefahren. Wenn VMs für eine gewisse Zeit heruntergefahren werden, kann das Tool „Serverbewertung“ für diesen Zeitraum keine Leistungsdaten erfassen.

- Nach dem Start der Ermittlung in der Serverbewertung wurden wenige VMs erstellt. Ein Beispiel: Angenommen, Sie erstellen eine Bewertung für den Leistungsverlauf des letzten Monats, und in der Umgebung wurden letzte Woche einige virtuelle Computer erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen VMs zur Verfügung und die Zuverlässigkeitsstufe wäre gering.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Leitfaden zum Migrationstool für AVS-Bewertungen

Im Bericht zur Azure-Bereitschaft für die Bewertung „Azure VMware Solution (AVS)“ werden folgende Tools empfohlen: 
- **VMware HCX oder Enterprise**: Für VMware-Computer wird die VMware HCX-Lösung (Hybrid Cloud Extension) empfohlen, um Ihre lokale Workload zu Ihrer privaten AVS-Cloud (Azure VMware Solution) zu migrieren. [Weitere Informationen](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Unbekannt:** Für Computer, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool unbekannt. Für VMware-Computer empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension).


## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr darüber](concepts-assessment-calculation.md), wie Bewertungen berechnet werden.
- Informationen zum Anpassen von Bewertungen finden Sie [hier](how-to-modify-assessment.md).
