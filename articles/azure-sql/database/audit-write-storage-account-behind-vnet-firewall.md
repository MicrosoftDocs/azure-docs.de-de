---
title: Überwachen in einem Speicherkonto hinter einem VNET oder einer Firewall
description: Konfigurieren der Überwachung zum Schreiben von Datenbankereignissen in einem Speicherkonto hinter einem virtuellen Netzwerk und einer Firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 908c9f1d05c83eaa58f77b79a32d956898c35076
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348252"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Schreiben von Überwachungsprotokollen in ein Speicherkonto hinter einem VNET oder einer Firewall
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Bei der Überwachung für [Azure SQL-Datenbank](sql-database-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) wird das Schreiben von Datenbankereignissen in ein [Azure Storage-Konto](../../storage/common/storage-account-overview.md) hinter einem virtuellen Netzwerk und einer Firewall unterstützt.

In diesem Artikel werden zwei Möglichkeiten erläutert, mit denen Sie Azure SQL-Datenbank und das Azure Storage-Konto für diese Option konfigurieren können. Beim ersten Verfahren wird das Azure-Portal und beim zweiten Verfahren REST verwendet.

## <a name="background"></a>Hintergrund

[Azure Virtual Network (VNET)](../../virtual-network/virtual-networks-overview.md) ist der grundlegende Baustein für Ihr privates Netzwerk in Azure. Mit VNET können zahlreiche Arten von Azure-Ressourcen (beispielsweise virtuelle Azure-Computer) sicher untereinander sowie mit dem Internet und mit lokalen Netzwerken kommunizieren. VNET ähnelt einem herkömmlichen Netzwerk in Ihrem Rechenzentrum, bietet jedoch zusätzliche Vorteile der Infrastruktur von Azure, z. B. Skalierbarkeit, Verfügbarkeit und Isolation.

Weitere Informationen zu den VNET-Konzepten, bewährten Methoden und vieles andere mehr finden Sie unter [Was ist Azure Virtual Network?](../../virtual-network/virtual-networks-overview.md).

Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie unter [Schnellstart: Erstellen eines virtuellen Netzwerks im Azure-Portal](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein, damit Überwachungsprotokolle in ein Speicherkonto hinter einem VNET oder einer Firewall geschrieben werden können:

> [!div class="checklist"]
>
> * Ein Speicherkonto vom Typ „Universell V2“. Wenn Sie über ein Speicherkonto vom Typ „Universell V1“ ober über ein Blob Storage-Konto verfügen, [führen Sie ein Upgrade auf ein Speicherkonto vom Typ „Universell V2“ durch](../../storage/common/storage-account-upgrade.md). Weitere Informationen finden Sie unter [Speicherkontentypen](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * Das Speicherkonto muss sich im selben Abonnement und am selben Standort wie der [logische SQL-Server](logical-servers.md) befinden.
> * Das Azure Storage-Konto erfordert `Allow trusted Microsoft services to access this storage account`. Legen Sie dies im Speicherkonto unter **Firewalls und virtuelle Netzwerke** fest.
> * Sie benötigen für das ausgewählte Speicherkonto die Berechtigung `Microsoft.Authorization/roleAssignments/write`. Weitere Informationen finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Konfigurieren im Azure-Portal

Stellen Sie mit Ihrem Abonnement eine Verbindung mit dem [Azure-Portal](https://portal.azure.com) her. Navigieren Sie zu der Ressourcengruppe und dem Server.

1. Klicken Sie unter der Überschrift „Sicherheit“ auf **Überwachung**. Wählen Sie **Ein** aus.

2. Wählen Sie **Speicher**. Wählen Sie das Speicherkonto aus, in dem die Protokolle gespeichert werden sollen. Das Speicherkonto muss den unter [Voraussetzungen](#prerequisites) aufgeführten Anforderungen entsprechen.

3. Öffnen Sie **Speicherdetails**.

  > [!NOTE]
  > Wenn sich das ausgewählte Speicherkonto hinter einem VNET befindet, wird die folgende Meldung angezeigt:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Wenn diese Meldung nicht angezeigt wird, befindet sich das Speicherkonto nicht hinter einem VNET.

4. Wählen Sie die Anzahl von Tagen für die Beibehaltungsdauer aus. Klicken Sie dann auf **OK**. Protokolle, die älter als die Aufbewahrungsdauer sind, werden gelöscht.

5. Wählen Sie in den Überwachungseinstellungen **Speichern** aus.

Sie haben die Überwachung erfolgreich zum Schreiben in ein Speicherkonto hinter einem VNET oder einer Firewall konfiguriert.

## <a name="configure-with-rest-commands"></a>Konfigurieren mit REST-Befehlen

Als Alternative zur Verwendung des Azure-Portals können Sie mithilfe von REST-Befehlen die Überwachung zum Schreiben von Datenbankereignissen in ein Speicherkonto hinter einem VNET und einer Firewall konfigurieren.

Die Beispielskripts in diesem Abschnitt erfordern, dass Sie das Skript aktualisieren, bevor Sie es ausführen. Ersetzen Sie die folgenden Werte in den Skripts:

|Beispielwert|Beispielbeschreibung|
|:-----|:-----|
|`<subscriptionId>`| Azure-Abonnement-ID|
|`<resource group>`| Resource group|
|`<logical SQL Server>`| Servername|
|`<administrator login>`| Administratorkonto |
|`<complex password>`| Komplexes Kennwort für das Administratorkonto|

So konfigurieren Sie die SQL-Überwachung für das Schreiben von Ereignissen in ein Speicherkonto hinter einem VNET oder einer Firewall:

1. Registrieren Sie Ihren Server bei Azure Active Directory (Azure AD). Verwenden Sie entweder PowerShell oder die REST-API.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**REST-API**](/rest/api/sql/servers/createorupdate):

   Beispiel für eine Anforderung

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Anforderungstext

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
     }
   }
   ```

2. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zum Speicherkonto. Suchen Sie **Zugriffssteuerung (IAM)** , und klicken Sie auf **Rollenzuweisung hinzufügen**. Weisen Sie dem Server, der die im vorherigen Schritt bei Azure Active Directory (Azure AD) registrierte Datenbank hostet, die Azure-Rolle **Mitwirkender an Storage-Blobdaten** zu.

   > [!NOTE]
   > Nur Mitglieder mit der Berechtigung „Besitzer“ können diesen Schritt ausführen. Informationen zu verschiedenen integrierten Azure-Rollen finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

3. Konfigurieren Sie die [Blobüberwachungsrichtlinie des Servers](/rest/api/sql/server%20auditing%20settings/createorupdate), ohne einen *storageAccountAccessKey* anzugeben:

   Beispiel für eine Anforderung

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
   ```

   Anforderungstext

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

- [Erstellen oder Aktualisieren der Datenbanküberwachungsrichtlinie (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Erstellen oder Aktualisieren der Serverüberwachungsrichtlinie (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Verwenden von Azure Resource Manager-Vorlagen

Sie können die Überwachung so konfigurieren, dass Datenbankereignisse in ein Speicherkonto hinter einem virtuellen Netzwerk und einer Firewall geschrieben werden, indem Sie die [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Vorlage verwenden, wie im folgenden Beispiel gezeigt:

> [!IMPORTANT]
> Um ein Speicherkonto hinter einem virtuellen Netzwerk und einer Firewall zu verwenden, müssen Sie den Parameter **isStorageBehindVnet** auf TRUE festlegen.

- [Bereitstellen einer Azure SQL Server-Instanz mit aktivierter Überwachung zum Schreiben von Überwachungsprotokollen in Blob Storage](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> Das verknüpften Beispiel befindet sich in einem externen öffentlichen Repository, wird wie besehen ohne Gewähr zur Verfügung gestellt und wird von keinem Microsoft-Supportprogramm/-dienst unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von PowerShell zum Erstellen eines VNET-Dienstendpunkts und einer VNET-Regel für Azure SQL-Datenbank](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [VNET-Regeln: Vorgänge mit REST-APIs](/rest/api/sql/virtualnetworkrules)
* [Verwenden von VNET-Dienstendpunkten und -Regeln für Server](vnet-service-endpoint-rule-overview.md)
