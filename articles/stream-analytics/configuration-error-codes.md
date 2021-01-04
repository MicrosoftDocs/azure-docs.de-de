---
title: Konfigurationsfehlercodes – Azure Stream Analytics
description: Beheben Sie Probleme in Azure Stream Analytics mit Konfigurationsfehlercodes.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 80179506c133de92b56d476c9aa99d55c3e3bbd9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305805"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Konfigurationsfehlercodes in Azure Stream Analytics

Unerwartetes Verhalten Ihres Azure Stream Analytics-Auftrags können Sie mithilfe von Aktivitätsprotokollen und Ressourcenprotokollen debuggen. In diesem Artikel ist die Beschreibung für jeden Konfigurationsfehlercode aufgeführt. Konfigurationsfehler stehen mit Ihrer Auftragskonfiguration oder den Eingabe- und Ausgabekonfigurationen im Zusammenhang.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **Ursache:** Event Hub hat einen Fehler vom Typ *Nicht autorisierter Zugriff* ausgelöst.

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Ursache:** Es gibt mehrere Event Hub-Empfänger mit unterschiedlichen Epochenwerten.
* **Empfehlung** : Stellen Sie sicher, dass weder *Service Bus Explorer* noch eine *EventProcessorHost* -Anwendung verbunden ist, während der Stream Analytics-Auftrag ausgeführt wird.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **Ursache:** Stream Analytics kann keine Verbindung mit einer Partition herstellen, weil die maximal zulässige Anzahl von Empfängern pro Partition in einer Consumergruppe erreicht wurde.
* **Empfehlung** : Stellen Sie sicher, dass andere Stream Analytics-Aufträge oder Service Bus Explorer nicht dieselbe Consumergruppe verwenden.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Ursache:** Fehler beim Schreiben von Daten in Event Hub aufgrund einer Drosselung.
* **Empfehlung** : Falls dieses Problem regelmäßig auftritt, erhöhen Sie den Durchsatz.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Ursache:** Die angegebene Verbindungskonfiguration ist fehlerhaft.
* **Empfehlung** : Korrigieren Sie die Konfiguration, und starten Sie den Auftrag neu.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **Ursache:** Der Event Hub-Host ist nicht erreichbar.
* **Empfehlung** : Stellen Sie sicher, dass der angegebene Hostname richtig ist.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **Ursache:** Der EventHub-Absender hat eine unerwartete EventHub-Partitionsanzahl festgestellt.
* **Empfehlung** : Starten Sie den Stream Analytics-Auftrag neu, wenn sich die EventHub-Partitionsanzahl geändert hat.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Ursache:** Stream Analytics konnte den Partitionsschlüssel einer bestimmten Cosmos DB-Sammlung in der Datenbank nicht finden.
* **Empfehlung** : Stellen Sie sicher, dass in Cosmos DB ein gültiger Partitionsschlüssel für die Sammlung angegeben ist.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Ursache:** Wird ausgelöst, wenn ein Partitionsschlüssel kein Blattknoten ist und sich nicht auf der obersten Ebene befindet.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Ursache:** Die Abfrageausgabe darf nicht die Spalte \[id] enthalten, wenn eine andere Spalte als Primärschlüsseleigenschaft ausgewählt wurde.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Ursache:** Stream Analytics kann eine Cosmos DB-Datenbank nicht finden.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Ursache:** Stream Analytics kann eine bestimmte Cosmos DB-Sammlung in einer Datenbank nicht finden.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Ursache:** Fehler beim Schreiben von Daten aufgrund einer Drosselung durch Cosmos DB.
* **Empfehlung** : Führen Sie für die Leistungsstufe der Sammlung ein Upgrade durch, und optimieren Sie die Leistung Ihrer Datenbank.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **Ursache:** Authentifizierungsfehler beim Stream Analytics-Auftrag.
* **Empfehlung** : Stellen Sie sicher, dass die SQL-Datenbank-Verbindungszeichenfolge korrekt ist.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **Ursache:** Authentifizierungsfehler beim Stream Analytics-Auftrag. 
* **Empfehlung** : Stellen Sie sicher, dass der Kontoname ordnungsgemäß konfiguriert ist und die verwaltete Identität des Auftrags auf die SQL-Datenbank zugreifen kann.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **Ursache:** Stream Analytics kann die Schemainformationen für eine bestimmte Tabelle nicht finden.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Ursache:** SQL-Datenbank wird nicht unterstützt.
* **Empfehlung** : Verwenden Sie einen dedizierten SQL-Pool.

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung für Eingangsverbindungen](stream-analytics-troubleshoot-input.md)
* [Problembehandlung von Azure Stream Analytics-Ausgaben](stream-analytics-troubleshoot-output.md)
* [Problembehandlung von Azure Stream Analytics-Abfragen](stream-analytics-troubleshoot-query.md)
* [Problembehandlung von Azure Stream Analytics mit Ressourcenprotokollen](stream-analytics-job-diagnostic-logs.md)
* [Datenfehler in Azure Stream Analytics](data-errors.md)
