---
title: Externe Fehlercodes – Azure Stream Analytics
description: Beheben Sie Probleme in Azure Stream Analytics mit externen Fehlercodes.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 61d6556467bc6f1494e11ce3a01bf5266b740f3f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305784"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Externe Fehlercodes in Azure Stream Analytics

Unerwartetes Verhalten Ihres Azure Stream Analytics-Auftrags können Sie mithilfe von Aktivitätsprotokollen und Ressourcenprotokollen debuggen. In diesem Artikel ist die Beschreibung für jeden externen Fehlercode aufgeführt. Externe Fehler sind allgemeine Fehler, die von einem Upstream- oder Downstreamdienst ausgelöst werden und von Stream Analytics nicht als Datenfehler, Konfigurationsfehler oder Fehler der externen Verfügbarkeit eingeordnet werden können.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **Ursache:** Fehler beim Initialisieren eines Adapters.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Ursache:** Fehler beim Schreiben von Daten an einen Adapter.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Ursache:** Azure Functions hat einen HTTP-Fehler zurückgegeben.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Ursache:** Stream Analytics-Fehler beim Schreiben von Ereignissen an Azure Functions.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Ursache:** Bei der Ausgabe an Azure Functions ist ein Umleitungsfehler aufgetreten.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Ursache:** Bei der Ausgabe an Azure Functions ist ein Clientfehler aufgetreten.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Ursache:** Bei der Ausgabe an Azure Functions ist ein Serverfehler aufgetreten.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Ursache:** Fehler beim Schreiben an Azure Functions, weil die HTTP-Anforderung das Timeout überschritten hat. 
* **Empfehlung** : Überprüfen Sie die Azure Functions-Protokolle auf mögliche Verzögerungen.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Ursache:** Die Eingabeoffsets sind ungültig. Dies kann auf ein Failover zurückzuführen sein.
* **Empfehlung** : Starten Sie den Stream Analytics-Auftrag ab dem letzten Ausgabezeitpunkt neu.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Ursache:** Fehler beim Senden von Daten an Event Hub.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Ursache:** Stream Analytics konnte nach der maximalen Anzahl von Wiederholungsversuchen keine Verbindung mit einem Cosmos DB-Konto herstellen.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Ursache:** Stream Analytics konnte nach der maximalen Anzahl von Wiederholungsversuchen die Cosmos DB-Datenbank und -Sammlung nicht abfragen.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Ursache:** Cosmos DB kann nach mehreren Wiederholungsversuchen keine gespeicherte Prozedur erstellen.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Ursache:** Die gespeicherte upsert-Prozedur hat einen Fehler zurückgegeben. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Ursache:** Stream Analytics kann die SQL-Datenbank-Ausgabe nicht initialisieren.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Ursache:** Stream Analytics kann keine Ereignisse in die SQL-Datenbank-Ausgabe schreiben.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **Ursache:** Fehler beim Initialisieren einer dedizierten SQL-Poolausgabe.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Ursache:** Fehler beim Schreiben der Ausgabe in einen dedizierten SQL-Pool.

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung für Eingangsverbindungen](stream-analytics-troubleshoot-input.md)
* [Problembehandlung von Azure Stream Analytics-Ausgaben](stream-analytics-troubleshoot-output.md)
* [Problembehandlung von Azure Stream Analytics-Abfragen](stream-analytics-troubleshoot-query.md)
* [Problembehandlung von Azure Stream Analytics mit Ressourcenprotokollen](stream-analytics-job-diagnostic-logs.md)
* [Datenfehler in Azure Stream Analytics](data-errors.md)
