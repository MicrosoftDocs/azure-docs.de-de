---
title: Behandeln von Latenzproblemen mithilfe von Storage Analytics-Protokollen
description: Identifizieren und beheben Sie Latenzprobleme mithilfe von Azure Storage Analytics-Protokollen, und optimieren Sie die Clientanwendung.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 5f04a20b347e2672d9699551885f5dd16ceaa99c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785594"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Behandeln von Latenzproblemen mithilfe von Storage Analytics-Protokollen

Die Diagnose und das Beheben von Problemen ist eine wichtige Fähigkeit für die Erstellung und den Support von Clientanwendungen mit Azure Storage.

Aufgrund der dezentralen Natur einer Azure-Anwendung kann sich die Diagnose und Behebung sowohl von Fehlern als auch Leistungsproblemen als komplexer erweisen als in herkömmlichen Umgebungen.

In den folgenden Schritten wird veranschaulicht, wie Sie Latenzprobleme mithilfe von Azure Storage Analytics-Protokollen identifizieren und beheben sowie die Clientanwendung optimieren.

## <a name="recommended-steps"></a>Empfohlene Schritte

1. Laden Sie die [Storage Analytics-Protokolle](./storage-analytics-logging.md#download-storage-logging-log-data) herunter.

2. Verwenden Sie das folgende PowerShell-Skript, um die Protokolle im Rohdatenformat in ein Tabellenformat zu konvertieren:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Das Skript startet ein GUI-Fenster, in dem Sie die Informationen wie unten gezeigt nach Spalten filtern können.

   ![Parserfenster für Storage Analytics-Protokolle](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Grenzen Sie die Protokolleinträge auf Grundlage des Vorgangstyps (operation.type) ein, und suchen Sie nach dem Protokolleintrag, der während des Problemzeitraums erstellt wurde.

   ![Protokolleinträge nach Vorgangstyp](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Folgende Werte sind während des Zeitraums, in dem das Problem auftrat, von Bedeutung:

   * Operation-type = GetBlob
   * request-status = SASNetworkError
   * End-to-End-Latency-In-Ms = 8453
   * Server-Latency-In-Ms = 391

   Die End-to-End-Latenz wird mithilfe der folgenden Formel berechnet:

   * End-to-End-Latenz = Server-Latency + Client Latency

   Berechnen Sie die Clientlatenz anhand des Protokolleintrags:

   * Clientlatenz = End-to-End Latency – Server-Latency

        Beispiel: 8453 – 391 = 8062 ms

   In der folgenden Tabelle finden Sie Informationen zu „OperationType“ mit hoher Latenz und den Ergebnissen für „RequestStatus“:

   | Blobtyp |RequestStatus=<br>Erfolg|RequestStatus=<br>(SAS)NetworkError|Empfehlung|
   |---|---|---|---|
   |GetBlob|Ja|Nein|[**GetBlob-Vorgang:** RequestStatus = Success](#getblob-operation-requeststatus--success)|
   |GetBlob|Nein|Ja|[**GetBlob-Vorgang:** RequestStatus = (SAS)NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Ja|Nein|[**Put-Vorgang:** RequestStatus = Success](#put-operation-requeststatus--success)|
   |PutBlob|Nein|Ja|[**Put-Vorgang:** RequestStatus = (SAS)NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Statusergebnisse

### <a name="getblob-operation-requeststatus--success"></a>GetBlob-Vorgang: RequestStatus = Success

Überprüfen Sie die folgenden Werte, wie es in Schritt 5 des Abschnitts „Empfohlene Schritte“ angegeben ist:

* End-to-End Latency
* Server-Latency
* Client-Latency

Wenn bei einem **GetBlob-Vorgang** mit **RequestStatus = Success** der Zeitraum **Max Time** für **Client-Latency** aufgewendet wird, bedeutet dies, dass Azure Storage viel Zeit für das Schreiben von Daten an den Client braucht. Diese Verzögerung deutet auf ein clientseitiges Problem hin.

**Empfehlung:**

* Untersuchen Sie den Code in Ihrem Client.
* Verwenden Sie Wireshark, Microsoft Message Analyzer oder TCPing, um Netzwerkverbindungsprobleme des Client zu untersuchen. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob-Vorgang: RequestStatus = (SAS)NetworkError

Überprüfen Sie die folgenden Werte, wie es in Schritt 5 des Abschnitts „Empfohlene Schritte“ angegeben ist:

* End-to-End Latency
* Server-Latency
* Client-Latency

Wenn bei einem **GetBlob-Vorgang** mit **RequestStatus = (SAS)NetworkError** der Zeitraum **Max Time** für **Client-Latency** aufgewendet wird, ist das Problem zumeist, dass der Client die Verbindung abbricht, bevor ein Zeitlimit im Speicherdienst abläuft.

**Empfehlung:**

* Untersuchen Sie den Code in Ihrem Client, um herauszufinden, warum und wann der Client die Verbindung zum Speicherdienst abbricht.
* Verwenden Sie Wireshark, Microsoft Message Analyzer oder TCPing, um Netzwerkverbindungsprobleme des Client zu untersuchen. 

### <a name="put-operation-requeststatus--success"></a>Put-Vorgang: RequestStatus = Success

Überprüfen Sie die folgenden Werte, wie es in Schritt 5 des Abschnitts „Empfohlene Schritte“ angegeben ist:

* End-to-End Latency
* Server-Latency
* Client-Latency

Wenn bei einem **Put-Vorgang** mit **RequestStatus = Success** der Zeitraum **Max Time** für **Client-Latency** aufgewendet wird, bedeutet dies, dass der Client mehr Zeit zum Senden von Daten an Azure Storage braucht. Diese Verzögerung deutet auf ein clientseitiges Problem hin.

**Empfehlung:**

* Untersuchen Sie den Code in Ihrem Client.
* Verwenden Sie Wireshark, Microsoft Message Analyzer oder TCPing, um Netzwerkverbindungsprobleme des Client zu untersuchen. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put-Vorgang: RequestStatus = (SAS)NetworkError

Überprüfen Sie die folgenden Werte, wie es in Schritt 5 des Abschnitts „Empfohlene Schritte“ angegeben ist:

* End-to-End Latency
* Server-Latency
* Client-Latency

Wenn bei einem **PutBlob-Vorgang** mit **RequestStatus = (SAS)NetworkError** der Zeitraum **Max Time** für **Client-Latency** aufgewendet wird, ist das Problem zumeist, dass der Client die Verbindung abbricht, bevor ein Zeitlimit im Speicherdienst abläuft.

**Empfehlung:**

* Untersuchen Sie den Code in Ihrem Client, um herauszufinden, warum und wann der Client die Verbindung zum Speicherdienst abbricht.
* Verwenden Sie Wireshark, Microsoft Message Analyzer oder TCPing, um Netzwerkverbindungsprobleme des Client zu untersuchen.