---
title: Behandeln von Problemen bei Verwendung des Azure Cosmos DB-Emulators
description: Erfahren Sie, wie Sie Probleme bei Dienstverfügbarkeit, Zertifikaten, Verschlüsselung und Versionsverwaltung bei Verwendung des Azure Cosmos DB-Emulators beheben.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: bb07adafbc68ff1e9b7d3ee49bb7631dc4395d77
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033458"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>Behandeln von Problemen bei Verwendung des Azure Cosmos DB-Emulators
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Der Azure Cosmos DB-Emulator stellt eine lokale Umgebung bereit, die zu Entwicklungszwecken den Azure Cosmos DB-Dienst emuliert. Verwenden Sie die Tipps in diesem Artikel, um Probleme zu beheben, die bei der Installation oder Verwendung des Azure Cosmos DB-Emulators auftreten können. 

Wenn Sie eine neue Version des Emulators installiert haben und Fehler auftreten, setzen Sie Ihre Daten zurück. Klicken Sie hierzu mit der rechten Maustaste auf der Taskleiste auf das Symbol „Azure Cosmos DB-Emulator“ und anschließend auf „Daten zurücksetzen“. Wenn dies die Fehler nicht behebt, können Sie den Emulator und gegebenenfalls ältere Versionen des Emulators deinstallieren, das Verzeichnis *C:\Program files\Azure Cosmos DB Emulator* entfernen und den Emulator neu installieren. Anweisungen finden Sie unter [Deinstallieren des lokalen Emulators](local-emulator.md#uninstall). Wenn das Zurücksetzen der Daten nicht funktioniert, navigieren Sie zum Speicherort `%LOCALAPPDATA%\CosmosDBEmulator`, und löschen Sie den Ordner.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Korrigieren von beschädigten Windows-Leistungsindikatoren

* Bei einem Absturz des Azure Cosmos DB-Emulators erfassen Sie die Speicherabbilddateien aus dem Ordner `%LOCALAPPDATA%\CrashDumps`, komprimieren sie und öffnen ein Supportticket über das [Azure-Portal](https://portal.azure.com).

* Wenn es in `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` zu Abstürzen kommt, könnte dies ein Anzeichen dafür sein, dass sich die Leistungsindikatoren beschädigt sind. In der Regel wird dieses Problem durch die Ausführung des folgenden Befehls über die Eingabeaufforderung des Administrators gelöst:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Behandeln von Konnektivitätsproblemen

* Wenn ein Verbindungsproblem auftritt, [sammeln Sie Ablaufverfolgungsdateien](#trace-files), komprimieren sie und öffnen ein Supportticket im [Azure-Portal](https://portal.azure.com).

* Wenn die Meldung **Dienst nicht verfügbar** angezeigt wird, bedeutet das, dass der Emulator den Netzwerkstapel möglicherweise nicht initialisieren kann. Überprüfen Sie, ob Sie den sicheren Pulse-Client oder den Juniper Networks-Client installiert haben. Die zugehörigen Treiber für Netzwerkfilter könnten das Problem verursachen. Durch Deinstallieren der Netzwerkfiltertreiber von Drittanbietern wird das Problem in der Regel behoben. Alternativ können Sie den Emulator auch mit „/DisableRIO“ starten, wodurch die Netzwerkkommunikation des Emulators auf reguläres Winsock umgeschaltet wird. 

* Wenn **„Verboten“, „Nachricht“: „Anforderung erfolgt mit verbotener Verschlüsselung in Transitprotokoll oder Chiffre. Minimal zulässige Protokolleinstellung für Konto-SSL/TLS überprüfen...“** -Konnektivitätsprobleme angezeigt werden, könnte dies durch globale Änderungen im Betriebssystem (z. B. Insider Preview Build 20170) oder die Browsereinstellungen, die TLS 1.3 als Standard aktivieren, verursacht worden sein. Ein ähnlicher Fehler kann auftreten, wenn Sie das SDK verwenden, um eine Anforderung für den Cosmos-Emulator auszuführen, z. B. **Microsoft. Azure. Documents.DocumentClientException: Anforderung erfolgt mit verbotener Verschlüsselung in Transitprotokoll oder Chiffre. Minimal zulässige Protokolleinstellung für Konto-SSL/TLS überprüfen**. Dies wird zu diesem Zeitpunkt erwartet, da der Cosmos-Emulator nur das TLS 1.2-Protokoll akzeptiert und verwendet. Die empfohlene Problemumgehung ist die Änderung der Einstellungen und des Standards in TLS 1.2. Navigieren Sie beispielsweise im IIS-Manager zu „Sites“ > „Standardwebsites“, suchen Sie nach „Sitebindungen“ für Port 8081, und bearbeiten Sie sie, um TLS 1.3 zu deaktivieren. Ein ähnlicher Vorgang kann für den Webbrowser über die Optionen „Einstellungen“ ausgeführt werden.

* Wenn Ihr Computer während der Ausführung des Emulators in den Ruhezustand versetzt wird oder Betriebssystemupdates ausführt, wird möglicherweise die Meldung **Der Dienst ist zurzeit nicht verfügbar** angezeigt. Setzen Sie die Daten des Emulators zurück, indem Sie mit der rechten Maustaste auf das Symbol klicken, das im Windows-Infobereich angezeigt wird und **Daten zurücksetzen** auswählen.

## <a name="collect-trace-files"></a><a id="trace-files"></a>Sammeln von Ablaufverfolgungsdateien

Zum Sammeln von Debugablaufverfolgungen führen Sie die folgenden Befehle an einer Administratoreingabeaufforderung aus:

1. Navigieren Sie zu dem Pfad, in dem der Emulator installiert ist:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Fahren Sie den Emulator herunter, und beobachten Sie die Taskleiste, um sicherzugehen, dass das Programm beendet wurde. Dies kann eine Minute in Anspruch nehmen. Sie können auch auf der Benutzeroberfläche des Azure Cosmos DB-Emulators **Beenden** auswählen.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Führen Sie den folgenden Befehl aus, um die Protokollierung zu starten:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Starten des Emulators

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Reproduzieren des Problems Wenn der Daten-Explorer nicht funktioniert, müssen Sie nur einige Sekunden warten, bis der Browser geöffnet wird, um den Fehler zu erfassen.

1. Beenden Sie die Protokollierung mit dem folgenden Befehl:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Navigieren Sie zum Pfad `%ProgramFiles%\Azure Cosmos DB Emulator`, und suchen Sie die Datei *docdbemulator_000001.etl*.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) ein Supportticket, und fügen Sie die ETL-Datei zusammen mit den Reproduktionsschritten ein.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Probleme mit dem lokalen Emulator debuggen. Sie können jetzt mit dem nächsten Artikel fortfahren:

* [Exportieren der Azure Cosmos DB-Emulatorzertifikate für die Verwendung mit Java-, Python- und Node.js-Apps](local-emulator-export-ssl-certificates.md)
* [Verwenden von Befehlszeilenparametern und PowerShell-Befehlen zum Steuern des Emulators](emulator-command-line-parameters.md)
