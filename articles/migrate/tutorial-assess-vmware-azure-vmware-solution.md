---
title: Bewerten von VMware-VMs für die Migration zu Azure VMware Solution (AVS) mit Azure Migrate
description: Es wird beschrieben, wie Sie VMware-VMs für die Migration zu AVS bewerten, indem Sie die Azure Migrate-Serverbewertung verwenden.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: f6d3c6f77b062939a88e7277cb7f0ab6ecff9fcb
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753075"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Tutorial: Bewerten von virtuellen VMware-Computern für die Migration zu AVS

Im Rahmen Ihrer Migrationsjourney zu Azure bewerten Sie Ihre lokalen Workloads, um die Cloudbereitschaft zu messen, Risiken zu identifizieren und Kosten und Komplexität zu schätzen.

In diesem Artikel wird veranschaulicht, wie Sie ermittelte virtuelle VMware-Computer (VMs) für die Migration zu Azure VMware Solution (AVS) per Azure Migrate-Serverbewertung bewerten. Bei AVS handelt es sich um einen verwalteten Dienst, mit dem Sie die VMware-Plattform in Azure ausführen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
- Führen Sie eine Bewertung basierend auf den Computermetadaten und den Konfigurationsinformationen durch.
- Führen Sie eine Bewertung basierend auf den Leistungsdaten durch.

> [!NOTE]
> In Tutorials wird der schnellste Weg zum Ausprobieren eines Szenarios beschrieben, und nach Möglichkeit werden dabei die Standardoptionen verwendet. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.



## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die zu bewertenden Computer richtig ermittelt haben, bevor Sie dieses Tutorial zum Bewerten Ihrer Computer für die Migration zu AVS durcharbeiten:

- Arbeiten Sie [dieses Tutorial](tutorial-discover-vmware.md) durch, um Computer mit der Azure Migrate-Appliance zu ermitteln. 
- Bei der Ermittlung von Computern mit einer importierten CSV-Datei hilft Ihnen [dieses Tutorial](tutorial-discover-import.md) weiter.


## <a name="decide-which-assessment-to-run"></a>Treffen einer Entscheidung für eine Bewertung


Entscheiden Sie, ob Sie eine Bewertung durchführen möchten, bei der die Kriterien für die Größenanpassung auf den Daten bzw. Metadaten der Computerkonfiguration basieren, die lokal im unveränderten Zustand gesammelt werden, oder auf dynamischen Leistungsdaten.

**Bewertung** | **Details** | **Empfehlung**
--- | --- | ---
**Aktuelle lokale Umgebung** | Bewertung basierend auf den Daten bzw. Metadaten für die Computerkonfiguration.  | Die empfohlene Knotengröße in AVS basiert auf der lokalen VM-Größe sowie auf den Einstellungen, die Sie in der Bewertung für den Knotentyp, für den Speichertyp und für tolerierbare Fehler angeben.
**Leistungsbasiert** | Bewertung basierend auf gesammelten dynamischen Leistungsdaten. | Die empfohlene Knotengröße in AVS basiert auf den Auslastungsdaten für CPU und Arbeitsspeicher sowie auf den Einstellungen, die Sie in der Bewertung für den Knotentyp, für den Speichertyp und für tolerierbare Fehler angeben.

## <a name="run-an-assessment"></a>Durchführen einer Bewertung

Führen Sie eine Bewertung wie folgt aus:

1. Klicken Sie auf der Seite **Server** unter **Windows- und Linux-Server** auf **Server bewerten und migrieren**.

   ![Ort der Schaltfläche „Server bewerten und migrieren“](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

2. Klicken Sie in **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Bewerten**.

3. Wählen Sie unter **Server bewerten** > **Bewertungstyp** die Option **Azure VMware Solution (AVS) (Vorschau)** aus.
4. Unter **Ermittlungsquelle**:

    - Wählen Sie die Option **Von Azure Migrate-Appliance erkannte Computer** aus, falls Sie Computer mit der Appliance ermittelt haben.
    - Wählen Sie die Option **Importierte Computer** aus, falls Sie Computer mit einer importierten CSV-Datei ermittelt haben. 
    
5. Geben Sie einen Namen für die Bewertung an. 
6. Klicken Sie auf **Alle anzeigen**, um die Eigenschaften für die Bewertung zu überprüfen.

    ![Seite zum Auswählen der Bewertungseinstellungen](./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png)


7. Unter **Bewertungseigenschaften** > **Zieleigenschaften**:

    - Geben Sie unter **Zielspeicherort** die Azure-Region an, zu der Sie die Migration durchführen möchten.
       - Empfehlungen zur Größe und zu den Kosten basieren auf dem von Ihnen angegebenen Standort.
       - Derzeit können Sie die Bewertung für drei Regionen („USA, Osten“, „USA, Westen“, „Europa, Westen“) durchführen.
   - Übernehmen Sie unter **Speichertyp** die Option **vSAN**. Dies ist der Standardspeichertyp für eine private AVS-Cloud.
   - **Reservierte Instanzen** werden für AVS-Knoten derzeit nicht unterstützt.
8. Unter **VM-Größe**:
    - Wählen Sie unter **Knotentyp** einen Knotentyp basierend auf den Workloads aus, die auf den lokalen VMs ausgeführt werden.
        - Azure Migrate empfiehlt aus der Gruppe der Knoten denjenigen Knoten, der zum Migrieren der VMs zu AVS benötigt wird.
        - Der Standardknotentyp ist AV36.
    - **FTT-Einstellung, RAID-Ebene**: Wählen Sie die Kombination für tolerierbare Fehler (Failure to Tolerate, FTT) und RAID aus.  Die ausgewählte FTT-Option in Kombination mit der lokalen VM-Datenträgeranforderung bestimmt den insgesamt in AVS erforderlichen vSAN-Speicher.
    - Geben Sie unter **CPU-Überzeichnung** das Verhältnis der virtuellen Kerne an, die auf dem AVS-Knoten einem physischen Kern zugeordnet sind. Eine höhere Überzeichnung als 4:1 kann unter Umständen zwar zu einer Leistungsbeeinträchtigung führen, aber sie kann für Workloads vom Typ „Webserver“ verwendet werden.

9. Unter **Knotengröße**: 
    - Wählen Sie unter **Größenkriterium** aus, ob die Bewertung auf statischen Metadaten oder leistungsbezogenen Daten basieren soll. Bei Verwendung von Leistungsdaten:
        - Geben Sie unter **Leistungsverlauf** die Datendauer an, auf der die Bewertung basieren soll.
        - Geben Sie unter **Perzentilwert der Nutzung** den Perzentilwert an, den Sie für das Leistungsbeispiel verwenden möchten. 
    - Geben Sie unter **Komfortfaktor** den Puffer an, den Sie während der Bewertung verwenden möchten. Hierbei werden Aspekte wie saisonale Nutzung, ein kurzer Leistungsverlauf und eine voraussichtliche Zunahme der zukünftigen Nutzung berücksichtigt. Beispiel für einen Komfortfaktor von „2“:
    
        **Komponente** | **Tatsächliche Auslastung** | **Komfortfaktor hinzufügen (2.0)**
        --- | --- | ---  
        Kerne | 2 | 4
        Arbeitsspeicher | 8 GB | 16 GB     

10. Unter **Preise**:
    - Unter **Angebot** wird das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/) angezeigt, für das Sie registriert sind. Bei der Serverbewertung werden die Kosten für dieses Angebot geschätzt.
    - Wählen Sie unter **Währung** die Abrechnungswährung für Ihr Konto aus.
    - Fügen Sie unter **Abzug (%)** alle abonnementspezifischen Rabatte hinzu, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %.

11. Klicken Sie auf **Speichern**, falls Sie Änderungen vorgenommen haben.

    ![Bewertungseigenschaften](./media/tutorial-assess-vmware-azure-vmware-solution/view-all.png)

12. Klicken Sie unter **Server bewerten** auf **Weiter**.
13. Wählen Sie zum Erstellen einer neuen Gruppe mit Servern für die Bewertung unter **Server bewerten** > **Computer für die Bewertung auswählen** die Option **Neu erstellen** aus, und geben Sie einen Gruppennamen an. 
14. Wählen Sie die Appliance und dann die VMs aus, die Sie der Gruppe hinzufügen möchten. Klicken Sie dann auf **Weiter**.
15. Sehen Sie sich unter **Überprüfen + Bewertung erstellen** die Bewertungsdetails an, und klicken Sie auf **Bewertung erstellen**, um die Gruppe zu erstellen und die Bewertung durchzuführen.

    > [!NOTE]
    > Bei leistungsbezogenen Bewertungen empfehlen wir Ihnen, nach dem Starten einer Ermittlung mindestens einen Tag zu warten, bevor Sie eine Bewertung erstellen. Dies ermöglicht einen längeren Zeitraum zum Sammeln von Leistungsdaten mit höherer Zuverlässigkeit. Idealerweise sollten Sie nach dem Starten der Ermittlung den Zeitraum der Leistungsdauer abwarten, den Sie angegeben haben (Tag/Woche/Monat), um eine Bewertung mit einer hohen Zuverlässigkeit zu erzielen.

## <a name="review-an-assessment"></a>Überprüfen einer Bewertung

Mit einer AVS-Bewertung wird Folgendes beschrieben:

- AVS-Bereitschaft: Ermittelt, ob die lokalen virtuellen Computer für die Migration zu Azure VMware Solution (AVS) geeignet sind.
- Anzahl der AVS-Knoten: Geschätzte Anzahl der AVS-Knoten, die zur Ausführung der virtuellen Computer erforderlich sind.
- Übergreifende Nutzung auf AVS-Knoten: Projizierte CPU-, Arbeitsspeicher- und Speicherauslastung über alle Knoten hinweg.
- Geschätzte monatliche Kosten: Die geschätzten monatlichen Kosten für alle AVS-Knoten (Azure VMware Solution), auf denen die lokalen virtuellen Computer ausgeführt werden.

## <a name="view-an-assessment"></a>Anzeigen einer Bewertung

So zeigen Sie eine Bewertung an:

1. Klicken Sie unter **Server** > **Azure Migrate: Serverbewertung** auf die Zahl neben **Bewertungen**.
2. Wählen Sie unter **Bewertungen** eine Bewertung aus, um sie zu öffnen. 
3. Sehen Sie sich die Zusammenfassung der Bewertung an. Sie können auch die Bewertungseigenschaften bearbeiten oder die Bewertung neu berechnen.
 

### <a name="review-readiness"></a>Überprüfen der Bereitschaft

1. Klicken Sie auf **Azure-Bereitschaft**.
2. Sehen Sie sich unter **Azure-Bereitschaft** den VM-Status an.

    - **Bereit für AVS**: Der Computer kann ohne Änderungen zu AVS migriert werden. Der Computer wird in AVS mit voller AVS-Unterstützung gestartet.
    - **Bereit mit Bedingungen**: Für den Computer bestehen unter Umständen Kompatibilitätsprobleme in Bezug auf die aktuelle vSphere-Version. Gegebenenfalls müssen VMware-Tools installiert oder andere Einstellungen vorgenommen werden, bevor die volle Funktionalität in AVS zur Verfügung steht.
    - **Nicht bereit für AVS**: Die VM wird in AVS nicht gestartet. Wenn an eine lokale VMware-VM beispielsweise ein externes Gerät (z. B. ein CD-ROM-Laufwerk) angefügt ist und Sie VMware VMotion verwenden, tritt beim VMotion-Vorgang ein Fehler auf.
 - **Bereitschaft unbekannt**: Azure Migrate konnte die Bereitschaft des Computers aufgrund unzureichender Metadaten aus der lokalen Umgebung nicht bestimmen.

3. Überprüfen Sie das vorgeschlagene Tool.

    - VMware HCX oder Enterprise: Für VMware-Computer wird die VMware HCX-Lösung (Hybrid Cloud Extension) empfohlen, um Ihre lokale Workload zu Ihrer privaten AVS-Cloud (Azure VMware Solution) zu migrieren. Weitere Informationen
    - Unbekannt: Für Computer, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool unbekannt. Für VMware-Computer empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension).
4. Klicken Sie auf einen AVS-Bereitschaftsstatus. Sie können Details zur VM-Bereitschaft sowie VM-Details wie Compute-, Speicher- und Netzwerkeinstellungen anzeigen.

### <a name="review-cost-estimates"></a>Überprüfen der Kostenschätzungen

Die Zusammenfassung der Bewertung enthält die geschätzten Compute- und Speicherkosten für die VM-Ausführung in Azure. 

1. Überprüfen Sie die monatlichen Gesamtkosten. Die Kosten für alle VMs in der bewerteten Gruppe werden aggregiert.

    - Kostenschätzungen basieren auf der Anzahl von benötigten AVS-Knoten unter Berücksichtigung der Ressourcenanforderungen aller VMs insgesamt.
    - Da die Preise für AVS pro Knoten angegeben werden, enthalten die Gesamtkosten keine Verteilung der Compute- und Speicherkosten.
    - Die Kostenschätzung gilt für die Ausführung der lokalen VMs in AVS. PaaS- oder SaaS-Kosten werden von der Azure Migrate-Serverbewertung nicht berücksichtigt.

2. Überprüfen Sie den geschätzten monatlichen Speicheraufwand. In der Ansicht werden die aggregierten Speicherkosten für die bewertete Gruppe angezeigt, aufgeschlüsselt nach verschiedenen Arten von Speicherdatenträgern. 
3. Sie können einen Drilldown ausführen, um Kostendetails für bestimmte virtuelle Computer anzuzeigen.

### <a name="review-confidence-rating"></a>Prüfen der Zuverlässigkeitsstufe

Von der Serverbewertung wird für leistungsbezogene Bewertungen eine Zuverlässigkeitsstufe zugewiesen. Die Bewertung kann einen Wert zwischen einem Stern (am niedrigsten) und fünf Sternen (am höchsten) aufweisen.

![Zuverlässigkeitsstufe](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

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
- Richten Sie die Abhängigkeitszuordnung [ohne Agent](how-to-create-group-machine-dependencies-agentless.md) oder [mit Agent](how-to-create-group-machine-dependencies.md) ein.
