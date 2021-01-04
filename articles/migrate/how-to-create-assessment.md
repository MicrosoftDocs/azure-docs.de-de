---
title: Erstellen einer Azure VM-Bewertung mit Azure Migrate-Serverbewertung | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine Azure VM-Bewertung mit dem Tool Azure Migrate-Serverbewertung ausführen.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: cc7101c61db8f0863c3a16b1c17f04188f9bee4e
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754299"
---
# <a name="create-an-azure-vm-assessment"></a>Erstellen einer Azure-VM-Bewertung

In diesem Artikel wird beschrieben, wie Sie eine Azure VM-Bewertung für lokale VMware-VMs oder Hyper-V-VMs mit Azure Migrate erstellen: Server Assessment“ (Azure Migrate-Serverbewertung) erstellen.

[Azure Migrate](migrate-services-overview.md) hilft Ihnen bei der Migration zu Azure. Azure Migrate bietet einen zentralisierten Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration lokaler Infrastruktur, Anwendungen und Daten zu Azure. Der Hub stellt Azure-Tools für die Bewertung und Migration sowie Drittanbietertools von unabhängigen Softwareanbietern (ISVs) bereit. 

## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie sicher, dass Sie ein Azure Migrate-Projekt [erstellt](./create-manage-projects.md) haben.
- Wenn Sie bereits ein Projekt erstellt haben, vergewissern Sie sich, dass Sie das Tool Azure Migrate-Serverbewertung[hinzugefügt](how-to-assess.md): Migrate-Serverbewertung bewerten.
- Zum Erstellen einer Bewertung müssen Sie eine Azure Migrate-Appliance für [VMware](how-to-set-up-appliance-vmware.md) oder [Hyper-V](how-to-set-up-appliance-hyper-v.md) einrichten. Die Appliance ermittelt lokale Computer und sendet Metadaten und Leistungsdaten an Azure Migrate: Server Assessment“ (Azure Migrate-Serverbewertung) erstellen. [Weitere Informationen](migrate-appliance.md)


## <a name="azure-vm-assessment-overview"></a>Übersicht über Azure VM-Bewertungen
Es gibt zwei Arten von Größenanpassungskriterien, die Sie zur Erstellung einer Azure VM-Bewertung mit Azure Migrate verwenden können: Server Assessment“ (Azure Migrate-Serverbewertung) erstellen.

**Bewertung** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen basierend auf gesammelten Leistungsdaten | **Empfohlene VM-Größe**: Basierend auf CPU- und Arbeitsspeicher-Nutzungsdaten<br/><br/> **Empfohlener Datenträgertyp (Verwalteter Datenträger vom Typ Standard oder Premium)** : Basierend auf IOPS und Durchsatz der lokalen Datenträger
**Wie lokal** | Bewertungen basierend auf lokaler Größenanpassung | **Empfohlene VM-Größe**: Basierend auf der Größe der lokalen VM<br/><br> **Empfohlener Datenträgertyp**: Basierend auf der für die Bewertung ausgewählten Speichertypeinstellung

[Hier erfahren Sie mehr](concepts-assessment-calculation.md) über Bewertungen.

## <a name="run-an-assessment"></a>Durchführen einer Bewertung

Führen Sie eine Bewertung wie folgt aus:

1. Machen Sie sich mit den [bewährten Methoden](best-practices-assessment.md) für die Bewertungserstellung vertraut.
2. Klicken Sie auf der Registerkarte **Server** unter der Kachel **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Bewerten**.

    ![Screenshot: Azure Migrate Server mit ausgewählter Option „Bewerten“ unter Bewertungstools](./media/how-to-create-assessment/assess.png)

3. Wählen Sie unter **Server bewerten** die Option „Azure-VM“ als Bewertungstyp und dann die Ermittlungsquelle aus, und geben Sie den Bewertungsnamen an.

    ![Grundlagen der Bewertung](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Klicken Sie auf **Alle anzeigen**, um die Eigenschaften für die Bewertung zu überprüfen.

    ![Bewertungseigenschaften](./media/how-to-create-assessment//view-all.png)

5. Klicken Sie auf **Weiter**, um zu **Computer für die Bewertung auswählen** zu gelangen. Wählen Sie unter **Gruppe auswählen oder erstellen** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Gruppe ein. Eine Gruppe enthält mindestens eine zu bewertende VM.
6. Wählen Sie unter **Computer zur Gruppe hinzufügen** die VMs aus, die der Gruppe hinzugefügt werden sollen.
7. Klicken Sie auf **Weiter**, um unter **Überprüfen + Bewertung erstellen** die Bewertungsdetails zu überprüfen.
8. Klicken Sie auf **Bewertung erstellen**, um die Gruppe zu erstellen und die Bewertung auszuführen.

    ![Erstellen einer Bewertung](./media/how-to-create-assessment//assessment-create.png)

9. Zeigen Sie die erstellte Bewertung unter **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) > **Bewertungen** an.
10. Klicken Sie auf **Bewertung exportieren**, um sie als Excel-Datei herunterzuladen.



## <a name="review-an-azure-vm-assessment"></a>Überprüfen einer Azure-VM-Bewertung

Bei einer Azure-VM-Bewertung wird Folgendes beschrieben:

- **Azure-Bereitschaft**: Gibt an, ob VMs für die Migration zu Azure geeignet sind.
- **Geschätzte monatliche Kosten**: Die geschätzten monatlichen Compute- und Speicherkosten für die Ausführung der VMs in Azure.
- **Geschätzte monatliche Speicherkosten**: Die geschätzten Kosten für den Datenträgerspeicher nach der Migration.

### <a name="view-an-azure-vm-assessment"></a>Anzeigen einer Azure VM-Bewertung

1. Klicken Sie unter **Migrationsziele** >  **Server** auf **Bewertungen** (unter **Azure Migrate: Serverbewertung** aus.
2. Klicken Sie unter **Bewertungen** auf eine Bewertung, um sie zu öffnen.

    ![Zusammenfassung der Bewertung](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Überprüfen der Azure-Bereitschaft

1. Überprüfen Sie unter **Azure-Bereitschaft**, ob VMs für die Migration zu Azure bereit sind.
2. Überprüfen Sie den VM-Status:
    - **Bereit für Azure**: Azure Migrate empfiehlt in der Bewertung eine VM-Größe und gibt Kostenschätzungen für VMs ab.
    - **Bereit mit Bedingungen**: Zeigt Probleme und eine vorgeschlagene Abhilfe an.
    - **Nicht bereit für Azure**: Zeigt Probleme und eine vorgeschlagene Abhilfe an.
    - **Bereitschaft unbekannt**: Wird verwendet, wenn Azure Migrate die Bereitschaft aufgrund von Problemen mit der Datenverfügbarkeit nicht bewerten kann.

3. Klicken Sie auf einen **Azure-Bereitschaftsstatus**. Sie können Details zur VM-Bereitschaft sowie VM-Details wie Compute-, Speicher- und Netzwerkeinstellungen anzeigen.



### <a name="review-cost-details"></a>Überprüfen der Kostendetails

In dieser Ansicht werden die geschätzten Compute- und Speicherkosten für die Ausführung der VMs in Azure angezeigt.

1. Überprüfen Sie die monatlichen Compute- und Speicherkosten. Die Kosten für alle VMs in der bewerteten Gruppe werden aggregiert.

    - Kostenschätzungen basieren auf den Größenempfehlungen für einen Computer sowie auf seinen Datenträgern und Eigenschaften.
    - Die geschätzten monatlichen Kosten für Compute und Speicher werden angezeigt.
    - Die Kostenschätzung gilt für die Ausführung der lokalen VMs als IaaS-VMs. PaaS- oder SaaS-Kosten werden von der Azure Migrate-Serverbewertung nicht berücksichtigt.

2. Sie können die geschätzten monatlichen Speicherkosten überprüfen. Diese Ansicht zeigt aggregierte Speicherkosten für die bewertete Gruppe, aufgeschlüsselt nach verschiedenen Arten von Speicherdatenträgern.
3. Sie können einen Drilldown ausführen, um die Details für bestimmte VMs anzuzeigen.


### <a name="review-confidence-rating"></a>Prüfen der Zuverlässigkeitsstufe

Wenn Sie leistungsbasierte Bewertungen ausführen, wird der Bewertung eine Zuverlässigkeitsstufe zugewiesen.

![Zuverlässigkeitsstufe](./media/how-to-create-assessment/confidence-rating.png)

- Es wird eine Bewertung zwischen einem Stern (niedrigste Stufe) und fünf Sternen (höchste Stufe) vergeben.
- Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von der Bewertung bereitgestellten Größenempfehlungen besser einschätzen.
- Die Zuverlässigkeitsstufe basiert auf der Verfügbarkeit von Datenpunkten, die zum Berechnen der Bewertung erforderlich sind.

Die Zuverlässigkeitsstufen für eine Bewertung sehen wie folgt aus:

**Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
--- | ---
0 % bis 20 % | Ein Stern
21 % bis 40 % | Zwei Sterne
41 % bis 60 % | Drei Sterne
61 % bis 80 % | Vier Sterne
81 % bis 100 % | Fünf Sterne




## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Erstellen von Gruppen mit hoher Vertrauenswürdigkeit mithilfe der [Zuordnung von Abhängigkeiten](how-to-create-group-machine-dependencies.md).
- [Weitere Informationen](concepts-assessment-calculation.md) zur Berechnung von Bewertungen