---
title: Sichern von SQL Server-Datenbanken in Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie SQL Server in Azure sichern.
services: backup
author: dcurwin
manager: ''
ms.service: backup
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: dacurwin
ms.openlocfilehash: bfe48fb1bf6a361ce79d0ddc5281a6380a5367e4
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016115"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Sichern von SQL Server-Datenbanken auf virtuellen Azure-Computern



In diesem Artikel wird veranschaulicht, wie Sie eine SQL Server-Datenbank, die auf einem virtuellen Azure-Computer ausgeführt wird, in einem Azure Backup Recovery Services-Tresor sichern. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen und Konfigurieren eines Tresors
> * Erkennen von Datenbanken und Einrichten von Sicherungen
> * Einrichten des automatischen Schutzes für Datenbanken
> * Ausführen einer Ad-hoc-Sicherung


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Ihre SQL Server-Datenbank sichern können, müssen folgende Bedingungen erfüllt sein:

1. Identifizieren oder [erstellen](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) Sie einen Recovery Services-Tresor in derselben Region oder demselben Gebietsschema wie der virtuelle Computer, der die SQL Server-Instanz hostet.
2. [Überprüfen Sie die erforderlichen Berechtigungen für virtuelle Computer](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) zum Sichern der SQL-Datenbanken.
3. Stellen Sie sicher, dass der virtuelle Computer über [Netzwerkkonnektivität](backup-sql-server-database-azure-vms.md#establish-network-connectivity) verfügt.
4. Vergewissern Sie sich, dass die SQL Server-Datenbanken in Übereinstimmung mit den [Benennungsrichtlinien](#verify-database-naming-guidelines-for-azure-backup) für Azure Backup benannt sind.
5. Stellen Sie sicher, dass Sie keine anderen Sicherungslösungen für die Datenbank aktiviert sind. Deaktivieren Sie alle anderen SQL Server-Sicherungen, bevor Sie dieses Szenario einrichten. Sie können Azure Backup für einen virtuellen Azure-Computer zusammen mit Azure Backup für eine auf dem virtuellen Computer ausgeführte SQL Server-Datenbank ohne Konflikte aktivieren.


### <a name="establish-network-connectivity"></a>Herstellen der Netzwerkverbindung

Der virtuelle SQL Server-Computer benötigt für alle Vorgänge eine Verbindung, um auf die öffentlichen IP-Adressen von Azure zuzugreifen. VM-Vorgänge (Ermitteln von Datenbanken, Konfigurieren von Sicherungen, Planen von Sicherungen, Zurücksetzen von Wiederherstellungspunkten usw.) schlagen ohne Verbindung zu den öffentlichen IP-Adressen fehl. Richten Sie die Verbindung mit einer der folgenden Optionen ein:

- **Zulassen der IP-Adressbereiche des Azure-Rechenzentrums**: Lassen Sie die [IP-Adressbereiche](https://www.microsoft.com/download/details.aspx?id=41653) im Download zu. Verwenden Sie für den Zugriff auf eine Netzwerksicherheitsgruppe (NSG) das Cmdlet **Set-AzureNetworkSecurityRule**.
- **Bereitstellen eines HTTP-Proxyservers für das Weiterleiten von Datenverkehr**: Wenn Sie eine SQL Server-Datenbank auf einem virtuellen Azure-Computer sichern, verwendet die Sicherungserweiterung auf dem virtuellen Computer die HTTPS-APIs, um Verwaltungsbefehle an Azure Backup und Daten an Azure Storage zu senden. Die Sicherungserweiterung verwendet auch Azure Active Directory (Azure AD) zur Authentifizierung. Leiten Sie den Datenverkehr der Sicherungserweiterung für diese drei Dienste über den HTTP-Proxy weiter. Die Erweiterungen sind die einzigen Komponenten, die für den Zugriff auf das öffentliche Internet konfiguriert sind.

Jede Option hat Vor- und Nachteile.

**Option** | **Vorteile** | **Nachteile**
--- | --- | ---
Zulassen von IP-Adressbereichen | Keine zusätzlichen Kosten. | Komplexe Verwaltung, da sich die IP-Adressbereiche im Laufe der Zeit ändern. <br/><br/> Zugriff auf alle Azure-Dienste, nicht nur auf Azure Storage.
Verwenden eines HTTP-Proxys   | Feinsteuerung im Proxy über die Speicher-URLs ist zulässig. <br/><br/> Zentraler Punkt für Internetzugriff auf virtuelle Computer. <br/><br/> Unterliegt keinen Azure-IP-Adressänderungen. | Zusätzliche Kosten für das Ausführen einer VM mit Proxysoftware.

### <a name="set-vm-permissions"></a>Einrichten von Berechtigungen für virtuelle Computer

Azure Backup führt eine Reihe von Aktionen durch, wenn Sie die Sicherung für eine SQL Server-Datenbank konfigurieren:

- Fügt die Erweiterung **AzureBackupWindowsWorkload** hinzu.
- Azure Backup erstellt das Konto **NT SERVICE\AzureWLBackupPluginSvc**, um Datenbanken auf dem virtuellen Computer zu ermitteln. Dieses Konto wird zum Sichern und Wiederherstellen verwendet und erfordert SQL-Systemadministratorberechtigungen.
- Azure Backup verwendet das Konto **NT AUTHORITY\SYSTEM** für die Ermittlung von Datenbanken und Anfragen an Datenbanken. Dieses Konto muss also über eine öffentliche Anmeldung in SQL verfügen.

Wenn Sie den virtuellen SQL Server-Computer nicht in Azure Marketplace erstellt haben, erhalten Sie möglicherweise den Fehler **UserErrorSQLNoSysadminMembership**. Gehen Sie in diesem Fall [wie folgt vor](backup-azure-sql-database.md#fix-sql-sysadmin-permissions):

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Überprüfen der Benennungsrichtlinien für SQL-Datenbanken in Azure Backup

Vermeiden Sie folgende Zeichen in Datenbanknamen:

  * Nachgestellte/führende Leerzeichen
  * Führende Ausrufezeichen
  * Schließende eckige Klammer „]“
  * Datenbanknamen, die mit „F:/“ beginnen

Obwohl Aliasing für nicht unterstützte Zeichen aus Azure-Tabellen möglich ist, wird empfohlen, diese Zeichen zu vermeiden. [Weitere Informationen](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Ermitteln von SQL Server-Datenbanken

Ermitteln Sie auf dem virtuellen Computer ausgeführte Datenbanken.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Recovery Services-Tresor, mit dem Sie die Datenbank sichern.

2. Wählen Sie auf dem Dashboard des **Recovery Services-Tresors** **Sichern** aus.

   ![Auswählen von „Sichern“, um Menü „Sicherungsziel“ zu öffnen](./media/backup-azure-sql-database/open-backup-menu.png)

3. Legen Sie in **Sicherungsziel** die Einstellung **Wo wird die Workload ausgeführt?** auf **Azure** (Standard) fest.

4. Wählen Sie unter **Was möchten Sie sichern?** die Option **SQL Server in Azure VM** aus.

    ![Auswählen von „SQL Server in Azure-VM“ für die Sicherung](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Wählen Sie in **Sicherungsziel** > **DBs in VMs ermitteln** die Option **Ermittlung starten** aus, um nach nicht geschützten virtuellen Computern im Abonnement zu suchen. Die Suche kann je nach Anzahl von ungeschützten virtuellen Computern im Abonnement eine Weile dauern.

   - Nicht geschützte virtuelle Computer sollten nach der Ermittlung sortiert nach Name und Ressourcengruppe in der Liste angezeigt werden.
   - Wenn ein virtueller Computer nicht wie erwartet aufgeführt wird, überprüfen Sie, ob er bereits in einem Tresor gesichert wird.
   - Mehrere virtuelle Computer können den gleichen Namen aufweisen, gehören dann aber verschiedenen Ressourcengruppen an.

     ![Ausstehende Sicherung bei der Suche nach Datenbanken in VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Wählen Sie in der Liste der virtuellen Computer den virtuellen Computer mit der SQL Server-Datenbank und dann **Datenbankermittlung** aus.

7. Verfolgen Sie die Datenbankermittlung im Bereich **Benachrichtigungen**. Je nachdem, wie viele Datenbanken sich auf dem virtuellen Computer befinden, kann es eine Weile dauern, bis der Auftrag abgeschlossen ist. Wenn die ausgewählten Datenbanken ermittelt wurden, wird eine Erfolgsmeldung angezeigt.

    ![Meldung über erfolgreiche Bereitstellung](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup ermittelt alle SQL Server-Datenbanken auf dem virtuellen Computer. Während der Ermittlung geschieht im Hintergrund Folgendes:

    - Azure Backup registriert den virtuellen Computer beim Tresor für die Workload-Sicherung. Alle Datenbanken auf dem registrierten virtuellen Computer können nur in diesem Tresor gesichert werden.
    - Azure Backup installiert die Erweiterung **AzureBackupWindowsWorkload** auf dem virtuellen Computer. In der SQL-Datenbank ist kein Agent installiert.
    - Azure Backup erstellt das Dienstkonto **NT Service\AzureWLBackupPluginSvc** auf dem virtuellen Computer.
      - Für alle Sicherungs- und Wiederherstellungsvorgänge wird das Dienstkonto verwendet.
      - **NT Service\AzureWLBackupPluginSvc** erfordert SQL-Systemadministratorberechtigungen. Die Erweiterung **SqlIaaSExtension** ist auf allen in Azure Marketplace erstellten SQL Server-VMs vorinstalliert. Die Erweiterung **AzureBackupWindowsWorkload** verwendet **SQLIaaSExtension**, um erforderliche Berechtigungen automatisch abzurufen.
    - Wenn Sie den virtuellen Computer nicht im Marketplace erstellt haben, ist die Erweiterung **SqlIaaSExtension** nicht auf dem virtuellen Computer installiert, und der Ermittlungsvorgang schlägt mit der Fehlermeldung **UserErrorSQLNoSysAdminMembership** fehl. Führen Sie die [Anleitungen](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) aus, um dieses Problem zu beheben.

        ![Auswählen der VM und der Datenbank](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurieren der Sicherung  

Konfigurieren Sie die Sicherung wie folgt:

1. Wählen Sie in **Sicherungsziel** die Option **Sicherung konfigurieren** aus.

   ![Auswählen von „Sicherung konfigurieren“](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Klicken Sie auf **Sicherung konfigurieren**, um das Blatt **Elemente für die Sicherung auswählen** anzuzeigen. Alle registrierten Verfügbarkeitsgruppen und eigenständigen Computer mit SQL Server werden aufgeführt. Erweitern Sie das Chevron links von der Zeile, um alle ungeschützten Datenbanken dieser Instanz oder der Always On-Verfügbarkeitsgruppe anzuzeigen.  

    ![Anzeigen aller SQL Server-Instanzen mit eigenständigen Datenbanken](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Wählen Sie alle Datenbanken aus, die Sie schützen möchten, und wählen Sie dann **OK**.

   ![Schützen der Datenbank](./media/backup-azure-sql-database/select-database-to-protect.png)

   Zum Optimieren von Sicherungslasten legt Azure Backup die maximale Anzahl von Datenbanken in einem Sicherungsauftrag auf 50 fest.

    
     * Alternativ können Sie durch Auswahl der Option **EIN** in der entsprechenden Dropdownliste in der Spalte **AUTOMATISCHER SCHUTZ** den automatischen Schutz für die gesamte Instanz oder Always On-Verfügbarkeitsgruppe aktivieren. Das Feature des automatischen Schutzes ermöglicht nicht nur den gleichzeitigen Schutz aller vorhandenen Datenbanken, sondern schützt auch automatisch alle neuen Datenbanken, die dieser Instanz oder Verfügbarkeitsgruppe in Zukunft hinzugefügt werden.  

4. Klicken Sie auf **OK**, um das Blatt **Sicherungsrichtlinie** zu öffnen.

    ![Aktivieren des automatischen Schutzes für die AlwaysOn-Verfügbarkeitsgruppe](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Wählen Sie unter  **Sicherungsrichtlinie auswählen** eine Richtlinie aus, und klicken Sie dann auf  **OK**.

   - Auswahl der Standardrichtlinie: HourlyLogBackup.
   - Auswählen einer vorhandenen, zuvor für SQL erstellten Sicherungsrichtlinie
   - Definieren einer neuen Richtlinie basierend auf Ihrer RPO und Ihrer Beibehaltungsdauer

     ![Auswählen der Sicherungsrichtlinie](./media/backup-azure-sql-database/select-backup-policy.png)

6. Wählen Sie im Menü  **Sicherung** die Option  **Sicherung aktivieren**.

    ![Aktivieren der ausgewählten Sicherungsrichtlinie](./media/backup-azure-sql-database/enable-backup-button.png)

7. Sie können den Konfigurationsprozess im Portalbereich  **Benachrichtigungen**  nachverfolgen.

    ![Benachrichtigungsbereich](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Eine Sicherungsrichtlinie legt fest, wann Sicherungen erstellt und wie lange sie aufbewahrt werden.

- Eine Richtlinie wird auf Tresorebene erstellt.
- Mehrere Tresore können die gleiche Sicherungsrichtlinie verwenden. Allerdings müssen Sie die Sicherungsrichtlinie auf jeden Tresor anwenden.
- Wenn Sie eine Sicherungsrichtlinie erstellen, entspricht eine tägliche vollständige Sicherung der Standardeinstellung.
- Sie können eine differenzielle Sicherung nur hinzufügen, wenn Sie für die vollständige Sicherung festlegen, dass diese wöchentlich erfolgt.
- [Erfahren Sie mehr](backup-architecture.md#sql-server-backup-types) über verschiedene Arten von Sicherungsrichtlinien.

So erstellen Sie eine Sicherungsrichtlinie

1. Klicken Sie im Tresor auf **Sicherungsrichtlinien** > **Hinzufügen**.
2. Klicken Sie im Menü **Hinzufügen** auf **SQL Server in Azure-VM**. So definieren Sie den Richtlinientyp:

   ![Auswählen eines Richtlinientyps für die neue Sicherungsrichtlinie](./media/backup-azure-sql-database/policy-type-details.png)

3. Geben Sie unter **Richtlinienname** einen Namen für die neue Richtlinie ein.
4. Wählen Sie in **Richtlinie für vollständige Sicherung** für **Sicherungshäufigkeit** **Täglich** oder **Wöchentlich** aus.

   - Wählen Sie für **Täglich** die Uhrzeit und die Zeitzone für den Beginn des Sicherungsauftrags aus.
   - Sie müssen eine vollständige Sicherung ausführen, da die Option **Vollständige Sicherung** nicht deaktiviert werden kann.
   - Klicken Sie auf **Vollständige Sicherung**, um die Richtlinie anzuzeigen.
   - Sie können keine differenziellen Sicherungen für tägliche vollständige Sicherungen erstellen.
   - Wählen Sie für **Wöchentlich** den Wochentag, die Uhrzeit und die Zeitzone für den Beginn des Sicherungsauftrags aus.

     ![Neue Felder für Sicherungsrichtlinien](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Für **Beibehaltungsdauer** sind standardmäßig alle Optionen aktiviert. Deaktivieren Sie alle Optionen für die Beibehaltungsdauer, die Sie nicht nutzen möchten, und legen Sie die zu verwendenden Intervalle fest.

    - Der minimale Aufbewahrungszeitraum für alle Arten von Sicherungen (vollständig/differenziell/Protokoll) beträgt sieben Tage.
    - Wiederherstellungspunkte werden unter Berücksichtigung ihrer Beibehaltungsdauer mit einer Markierung versehen. Wenn Sie beispielsweise eine tägliche vollständige Sicherung wählen, wird pro Tag nur eine vollständige Sicherung ausgelöst.
    - Die Sicherung für einen bestimmten Tag wird auf Grundlage der wöchentlichen Beibehaltungsdauer und Ihrer wöchentlichen Aufbewahrungseinstellung markiert und beibehalten.
    - Mit der monatlichen und jährlichen Beibehaltungsdauer verhält es sich ähnlich.

   ![Intervalleinstellungen für Beibehaltungsdauer](./media/backup-azure-sql-database/retention-range-interval.png)

6. Wählen Sie im Menü **Richtlinie für vollständige Sicherung** **OK** aus, um die Einstellungen zu übernehmen.
7. Um eine Richtlinie für eine differenzielle Sicherung hinzuzufügen, wählen Sie **Differenzielle Sicherung** aus.

   ![Intervalleinstellungen für Beibehaltungsdauer](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Öffnen des Menüs „Richtlinie für differenzielle Sicherung“](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Wählen Sie in **Richtlinie für differenzielle Sicherung** die Option **Aktivieren** aus, um die Einstellungen für Häufigkeit und Beibehaltung vorzunehmen.

    - Pro Tag kann höchstens eine differenzielle Sicherung ausgelöst werden.
    - Differenzielle Sicherungen können maximal 180 Tage aufbewahrt werden. Wenn Sie eine längere Aufbewahrung wünschen, müssen Sie vollständige Sicherungen verwenden.

9. Wählen Sie **OK** aus, um die Richtlinie zu speichern und zum Hauptmenü **Sicherungsrichtlinie** zurückzukehren.

10. Um eine Richtlinie für eine Transaktionsprotokollsicherung hinzuzufügen, wählen Sie **Protokollsicherung** aus.
11. Wählen Sie in **Protokollsicherung** die Option **Aktivieren** aus, und legen Sie die Einstellungen für Häufigkeit und Aufbewahrung fest. Transaktionsprotokollsicherungen können alle 15 Minuten erfolgen und bis zu 35 Tage aufbewahrt werden.
12. Wählen Sie **OK** aus, um die Richtlinie zu speichern und zum Hauptmenü **Sicherungsrichtlinie** zurückzukehren.

    ![Bearbeiten der Richtlinie für die Transaktionsprotokollsicherung](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Legen Sie im Menü **Sicherungsrichtlinie** fest, ob die **SQL-Sicherungskomprimierung** aktiviert wird.
    - Die Komprimierung ist standardmäßig deaktiviert.
    - Auf dem Back-End verwendet Azure Backup die native SQL-Sicherungskomprimierung.

14. Nachdem Sie die Sicherungsrichtlinie bearbeitet haben, wählen Sie **OK** aus.

## <a name="run-an-ad-hoc-backup"></a>Ausführen einer Ad-hoc-Sicherung

1. Wählen Sie in Ihrem Recovery Services-Tresor Sicherungselemente aus.
2. Klicken Sie auf „SQL Server in Azure-VM“.
3. Klicken Sie mit der rechten Maustaste auf eine Datenbank, und wählen Sie „Jetzt sichern“ aus.
4. Wählen Sie den Sicherungstyp (Vollständig/Differenziell/Protokoll/Nur vollständig kopieren) und die Komprimierung (Aktivieren/Deaktivieren) aus.
5. Wählen Sie „OK“ aus, um die Sicherung zu starten.
6. Navigieren Sie zum Überwachen des Sicherungsauftrags zu Ihrem Recovery Services-Tresor, und wählen Sie „Sicherungsaufträge“ aus.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie das Azure-Portal zu folgenden Zwecken verwendet:

> [!div class="checklist"]
> * Erstellen und Konfigurieren eines Tresors
> * Erkennen von Datenbanken und Einrichten von Sicherungen
> * Einrichten des automatischen Schutzes für Datenbanken
> * Ausführen einer Ad-hoc-Sicherung

Im nächsten Tutorial stellen Sie einen virtuellen Azure-Computer von einem Datenträger wieder her.

> [!div class="nextstepaction"]
> [Wiederherstellen von SQL Server-Datenbanken auf virtuellen Azure-Computern](./restore-sql-database-azure-vm.md)
 

