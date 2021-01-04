---
title: AVS-Bewertungsberechnungen in Azure Migrate | Microsoft-Dokumentation
description: Bietet eine Übersicht über AVS-Bewertungsberechnungen im Azure Migrate-Dienst.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 67d4137a21753b221e17a1effde35bc1b89600d3
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753806"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>Serverbewertungsübersicht (Migration zu Azure VMware Solution)

[Azure Migrate](migrate-services-overview.md) bietet einen zentralen Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration von lokalen Apps und Workloads. Außerdem werden Ihre privaten und öffentlichen Cloudinstanzen in Azure nachverfolgt. Der Hub stellt Azure Migrate-Tools für die Bewertung und Migration sowie Angebote von unabhängigen Drittanbietern (Independent Software Vendors, ISVs) bereit.

Die Serverbewertung ist ein Tool in Azure Migrate, das lokale Server für die Migration zu virtuellen Azure IaaS-Computern und Azure VMware Solution (AVS) bewertet. Dieser Artikel enthält Informationen dazu, wie AVS-Bewertungen (Azure VMware Solution) berechnet werden.

> [!NOTE]
> Die AVS-Bewertung (Azure VMware Solution) befindet sich derzeit in der Vorschauversion und kann nur für virtuelle VMware-Computer erstellt werden.

## <a name="types-of-assessments"></a>Arten von Bewertungen

Bewertungen, die Sie mit der Serverbewertung erstellen, sind Momentaufnahmen von Daten zu einem bestimmten Zeitpunkt. Es gibt zwei Arten von Bewertungen, die Sie mit der Azure Server Assessment“ (Azure Migrate-Serverbewertung) erstellen.

**Bewertungstyp** | **Details**
--- | --- 
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md), [Hyper-V-VMs](how-to-set-up-appliance-hyper-v.md) und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

Die AVS-Bewertung (Azure VMware Solution) in der Serverbewertung bietet zwei Optionen für Größenkriterien:

**Bewertung** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen basierend auf gesammelten Leistungsdaten von lokalen virtuellen Computern. | **Empfohlene Knotengröße**: Basierend auf den Daten zur CPU- und Arbeitsspeicherauslastung zusammen mit dem Knotentyp, dem Speichertyp und der FTT-Einstellung, die Sie für die Bewertung auswählen.
**Wie lokal** | Bewertungen basierend auf lokaler Größenanpassung | **Empfohlene Knotengröße**: Basierend auf der lokalen VM-Größe zusammen mit dem Knotentyp, dem Speichertyp und der FTT-Einstellung, die Sie für die Bewertung auswählen.

## <a name="how-do-i-run-an-assessment"></a>Ausführen einer Bewertung

Eine Bewertung kann auf verschiedene Arten durchgeführt werden.

- Computer können mithilfe von Servermetadaten bewertet werden, die von einer einfachen Azure Migrate-Appliance erfasst werden. Die Appliance ermittelt lokale Computer. Anschließend werden Computermetadaten und Leistungsdaten an Azure Migrate gesendet.
- Computer können mithilfe von Servermetadaten bewertet werden, die im CSV-Format (durch Trennzeichen getrennte Werte) importiert werden.

## <a name="how-do-i-assess-with-the-appliance"></a>Wie führe ich eine Bewertung mit der Appliance durch?

Wenn Sie eine Azure Migrate-Appliance bereitstellen, um lokale Server zu ermitteln, führen Sie die folgenden Schritte aus:

1. Richten Sie Azure und Ihre lokale Umgebung so ein, dass die Serverbewertung verwendet werden kann.
2. Erstellen Sie für Ihre erste Bewertung ein Azure-Projekt, und fügen Sie dem Projekt das Serverbewertungstool hinzu.
3. Stellen Sie eine einfache Azure Migrate-Appliance bereit. Die Appliance ermittelt kontinuierlich lokale Computer und sendet Metadaten und Leistungsdaten für diese Computer an Azure Migrate. Stellen Sie die Appliance als virtuellen Computer bereit. Auf den Computern, die Sie bewerten möchten, müssen keine Komponenten installiert werden.

Wenn die Appliance mit der Ermittlung von Computern begonnen hat, können Sie die Computer, die Sie bewerten möchten, zu einer Gruppe hinzufügen und eine Bewertung für diese Gruppe mit dem Bewertungstyp **Azure VMware Solution (AVS)** durchführen.

Erstellen Sie Ihre erste AVS-Bewertung (Azure VMware Solution), indem Sie den Schritten [hier](how-to-create-azure-vmware-solution-assessment.md) folgen.

## <a name="how-do-i-assess-with-imported-data"></a>Wie führe ich eine Bewertung mit importierten Daten durch?

Wenn Sie Server mithilfe einer CSV-Datei bewerten, benötigen Sie keine Appliance. Führen Sie stattdessen die folgenden Schritte aus:

1. Richten Sie Azure so ein, dass die Serverbewertung verwendet werden kann.
2. Erstellen Sie für Ihre erste Bewertung ein Azure-Projekt, und fügen Sie dem Projekt das Serverbewertungstool hinzu.
3. Laden Sie eine CSV-Vorlage herunter, und fügen Sie Serverdaten zu dieser Vorlage hinzu.
4. Importieren Sie die Vorlage in die Serverbewertung.
5. Ermitteln Sie die mit dem Import hinzugefügten Server, fassen Sie sie in einer Gruppe zusammen, und führen Sie eine Bewertung für die Gruppe mit dem Bewertungstyp **Azure VMware Solution (AVS)** durch.

## <a name="what-data-does-the-appliance-collect"></a>Welche Daten werden von der Appliance erfasst?

Wenn Sie die Azure Migrate-Appliance für die Bewertung verwenden, erfahren Sie in den folgenden verlinkten Artikeln mehr über die Metadaten und Leistungsdaten, die für [VMware](migrate-appliance.md#collected-data---vmware) erfasst werden.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Wie berechnet die Appliance Leistungsdaten?

Wenn Sie die Appliance für die Ermittlung verwenden, führt die Appliance folgende Schritte zum Erfassen der Leistungsdaten für Computeeinstellungen aus:

1. Die Appliance erfasst einen Echtzeit-Abtastpunkt.

    - **VMware-VMs:** Ein Abtastpunkt wird alle 20 Sekunden erfasst.

2. Die Appliance fasst die Abtastpunkte alle zehn Minuten zu einem einzigen Abtastpunkt zusammen. Zum Erstellen des Datenpunkts wählt die Appliance den Spitzenwert aus allen Stichproben aus. Anschließend sendet sie den Datenpunkt an Azure.
3. Die Serverbewertung speichert alle im zehnminütigen Intervall erfassten Datenpunkte des letzten Monats.
4. Beim Erstellen einer Bewertung ermittelt die Serverbewertung den geeigneten Datenpunkt, um die richtige Größe zu bestimmen. Die Ermittlung basiert auf dem Perzentilwert für den *Leistungsverlauf* und dem *Perzentilwert der Nutzung*.

    - Wenn der Leistungsverlauf beispielsweise auf eine Woche festgelegt ist und der Perzentilwert der Nutzung das 95. Perzentil ist, sortiert die Serverbewertung die 10-minütigen Abtastpunkte für die letzte Woche in aufsteigender Reihenfolge und wählt das 95. Perzentil zum Bestimmen der richtigen Größe aus.
    - Mit dem Wert des 95. Perzentils wird sichergestellt, dass Ausreißer ignoriert werden. Diese können enthalten sein, wenn Sie das 99. Perzentil wählen.
    - Falls Sie die Spitzenauslastung für den Zeitraum wählen und auch Ausreißer berücksichtigen möchten, sollten Sie das 99. Perzentil als Perzentilwert der Nutzung wählen.

5. Dieser Wert wird mit dem Komfortfaktor multipliziert, um die effektiven Leistungs- und Auslastungsdaten für diese Metriken zu erhalten, die von der Appliance erfasst werden:

    - CPU-Auslastung
    - RAM-Auslastung
    - Datenträger-IOPS (Lese- und Schreibvorgänge)
    - Datenträgerdurchsatz (Lese- und Schreibvorgänge)
    - Netzwerkdurchsatz (eingehend und ausgehend)

Die folgenden Leistungsdaten werden gesammelt, aber nicht für Größenempfehlungen für AVS-Bewertungen verwendet:
  - IOPS- und Durchsatzdaten für jeden an den virtuellen Computer angefügten Datenträger
  - Informationen zur Netzwerk-E/A für die leistungsbasierte Größenanpassung für jeden Netzwerkadapter, der an einen virtuellen Computer angefügt ist

## <a name="how-are-avs-assessments-calculated"></a>Wie werden AVS-Bewertungen berechnet?

Bei der Serverbewertung werden mithilfe von Metadaten und Leistungsdaten der lokalen Computer Bewertungen durchgeführt. Wenn Sie die Azure Migrate-Appliance bereitstellen, erfolgt die Bewertung anhand von Daten, die von der Appliance erfasst werden. Wenn Sie jedoch eine Bewertung anhand von Daten durchführen, die mithilfe einer CSV-Datei importiert wurden, stellen Sie die Metadaten für die Berechnung bereit.

Berechnungen erfolgen in den folgenden drei Phasen:

1. **Berechnen der AVS-Bereitschaft (Azure VMware Solution)** : Ermittelt, ob die lokalen virtuellen Computer für die Migration zu Azure VMware Solution (AVS) geeignet sind.
2. **Berechnen der Anzahl von AVS-Knoten und der knotenübergreifenden Auslastung**: Geschätzte Anzahl der AVS-Knoten, die für die Ausführung der virtuellen Computer erforderlich sind, und prognostizierte CPU-, Arbeitsspeicher- und Speicherauslastung über alle Knoten hinweg.
3. **Geschätzte monatliche Kosten**: Die geschätzten monatlichen Kosten für alle AVS-Knoten (Azure VMware Solution), auf denen die lokalen virtuellen Computer ausgeführt werden.

Berechnungen erfolgen in der obigen Reihenfolge. Ein Server gelangt erst dann in die nächste Phase, wenn die vorherige Phase abgeschlossen wurde. Wenn ein Server z. B. die Phase zur Bewertung der AVS-Bereitschaft nicht besteht, wird er als nicht geeignet für Azure gekennzeichnet. Für diesen Server werden keine Berechnungen zur Größe und zu den Kosten durchgeführt.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Was ist eine Bewertung vom Typ „Azure VMware Solution (AVS)“?

Eine AVS-Bewertung mit dem Serverbewertungstool umfasst Folgendes:


| **Eigenschaft** | **Details** 
| - | - 
| **Zielstandort** | Gibt den Standort der privaten AVS-Cloud an, zu der Sie migrieren möchten.<br/><br/> Die AVS-Bewertung der Serverbewertung unterstützt derzeit die folgenden Zielregionen: „USA, Osten“, „Europa, Westen“, „USA, Westen“. 
| **Speichertyp** | Gibt die Speicher-Engine an, die in AVS verwendet werden soll.<br/><br/> Die AVS-Bewertungen unterstützen nur vSAN als Standardspeichertyp. 
**Reservierte Instanzen (RI)** | Mit dieser Eigenschaft können Sie reservierte Instanzen in AVS angeben. RIs werden derzeit für AVS-Knoten nicht unterstützt. 
**Knotentyp** | Gibt den [AVS-Knotentyp](../azure-vmware/concepts-private-clouds-clusters.md) an, der zum Zuordnen der lokalen VMs verwendet wird. Der Standardknotentyp ist AV36. <br/><br/> Azure Migrate empfiehlt eine erforderliche Anzahl von Knoten für die VMs, die zu AVS migriert werden sollen. 
**FTT-Einstellung, RAID-Ebene** | Gibt die anwendbaren Optionen für tolerierbare Fehler (FTT) oder RAID-Kombinationen an. Die ausgewählte FTT-Option in Kombination mit der lokalen VM-Datenträgeranforderung bestimmt den gesamten in AVS erforderlichen vSAN-Speicher. 
**Größenkriterium** | Legt das Kriterium fest, das zur *Größenanpassung* virtueller Computer für AVS verwendet werden soll. Sie können eine *leistungsbasierte* oder *lokale* Größenanpassung vornehmen, ohne den Leistungsverlauf zu berücksichtigen. 
**Leistungsverlauf** | Legt die zur Bewertung der Leistungsdaten der Computer zu berücksichtigende Dauer fest. Diese Eigenschaft ist nur anwendbar, wenn als Größenkriterium *leistungsbasiert* festgelegt ist. 
**Perzentilwert der Nutzung** | Gibt den für die Größenanpassung zu berücksichtigenden Perzentilwert des festgelegten Leistungsbeispiels. Diese Eigenschaft ist nur anwendbar, wenn eine leistungsbasierte Größenanpassung ausgewählt wurde.
**Komfortfaktor** | Bei der Azure Migrate-Serverbewertung wird während der Bewertung ein Puffer (Komfortfaktor) berücksichtigt. Dieser Puffer wird zusätzlich zu den Daten zur Computernutzung für VMs (CPU, Arbeitsspeicher, Datenträger und Netzwerk) angewendet. Beim Komfortfaktor geht es um Bereiche wie saisonale Nutzung, kurzer Leistungsverlauf und voraussichtliche zukünftige Zunahme der Nutzung.<br/><br/> Beispiel: Für einen virtuellen Computer mit zehn Kernen und einer Auslastung von 20 Prozent ergibt sich normalerweise ein virtueller Computer mit zwei Kernen. Bei einem Komfortfaktor von 2.0x ist das Ergebnis dagegen eine VM mit vier Kernen. 
**Angebot** | Zeigt das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/) an, bei dem Sie registriert sind. Dies wird dann von Azure Migrate bei der Kostenschätzung entsprechend berücksichtigt.
**Währung** | Zeigt die Abrechnungswährung für Ihr Konto an. 
**Rabatt (%)** | Listet alle abonnementspezifischen Rabatte auf, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %. 
**Azure-Hybridvorteil** | Gibt an, ob Sie über Software Assurance verfügen und den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) nutzen können. Obwohl dies aufgrund des knotenbasierten Preises keine Auswirkungen auf die Preisgestaltung von Azure VMware-Lösungen hat, können Kunden dennoch ihre lokalen Betriebssystemlizenzen (Microsoft-basiert) in AVS mit Azure-Hybridvorteilen anwenden. Andere Anbieter von Softwarebetriebssystemen werden ihre eigenen Lizenzbedingungen wie RHEL zur Verfügung stellen müssen. 
**vCPU-Überzeichnung** | Gibt das Verhältnis der Anzahl der virtuellen Kerne an, die an einen physischen Kern im AVS-Knoten gebunden sind. Der Standardwert in den Berechnungen in AVS beträgt vier virtuelle CPUs pro physischem Kern. <br/><br/> API-Benutzer können diesen Wert als ganze Zahl (Integer) festlegen. Beachten Sie, dass eine vCPU-Überzeichnung > 4:1 je nach CPU-Auslastung Auswirkungen auf die Workloads haben kann. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>AVS-Eignungsanalyse (Azure VMware Solution)

AVS-Bewertungen in der Serverbewertung bewerten die einzelnen lokalen virtuellen Computer hinsichtlich ihrer Eignung für AVS, indem die Eigenschaften des Computers überprüft werden. Außerdem werden die einzelnen bewerteten Computer einer der folgenden Eignungskategorien zugewiesen:

- **Bereit für AVS**: Der Computer kann ohne Änderungen zu Azure (AVS) migriert werden. Er wird in AVS mit voller AVS-Unterstützung starten.
- **Bereit mit Bedingungen**: Die VM hat möglicherweise Kompatibilitätsprobleme mit der aktuellen vSphere-Version und erfordert möglicherweise VMware-Tools und/oder andere Einstellungen, bevor die volle Funktionalität der VM in AVS erreicht werden kann.
- **Nicht bereit für AVS**: Der virtuelle Computer kann nicht in AVS gestartet werden. Wenn z. B. an den lokalen virtuellen VMware-Computer ein externes Gerät wie ein CD-ROM angeschlossen ist, tritt beim VMware VMotion-Vorgang ein Fehler auf (bei Verwendung von VMware VMotion).
- **Bereitschaft unbekannt**: Azure Migrate konnte die Bereitschaft des Computers aufgrund unzureichender Metadaten aus der lokalen Umgebung nicht bestimmen.

Die Serverbewertung überprüft die Computereigenschaften, um die Azure-Bereitschaft des lokalen Computers zu bestimmen.

### <a name="machine-properties"></a>Computereigenschaften

Die Serverbewertung überprüft die folgende Eigenschaft des lokalen virtuellen Computers, um zu ermitteln, ob dieser in Azure VMware Solution (AVS) ausgeführt werden kann.


| **Eigenschaft** | **Details** | **AVS-Bereitschaftsstatus** 
| - | - | - 
| **Internetprotokoll** | Die IPv6-Internetadressierung wird von AVS derzeit nicht unterstützt.<br/><br/> Sollte Ihr Computer mit IPv6 erkannt werden, wenden Sie sich an Ihr lokales MSFT-AVS-GBB-Team.| Internetprotokoll mit bedingter Bereitschaft


### <a name="guest-operating-system"></a>Gastbetriebssystem

Gegenwärtig werden Betriebssysteme bei AVS-Bewertungen nicht im Rahmen der Eignungsanalyse überprüft. Alle Betriebssysteme, die auf lokalen virtuellen Computern ausgeführt werden, können wahrscheinlich unter Azure VMware Solution (AVS) ausgeführt werden.

Neben den VM-Eigenschaften untersucht die Serverbewertung auch das Gastbetriebssystem der Computer, um zu ermitteln, ob sie auf Azure ausgeführt werden können.


## <a name="sizing"></a>Festlegen der Größe

Nachdem ein Computer als „bereit für AVS“ markiert wurde, gibt die AVS-Bewertung in der Serverbewertung Empfehlungen zur Knotengrößenbestimmung ab, die die Identifizierung der entsprechenden lokalen VM-Anforderungen und die Ermittlung der Gesamtanzahl der erforderlichen AVS-Knoten umfassen. Die Empfehlungen variieren je nach den angegebenen Bewertungseigenschaften.

- Wenn die Bewertung eine *leistungsbasierte Dimensionierung* verwendet, berücksichtigt Azure Migrate den Leistungsverlauf des Computers, um die geeignete Empfehlung zur Größenanpassung für AVS abzugeben. Diese Methode ist besonders hilfreich, wenn Sie die lokalen virtuellen Computer überbelegt haben, die Auslastung jedoch gering ist, und Sie die Größe der virtuellen Computer in AVS optimal anpassen möchten, um Kosten zu sparen. Diese Methode ist hilfreich, um die Größen bei der Migration zu optimieren.
- Wenn Sie die Leistungsdaten für die VM-Größe nicht berücksichtigen und stattdessen die lokalen Computer unverändert zu AVS übertragen möchten, können Sie die Größenkriterien auf *Wie lokal* festlegen. Anschließend wird die Größe der virtuellen Computer basierend auf der lokalen Konfiguration ohne Berücksichtigung der Nutzungsdaten von der Serverbewertung berechnet. 


### <a name="ftt-sizing-parameters"></a>FTT-Parameter für die Größenanpassung

Die in AVS verwendete Speicher-Engine ist vSAN. Die Speicheranforderungen für Ihre virtuellen Computer werden mithilfe von vSAN-Speicherrichtlinien definiert. Diese Richtlinien steuern, wie Speicher dem virtuellen Computer zugeordnet wird, um die erforderliche Dienstebene für Ihre virtuellen Computer zu garantieren. Folgende FTT-RAID-Kombinationen sind verfügbar: 

**Zu tolerierende Fehler (Failures to Tolerate, FTT)** | **RAID-Konfiguration** | **Mindestens erforderliche Hostanzahl** | **Überlegungen zur Größe**
--- | --- | --- | --- 
1 | RAID-1 (Spiegelung) | 3 | Von einem virtuellen Computer mit 100 GB werden 200 GB beansprucht.
1 | RAID-5 (Erasure Coding) | 4 | Von einem virtuellen Computer mit 100 GB werden 133,33 GB beansprucht.
2 | RAID-1 (Spiegelung) | 5 | Von einem virtuellen Computer mit 100 GB werden 300 GB beansprucht.
2 | RAID-6 (Erasure Coding) | 6 | Von einem virtuellen Computer mit 100 GB werden 150 GB beansprucht.
3 | RAID-1 (Spiegelung) | 7 | Von einem virtuellen Computer mit 100 GB werden 400 GB beansprucht.

### <a name="performance-based-sizing"></a>Leistungsbasierte Größenanpassung

Bei der leistungsbasierten Größenanpassung beginnt die Serverbewertung mit den Datenträgern, die an den virtuellen Computer angefügt sind, und fährt danach mit den Netzwerkadaptern fort. Anschließend werden die VM-Anforderungen einer geeigneten Anzahl von Knoten für AVS zugeordnet. Die Azure Migrate-Appliance erstellt Profile der lokalen Umgebung, um Leistungsdaten für CPU, Speicher, Festplatten und Netzwerkadapter zu sammeln.

**Schritte zur Leistungsdatensammlung:**

1. Bei virtuellen VMware-Computern sammelt die Azure Migrate-Appliance Echtzeit-Abtastpunkte in einem 20-Sekunden-Intervall. 
2. Das Gerät führt einen Rollup für die alle 10 Minuten gesammelten Abtastpunkte aus und sendet den Maximalwert der letzten 10 Minuten an die Serverbewertung.
3. Die Serverbewertung speichert alle 10-Minuten-Abtastpunkte des letzten Monats. Abhängig von den Bewertungseigenschaften, die für den *Leistungsverlauf* und die *prozentuale Auslastung* angegeben werden, wird der entsprechende Datenpunkt identifiziert, der für die richtige Größenanpassung verwendet werden soll. Wenn beispielsweise der Leistungsverlauf auf einen Tag festgelegt ist und die prozentuale Auslastung das 95. Perzentil ist, dann verwendet die Serverbewertung die 10-minütigen Abtastpunkte für den letzten Tag, sortiert sie in aufsteigender Reihenfolge und wählt das 95. Perzentil für die richtige Größe aus.
4. Dieser Wert wird dann mit dem Komfortfaktor multipliziert, um die effektiven Leistungsauslastungsdaten für jede Metrik (CPU-Auslastung, Speicherauslastung, Festplatten-IOPS (Lesen und Schreiben), Festplattendurchsatz (Lesen und Schreiben), Netzwerkdurchsatz (Ein- und Ausgabe)) zu erhalten, die die Appliance sammelt.

Nachdem der Wert für die effektive Auslastung festgelegt wurde, werden Speicher-, Netzwerk- und Computegröße wie folgt behandelt.

**Speichergröße**: Azure Migrate verwendet den gesamten lokalen VM-Speicherplatz auf dem Datenträger als Berechnungsparameter, um die AVS vSAN-Speicheranforderungen zusätzlich zu der vom Kunden gewählten FTT-Einstellung zu bestimmen. FTT – Wenn eine FTT-Option nicht toleriert wird und eine Mindestanzahl von Knoten pro FTT-Option erforderlich ist, wird der gesamte erforderliche vSAN-Speicher in Kombination mit der VM-Datenträgeranforderung bestimmt.

**Netzwerkgröße**: Bei AVS-Bewertungen werden von der Serverbewertung derzeit keine Netzwerkeinstellungen berücksichtigt.

**Computegröße**: Nach der Berechnung der Speicheranforderungen werden von der Serverbewertung die CPU- und Arbeitsspeicheranforderungen berücksichtigt, um die erforderliche Knotenanzahl für AVS auf der Grundlage des Knotentyps zu bestimmen.

- Basierend auf den Kriterien zur Größenanpassung betrachtet die Serverbewertung entweder die leistungsbasierten VM-Daten oder die lokale VM-Konfiguration. Die Einstellung des Komfortfaktors ermöglicht die Angabe des Zuwachsfaktors des Clusters. Da Hyperthreading standardmäßig aktiviert ist, verfügt ein Knoten mit 36 Kernen über 72 virtuelle Kerne. Pro physischem Kern werden vier virtuelle Kerne verwendet, um CPU-Schwellenwerte pro Cluster zu ermitteln. Dabei gilt der VMware-Standard einer maximalen Auslastung von 80 Prozent, um Wartungsmaßnahmen oder Fehlerbehandlungen zu ermöglichen, ohne die Clusterverfügbarkeit zu beeinträchtigen. Gegenwärtig gibt es keine Möglichkeit, die Überzeichnungswerte zu ändern, und dies könnte in zukünftigen Versionen der Fall sein.

### <a name="as-on-premises-sizing"></a>Größenanpassung vom Typ „Wie lokal“

Wenn Sie die Größenanpassung *Wie lokal* verwenden, wird der Leistungsverlauf der VMs und Datenträger von der Serverbewertung nicht berücksichtigt. Stattdessen werden AVS-Knoten basierend auf der lokal zugeordneten Größe zugewiesen. Der Standardspeichertyp in AVS ist vSAN.

## <a name="confidence-ratings"></a>Zuverlässigkeitsstufen

Jeder leistungsbasierten Bewertung in Azure Migrate wird eine Zuverlässigkeitsstufe zugeordnet, die zwischen ein (niedrigster Wert) bis fünf Sterne (höchster Wert) betragen kann.

- Die Zuverlässigkeitsstufe wird einer Bewertung auf der Grundlage der Verfügbarkeit von Datenpunkten zugeordnet, die zum Berechnen der Bewertung erforderlich sind.
- Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von Azure Migrate bereitgestellten Größenempfehlungen besser einschätzen.
- Die Zuverlässigkeitsstufe gilt nicht für Bewertungen vom Typ *Wie lokal*.
- Für eine leistungsbasierte Größenanpassung benötigen die AVS-Bewertungen in der Serverbewertung die Auslastungsdaten für CPU- und VM-Arbeitsspeicher. Die folgenden Daten werden gesammelt, aber nicht für Größenempfehlungen für AVS verwendet:
  - IOPS- und Durchsatzdaten für jeden an den virtuellen Computer angefügten Datenträger
  - Informationen zur Netzwerk-E/A für die leistungsbasierte Größenanpassung für jeden Netzwerkadapter, der an einen virtuellen Computer angefügt ist

  Steht eine dieser Nutzungsangaben in vCenter Server nicht zur Verfügung, ist die Größenempfehlung unter Umständen nicht zuverlässig.

Die Zuverlässigkeitsstufe für die Bewertung ist abhängig davon, wie viele Datenpunkte verfügbar sind (in Prozent).


| **Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe** |
| - | - |
| 0–20 % | 1 Stern |
| 21–40 % | 2 Sterne |
| 41–60 % | 3 Sterne |
| 61–80 % | 4 Sterne |
| 81–100 % | 5 Sterne |

### <a name="low-confidence-ratings"></a>Niedrige Zuverlässigkeitsstufen

Einige mögliche Gründe für eine niedrige Zuverlässigkeitsstufe einer Bewertung:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil der Umgebung erstellt. Wenn Sie z. B. die Bewertung mit einer auf 1 Tag festgelegten Leistungsdauer erstellen, müssen Sie bis mindestens einen Tag nach dem Start der Ermittlung warten, bis alle Datenpunkte gesammelt sind.
- Einige virtuelle Computer wurden während des Zeitraums, für den die Bewertung berechnet wird, heruntergefahren. Wenn einige VMs für eine gewisse Zeit heruntergefahren wurden, kann die Serverbewertung für diesen Zeitraum keine Leistungsdaten sammeln.
- Einige virtuelle Computer wurden während des Zeitraums erstellt, für den die Bewertung berechnet wird. Wenn Sie beispielsweise eine Bewertung für den Leistungsverlauf des letzten Monats erstellen, aber einige VMs erst vor einer Woche in der Umgebung erstellt wurden, dann ist der Leistungsverlauf der neuen VMs nicht für die gesamte Dauer abrufbar.

> [!NOTE]
> Bei einer Zuverlässigkeitsstufe von weniger als fünf Sternen wird empfohlen, mindestens einen Tag zu warten, damit die Appliance ein Profil der Umgebung erstellen kann. Führen Sie dann eine Neuberechnung der Bewertung durch. Wenn Sie dies nicht tun, ist die leistungsbasierte Dimensionierung möglicherweise nicht zuverlässig. In diesem Fall wird empfohlen, die Bewertung auf die Größenanpassung „Wie lokal“ zu ändern.

## <a name="monthly-cost-estimation"></a>Schätzung der monatlichen Kosten

Nachdem die Größenempfehlungen abgeschlossen sind, berechnet Azure Migrate die Gesamtkosten für die Ausführung der lokalen Workloads in AVS, indem die Anzahl der erforderlichen AVS-Knoten mit dem Knotenpreis multipliziert wird. Die Kosten pro VM werden berechnet, indem die Gesamtkosten durch die Anzahl der virtuellen Computer in der Bewertung dividiert werden. 
- Bei der Berechnung werden die Anzahl erforderlicher Knoten, der Knotentyp und der Standort berücksichtigt.
- Die Kosten aller Knoten werden zusammengefasst, um die monatlichen Gesamtkosten zu berechnen.
- Kosten werden in der Währung angezeigt, die in den Bewertungseinstellungen festgelegt ist.

Da die Preise für Azure VMware Solution (AVS) pro Knoten angegeben werden, enthalten die Gesamtkosten keine Verteilung der Compute- und Speicherkosten. [Weitere Informationen](../azure-vmware/introduction.md)

Beachten Sie, dass die Knotenpreise in der Bewertung Vorschaupreise sind, da sich Azure VMware Solution (AVS) in der Vorschauversion befindet. Wenden Sie sich an das lokale MSFT AVS GBB-Team, um weitere Informationen zu erhalten.

## <a name="migration-tool-guidance"></a>Leitfaden zum Migrationstool

Im Bericht zur Azure-Bereitschaft für die Bewertung „Azure VMware Solution (AVS)“ werden folgende Tools empfohlen: 
- **VMware HCX oder Enterprise**: Für VMware-Computer wird die VMware HCX-Lösung (Hybrid Cloud Extension) empfohlen, um Ihre lokale Workload zu Ihrer privaten AVS-Cloud (Azure VMware Solution) zu migrieren. [Weitere Informationen](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Unbekannt:** Für Computer, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool unbekannt. Für VMware-Computer empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension).

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine Bewertung für [virtuelle AVS VMware-Computer](how-to-create-azure-vmware-solution-assessment.md).
