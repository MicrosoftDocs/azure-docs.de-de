---
title: Überwachen der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure-Dateisynchronisierung überwachen.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c0f19e3ea4f5952ac96b589fa267a2136c85e4f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711657"
---
# <a name="monitor-azure-file-sync"></a>Überwachen der Azure-Dateisynchronisierung

Mit der Azure-Dateisynchronisierung können Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Mit der Azure-Dateisynchronisierung werden Ihre Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Sie können weltweit so viele Caches wie nötig nutzen.

In diesem Artikel wird beschrieben, wie Sie Ihre Bereitstellung der Azure-Dateisynchronisierung mit dem Azure-Portal und Windows Server überwachen.

Aktuell stehen die folgenden Überwachungsoptionen zur Verfügung.

## <a name="azure-portal"></a>Azure-Portal

Im Azure-Portal können Sie die Integrität registrierter Server und Serverendpunkte (Synchronisierungsintegrität) sowie Metriken anzeigen.

### <a name="storage-sync-service"></a>Speichersynchronisierungsdienst

Wechseln Sie im Azure-Portal zum Speichersynchronisierungsdienst, um die Integrität registrierter Server, die Integrität der Serverendpunkte und Metriken anzuzeigen. Die Integrität registrierter Server können Sie auf dem Blatt **Registrierte Server** anzeigen, die Integrität der Serverendpunkte auf dem Blatt **Synchronisierungsgruppen**.

Integrität registrierter Server:

- Wenn sich der **registrierte Server** im Zustand **Online** befindet, kommuniziert er erfolgreich mit dem Dienst.
- Wenn sich der **registrierte Server** im Zustand **Als offline angezeigt** befindet, vergewissern Sie sich, dass der Überwachungsprozess für die Speichersynchronisierung (AzureStorageSyncMonitor.exe) auf dem Server ausgeführt wird. Wenn sich der Server hinter einer Firewall oder einem Proxy befindet, konfigurieren Sie die Firewall und den Proxy gemäß [diesem Artikel](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy).

Integrität der Serverendpunkte:

- Die Integrität der Serverendpunkte im Portal basiert auf den Synchronisierungsereignissen, die im Protokoll für Telemetrieereignisse auf dem Server protokolliert werden (ID 9102 und 9302). Wenn eine Synchronisierungssitzung aufgrund eines vorübergehenden Fehlers (z.B. Abbruchfehler) nicht erfolgreich ist, wird die Synchronisierung im Portal möglicherweise weiterhin als fehlerfrei angezeigt, solange ein Fortschritt bei der Synchronisierungssitzung verzeichnet wird. Anhand der Ereignis-ID 9302 wird bestimmt, ob Dateien angewendet werden. Weitere Informationen finden Sie unter [Wie überwache ich die Integrität der Synchronisierung?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) und [Wie überwache ich den Fortschritt einer aktuellen Synchronisierungssitzung?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Wenn das Portal einen Synchronisierungsfehler aufgrund eines fehlenden Fortschritts der Synchronisierung anzeigt, lesen Sie die [Dokumentation zur Problembehandlung](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors).

Metriken:

- Die folgenden Metriken können im Speichersynchronisierungsdienst-Portal angezeigt werden:

  | Metrikname | BESCHREIBUNG | Blattname |
  |-|-|-|
  | Bytes synchronisiert | Größe der übertragenen Daten (Upload und Download) | Synchronisierungsgruppe, Serverendpunkt |
  | Cloudtieringrückruf | Größe der zurückgerufenen Daten | Registrierte Server |
  | Dateien ohne Synchronisierung | Anzahl der Dateien, die nicht synchronisiert werden können | Serverendpunkt |
  | Dateien synchronisiert | Anzahl der übertragenen Dateien (Upload und Download) | Synchronisierungsgruppe, Serverendpunkt |
  | Onlinestatus des Servers | Anzahl der Taktsignale, die vom Server empfangen wurden | Registrierte Server |

- Weitere Informationen finden Sie im Abschnitt [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Die Diagramme im Speichersynchronisierungsdienst-Portal haben einen Zeitbereich von 24 Stunden. Die verschiedenen Zeitbereiche oder -dimensionen können Sie mit Azure Monitor anzeigen.

### <a name="azure-monitor"></a>Azure Monitor

Verwenden Sie [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview), um Synchronisierung, Cloudtiering und Serverkonnektivität zu überwachen. Metriken für die Azure-Dateisynchronisierung werden standardmäßig aktiviert und alle 15 Minuten an Azure Monitor gesendet.

Wählen Sie den Ressourcentyp **Speichersynchronisierungsdienste** aus, um Metriken der Azure-Dateisynchronisierung in Azure Monitor anzuzeigen.

In Azure Monitor sind die folgenden Metriken für die Azure-Dateisynchronisierung verfügbar:

| Metrikname | BESCHREIBUNG |
|-|-|
| Bytes synchronisiert | Größe der übertragenen Daten (Upload und Download).<br><br>Einheit: Byte<br>Aggregationstyp: Summe<br>Verfügbare Dimensionen: Name des Serverendpunkts, Synchronisierungsrichtung, Name der Synchronisierungsgruppe |
| Cloudtieringrückruf | Größe der zurückgerufenen Daten.<br><br>Einheit: Byte<br>Aggregationstyp: Summe<br>Verfügbare Dimension: Servername |
| Dateien ohne Synchronisierung | Anzahl von Dateien, für die keine Synchronisierung möglich ist.<br><br>Einheit: Count<br>Aggregationstyp: Summe<br>Verfügbare Dimensionen: Name des Serverendpunkts, Synchronisierungsrichtung, Name der Synchronisierungsgruppe |
| Dateien synchronisiert | Anzahl der übertragenen Dateien (Upload und Download)<br><br>Einheit: Count<br>Aggregationstyp: Summe<br>Verfügbare Dimensionen: Name des Serverendpunkts, Synchronisierungsrichtung, Name der Synchronisierungsgruppe |
| Onlinestatus des Servers | Anzahl von Taktsignalen, die vom Server empfangen wurden.<br><br>Einheit: Count<br>Aggregationstyp: Maximum<br>Verfügbare Dimension: Servername |
| Ergebnis der Synchronisierungssitzung | Ergebnis der Synchronisierungssitzung (1 = erfolgreiche Synchronisierungssitzung, 0 = fehlerhafte Synchronisierungssitzung)<br><br>Einheit: Count<br>Aggregationstypen: Maximum<br>Verfügbare Dimensionen: Name des Serverendpunkts, Synchronisierungsrichtung, Name der Synchronisierungsgruppe |

## <a name="windows-server"></a>Windows Server

Unter Windows Server können Sie die Integrität für Cloudtiering, registrierte Server und Synchronisierung anzeigen.

### <a name="event-logs"></a>Ereignisprotokolle

Verwenden Sie das Telemetrieereignisprotokoll auf dem Server, um die Integrität für registrierte Server, Synchronisierung und Cloudtiering zu überwachen. Das Telemetrieereignisprotokoll befindet sich in der Ereignisanzeige unter *Anwendungen und Dienste\Microsoft\FileSync\Agent*.

Synchronisierungsintegrität:

- Nach Abschluss einer Synchronisierungssitzung wird ein Ereignis mit der ID 9102 protokolliert. Anhand dieses Ereignisses können Sie bestimmen, ob Synchronisierungssitzungen erfolgreich abgeschlossen wurden (**HResult = 0**) und ob Synchronisierungsfehler auf Elementebene vorliegen. Weitere Informationen finden Sie in der Dokumentation zur [Synchronisierungsintegrität](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) und zu [Fehlern auf Elementebene](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Manchmal tritt bei Synchronisierungssitzungen allgemein ein Fehler auf oder PerItemErrorCount ist ungleich 0. Jedoch wird trotzdem ein Fortschritt angezeigt, und einige Dateien synchronisieren erfolgreich. Dies kann bei den Feldern beobachtet werden, die mit „Applied“ beginnen (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount und AppliedSizeBytes). Die Werte dieser Felder geben an, bis zu welchem Anteil die Sitzung erfolgreich durchgeführt wurde. Wenn für mehrere aufeinanderfolgende Synchronisierungssitzungen Fehler angezeigt werden, die Sitzungen jedoch steigende „Applied“-Werte aufweisen, sollten Sie der Synchronisierung Zeit für einen erneuten Versuch geben, bevor Sie ein Supportticket öffnen.

- Die Ereignis-ID 9302 wird alle 5 bis 10 Minuten protokolliert, wenn eine aktive Synchronisierungssitzung vorliegt. Bestimmen Sie anhand dieses Ereignisses, ob für die aktuelle Synchronisierungssitzung ein Fortschritt verzeichnet wird (**AppliedItemCount > 0**). Wenn die Synchronisierung keinen Fortschritt zeigt, sollte für die Synchronisierungssitzung irgendwann ein Fehler auftreten, und es wird die Ereignis-ID 9102 mit dem Fehler protokolliert. Weitere Informationen finden Sie in der [Dokumentation zum Synchronisierungsfortschritt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integrität registrierter Server:

- Die Ereignis-ID 9301 wird alle 30 Sekunden protokolliert, wenn ein Server Aufträge im Dienst abfragt. Wenn GetNextJob mit dem **Status = 0** abgeschlossen wird, ist eine Kommunikation zwischen dem Server und dem Dienst möglich. Wenn GetNextJob mit einem Fehler abgeschlossen wird, lesen Sie die [Dokumentation zur Problembehandlung](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors), um weitere Informationen zu erhalten.

Cloudtieringintegrität:

- Verwenden Sie die Ereignis-IDs 9003, 9016 und 9029 im Telemetrieereignisprotokoll (in der Ereignisanzeige unter *Anwendungen und Dienste\Microsoft\FileSync\Agent*), um die Tieringaktivität auf einem Server zu überwachen.

  - Die Ereignis-ID 9003 ermöglicht die Fehlerverteilung für einen Serverendpunkt. Beispiel:  Gesamtfehlerzahl und ErrorCode. Pro Fehlercode wird ein Ereignis protokolliert.
  - Die Ereignis-ID 9016 stellt Ghostingergebnisse für ein Volume bereit. Beispiel:  Freier Speicherplatz in Prozent, Anzahl der Dateien in der Sitzung, für die ein Ghosting durchgeführt wurde, und Anzahl von Dateien, bei denen beim Ghosting ein Fehler aufgetreten ist.
  - Die Ereignis-ID 9029 bietet Informationen zu Ghostingsitzungen für einen Serverendpunkt. Beispiel:  Anzahl der in der Sitzung herangezogenen Dateien, Anzahl der Dateien in der Sitzung, für die in der Sitzung ein Tiering durchgeführt wurde, und Anzahl der Dateien, für die bereits ein Tiering durchgeführt wurde.
  
- Verwenden Sie die Ereignis-IDs 9005, 9006, 9009 und 9059 im Telemetrieereignisprotokoll (in der Ereignisanzeige unter *Anwendungen und Dienste\Microsoft\FileSync\Agent*), um die Rückrufaktivität auf einem Server zu überwachen.

  - Die Ereignis-ID 9005 bietet Zuverlässigkeit beim Rückruf für einen Serverendpunkt. Beispiel:  Gesamtanzahl eindeutiger Dateien, auf die zugegriffen wird, und Gesamtanzahl eindeutiger Dateien, bei denen beim Zugriff ein Fehler aufgetreten ist.
  - Die Ereignis-ID 9006 ermöglicht die Rückruffehlerverteilung für einen Serverendpunkt. Beispiel:  Gesamtanzahl der Anforderungen, bei denen ein Fehler aufgetreten ist, und ErrorCode. Pro Fehlercode wird ein Ereignis protokolliert.
  - Die Ereignis-ID 9009 bietet Informationen zu Rückrufsitzungen für einen Serverendpunkt. Beispiel:  DurationSeconds, CountFilesRecallSucceeded und CountFilesRecallFailed.
  - Die Ereignis-ID 9059 bietet Informationen zur Anwendungsrückrufverteilung für einen Serverendpunkt. Beispiel:  ShareId, Anwendungsname und TotalEgressNetworkBytes.

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

## <a name="next-steps"></a>Nächste Schritte
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Berücksichtigen von Firewall- und Proxyeinstellungen](storage-sync-files-firewall-and-proxy.md)
- [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
- [Problembehandlung für die Azure-Dateisynchronisierung](storage-sync-files-troubleshoot.md)
- [Häufig gestellte Fragen zu Azure Files](storage-files-faq.md)
