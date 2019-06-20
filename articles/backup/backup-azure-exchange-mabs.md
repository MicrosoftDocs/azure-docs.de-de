---
title: Sichern eines Exchange-Servers in Azure Backup mit Azure Backup Server
description: Erfahren Sie, wie Sie einen Exchange-Server mit Azure Backup Server in Azure Backup sichern.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 40541596b4da9e0590d497785afd7d6d7f4cbcb4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641475"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Sichern eines Exchange-Servers in Azure mit Azure Backup Server
In diesem Artikel wird beschrieben, wie Sie einen Microsoft Azure Backup Server (MABS) konfigurieren, um einen Microsoft Exchange-Server in Azure zu sichern.  

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie fortfahren, stellen Sie sicher, dass Azure Backup Server [installiert und vorbereitet](backup-azure-microsoft-azure-backup.md) ist.

## <a name="mabs-protection-agent"></a>MABS-Schutz-Agent
Führen Sie die folgenden Schritte aus, um den MABS-Schutz-Agent auf dem Exchange-Server zu installieren:

1. Stellen Sie sicher, dass die Firewalls richtig konfiguriert sind. Weitere Informationen finden Sie unter [Konfigurieren von Firewallausnahmen für den Agent](https://technet.microsoft.com/library/Hh758204.aspx).
2. Installieren Sie den Agent auf dem Exchange-Server, indem Sie in der MABS-Verwaltungskonsole auf **Verwaltung > Agents > Installieren** klicken. Ausführliche Schritte finden Sie unter [Installieren des MABS-Schutz-Agents](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396).

## <a name="create-a-protection-group-for-the-exchange-server"></a>Erstellen einer Schutzgruppe für den Exchange-Server
1. Klicken Sie in der MABS-Verwaltungskonsole auf **Schutz** und dann im Menüband des Tools auf **Neu**, um den Assistenten **Neue Schutzgruppe erstellen** zu öffnen.
2. Klicken Sie im Assistenten auf dem Bildschirm **Willkommen** auf **Weiter**.
3. Wählen Sie auf dem Bildschirm **Schutzgruppentyp auswählen** die Option **Server** aus, und klicken Sie auf **Weiter**.
4. Wählen Sie die Datenbank des Exchange Servers aus, die Sie schützen möchten, und klicken Sie auf **Weiter**.

   > [!NOTE]
   > Wenn Sie Exchange 2013 schützen, aktivieren Sie die [Exchange 2013-Voraussetzungen](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    Im folgenden Beispiel wird die Exchange 2010-Datenbank ausgewählt.

    ![Gruppenmitglieder auswählen](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Wählen Sie die Methoden zum Schützen der Daten aus.

    Geben Sie der Schutzgruppe einen Namen, und wählen Sie dann die beiden folgenden Optionen aus:

   * Kurzfristiger Schutz unter Verwendung eines Datenträgers
   * Onlineschutz
6. Klicken Sie auf **Weiter**.
7. Wählen Sie die Option **„Eseutil“ zum Prüfen der Datenintegrität ausführen** aus, wenn Sie die Integrität der Exchange-Server-Datenbanken überprüfen möchten.

    Nach dem Auswählen dieser Option wird auf dem MABS die Backupkonsistenz überprüft, um den E/A-Datenverkehr zu vermeiden, der durch das Ausführen des Befehls **eseutil** auf dem Exchange-Server generiert wird.

   > [!NOTE]
   > Zum Verwenden dieser Option müssen Sie die Dateien „Ese.dll“ und „Eseutil.exe“ auf dem MAB-Server in das Verzeichnis „C:\Programme\Microsoft Azure Backup\DPM\DPM\bin“ kopieren. Andernfalls wird der folgende Fehler ausgelöst:  
   > ![eseutil-Fehler](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klicken Sie auf **Weiter**.
9. Wählen Sie die Datenbank für **Kopiesicherung** aus, und klicken Sie auf **Weiter**.

   > [!NOTE]
   > Wenn Sie nicht für mindestens eine DAG-Kopie einer Datenbank die Option „Vollständige Sicherung“ auswählen, werden die Protokolle nicht abgeschnitten.
   >
   >
10. Konfigurieren Sie die Ziele für **Kurzfristige Sicherung**, und klicken Sie auf **Weiter**.
11. Überprüfen Sie den verfügbaren Speicherplatz, und klicken Sie auf **Weiter**.
12. Wählen Sie den Zeitpunkt aus, an dem der MAB-Server die erste Replikation erstellt, und klicken Sie auf **Weiter**.
13. Wählen Sie die Optionen für die Konsistenzüberprüfung aus, und klicken Sie auf **Weiter**.
14. Wählen Sie die Datenbank aus, die Sie unter Azure sichern möchten, und klicken Sie auf **Weiter**. Beispiel:

    ![Onlineschutzdaten angeben](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definieren Sie den Zeitplan für **Azure Backup**, und klicken Sie auf **Weiter**. Beispiel:

    ![Zeitplan für Onlinesicherung angeben](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Beachten Sie, dass die Online-Wiederherstellungspunkte auf den Wiederherstellungspunkten für die schnelle vollständige Wiederherstellung basieren. Daher müssen Sie den Online-Wiederherstellungspunkt nach dem Zeitpunkt einplanen, der für den Wiederherstellungspunkt für die schnelle vollständige Wiederherstellung angegeben wird.
    >
    >
16. Konfigurieren Sie die Aufbewahrungsrichtlinie für **Azure Backup**, und klicken Sie auf **Weiter**.
17. Wählen Sie eine Option für die Onlinereplikation aus, und klicken Sie auf **Weiter**.

    Wenn Sie über eine große Datenbank verfügen, kann es lange dauern, bis das erste Backup über das Netzwerk erstellt wird. Sie können ein Offlinebackup erstellen, um dieses Problem zu vermeiden.  

    ![Online-Aufbewahrungsrichtlinie angeben](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bestätigen Sie die Einstellungen, und klicken Sie anschließend auf **Gruppe erstellen**.
19. Klicken Sie auf **Schließen**.

## <a name="recover-the-exchange-database"></a>Wiederherstellen der Exchange-Datenbank
1. Klicken Sie zum Wiederherstellen einer Exchange-Datenbank in der MABS-Administratorkonsole auf **Wiederherstellung**.
2. Suchen Sie nach der Exchange-Datenbank, die Sie wiederherstellen möchten.
3. Wählen Sie in der Dropdownliste *Wiederherstellungszeit* einen Online-Wiederherstellungspunkt aus.
4. Klicken Sie auf **Wiederherstellen**, um den **Wiederherstellungs-Assistenten** zu starten.

Für Online-Wiederherstellungspunkte gibt es fünf Wiederherstellungstypen:

* **Im ursprünglichen Exchange Server-Speicherort wiederherstellen:** Die Daten werden auf dem ursprünglichen Exchange Server wiederhergestellt.
* **In einer anderen Datenbank auf einem Exchange Server wiederherstellen:** Die Daten werden in einer anderen Datenbank auf einem anderen Exchange Server wiederhergestellt.
* **In einer Wiederherstellungsdatenbank wiederherstellen:** Die Daten werden in einer Exchange-Wiederherstellungsdatenbank (RDB) wiederhergestellt.
* **In einen Netzwerkordner kopieren:** Die Daten werden in einem Netzwerkordner wiederhergestellt.
* **Auf Band kopieren:** Wenn Sie eine Bandbibliothek oder ein eigenständiges Bandlaufwerk angeschlossen und auf dem MABS konfiguriert haben, wird der Wiederherstellungspunkt auf ein freies Band kopiert.

    ![Onlinereplikation auswählen](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nächste Schritte
* [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)
