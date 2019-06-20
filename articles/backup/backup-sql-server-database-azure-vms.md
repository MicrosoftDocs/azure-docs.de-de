---
title: Sichern von SQL Server-Datenbanken auf virtuellen Azure-Computern | Microsoft-Dokumentation
description: Erfahren Sie, wie sie SQL Server-Datenbanken auf virtuellen Azure-Computern sichern
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 0307dc5c83782119f6c10279563b8b9f0a999d28
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236885"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Sichern von SQL Server-Datenbanken auf virtuellen Azure-Computern

SQL Server-Datenbanken sind kritische Workloads, die eine niedrige Recovery Point Objective (RPO) und lange Aufbewahrungszeit erfordern. Sie können auf virtuellen Azure-Computern (VMs) ausgeführte SQL Server-Datenbanken mithilfe von [Azure Backup](backup-overview.md) sichern.

In diesem Artikel wird veranschaulicht, wie eine SQL Server-Datenbank, die auf einer Azure-VM ausgeführt wird, in einem Azure Backup Recovery Services-Tresor gesichert wird.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Konfigurieren eines Tresors
> * Ermitteln von Datenbanken und Einrichten von Sicherungen
> * Einrichten des automatischen Schutzes für Datenbanken


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie eine SQL Server-Datenbank sichern können, müssen folgende Kriterien erfüllt sein:

1. Bestimmen oder [erstellen](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) Sie einen Recovery Services-Tresor in derselben Region oder im selben Gebietsschema wie die VM, die die SQL Server-Instanz hostet.
2. Überprüfen Sie die [erforderlichen Berechtigungen für virtuelle Computer](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) zum Sichern von SQL Server-Datenbanken.
3. Stellen Sie sicher, dass der virtuelle Computer über [Netzwerkkonnektivität](backup-sql-server-database-azure-vms.md#establish-network-connectivity) verfügt.
4. Vergewissern Sie sich, dass die SQL Server-Datenbanken die [Benennungsrichtlinien für Datenbanken für Azure Backup](#database-naming-guidelines-for-azure-backup) befolgen.
5. Stellen Sie sicher, dass keine anderen Sicherungslösungen für die Datenbank aktiviert sind. Deaktivieren Sie alle anderen SQL Server-Sicherungen, bevor Sie die Datenbank sichern.

> [!NOTE]
> Sie können Azure Backup für eine Azure-VM und auch für eine auf der VM ausgeführte SQL Server-Datenbank ohne Konflikte aktivieren.


### <a name="establish-network-connectivity"></a>Herstellen der Netzwerkverbindung

Eine SQL Server-VM benötigt für alle Vorgänge eine Verbindung mit öffentlichen IP-Adressen von Azure. VM-Vorgänge (Ermitteln von Datenbanken, Konfigurieren von Sicherungen, Planen von Sicherungen, Zurücksetzen von Wiederherstellungspunkten usw.) schlagen ohne Verbindung mit öffentlichen IP-Adressen von Azure fehl.

Stellen Sie die Verbindung mithilfe einer der folgenden Optionen her:

- **Zulassen der IP-Adressbereiche des Azure-Rechenzentrums**. Diese Option lässt [IP-Adressbereiche](https://www.microsoft.com/download/details.aspx?id=41653) im Download zu. Verwenden Sie für den Zugriff auf eine Netzwerksicherheitsgruppe (NSG) das Cmdlet Set-AzureNetworkSecurityRule. Wenn Ihre Liste sicherer Empfänger nur regionsspezifische IP-Adressen enthält, müssen Sie auch das Diensttag „Azure Active Directory“ (Azure AD) der Liste sicherer Empfänger aktualisieren, um die Authentifizierung zu ermöglichen.

- **Zulassen des Zugriffs mithilfe von NSG-Tags**. Wenn Sie NSGs verwenden, um die Konnektivität einzuschränken, fügt diese Option Ihrer NSG eine Regel hinzu, die den ausgehenden Zugriff auf Azure Backup über das Tag „AzureBackup“ ermöglicht. Zusätzlich zu diesem Tag benötigen Sie auch entsprechende [Regeln](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) für Azure AD und Azure Storage, um Konnektivität für Authentifizierung und Datenübertragung zu ermöglichen. Das Tag „AzureBackup“ ist derzeit nur in PowerShell verfügbar. So erstellen Sie eine Regel mit dem Tag „AzureBackup“

    - Hinzufügen von Azure-Anmeldeinformationen und Aktualisieren der nationalen Clouds<br/>
    `Add-AzureRmAccount`

    - Auswählen des NSG-Abonnements<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Auswählen der NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Hinzufügen der Ausgangsregel zum Zulassen des Diensttags „Azure Backup“<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Speichern der NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Zulassen des Zugriffs mithilfe von Azure Firewall-Tags**. Wenn Sie Azure Firewall verwenden, erstellen Sie eine Anwendungsregel mithilfe des [FQDN-Tags](https://docs.microsoft.com/azure/firewall/fqdn-tags) „AzureBackup“. Es erlaubt den ausgehenden Zugriff auf Azure Backup.
- **Bereitstellen eines HTTP-Proxyservers für das Weiterleiten von Datenverkehr**. Wenn Sie eine SQL Server-Datenbank auf einem virtuellen Azure-Computer sichern, verwendet die Sicherungserweiterung auf dem virtuellen Computer die HTTPS-APIs, um Verwaltungsbefehle an Azure Backup und Daten an Azure Storage zu senden. Die Sicherungserweiterung verwendet auch Azure AD zur Authentifizierung. Leiten Sie den Datenverkehr der Sicherungserweiterung für diese drei Dienste über den HTTP-Proxy weiter. Die Erweiterungen sind die einzigen Komponenten, die für den Zugriff auf das öffentliche Internet konfiguriert sind.

Die Konnektivitätsoptionen haben u.a. die folgenden Vor- und Nachteile:

**Option** | **Vorteile** | **Nachteile**
--- | --- | ---
Zulassen von IP-Adressbereichen | Keine zusätzlichen Kosten | Komplexe Verwaltung, da sich die IP-Adressbereiche im Laufe der Zeit ändern <br/><br/> Zugriff auf alle Azure-Dienste, nicht nur auf Azure Storage
Verwenden von NSG-Diensttags | Einfachere Verwaltung, da Bereichsänderungen automatisch zusammengeführt werden <br/><br/> Keine zusätzlichen Kosten <br/><br/> | Nur mit NSGs möglich <br/><br/> Zugriff auf den gesamten Dienst
Verwenden von FQDN-Tags von Azure Firewall | Einfachere Verwaltung, da die erforderlichen FQDNs automatisch verwaltet werden | Nur mit Azure Firewall möglich
Verwenden eines HTTP-Proxys | Feinsteuerung im Proxy über die Speicher-URLs zulässig <br/><br/> Zentraler Internetzugriffspunkt für VMs <br/><br/> Unterliegt keinen Azure-IP-Adressänderungen | Zusätzliche Kosten für das Ausführen einer VM mit der Proxysoftware

### <a name="set-vm-permissions"></a>Einrichten von Berechtigungen für virtuelle Computer

Wenn Sie eine Sicherung für eine SQL Server-Datenbank konfigurieren, führt Azure Backup die folgenden Schritte aus:

- Fügt die Erweiterung AzureBackupWindowsWorkload hinzu.
- Erstellt das Konto „NT SERVICE\AzureWLBackupPluginSvc“, um Datenbanken auf dem virtuellen Computer zu ermitteln. Dieses Konto wird zum Sichern und Wiederherstellen verwendet und erfordert Systemadministratorberechtigungen für SQL Server.
- Ermittelt auf einer VM ausgeführte Datenbanken. Azure Backup verwendet das Konto NT AUTHORITY\SYSTEM. Dieses Konto muss eine öffentliche Anmeldung in SQL Server ermöglichen.

Wenn Sie die SQL Server-VM nicht in Azure Marketplace erstellt haben, erhalten Sie möglicherweise den Fehler UserErrorSQLNoSysadminMembership. Weitere Informationen finden Sie finden Sie im Artikel [Informationen zur SQL Server-Sicherung auf virtuellen Azure-Computern](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) im Abschnitt „Funktionsaspekte und Einschränkungen“.

### <a name="database-naming-guidelines-for-azure-backup"></a>Benennungsrichtlinien für Datenbanken in Azure Backup

Verwenden Sie in Datenbanknamen nicht die folgenden Elemente:

  * Nachstehende und führende Leerzeichen
  * Nachstehende Ausrufezeichen (!)
  * Schließende eckige Klammern (])
  * Semikolon ';'
  * Schrägstrich '/'

Obwohl Aliase für nicht unterstützte Zeichen möglich ist, wird empfohlen, sie zu vermeiden. Weitere Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Ermitteln von SQL Server-Datenbanken

Ermitteln von auf einer VM ausgeführten Datenbanken:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Recovery Services-Tresor, mit dem Sie die Datenbank sichern.

2. Wählen Sie **Sichern** im Dashboard von **Recovery Services-Tresor** aus.

   ![Auswählen von „Sichern“, um Menü „Sicherungsziel“ zu öffnen](./media/backup-azure-sql-database/open-backup-menu.png)

3. Legen Sie unter **Sicherungsziel** die Einstellung **Wo wird die Workload ausgeführt?** auf **Azure** fest.

4. Wählen Sie unter **Was möchten Sie sichern?** die Option **SQL Server in Azure VM** aus.

    ![Auswählen von „SQL Server in Azure-VM“ für die Sicherung](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Wählen Sie in **Sicherungsziel** > **DBs in VMs ermitteln** die Option **Ermittlung starten** aus, um nach nicht geschützten virtuellen Computern im Abonnement zu suchen. Diese Suche kann je nach Anzahl ungeschützter VMs im Abonnement eine Weile dauern.

   - Nicht geschützte virtuelle Computer sollten nach der Ermittlung sortiert nach Name und Ressourcengruppe in der Liste angezeigt werden.
   - Wenn eine VM nicht wie erwartet aufgeführt wird, prüfen Sie, ob sie bereits in einem Tresor gesichert wird.
   - Mehrere VMs können den gleichen Namen aufweisen, gehören dann aber verschiedenen Ressourcengruppen an.

     ![Ausstehende Sicherung bei der Suche nach Datenbanken in VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Wählen Sie in der Liste der virtuellen Computer den virtuellen Computer mit der SQL Server-Datenbank und dann **Datenbankermittlung** aus.

7. Verfolgen Sie die Datenbankermittlung im Bereich **Benachrichtigungen**. Der Zeitaufwand für diese Aktion hängt von der Anzahl von Datenbanken auf VMs ab. Wenn die ausgewählten Datenbanken ermittelt wurden, wird eine Erfolgsmeldung angezeigt.

    ![Meldung über erfolgreiche Bereitstellung](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup ermittelt alle SQL Server-Datenbanken auf dem virtuellen Computer. Während der Ermittlung geschieht im Hintergrund Folgendes:

    - Azure Backup registriert die VM beim Tresor für die Sicherung der Workload. Alle Datenbanken auf der registrierten VM können nur in diesem Tresor gesichert werden.
    - Azure Backup installiert die Erweiterung AzureBackupWindowsWorkload auf der VM. In einer SQL Server-Datenbank ist kein Agent installiert.
    - Azure Backup erstellt das Dienstkonto „NT Service\AzureWLBackupPluginSvc“ auf der VM.
      - Für alle Sicherungs- und Wiederherstellungsvorgänge wird das Dienstkonto verwendet.
      - „NT Service\AzureWLBackupPluginSvc erfordert“ Systemadministratorberechtigungen für SQL Server. Die Erweiterung SqlIaaSExtension ist auf allen in Azure Marketplace erstellten SQL Server-VMs vorinstalliert. Die Erweiterung AzureBackupWindowsWorkload verwendet SQLIaaSExtension, um erforderliche Berechtigungen automatisch abzurufen.
    - Wenn Sie die VM nicht im Marketplace erstellt haben, ist die Erweiterung SqlIaaSExtension nicht auf der VM installiert, und der Ermittlungsvorgang schlägt mit der Fehlermeldung UserErrorSQLNoSysAdminMembership fehl. Befolgen Sie die [Anleitungen](backup-azure-sql-database.md#fix-sql-sysadmin-permissions), um dieses Problem zu beheben.

        ![Auswählen der VM und der Datenbank](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurieren der Sicherung  

1. In **Sicherungsziel** > **Schritt 2: Sicherung konfigurieren** wählen Sie **Sicherung konfigurieren** aus.

   ![Auswählen von „Sicherung konfigurieren“](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Unter **Elemente für die Sicherung auswählen** sehen Sie alle registrierten Verfügbarkeitsgruppen und eigenständigen SQL Server-Instanzen. Klicken Sie auf den Pfeil links neben einer Zeile, um die Liste aller ungeschützten Datenbanken in dieser Instanz oder Always On-Verfügbarkeitsgruppe zu erweitern.  

    ![Anzeigen aller SQL Server-Instanzen mit eigenständigen Datenbanken](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Wählen Sie alle Datenbanken, die Sie schützen möchten, und klicken Sie dann auf **OK**.

   ![Schützen der Datenbank](./media/backup-azure-sql-database/select-database-to-protect.png)

   Zum Optimieren von Sicherungslasten legt Azure Backup die maximale Anzahl von Datenbanken in einem Sicherungsauftrag auf 50 fest.

     * Zum Schützen von mehr als 50 Datenbanken konfigurieren Sie mehrere Sicherungen.
     * So aktivieren Sie [](#enable-auto-protection) die gesamte Instanz oder die Always On-Verfügbarkeitsgruppe. Wählen Sie in der Dropdownliste **AUTOPROTECT** **EIN** und dann **OK** aus.

    > [!NOTE]
    > Das Feature [Automatischer Schutz](#enable-auto-protection) ermöglicht nicht nur den gleichzeitigen Schutz aller vorhandenen Datenbanken, sondern schützt auch automatisch alle neuen Datenbanken, die dieser Instanz oder Verfügbarkeitsgruppe hinzugefügt werden.  

4. Klicken Sie auf **OK**, um **Sicherungsrichtlinie** zu öffnen.

    ![Aktivieren des automatischen Schutzes für die Always On-Verfügbarkeitsgruppe](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Wählen Sie in **Sicherungsrichtlinie**eine Richtlinie, und klicken Sie dann auf  **OK**.

   - Wählen Sie als Standardrichtlinie HourlyLogBackup aus.
   - Auswählen einer vorhandenen, zuvor für SQL erstellten Sicherungsrichtlinie
   - Definieren einer neuen Richtlinie basierend auf Ihrer RPO und Ihrer Beibehaltungsdauer

     ![Auswählen der Sicherungsrichtlinie](./media/backup-azure-sql-database/select-backup-policy.png)

6. Wählen Sie in  **Sicherung** die Option  **Sicherung aktivieren** aus.

    ![Aktivieren der ausgewählten Sicherungsrichtlinie](./media/backup-azure-sql-database/enable-backup-button.png)

7. Sie können den Konfigurationsprozess im Portalbereich  **Benachrichtigungen**  nachverfolgen.

    ![Benachrichtigungsbereich](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Eine Sicherungsrichtlinie legt fest, wann Sicherungen erstellt und wie lange sie aufbewahrt werden.

- Eine Richtlinie wird auf Tresorebene erstellt.
- Mehrere Tresore können die gleiche Sicherungsrichtlinie verwenden. Allerdings müssen Sie die Sicherungsrichtlinie auf jeden Tresor anwenden.
- Wenn Sie eine Sicherungsrichtlinie erstellen, entspricht eine tägliche vollständige Sicherung der Standardeinstellung.
- Sie können eine differenzielle Sicherung nur hinzufügen, wenn Sie für die vollständige Sicherung festlegen, dass diese wöchentlich erfolgt.
- Erfahren Sie mehr über [verschiedene Arten von Sicherungsrichtlinien](backup-architecture.md#sql-server-backup-types).

So erstellen Sie eine Sicherungsrichtlinie

1. Klicken Sie im Tresor auf **Sicherungsrichtlinien** > **Hinzufügen**.
2. Wählen Sie in **Hinzufügen** zum Definieren des Richtlinientyps **SQL Server in Azure-VM**  aus.

   ![Auswählen eines Richtlinientyps für die neue Sicherungsrichtlinie](./media/backup-azure-sql-database/policy-type-details.png)

3. Geben Sie unter **Richtlinienname** einen Namen für die neue Richtlinie ein.
4. Wählen Sie in **Richtlinie für vollständige Sicherung** eine **Sicherungshäufigkeit** aus. Wählen Sie entweder **Täglich** oder **Wöchentlich**.

   - Wählen Sie für **Täglich** die Uhrzeit und die Zeitzone für den Beginn des Sicherungsauftrags aus.
   - Wählen Sie für **Wöchentlich** den Wochentag, die Uhrzeit und die Zeitzone für den Beginn des Sicherungsauftrags aus.
   - Sie müssen eine vollständige Sicherung ausführen, da die Option **Vollständige Sicherung** nicht deaktiviert werden kann.
   - Klicken Sie auf **Vollständige Sicherung**, um die Richtlinie anzuzeigen.
   - Sie können keine differenziellen Sicherungen für tägliche vollständige Sicherungen erstellen.

     ![Neue Felder für Sicherungsrichtlinien](./media/backup-azure-sql-database/full-backup-policy.png)  

5. In **BEIBEHALTUNGSDAUER** sind standardmäßig alle Optionen aktiviert. Deaktivieren Sie alle Optionen für die Beibehaltungsdauer, die Sie nicht wünschen, und legen Sie dann die zu verwendenden Intervalle fest.

    - Die Mindestbeibehaltungsdauer beträgt für alle Sicherungstypen (vollständig/differenziell/Protokoll) sieben Tage.
    - Wiederherstellungspunkte werden unter Berücksichtigung ihrer Beibehaltungsdauer mit einer Markierung versehen. Wenn Sie beispielsweise eine tägliche vollständige Sicherung wählen, wird pro Tag nur eine vollständige Sicherung ausgelöst.
    - Die Sicherung für einen bestimmten Tag wird auf Grundlage der wöchentlichen Beibehaltungsdauer und der wöchentlichen Beibehaltungseinstellung markiert und beibehalten.
    - Mit der monatlichen und jährlichen Beibehaltungsdauer verhält es sich ähnlich.

       ![Intervalleinstellungen für Beibehaltungsdauer](./media/backup-azure-sql-database/retention-range-interval.png)

6. Wählen Sie im Menü **Richtlinie für vollständige Sicherung** **OK** aus, um die Einstellungen zu übernehmen.
7. Um eine Richtlinie für eine differenzielle Sicherung hinzuzufügen, wählen Sie **Differenzielle Sicherung** aus.

   ![Intervalleinstellungen für Beibehaltungsdauer](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Öffnen des Menüs „Richtlinie für differenzielle Sicherung“](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Wählen Sie in **Richtlinie für differenzielle Sicherung** die Option **Aktivieren** aus, um die Einstellungen für Häufigkeit und Beibehaltung vorzunehmen.

    - Pro Tag können Sie nur eine differenzielle Sicherung auslösen.
    - Differenzielle Sicherungen können maximal 180 Tage aufbewahrt werden. Verwenden Sie für eine längere Aufbewahrung vollständige Sicherungen.

9. Wählen Sie **OK** aus, um die Richtlinie zu speichern und zum Hauptmenü **Sicherungsrichtlinie** zurückzukehren.

10. Um eine Richtlinie für eine Transaktionsprotokollsicherung hinzuzufügen, wählen Sie **Protokollsicherung** aus.
11. Wählen Sie in **Protokollsicherung** die Option **Aktivieren** aus, und legen Sie die Einstellungen für Häufigkeit und Aufbewahrung fest. Transaktionsprotokollsicherungen können alle 15 Minuten erfolgen und bis zu 35 Tage aufbewahrt werden.
12. Wählen Sie **OK** aus, um die Richtlinie zu speichern und zum Hauptmenü **Sicherungsrichtlinie** zurückzukehren.

    ![Bearbeiten der Richtlinie für die Transaktionsprotokollsicherung](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Legen Sie im Menü **Sicherungsrichtlinie** fest, ob die **SQL-Sicherungskomprimierung** aktiviert wird.
    - Die Komprimierung ist standardmäßig deaktiviert.
    - Auf dem Back-End verwendet Azure Backup die native SQL-Sicherungskomprimierung.

14. Nachdem Sie die Sicherungsrichtlinie bearbeitet haben, wählen Sie **OK** aus.


### <a name="modify-policy"></a>Ändern der Richtlinie
Bearbeiten Sie die Richtlinie, um die Sicherungshäufigkeit oder die Aufbewahrungsdauer zu ändern.

> [!NOTE]
> Jede Änderung der Aufbewahrungsdauer wird nicht nur auf die neuen Wiederherstellungspunkte angewendet, sondern auch rückwirkend auf alle älteren.

Navigieren Sie im Tresordashboard zu **Verwalten** > **Sicherungsrichtlinien**, und wählen Sie die Richtlinie aus, die Sie bearbeiten möchten.

  ![Verwalten von Sicherungsrichtlinien](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Aktivieren des automatischen Schutzes  

Sie können den automatischen Schutz aktivieren, um automatisch alle vorhandenen und künftigen Datenbanken in einer eigenständigen SQL Server-Instanz oder Always On-Verfügbarkeitsgruppe zu sichern.

- Die Anzahl der Datenbanken, die Sie für den automatischen Schutz in einer Aktion auswählen können, ist nicht begrenzt.
- Es ist nicht möglich, beim Aktivieren des automatischen Schutzes Datenbanken selektiv zu schützen oder vom Schutz in einer Instanz auszuschließen.
- Falls Ihre Instanz bereits einige geschützte Datenbanken enthält, sind sie unter den jeweiligen Richtlinien auch dann weiterhin geschützt, wenn Sie den automatischen Schutz deaktivieren. Für alle nicht geschützten Datenbanken, die später hinzugefügt werden, gilt aber nur eine einzelne Richtlinie, die zum Zeitpunkt der Aktivierung des automatischen Schutzes unter  **Sicherung konfigurieren** definieren. Sie können die Richtlinie, die einer automatisch geschützten Datenbank zugeordnet ist, später aber ändern.  

So aktivieren Sie den automatischen Schutz

  1. Wählen Sie in **Elemente für Sicherung** die Instanz aus, für die Sie den automatischen Schutz aktivieren möchten.
  2. Wählen Sie in der Dropdownliste unter **AUTOMATISCHER SCHUTZ** die Option **EIN** aus, und klicken Sie dann auf **OK**.

      ![Aktivieren des automatischen Schutzes für die Verfügbarkeitsgruppe](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Die Sicherung wird für alle Datenbanken gemeinsam konfiguriert und kann unter **Sicherungsaufträge** nachverfolgt werden.

Wenn Sie den automatischen Schutz deaktivieren müssen, klicken Sie unter **Sicherung konfigurieren** auf den Namen der Instanz, und wählen Sie dann **Automatischen Schutz deaktivieren** für die Instanz aus. Alle Datenbanken werden weiterhin gesichert, aber zukünftige Datenbanken sind nicht automatisch geschützt.

![Deaktivieren des automatischen Schutzes für diese Instanz](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:

- [Wiederherstellen von gesicherten SQL Server-Datenbanken](restore-sql-database-azure-vm.md)
- [Verwalten von gesicherten SQL Server-Datenbanken](manage-monitor-sql-database-backup.md)
