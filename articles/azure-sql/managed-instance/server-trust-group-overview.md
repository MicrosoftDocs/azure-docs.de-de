---
title: Serververtrauensgruppe
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie mehr über Serververtrauensgruppen und die Verwaltung der Vertrauensstellung zwischen Instanzen von Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: f9d5528746a85668677ab122d98e954bd39cd163
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790728"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>Sie verwenden Serververtrauensgruppen, um die Vertrauensstellung zwischen Instanzen von SQL Managed Instance einzurichten und zu verwalten.
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Das Konzept von Serververtrauensgruppen dient zum Verwalten der Vertrauensstellungen zwischen Instanzen von Azure SQL Managed Instance. Durch das Erstellen einer Gruppe wird eine zertifikatbasierte Vertrauensstellung zwischen den Mitgliedern eingerichtet. Diese Vertrauensstellung kann für verschiedene instanzübergreifende Szenarien verwendet werden. Durch das Entfernen von Servern aus der Gruppe oder das Löschen der Gruppe wird die Vertrauensstellung zwischen den Servern aufgehoben. Benutzer, die eine Serververtrauensgruppe erstellen oder löschen möchten, müssen über Schreibberechtigungen für die verwaltete Instanz verfügen.
Die [Serververtrauensgruppe](/azure/templates/microsoft.sql/allversions) ist ein Azure Resource Manager-Objekt, das im Azure-Portal als **SQL-Vertrauensgruppe** bezeichnet wird.

> [!NOTE]
> Serververtrauensgruppen wurden in der öffentlichen Vorschau verteilter Transaktionen zwischen Azure SQL Managed Instance eingeführt und weisen derzeit einige Einschränkungen auf, die später in diesem Artikel beschrieben werden.

## <a name="server-trust-group-setup"></a>Einrichten von Serververtrauensgruppen

Im folgenden Abschnitt wird das Einrichten einer Serververtrauensgruppe beschrieben.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Navigieren Sie zu der Instanz von Azure SQL Managed Instance, die Sie einer neu erstellten Serververtrauensgruppe hinzufügen möchten.

3. Wählen Sie in den Einstellungen für die **Sicherheit** die Registerkarte **SQL-Vertrauensgruppen** aus.

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Serververtrauensgruppen":::

4. Wählen Sie auf der Konfigurationsseite der Serververtrauensgruppe das Symbol **Neue Gruppe** aus.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Serververtrauensgruppen":::

5. Legen Sie auf dem Blatt zum Erstellen der **SQL-Vertrauensgruppe** den **Gruppennamen** fest. Er muss in allen Regionen mit Gruppenmitgliedern eindeutig sein. Der **Vertrauensbereich** legt den Typ des instanzübergreifenden Szenarios fest, das mit der Serververtrauensgruppe ermöglicht wird. In der Vorschauphase ist der einzig verfügbare Vertrauensbereich **Verteilte Transaktionen** . Er ist daher bereits ausgewählt und kann nicht geändert werden. Alle **Gruppenmitglieder** müssen demselben **Abonnement** angehören, sie können sich jedoch in verschiedenen Ressourcengruppen befinden. Wählen Sie die **Ressourcengruppe** und den bzw. die **SQL-Server/-Instanz** aus. Damit legen Sie die Instanz von Azure SQL Managed Instance fest, die Mitglied der Gruppe werden soll.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Serververtrauensgruppen":::

6. Nachdem alle erforderlichen Felder ausgefüllt wurden, klicken Sie auf **Speichern** .

## <a name="server-trust-group-maintenance-and-deletion"></a>Verwalten und Löschen von Serververtrauensgruppen

Serververtrauensgruppen können nicht bearbeitet werden. Wenn Sie eine verwaltete Instanz aus einer Gruppe entfernen möchten, müssen Sie die Gruppe löschen und eine neue erstellen.

Im folgenden Abschnitt wird das Löschen einer Serververtrauensgruppe beschrieben. 
1. Öffnen Sie das Azure-Portal.
2. Navigieren Sie zu einer verwalteten Instanz, die der Vertrauensgruppe angehört.
3. Wählen Sie in den Einstellungen für die **Sicherheit** die Registerkarte **SQL-Vertrauensgruppen** aus.
4. Wählen Sie die Vertrauensgruppe aus, die Sie löschen möchten.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Serververtrauensgruppen":::
5. Klicken Sie auf **Gruppe löschen** .
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Serververtrauensgruppen":::
6. Geben Sie den Namen der Serververtrauensgruppe ein, um den Löschvorgang zu bestätigen, und klicken Sie dann auf **Löschen** .
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Serververtrauensgruppen":::

> [!NOTE]
> Durch das Löschen der Serververtrauensgruppe wird die Vertrauensstellung zwischen den beiden verwalteten Instanzen möglicherweise nicht sofort aufgehoben. Sie können das Aufheben von Vertrauensstellungen erzwingen, indem Sie ein [Failover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) der verwalteten Instanzen auslösen. Aktuelle Informationen hierzu finden Sie unter [Bekannte Probleme](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues).

## <a name="limitations"></a>Einschränkungen

Während der öffentlichen Vorschau gelten die folgenden Einschränkungen für Serververtrauensgruppen.
 * Der Name der Serververtrauensgruppe muss in allen Regionen, in denen sich Mitglieder befinden, eindeutig sein.
 * Die Gruppe darf nur Instanzen von Azure SQL Managed Instance enthalten, die außerdem demselben Azure-Abonnement angehören müssen.
 * In der Vorschau darf die Gruppe nur genau zwei verwaltete Instanzen enthalten. Um verteilte Transaktionen über mehr als zwei verwaltete Instanzen auszuführen, müssen Sie für jedes Paar verwalteter Instanzen eine Serververtrauensgruppe erstellen.
 * Verteilte Transaktionen stellen den einzig gültigen Bereich für Serververtrauensgruppen dar.
 * Die Serververtrauensgruppe kann nur über das Azure-Portal verwaltet werden. Unterstützung für PowerShell und die Befehlszeilenschnittstelle wird zu einem späteren Zeitpunkt eingeführt.
 * Serververtrauensgruppen können über das Azure-Portal nicht bearbeitet werden. Sie können nur erstellt oder gelöscht werden.
 * Je nach Ihrem Szenario gelten möglicherweise weitere Einschränkungen für verteilte Transaktionen. Vor allem muss die Verbindung zwischen den verwalteten Instanzen über private Endpunkte, ein VNET oder VNET-Peering erfolgen. Informieren Sie sich unbedingt über die aktuellen [Einschränkungen für verteilte Transaktionen für verwaltete Instanzen](../database/elastic-transactions-overview.md#limitations).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu verteilten Transaktionen in Azure SQL Managed Instance finden Sie unter [Verteilte Transaktionen](../database/elastic-transactions-overview.md).
* Informationen zu Releaseupdates und bekannten Problemen finden Sie in den [Versionshinweisen zu Managed Instance](../database/doc-changes-updates-release-notes.md).
* Wenn Sie weitere Features benötigen, können Sie diese im [Forum für Managed Instance](https://feedback.azure.com/forums/915676-sql-managed-instance) veröffentlichen.