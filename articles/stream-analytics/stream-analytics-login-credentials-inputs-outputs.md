---
title: Rotieren der Anmeldeinformationen in Azure Stream Analytics-Aufträgen
description: In diesem Artikel wird beschrieben, wie Sie die Anmeldeinformationen für Eingabe- und Ausgabesenken in Azure Stream Analytics-Aufträgen aktualisieren.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 47afaea03e58cf6a24382727c6fc7193fde5abe4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012100"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Austauschen von Anmeldeinformationen für Ein- und Ausgaben eines Stream Analytics-Auftrags

Jedes Mal, wenn Sie Anmeldeinformationen für eine Eingabe oder Ausgabe eines Stream Analytics-Auftrags neu generieren, sollten Sie den Auftrag mit neuen Anmeldeinformationen aktualisieren. Sie müssen den Auftrag beenden, bevor Sie die Anmeldeinformationen aktualisieren. Sie können die Anmeldeinformationen nicht ersetzen, während der Auftrag ausgeführt wird. Um die Verzögerung zwischen den Beenden und Neustarten des Auftrags zu verkürzen, unterstützt Stream Analytics Fortsetzen eines Auftrags aus dessen letzter Ausgabe. In diesem Thema ist der Prozess des Austauschens der Anmeldeinformationen und Neustartens des Auftrags mit den neuen Anmeldeinformationen beschrieben.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Generieren neuer Anmeldeinformationen und Aktualisieren Ihres Auftrags mit den neuen Anmeldeinformationen 

In diesem Abschnitt werden Sie durch die Schritte geführt, in denen Sie die Anmeldeinformationen für Blob Storage (Blob-Speicher), Event Hubs, SQL-Datenbank und Table Storage (Tabellenspeicher) neu generieren. 

### <a name="blob-storagetable-storage"></a>Blob-Speicher/Tabellenspeicher
1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu dem Speicherkonto, das Sie als Eingabe/Ausgabe für den Stream Analytics-Auftrag verwendet haben.    
2. Öffnen Sie im Abschnitt „Einstellungen“ den Punkt **Zugriffsschlüssel**. Wählen Sie aus den beiden Standardschlüsseln (key1, key2) denjenigen aus, der nicht für Ihren Auftrag verwendet wird, und generieren Sie den Schlüssel neu:  
   ![Schlüssel für Speicherkonto neu generieren](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Kopieren Sie den neu generierten Schlüssel.    
4. Navigieren Sie im Azure-Portal zu Ihrem Stream Analytics-Auftrag, wählen Sie **Beenden** aus, und warten Sie, bis der Auftrag beendet wurde.    
5. Suchen Sie nach der Blob-/Tabellespeichereingabe/-ausgabe, für die Sie die Anmeldeinformationen aktualisieren möchten.    
6. Suchen Sie nach dem Feld **Speicherkontoschlüssel**, fügen Sie Ihren neu generierten Schlüssel ein, und klicken Sie auf **Speichern**.    
7. Wenn Sie die Änderungen speichern, wird automatisch ein Verbindungstest gestartet. Diesen können Sie sich auf der Registerkarte „Benachrichtigungen“ ansehen. Es gibt zwei Benachrichtigungen, wobei eine dem Speichern der Aktualisierung und die andere dem Testen der Verbindung entspricht:  
   ![Benachrichtigungen nach einem Bearbeiten des Schlüssels](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Wechseln Sie zum Abschnitt [Starten des Auftrags ab dem Zeitpunkt der letzten Beendigung](#start-your-job-from-the-last-stopped-time).

### <a name="event-hubs"></a>Event Hubs

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu dem Event Hub, den Sie als Eingabe/Ausgabe für den Stream Analytics-Auftrag verwendet haben.    
2. Öffnen Sie im Abschnitt „Einstellungen“ die Option **SAS-Richtlinien**, und wählen Sie die erforderliche Zugriffsrichtlinie aus. Wählen Sie aus **Primärer Schlüssel** und **Sekundärer Schlüssel** denjenigen aus, der nicht für Ihren Auftrag verwendet wird, und generieren Sie ihn neu:  
   ![Neugenerieren von Schlüsseln für Event Hubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Kopieren Sie den neu generierten Schlüssel.    
4. Navigieren Sie im Azure-Portal zu Ihrem Stream Analytics-Auftrag, wählen Sie **Beenden** aus, und warten Sie, bis der Auftrag beendet wurde.    
5. Suchen Sie nach der Event Hubs-Eingabe/-Ausgabe, für die Sie die Anmeldeinformationen aktualisieren möchten.    
6. Suchen Sie nach dem Feld **Event Hub-Richtlinienschlüssel**, fügen Sie den neu generierten Schlüssel ein, und klicken Sie auf **Speichern**.    
7. Beim Speichern der Änderungen wird automatisch ein Verbindungstest gestartet, dessen erfolgreiche Ausführung Sie prüfen sollten.    
8. Wechseln Sie zum Abschnitt [Starten des Auftrags ab dem Zeitpunkt der letzten Beendigung](#start-your-job-from-the-last-stopped-time).

### <a name="sql-database"></a>SQL-Datenbank

Sie müssen eine Verbindung mit der SQL-Datenbank herstellen, um die Anmeldeinformationen eines vorhandenen Benutzers zu aktualisieren. Sie können Anmeldeinformationen aktualisieren, indem Sie das Azure-Portal oder ein clientseitiges Tool verwenden, etwa SQL Server Management Studio. In diesem Abschnitt wird veranschaulicht, wie Anmeldeinformationen über das Azure-Portal aktualisiert werden.

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu der SQL-Datenbank, die Sie als Ausgabe für den Stream Analytics-Auftrag verwendet haben.    
2. Melden Sie sich aus **Daten-Explorer** bei der Datenbank an, wählen Sie als Autorisierungstyp den Typ **SQL Server-Authentifizierung** aus, geben Sie Ihren **Anmeldenamen** und Ihr  **Kennwort** ein, und wählen Sie **OK** aus.  
   ![Neugenerieren von Anmeldeinformationen für SQL-Datenbank](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Ändern Sie auf der Registerkarte „Abfrage“ das Kennwort für einen der Benutzer, indem Sie die folgende Abfrage ausführen (ersetzen Sie `<user_name>` durch Ihren Benutzernamen und `<new_password>` durch Ihr neues Kennwort):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Notieren Sie sich das neue Kennwort.    
5. Navigieren Sie im Azure-Portal zu Ihrem Stream Analytics-Auftrag, wählen Sie **Beenden** aus, und warten Sie, bis der Auftrag beendet wurde.    
6. Suchen Sie nach der SQL-Datenbank-Ausgabe, für die Sie die Anmeldeinformationen austauschen möchten. Aktualisieren Sie das Kennwort, und speichern Sie die Änderungen.    
7. Beim Speichern der Änderungen wird automatisch ein Verbindungstest gestartet, dessen erfolgreiche Ausführung Sie prüfen sollten.    
8. Wechseln Sie zum Abschnitt [Starten des Auftrags ab dem Zeitpunkt der letzten Beendigung](#start-your-job-from-the-last-stopped-time).

### <a name="power-bi"></a>Power BI
1. Melden Sie sich beim Azure-Portal an, navigieren Sie zu Ihrem Stream Analytics-Auftrag, wählen Sie **Beenden** aus, und warten Sie, bis der Auftrag beendet wurde.    
2. Suchen Sie nach der Power BI-Ausgabe, für die Sie Anmeldeinformationen erneuern möchten, klicken Sie auf **Autorisierung erneuern** (es sollte eine Erfolgsmeldung angezeigt werden), und **Speichern** Sie die Änderungen.    
3. Beim Speichern der Änderungen wird automatisch ein Verbindungstest gestartet, dessen erfolgreiche Ausführung Sie prüfen sollten.    
4. Wechseln Sie zum Abschnitt [Starten des Auftrags ab dem Zeitpunkt der letzten Beendigung](#start-your-job-from-the-last-stopped-time).

## <a name="start-your-job-from-the-last-stopped-time"></a>Starten des Auftrags ab dem Zeitpunkt der letzten Beendigung

1. Navigieren Sie zum **Übersicht**-Bereich des Auftrags, und wählen Sie **Starten** aus, um den Auftrag zu starten.    
2. Wählen Sie **Zeitpunkt der letzten Beendigung** aus, und klicken Sie auf **Starten**. Beachten Sie, dass die Option „Zeitpunkt der letzten Beendigung“ nur angezeigt wird, wenn Sie den Auftrag zuvor ausgeführt und dabei etwas Ausgabe generiert haben. Der Auftrag wird anhand des Zeitpunkts des letzten Ausgabewerts neu gestartet.
   ![Starten des Stream Analytics-Auftrags](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)