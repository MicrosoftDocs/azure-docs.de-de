---
title: Azure VM-Bewertungen mit der Azure Migrate-Serverbewertung
description: Erfahren Sie mehr über Bewertungen mit der Azure Migrate-Serverbewertung.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: f8a4f29114f7e0a2ed7868f01e05e25c8a0d0ce1
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752225"
---
# <a name="server-assessment-overview-migrate-to-azure-vms"></a>Serverbewertungsübersicht (Migration zu Azure-VMs)

In diesem Artikel finden Sie eine Übersicht über Bewertungen im [Serverbewertungstool von Azure Migrate](migrate-services-overview.md#azure-migrate-server-assessment-tool). Mit diesem Tool können Sie lokale VMware-VMs, Hyper-V-VMs und physische Server für die Migration zu Azure bewerten.

## <a name="whats-an-assessment"></a>Was ist eine Bewertung?

Bei einer Bewertung mit dem Serverbewertungstool wird gemessen, inwieweit lokale Server für eine Migration zu Azure bereit sind. Außerdem werden die Auswirkungen einer solchen Migration abgeschätzt.

> [!NOTE]
> Überprüfen Sie in Azure Government die [unterstützten Ziele](migrate-support-matrix.md#supported-geographies-azure-government) für die Bewertung. Beachten Sie, dass für die Empfehlungen zur VM-Größe in Bewertungen die VM-Serien für Regionen der Government-Cloud verwendet werden. [Weitere Informationen](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) zu VM-Typen.

## <a name="types-of-assessments"></a>Arten von Bewertungen

Es gibt zwei Arten von Bewertungen, die Sie mit der Azure Server Assessment“ (Azure Migrate-Serverbewertung) erstellen.

**Bewertungstyp** | **Details**
--- | --- 
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md), [Hyper-V-VMs](how-to-set-up-appliance-hyper-v.md) und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure bewerten, indem Sie diesen Bewertungstyp verwenden.
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

Bewertungen, die Sie mit der Serverbewertung erstellen, sind Momentaufnahmen von Daten zu einem bestimmten Zeitpunkt. Eine Azure VM-Bewertung in der Serverbewertung bietet zwei Optionen für Größenkriterien:

**Bewertungstyp** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen, die Empfehlungen auf der Grundlage der erfassten Leistungsdaten aussprechen | Die Empfehlung zur VM-Größe basiert auf CPU- und RAM-Auslastungsdaten.<br/><br/> Die Empfehlung zum Datenträgertyp basiert auf den Eingabe-/Ausgabevorgängen pro Sekunde (IOPS) und dem Durchsatz der lokalen Datenträger. Mögliche Datenträgertypen sind Azure HDD Standard-, Azure SSD Standard- und Azure Premium-Datenträger.
**Aktuelle lokale Umgebung** | Bewertungen, bei denen keine Leistungsdaten verwendet werden, um Empfehlungen auszusprechen | Die Empfehlung zur VM-Größe basiert auf der lokalen VM-Größe.<br/><br> Der empfohlene Datenträgertyp basiert auf dem ausgewählten Speichertyp für die Bewertung.

## <a name="how-do-i-run-an-assessment"></a>Ausführen einer Bewertung

Eine Bewertung kann auf verschiedene Arten durchgeführt werden.

- Computer können mithilfe von Servermetadaten bewertet werden, die von einer einfachen Azure Migrate-Appliance erfasst werden. Die Appliance ermittelt lokale Computer. Anschließend werden Computermetadaten und Leistungsdaten an Azure Migrate gesendet.
- Computer können mithilfe von Servermetadaten bewertet werden, die im CSV-Format (durch Trennzeichen getrennte Werte) importiert werden.

## <a name="how-do-i-assess-with-the-appliance"></a>Wie führe ich eine Bewertung mit der Appliance durch?

Wenn Sie eine Azure Migrate-Appliance bereitstellen, um lokale Server zu ermitteln, führen Sie die folgenden Schritte aus:

1. Richten Sie Azure und Ihre lokale Umgebung so ein, dass die Serverbewertung verwendet werden kann.
1. Erstellen Sie für Ihre erste Bewertung ein Azure-Projekt, und fügen Sie dem Projekt das Serverbewertungstool hinzu.
1. Stellen Sie eine einfache Azure Migrate-Appliance bereit. Die Appliance ermittelt kontinuierlich lokale Computer und sendet Metadaten und Leistungsdaten für diese Computer an Azure Migrate. Stellen Sie die Appliance als VM oder als physischen Computer bereit. Auf den Computern, die Sie bewerten möchten, müssen keine Komponenten installiert werden.

Wenn die Appliance mit der Ermittlung von Computern begonnen hat, können Sie die Computer, die Sie bewerten möchten, zu einer Gruppe hinzufügen und eine Bewertung für diese Gruppe mit dem Bewertungstyp **Azure VM** durchführen.

Führen Sie diese Schritte zum Testen des Verfahrens mit unseren Tutorials für [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md) oder [physische Server](./tutorial-discover-physical.md) aus.

## <a name="how-do-i-assess-with-imported-data"></a>Wie führe ich eine Bewertung mit importierten Daten durch?

Wenn Sie Server mithilfe einer CSV-Datei bewerten, benötigen Sie keine Appliance. Führen Sie stattdessen die folgenden Schritte aus:

1. Richten Sie Azure so ein, dass die Serverbewertung verwendet werden kann.
1. Erstellen Sie für Ihre erste Bewertung ein Azure-Projekt, und fügen Sie dem Projekt das Serverbewertungstool hinzu.
1. Laden Sie eine CSV-Vorlage herunter, und fügen Sie Serverdaten zu dieser Vorlage hinzu.
1. Importieren Sie die Vorlage in die Serverbewertung.
1. Ermitteln Sie die mit dem Import hinzugefügten Server, fassen Sie sie in einer Gruppe zusammen, und führen Sie eine Bewertung für die Gruppe mit dem Bewertungstyp **Azure VM** durch.

## <a name="what-data-does-the-appliance-collect"></a>Welche Daten werden von der Appliance erfasst?

Wenn Sie die Azure Migrate-Appliance für die Bewertung verwenden, erfahren Sie in den folgenden verlinkten Artikeln mehr über die Metadaten und Leistungsdaten, die für [VMware](migrate-appliance.md#collected-data---vmware) und [Hyper-V](migrate-appliance.md#collected-data---hyper-v) erfasst werden.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Wie berechnet die Appliance Leistungsdaten?

Wenn Sie die Appliance für die Ermittlung verwenden, führt die Appliance folgende Schritte zum Erfassen der Leistungsdaten für Computeeinstellungen aus:

1. Die Appliance erfasst einen Echtzeit-Abtastpunkt.

    - **VMware-VMs:** Ein Abtastpunkt wird alle 20 Sekunden erfasst.
    - **Virtuelle Hyper-V-Computer:** Ein Abtastpunkt wird alle 30 Sekunden erfasst.
    - **Physische Server**: Ein Abtastpunkt wird alle fünf Minuten erfasst.

1. Die Appliance kombiniert die Beispielpunkte so, dass bei VMware- und Hyper-V-Servern alle 10 Minuten und bei physischen Servern alle 5 Minuten ein einziger Datenpunkt erstellt wird. Zum Erstellen des Datenpunkts wählt die Appliance den Spitzenwert aus allen Stichproben aus. Anschließend sendet sie den Datenpunkt an Azure.
1. Die Serverbewertung speichert alle im zehnminütigen Intervall erfassten Datenpunkte des letzten Monats.
1. Beim Erstellen einer Bewertung ermittelt die Serverbewertung den geeigneten Datenpunkt, um die richtige Größe zu bestimmen. Die Ermittlung basiert auf dem Perzentilwert für den *Leistungsverlauf* und dem *Perzentilwert der Nutzung*.

    - Wenn der Leistungsverlauf beispielsweise auf eine Woche festgelegt ist und der Perzentilwert der Nutzung das 95. Perzentil ist, sortiert die Serverbewertung die 10-minütigen Abtastpunkte für die letzte Woche in aufsteigender Reihenfolge und wählt das 95. Perzentil zum Bestimmen der richtigen Größe aus.
    - Mit dem Wert des 95. Perzentils wird sichergestellt, dass Ausreißer ignoriert werden. Diese können enthalten sein, wenn Sie das 99. Perzentil wählen.
    - Falls Sie die Spitzenauslastung für den Zeitraum wählen und auch Ausreißer berücksichtigen möchten, sollten Sie das 99. Perzentil als Perzentilwert der Nutzung wählen.

1. Dieser Wert wird mit dem Komfortfaktor multipliziert, um die effektiven Leistungs- und Auslastungsdaten für diese Metriken zu erhalten, die von der Appliance erfasst werden:

    - CPU-Auslastung
    - RAM-Auslastung
    - Datenträger-IOPS (Lese- und Schreibvorgänge)
    - Datenträgerdurchsatz (Lese- und Schreibvorgänge)
    - Netzwerkdurchsatz (eingehend und ausgehend)

## <a name="how-are-azure-vm-assessments-calculated"></a>Wie werden Azure VM-Bewertungen berechnet?

Bei der Serverbewertung werden mithilfe von Metadaten und Leistungsdaten der lokalen Computer Bewertungen durchgeführt. Wenn Sie die Azure Migrate-Appliance bereitstellen, erfolgt die Bewertung anhand von Daten, die von der Appliance erfasst werden. Wenn Sie jedoch eine Bewertung anhand von Daten durchführen, die mithilfe einer CSV-Datei importiert wurden, stellen Sie die Metadaten für die Berechnung bereit.

Berechnungen erfolgen in den folgenden drei Phasen:

1. **Berechnen der Azure-Bereitschaft:** Bewerten, ob die Computer für die Migration zu Azure geeignet sind
1. **Berechnen der Größenempfehlungen:** Schätzen der Compute-, Speicher- und Netzwerkgröße
1. **Berechnen der monatlichen Kosten:** Berechnen der geschätzten monatlichen Compute- und Speicherkosten für die Ausführung der Computer in Azure nach der Migration

Berechnungen erfolgen in der obigen Reihenfolge. Ein Server gelangt erst dann in die nächste Phase, wenn die vorherige Phase abgeschlossen wurde. Wenn ein Server z. B. die Phase zur Bewertung der Azure-Bereitschaft nicht besteht, wird er als nicht geeignet für Azure gekennzeichnet. Für diesen Server werden keine Berechnungen zur Größe und zu den Kosten durchgeführt.

## <a name="whats-in-an-azure-vm-assessment"></a>Was umfasst eine Azure VM-Bewertung?

Eine Azure VM-Bewertung mit dem Serverbewertungstool umfasst Folgendes:

**Eigenschaft** | **Details**
--- | ---
**Zielstandort** | Der Standort, zu dem die Migration durchgeführt werden soll. Die Serverbewertung unterstützt derzeit die folgenden Azure-Zielregionen:<br/><br/> „Australien, Osten“, „Australien, Südosten“, „Brasilien, Süden“, „Kanada, Mitte“, „Kanada, Osten“, „Indien, Mitte“, „USA, Mitte“, „China, Osten“, „China, Norden“, „Asien, Osten“, „USA, Osten“, „USA, Osten 2“„Deutschland, Mitte“, „Deutschland, Nordosten“, „Japan, Osten“, „Japan, Westen“, „Korea, Mitte“, „Korea, Süden“, „USA, Norden-Mitte“, „Europa, Norden“, „USA, Süden-Mitte“, „Asien, Südosten“, „Indien, Süden“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „US Gov Arizona“, „US Gov Texas“, „US Gov Virginia“, „USA, Westen-Mitte“, „Europa, Westen“, „Indien, Westen“, „USA, Westen“ und „USA, Westen 2“.
**Zielspeicherdatenträger (übernommene Größe)** | Der Typ der Datenträger, die für die Speicherung in Azure verwendet werden sollen. <br/><br/> Geben Sie den Zielspeicherdatenträger als verwalteten Premium-, verwalteten SSD Standard- oder verwalteten HDD Standard-Datenträger an.
**Zielspeicherdatenträger (leistungsbasierte Größenanpassung)** | Geben Sie den Zielspeicherdatenträger als automatischen, verwalteten Premium-, verwalteten HDD Standard- oder verwalteten SSD Standard-Datenträger an.<br/><br/> **Automatisch**: Die Datenträgerempfehlung erfolgt basierend auf den Leistungsdaten der Datenträger (IOPS und Durchsatz).<br/><br/>**Premium oder Standard**:  Für die Bewertung wird eine Datenträger-SKU innerhalb des ausgewählten Speichertyps empfohlen.<br/><br/> Wenn Sie eine Einzelinstanz-VM-SLA (Vereinbarung zum Servicelevel) von 99,9 % erreichen möchten, sollten Sie verwaltete Premium-Datenträger in Betracht ziehen. Dadurch wird sichergestellt, dass für alle Datenträger in der Bewertung verwaltete Premium-Datenträger empfohlen werden.<br/><br/> Azure Migrate unterstützt für die Migrationsbewertung ausschließlich verwaltete Datenträger.
**Azure Reserved VM Instances** | Geben Sie [reservierte Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) an, damit diese bei Kostenschätzungen in der Bewertung berücksichtigt werden.<br/><br/> Wenn Sie „Reservierte Instanzen“ auswählen, sind die „Rabatt (%)“- und „VM-Betriebszeit“-Eigenschaften nicht anwendbar.<br/><br/> Azure Migrate unterstützt Azure Reserved VM Instances derzeit nur bei Angeboten mit nutzungsbasierter Bezahlung.
**Größenkriterien** | Für die Anpassung der Azure-VM-Größe.<br/><br/> Sie können die Größe übernehmen oder die leistungsbasierte Größenanpassung verwenden.
**Leistungsverlauf** | Wird bei der leistungsbasierten Größenanpassung verwendet. Der Leistungsverlauf gibt den Zeitraum für die Bewertung von Leistungsdaten an.
**Perzentilwert der Nutzung** | Wird bei der leistungsbasierten Größenanpassung verwendet. Der Perzentilwert der Nutzung gibt den für die Größenanpassung zu verwendenden Perzentilwert der Leistungsstichprobe an.
**VM-Serie** | Die Azure-VM-Serie, die für die Größenanpassung berücksichtigt werden soll. Wenn Sie beispielsweise in der Produktionsumgebung keine virtuellen Computer der A-Serie in Azure benötigen, können Sie die A-Serie aus der Liste der Serien ausschließen.
**Komfortfaktor** | Der bei der Bewertung verwendete Puffer. Er wird auf die CPU-, RAM-, Datenträger- und Netzwerkdaten für VMs angewendet. Er berücksichtigt Aspekte wie saisonale Nutzung, einen kurzen Leistungsverlauf und eine voraussichtliche Zunahme der zukünftigen Nutzung.<br/><br/> Beispiel: Für einen virtuellen Computer mit zehn Kernen und einer Auslastung von 20 % ergibt sich normalerweise ein virtueller Computer mit zwei Kernen. Bei einem Komfortfaktor von 2.0 ist das Ergebnis dagegen ein virtueller Computer mit vier Kernen.
**Angebot** | Das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/), für das Sie registriert sind. Bei der Serverbewertung werden die Kosten für dieses Angebot geschätzt.
**Währung** | Die Rechnungswährung für Ihr Konto.
**Rabatt (%)** | Abonnementspezifische Rabatte, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %.
**VM-Betriebszeit** | Die Dauer in Tagen pro Monat sowie Stunden pro Tag für Azure-VMs, die nicht dauerhaft ausgeführt werden. Die Kostenschätzungen basieren auf dieser Dauer.<br/><br/> Die Standardwerte sind 31 Tage pro Monat und 24 Stunden pro Tag.
**Azure-Hybridvorteil** | Gibt an, ob Sie über Software Assurance verfügen und den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) nutzen können. Wenn für diese Einstellung der Standardwert „Ja“ festgelegt ist, werden für Windows-VMs die Azure-Preise für andere Betriebssysteme als Windows berücksichtigt.
**EA-Abonnement** | Mit dieser Eigenschaft wird festgelegt, dass ein EA-Abonnement (Enterprise Agreement) für die Kostenschätzung verwendet wird. Dabei wird der Rabatt berücksichtigt, der für das Abonnement gilt. <br/><br/> Behalten Sie die Standardeinstellungen für die Eigenschaften „reservierte Instanzen“, „Rabatt (%)“ und „VM-Betriebszeit“ bei.


Sehen Sie sich die [bewährten Methoden](best-practices-assessment.md) für die Erstellung einer Bewertung mit der Serverbewertung an.

## <a name="calculate-readiness"></a>Berechnen der Bereitschaft

Nicht alle Computer sind für die Ausführung in Azure geeignet. Bei einer Azure VM-Bewertung werden alle lokalen Computer bewertet und einer Bereitschaftskategorie zugewiesen.

- **Bereit für Azure**: Der Computer kann ohne Änderungen zu Azure migriert werden. Er wird in Azure mit vollständiger Azure-Unterstützung gestartet.
- **Bedingt bereit für Azure:** Der Computer kann ggf. in Azure gestartet werden, verfügt jedoch möglicherweise nicht über vollständigen Azure-Support. Beispielsweise unterstützt Azure keine Computer, auf denen eine ältere Version von Windows Server ausgeführt wird. Gehen Sie vor der Migration dieser Computer zu Azure vorsichtig vor. Befolgen Sie die in der Bewertung empfohlene Korrekturanleitung, um Bereitschaftsprobleme zu beheben.
- **Nicht bereit für Azure**: Der Computer wird in Azure nicht gestartet. Wenn auf dem Datenträger eines lokalen Computers z. B. mehr als 64 TB Daten gespeichert sind, kann dieser Computer nicht in Azure gehostet werden. Befolgen Sie vor der Migration die Korrekturanleitung, um das Problem zu beheben.
- **Bereitschaft unbekannt**: Azure Migrate kann die Bereitschaft des Computers aufgrund unzureichender Metadaten nicht ermitteln.

Zur Berechnung der Bereitschaft werden bei der Serverbewertung die in den folgenden Tabellen aufgeführten Computereigenschaften und Betriebssystemeinstellungen überprüft.

### <a name="machine-properties"></a>Computereigenschaften

Für eine Azure VM-Bewertung überprüft die Serverbewertung die folgenden Eigenschaften eines lokalen virtuellen Computers, um zu ermitteln, ob dieser auf virtuellen Azure-Computern ausgeführt werden kann.

Eigenschaft | Details | Azure-Bereitschaftsstatus
--- | --- | ---
**Starttyp** | Azure unterstützt virtuelle Computer mit dem Starttyp BIOS, jedoch nicht UEFI. | Bedingt bereit, wenn der Starttyp UEFI ist
**Kerne** | Pro Computer sind maximal 128 Kerne zulässig. Dies ist die maximale Anzahl, die von einer Azure-VM unterstützt wird.<br/><br/> Wenn der Leistungsverlauf verfügbar ist, berücksichtigt Azure Migrate die genutzten Kerne beim Vergleich. Wenn in den Bewertungseinstellungen ein Komfortfaktor festgelegt ist, wird die Anzahl von genutzten Kernen mit diesem Komfortfaktor multipliziert.<br/><br/> Wenn kein Leistungsverlauf vorhanden ist, verwendet Azure Migrate die zugeordneten Kerne zur Anwendung des Komfortfaktors. | Bereit, wenn die Anzahl von Kernen innerhalb des Limits liegt
**RAM** | Pro Computer sind maximal 3.892 GB RAM zulässig. Dies ist die maximale Größe, die von einer Azure-VM der M-Serie vom Typ Standard_M128m&nbsp;<sup>2</sup> unterstützt wird. [Weitere Informationen](../virtual-machines/sizes.md)<br/><br/> Wenn der Leistungsverlauf verfügbar ist, berücksichtigt Azure Migrate den genutzten RAM zum Vergleich. Wenn ein Komfortfaktor festgelegt ist, wird der genutzte RAM mit dem Komfortfaktor multipliziert.<br/><br/> Wenn kein Verlauf vorhanden ist, wird der zugeordnete RAM verwendet, um einen Komfortfaktor anzuwenden.<br/><br/> | Bereit, wenn die RAM-Größe innerhalb des Limits liegt
**Speicherdatenträger** | Die zugeordnete Größe eines Datenträgers darf maximal 32 TB betragen. Wenngleich in Azure 64-TB-Datenträger mit Azure SSD Ultra-Datenträgern unterstützt werden, überprüft die Serverbewertung von Azure Migrate derzeit die Datenträgergröße auf Überschreitung von 32 TB, da sie SSD Ultra aktuell noch nicht unterstützt. <br/><br/> Pro Computer dürfen einschließlich des Betriebssystemdatenträgers höchstens 65 Datenträger angefügt sein. | Bereit, wenn die Größe und Anzahl von Datenträgern innerhalb der Limits liegen
**Netzwerk** | Pro Computer dürfen maximal 32 Netzwerkschnittstellen (NICs) angefügt sein. | Bereit, wenn die Anzahl von NICs innerhalb des Limits liegt

### <a name="guest-operating-system"></a>Gastbetriebssystem

Für eine Azure VM-Bewertung untersucht die Serverbewertung neben den VM-Eigenschaften auch das Gastbetriebssystem eines Computers, um zu ermitteln, ob es in Azure ausgeführt werden kann.

> [!NOTE]
> Für die Gastbetriebssystemanalyse bei VMware-VMs verwendet die Serverbewertung das in vCenter Server für die VM angegebene Betriebssystem. vCenter Server stellt jedoch nicht die Kernelversion für Linux-VM-Betriebssysteme zur Verfügung. Zum Ermitteln der Version müssen Sie die [Anwendungsermittlung](./how-to-discover-applications.md) einrichten. Dann ermittelt die Appliance Versionsinformationen anhand der Gastanmeldeinformationen, die Sie beim Einrichten der App-Ermittlung angeben.


Die Serverbewertung verwendet die folgende Logik, um die Azure-Bereitschaft basierend auf dem Betriebssystem zu ermitteln:

**Betriebssystem** | **Details** | **Azure-Bereitschaftsstatus**
--- | --- | ---
Windows Server 2016 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure.
Windows Server 2012 R2 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure.
Windows Server 2012 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure.
Windows Server 2008 R2 mit allen SPs | Azure bietet vollständige Unterstützung.| Bereit für Azure.
Windows Server 2008 (32-Bit und 64-Bit) | Azure bietet vollständige Unterstützung. | Bereit für Azure.
Windows Server 2003 und Windows Server 2003 R2 | Der Unterstützungszeitraum für diese Betriebssysteme ist abgelaufen. Für die Unterstützung in Azure wird eine [benutzerdefinierte Supportvereinbarung](/troubleshoot/azure/virtual-machines/server-software-support) (Custom Support Agreement, CSA) benötigt. | Bedingt bereit für Azure. Ziehen Sie vor der Migration zu Azure ein Upgrade des Betriebssystems in Erwägung.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 und MS-DOS | Der Unterstützungszeitraum für diese Betriebssysteme ist abgelaufen. Der Computer startet möglicherweise in Azure, aber Azure stellt keine Betriebssystemunterstützung bereit. | Bedingt bereit für Azure. Es wird empfohlen, vor der Migration zu Azure ein Upgrade des Betriebssystems auszuführen.
Windows 7, Windows 8 und Windows 10 | Azure bietet [nur mit einem Visual Studio-Abonnement](../virtual-machines/windows/client-images.md) Unterstützung. | Bedingt bereit für Azure.
Windows 10 Pro | Azure bietet Unterstützung mit [mehrinstanzenfähigen Hostingrechten](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md). | Bedingt bereit für Azure.
Windows Vista und Windows XP Professional | Der Unterstützungszeitraum für diese Betriebssysteme ist abgelaufen. Der Computer startet möglicherweise in Azure, aber Azure stellt keine Betriebssystemunterstützung bereit. | Bedingt bereit für Azure. Es wird empfohlen, vor der Migration zu Azure ein Upgrade des Betriebssystems auszuführen.
Linux | Azure unterstützt diese [Linux-Betriebssysteme](../virtual-machines/linux/endorsed-distros.md). Andere Linux-Betriebssysteme werden möglicherweise ebenfalls in Azure gestartet. Vor der Migration zu Azure wird jedoch ein Upgrade auf eine unterstützte Betriebssystemversion empfohlen. | Bereit für Azure, wenn die Version unterstützt wird.<br/><br/>Bedingt bereit, wenn die Version nicht unterstützt wird.
Andere Betriebssysteme wie Oracle Solaris, Apple macOS und FreeBSD | Azure unterstützt diese Betriebssysteme nicht. Der Computer startet möglicherweise in Azure, aber Azure stellt keine Betriebssystemunterstützung bereit. | Bedingt bereit für Azure. Es wird empfohlen, vor der Migration zu Azure ein unterstütztes Betriebssystem zu installieren.  
In vCenter Server als **Sonstige** angegebenes Betriebssystem | In diesem Fall kann Azure Migrate das Betriebssystem nicht identifizieren. | Bereitschaft unbekannt. Stellen Sie sicher, dass Azure das auf dem virtuellen Computer ausgeführte Betriebssystem unterstützt.
32-Bit-Betriebssysteme | Der Computer kann in Azure gestartet werden, Azure bietet jedoch möglicherweise keine vollständige Unterstützung. | Bedingt bereit für Azure. Ziehen Sie vor der Migration zu Azure ein Upgrade auf ein 64-Bit-Betriebssystems in Erwägung.

## <a name="calculating-sizing"></a>Berechnen der Größe

Nachdem ein Computer als bereit für Azure markiert wurde, werden bei der Serverbewertung Größenempfehlungen in der Azure VM-Bewertung angegeben. Diese Empfehlungen beziehen sich auf die Azure-VM und die Datenträger-SKU. Die Berechnung der Größe hängt davon ab, ob Sie die lokale Größe übernehmen oder eine leistungsbasierte Größenanpassung verwenden.

### <a name="calculate-sizing-as-is-on-premises"></a>Berechnen der Größe (wie in der lokalen Umgebung)

 Wenn Sie die lokale Größe übernehmen, wird der Leistungsverlauf der virtuellen Computer und Datenträger bei der Serverbewertung in der Azure VM-Bewertung nicht berücksichtigt.

- **Computegröße**: Das Serverbewertungstool weist basierend auf der lokal zugeordneten Größe eine Azure-VM-SKU zu.
- **Speicher-/Datenträgergröße**: Bei der Serverbewertung wird der Speichertyp berücksichtigt, der in den Bewertungseigenschaften angegeben wurde, und ein entsprechender Datenträgertyp empfohlen. Mögliche Speichertypen sind HDD Standard, SSD Standard und Premium. Der Standardspeichertyp ist „Premium“.
- **Netzwerkgröße**: Bei der Serverbewertung wird der Netzwerkadapter auf dem lokalen Computer berücksichtigt.

### <a name="calculate-sizing-performance-based"></a>Berechnen der Größe (leistungsbasierte Anpassung)

Bei Verwendung der leistungsbasierten Größenanpassung in einer Azure VM-Bewertung werden Größenempfehlungen wie folgt ermittelt:

- Bei der Serverbewertung wird der Leistungsverlauf des Computers berücksichtigt, um die VM-Größe und den Datenträgertyp in Azure zu ermitteln.
- Wenn Server mithilfe einer CSV-Datei importiert wurden, werden die von Ihnen angegebenen Werte verwendet. Diese Methode ist besonders nützlich, wenn Sie für den lokalen Computer eine übermäßige Zuordnung vorgenommen haben, die Auslastung jedoch gering ist, und Sie die Größe des virtuellen Azure-Computers anpassen möchten, um Kosten zu sparen.
- Wenn Sie die Leistungsdaten nicht verwenden möchten, setzen Sie die Größenkriterien auf die lokale Größe zurück (siehe vorheriger Abschnitt).

#### <a name="calculate-storage-sizing"></a>Berechnen der Speichergröße

Bei der Speichergröße in einer Azure VM-Bewertung versucht Azure Migrate, jeden an den Computer angefügten Datenträger einem Datenträger in Azure zuzuordnen. Dies funktioniert wie folgt:

1. Die Serverbewertung addiert die Lese- und Schreib-IOPS eines Datenträgers, um die gesamten benötigten IOPS zu ermitteln. Ebenso werden die Werte für Lese- und Schreibdurchsatz addiert, um den gesamten Durchsatz jedes Datenträgers zu erhalten. Bei importbasierten Bewertungen haben Sie die Möglichkeit, die gesamten IOPS, den Gesamtdurchsatz und die Gesamtanzahl von Datenträgern in der importierten Datei ohne Angabe einzelner Datenträgereinstellungen anzugeben. In diesem Fall wird die individuelle Datenträgergrößenanpassung übersprungen und die gelieferten Daten werden direkt zur Berechnung der Größenanpassung und zur Auswahl einer geeigneten VM-SKU verwendet.

1. Wenn Sie für den Speichertyp die Option „Automatisch“ festgelegt haben, basiert der ausgewählte Typ auf den effektiven IOPS- und Durchsatzwerten. Das Serverbewertungstool bestimmt, ob der Datenträger einem HDD Standard-, SSD Standard- oder Premium-Datenträger in Azure zugeordnet werden soll. Wenn der Speichertyp auf einen dieser Datenträgertypen festgelegt wurde, wird versucht, eine Datenträger-SKU innerhalb des ausgewählten Speichertyps zu finden.
1. Die Datenträger werden wie folgt ausgewählt:
    - Wenn die Serverbewertung keinen Datenträger mit den erforderlichen IOPS- und Durchsatzwerten finden kann, wird der Computer als ungeeignet für Azure gekennzeichnet.
    - Wenn die Serverbewertung mehrere geeignete Datenträger findet, wählt sie diejenigen aus, die den in den Bewertungseinstellungen angegebenen Speicherort unterstützen.
    - Falls mehrere geeignete Datenträger vorhanden sind, wählt die Serverbewertung den mit den geringsten Kosten aus.
    - Wenn für einen Datenträger keine Leistungsdaten verfügbar sind, wird anhand der Datenträgergröße innerhalb der Konfiguration nach einem SSD Standard-Datenträger in Azure gesucht.

#### <a name="calculate-network-sizing"></a>Berechnen der Netzwerkgröße

Für eine Azure VM-Bewertung versucht die Serverbewertung, einen virtuellen Azure-Computer zu finden, der die Anzahl der an den lokalen Computer angefügten Netzwerkadapter sowie deren erforderliche Leistung unterstützt.

- Um die effektive Netzwerkleistung des lokalen virtuellen Computers zu ermitteln, aggregiert die Serverbewertung die ausgehende Datenübertragungsrate des Computers (ausgehende Netzwerkrate) aller Netzwerkadapter. Anschließend wird der Komfortfaktor angewendet. Anhand des resultierenden Werts wird nach einem virtuellen Azure-Computer gesucht, der die erforderliche Netzwerkleistung unterstützen kann.
- Neben der Netzwerkleistung prüft die Serverbewertung zudem, ob der virtuelle Azure-Computer die erforderliche Anzahl von Netzwerkadaptern unterstützen kann.
- Wenn keine Daten zur Netzwerkleistung verfügbar sind, berücksichtigt die Serverbewertung bei der Größenanpassung für virtuelle Computer lediglich die Anzahl von Netzwerkadaptern.

#### <a name="calculate-compute-sizing"></a>Berechnen der Computegröße

Nach der Berechnung der Speicher- und Netzwerkanforderungen prüft die Serverbewertung die CPU- und RAM-Anforderungen, um eine geeignete VM-Größe in Azure zu ermitteln.

- Azure Migrate betrachtet die effektiv genutzten Kerne und den RAM, um eine geeignete Größe der Azure-VMs zu bestimmen.
- Wenn keine geeignete Größe gefunden wird, wird der Computer als ungeeignet für Azure gekennzeichnet.
- Wenn eine geeignete Größe gefunden wird, wendet Azure Migrate die Speicher -und Netzwerkberechnungen an. Für die endgültige VM-Größenempfehlung werden dann noch Standort- und Tarifeinstellungen angewandt.
- Wenn mehrere geeignete Azure-VM-Größen vorhanden sind, wird die mit den geringsten Kosten empfohlen.

## <a name="confidence-ratings-performance-based"></a>Zuverlässigkeitsstufen (leistungsbasiert)

Jede leistungsbasierte Azure VM-Bewertung in Azure Migrate ist einer Zuverlässigkeitsstufe zugeordnet. Die Bewertung reicht von einem Stern (niedrigste Zuverlässigkeit) bis fünf Sterne (höchste Zuverlässigkeit). Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von Azure Migrate bereitgestellten Größenempfehlungen besser einschätzen.

- Die Zuverlässigkeitsstufe wird einer Bewertung zugewiesen. Sie basiert auf der Verfügbarkeit von Datenpunkten, die zum Berechnen der Bewertung erforderlich sind.
- Die Serverbewertung benötigt für die leistungsbasierte Dimensionierung die folgenden Informationen:
    - Die Auslastungsdaten für CPU und VM-RAM.
    - IOPS- und Durchsatzdaten für jeden an den virtuellen Computer angefügten Datenträger
    - Informationen zur Netzwerk-E/A für die leistungsbasierte Größenanpassung für jeden Netzwerkadapter, der an einen virtuellen Computer angefügt ist

Steht eine dieser Nutzungsangaben nicht zur Verfügung, sind die Größenempfehlungen möglicherweise nicht zuverlässig.

> [!NOTE]
> Servern, die anhand von importierten CSV-Dateien bewertet werden, werden keine Zuverlässigkeitsstufen zugewiesen. Die Bewertung gilt nicht für Bewertungen mit Übernahme der lokalen Größe.

### <a name="ratings"></a>Ratings

Die nachfolgende Tabelle zeigt die Zuverlässigkeitsstufen einer Bewertung, die vom Prozentsatz der verfügbaren Datenpunkte abhängen:

   **Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
   --- | ---
   0–20 % | 1 Stern
   21–40 % | 2 Sterne
   41–60 % | 3 Sterne
   61–80 % | 4 Sterne
   81–100 % | 5 Sterne

### <a name="low-confidence-ratings"></a>Niedrige Zuverlässigkeitsstufen

Einige mögliche Gründe für eine niedrige Zuverlässigkeitsstufe einer Bewertung:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil der Umgebung erstellt. Wenn Sie die Bewertung z. B. mit einer auf einen Tag festgelegten Leistungsdauer erstellen, müssen Sie bis mindestens einen Tag nach dem Start der Ermittlung warten, bis alle Datenpunkte gesammelt sind.
- Einige virtuelle Computer wurden während des Zeitraums, für den die Bewertung berechnet wird, heruntergefahren. Wenn einige VMs für eine gewisse Zeit heruntergefahren wurden, kann die Serverbewertung für diesen Zeitraum keine Leistungsdaten sammeln.
- Einige virtuelle Computer wurden während des Zeitraums, für den die Bewertung berechnet wird, erstellt. Beispiel: Sie haben eine Bewertung für den Leistungsverlauf im letzten Monat erstellt, einige VMs wurden jedoch erst vor einer Woche erstellt. Bei den neuen VMs ist in diesem Fall kein Leistungsverlauf für den gesamten Zeitraum verfügbar.

> [!NOTE]
> Bei einer Zuverlässigkeitsstufe von weniger als fünf Sternen wird empfohlen, mindestens einen Tag zu warten, damit die Appliance ein Profil der Umgebung erstellen kann. Führen Sie dann eine Neuberechnung der Bewertung durch. Anderenfalls ist die leistungsbasierte Größenanpassung möglicherweise unzuverlässig. In diesem Fall wird empfohlen, die Bewertung auf die Größenanpassung „Wie lokal“ zu ändern.

## <a name="calculate-monthly-costs"></a>Berechnen der monatlichen Kosten

Nachdem die Größenempfehlungen ermittelt wurden, berechnet eine Azure VM-Bewertung in Azure Migrate die Compute- und Speicherkosten nach der Migration.

- **Computekosten**: Azure Migrate berechnet anhand der empfohlenen Azure-VM-Größe und mithilfe der Azure-Abrechnungs-API die monatlichen Kosten für den virtuellen Computer.

    Bei dieser Berechnung wird Folgendes berücksichtigt:
    - Betriebssystem
    - Software Assurance
    - Reservierte Instanzen
    - VM-Betriebszeit
    - Standort
    - Währungseinstellungen

    Das Serverbewertungstool fasst die Kosten aller Computer zusammen, um die monatlichen Gesamtcomputekosten zu berechnen.

- **Speicherkosten**: Die monatlichen Speicherkosten für einen Computer werden berechnet, indem die monatlichen Kosten aller an den Computer angefügten Datenträger zusammengefasst werden.

    Die Serverbewertung berechnet die gesamten monatlichen Speicherkosten durch Aggregieren der Speicherkosten aller Computer. Derzeit werden bei der Berechnung keine Angebote berücksichtigt, die in den Bewertungseinstellungen angegeben sind.

Kosten werden in der Währung angezeigt, die in den Bewertungseinstellungen festgelegt ist.

## <a name="next-steps"></a>Nächste Schritte

[Bewährte Methoden für die Erstellung von Bewertungen](best-practices-assessment.md) 

- Erfahren Sie etwas über das Ausführen von Bewertungen für [virtuelle VMware-Computer](./tutorial-discover-vmware.md), [virtuelle Hyper-V-Computer](./tutorial-discover-hyper-v.md) und [physische Server](./tutorial-discover-physical.md).
- Erfahren Sie etwas über die Bewertung von [mithilfe einer CSV-Datei importierten](./tutorial-discover-import.md) Servern.
- Erfahren Sie etwas über das Einrichten der [Visualisierung von Abhängigkeiten](concepts-dependency-visualization.md).