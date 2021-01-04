---
title: Überwachen der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Bereitstellung der Azure-Dateisynchronisierung mit Azure Monitor, dem Speichersynchronisierungsdienst und Windows Server überwachen.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 59c489fac8bf02263cc51833675af414d5de6a52
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686003"
---
# <a name="monitor-azure-file-sync"></a>Überwachen der Azure-Dateisynchronisierung

Mit der Azure-Dateisynchronisierung können Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Mit der Azure-Dateisynchronisierung werden Ihre Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Sie können weltweit so viele Caches wie nötig nutzen.

In diesem Artikel wird beschrieben, wie Sie Ihre Bereitstellung der Azure-Dateisynchronisierung mit Azure Monitor, dem Speichersynchronisierungsdienst und Windows Server überwachen.

Folgende Szenarios werden in diesem Leitfaden abgedeckt: 
- Anzeigen von Metriken der Azure-Dateisynchronisierung in Azure Monitor.
- Erstellen von Warnungen in Azure Monitor, die Sie proaktiv über kritische Zustände unterrichten.
- Anzeigen der Integrität Ihrer Bereitstellung der Azure-Dateisynchronisierung mithilfe des Azure-Portals.
- Verwenden der Ereignisprotokolle und Leistungsindikatoren auf Ihren Windows-Servern zum Überwachen der Integrität Ihrer Bereitstellung der Azure-Dateisynchronisierung. 

## <a name="azure-monitor"></a>Azure Monitor

Verwenden Sie [Azure Monitor](../../azure-monitor/overview.md), um Metriken anzuzeigen und Warnungen für Synchronisierung, Cloudtiering und Serverkonnektivität zu konfigurieren.  

### <a name="metrics"></a>Metriken

Metriken für die Azure-Dateisynchronisierung werden standardmäßig aktiviert und alle 15 Minuten an Azure Monitor gesendet.

**Anzeigen von Metriken der Azure-Dateisynchronisierung in Azure Monitor**
1. Wechseln Sie im **Azure-Portal** zu Ihrem **Speichersynchronisierungsdienst**, und klicken Sie auf **Metriken**.
2. Klicken Sie auf die Dropdownliste **Metrik**, und wählen Sie die Metrik aus, die Sie anzeigen möchten.

![Screenshot: Metriken für die Azure-Dateisynchronisierung](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

In Azure Monitor sind die folgenden Metriken für die Azure-Dateisynchronisierung verfügbar:

| Metrikname | BESCHREIBUNG |
|-|-|
| Bytes synchronisiert | Größe der übertragenen Daten (Upload und Download).<br><br>Einheit: Byte<br>Aggregationstyp: SUM<br>Verfügbare Dimensionen: Name des Serverendpunkts, Synchronisierungsrichtung, Name der Synchronisierungsgruppe |
| Cloudtieringrückruf | Größe der zurückgerufenen Daten.<br><br>**Hinweis**: Diese Metrik wird in Zukunft entfernt. Verwenden Sie die Metrik „ Cloudtiering-Rückrufgröße“, um die Größe der zurückgerufenen Daten zu überwachen.<br><br>Einheit: Byte<br>Aggregationstyp: SUM<br>Verfügbare Dimension: Servername |
| Cloudtiering-Rückrufgröße | Größe der zurückgerufenen Daten.<br><br>Einheit: Byte<br>Aggregationstyp: SUM<br>Verfügbare Dimension: Servername, Name der Synchronisierungsgruppe |
| Cloudtiering-Rückrufgröße nach Anwendung | Größe der zurückgerufenen Daten nach Anwendung.<br><br>Einheit: Byte<br>Aggregationstyp: SUM<br>Verfügbare Dimension: Anwendungsname, Servername, Name der Synchronisierungsgruppe |
| Cloudtiering-Rückrufdurchsatz | Größe des Datenrückruf-Durchsatzes.<br><br>Einheit: Byte<br>Aggregationstyp: SUM<br>Verfügbare Dimension: Servername, Name der Synchronisierungsgruppe |
| Dateien ohne Synchronisierung | Anzahl von Dateien, für die keine Synchronisierung möglich ist.<br><br>Einheit: Anzahl<br>Aggregationstyp: SUM<br>Verfügbare Dimensionen: Name des Serverendpunkts, Synchronisierungsrichtung, Name der Synchronisierungsgruppe |
| Dateien synchronisiert | Anzahl der übertragenen Dateien (Upload und Download)<br><br>Einheit: Anzahl<br>Aggregationstyp: SUM<br>Verfügbare Dimensionen: Name des Serverendpunkts, Synchronisierungsrichtung, Name der Synchronisierungsgruppe |
| Onlinestatus des Servers | Anzahl von Taktsignalen, die vom Server empfangen wurden.<br><br>Einheit: Anzahl<br>Aggregationstyp: Maximum<br>Verfügbare Dimension: Servername |
| Ergebnis der Synchronisierungssitzung | Ergebnis der Synchronisierungssitzung (1 = erfolgreiche Synchronisierungssitzung, 0 = fehlerhafte Synchronisierungssitzung)<br><br>Einheit: Anzahl<br>Aggregationstypen: Maximum<br>Verfügbare Dimensionen: Name des Serverendpunkts, Synchronisierungsrichtung, Name der Synchronisierungsgruppe |

### <a name="alerts"></a>Alerts

Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Weitere Informationen zum Konfigurieren von Warnungen in Azure Monitor finden Sie unter [Übersicht über Warnungen in Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

**Erstellen von Warnungen für die Azure-Dateisynchronisierung**

1. Wechseln Sie im **Azure-Portal** zu Ihrem **Speichersynchronisierungsdienst**. 
2. Klicken Sie im Abschnitt „Überwachung“ auf **Warnungen** und dann auf **+ Neue Warnungsregel**.
3. Klicken Sie auf **Bedingung auswählen**, und geben Sie die folgenden Informationen für die Warnung an: 
    - **Metrik**
    - **Dimensionsname**
    - **Warnungslogik**
4. Klicken Sie auf **Aktionsgruppe auswählen**, und fügen Sie der Warnung eine Aktionsgruppe (E-Mail, SMS usw.) hinzu, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.
5. Geben Sie die **Warnungsdetails** wie **Warnungsregelname**, **Beschreibung** und **Schweregrad** ein.
6. Klicken Sie auf **Warnungsregel erstellen**, um die Warnung zu erstellen.  

In der folgenden Tabelle sind einige Beispielszenarios für die Überwachung und die jeweils geeignete Metrik für die Warnung aufgeführt:

| Szenario | Für Warnung zu verwendende Metrik |
|-|-|
| Serverendpunkt-Integrität zeigt einen Fehler im Portal an | Ergebnis der Synchronisierungssitzung |
| Fehler beim Synchronisieren von Dateien mit einem Server- oder Cloudendpunkt | Dateien ohne Synchronisierung |
| Registrierter Server kann nicht mit dem Speichersynchronisierungsdienst kommunizieren | Onlinestatus des Servers |
| Cloudtiering-Rückrufgröße hat 500 GiB pro Tag überschritten  | Cloudtiering-Rückrufgröße |

Anweisungen zum Erstellen von Warnungen für diese Szenarios finden Sie im Abschnitt [Warnungsbeispiele](#alert-examples).

## <a name="storage-sync-service"></a>Speichersynchronisierungsdienst

Navigieren Sie zum Anzeigen der Integrität Ihrer Bereitstellung der Azure-Dateisynchronisierung im **Azure-Portal** zum **Speichersynchronisierungsdienst**. Dort sind folgende Informationen verfügbar:

- Integrität registrierter Server
- Integrität der Serverendpunkte
    - Dateien ohne Synchronisierung
    - Synchronisierungsaktivität
    - Cloudtieringeffizienz
    - Dateien ohne Tiering
    - Rückruffehler
- Metriken

### <a name="registered-server-health"></a>Integrität registrierter Server

Um die **Integrität registrierter Server** im Portal anzuzeigen, navigieren Sie zum Abschnitt **Registrierte Server** des **Speichersynchronisierungsdiensts**.

![Screenshot: Integrität registrierter Server](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Wenn sich der **registrierte Server** im Zustand **Online** befindet, kommuniziert er erfolgreich mit dem Dienst.
- Wenn für **Registrierte Server** der Status **Als offline angezeigt** angezeigt wird, wird der Überwachungsprozess für die Speichersynchronisierung (AzureStorageSyncMonitor.exe) nicht ausgeführt, oder der Server kann nicht auf den Azure-Dateisynchronisierungsdienst zugreifen. Weitere Informationen finden Sie in der [Dokumentation zur Problembehandlung](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity).

### <a name="server-endpoint-health"></a>Integrität der Serverendpunkte

Navigieren Sie zum Anzeigen der Integrität eines **Serverendpunkts** im Portal zum Abschnitt **Synchronisierungsgruppen** des **Speichersynchronisierungsdiensts**, und wählen Sie eine **Synchronisierungsgruppe** aus.

![Screenshot: Integrität der Serverendpunkte](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- Die **Serverendpunktintegrität** und **Synchronisierungsaktivität** im Portal basiert auf den Synchronisierungsereignissen, die im Protokoll für Telemetrieereignisse auf dem Server protokolliert werden (ID 9102 und 9302). Wenn eine Synchronisierungssitzung aufgrund eines vorübergehenden Fehlers (z. B. Abbruchfehler) nicht erfolgreich ist, wird der Serverendpunkt im Portal weiterhin als **fehlerfrei** angezeigt, solange ein Fortschritt bei der Synchronisierungssitzung verzeichnet wird (Dateien werden angewendet). Ereignis-ID 9302 ist das Synchronisierungsfortschrittereignis, und die Ereignis-ID 9102 wird protokolliert, sobald eine Synchronisierungssitzung abgeschlossen ist.  Weitere Informationen finden Sie unter [Wie überwache ich die Integrität der Synchronisierung?](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) und [Wie überwache ich den Fortschritt einer aktuellen Synchronisierungssitzung?](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Wenn für die Integrität der Serverendpunkte ein **Fehler** oder **Keine Aktivität** angezeigt wird, finden Sie in der [Dokumentation zur Problembehandlung](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors) weitere Informationen.
- Die Anzahl **Dateien ohne Synchronisierung** im Portal basiert auf der Ereignis-ID 9121, die im Telemetrieereignisprotokoll auf dem Server protokolliert wird. Dieses Ereignis wird für jeden Fehler pro Element protokolliert, sobald die Synchronisierungssitzung abgeschlossen ist. Wie Sie Fehler auf Elementebene aufzulösen, erfahren Sie unter [Woran erkenne ich, dass bestimmte Dateien oder Ordner nicht synchronisiert wurden?](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)
- Um die **Cloudtieringeffizienz** im Portal anzuzeigen, navigieren Sie zu den **Eigenschaften des Serverendpunkts** und dann zum Abschnitt **Cloudtiering**. Die für die Cloudtieringeffizienz bereitgestellten Daten basieren auf der Ereignis-ID 9071, die im Telemetrieereignisprotokoll auf dem Server protokolliert wird. Weitere Informationen finden Sie im Artikel [Übersicht über Cloudtiering](./storage-sync-cloud-tiering.md).
- Um die **Dateien ohne Tiering** und **Rückruffehler** im Portal anzuzeigen, navigieren Sie zu den **Eigenschaften des Serverendpunkts** und dann zum Abschnitt **Cloudtiering**. **Dateien ohne Tiering** basiert auf der Ereignis-ID 9003, die im Telemetrieereignisprotokoll auf dem Server protokolliert ist, und **Rückruffehler** auf der Ereignis-ID 9006. Wie Sie Dateien untersuchen, bei denen kein Tiering möglich ist oder Rückruffehler auftreten, erfahren Sie unter [So beheben Sie Probleme bei Dateien, bei denen kein Tiering möglich ist](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) und [Beheben von Rückruffehlern bei Dateien](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled).

### <a name="metric-charts"></a>Metrikdiagramme

- Die folgenden Metrikdiagramme können im Speichersynchronisierungsdienst-Portal angezeigt werden:

  | Metrikname | BESCHREIBUNG | Blattname |
  |-|-|-|
  | Bytes synchronisiert | Größe der übertragenen Daten (Upload und Download) | Synchronisierungsgruppe, Serverendpunkt |
  | Cloudtieringrückruf | Größe der zurückgerufenen Daten | Registrierte Server |
  | Dateien ohne Synchronisierung | Anzahl der Dateien, die nicht synchronisiert werden können | Serverendpunkt |
  | Dateien synchronisiert | Anzahl der übertragenen Dateien (Upload und Download) | Synchronisierungsgruppe, Serverendpunkt |
  | Onlinestatus des Servers | Anzahl der Taktsignale, die vom Server empfangen wurden | Registrierte Server |

- Weitere Informationen finden Sie im Abschnitt [Azure Monitor](#azure-monitor).

  > [!Note]  
  > Die Diagramme im Speichersynchronisierungsdienst-Portal haben einen Zeitbereich von 24 Stunden. Die verschiedenen Zeitbereiche oder -dimensionen können Sie mit Azure Monitor anzeigen.

## <a name="windows-server"></a>Windows Server

Auf dem **Windows-Server**, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, können Sie die Integrität der Serverendpunkte auf diesem Server mithilfe der **Ereignisprotokolle** und **Leistungsindikatoren** anzeigen.

### <a name="event-logs"></a>Ereignisprotokolle

Verwenden Sie das Telemetrieereignisprotokoll auf dem Server, um die Integrität für registrierte Server, Synchronisierung und Cloudtiering zu überwachen. Das Telemetrieereignisprotokoll befindet sich in der Ereignisanzeige unter *Anwendungen und Dienste\Microsoft\FileSync\Agent*.

Synchronisierungsintegrität

- Nach Abschluss einer Synchronisierungssitzung wird ein Ereignis mit der ID 9102 protokolliert. Anhand dieses Ereignisses können Sie bestimmen, ob Synchronisierungssitzungen erfolgreich abgeschlossen wurden (**HResult = 0**) und ob Synchronisierungsfehler auf Elementebene (**PerItemErrorCount**) vorliegen. Weitere Informationen finden Sie in der Dokumentation zur [Synchronisierungsintegrität](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) und zu [Fehlern auf Elementebene](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Manchmal tritt bei Synchronisierungssitzungen allgemein ein Fehler auf oder PerItemErrorCount ist ungleich 0. Jedoch wird trotzdem ein Fortschritt angezeigt, und einige Dateien synchronisieren erfolgreich. Dies kann bei den Feldern beobachtet werden, die mit „Applied“ beginnen (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount und AppliedSizeBytes). Die Werte dieser Felder geben an, bis zu welchem Anteil die Sitzung erfolgreich durchgeführt wurde. Wenn für mehrere aufeinanderfolgende Synchronisierungssitzungen Fehler angezeigt werden, die Sitzungen jedoch steigende „Applied“-Werte aufweisen, sollten Sie der Synchronisierung Zeit für einen erneuten Versuch geben, bevor Sie ein Supportticket öffnen.

- Ereignis-ID 9121 wird für jeden Fehler pro Element protokolliert, sobald die Synchronisierungssitzung abgeschlossen ist. Bestimmen Sie anhand dieses Ereignisses die Anzahl der Dateien, bei denen die Synchronisierung mit diesem Fehler (**PersistentCount** und **TransientCount**) nicht erfolgreich ist. Wie Sie persistente Fehler auf Elementebene untersuchen sollten, erfahren Sie unter [Woran erkenne ich, dass bestimmte Dateien oder Ordner nicht synchronisiert wurden?](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- Die Ereignis-ID 9302 wird alle 5 bis 10 Minuten protokolliert, wenn eine aktive Synchronisierungssitzung vorliegt. Verwenden Sie dieses Ereignis, um die Anzahl der Elemente zu ermitteln, die synchronisiert werden sollen (**TotalItemCount**), sowie die Anzahl der bisher synchronisierten Elemente (**AppliedItemCount**) und die Anzahl der Elemente, die aufgrund eines elementspezifischen Fehlers nicht synchronisiert werden konnten (**PerItemErrorCount**). Wenn die Synchronisierung keinen Fortschritt zeigt (**AppliedItemCount=0**), tritt für die Synchronisierungssitzung irgendwann ein Fehler auf, und die Ereignis-ID 9102 wird mit dem Fehler protokolliert. Weitere Informationen finden Sie in der [Dokumentation zum Synchronisierungsfortschritt](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integrität registrierter Server

- Die Ereignis-ID 9301 wird alle 30 Sekunden protokolliert, wenn ein Server Aufträge im Dienst abfragt. Wenn GetNextJob mit dem **Status = 0** abgeschlossen wird, ist eine Kommunikation zwischen dem Server und dem Dienst möglich. Wenn GetNextJob mit einem Fehler abgeschlossen wird, lesen Sie die [Dokumentation zur Problembehandlung](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity), um weitere Informationen zu erhalten.

Cloudtieringintegrität

- Verwenden Sie die Ereignis-IDs 9003, 9016 und 9029 im Telemetrieereignisprotokoll (in der Ereignisanzeige unter *Anwendungen und Dienste\Microsoft\FileSync\Agent*), um die Tieringaktivität auf einem Server zu überwachen.

  - Die Ereignis-ID 9003 ermöglicht die Fehlerverteilung für einen Serverendpunkt. Beispiel: Gesamtfehlerzahl und ErrorCode. Pro Fehlercode wird ein Ereignis protokolliert.
  - Die Ereignis-ID 9016 stellt Ghostingergebnisse für ein Volume bereit. Beispiel: Freier Speicherplatz in Prozent, Anzahl der Dateien in der Sitzung, für die ein Ghosting durchgeführt wurde, und Anzahl von Dateien, bei denen beim Ghosting ein Fehler aufgetreten ist.
  - Die Ereignis-ID 9029 bietet Informationen zu Ghostingsitzungen für einen Serverendpunkt. Beispiel: Anzahl der in der Sitzung herangezogenen Dateien, Anzahl der Dateien in der Sitzung, für die in der Sitzung ein Tiering durchgeführt wurde, und Anzahl der Dateien, für die bereits ein Tiering durchgeführt wurde.
  
- Verwenden Sie die Ereignis-IDs 9005, 9006, 9009, 9059 und 9071 im Telemetrieereignisprotokoll (in der Ereignisanzeige unter *Anwendungen und Dienste\Microsoft\FileSync\Agent*), um die Rückrufaktivität auf einem Server zu überwachen.

  - Die Ereignis-ID 9005 bietet Zuverlässigkeit beim Rückruf für einen Serverendpunkt. Beispiel: Gesamtanzahl eindeutiger Dateien, auf die zugegriffen wird, und Gesamtanzahl eindeutiger Dateien, bei denen beim Zugriff ein Fehler aufgetreten ist.
  - Die Ereignis-ID 9006 ermöglicht die Rückruffehlerverteilung für einen Serverendpunkt. Beispiel: Gesamtanzahl der Anforderungen, bei denen ein Fehler aufgetreten ist, und ErrorCode. Pro Fehlercode wird ein Ereignis protokolliert.
  - Die Ereignis-ID 9009 bietet Informationen zu Rückrufsitzungen für einen Serverendpunkt. Beispiel: DurationSeconds, CountFilesRecallSucceeded und CountFilesRecallFailed.
  - Die Ereignis-ID 9059 bietet Informationen zur Anwendungsrückrufverteilung für einen Serverendpunkt. Beispiel: ShareId, Anwendungsname und TotalEgressNetworkBytes.
  - Über die Ereignis-ID 9071 wird die Cloudtieringeffizienz für einen Serverendpunkt angegeben. Beispiel: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes und TotalCacheMissBytes.

### <a name="performance-counters"></a>Leistungsindikatoren

Verwenden Sie die Leistungsindikatoren der Azure-Dateisynchronisierung auf dem Server, um die Synchronisierungsaktivität zu überwachen.

Öffnen Sie den Systemmonitor (Perfmon.exe), um Leistungsindikatoren der Azure-Dateisynchronisierung auf dem Server anzuzeigen. Die Indikatoren befinden sich unter den Objekten **AFS Bytes Transferred** (Übertragene AFS-Bytes) und **AFS Sync Operations** (AFS-Synchronisierungsvorgänge).

Im Systemmonitor stehen die folgenden Leistungsindikatoren für die Azure-Dateisynchronisierung zur Verfügung:

| Name des Leistungsobjekts\Leistungsindikators | BESCHREIBUNG |
|-|-|
| Übertragene AFS-Bytes\Heruntergeladene Bytes/s | Die Anzahl von pro Sekunde heruntergeladenen Bytes. |
| Übertragene AFS-Bytes\Hochgeladene Bytes/s | Die Anzahl von pro Sekunde hochgeladenen Bytes. |
| Übertragene AFS-Bytes\Bytes gesamt/s | Die Gesamtanzahl von Bytes pro Sekunde (Upload und Download). |
| AFS-Synchronisierungsvorgänge\Heruntergeladene Synchronisierungsdateien/s | Die Anzahl von pro Sekunde heruntergeladenen Synchronisierungsdateien. |
| AFS-Synchronisierungsvorgänge\Hochgeladene Synchronisierungsdateien/s | Die Anzahl von pro Sekunde hochgeladenen Synchronisierungsdateien. |
| AFS-Synchronisierungsvorgänge\Synchronisierungsdateivorgänge gesamt/s | Die Gesamtanzahl von synchronisierten Dateien (Upload und Download). |

## <a name="alert-examples"></a>Beispiele für Warnungen
In diesem Abschnitt finden Sie einige Beispiele für Warnungen für die Azure-Dateisynchronisierung.

  > [!Note]  
  > Wenn Sie eine Warnung erstellen und zu viele Warnungen ausgelöst werden, passen Sie den Schwellenwert und die Warnungslogik an.
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Erstellen einer Warnung, wenn die Serverendpunkt-Integrität einen Fehler im Portal anzeigt

1. Navigieren Sie im **Azure-Portal** zum entsprechenden **Speichersynchronisierungsdienst**. 
2. Wechseln Sie zum Abschnitt **Überwachung**, und klicken Sie auf **Warnungen**. 
3. Klicken Sie auf **+ Neue Warnungsregel**, um eine neue Warnungsregel zu erstellen. 
4. Konfigurieren Sie eine Bedingung, indem Sie auf **Bedingung auswählen** klicken.
5. Klicken Sie auf dem Blatt **Konfigurieren der Signallogik** unter dem Signalnamen auf **Ergebnis der Synchronisierungssitzung**.  
6. Wählen Sie die folgende Dimensionskonfiguration aus: 
    - Dimensionsname: **Name des Serverendpunkts**  
    - Operator: **=** 
    - Dimensionswerte: **Alle aktuellen und zukünftigen Werte**  
7. Navigieren Sie zu **Warnungslogik**, und führen Sie die folgenden Schritte aus: 
    - Auf **Statisch** festgelegter Schwellenwert 
    - Operator: **Kleiner als** 
    - Aggregationstyp: **Maximum**  
    - Schwellenwert: **1** 
    - Auswertung basierend auf: Aggregationsgranularität = **24 Stunden** | Häufigkeit der Auswertung = **Jede Stunde** 
    - Klicken Sie auf **Fertig**. 
8. Klicken Sie auf **Aktionsgruppe auswählen**, um der Warnung eine Aktionsgruppe (E-Mail, SMS usw.) hinzuzufügen, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.
9. Geben Sie die **Warnungsdetails** wie **Warnungsregelname**, **Beschreibung** und **Schweregrad** ein.
10. Klicken Sie auf **Warnungsregel erstellen**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Erstellen einer Warnung, wenn beim Synchronisieren von Dateien mit einem Server- oder Cloudendpunkt Fehler auftreten

1. Navigieren Sie im **Azure-Portal** zum entsprechenden **Speichersynchronisierungsdienst**. 
2. Wechseln Sie zum Abschnitt **Überwachung**, und klicken Sie auf **Warnungen**. 
3. Klicken Sie auf **+ Neue Warnungsregel**, um eine neue Warnungsregel zu erstellen. 
4. Konfigurieren Sie eine Bedingung, indem Sie auf **Bedingung auswählen** klicken.
5. Klicken Sie auf dem Blatt **Konfigurieren der Signallogik** unter dem Signalnamen auf **Dateien ohne Synchronisierung**.  
6. Wählen Sie die folgende Dimensionskonfiguration aus: 
     - Dimensionsname: **Name des Serverendpunkts**  
     - Operator: **=** 
     - Dimensionswerte: **Alle aktuellen und zukünftigen Werte**  
7. Navigieren Sie zu **Warnungslogik**, und führen Sie die folgenden Schritte aus: 
     - Auf **Statisch** festgelegter Schwellenwert 
     - Operator: **Größer als** 
     - Aggregationstyp: **Mittelwert**  
     - Schwellenwert: **100** 
     - Auswertung basierend auf: Aggregationsgranularität = **5 Minuten** | Häufigkeit der Auswertung = **alle 5 Minuten** 
     - Klicken Sie auf **Fertig**. 
8. Klicken Sie auf **Aktionsgruppe auswählen**, um der Warnung eine Aktionsgruppe (E-Mail, SMS usw.) hinzuzufügen, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.
9. Geben Sie die **Warnungsdetails** wie **Warnungsregelname**, **Beschreibung** und **Schweregrad** ein.
10. Klicken Sie auf **Warnungsregel erstellen**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Erstellen einer Warnung, wenn ein registrierter Server nicht mit dem Speichersynchronisierungsdienst kommunizieren kann

1. Navigieren Sie im **Azure-Portal** zum entsprechenden **Speichersynchronisierungsdienst**. 
2. Wechseln Sie zum Abschnitt **Überwachung**, und klicken Sie auf **Warnungen**. 
3. Klicken Sie auf **+ Neue Warnungsregel**, um eine neue Warnungsregel zu erstellen. 
4. Konfigurieren Sie eine Bedingung, indem Sie auf **Bedingung auswählen** klicken.
5. Klicken Sie auf dem Blatt **Konfigurieren der Signallogik** unter dem Signalnamen auf **Onlinestatus des Servers**.  
6. Wählen Sie die folgende Dimensionskonfiguration aus: 
     - Dimensionsname: **Servername**  
     - Operator: **=** 
     - Dimensionswerte: **Alle aktuellen und zukünftigen Werte**  
7. Navigieren Sie zu **Warnungslogik**, und führen Sie die folgenden Schritte aus: 
     - Auf **Statisch** festgelegter Schwellenwert 
     - Operator: **Kleiner als** 
     - Aggregationstyp: **Maximum**  
     - Schwellenwert (in Bytes): **1** 
     - Auswertung basierend auf: Aggregationsgranularität = **1 Stunde** | Häufigkeit der Auswertung = **Alle 30 Minuten** 
        - Beachten Sie, dass die Metriken alle 15 bis 20 Minuten an Azure Monitor gesendet werden. Legen Sie die **Häufigkeit der Auswertung** nicht auf weniger als 30 Minuten fest (generiert falsche Warnungen).
     - Klicken Sie auf **Fertig**. 
8. Klicken Sie auf **Aktionsgruppe auswählen**, um der Warnung eine Aktionsgruppe (E-Mail, SMS usw.) hinzuzufügen, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.
9. Geben Sie die **Warnungsdetails** wie **Warnungsregelname**, **Beschreibung** und **Schweregrad** ein.
10. Klicken Sie auf **Warnungsregel erstellen**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Erstellen einer Warnung, wenn die Cloudtiering-Rückrufgröße 500 GiB pro Tag überschritten hat

1. Navigieren Sie im **Azure-Portal** zum entsprechenden **Speichersynchronisierungsdienst**. 
2. Wechseln Sie zum Abschnitt **Überwachung**, und klicken Sie auf **Warnungen**. 
3. Klicken Sie auf **+ Neue Warnungsregel**, um eine neue Warnungsregel zu erstellen. 
4. Konfigurieren Sie eine Bedingung, indem Sie auf **Bedingung auswählen** klicken.
5. Klicken Sie auf dem Blatt **Konfigurieren der Signallogik** unter dem Signalnamen auf **Cloudtiering-Rückrufgröße**.  
6. Wählen Sie die folgende Dimensionskonfiguration aus: 
     - Dimensionsname: **Servername**  
     - Operator: **=** 
     - Dimensionswerte: **Alle aktuellen und zukünftigen Werte**  
7. Navigieren Sie zu **Warnungslogik**, und führen Sie die folgenden Schritte aus: 
     - Auf **Statisch** festgelegter Schwellenwert 
     - Operator: **Größer als** 
     - Aggregationstyp: **Gesamt**  
     - Schwellenwert (in Bytes): **67108864000** 
     - Auswertung basierend auf: Aggregationsgranularität = **24 Stunden** | Häufigkeit der Auswertung = **Jede Stunde** 
    - Klicken Sie auf **Fertig**. 
8. Klicken Sie auf **Aktionsgruppe auswählen**, um der Warnung eine Aktionsgruppe (E-Mail, SMS usw.) hinzuzufügen, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.
9. Geben Sie die **Warnungsdetails** wie **Warnungsregelname**, **Beschreibung** und **Schweregrad** ein.
10. Klicken Sie auf **Warnungsregel erstellen**. 

## <a name="next-steps"></a>Nächste Schritte
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Berücksichtigen von Firewall- und Proxyeinstellungen](storage-sync-files-firewall-and-proxy.md)
- [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
- [Problembehandlung für die Azure-Dateisynchronisierung](storage-sync-files-troubleshoot.md)
- [Häufig gestellte Fragen zu Azure Files](storage-files-faq.md)
