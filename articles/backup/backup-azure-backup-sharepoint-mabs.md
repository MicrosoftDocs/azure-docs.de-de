---
title: Sichern einer SharePoint-Farm mit Azure Backup Server in Azure
description: Verwenden Sie Azure Backup Server zum Sichern und Wiederherstellen Ihrer SharePoint-Daten. Dieser Artikel stellt die benötigten Informationen bereit, um Ihre SharePoint-Farm so zu konfigurieren, dass Sie die gewünschten Daten in Azure speichern können. Sie können geschützte SharePoint-Daten vom Datenträger oder aus Azure wiederherstellen.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: kasinh
ms.openlocfilehash: 7fa68e11ccac69db9335e589f5048264df9d0a47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60645329"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Sichern einer SharePoint-Farm in Azure
Die Vorgehensweise zum Sichern einer SharePoint-Farm mithilfe von Microsoft Azure Backup Server (MABS) in Microsoft Azure ähnelt der Vorgehensweise zum Sichern anderer Datenquellen. Azure Backup ermöglicht die Verwendung eines flexiblen Sicherungszeitplans, mit dem Sie tägliche, wöchentliche, monatliche oder jährliche Sicherungspunkte erstellen und Aufbewahrungsrichtlinienoptionen für unterschiedliche Sicherungspunkte konfigurieren können. Mit MABS können Sie zudem lokale Festplattenkopien speichern, um die Wiederherstellung zu beschleunigen. Außerdem können Sie Kopien zur kostengünstigen, langfristigen Aufbewahrung in Azure speichern.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Von SharePoint unterstützte Versionen und entsprechende Sicherungsszenarien
Azure Backup für DPM unterstützt folgende Szenarios:

| Workload | Version | SharePoint-Bereitstellung | Schutz und Wiederherstellung |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007 und SharePoint 3.0 |Bereitstellung von SharePoint als physischer Server oder als virtueller Hyper-V-/VMware-Computer <br> -------------- <br> SQL AlwaysOn | Schutz der SharePoint-Farm-Wiederherstellungsoptionen: Wiederherstellung von Farm, Datenbank und Datei oder Listenelement aus Datenträger-Wiederherstellungspunkten.  Farm und Datenbankwiederherstellung aus Azure-Wiederherstellungspunkten. |

## <a name="before-you-start"></a>Vorbereitung
Vor dem Sichern einer SharePoint-Farm in Azure müssen Sie ein paar Punkte bestätigen.

### <a name="prerequisites"></a>Voraussetzungen
Bevor Sie fortfahren, stellen Sie sicher, [dass Azure Backup Server installiert und für den Schutz von Workloads vorbereitet wurde](backup-azure-microsoft-azure-backup.md).

### <a name="protection-agent"></a>Schutz-Agent
Der Azure Backup-Agent muss auf dem Server, auf dem SharePoint ausgeführt wird, auf den Servern, auf denen SQL Server ausgeführt wird, sowie allen anderen Servern installiert werden, die der SharePoint-Farm angehören. Weitere Informationen zum Einrichten des Schutz-Agents finden Sie unter [Einrichten des Schutz-Agents](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Einzige Ausnahme: Der Agent wird nur auf einem einzelnen Web-Front-End-Server (WFE) installiert. Azure Backup Server benötigt den Agent auf einem WFE-Server nur als Einstiegspunkt für den Schutz.

### <a name="sharepoint-farm"></a>SharePoint-Farm
Pro zehn Millionen Farmelemente müssen mindestens 2 GB Speicherplatz auf dem Volume verfügbar sein, auf dem sich der MABS-Ordner befindet. Dieser Speicherplatz wird für die Kataloggenerierung benötigt. Für die Wiederherstellung bestimmter Elemente (Websitesammlungen, Websites, Listen, Dokumentbibliotheken, Ordner, einzelne Dokumente und Listenelemente) durch MABS erstellt die Kataloggenerierung eine Liste mit den URLs aus den einzelnen Inhaltsdatenbanken. Sie können die URL-Liste im Bereich für wiederherstellbare Elemente (im Aufgabenbereich **Wiederherstellung** der MABS-Administratorkonsole) anzeigen.

### <a name="sql-server"></a>SQL Server
Azure Backup Server wird als ein „LocalSystem“-Konto ausgeführt. Zum Sichern von SQL Server-Datenbanken benötigt MABS Systemadministratorberechtigungen für dieses Konto für den Server, auf dem SQL Server ausgeführt wird. Legen Sie für NT AUTHORITY\SYSTEM *sysadmin* auf dem Server fest, auf dem SQL Server ausgeführt wird, bevor Sie ihn sichern.

Falls die SharePoint-Farm über SQL Server-Datenbanken verfügt, die mit SQL Server-Aliasnamen konfiguriert sind, installieren Sie die SQL Server-Clientkomponenten auf dem Front-End-Webserver, der von MABS geschützt wird.

### <a name="sharepoint-server"></a>SharePoint Server
Die Leistung hängt von zahlreichen Faktoren ab – unter anderem von der Größe der SharePoint-Farm. Grundsätzlich gilt: Mit einer einzelnen MABS-Instanz kann eine SharePoint-Farm mit einem Volumen von 25 TB geschützt werden.

### <a name="whats-not-supported"></a>Nicht unterstützte Funktionen
* Der MABS-Schutz einer SharePoint-Farm erstreckt sich nicht auf Suchindizes oder Anwendungsdienstdatenbanken. Für diese Datenbanken muss der Schutz separat konfiguriert werden.
* Von MABS werden keine SharePoint-SQL Server-Datenbanken gesichert, die sich auf Freigaben von Dateiservern mit horizontaler Skalierung (Scale Out File Server, SOFS) befinden.

## <a name="configure-sharepoint-protection"></a>Konfigurieren des SharePoint-Schutzes
Bevor Sie SharePoint mit MABS schützen können, müssen Sie den SharePoint VSS Writer-Dienst (WSS Writer-Dienst) mithilfe von **ConfigureSharePoint.exe**konfigurieren.

Die Datei **ConfigureSharePoint.exe** befindet sich auf dem Front-End-Webserver im Ordner „[MABS-Installationspfad]\bin“. Dieses Tool stellt den Schutz-Agent mit den Anmeldeinformationen für die SharePoint-Farm bereit. Es wird auf einem einzelnen WFE-Server ausgeführt. Wählen Sie bei Verwendung mehrerer WFE-Server einen der Server aus, wenn Sie eine Schutzgruppe konfigurieren.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>So konfigurieren Sie den SharePoint VSS Writer-Dienst
1. Navigieren Sie an einer Eingabeaufforderung auf dem WFE-Server zu „[MABS-Installationsort]\bin\“.
2. Geben Sie ConfigureSharePoint -EnableSharePointProtection ein.
3. Geben Sie die Administratoranmeldeinformationen für die Farm an. Dieses Konto muss der lokalen Administratorgruppe auf dem WFE-Server angehören. Wenn der Farmadministrator kein lokaler Administrator ist, erteilen Sie auf dem WFE-Server die folgenden Berechtigungen:
   * Erteilen Sie der Gruppe „WSS_ADMIN_WPG“ Vollzugriff auf den DPM-Ordner (%Program Files%\Microsoft Azure Backup\DPM).
   * Erteilen Sie der Gruppe „WSS_Admin_WPG“ Lesezugriff auf den DPM-Registrierungsschlüssel (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Wenn sich die Administratoranmeldeinformationen für die SharePoint-Farm ändern, muss „ConfigureSharePoint.exe“ erneut ausgeführt werden.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Sichern einer SharePoint-Farm mit MABS
Wenn Sie MABS und die SharePoint-Farm wie oben beschrieben konfiguriert haben, kann SharePoint von MABS geschützt werden.

### <a name="to-protect-a-sharepoint-farm"></a>So schützen Sie eine SharePoint-Farm
1. Klicken Sie auf der Registerkarte **Schutz** der MABS-Administratorkonsole auf **Neu**.
    ![Neue Registerkarte „Schutz“](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Wählen Sie auf der Seite **Schutzgruppentyp auswählen** des Assistenten **Neue Schutzgruppe erstellen** die Option **Server** aus, und klicken Sie anschließend auf **Weiter**.

    ![Schutzgruppentyp auswählen](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Aktivieren Sie im Bildschirm **Gruppenmitglieder auswählen** das Kontrollkästchen für den zu schützenden SharePoint-Server, und klicken Sie auf **Weiter**.

    ![Gruppenmitglieder auswählen](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Wenn der Schutz-Agent installiert ist, wird der Server im Assistenten angezeigt. Darüber hinaus wird von MABS die Struktur angezeigt. Dank der Ausführung von „ConfigureSharePoint.exe“ kommuniziert MABS mit dem SharePoint VSS Writer-Dienst sowie mit den entsprechende SQL-Datenbanken und erkennt die Struktur der SharePoint-Farm (die zugeordneten Inhaltsdatenbanken und alle dazugehörigen Elemente).
   >
   >
4. Geben Sie auf der Seite **Datenschutzmethode auswählen** den Namen der **Schutzgruppe** ein, und wählen Sie Ihre bevorzugten *Schutzmethoden* aus. Klicken Sie auf **Weiter**.

    ![Datenschutzmethode auswählen](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Die Datenträgerschutz-Methode erleichtert das Einhalten kurzer Wiederherstellungszeit-Ziele.
   >
   >
5. Wählen Sie auf der Seite **Kurzfristige Ziele angeben** Ihre bevorzugte **Beibehaltungsdauer** aus, und geben Sie an, wann die Sicherungen durchgeführt werden sollen.

    ![Kurzfristige Ziele angeben](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Da in den meisten Fällen Daten wiederhergestellt werden, die weniger als fünf Tage alt sind, haben wir in diesem Beispiel eine datenträgerbasierte Beibehaltungsdauer von fünf Tagen ausgewählt und die Sicherung so konfiguriert, dass sie nicht während der Produktionszeiten erfolgt.
   >
   >
6. Überprüfen Sie den für die Schutzgruppe reservierten Speicherpool-Speicherplatz, und klicken Sie auf **Weiter**.
7. MABS reserviert für jede Schutzgruppe Speicherplatz zum Speichern und Verwalten von Replikaten. An diesem Punkt muss MABS eine Kopie der ausgewählten Daten erstellen. Wählen Sie aus, wie und wann das Replikat erstellt werden soll, und klicken Sie anschließend auf **Weiter**.

    ![Replikaterstellungsmethode auswählen](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Wählen Sie eine Zeit außerhalb der Produktionszeit, um eine Beeinträchtigung des Netzwerkdatenverkehrs zu vermeiden.
   >
   >
8. MABS prüft die Konsistenz des Replikats und stellt so die Datenintegrität sicher. Hierbei stehen zwei Optionen zur Verfügung: Sie können einen Zeitplan für die Ausführung von Konsistenzprüfungen definieren, oder DPM kann die Konsistenz des Replikats automatisch überprüfen, wenn es inkonsistent wird. Wählen Sie Ihre bevorzugte Option aus, und klicken Sie anschließend auf **Weiter**.

    ![Konsistenzprüfung](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Wählen Sie auf der Seite **Online zu schützende Daten** die zu schützende SharePoint-Farm aus, und klicken Sie anschließend auf **Weiter**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Wählen Sie auf der Seite **Online-Sicherungszeitplan angeben** Ihren bevorzugten Zeitplan aus, und klicken Sie anschließend auf **Weiter**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS führt maximal zwei tägliche Sicherungen in Azure aus, und zwar von dem jeweils verfügbaren neuesten Datenträgersicherungspunkt aus. Azure Backup kann auch mithilfe der [Azure Backup-Netzwerk-Bandbreiteneinschränkung](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling)steuern, welche WAN-Bandbreite innerhalb und außerhalb von Spitzenzeiten für Sicherungen verwendet werden kann.
    >
    >
11. Wählen Sie abhängig vom ausgewählten Sicherungszeitplan auf der Seite **Online-Aufbewahrungsrichtlinie angeben** die gewünschte Aufbewahrungsrichtlinie für die täglichen, wöchentlichen, monatlichen und jährlichen Sicherungspunkte aus.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > Das Aufbewahrungsschema von MABS funktioniert nach dem Großvater-Vater-Sohn-Prinzip. Dabei kann für verschiedene Sicherungspunkte jeweils eine andere Aufbewahrungsrichtlinie ausgewählt werden.
    >
    >
12. In Azure muss (ähnlich wie bei einem Datenträger) ein erstes Referenzpunktreplikat erstellt werden. Wählen Sie Ihre bevorzugte Option zum Erstellen der ersten Sicherungskopie in Azure, und klicken Sie anschließend auf **Weiter**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Überprüfen Sie in der **Zusammenfassung** die ausgewählten Einstellungen, und klicken Sie anschließend auf **Gruppe erstellen**. Nach Erstellen der Schutzgruppe wird eine Erfolgsmeldung angezeigt.

    ![Zusammenfassung](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Wiederherstellen eines SharePoint-Elements vom Datenträger mit MABS
Im folgenden Beispiel wurde *Recovering SharePoint item* versehentlich gelöscht und muss wiederhergestellt werden.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öffnen Sie die **DPM-Verwaltungskonsole**. Alle von DPM geschützten SharePoint-Farmen werden auf der Registerkarte **Schutz** angezeigt.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Wechseln Sie zum Wiederherstellen des Elements zunächst zur Registerkarte **Wiederherstellung** .

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Sie können SharePoint mithilfe einer platzhalterbasierten Suche innerhalb eines Wiederherstellungspunkt-Bereichs nach *Recovering SharePoint item* durchsuchen.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wählen Sie in den Suchergebnissen den entsprechenden Wiederherstellungspunkt aus, klicken Sie mit der rechten Maustaste auf das Element, und wählen Sie **Wiederherstellen**.
5. Sie können auch andere Wiederherstellungspunkte durchsuchen und eine Datenbank oder ein Element für die Wiederherstellung auswählen. Wählen Sie **Datum > Wiederherstellungszeit** und anschließend die korrekte Option für **Datenbank > SharePoint-Farm > Wiederherstellungspunkt > Element**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klicken Sie mit der rechten Maustaste auf das Element, und wählen Sie **Wiederherstellen**, um den **Wiederherstellungs-Assistenten** zu öffnen. Klicken Sie auf **Weiter**.

    ![Wiederherstellungsauswahl prüfen](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Wählen Sie den gewünschten Wiederherstellungstyp aus, und klicken Sie anschließend auf **Weiter**.

    ![Wiederherstellungstyp](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Bei Auswahl von **In ursprünglicher Site wiederherstellen** im Beispiel wird das Element in der ursprünglichen SharePoint-Website wiederhergestellt.
   >
   >
8. Wählen Sie den gewünschten **Wiederherstellungsprozess** aus.

   * Wählen Sie **Ohne Wiederherstellungsfarm wiederherstellen** , wenn sich die SharePoint-Farm nicht geändert hat und dem Wiederherstellungspunkt entspricht, der wiederhergestellt wird.
   * Wählen Sie **Mithilfe einer Wiederherstellungsfarm wiederherstellen** , wenn sich die SharePoint-Farm seit der Erstellung des Wiederherstellungspunkts geändert hat.

     ![Wiederherstellungsprozess](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Geben Sie einen SQL Server-Instanz-Stagingort für die vorübergehende Wiederherstellung der Datenbank und eine Stagingdateifreigabe auf MABS sowie dem Server an, auf dem SharePoint ausgeführt wird, um das Element wiederherzustellen.

    ![Staging Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS verknüpft die Inhaltsdatenbank, die als Host für das SharePoint-Element fungiert, mit der temporären SQL Server-Instanz. MABS stellt das Element aus der Inhaltsdatenbank wieder her und platziert es am Stagingdateispeicherort auf MABS. Das am Stagingort wiederhergestellte Element muss nun an den Stagingort in der SharePoint-Farm exportiert werden.

    ![Staging Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wählen Sie **Wiederherstellungsoptionen angeben**, und wenden Sie Sicherheitseinstellungen auf die SharePoint-Farm oder die Sicherheitseinstellungen des Wiederherstellungspunkts an. Klicken Sie auf **Weiter**.

    ![Wiederherstellungsoptionen](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Sie können auch die Nutzung der Netzwerkbandbreite drosseln. Dies minimiert die Auswirkungen auf den Produktionsserver während der Produktion.
    >
    >
11. Überprüfen Sie die Zusammenfassung, und klicken Sie anschließend auf **Wiederherstellen** , um die Wiederherstellung der Datei zu initiieren.

    ![Wiederherstellungszusammenfassung](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Klicken Sie auf die Registerkarte **Überwachung** der **MABS-Administratorkonsole**, um den **Status** der Wiederherstellung anzuzeigen.

    ![Wiederherstellungsstatus](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Die Datei ist nun wiederhergestellt. Sie können die SharePoint-Site aktualisieren, um die wiederhergestellte Datei zu überprüfen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Azure-basiertes Wiederherstellen einer SharePoint-Datenbank mit DPM
1. Durchsuchen Sie zum Wiederherstellen einer SharePoint-Inhaltsdatenbank verschiedene Wiederherstellungspunkte (siehe oben), und wählen Sie den wiederherzustellenden Wiederherstellungspunkt aus.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Doppelklicken Sie auf den SharePoint-Wiederherstellungspunkt, um die verfügbaren SharePoint-Kataloginformationen anzuzeigen.

   > [!NOTE]
   > Da die SharePoint-Farm mit langfristiger Aufbewahrung in Azure geschützt ist, sind auf MABS keine Kataloginformationen (Metadaten) verfügbar. Wenn also eine Zeitpunktwiederherstellung einer SharePoint-Inhaltsdatenbank erforderlich ist, muss die SharePoint-Farm neu katalogisiert werden.
   >
   >
3. Klicken Sie auf **die entsprechende** Option .

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Das Statusfenster für die **Neukatalogisierung der Cloud** wird geöffnet.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nach Abschluss der Katalogisierung ändert sich der Status in *Erfolgreich*. Klicken Sie auf **Schließen**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klicken Sie auf das SharePoint-Objekt, das auf der MABS-Registerkarte **Wiederherstellung** angezeigt wird, um die Struktur der Inhaltsdatenbank abzurufen. Klicken Sie mit der rechten Maustaste auf das Element, und klicken Sie dann auf **Wiederherstellen**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Führen Sie nun die weiter oben in diesem Artikel beschriebenen Wiederherstellungsschritte für die Wiederherstellung einer SharePoint-Inhaltsdatenbank vom Datenträger aus.

## <a name="faqs"></a>Häufig gestellte Fragen
F: Kann ich ein SharePoint-Element am ursprünglichen Speicherort wiederherstellen, wenn SharePoint mit SQL AlwaysOn (und datenträgerbasiertem Schutz) konfiguriert wird?<br>
A: Ja, das Element kann in der ursprünglichen SharePoint-Site wiederhergestellt werden.

F: Kann ich eine SharePoint-Datenbank am ursprünglichen Speicherort wiederherstellen, wenn SharePoint mit SQL AlwaysOn konfiguriert wird?<br>
A: Da SharePoint-Datenbanken in SQL AlwaysOn konfiguriert werden, können sie nur geändert werden, wenn die Verfügbarkeitsgruppe entfernt wird. MABS kann eine Datenbank daher nicht am ursprünglichen Speicherort wiederherstellen. Sie können eine SQL Server-Datenbank in einer anderen SQL Server-Instanz wiederherstellen.

## <a name="next-steps"></a>Nächste Schritte

Siehe den Artikel [Sichern eines Exchange-Servers](backup-azure-exchange-mabs.md).
Siehe den Artikel [Sichern eines SQL Server-Servers](backup-azure-sql-mabs.md).
