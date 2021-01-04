---
title: Bewerten von virtuellen Hyper-V-Computern für die Migration zu Azure-VMs mit Serverbewertung in Azure Migrate
description: Es wird beschrieben, wie Sie Hyper-V-VMs für die Migration zu Azure-VMs bewerten, indem Sie die Serverbewertung verwenden.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 04570785d80e494134bc50cefe3381277d6440a4
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753279"
---
# <a name="tutorial-assess-hyper-v-vms-for-migration-to-azure"></a>Tutorial: Bewerten von virtuellen Hyper-V-Computern für die Migration zu Azure

Im Rahmen Ihrer Migrationsjourney zu Azure bewerten Sie Ihre lokalen Workloads, um die Cloudbereitschaft zu messen, Risiken zu identifizieren und Kosten und Komplexität zu schätzen.

In diesem Artikel wird veranschaulicht, wie Sie ermittelte virtuelle Hyper-V-Computer (VMs) für die Migration zu Azure per Azure Migrate-Serverbewertung bewerten.


In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
- Durchführen einer Bewertung
- Analysieren einer Bewertung

> [!NOTE]
> In Tutorials wird der schnellste Weg zum Ausprobieren eines Szenarios beschrieben, und nach Möglichkeit werden dabei die Standardoptionen verwendet. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass Sie die zu bewertenden Computer richtig ermittelt haben, bevor Sie dieses Tutorial zum Bewerten Ihrer Computer für die Migration zu Azure-VMs durcharbeiten:
    - Arbeiten Sie [dieses Tutorial](tutorial-discover-hyper-v.md) durch, um Computer mit der Azure Migrate-Appliance zu ermitteln. 
    - Bei der Ermittlung von Computern mit einer importierten CSV-Datei hilft Ihnen [dieses Tutorial](tutorial-discover-import.md) weiter.



## <a name="decide-which-assessment-to-run"></a>Treffen einer Entscheidung für eine Bewertung


Entscheiden Sie, ob Sie eine Bewertung durchführen möchten, bei der die Kriterien für die Größenanpassung auf den Daten bzw. Metadaten der Computerkonfiguration basieren, die lokal im unveränderten Zustand gesammelt werden, oder auf dynamischen Leistungsdaten.

**Bewertung** | **Details** | **Empfehlung**
--- | --- | ---
**Aktuelle lokale Umgebung** | Bewertung basierend auf den Daten bzw. Metadaten für die Computerkonfiguration.  | Die empfohlene Größe der Azure-VM basiert auf der lokalen VM-Größe.<br/><br> Der empfohlene Azure-Datenträgertyp basiert auf Ihrer Auswahl in der Speichertypeinstellung in der Bewertung.
**Leistungsbasiert** | Bewertung basierend auf gesammelten dynamischen Leistungsdaten. | Die empfohlene Größe der Azure-VM basiert auf den Daten zur Auslastung der CPU und des Arbeitsspeichers.<br/><br/> Der empfohlene Datenträgertyp basiert auf dem IOPS-Wert und dem Durchsatz der lokalen Datenträger.


## <a name="run-an-assessment"></a>Durchführen einer Bewertung

Führen Sie eine Bewertung wie folgt aus:

1. Klicken Sie auf der Seite **Server** unter **Windows- und Linux-Server** auf **Server bewerten und migrieren**.

   ![Ort der Schaltfläche „Server bewerten und migrieren“](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. Klicken Sie unter „Azure Migrate-Serverbewertung“ auf **Bewerten**.

    ![Position der Schaltfläche „Bewerten“](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. Wählen Sie unter **Server bewerten** > **Bewertungstyp** die Option **Azure-VM** aus.
4. Unter **Ermittlungsquelle**:

    - Wählen Sie die Option **Von Azure Migrate-Appliance erkannte Computer** aus, falls Sie Computer mit der Appliance ermittelt haben.
    - Wählen Sie die Option **Importierte Computer** aus, falls Sie Computer mit einer importierten CSV-Datei ermittelt haben. 
    
5. Geben Sie einen Namen für die Bewertung an. 
6. Klicken Sie auf **Alle anzeigen**, um die Eigenschaften für die Bewertung zu überprüfen.

    ![Ort der Schaltfläche „Alle anzeigen“ zum Anzeigen der Bewertungseigenschaften](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

7. Unter **Bewertungseigenschaften** > **Zieleigenschaften**:
    - Geben Sie unter **Zielspeicherort** die Azure-Region an, zu der Sie die Migration durchführen möchten.
        - Empfehlungen zur Größe und zu den Kosten basieren auf dem von Ihnen angegebenen Standort.
        - Bei Azure Government können Sie Bewertungen in [diesen Regionen](migrate-support-matrix.md#supported-geographies-azure-government) durchführen.
    - Unter **Speichertyp**:
        - Wählen Sie bei Verwendung von leistungsbezogenen Daten in der Bewertung die Option **Automatisch** für Azure Migrate aus, um basierend auf dem IOPS-Wert und dem Durchsatz des Datenträgers eine Empfehlung zum Speichertyp anzuzeigen.
        - Wählen Sie alternativ den Speichertyp aus, den Sie beim Migrieren für die VM verwenden möchten.
    - Geben Sie unter **Reservierte Instanzen** an, ob Sie für die VM beim Migrieren reservierte Instanzen verwenden möchten.
        - Wenn Sie die Verwendung einer reservierten Instanz auswählen, können Sie nicht **Abzug (%)** oder **VM-Betriebszeit** angeben. 
        - [Weitere Informationen](https://aka.ms/azurereservedinstances)
8. Unter **VM-Größe**:
 
    - Wählen Sie unter **Größenkriterium** aus, ob die Bewertung auf Daten bzw. Metadaten für die Computerkonfiguration oder leistungsbezogenen Daten basieren soll. Bei Verwendung von Leistungsdaten:
        - Geben Sie unter **Leistungsverlauf** die Datendauer an, auf der die Bewertung basieren soll.
        - Geben Sie unter **Perzentilwert der Nutzung** den Perzentilwert an, den Sie für das Leistungsbeispiel verwenden möchten. 
    - Geben Sie unter **VM-Serie** die gewünschte Azure-VM-Serie an.
        - Bei Verwendung der leistungsbezogenen Bewertung wird Ihnen von Azure Migrate ein Wert vorgeschlagen.
        - Optimieren Sie die Einstellungen nach Bedarf. Wenn Sie beispielsweise in der Produktionsumgebung keine virtuellen Computer der A-Serie in Azure benötigen, können Sie die A-Serie aus der Liste der Serien ausschließen.
    - Geben Sie unter **Komfortfaktor** den Puffer an, den Sie während der Bewertung verwenden möchten. Hierbei werden Aspekte wie saisonale Nutzung, ein kurzer Leistungsverlauf und eine voraussichtliche Zunahme der zukünftigen Nutzung berücksichtigt. Beispiel für einen Komfortfaktor von „2“: **Komponente** | **Tatsächliche Auslastung** | **Komfortfaktor hinzufügen (2.0)** Kerne | 2 | 4 Arbeitsspeicher | 8 GB | 16 GB     
   
9. Unter **Preise**:
    - Geben Sie unter **Angebot** das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/) ein, wenn Sie registriert sind. Bei der Serverbewertung werden die Kosten für dieses Angebot geschätzt.
    - Wählen Sie unter **Währung** die Abrechnungswährung für Ihr Konto aus.
    - Fügen Sie unter **Abzug (%)** alle abonnementspezifischen Rabatte hinzu, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %.
    - Geben Sie unter **VM-Betriebszeit** die Dauer (Tage pro Monat/Stunden pro Tag) für die Ausführung der VMs an.
        - Dies ist für Azure-VMs hilfreich, die nicht dauerhaft ausgeführt werden.
        - Die Kostenschätzungen basieren auf der angegebenen Dauer.
        - Der Standardwert ist „31 Tage pro Monat/24 Stunden pro Tag“.

    - Geben Sie unter **EA-Abonnement** an, ob bei der Kostenschätzung ein Rabatt für ein EA-Abonnement (Enterprise Agreement) berücksichtigt werden soll. 
    - Geben Sie unter **Azure-Hybridvorteil** an, ob Sie bereits über eine Windows Server-Lizenz verfügen. Wenn dies der Fall ist und eine aktive Software Assurance-Abdeckung für Windows Server-Abonnements besteht, können Sie sich für den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) bewerben, sofern Sie eigene Lizenzen für Azure mitbringen.

10. Klicken Sie auf **Speichern**, falls Sie Änderungen vorgenommen haben.

    ![Bewertungseigenschaften](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

11. Klicken Sie unter **Server bewerten** auf **Weiter**.
12. Wählen Sie unter **Computer für die Bewertung auswählen** die Option **Neu erstellen** aus, und geben Sie einen Gruppennamen an. 
13. Wählen Sie die Appliance und dann die VMs aus, die Sie der Gruppe hinzufügen möchten. Klicken Sie dann auf **Weiter**.
14. Sehen Sie sich unter „Überprüfen + Bewertung erstellen“ die Bewertungsdetails an, und klicken Sie auf **Bewertung erstellen**, um die Gruppe zu erstellen und die Bewertung durchzuführen.


    > [!NOTE]
    > Bei leistungsbezogenen Bewertungen empfehlen wir Ihnen, nach dem Starten einer Ermittlung mindestens einen Tag zu warten, bevor Sie eine Bewertung erstellen. Dies ermöglicht einen längeren Zeitraum zum Sammeln von Leistungsdaten mit höherer Zuverlässigkeit. Idealerweise sollten Sie nach dem Starten der Ermittlung den Zeitraum der Leistungsdauer abwarten, den Sie angegeben haben (Tag/Woche/Monat), um eine Bewertung mit einer hohen Zuverlässigkeit zu erzielen.

## <a name="review-an-assessment"></a>Überprüfen einer Bewertung

Eine Bewertung beschreibt Folgendes:

- **Azure-Bereitschaft**: Gibt an, ob VMs für die Migration zu Azure geeignet sind.
- **Geschätzte monatliche Kosten**: Die geschätzten monatlichen Compute- und Speicherkosten für die Ausführung der VMs in Azure.
- **Geschätzte monatliche Speicherkosten**: Die geschätzten Kosten für den Datenträgerspeicher nach der Migration.

So zeigen Sie eine Bewertung an:

1. Klicken Sie unter **Server** > **Azure Migrate: Serverbewertung** auf die Zahl neben **Bewertungen**.
2. Wählen Sie unter **Bewertungen** eine Bewertung aus, um sie zu öffnen. Beispiel (Schätzungen und Kosten gelten nur für das Beispiel): 

    ![Zusammenfassung der Bewertung](./media/tutorial-assess-vmware-azure-vm/assessment-summary.png)

3. Sehen Sie sich die Zusammenfassung der Bewertung an. Sie können auch die Bewertungseigenschaften bearbeiten oder die Bewertung neu berechnen.
 
 
### <a name="review-readiness"></a>Überprüfen der Bereitschaft

1. Klicken Sie auf **Azure-Bereitschaft**.
2. Sehen Sie sich unter **Azure-Bereitschaft** den VM-Status an:
    - **Bereit für Azure**: Wird verwendet, wenn Azure Migrate in der Bewertung eine VM-Größe empfiehlt und Kostenschätzungen für virtuelle Computer angibt.
    - **Bereit mit Bedingungen**: Zeigt Probleme und eine vorgeschlagene Abhilfe an.
    - **Nicht bereit für Azure**: Zeigt Probleme und eine vorgeschlagene Abhilfe an.
    - **Bereitschaft unbekannt**: Wird verwendet, wenn Azure Migrate die Bereitschaft aufgrund von Problemen mit der Datenverfügbarkeit nicht bewerten kann.

3. Wählen Sie unter **Azure-Bereitschaft** einen Status aus. Sie können Details zur VM-Bereitschaft anzeigen. Darüber hinaus können Sie VM-Details wie Compute-, Speicher- und Netzwerkeinstellungen anzeigen.

### <a name="review-cost-estimates"></a>Überprüfen der Kostenschätzungen

Die Zusammenfassung der Bewertung enthält die geschätzten Compute- und Speicherkosten für die VM-Ausführung in Azure. 

1. Überprüfen Sie die monatlichen Gesamtkosten. Die Kosten für alle VMs in der bewerteten Gruppe werden aggregiert.

    - Kostenschätzungen basieren auf den Größenempfehlungen für einen Computer sowie auf seinen Datenträgern und Eigenschaften.
    - Die geschätzten monatlichen Kosten für Compute und Speicher werden angezeigt.
    - Die Kostenschätzung gilt für die Ausführung der lokalen VMs auf Azure-VMs. Bei der Schätzung werden keine PaaS- oder SaaS-Kosten berücksichtigt.

2. Überprüfen Sie die monatlichen Speicherkosten. In der Ansicht werden die aggregierten Speicherkosten für die bewertete Gruppe angezeigt, aufgeschlüsselt nach verschiedenen Arten von Speicherdatenträgern. 
3. Sie können einen Drilldown ausführen, um Kostendetails für bestimmte virtuelle Computer anzuzeigen.

### <a name="review-confidence-rating"></a>Prüfen der Zuverlässigkeitsstufe

Von der Serverbewertung wird für leistungsbezogene Bewertungen eine Zuverlässigkeitsstufe zugewiesen. Die Bewertung kann einen Wert zwischen einem Stern (am niedrigsten) und fünf Sternen (am höchsten) aufweisen.

![Zuverlässigkeitsstufe](./media/tutorial-assess-vmware-azure-vm/confidence-rating.png)

Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der Größenempfehlungen in der Bewertung besser einschätzen. Die Stufe basiert auf der Verfügbarkeit von Datenpunkten, die für die Berechnung der Bewertung erforderlich sind.

> [!NOTE]
> Zuverlässigkeitsstufen werden nicht zugewiesen, wenn Sie eine Bewertung basierend auf einer CSV-Datei erstellen.

Die Zuverlässigkeitsstufen lauten wie unten angegeben.

**Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
--- | ---
0 % bis 20 % | 1 Stern
21 % bis 40 % | 2 Sterne
41 % bis 60 % | 3 Sterne
61 % bis 80 % | 4 Sterne
81 % bis 100 % | 5 Sterne

Informieren Sie sich über [Zuverlässigkeitsstufen](concepts-assessment-calculation.md#confidence-ratings-performance-based).

## <a name="next-steps"></a>Nächste Schritte

- Suchen Sie nach Computerabhängigkeiten, indem Sie die [Zuordnung von Abhängigkeiten](concepts-dependency-visualization.md) verwenden.
- Richten Sie die Abhängigkeitszuordnung [mit Agent](how-to-create-group-machine-dependencies.md) ein.
