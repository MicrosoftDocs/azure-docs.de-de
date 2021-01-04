---
title: 'Schnellstart: Erstellen und Abfragen eines dedizierten SQL-Pools (vormals SQL DW) (Azure-Portal)'
description: Hier erfahren Sie, wie Sie einen dedizierten SQL-Pool (vormals SQL DW) über das Azure-Portal erstellen und abfragen.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/28/2019
ms.author: pimorano
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 78a0982081b8e34461fb2910cc7ce21be622cb6a
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922809"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>Schnellstart: Erstellen und Abfragen eines dedizierten SQL-Pools (vormals SQL DW) in Azure Synapse Analytics über das Azure-Portal

Hier erfahren Sie, wie Sie über das Azure-Portal schnell einen dedizierten SQL-Pool (vormals SQL DW) in Azure Synapse Analytics erstellen und abfragen.

## <a name="prerequisites"></a>Voraussetzungen

1. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

   > [!NOTE]
   > Die Erstellung eines dedizierten SQL-Pools (vormals SQL DW) in Azure Synapse führt möglicherweise zu einem neuen abrechenbaren Dienst. Weitere Informationen finden Sie unter [Azure Synapse Analytics – Preise](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Laden Sie die aktuelle Version von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) herunter, und installieren Sie sie.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-sql-pool"></a>Erstellen eines SQL-Pools

Data Warehouses werden unter Verwendung eines dedizierten SQL-Pools (vormals SQL DW) in Azure Synapse Analytics erstellt. Ein dedizierter SQL-Pool (vormals SQL DW) wird mit einem definierten Satz von [Computeressourcen](memory-concurrency-limits.md) erstellt. Die Datenbank wird innerhalb einer [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) und auf einem [logischen SQL-Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) erstellt.

Gehen Sie wie folgt vor, um einen dedizierten SQL-Pool (vormals SQL DW) mit den Beispieldaten von **AdventureWorksDW** zu erstellen:

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.

   ![Erstellen einer Ressource im Azure-Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. Geben Sie auf der Suchleiste „dedizierter SQL-Pool“ ein, und wählen Sie die Option „Dedizierter SQL-Pool (vormals SQL DW)“ aus. Wählen Sie auf der daraufhin angezeigten Seite die Option **Erstellen** aus.

   ![Erstellen eines Data Warehouse](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. Geben Sie unter **Basiseinstellungen** Ihr Abonnement, Ihre Ressourcengruppe, den Namen des dedizierten SQL-Pools (vormals SQL DW) und den Servernamen an:

   | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
   | :------ | :-------------- | :---------- |
   | **Abonnement** | Ihr Abonnement | Ausführliche Informationen zu Ihren Abonnements finden Sie unter [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Ressourcengruppe** | myResourceGroup | Gültige Ressourcengruppennamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Benennungsregeln und Einschränkungen). |
   | **Name des SQL-Pools** | Beliebiger global eindeutiger Name (Beispiel: *mySampleDataWarehouse*) | Gültige Datenbanknamen finden Sie unter [Database Identifiers](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (Datenbankbezeichner).  |
   | **Server** | Ein global eindeutiger Name | Wählen Sie einen vorhandenen Server aus, oder erstellen Sie einen neuen Servernamen, indem Sie **Neu erstellen** auswählen. Gültige Servernamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Benennungsregeln und Einschränkungen). |

   ![Erstellen einer Data Warehouse-Instanz: Grundlegende Details](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. Wählen Sie unter **Leistungsebene** die Option **Leistungsebene auswählen** aus, um Ihre Konfiguration per Schieberegler zu ändern (optional).

   ![Ändern der Leistungsebene für die Data Warehouse-Instanz](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Weitere Informationen zu Leistungsebenen finden Sie unter [Verwalten von Computeressourcen in Azure Synapse Analytics](sql-data-warehouse-manage-compute-overview.md).

5. Wählen Sie **Zusätzliche Einstellungen** und dann unter **Vorhandene Daten verwenden** die Option **Beispiel** aus, damit AdventureWorksDW als Beispieldatenbank erstellt wird.

    ![„Vorhandene Daten verwenden“ auswählen](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Wählen Sie nach dem Ausfüllen der Registerkarte „Grundlagen“ im Azure Synapse Analytics-Formular die Option **Bewerten + erstellen** und dann **Erstellen** aus, um den SQL-Pool zu erstellen. Die Bereitstellung dauert einige Minuten.

   ![Auswählen von „Bewerten + erstellen“](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![„Erstellen“ auswählen](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Wählen Sie in der Symbolleiste die Option **Benachrichtigungen** aus, um den Bereitstellungsprozess zu überwachen.

   ![Screenshot, in dem Benachrichtigungen für „Bereitstellung wird durchgeführt“ gezeigt sind](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Erstellen einer Firewallregel auf Serverebene

Der Azure Synapse-Dienst erstellt eine Firewall auf Serverebene. Diese Firewall verhindert, dass externe Anwendungen und Tools eine Verbindung mit dem Server oder mit Datenbanken auf dem Server herstellen. Zum Herstellen von Konnektivität können Sie Firewallregeln hinzufügen, mit denen Konnektivität für bestimmte IP-Adressen ermöglicht wird. Führen Sie die folgenden Schritte aus, um eine [Firewallregel auf Serverebene](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) für die IP-Adresse Ihres Clients zu erstellen.

> [!NOTE]
> Azure Synapse kommuniziert über Port 1433. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 1433 öffnet.

1. Klicken Sie nach Abschluss der Bereitstellung im linken Menü auf **Alle Dienste**. Wählen Sie die Option **Datenbanken** und das Sternchen neben **Azure Synapse Analytics** aus, um Azure Synapse Analytics Ihren Favoriten hinzuzufügen.

2. Wählen Sie **Azure Synapse Analytics** im Menü auf der linken Seite und dann auf der Seite **Azure Synapse Analytics** die Option **mySampleDataWarehouse** aus. Die Übersichtsseite für Ihre Datenbank wird geöffnet. Diese enthält den vollqualifizierten Servernamen (z. B. **sqlpoolservername.database.windows.net**) und Optionen für die weitere Konfiguration.

3. Kopieren Sie diesen vollqualifizierten Servernamen, um ihn in diesem und nachfolgenden Schnellstarts zur Verbindungsherstellung mit Ihrem Server und den Datenbanken zu verwenden. Wählen Sie den Servernamen aus, um die Servereinstellungen zu öffnen.

   ![Suchen des Servernamens](./media/create-data-warehouse-portal/find-server-name.png)

4. Wählen Sie die Option **Firewalleinstellungen anzeigen** aus.

   ![Servereinstellungen](./media/create-data-warehouse-portal/server-settings.png)

5. Die Seite **Firewalleinstellungen** für den Server wird geöffnet.

   ![Serverfirewallregel](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Wählen Sie in der Symbolleiste die Option **Client-IP-Adresse hinzufügen** aus, um Ihre aktuelle IP-Adresse einer neuen Firewallregel hinzuzufügen. Eine Firewallregel kann Port 1433 für eine einzelne IP-Adresse oder einen Bereich von IP-Adressen öffnen.

7. Wählen Sie **Speichern** aus. Für Ihre aktuelle IP-Adresse wird eine Firewallregel auf Serverebene erstellt, und auf dem Server wird der Port 1433 geöffnet.

8. Wählen Sie **OK** aus, und schließen Sie anschließend die Seite **Firewalleinstellungen**.

Jetzt können Sie mithilfe dieser IP-Adresse eine Verbindung mit dem Server und den zugehörigen SQL-Pools herstellen. Die Verbindung über SQL Server Management Studio oder ein anderes Tool Ihrer Wahl hergestellt werden. Verwenden Sie zum Herstellen der Verbindung das Serveradministratorkonto, das Sie zuvor erstellt haben.

> [!IMPORTANT]
> Standardmäßig ist der Zugriff über die SQL-Datenbank-Firewall für alle Azure-Dienste aktiviert. Wählen Sie auf dieser Seite die Option **AUS** und dann **Speichern** aus, um die Firewall für alle Azure-Dienste zu deaktivieren.

## <a name="get-the-fully-qualified-server-name"></a>Abrufen des vollqualifizierten Servernamens

Rufen Sie den vollqualifizierten Servernamen für Ihren Server im Azure-Portal ab. Später verwenden Sie den vollqualifizierten Namen zum Herstellen einer Verbindung mit dem Server.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie im Menü auf der linken Seite die Option **Azure Synapse Analytics** und dann auf der Seite **Azure Synapse Analytics** Ihre Datenbank aus.

3. Suchen Sie im Azure-Portal auf der Seite für Ihre Datenbank unter **Zusammenfassung** nach Ihrer Datenbank, und kopieren Sie den **Servernamen**. In diesem Beispiel lautet der vollqualifizierte Name „sqlpoolservername.database.windows.net“.

    ![Verbindungsinformationen](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Herstellen einer Verbindung mit dem Server als Serveradministrator

In diesem Abschnitt wird [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) zum Herstellen einer Verbindung mit Ihrem Server verwendet.

1. Öffnen Sie SQL Server Management Studio.

2. Geben Sie im Dialogfeld **Mit Server verbinden** die folgenden Informationen ein:

   | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
   | :------ | :-------------- | :---------- |
   | Servertyp | Datenbank-Engine | Dieser Wert ist erforderlich. |
   | Servername | Der vollqualifizierte Servername | Beispiel: **sqlpoolservername.database.windows.net**. |
   | Authentifizierung | SQL Server-Authentifizierung | In diesem Tutorial ist die SQL-Authentifizierung der einzige konfigurierte Authentifizierungstyp. |
   | Anmeldename | Das Serveradministratorkonto | Dies ist das Konto, das Sie bei der Servererstellung angegeben haben. |
   | Kennwort | Das Kennwort für das Serveradministratorkonto | Dies ist das Kennwort, das Sie beim Erstellen des Servers angegeben haben. |
   ||||

   ![Verbindung mit dem Server herstellen](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Wählen Sie **Verbinden** aus. Das Fenster „Objekt-Explorer“ wird in SSMS geöffnet.

4. Erweitern Sie im Objekt-Explorer den Eintrag **Datenbanken**. Erweitern Sie dann **mySampleDatabase**, um die Objekte in der neuen Datenbank anzuzeigen.

   ![Datenbankobjekte](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Ausführen einiger Abfragen

Sie sollten keine große Abfragen ausführen, während Sie als Serveradministrator protokolliert werden, da hierbei eine [Ressourcenklasse mit begrenzten Ressourcen](resource-classes-for-workload-management.md)verwendet wird. Konfigurieren Sie stattdessen [Workloadisolation](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql) wie [in den Tutorials veranschaulicht](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/load-data-wideworldimportersdw#create-a-user-for-loading-data).

Azure Synapse Analytics nutzt T-SQL als Abfragesprache. Führen Sie die folgenden Schritte aus, um ein Abfragefenster zu öffnen und einige T-SQL-Abfragen auszuführen:

1. Wählen Sie mit der rechten Maustaste die Option **mySampleDataWarehouse** aus, und wählen Sie dann **Neue Abfrage** aus. Ein neues Abfragefenster wird geöffnet.

2. Geben Sie im Abfragefenster den folgenden Befehl ein, um eine Liste mit Datenbanken anzuzeigen.

    ```sql
    SELECT * FROM sys.databases
    ```

3. wählen Sie **Execute** (Ausführen) aus. Die Abfrageergebnisse zeigen zwei Datenbanken an: **master** und **mySampleDataWarehouse**.

   ![Abfragen von Datenbanken](./media/create-data-warehouse-portal/query-databases.png)

4. Zum Überprüfen einiger Daten verwenden Sie den folgenden Befehl, um die Anzahl der Kunden mit dem Nachnamen Baar anzuzeigen, die drei Kinder zu Hause haben. In den Ergebnissen sind sechs Kunden aufgelistet.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Abfragen von „dbo.dimCustomer“](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Ihnen werden Gebühren für Data Warehouse-Einheiten und die in Ihrem dedizierten SQL-Pool (vormals SQL DW) gespeicherten Daten in Rechnung gestellt. Diese Compute- und Speicherressourcen werden separat in Rechnung gestellt.

- Falls Sie die Daten im Speicher belassen möchten, können Sie Computeressourcen anhalten, wenn Sie den dedizierten SQL-Pool (vormals SQL DW) nicht verwenden. Wenn Sie Computeressourcen anhalten, werden Ihnen nur die Datenspeichergebühren in Rechnung gestellt. Sie können Computeressourcen fortsetzen, wenn Sie mit den Daten arbeiten möchten.

- Wenn künftig keine Gebühren mehr anfallen sollen, können Sie den dedizierten SQL-Pool (vormals SQL DW) löschen.

Führen Sie die folgenden Schritte aus, um nicht länger benötigte Ressourcen zu bereinigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie Ihren dedizierten SQL-Pool (vormals SQL DW) aus.

   ![Bereinigen von Ressourcen](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Wählen Sie zum Anhalten von Computeressourcen die Schaltfläche **Anhalten**. Wenn der dedizierte SQL-Pool (vormals SQL DW) angehalten ist, wird die Schaltfläche **Fortsetzen** angezeigt. Wählen Sie zum Fortsetzen der Computeressourcen die Option **Fortsetzen** aus.

3. Wenn Sie den dedizierten SQL-Pool (vormals SQL DW) entfernen möchten, damit keine Gebühren für Compute- oder Speicherressourcen anfallen, wählen Sie **Löschen** aus.

4. Wählen Sie zum Entfernen des von Ihnen erstellten Servers die Option **sqlpoolservername.database.windows.net** (siehe Abbildung oben) und dann **Löschen** aus. Seien Sie bei diesem Löschvorgang vorsichtig, da beim Löschen des Servers auch alle Datenbanken gelöscht werden, die dem Server zugewiesen sind.

5. Wählen Sie zum Entfernen der Ressourcengruppe die Option **myResourceGroup** und dann **Ressourcengruppe löschen**.

Möchten Sie Ihre Cloudausgaben optimieren und Geld sparen?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Laden von Daten in Ihren dedizierten SQL-Pool (vormals SQL DW) finden Sie im [Artikel zum Laden von Daten in einen dedizierten SQL-Pool](load-data-from-azure-blob-storage-using-copy.md).
