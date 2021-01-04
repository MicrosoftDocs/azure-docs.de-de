---
title: Anpassen von Bewertungen für Azure Migrate-Serverbewertung | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie mit der Azure Migrate-Serverbewertung erstellte Bewertungen anpassen.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: de90cffe8760a55aafebf079678d139c6f6f99e1
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751103"
---
# <a name="customize-an-assessment"></a>Anpassen einer Bewertung

Dieser Artikel beschreibt, wie Sie mit der Azure Migrate-Serverbewertung erstellte Bewertungen anpassen.

[Azure Migrate](migrate-services-overview.md) ist ein zentraler Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration Ihrer lokalen Apps und Workloads sowie von VMs in der privaten/öffentlichen Cloud zu Azure. Der Hub stellt Azure Migrate-Tools für die Bewertung und Migration sowie Angebote von unabhängigen Drittanbietern (Independent Software Vendors, ISVs) bereit.

Mit dem Tool Azure Migrate-Serverbewertung können Sie Bewertungen für lokale VMware-VMs und Hyper-V-VMs in Vorbereitung auf die Migration zu Azure erstellen. Das Serverbewertungstool bewertet lokale Server für die Migration zu virtuellen Azure IaaS-Computern und Azure VMware Solution (AVS). 

## <a name="about-assessments"></a>Informationen zu Bewertungen

Bewertungen, die Sie mit der Serverbewertung erstellen, sind Momentaufnahmen von Daten zu einem bestimmten Zeitpunkt. Es gibt zwei Arten von Bewertungen, die Sie mit der Azure Server Assessment“ (Azure Migrate-Serverbewertung) erstellen.

**Bewertungstyp** | **Details**
--- | --- 
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md), [Hyper-V-VMs](how-to-set-up-appliance-hyper-v.md) und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure bewerten, indem Sie diesen Bewertungstyp verwenden.(concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

Eine Azure VM-Bewertung in der Serverbewertung bietet zwei Optionen für Größenkriterien:

**Größenkriterien** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen, die Empfehlungen auf der Grundlage der erfassten Leistungsdaten aussprechen | **Bewertung vom Typ „Virtueller Azure-Computer“** : Die Empfehlung zur VM-Größe basiert auf CPU- und Speicherauslastungsdaten.<br/><br/> Die Empfehlung zum Datenträgertyp (HDD/SSD Standard oder Premium) basiert auf dem IOPS und dem Durchsatz der lokalen Datenträger.<br/><br/> **Bewertung vom Typ „Azure VMware Solution (AVS)“** : Die Empfehlung zu AVS-Knoten basiert auf CPU- und Speicherauslastungsdaten.
**Aktuelle lokale Umgebung** | Bewertungen, die keine Leistungsdaten verwenden, um Empfehlungen auszusprechen. | **Bewertung vom Typ „Virtueller Azure-Computer“** : Die Empfehlung zur VM-Größe basiert auf der lokalen VM-Größe<br/><br> Der empfohlene Datenträgertyp basiert auf dem, was Sie in der Speichertypeinstellung für die Bewertung auswählen.<br/><br/> **Bewertung vom Typ „Azure VMware Solution (AVS)“** : Die Empfehlung zu AVS-Knoten basiert auf der lokalen VM-Größe.


## <a name="how-is-an-assessment-done"></a>Wie wird eine Bewertung durchgeführt?

Eine Bewertung in der Azure Migrate-Serverbewertung erfolgt in drei Phasen. Die Bewertung beginnt mit einer Eignungsanalyse, gefolgt von einer Größenanpassung und abschließend einer Schätzung der monatlichen Kosten. Ein Computer gelangt nur in die nächste Phase, wenn er die vorherige besteht. Beispiel: Besteht ein Computer die Azure-Eignungsprüfung nicht, wird er als ungeeignet für Azure markiert, und die Größen- und Kostenschätzungen werden nicht durchgeführt. [Weitere Informationen.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Was umfasst eine Azure VM-Bewertung?

**Eigenschaft** | **Details**
--- | ---
**Zielstandort** | Der Azure-Standort, zu dem die Migration durchgeführt werden soll.<br/> Die Serverbewertung unterstützt derzeit die folgenden Zielregionen: „Australien, Osten“, „Australien, Südosten“, „Brasilien, Süden“, „Kanada, Mitte“, „Kanada, Osten“, „Indien, Mitte“, „USA, Mitte“, „China, Osten“, „China, Norden“, „Asien, Osten“, „USA, Osten“, „Deutschland, Mitte“, „Deutschland, Nordosten“, „USA, Osten 2“, „Japan, Osten“, „Japan, Westen“, „Südkorea, Mitte“, „Südkorea, Süden“, „USA, Norden-Mitte“, „Europa, Norden“, „USA, Süden-Mitte“, „Asien, Südosten“, „Indien, Süden“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „US Gov Arizona“, „US Gov Texas“, „US Gov Virginia“, „USA, Westen-Mitte“, „Europa, Westen“, „Indien, Westen“, „USA, Westen“ und „USA, Westen 2“.
**Speichertyp** | Sie können diese Eigenschaft nutzen, um den Typ der Datenträger anzugeben, die Sie nach Azure verschieben möchten.<br/><br/> Für die Größenanpassung für die lokale Umgebung können Sie den Zieldatenträgertyp als „Managed Disks Premium“, „Managed Disks SSD Standard“ oder „Managed Disks HDD Standard“ angeben. Für die leistungsbasierte Größenanpassung können Sie den Zieldatenträgertyp als „Automatisch“, „Managed Disks Premium“, „Managed Disks HDD Standard“ oder „Managed Disks SSD Standard“ angeben.<br/><br/> Beim Angeben des Speichertyps als „Automatisch“ erfolgt die Datenträgerempfehlung basierend auf den Leistungsdaten der Datenträger (IOPS und Durchsatz). Wenn Sie den Speichertyp als Premium/Standard angeben, empfiehlt die Bewertung eine Datenträger-SKU innerhalb des ausgewählten Speichertyps. Wenn Sie eine Einzelinstanz-VM-SLA von 99,9% erreichen möchten, könnten Sie als Speichertyp „Managed Disks Premium“ angeben. Dadurch wird sichergestellt, dass für alle Datenträger in der Bewertung „Managed Disks Premium“ empfohlen wird. Azure
**Reservierte Instanzen (RI)** | Mit dieser Eigenschaft können Sie festlegen, ob Sie [reservierte Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure haben. Die Kostenschätzungen in der Bewertung werden dann unter Berücksichtigung von RI-Rabatten durchgeführt. Reservierte Instanzen werden derzeit nur für das Angebot mit nutzungsbasierter Bezahlung in Azure Migrate unterstützt.
**Größenkriterium** | Das Kriterium, das zur Größenanpassung virtueller Computer für Azure verwendet werden soll. Sie können eine *leistungsbasierte* Größenanpassung vornehmen oder die Größe der virtuellen Computer *lokal* ändern, ohne den Leistungsverlauf zu berücksichtigen.
**Leistungsverlauf** | Die zur Bewertung der Leistungdaten der Computer zu berücksichtigende Dauer. Diese Eigenschaft ist nur anwendbar, wenn als Größenkriterium *leistungsbasiert* festgelegt ist.
**Perzentilwert der Nutzung** | Der für die Größenanpassung zu berücksichtigende Perzentilwert des festgelegten Leistungsbeispiels. Diese Eigenschaft ist nur anwendbar, wenn als Größe *leistungsbasiert* festgelegt ist.
**VM-Serie** |     Sie können die VM-Serie angeben, die Sie für die Größenanpassung berücksichtigen möchten. Wenn Sie beispielsweise eine Produktionsumgebung haben, die Sie nicht zu VMs der A-Serie in Azure migrieren möchten, können Sie die A-Serie von der Liste oder aus der Serie ausschließen, sodass die Größenanpassung nur in der ausgewählten Serie erfolgt.
**Komfortfaktor** | Bei der Azure Migrate-Serverbewertung wird während der Bewertung ein Puffer (Komfortfaktor) berücksichtigt. Dieser Puffer wird zusätzlich zu den Daten zur Computernutzung für VMs (CPU, Arbeitsspeicher, Datenträger und Netzwerk) angewendet. Beim Komfortfaktor geht es um Bereiche wie saisonale Nutzung, kurzer Leistungsverlauf und voraussichtliche zukünftige Zunahme der Nutzung.<br/><br/> Beispiel: Für einen virtuellen Computer mit zehn Kernen und einer Auslastung von 20 Prozent ergibt sich normalerweise ein virtueller Computer mit zwei Kernen. Bei einem Komfortfaktor von 2.0x ist das Ergebnis dagegen eine VM mit vier Kernen.
**Angebot** | Das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/), bei dem Sie registriert sind. Dies wird dann von Azure Migrate bei der Kostenschätzung entsprechend berücksichtigt.
**Währung** | Rechnungswährung.
**Rabatt (%)** | Alle abonnementspezifischen Rabatte, die Sie zusätzlich zum Azure-Angebot erhalten.<br/> Die Standardeinstellung ist 0 %.
**VM-Betriebszeit** | Wenn Ihre virtuellen Computer nicht rund um die Uhr in Azure aktiv sind, können Sie den Zeitraum angeben (Anzahl der Tage pro Monat und Anzahl der Stunden pro Tag), für den sie voraussichtlich ausgeführt werden, um eine entsprechende Kostenschätzung zu erhalten.<br/> Der Standardwert sind 31 Tage pro Monat und 24 Stunden pro Tag.
**Azure-Hybridvorteil** | Geben Sie an, ob Sie über die Software Assurance verfügen und den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) nutzen können. Wenn diese Einstellung auf „Ja“ festgelegt ist, werden für virtuelle Windows-Computer Nicht-Windows-Azure-Preise veranschlagt. Die Standardeinstellung ist „Ja“.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Was ist eine Bewertung vom Typ „Azure VMware Solution (AVS)“?

Eine AVS-Bewertung mit dem Serverbewertungstool umfasst Folgendes:


| **Eigenschaft** | **Details** |
| - | - |
| **Zielstandort** | Gibt den Standort der privaten AVS-Cloud an, zu der Sie migrieren möchten.<br/><br/> Die AVS-Bewertung der Serverbewertung unterstützt derzeit die folgenden Zielregionen: „USA, Osten“, „Europa, Westen“, „USA, Westen“. |
| **Speichertyp** | Gibt die Speicher-Engine an, die in AVS verwendet werden soll.<br/><br/> Beachten Sie, dass die AVS-Bewertungen nur vSAN als Standardspeichertyp unterstützen. |
**Reservierte Instanzen (RI)** | Mit dieser Eigenschaft können Sie reservierte Instanzen in AVS angeben. RIs werden derzeit für AVS-Knoten nicht unterstützt. |
**Knotentyp** | Gibt den [AVS-Knotentyp](../azure-vmware/concepts-private-clouds-clusters.md) an, der zum Zuordnen der lokalen VMs verwendet wird. Beachten Sie, dass der Standardknotentyp AV36 ist. <br/><br/> Azure Migrate empfiehlt eine erforderliche Anzahl von Knoten für die VMs, die zu AVS migriert werden sollen. |
**FTT-Einstellung, RAID-Ebene** | Gibt die anwendbaren Optionen für tolerierbare Fehler (FTT) oder RAID-Kombinationen an. Die ausgewählte FTT-Option in Kombination mit der lokalen VM-Datenträgeranforderung bestimmt den gesamten in AVS erforderlichen vSAN-Speicher. |
**Größenkriterium** | Legt das Kriterium fest, das zur _Größenanpassung_ virtueller Computer für AVS verwendet werden soll. Sie können eine _leistungsbasierte_ oder _lokale_ Größenanpassung vornehmen, ohne den Leistungsverlauf zu berücksichtigen. |
**Leistungsverlauf** | Legt die zur Bewertung der Leistungsdaten der Computer zu berücksichtigende Dauer fest. Diese Eigenschaft ist nur anwendbar, wenn als Größenkriterium _leistungsbasiert_ festgelegt ist. |
**Perzentilwert der Nutzung** | Gibt den für die Größenanpassung zu berücksichtigenden Perzentilwert des festgelegten Leistungsbeispiels. Diese Eigenschaft ist nur anwendbar, wenn eine leistungsbasierte Größenanpassung ausgewählt wurde.|
**Komfortfaktor** | Bei der Azure Migrate-Serverbewertung wird während der Bewertung ein Puffer (Komfortfaktor) berücksichtigt. Dieser Puffer wird zusätzlich zu den Daten zur Computernutzung für VMs (CPU, Arbeitsspeicher, Datenträger und Netzwerk) angewendet. Beim Komfortfaktor geht es um Bereiche wie saisonale Nutzung, kurzer Leistungsverlauf und voraussichtliche zukünftige Zunahme der Nutzung.<br/><br/> Beispiel: Für einen virtuellen Computer mit zehn Kernen und einer Auslastung von 20 Prozent ergibt sich normalerweise ein virtueller Computer mit zwei Kernen. Bei einem Komfortfaktor von 2.0x ist das Ergebnis dagegen eine VM mit vier Kernen. |
**Angebot** | Zeigt das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/) an, bei dem Sie registriert sind. Dies wird dann von Azure Migrate bei der Kostenschätzung entsprechend berücksichtigt.|
**Währung** | Zeigt die Abrechnungswährung für Ihr Konto an. |
**Rabatt (%)** | Listet alle abonnementspezifischen Rabatte auf, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %. |
**Azure-Hybridvorteil** | Gibt an, ob Sie über Software Assurance verfügen und den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) nutzen können. Obwohl dies aufgrund des knotenbasierten Preises keine Auswirkungen auf die Preisgestaltung von Azure VMware-Lösungen hat, können Kunden dennoch ihre lokalen Betriebssystemlizenzen (Microsoft-basiert) in AVS mit Azure-Hybridvorteilen anwenden. Andere Anbieter von Softwarebetriebssystemen werden ihre eigenen Lizenzbedingungen wie RHEL zur Verfügung stellen müssen. |
**vCPU-Überzeichnung** | Gibt das Verhältnis der Anzahl der virtuellen Kerne an, die an einen physischen Kern im AVS-Knoten gebunden sind. Der Standardwert in den Berechnungen beträgt vier virtuelle CPUs (vCPU): Ein physischer Kern in AVS. <br/><br/> API-Benutzer können diesen Wert als ganze Zahl (Integer) festlegen. Beachten Sie, dass eine vCPU-Überzeichnung > 4:1 zu einer Leistungsbeeinträchtigung führen kann, aber für Workloads vom Typ „Webserver“ verwendet werden kann. |

## <a name="edit-assessment-properties"></a>Bearbeiten von Bewertungseigenschaften

Gehen Sie folgendermaßen vor, um Bewertungseigenschaften nach dem Erstellen einer Bewertung zu bearbeiten:

1. Klicken Sie im Azure Migrate-Projekt auf **Server**.
2. Klicken Sie in **Azure Migrate: Serverbewertung** auf den Bewertungenleistungsindikator.
3. Klicken Sie in **Bewertung** auf die relevante Bewertung und dann auf **Eigenschaften bearbeiten**.
5. Passen Sie die Bewertungseigenschaften in Übereinstimmung mit den obigen Tabellen an.
6. Klicken Sie auf **Speichern**, um die Bewertung zu aktualisieren.


Sie können Bewertungseigenschaften auch bearbeiten, wenn Sie eine Bewertung erstellen.


## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen](concepts-assessment-calculation.md) zur Berechnung von Azure VM-Bewertungen.
- [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md) zur Berechnung von AVS-Bewertungen.
