---
title: Manuelles Initiieren eines Failovers für SQL Managed Instance
description: Erfahren Sie, wie Sie ein manuelles Failover der primären und sekundären Replikate in einer Azure SQL Managed Instance ausführen.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 08/31/2020
ms.openlocfilehash: 51e9e66e2fd8ff60dd20c275a66fd13c047cc629
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985517"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Vom Benutzer initiiertes manuelles Failover für SQL Managed Instance

In diesem Artikel wird erläutert, wie Sie ein manuelles Failover eines primären Knotens auf den SQL Managed Instance-Dienstebenen „Universell“ und „Unternehmenskritisch“ ausführen. Außerdem erfahren Sie, wie Sie ein manuelles Failover eines sekundären, schreibgeschützten Replikatknotens nur auf der Dienstebene „Unternehmenskritisch“ ausführen.

## <a name="when-to-use-manual-failover"></a>Verwendungszwecke für ein manuelles Failover

[Hochverfügbarkeit](../database/high-availability-sla.md) ist ein wesentlicher Bestandteil der SQL Managed Instance-Plattform und transparent für Ihre Datenbankanwendungen. Failovervorgänge vom primären auf den sekundären Knoten können erfolgen, wenn ein Knoten beeinträchtigt ist oder ein Fehler erkannt wird oder während die regelmäßigen monatlichen Softwareupdates ausgeführt werden. Dies alles sind erwartete Verhaltensweisen für alle Anwendungen mit SQL Managed Instance in Azure.

In folgenden Fällen sollten Sie allerdings ein [manuelles Failover](../database/high-availability-sla.md#testing-application-fault-resiliency) in SQL Managed Instance in Erwägung ziehen:
- Testen der Anwendung auf Failoverresilienz vor dem Bereitstellen in der Produktion
- Testen von End-to-End-Systemen auf Fehlerresilienz bei automatischen Failovervorgängen
- Testen der Auswirkungen von Failovervorgängen auf vorhandene Datenbanksitzungen
- Überprüfen, ob ein Failover aufgrund von Änderungen bei der Netzwerklatenz die End-to-End-Leistung verändert
- Wenn die Abfrageleistung beeinträchtigt ist, kann ein manuelles Failover in einigen Fällen die Leistungsprobleme mindern.

> [!NOTE]
> Bevor Sie eine Anwendung in der Produktion bereitstellen, sollten Sie sicherstellen, dass sie resilient gegenüber Failovervorgängen ist. So reduzieren Sie das Risiko, dass die Anwendung in der Produktion ausfällt, und verbessern die Verfügbarkeit Ihrer Anwendung für Ihre Kunden.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Initiieren eines manuellen Failovers in SQL Managed Instance

### <a name="azure-rbac-permissions-required"></a>Erforderliche Azure RBAC-Berechtigungen

Benutzer, die einen Failover initiieren, müssen über eine der folgenden Azure-Rollen verfügen:

- Rolle „Besitzer des Abonnements“ oder
- Rolle „Mitwirkender“ der verwalteten Instanz oder
- Benutzerdefinierte Rolle mit der folgenden Berechtigung:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>PowerShell

Az.Sql muss mindestens in Version [v2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) vorliegen. Verwenden Sie die [Azure Cloud Shell](../../cloud-shell/overview.md) im Azure-Portal – dort finden Sie immer die neueste PowerShell-Version. 

Verwenden Sie vorab das folgende PowerShell-Skript, um die erforderlichen Azure-Module zu installieren. Wählen Sie außerdem das Abonnement aus, in dem sich die Managed Instanz befindet, für die das Failover ausgeführt werden soll.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Verwenden Sie den PowerShell-Befehl [Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) mit dem folgenden Beispiel, um ein Failover des primären Knotens zu initiieren. Dies gilt sowohl für die Dienstebene „Unternehmenskritisch“ als auch für die Ebene „Universell“.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

Verwenden Sie den folgenden PowerShell-Befehl, um ein Failover des sekundären schreibgeschützten Knotens auszuführen. Dies gilt nur die Dienstebene „Unternehmenskritisch“.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>Verwenden von CLI

Stellen Sie sicher, dass die neuesten CLI-Skripts installiert sind.

Verwenden Sie den CLI-Befehl „az sql mi failover“ mit dem folgenden Beispiel, um ein Failover des primären Knotens zu initiieren. Dies gilt sowohl für die Dienstebene „Unternehmenskritisch“ als auch für die Ebene „Universell“.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

Verwenden Sie den folgenden CLI-Befehl, um ein Failover des sekundären schreibgeschützten Knotens auszuführen. Dies gilt nur die Dienstebene „Unternehmenskritisch“.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>Verwenden der REST-API

Erfahrene Benutzer, die Failovervorgänge ihrer SQL Managed Instances zur Implementierung einer Pipeline für kontinuierliche Tests oder zur automatisierten Minimierung von Leistungsproblemen automatisieren müssen, können zur Initiierung auch einen API-Aufruf verwenden. Weitere Informationen finden Sie unter [Managed Instances – REST-API für Failover](/rest/api/sql/managed%20instances%20-%20failover/failover).

Wenn Sie ein Failover über einen REST-API-Aufruf initiieren möchten, generieren Sie zunächst mithilfe eines API-Clients Ihrer Wahl das Authentifizierungstoken. Das generierte Authentifizierungstoken wird als Autorisierungseigenschaft im Header der API-Anforderung verwendet und ist obligatorisch.

Der folgende Code ist ein Beispiel für den aufzurufenden API-URI:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

Die folgenden Eigenschaften müssen im API-Aufruf übergeben werden:

| **API-Eigenschaft** | **Parameter** |
| --- | --- |
| subscriptionId | Die ID des Abonnements, in dem die Managed Instance bereitgestellt wird. |
| resourceGroupName | Die Ressourcengruppe, die die Managed Instance enthält. |
| managedInstanceName | Der Name der Managed Instance. |
| replicaType | (Optional, „Primary“ oder „ReadableSecondary“). Diese Parameter repräsentieren den Replikattyp, für den das Failover ausgeführt werden soll: primäres oder lesbares sekundäres Replikat. Wenn hier kein Wert angegeben ist, wird das Failover standardmäßig im primären Replikat initiiert. |
| api-version | Statischer Wert, muss derzeit „2019-06-01-preview“ lauten. |

Die API-Antwort gibt einen der folgenden beiden Werte zurück:

- 202 – Akzeptiert
- Einer der Anforderungsfehler vom Typ 400.

Der Status des Vorgangs kann anhand der API-Antworten in Antwortheadern nachverfolgt werden. Weitere Informationen finden Sie unter [Status asynchroner Vorgänge in Azure](../../azure-resource-manager/management/async-operations.md).

## <a name="monitor-the-failover"></a>Überwachen des Failovers

Um den Fortschritt eines vom Benutzer initiierten manuellen Failovervorgangs zu überwachen, führen Sie die folgende T-SQL-Abfrage in der SQL Managed Instance in Ihrem bevorzugten Client aus (z. B. in SSMS). Die Abfrage liest die Systemsicht „sys.dm_hadr_fabric_replica_states“ und meldet die in der Instanz verfügbaren Replikate. Führen Sie dieselbe Abfrage nach dem Initiieren des manuellen Failovers erneut aus.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

Vor dem Initiieren des Failovers gibt die Ausgabe das aktuelle primäre Replikat in der Dienstebene „Unternehmenskritisch“ an, die ein primäres und drei sekundäre Replikate in der Always On-Verfügbarkeitsgruppe enthält. Nach einem Failover müsste bei der erneuten Ausführung der Abfrage eine Änderung des primären Knotens angezeigt werden.

In der Dienstebene „Universell“ erhalten Sie eine andere Ausgabe als in der Dienstebene „Unternehmenskritisch“. Das liegt daran, dass die Dienstebene „Universell“ auf einem einzelnen Knoten basiert. Die Ausgabe einer T-SQL-Abfrage in der Dienstebene „Universell“ zeigt vor und nach dem Failover nur einen einzelnen Knoten. Der Verlust der Konnektivität mit Ihrem Client während des Failovers, der in aller Regel weniger als eine Minute dauert, ist ein Hinweis auf die Ausführung des Failovers.

> [!NOTE]
> Bei Workloads mit **hoher Intensität** kann die vollständige Ausführung eines Failovervorgangs (nicht die kurze Nichtverfügbarkeit) mehrere Minuten dauern. Das liegt daran, dass die Instanz-Engine alle aktuellen Transaktionen auf dem primären Knoten ausführen und den sekundären Knoten auf den neuesten Stand bringen muss, bevor das Failover ausgeführt werden kann.

> [!IMPORTANT]
> Folgende funktionsbezogene Einschränkungen gelten bei vom Benutzer initiierten manuellen Failovervorgängen:
> - Auf ein und derselben Managed Instance kann alle **30 Minuten** immer nur ein (1) Failover initiiert werden.
> - Bei unternehmenskritischen Instanzen muss ein Quorum mit Replikaten vorhanden sein, damit die Failoveranforderung akzeptiert wird.
> - Bei unternehmenskritischen Instanzen kann nicht angegeben werden, auf welchem lesbaren sekundären Replikat das Failover initiiert werden soll.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Hochverfügbarkeit für Azure SQL Managed Instance](../database/high-availability-sla.md).
- Eine Übersicht finden Sie unter [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
