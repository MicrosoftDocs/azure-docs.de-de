---
title: Serverprotokolle in Azure Database for PostgreSQL (Einzelserver)
description: Dieser Artikel beschreibt, wie Azure Database for PostgreSQL (Einzelserver) Protokolle für Abfragen und Fehler generiert und wie die Protokollaufbewahrung konfiguriert wird.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4d1cf2c59e324cedd9b747b1ac65d6edcb9deb45
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067392"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Serverprotokolle in Azure Database for PostgreSQL (Einzelserver)
Azure Database for PostgreSQL generiert Abfragen und Fehlerprotokolle. Diese Abfrage- und Fehlerprotokolle dienen zur Identifizierung, Behebung und Reparatur von Konfigurationsfehlern und suboptimaler Leistung. (Der Zugriff auf Transaktionsprotokolle ist nicht enthalten.) 

## <a name="configure-logging"></a>Konfigurieren der Protokollierung 
Sie können die Protokollierung auf dem Server mithilfe von Serverparametern für die Protokollierung konfigurieren. Auf jedem neuen Server sind **log_checkpoints** und **log_connections** standardmäßig aktiviert. Es gibt zusätzliche Parameter, die Sie entsprechend Ihren Protokollierungsanforderungen anpassen können: 

![Azure Database for PostgreSQL – Protokollierungsparameter](./media/concepts-server-logs/log-parameters.png)

Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu PostgreSQL unter [Fehlerberichterstattung und -protokollierung](https://www.postgresql.org/docs/current/static/runtime-config-logging.html). Weitere Informationen dazu, wie Sie Azure Database for PostgreSQL-Parameter konfigurieren, finden Sie in der Dokumentation zum [Portal](howto-configure-server-parameters-using-portal.md) bzw. zur [Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Zugreifen auf Serverprotokolle über das Portal oder die Befehlszeilenschnittstelle
Wenn Sie Protokolle aktiviert haben, können Sie im Protokollspeicher von Azure Database for PostgreSQL über das [Azure-Portal](howto-configure-server-logs-in-portal.md), die [Azure-Befehlszeilenschnittstelle](howto-configure-server-logs-using-cli.md) und die Azure-REST-APIs auf sie zugreifen. Die Protokolldateien rotieren jede Stunde oder bei einer Größe von 100 MB, je nachdem, welcher Fall zuerst eintritt. Mithilfe des mit Ihrem Server verknüpften Parameters  **log\_retention\_period** können Sie den Aufbewahrungszeitraum für diesen Protokollspeicher festlegen. Der Standardwert ist 3 Tage. Der Maximalwert beträgt 7 Tage. Ihrem Server muss genügend Speicher zugewiesen sein, damit die Protokolldateien gespeichert werden können. (Dieser retention-Parameter steuert nicht Azure-Diagnoseprotokolle.)


## <a name="diagnostic-logs"></a>Diagnoseprotokolle
Azure Database for PostgreSQL ist in Azure Monitor-Diagnoseprotokolle integriert. Nachdem Sie Protokolle auf Ihrem PostgreSQL-Server aktiviert haben, können Sie auswählen, dass sie an [Azure Monitor-Protokolle](../azure-monitor/log-query/log-query-overview.md), Event Hubs oder Azure Storage ausgegeben werden sollen. Weitere Informationen zum Aktivieren von Diagnoseprotokollen finden Sie im Gewusst-wie-Abschnitt der [Dokumentation zu Diagnoseprotokollen](../azure-monitor/platform/diagnostic-logs-overview.md). 

> [!IMPORTANT]
> Dieses Diagnosefeature für Serverprotokolle steht nur in den [Tarifen](concepts-pricing-tiers.md) „Universell“ und „Arbeitsspeicheroptimiert“ zur Verfügung.

In der folgenden Tabelle wird der Inhalt der einzelnen Protokolle beschrieben. Je nach dem ausgewählten Ausgabeendpunkt können die enthaltenen Felder und ihre Reihenfolge variieren. 

|**Feld** | **Beschreibung** |
|---|---|
| TenantId | Ihre Mandanten-ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| Type | Typ des Protokolls Immer `AzureDiagnostics` |
| SubscriptionId | GUID für das Abonnement, zu dem der Server gehört |
| ResourceGroup | Name der Ressourcengruppe, zu der der Server gehört |
| ResourceProvider | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| resourceId | Ressourcen-URI |
| Resource | Name des Servers |
| Category (Kategorie) | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Beispiel für die Protokollierungsstufe: LOG, ERROR, NOTICE |
| `Message` | Primäre Protokollmeldung | 
| Domäne | Serverversion, Beispiel: postgres-10 |
| Detail | Sekundäre Protokollmeldung (falls zutreffend) |
| ColumnName | Name der Spalte (falls zutreffend) |
| SchemaName | Name des Schemas (falls zutreffend) |
| DatatypeName | Name des Datentyps (falls zutreffend) |
| LogicalServerName | Name des Servers | 
| _ResourceId | Ressourcen-URI |

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über den Zugriff auf Protokolle über das [Azure-Portal](howto-configure-server-logs-in-portal.md) oder die [Azure-Befehlszeilenschnittstelle](howto-configure-server-logs-using-cli.md).
- Erfahren Sie mehr über die [Preise für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
