---
title: Sichern von Dateien in Azure Stack-VMs
description: Verwenden Sie Azure Backup, um Azure Stack-Dateien und -Anwendungen in Ihrer Azure Stack-Umgebung zu sichern und wiederherzustellen.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 67d79f2aa41bab8a14d693098538d22ffeb05a4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848755"
---
# <a name="back-up-files-on-azure-stack"></a>Sichern von Dateien in Azure Stack
Sie können Azure Backup verwenden, um Dateien und Anwendungen in Azure Stack zu schützen (oder zu sichern). Installieren Sie Microsoft Azure Backup Server als virtuellen Computer, der unter Azure Stack ausgeführt wird, um Dateien und Anwendungen zu sichern. Sie können die Dateien schützen, die auf einem beliebigen Azure Stack-Server in demselben virtuellen Netzwerk ausgeführt werden. Nachdem Sie Azure Backup Server installiert haben, fügen Sie Azure-Datenträger hinzu, um den lokalen Speicher zu vergrößern, der für die kurzfristige Sicherung von Daten verfügbar ist. Azure Backup Server nutzt Azure-Speicher für die langfristige Aufbewahrung.

> [!NOTE]
> Azure Backup Server und System Center Data Protection Manager (DPM) sind zwar ähnlich, aber DPM wird für die Verwendung mit Azure Stack nicht unterstützt.
>

In diesem Artikel wird nicht die Installation von Azure Backup Server in der Azure Stack-Umgebung behandelt. Informationen zur Installation von Azure Backup Server in Azure Stack finden Sie im Artikel [Installieren von Azure Backup Server in Azure Stack](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Sichern von Dateien und Ordnern in Azure Stack-VMs in Azure

Um Azure Backup Server zum Schutz von Dateien auf virtuellen Azure Stack-Computern zu konfigurieren, öffnen Sie die Azure Backup Server-Konsole. Sie verwenden die Konsole, um Schutzgruppen zu konfigurieren und die Daten auf Ihren virtuellen Computern zu schützen.

1. Klicken Sie in der Azure Backup Server-Konsole auf **Schutz** und dann auf der Symbolleiste auf **Neu**, um den**Assistenten zum Erstellen einer neuen Schutzgruppe zu** öffnen.

   ![Konfigurieren des Schutzes in der Azure Backup Server-Konsole](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Das Öffnen des Assistenten kann einige Sekunden dauern. Klicken Sie im geöffneten Assistenten auf **Weiter**, um mit dem Bildschirm **Schutzgruppentyp auswählen** fortzufahren.

   ![Der Assistent zum Erstellen einer neuen Schutzgruppe wird geöffnet](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Wählen Sie auf dem Bildschirm **Schutzgruppentyp auswählen** die Option **Server** aus, und klicken Sie auf **Weiter**.

    ![Der Assistent zum Erstellen einer neuen Schutzgruppe wird geöffnet](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Der Bildschirm **Gruppenmitglieder auswählen** wird geöffnet. 

    ![Der Assistent zum Erstellen einer neuen Schutzgruppe wird geöffnet](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Klicken Sie auf dem Bildschirm **Gruppenmitglieder auswählen** auf **+** , um die Liste mit Unterelementen zu erweitern. Aktivieren Sie das Kontrollkästchen für alle Elemente, die Sie schützen möchten. Nachdem alle Elemente ausgewählt wurden, klicken Sie auf **Weiter**.

    ![Der Assistent zum Erstellen einer neuen Schutzgruppe wird geöffnet.](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Microsoft empfiehlt, alle Daten, für die eine gemeinsame Schutzrichtlinie gilt, in einer Schutzgruppe zusammenzufassen. Ausführliche Informationen zur Planung und Bereitstellung von Schutzgruppen finden Sie im System Center DPM-Artikel [Bereitstellen von Schutzgruppen](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Geben Sie auf dem Bildschirm **Datenschutzmethode auswählen** einen Namen für die Schutzgruppe ein. Aktivieren Sie das Kontrollkästchen **Ich möchte kurzfristigen Schutz per:** und **Ich möchte Onlineschutz**. Klicken Sie auf **Weiter**.

    ![Der Assistent zum Erstellen einer neuen Schutzgruppe wird geöffnet.](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Um **Ich möchte Onlineschutz** auszuwählen, müssen Sie zuerst **Ich möchte kurzfristigen Schutz per: Datenträger** auswählen. Azure Backup Server sorgt nicht mittels Bandtechnologie für Schutz, weshalb ein Datenträger die einzige Wahl für kurzfristigen Schutz ist.

5. Wählen Sie auf dem Bildschirm **Kurzfristige Ziele angeben**, wie lange die auf dem Datenträger gespeicherten Wiederherstellungspunkte beibehalten und wann inkrementelle Sicherungen gespeichert werden sollen. Klicken Sie auf **Weiter**.

    > [!IMPORTANT]
    > Sie sollten Sicherungsdaten für die Wiederherstellung des Betriebs **nicht** länger als fünf Tage auf Datenträgern aufbewahren, die an Azure Backup Server angefügt sind.
    >

    ![Der Assistent zum Erstellen einer neuen Schutzgruppe wird geöffnet.](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Anstatt ein Intervall für inkrementelle Sicherungen auszuwählen, klicken Sie auf **Direkt vor einem Wiederherstellungspunkt**, um eine schnelle vollständige Sicherung unmittelbar vor jedem geplanten Wiederherstellungspunkt durchzuführen. Wenn Sie Anwendungsworkloads schützen, erstellt Azure Backup Server Wiederherstellungspunkte gemäß dem Zeitplan für die Synchronisierungsfrequenz (sofern die Anwendung inkrementelle Sicherungen unterstützt). Wenn die Anwendung keine inkrementellen Sicherungen unterstützt, führt Azure Backup Server eine schnelle vollständige Sicherung durch.

    Geben Sie für **Dateiwiederherstellungspunkte** an, wann Wiederherstellungspunkte erstellt werden sollen. Klicken Sie auf **Ändern**, um die Uhrzeiten und Wochentage festzulegen, an denen Wiederherstellungspunkte erstellt werden.

6. Überprüfen Sie auf dem Bildschirm **Datenträgerzuordnungen überprüfen** den Speicherplatz im Speicherpool, der der Schutzgruppe zugeordnet ist.

    **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten. **Bereitzustellender Speicherplatz** für Azure Backup Server ist der für die Schutzgruppe empfohlene Speicherplatz. Azure Backup Server wählt auf Grundlage der Einstellungen das ideale Sicherungsvolume. Sie können jedoch in „Details zur Datenträgerzuordnung“ das gewählte Sicherungsvolume ändern. Wählen Sie für die Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich „Verfügbarer Speicherplatz“ die Werte für „Gesamtspeicher“ und „Freier Speicher“. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die laut Vorschlag von Azure Backup Server zum Sicherstellen künftiger reibungsloser Sicherungen dem Volume hinzugefügt werden sollte.

7. Wählen Sie unter **Replikaterstellungsmethode auswählen** aus, wie die erste vollständige Datenreplikation erfolgen soll. Wenn Sie sich für die Replikation über das Netzwerk entscheiden, empfiehlt Azure, hierfür eine Nebenzeit auszuwählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Replikation der Daten mit Wechselmedien in Betracht.

8. Legen Sie unter **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen. Aktivieren Sie für Konsistenzprüfungen die Ausführung nur bei inkonsistenter Datenreplikation oder nach einem Zeitplan. Wenn Sie keine automatische Konsistenzprüfung konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung wie folgt ausführen:
    * Klicken Sie im Bereich **Schutz** der Azure Backup Server-Konsole mit der rechten Maustaste auf die Schutzgruppe, und wählen Sie **Konsistenzprüfung ausführen** aus.

9. Wenn Sie die Sicherung in Azure ausgewählt haben, vergewissern Sie sich, dass auf der Seite **Online zu schützende Daten angeben** die Workloads ausgewählt sind, die Sie in Azure sichern möchten.

10. Geben Sie unter **Onlinesicherungszeitplan angeben** an, wann inkrementelle Sicherungen in Azure erfolgen sollen. 

    Sie können tägliche, wöchentliche, monatliche und jährliche Sicherungen planen und Datum und Uhrzeit ihrer Ausführung festlegen. Sicherungen können bis zu zweimal täglich erfolgen. Bei jeder Ausführung eines Sicherungsauftrags wird ein Datenwiederherstellungspunkt in Azure anhand der Kopie der Sicherungsdaten erstellt, die auf dem Azure Backup Server-Datenträger gespeichert sind.

11. Wählen Sie auf der Seite **Onlineaufbewahrungsrichtlinie angeben** aus, wie die Wiederherstellungspunkte aus den täglichen, wöchentlichen, monatlichen und jährlichen Sicherungen in Azure aufbewahrt werden.

12. Geben Sie auf der Seite **Onlinereplikation wählen** an, wie die erste vollständige Replikation der Daten erfolgt. 

13. Überprüfen Sie unter **Zusammenfassung** Ihre Einstellungen. Wenn Sie auf **Gruppe erstellen** klicken, erfolgt die erstmalige Datenreplikation. Nach Abschluss der Datenreplikation wird der Status der Schutzgruppe auf der Seite **Status** als **OK** angezeigt. Der erstmalige Sicherungsauftrag erfolgt entsprechend den Einstellungen der Schutzgruppe.

## <a name="recover-file-data"></a>Wiederherstellen von Dateidaten

Verwenden Sie die Azure Backup Server-Konsole, um Daten auf dem virtuellen Computer wiederherzustellen.

1. Klicken Sie in der Azure Backup Server-Konsole auf der Navigationsleiste auf **Wiederherstellung**, und suchen Sie die Daten, die Sie wiederherstellen möchten. Wählen Sie die Daten im Ergebnisbereich aus.

2. Im Kalender im Abschnitt „Wiederherstellungspunkte“ bedeuten fett gedruckte Datumsangaben, dass Wiederherstellungspunkte verfügbar sind. Wählen Sie das Datum aus, für das die Wiederherstellung durchgeführt werden soll.

3. Wählen Sie im Bereich **Wiederherstellbares Element** das Element aus, das Sie wiederherstellen möchten.

4. Klicken Sie im Bereich **Aktionen** auf **Wiederherstellen**, um den Wiederherstellungs-Assistenten zu öffnen.

5. Sie können Daten wie folgt wiederherstellen:

    * **Am ursprünglichen Speicherort wiederherstellen**: Wenn der Clientcomputer über VPN verbunden ist, funktioniert diese Option nicht. Wählen Sie stattdessen einen anderen Speicherort aus, und kopieren Sie Daten von diesem Speicherort.
    * **An anderem Speicherort wiederherstellen**

6. Geben Sie die Wiederherstellungsoptionen an:

    * Wählen Sie für **Wiederherstellungsverhalten für vorhandene Versionen** entweder **Kopie erstellen**, **Überspringen** oder **Überschreiben** aus. „Überschreiben“ ist nur für das Wiederherstellen am ursprünglichen Speicherort verfügbar.
    * Wählen Sie für **Sicherheit wiederherstellen** entweder **Sicherheitseinstellungen des Zielcomputers anwenden** oder **Sicherheitseinstellungen der Wiederherstellungspunktversion anwenden**.
    * Klicken Sie für **Netzwerk-Bandbreiteneinschränkung** auf **Ändern**, um die Netzwerk-Bandbreiteneinschränkung zu aktivieren.
    * **Benachrichtigung**: Klicken Sie auf **Eine E-Mail senden, wenn diese Wiederherstellung abgeschlossen ist**, und geben Sie dann die Empfänger an, die die Benachrichtigung erhalten sollen. Trennen Sie die E-Mail-Adressen durch Kommas.
    * Klicken Sie auf **Weiter**, nachdem Sie Ihre Auswahl getroffen haben.

7. Überprüfen Sie Ihre Wiederherstellungseinstellungen, und klicken Sie auf **Wiederherstellen**. 

    > [!Note] 
    > Alle Synchronisierungsaufträge für das ausgewählte Wiederherstellungselement werden abgebrochen, während der Wiederherstellungsauftrag ausgeführt wird.
    >

Wenn Sie Modern Backup Storage(MBS) verwenden, wird die Wiederherstellung durch Endbenutzer für Dateiserver nicht unterstützt. Diese Art der Wiederherstellung weist eine Abhängigkeit vom Volumeschattenkopie-Dienst auf, den Modern Backup Storage nicht verwendet. Wenn die Wiederherstellung durch Endbenutzer aktiviert ist, führen Sie die folgenden Schritte aus, um Daten wiederherzustellen:

1. Navigieren Sie zu den geschützten Dateien, klicken Sie mit der rechten Maustaste auf den Dateinamen, und wählen Sie **Eigenschaften** aus.

2. Klicken Sie im Menü **Eigenschaften** auf **Vorherige Versionen**, und wählen Sie die Version, die Sie wiederherstellen möchten.

## <a name="view-azure-backup-server-with-a-vault"></a>Anzeigen von Azure Backup Server mit einem Tresor
Mit folgenden Schritten können Sie Azure Backup Server-Entitäten im Azure-Portal anzeigen:
1. Recovery Services-Tresor öffnen
2. Auf „Sicherungsinfrastruktur“ klicken
3. Sicherungsverwaltungsserver anzeigen

## <a name="see-also"></a>Weitere Informationen
Informationen zur Verwendung von Azure Backup Server zum Schützen anderer Workloads finden Sie in einem der folgenden Artikel:
- [Sichern einer SharePoint-Farm](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Sichern einer SQL Server-Instanz](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
