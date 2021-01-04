---
title: Sichern einer SAP HANA-Datenbank mit Azure Backup in Azure
description: In diesem Artikel erfahren Sie, wie Sie eine SAP HANA-Datenbanken mit dem Azure Backup-Dienst auf virtuellen Azure-Computern sichern können.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: f7957670b3ba98c640ebc53c6427273ca75a4e6d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682847"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern

SAP HANA-Datenbanken sind kritische Workloads, die eine niedrige Recovery Point Objective (RPO) und lange Aufbewahrungszeit erfordern. Sie können auf virtuellen Azure-Computern (VMs) ausgeführte SAP HANA-Datenbanken mithilfe von [Azure Backup](backup-overview.md) sichern.

In diesem Artikel wird veranschaulicht, wie SAP HANA-Datenbanken, die auf virtuellen Azure-Computern ausgeführt werden, in einem Azure Backup Recovery Services-Tresor gesichert werden.

In diesem Artikel lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Erstellen und Konfigurieren eines Tresors
> * Ermitteln von Datenbanken
> * Konfigurieren von Sicherungen
> * Ausführen eines bedarfsgesteuerten Sicherungsauftrag

>[!NOTE]
>Seit dem 1. August 2020 sind SAP HANA-Sicherungen für RHEL (7.4, 7.6, 7.7 und 8.1) allgemein verfügbar.

>[!NOTE]
>**Die Workloads für vorläufiges Löschen für SQL Server auf virtuellen Azure-Computern und vorläufiges Löschen für SAP HANA auf virtuellen Azure-Computern** sind jetzt als Vorschauversion verfügbar.<br>
>Schreiben Sie uns unter [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com), um sich für die Vorschauversion zu registrieren.

## <a name="prerequisites"></a>Voraussetzungen

Informationen zum Einrichten der Datenbank für die Sicherung finden Sie in den Abschnitten zu den [Voraussetzungen](tutorial-backup-sap-hana-db.md#prerequisites) und den [Aufgaben des Vorregistrierungsskripts](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="establish-network-connectivity"></a>Herstellen der Netzwerkverbindung

Eine SAP HANA-Datenbank auf einem virtuellen Azure-Computer benötigt für alle Operationen die Verbindung zum Azure Backup-Dienst, zu Azure Storage und zu Azure Active Directory. Dies kann durch die Verwendung privater Endpunkte oder durch die Gewährung des Zugriffs auf die erforderlichen öffentlichen IP-Adressen oder FQDNs erreicht werden. Wenn keine ordnungsgemäße Verbindung zu den erforderlichen Azure-Diensten zugelassen wird, kann dies zu Fehlern bei Operationen wie der Datenbankermittlung, der Konfiguration von Sicherungen, der Durchführung von Sicherungen und der Wiederherstellung von Daten führen.

In der folgenden Tabelle sind die verschiedenen Alternativen zum Herstellen der Konnektivität aufgeführt:

| **Option**                        | **Vorteile**                                               | **Nachteile**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Private Endpunkte                 | Sicherungen über private IPs innerhalb des virtuellen Netzwerks möglich  <br><br>   Genau abgestimmte Zugriffssteuerung auf Netzwerk- und Tresorseite | Standardmäßige [Kosten](https://azure.microsoft.com/pricing/details/private-link/) für private Endpunkte |
| NSG-Diensttags                  | Einfachere Verwaltung, da Bereichsänderungen automatisch zusammengeführt werden   <br><br>   Keine zusätzlichen Kosten | Nur mit NSGs möglich  <br><br>    Zugriff auf den gesamten Dienst |
| FQDN-Tags von Azure Firewall          | Einfachere Verwaltung, da die erforderlichen FQDNs automatisch verwaltet werden | Nur mit Azure Firewall möglich                         |
| Zugriff auf Dienst-FQDNs/-IPs | Keine zusätzlichen Kosten   <br><br>  Verwendung mit allen Netzwerksicherheits-Appliances und Firewalls möglich | Möglicherweise ist ein umfassender Satz von IP-Adressen oder FQDNs erforderlich.   |
| Verwenden eines HTTP-Proxys                 | Zentraler Internetzugriffspunkt für VMs                       | Zusätzliche Kosten für das Ausführen einer VM mit der Proxysoftware         |

Weitere Informationen zur Verwendung dieser Optionen finden Sie unten:

#### <a name="private-endpoints"></a>Private Endpunkte

Private Endpunkte ermöglichen Ihnen, sichere Verbindungen von Servern in einem virtuellen Netzwerk mit Ihrem Recovery Services-Tresor herzustellen. Der private Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihren Tresor. Der Netzwerkdatenverkehr zwischen Ihren Ressourcen innerhalb des virtuellen Netzwerks und dem Tresor wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbone-Netzwerk übertragen. Dadurch wird er vom öffentlichen Internet isoliert. Weitere Informationen zu privaten Endpunkten für Azure Backup finden Sie [hier](./private-endpoints.md).

#### <a name="nsg-tags"></a>NSG-Tags

Wenn Sie Netzwerksicherheitsgruppen (NSG) verwenden, können Sie den ausgehenden Zugriff auf Azure Backup mithilfe des Diensttags *AzureBackup* zulassen. Zusätzlich zum Tag „Azure Backup“ müssen Sie auch Konnektivität für Authentifizierung und Datenübertragung zulassen, indem Sie ähnliche [NSG-Regeln](../virtual-network/network-security-groups-overview.md#service-tags) für Azure AD (*AzureActiveDirectory*) und Azure Storage (*Storage*) erstellen.  Die folgenden Schritte beschreiben das Vorgehen zum Erstellen einer Regel für das Azure Backup-Tag:

1. Navigieren Sie unter **Alle Dienste** zu **Netzwerksicherheitsgruppen**, und wählen Sie die Netzwerksicherheitsgruppe aus.

1. Wählen Sie unter **Einstellungen** die Option **Ausgangssicherheitsregeln** aus.

1. Wählen Sie **Hinzufügen**. Geben Sie die erforderlichen Informationen zum Erstellen einer neuen Regel ein, wie unter [Einstellungen zu Sicherheitsregeln](../virtual-network/manage-network-security-group.md#security-rule-settings) beschrieben. Stellen Sie sicher, dass die Option **Ziel** auf *Diensttag* und **Zieldiensttag** auf *AzureBackup* festgelegt wurde.

1. Wählen Sie **Hinzufügen** aus, um die neu erstellte Ausgangssicherheitsregel zu speichern.

Auf ähnliche Weise können Sie ausgehende NSG-Sicherheitsregeln für Azure Storage und Azure AD erstellen. Weitere Informationen zu Diensttags finden Sie in [diesem Artikel](../virtual-network/service-tags-overview.md).

#### <a name="azure-firewall-tags"></a>Azure Firewall-Tags

Wenn Sie Azure Firewall verwenden, erstellen Sie eine Anwendungsregel mithilfe des [Azure Firewall-FQDN-Tags](../firewall/fqdn-tags.md) *AzureBackup*. Dies erlaubt sämtlichen ausgehenden Zugriff auf Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Zulassen des Zugriffs auf Dienst-IP-Bereiche

Wenn Sie sich dafür entscheiden, Zugriffsdienst-IPs zuzulassen, beziehen Sie sich auf die IP-Bereiche in der [hier](https://www.microsoft.com/download/confirmation.aspx?id=56519) verfügbaren JSON-Datei. Sie müssen den Zugriff auf IPs für Azure Backup, Azure Storage und Azure Active Directory zulassen.

#### <a name="allow-access-to-service-fqdns"></a>Zugriff auf Dienst-FQDNs

Sie können auch die folgenden FQDNs verwenden, um den Zugriff auf die erforderlichen Dienste von ihren Servern aus zuzulassen:

| Dienst    | Domänennamen, auf die zugegriffen werden soll                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Gewähren des Zugriffs auf FQDNs gemäß Abschnitt 56 und 59, wie in [diesem Artikel](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) beschrieben |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Verwenden eines HTTP-Proxyservers für das Weiterleiten von Datenverkehr

Wenn Sie eine SAP HANA-Datenbank auf einem virtuellen Azure-Computer sichern, verwendet die Sicherungserweiterung auf dem virtuellen Computer die HTTPS-APIs, um Verwaltungsbefehle an Azure Backup und Daten an Azure Storage zu senden. Die Sicherungserweiterung verwendet auch Azure AD zur Authentifizierung. Leiten Sie den Datenverkehr der Sicherungserweiterung für diese drei Dienste über den HTTP-Proxy weiter. Verwenden Sie die oben genannte Liste der IPs und FQDNs, um den Zugriff auf die erforderlichen Dienste zuzulassen. Authentifizierte Proxyserver werden nicht unterstützt.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Ermitteln der Datenbanken

1. Wählen Sie im Tresor unter **Erste Schritte** die Option **Sicherung** aus. Wählen Sie unter **Wo wird Ihre Workload ausgeführt?** den Eintrag **SAP HANA in Azure-VM** aus.
2. Wählen Sie **Ermittlung starten** aus. Dadurch wird die Ermittlung nicht geschützter Linux-VMs in der Tresorregion initiiert.

   * Nach der Ermittlung werden nicht geschützte VMs im Portal nach Name und Ressourcengruppe sortiert angezeigt.
   * Wenn ein virtueller Computer nicht wie erwartet aufgeführt wird, überprüfen Sie, ob er bereits in einem Tresor gesichert wird.
   * Mehrere virtuelle Computer können den gleichen Namen aufweisen, gehören dann aber verschiedenen Ressourcengruppen an.

3. Wählen Sie im Bereich **Virtuelle Computer auswählen** den Link zum Herunterladen des Skripts aus, das dem Azure Backup-Dienst Berechtigungen zum Zugreifen auf die SAP HANA-VMs für die Ermittlung von Datenbanken erteilt.
4. Führen Sie das Skript auf jedem virtuellen Computer aus, der die zu sichernden SAP HANA-Datenbanken hostet.
5. Wählen Sie nach dem Ausführen des Skripts auf den VMs im Bereich **Virtuelle Computer auswählen** die VMs aus. Wählen Sie dann **DBs ermitteln** aus.
6. Azure Backup ermittelt alle SAP HANA-Datenbanken auf dem virtuellen Computer. Während der Ermittlung registriert Azure Backup den virtuellen Computer beim Tresor und installiert eine Erweiterung auf dem VM. Für die Datenbank wird kein Agent installiert.

    ![Ermitteln von SAP HANA-Datenbanken](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurieren der Sicherung  

Aktivieren Sie jetzt die Sicherung.

1. Wählen Sie in Schritt 2 **Sicherung konfigurieren** aus.

    ![Konfiguration der Sicherung](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Wählen Sie unter **Zu sichernde Elemente auswählen** alle Datenbanken aus, die Sie schützen möchten, und wählen Sie dann **OK** aus.

    ![Auswählen von Elementen für die Sicherung](./media/backup-azure-sap-hana-database/select-items.png)
3. Wählen Sie **Sicherungsrichtlinie** > **Sicherungsrichtlinie auswählen** aus, und erstellen Sie eine neue Sicherungsrichtlinie für die Datenbanken gemäß den unten stehenden Anweisungen.

    ![Auswählen der Sicherungsrichtlinie](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Wählen Sie nach dem Erstellen der Richtlinie im Menü **Sicherung** die Option **Sicherung aktivieren** aus.

    ![Aktivieren der Sicherung](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Verfolgen Sie den Fortschritt der Sicherungskonfiguration im Bereich **Benachrichtigungen** des Portals.

### <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Eine Sicherungsrichtlinie legt fest, wann Sicherungen erstellt und wie lange sie aufbewahrt werden.

* Eine Richtlinie wird auf Tresorebene erstellt.
* Mehrere Tresore können die gleiche Sicherungsrichtlinie verwenden. Allerdings müssen Sie die Sicherungsrichtlinie auf jeden Tresor anwenden.

>[!NOTE]
>Azure Backup passt Änderungen an der Sommer- oder Winterzeit bei der Sicherung einer auf einem virtuellen Azure-Computer ausgeführten SAP HANA-Datenbank nicht automatisch an.
>
>Ändern Sie die Richtlinie nach Bedarf manuell.

Legen Sie die Richtlinieneinstellungen wie folgt fest:

1. Geben Sie unter **Richtlinienname** einen Namen für die neue Richtlinie ein.

   ![Eingeben des Richtliniennamens](./media/backup-azure-sap-hana-database/policy-name.png)
1. Wählen Sie in **Richtlinie für vollständige Sicherung** für **Sicherungshäufigkeit** **Täglich** oder **Wöchentlich** aus.
   * **Daily** (Täglich): Wählen Sie die Uhrzeit und die Zeitzone für den Beginn des Sicherungsauftrags aus.
       * Sie müssen eine vollständige Sicherung ausführen. Diese Option kann nicht deaktiviert werden.
       * Klicken Sie auf **Vollständige Sicherung**, um die Richtlinie anzuzeigen.
       * Sie können keine differenziellen Sicherungen für tägliche vollständige Sicherungen erstellen.
   * **Wöchentlich**: Wählen Sie den Wochentag, die Uhrzeit und die Zeitzone für die Ausführung des Sicherungsauftrags aus.

   ![Auswählen der Sicherungshäufigkeit](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. Konfigurieren Sie unter **Beibehaltungsdauer** die Aufbewahrungseinstellungen für die vollständige Sicherung.
    * Standardmäßig sind alle Optionen ausgewählt. Deaktivieren Sie alle Optionen für die Beibehaltungsdauer, die Sie nicht verwenden möchten, und legen Sie die gewünschten Grenzwerte fest.
    * Die Mindestaufbewahrungsdauer beträgt für alle Sicherungstypen (vollständig/differenziell/Protokoll) sieben Tage.
    * Wiederherstellungspunkte werden unter Berücksichtigung ihrer Beibehaltungsdauer mit einer Markierung versehen. Wenn Sie beispielsweise eine tägliche vollständige Sicherung wählen, wird pro Tag nur eine vollständige Sicherung ausgelöst.
    * Die Sicherung für einen bestimmten Tag wird auf Grundlage der wöchentlichen Beibehaltungsdauer und der entsprechenden Einstellung markiert und beibehalten.
    * Mit der monatlichen und jährlichen Beibehaltungsdauer verhält es sich ähnlich.

1. Wählen Sie im Menü **Richtlinie für vollständige Sicherung** **OK** aus, um die Einstellungen zu übernehmen.
1. Wählen Sie **Differenzielle Sicherung** aus, um eine Richtlinie für eine differenzielle Sicherung hinzuzufügen.
1. Wählen Sie in **Richtlinie für differenzielle Sicherung** die Option **Aktivieren** aus, um die Einstellungen für Häufigkeit und Beibehaltung vorzunehmen.
    * Pro Tag kann höchstens eine differenzielle Sicherung ausgelöst werden.
    * Differenzielle Sicherungen können maximal 180 Tage aufbewahrt werden. Wenn Sie eine längere Aufbewahrung wünschen, müssen Sie vollständige Sicherungen verwenden.

    ![Richtlinie für differenzielle Sicherung](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Inkrementelle Sicherungen werden nun in der öffentlichen Vorschau unterstützt. Sie können entweder „differenziell“ oder „inkrementell“ für die tägliche Sicherung auswählen, aber nicht beide Optionen.
1. Wählen Sie unter **Richtlinie zur inkrementellen Sicherung** die Option **Aktivieren** aus, um die Einstellungen für Häufigkeit und Beibehaltung vorzunehmen.
    * Pro Tag kann höchstens eine inkrementelle Sicherung ausgelöst werden.
    * Inkrementelle Sicherungen können maximal 180 Tage aufbewahrt werden. Wenn Sie eine längere Aufbewahrung wünschen, müssen Sie vollständige Sicherungen verwenden.

    ![Richtlinie zur inkrementellen Sicherung](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. Wählen Sie **OK** aus, um die Richtlinie zu speichern und zum Hauptmenü **Sicherungsrichtlinie** zurückzukehren.
1. Wählen Sie **Protokollsicherung** aus, um eine Richtlinie für eine Transaktionsprotokollsicherung hinzuzufügen.
    * Wählen Sie unter **Protokollsicherung** die Option **Aktivieren** aus.  Diese Option kann nicht deaktiviert werden, da SAP HANA alle Protokollsicherungen verwaltet.
    * Legen Sie die Häufigkeits- und Aufbewahrungssteuerelemente fest.

    > [!NOTE]
    > Protokollsicherungen werden erst nach dem erfolgreichen Abschluss einer vollständigen Sicherung übertragen.

1. Wählen Sie **OK** aus, um die Richtlinie zu speichern und zum Hauptmenü **Sicherungsrichtlinie** zurückzukehren.
1. Wählen Sie nach dem Definieren der Sicherungsrichtlinie **OK** aus.

> [!NOTE]
> Jede Protokollsicherung wird mit der vorherigen vollständigen Sicherung zu einer Wiederherstellungskette verkettet. Diese vollständige Sicherung wird aufbewahrt, bis die Aufbewahrungsdauer der letzten Protokollsicherung abgelaufen ist. Das kann bedeuten, dass die vollständige Sicherung für einen zusätzlichen Zeitraum aufbewahrt wird, um sicherzustellen, dass alle Protokolle wiederhergestellt werden können. Ein Beispiel: Ein Benutzer hat eine wöchentliche vollständige Sicherung, tägliche differenzielle Sicherungen und alle zwei Stunden ausgeführte Protokolle eingerichtet. Alle werden 30 Tage lang aufbewahrt. Die wöchentliche vollständige Sicherung kann aber erst vollständig bereinigt/gelöscht werden, wenn die nächste vollständige Sicherung verfügbar ist, also nach 30 + 7 Tagen. Angenommen, am 16. November erfolgt eine wöchentliche vollständige Sicherung. Gemäß der Aufbewahrungsrichtlinie wird diese bis zum 16. Dezember aufbewahrt. Die letzte Protokollsicherung hierfür erfolgt vor der nächsten geplanten vollständigen Sicherung, und zwar am 22. November. Solange dieses Protokoll bis zum 22. Dezember verfügbar ist, kann die vollständige Sicherung vom 16. November nicht gelöscht werden. Die vollständige Sicherung vom 16. November wird also bis zum 22. Dezember aufbewahrt.

## <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung

Sicherungen werden gemäß dem Richtlinienzeitplan ausgeführt. Eine bedarfsgesteuerte Sicherung können Sie wie folgt ausführen:

1. Wählen Sie im Tresormenü die Option **Sicherungselemente** aus.
2. Wählen Sie unter **Sicherungselemente** den virtuellen Computer aus, auf dem die SAP HANA-Datenbank ausgeführt wird, und wählen Sie dann **Jetzt sichern** aus.
3. Wählen Sie in **Jetzt sichern** den Sicherungstyp aus, den Sie durchführen möchten. Klicken Sie anschließend auf **OK**. Diese Sicherung wird gemäß der diesem Sicherungselement zugeordneten Richtlinie aufbewahrt.
4. Überwachen Sie die Portalbenachrichtigungen. Sie können den Auftragsstatus im Dashboard des Tresors unter **Sicherungsaufträge** > **In Bearbeitung** überwachen. Je nach Größe Ihrer Datenbank kann das Erstellen der ersten Sicherung einige Zeit dauern.

Standardmäßig werden bedarfsgesteuerte Sicherungen 45 Tage lang aufbewahrt.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Ausführen einer SAP HANA Studio-Sicherung einer Datenbank, für die Azure Backup aktiviert ist

Führen Sie die folgenden Schritte aus, wenn Sie (mit HANA Studio) eine lokale Sicherung einer Datenbank ausführen möchten, die mit Azure Backup gesichert wird:

1. Warten Sie, bis alle vollständigen Sicherungen oder Protokollsicherungen für die Datenbank abgeschlossen sind. Überprüfen Sie den Status in SAP HANA Studio/Cockpit.
1. Deaktivieren Sie Protokollsicherungen, und legen Sie den Sicherungskatalog auf das Dateisystem für die entsprechende Datenbank fest.
1. Doppelklicken Sie hierzu auf **systemdb** > **Konfiguration** > **Datenbank auswählen** > **Filter (Protokoll)** .
1. Legen Sie **enable_auto_log_backup** auf **No** fest.
1. Legen Sie **log_backup_using_backint** auf **False** fest.
1. Legen Sie **catalog_backup_using_backint** auf **FALSE** fest.
1. Erstellen Sie eine bedarfsgesteuerte vollständige Sicherung der Datenbank.
1. Warten Sie, bis die vollständige Sicherung und die Katalogsicherung abgeschlossen sind.
1. Stellen Sie die vorherigen Einstellungen für Azure wieder her:
    * Legen Sie **enable_auto_log_backup** auf **Yes** fest.
    * Legen Sie **log_backup_using_backint** auf **True** fest.
    * Legen Sie **catalog_backup_using_backint** auf **True** fest.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [nur auf virtuellen Azure-Computern ausgeführte SAP HANA-Datenbanken wiederherstellen können](./sap-hana-db-restore.md).
* [Verwalten und Überwachen gesicherter SAP HANA-Datenbanken](./sap-hana-db-manage.md)
