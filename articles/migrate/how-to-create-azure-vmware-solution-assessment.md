---
title: Erstellen einer AVS-Bewertung mit Azure Migrate-Serverbewertung | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine AVS-Bewertung mit dem Tool Azure Migrate-Serverbewertung ausführen.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 93d17ec2a4fb5c191ce02c73a7a3532e9c854b00
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752072"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Erstellen einer Bewertung vom Typ „Azure VMware Solution (AVS)“

Dieser Artikel beschreibt die Erstellung einer AVS-Bewertung (Azure VMware Solution) für lokale virtuelle VMware-Computer mit Azure Migrate: Server Assessment“ (Azure Migrate-Serverbewertung) erstellen.

[Azure Migrate](migrate-services-overview.md) hilft Ihnen bei der Migration zu Azure. Azure Migrate bietet einen zentralisierten Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration lokaler Infrastruktur, Anwendungen und Daten zu Azure. Der Hub stellt Azure-Tools für die Bewertung und Migration sowie Drittanbietertools von unabhängigen Softwareanbietern (ISVs) bereit.

## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie sicher, dass Sie ein Azure Migrate-Projekt [erstellt](./create-manage-projects.md) haben.
- Wenn Sie bereits ein Projekt erstellt haben, vergewissern Sie sich, dass Sie das Tool Azure Migrate-Serverbewertung[hinzugefügt](how-to-assess.md): Migrate-Serverbewertung bewerten.
- Um eine Bewertung zu erstellen, müssen Sie eine Azure Migrate-Appliance für [VMware](how-to-set-up-appliance-vmware.md) einrichten, die die lokalen Computer ermittelt und Metadaten und Leistungsdaten an Azure Migrate sendet: Server Assessment“ (Azure Migrate-Serverbewertung) erstellen. [Weitere Informationen](migrate-appliance.md)
- Sie könnten auch die [Servermetadaten](./tutorial-discover-import.md) im CSV-Format (durch Trennzeichen getrennte Werte) importieren.


## <a name="azure-vmware-solution-avs-assessment-overview"></a>AVS-Bewertung (Azure VMware Solution) – Übersicht

Es gibt zwei Arten von Bewertungen, die Sie mit der Azure Server Assessment“ (Azure Migrate-Serverbewertung) erstellen.

**Bewertungstyp** | **Details**
--- | --- 
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md), [Hyper-V-VMs](how-to-set-up-appliance-hyper-v.md) und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Die AVS-Bewertung (Azure VMware Solution) befindet sich derzeit in der Vorschauversion und kann nur für virtuelle VMware-Computer erstellt werden.


Es gibt zwei Arten von Kriterien zur Größenanpassung, die Sie für die Erstellung von AVS-Bewertungen (Azure VMware Solution) verwenden können:

**Bewertung** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen basierend auf gesammelten Leistungsdaten von lokalen virtuellen Computern. | **Empfohlene Knotengröße**: Basierend auf den Daten zur CPU- und Arbeitsspeicherauslastung zusammen mit dem Knotentyp, dem Speichertyp und der FTT-Einstellung, die Sie für die Bewertung auswählen.
**Wie lokal** | Bewertungen basierend auf lokaler Größenanpassung | **Empfohlene Knotengröße**: Basierend auf der lokalen VM-Größe zusammen mit dem Knotentyp, dem Speichertyp und der FTT-Einstellung, die Sie für die Bewertung auswählen.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Ausführen einer Bewertung vom Typ „Azure VMware Solution (AVS)“

Führen Sie eine Bewertung vom Typ „Azure VMware Solution (AVS)“ wie folgt aus:

1. Machen Sie sich mit den [bewährten Methoden](best-practices-assessment.md) für die Bewertungserstellung vertraut.

2. Klicken Sie auf der Registerkarte **Server** unter der Kachel **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Bewerten**.

    ![Screenshot: Azure Migrate Server mit ausgewählter Option „Bewerten“ unter Bewertungstools](./media/how-to-create-assessment/assess.png)

3. Wählen Sie unter **Server bewerten** die Option „Azure VMware Solution (AVS)“ als Bewertungstyp und dann die Ermittlungsquelle aus, und geben Sie den Bewertungsnamen an.

    ![Grundlagen der Bewertung](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. Klicken Sie auf **Alle anzeigen**, um die Eigenschaften für die Bewertung zu überprüfen.

    ![AVS-Bewertungseigenschaften](./media/how-to-create-avs-assessment/avs-view-all.png)

5. Klicken Sie auf **Weiter**, um zu **Computer für die Bewertung auswählen** zu gelangen. Wählen Sie unter **Gruppe auswählen oder erstellen** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Gruppe ein. Eine Gruppe enthält mindestens eine zu bewertende VM.

6. Wählen Sie unter **Computer zur Gruppe hinzufügen** die VMs aus, die der Gruppe hinzugefügt werden sollen.

7. Klicken Sie auf **Weiter**, um unter **Überprüfen + Bewertung erstellen** die Bewertungsdetails zu überprüfen.

8. Klicken Sie auf **Bewertung erstellen**, um die Gruppe zu erstellen und die Bewertung auszuführen.

    ![Erstellen einer AVS-Bewertung](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. Zeigen Sie die erstellte Bewertung unter **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) > **Bewertungen** an.

10. Klicken Sie auf **Bewertung exportieren**, um sie als Excel-Datei herunterzuladen.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Überprüfen einer Bewertung vom Typ „Azure VMware Solution (AVS)“

Eine Bewertung vom Typ „Azure VMware Solution (AVS)“ beschreibt Folgendes:

- **AVS-Bereitschaft (Azure VMware Solution)** : Ermittelt, ob die lokalen virtuellen Computer für die Migration zu Azure VMware Solution (AVS) geeignet sind.
- **Anzahl der AVS-Knoten**: Geschätzte Anzahl der AVS-Knoten, die zur Ausführung der virtuellen Computer erforderlich sind.
- **Übergreifende Nutzung auf AVS-Knoten**: Projizierte CPU-, Arbeitsspeicher- und Speicherauslastung über alle Knoten hinweg.
- **Geschätzte monatliche Kosten**: Die geschätzten monatlichen Kosten für alle AVS-Knoten (Azure VMware Solution), auf denen die lokalen virtuellen Computer ausgeführt werden.


### <a name="view-an-assessment"></a>Anzeigen einer Bewertung

1. Klicken Sie unter **Migrationsziele** >  **Server** auf **Bewertungen** (unter **Azure Migrate: Serverbewertung** aus.

2. Klicken Sie unter **Bewertungen** auf eine Bewertung, um sie zu öffnen.

    ![Zusammenfassung der AVS-Bewertung](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>Überprüfen der AVS-Bereitschaft (Azure VMware Solution)

1. Überprüfen Sie unter **Azure-Bereitschaft**, ob VMs für die Migration zu AVS bereit sind.

2. Überprüfen Sie den VM-Status:
    - **Bereit für AVS**: Der Computer kann ohne Änderungen zu Azure (AVS) migriert werden. Er wird in AVS mit voller AVS-Unterstützung starten.
    - **Bereit mit Bedingungen**: Die VM hat möglicherweise Kompatibilitätsprobleme mit der aktuellen vSphere-Version und erfordert möglicherweise VMware-Tools und/oder andere Einstellungen, bevor die volle Funktionalität der VM in AVS erreicht werden kann.
    - **Nicht bereit für AVS**: Der virtuelle Computer kann nicht in AVS gestartet werden. Wenn z. B. an den lokalen virtuellen VMware-Computer ein externes Gerät wie ein CD-ROM angeschlossen ist, tritt beim VMotion-Vorgang ein Fehler auf (bei Verwendung von VMware VMotion).
    - **Bereitschaft unbekannt**: Azure Migrate konnte die Bereitschaft des Computers aufgrund unzureichender Metadaten aus der lokalen Umgebung nicht bestimmen.

3. Überprüfen Sie das vorgeschlagene Tool:
    - **VMware HCX oder Enterprise**: Für VMware-Computer wird die VMware HCX-Lösung (Hybrid Cloud Extension) empfohlen, um Ihre lokale Workload zu Ihrer privaten AVS-Cloud (Azure VMware Solution) zu migrieren. [Weitere Informationen](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Unbekannt:** Für Computer, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool unbekannt. Für VMware-Computer empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension). 

4. Klicken Sie auf einen **AVS-Bereitschaftsstatus**. Sie können Details zur VM-Bereitschaft sowie VM-Details wie Compute-, Speicher- und Netzwerkeinstellungen anzeigen.



### <a name="review-cost-details"></a>Überprüfen der Kostendetails

In dieser Ansicht werden die geschätzten Kosten für die Ausführung der VMs in Azure VMware Solution (AVS) angezeigt.

1. Überprüfen Sie die monatlichen Gesamtkosten. Die Kosten für alle VMs in der bewerteten Gruppe werden aggregiert. 

    - Kostenschätzungen basieren auf der Anzahl der benötigten AVS-Knoten unter Berücksichtigung der Ressourcenanforderungen aller VMs insgesamt.
    - Da die Preise für Azure VMware Solution (AVS) pro Knoten angegeben werden, enthalten die Gesamtkosten keine Verteilung der Compute- und Speicherkosten.
    - Die Kostenschätzung gilt für die Ausführung der lokalen VMs in AVS. PaaS- oder SaaS-Kosten werden von der Azure Migrate-Serverbewertung nicht berücksichtigt.
    
2. Sie können die geschätzten monatlichen Speicherkosten überprüfen. Diese Ansicht zeigt aggregierte Speicherkosten für die bewertete Gruppe, aufgeschlüsselt nach verschiedenen Arten von Speicherdatenträgern.

3. Sie können einen Drilldown ausführen, um die Details für bestimmte VMs anzuzeigen.


### <a name="review-confidence-rating"></a>Prüfen der Zuverlässigkeitsstufe

Wenn Sie leistungsbasierte Bewertungen ausführen, wird der Bewertung eine Zuverlässigkeitsstufe zugewiesen.

![Zuverlässigkeitsstufe](./media/how-to-create-assessment/confidence-rating.png)

- Es wird eine Bewertung zwischen einem Stern (niedrigste Stufe) und fünf Sternen (höchste Stufe) vergeben.
- Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von der Bewertung bereitgestellten Größenempfehlungen besser einschätzen.
- Die Zuverlässigkeitsstufe basiert auf der Verfügbarkeit von Datenpunkten, die zum Berechnen der Bewertung erforderlich sind.
- Für eine leistungsbasierte Größenanpassung benötigen die AVS-Bewertungen in der Serverbewertung die Auslastungsdaten für CPU- und VM-Arbeitsspeicher. Die folgenden Leistungsdaten werden gesammelt, aber nicht für Größenempfehlungen für AVS-Bewertungen verwendet:
  - IOPS- und Durchsatzdaten für jeden an den virtuellen Computer angefügten Datenträger
  - Informationen zur Netzwerk-E/A für die leistungsbasierte Größenanpassung für jeden Netzwerkadapter, der an einen virtuellen Computer angefügt ist

Die Zuverlässigkeitsstufen für eine Bewertung sehen wie folgt aus:

**Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
--- | ---
0 % bis 20 % | Ein Stern
21 % bis 40 % | Zwei Sterne
41 % bis 60 % | Drei Sterne
61 % bis 80 % | Vier Sterne
81 % bis 100 % | Fünf Sterne

[Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md) zu Leistungsdaten. 


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Erstellen von Gruppen mit hoher Vertrauenswürdigkeit mithilfe der [Zuordnung von Abhängigkeiten](how-to-create-group-machine-dependencies.md).
- [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md) zur Berechnung von AVS-Bewertungen.