---
title: 'Azure-Portal: Erstellen einer verwalteten SQL-Datenbank-Instanz | Microsoft-Dokumentation'
description: Erstellen einer verwalteten SQL-Datenbank-Instanz, einer Netzwerkumgebung und eines virtuellen Clientcomputers für den Zugriff
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: f54cea75e6f7866f1be41d3b0f82393af2235a0a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949875"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Schnellstart: Erstellen einer verwalteten Azure SQL-Datenbank-Instanz

In dieser Schnellstartanleitung wird Schritt für Schritt beschrieben, wie Sie eine [verwaltete Azure SQL-Datenbank-Instanz](sql-database-managed-instance.md) im Azure-Portal erstellen.

> [!IMPORTANT]
> Informationen zu Einschränkungen finden Sie in den Abschnitten [Unterstützte Regionen](sql-database-managed-instance-resource-limits.md#supported-regions) und [Unterstützte Abonnementtypen](sql-database-managed-instance-resource-limits.md#supported-subscription-types).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-managed-instance"></a>Erstellen einer verwalteten Instanz

Die folgenden Schritte zeigen, wie Sie eine verwaltete Instanz erstellen.

1. Klicken Sie im Azure-Portal oben links auf **Ressource erstellen**.
2. Suchen Sie nach **Verwaltete Instanz**, und wählen Sie dann **Verwaltete Azure SQL-Datenbank-Instanz** aus.
3. Klicken Sie auf **Erstellen**.

   ![Erstellen einer verwalteten Instanz](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Füllen Sie das Formular für die **verwaltete SQL-Instanz** mit den geforderten Informationen aus, indem Sie die Angaben in der folgenden Tabelle verwenden:

   | Einstellung| Empfohlener Wert | BESCHREIBUNG |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Ihr Abonnement | Ein Abonnement, mit dem Sie die Berechtigung zum Erstellen neuer Ressourcen erhalten |
   |**Name der verwalteten Instanz**|Ein gültiger Name|Gültige Namen finden Sie unter [Benennungskonventionen](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Administratoranmeldung für verwaltete Instanz**|Ein beliebiger gültiger Benutzername|Gültige Namen finden Sie unter [Benennungskonventionen](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Verwenden Sie nicht „serveradmin“. Hierbei handelt es sich um eine reservierte Rolle auf Serverebene.|
   |**Kennwort**|Ein gültiges Kennwort|Das Kennwort muss mindestens 16 Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
   |**Zeitzone**|Die Zeitzone, die von Ihrer verwalteten Instanz beachtet werden soll.|Weitere Informationen finden Sie unter [Zeitzone in einer verwalteten Azure SQL-Datenbank-Instanz (Vorschauversion)](sql-database-managed-instance-timezone.md).|
   |**Sortierung**|Die Sortierung, die Sie für Ihre verwaltete Instanz verwenden möchten|Wenn Sie Datenbanken von SQL Server migrieren, überprüfen Sie die Quellsortierung mit `SELECT SERVERPROPERTY(N'Collation')`, und verwenden Sie diesen Wert. Informationen zu Sortierungen finden Sie unter [Festlegen oder Ändern der Serversortierung](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation).|
   |**Location**|Der Standort, an dem Sie die verwaltete Instanz erstellen möchten|Informationen zu Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/).|
   |**Virtuelles Netzwerk**|Wählen Sie entweder **Neues virtuelles Netzwerk erstellen** oder ein gültiges virtuelles Netzwerk und ein Subnetz aus.| Wenn ein Netzwerk oder Subnetz nicht verfügbar ist, muss es [modifiziert werden, um die Netzwerkanforderungen zu erfüllen](sql-database-managed-instance-configure-vnet-subnet.md), bevor Sie es als Ziel für die neue verwaltete Instanz auswählen. Informationen zu den Anforderungen für das Konfigurieren der Netzwerkumgebung für eine verwaltete Instanz finden Sie unter [Konnektivitätsarchitektur für eine verwaltete Instanz in Azure SQL-Datenbank](sql-database-managed-instance-connectivity-architecture.md). |
   |**Öffentlichen Endpunkt aktivieren**   |Aktivieren Sie diese Option, um den öffentlichen Endpunkt zu aktivieren.   |Damit über den öffentlichen Datenendpunkt auf die verwaltete Instanz zugegriffen werden kann, muss die Option **Öffentlichen Endpunkt aktivieren** aktiviert werden.| 
   |**Zugriff erlauben von**   |Wählen Sie eine der folgenden Optionen aus: <ul> <li>**Azure-Dienste**</li> <li>**Internet**</li> <li>**Kein Zugriff**</li></ul>   |Im Portal können Sie Sicherheitsgruppen mit einem öffentlichen Endpunkt konfigurieren. </br> </br> Wählen Sie basierend auf Ihrem Szenario eine der folgenden Optionen aus: </br> <ul> <li>Azure-Dienste: Diese Option wird empfohlen, wenn Sie eine Verbindung über Power BI oder einen anderen mehrinstanzenfähigen Dienst herstellen. </li> <li> Internet: Verwenden Sie diese Option zu Testzwecken, wenn Sie schnell eine verwaltete Instanz einrichten möchten. Von der Verwendung dieser Option in Produktionsumgebungen wird abgeraten. </li> <li> Kein Zugriff: Mit dieser Option wird eine Ablehnungssicherheitsregel erstellt. Sie müssen diese Regel ändern, damit über einen öffentlichen Endpunkt auf verwaltete Instanzen zugegriffen werden kann. </li> </ul> </br> Weitere Informationen zur Sicherheit öffentlicher Endpunkte finden Sie unter [Sicheres Verwenden einer verwalteten Azure SQL-Datenbank-Instanz mit öffentlichem Endpunkt](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Verbindungstyp**|Wählen Sie zwischen dem Proxy- und dem Umleitungsverbindungstyp.|Weitere Informationen zu Verbindungstypen finden Sie unter [Verbindungsarchitektur von Azure SQL](sql-database-connectivity-architecture.md#connection-policy).|
   |**Ressourcengruppe**|Eine neue oder vorhandene Ressourcengruppe.|Gültige Ressourcengruppennamen finden Sie unter [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Benennungsregeln und Einschränkungen).|

   ![Formular für die verwaltete Instanz](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Wenn Sie die verwaltete Instanz als sekundäre Instanzfailovergruppe verwenden möchten, wählen Sie den Auftragsabschluss aus, und geben Sie die verwaltete Instanz „DnsAzurePartner“ an. Dieses Feature befindet sich in der Vorschauphase und ist nicht im folgenden Screenshot zu sehen.
6. Wählen Sie **Tarif** aus, um die Größe der Compute- und Speicherressourcen festzulegen und die Tarifoptionen zu prüfen. Der Tarif „Universell“ mit 32 GB Arbeitsspeicher und 16 V-Kernen ist der Standardwert.
7. Verwenden Sie die Schieberegler oder Textfelder, um die Speichermenge und die Anzahl von virtuellen Kernen anzugeben.
8. Wählen Sie abschließend **Anwenden** aus, um Ihre Auswahl zu speichern. 
9. Wählen Sie **Erstellen**, um die verwaltete Instanz bereitzustellen.
10. Wählen Sie das Symbol **Benachrichtigungen** aus, um den Status der Bereitstellung anzuzeigen.

    ![Fortschritt der Bereitstellung einer verwalteten Instanz](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Wählen Sie **Die Bereitstellung wird ausgeführt.**, um das Fenster für die verwaltete Instanz zu öffnen und den Bereitstellungsstatus weiter zu verfolgen. 

> [!IMPORTANT]
> Für die erste Instanz in einem Subnetz ist die Bereitstellungsdauer normalerweise deutlich länger als bei den nachfolgenden Instanzen. Brechen Sie den Bereitstellungsvorgang nicht ab, weil er länger als erwartet dauert. Das Erstellen der zweiten verwalteten Instanz im Subnetz dauert nur einige Minuten.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Überprüfen der Ressourcen und Abrufen des vollqualifizierten Servernamens

War die Bereitstellung erfolgreich, überprüfen Sie die erstellten Ressourcen, und rufen Sie den vollqualifizierten Servernamen für die Verwendung in späteren Schnellstartanleitungen ab.

1. Öffnen Sie die Ressourcengruppe für Ihre verwaltete Instanz. Zeigen Sie die Ressourcen an, die in der Schnellstartanleitung [Erstellen einer verwalteten Instanz](#create-a-managed-instance) erstellt wurden.

   ![Ressourcen der verwalteten Instanz](./media/sql-database-managed-instance-get-started/resources.png)

2. Wählen Sie die Routingtabelle aus, um die Tabelle mit den benutzerdefinierten Routen anzuzeigen, die für Sie erstellt wurde.

   ![Routingtabelle](./media/sql-database-managed-instance-get-started/route-table.png)

3. Sehen Sie sich in der Routingtabelle die Einträge für das Leiten von Datenverkehr aus dem bzw. im virtuellen Netzwerk der verwalteten Instanz an. Wenn Sie Ihre Routingtabelle manuell erstellen oder konfigurieren, müssen Sie darauf achten, dass Sie diese Einträge in der Routingtabelle erstellen.

   ![Eintrag für „Subnetz der verwalteten Instanz zu lokal“](./media/sql-database-managed-instance-get-started/udr.png)

4. Wechseln Sie zurück zur Ressourcengruppe, und wählen Sie die Netzwerksicherheitsgruppe aus, um die Sicherheitsregeln anzuzeigen.

   ![Netzwerksicherheitsgruppe](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Überprüfen Sie die Eingangs- und Ausgangssicherheitsregeln. Wenn Sie öffentliche Endpunkte für Ihre verwaltete Instanz konfiguriert haben, finden Sie weitere Informationen im Artikel [Configure public endpoint in Azure SQL Database managed instance](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) (Konfigurieren eines öffentlichen Endpunkts in einer verwalteten Azure SQL-Datenbank-Instanz).

   ![Sicherheitsregeln](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Wechseln Sie zurück zur Ressourcengruppe, und wählen Sie Ihre verwaltete Instanz aus.

   ![Verwaltete Instanz](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Suchen Sie auf der Registerkarte **Übersicht** nach der Eigenschaft **Host**. Kopieren Sie die vollqualifizierte Hostadresse für die verwaltete Instanz zur Verwendung in der nächsten Schnellstartanleitung.

   ![Hostname](./media/sql-database-managed-instance-get-started/host-name.png)

   Der Name lautet etwa wie folgt: **Name_Ihres_Computers.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verbinden mit einer verwalteten Instanz:
  - Eine Übersicht über die Verbindungsoptionen für Anwendungen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-connect-app.md).
  - Eine Schnellstartanleitung, die zeigt, wie Sie eine Verbindung zwischen einem virtuellen Azure-Computer und einer verwalteten Instanz herstellen, finden Sie unter [Schnellstart: Konfigurieren einer Azure-VM für das Herstellen einer Verbindung mit einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-configure-vm.md).
  - Eine Schnellstartanleitung, die zeigt, wie Sie von einem lokalen Clientcomputer über eine Point-to-Site-Verbindung eine Verbindung mit einer verwalteten Instanz herstellen, finden Sie unter [Schnellstart: Konfigurieren einer Point-to-Site-Verbindung von einem lokalen Computer mit einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-configure-p2s.md).
- Wiederherstellen einer vorhandenen SQL Server-Datenbank aus einer lokalen Instanz in einer verwalteten Instanz: 
    - Verwenden Sie zur Wiederherstellung auf der Grundlage einer Datenbanksicherungsdatei [Azure Database Migration Service (DMS) für die Migration](../dms/tutorial-sql-server-to-managed-instance.md). 
    - Verwenden Sie zur Wiederherstellung auf der Grundlage einer Datenbanksicherungsdatei den Befehl [T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md).
- Informationen zur erweiterten Überwachung der Datenbankleistung verwalteter Instanzen mit integrierten Problembehandlungsfunktionen finden Sie unter [Überwachen von Azure SQL-Datenbank mithilfe von Azure SQL-Analyse (Vorschauversion)](../azure-monitor/insights/azure-sql.md).
