---
title: Versionshinweise zum Azure-Dateisynchronisierungs-Agent | Microsoft-Dokumentation
description: Lesen Sie die Versionshinweise zum Azure-Dateisynchronisierungs-Agent, mit dem Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren können.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 11/5/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 0b85bbbbd69338f54f9e698d9d47058f78c32057
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630072"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Versionshinweise zum Azure-Dateisynchronisierungs-Agent
Mit der Azure-Dateisynchronisierung können Sie Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Ihre Windows Server-Installationen werden in einen schnellen Cache Ihrer Azure-Dateifreigabe transformiert. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen (z.B. SMB, NFS und FTPS). Sie können weltweit so viele Caches wie nötig nutzen.

Dieser Artikel enthält die Versionshinweise für die unterstützten Versionen des Azure-Dateisynchronisierungs-Agents.

## <a name="supported-versions"></a>Unterstützte Versionen
Die folgenden Versionen des Agents für die Azure-Dateisynchronisierung werden unterstützt:

| Meilenstein | Agent-Versionsnummer | Veröffentlichungsdatum | Status |
|----|----------------------|--------------|------------------|
| Release für V11.1: [KB4539951](https://support.microsoft.com/en-us/help/4539951)| 11.1.0.0 | 4\. November 2020 | Unterstützt – Flighting |
| V10.1-Release: [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 5\. Juni 2020 | Unterstützt |
| Mai 2020 Updaterollup – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19. Mai 2020 | Unterstützt |
| V10-Release: [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 9\. April 2020 | Unterstützt |
| Updaterollup von Dezember 2019: [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12. Dezember 2019 | Unterstützt – Agent-Version läuft am 16. Februar 2021 ab. |
| V9-Release: [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2\. Dezember 2019 | Unterstützt – Agent-Version läuft am 16. Februar 2021 ab. |
| V8-Release – [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8\. Oktober 2019 | Unterstützt – Agent-Version läuft am 12. Januar 2021 ab. |

## <a name="unsupported-versions"></a>Nicht unterstützte Versionen
Die folgenden Versionen des Agents für die Azure-Dateisynchronisierung sind abgelaufen und werden nicht mehr unterstützt:

| Meilenstein | Agent-Versionsnummer | Veröffentlichungsdatum | Status |
|----|----------------------|--------------|------------------|
| V7 Release | 7.0.0.0 – 7.2.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 1. September 2020 abgelaufen. |
| V6-Release | 6.0.0.0–6.3.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 21. April 2020 abgelaufen. |
| Release V5 | 5.0.2.0–5.2.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 18. März 2020 abgelaufen. |
| Release V4 | 4.0.1.0 – 4.3.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 6. November 2019 abgelaufen. |
| Release V3 | 3.1.0.0 – 3.4.0.0 | – | Nicht unterstützt – Agent-Versionen sind am 19. August 2019 abgelaufen. |
| Pre-GA-Agents | 1.1.0.0 – 3.0.13.0 | – | Nicht unterstützt – Agent-Versionen sind am 1. Oktober 2018 abgelaufen. |

### <a name="azure-file-sync-agent-update-policy"></a>Updaterichtlinie für den Azure-Dateisynchronisierungs-Agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11100"></a>Agent-Version 11.1.0.0
Die folgenden Versionshinweise gelten für Version 11.1.0.0 des Azure-Dateisynchronisierungs-Agents (Veröffentlichung: 4. November 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme
- Neue Cloudtieringmodi zum Steuern des ersten Downloads und des proaktiven Abrufs
    - Ursprünglicher Downloadmodus: Sie können jetzt auswählen, wie Ihre Dateien anfänglich auf den neuen Serverendpunkt heruntergeladen werden sollen. Möchten Sie Ihre Dateien aufteilen oder so viele Dateien wie möglich gemäß dem Zeitstempel der letzten Änderung auf Ihren Server herunterladen? Das ist kein Problem. Sie können kein Cloudtiering verwenden? Nun können Sie dafür sorgen, dass es auf Ihrem System keine verteilten Dateien mehr gibt. Weitere Informationen finden Sie im Abschnitt [Erstellen eines Serverendpunkts](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) in der Dokumentation zum Bereitstellen der Azure-Dateisynchronisierung.
    - Proaktiver Abrufmodus: Jedes Mal, wenn eine Datei erstellt oder geändert wird, können Sie sie proaktiv auf Servern abrufen, die Sie in derselben Synchronisierungsgruppe angeben. Dadurch wird die Datei auf jedem angegebenen Server für den Verbrauch bereitgestellt. Arbeiten Teams auf der ganzen Welt an denselben Daten? Aktivieren Sie den proaktiven Abruf. Wenn ein Team in einer anderen Zeitzone Dateien aktualisiert, werden diese heruntergeladen und stehen am nächsten Morgen direkt für ein anderes Team bereit. Weitere Informationen finden Sie im Abschnitt [Proaktives Abrufen neuer und geänderter Dateien von einer Azure-Dateifreigabe](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) in der Dokumentation zum Bereitstellen der Azure-Dateisynchronisierung.

- Ausschließen von Anwendungen vom Cloudtiering basierend auf der Nachverfolgung der letzten Zugriffszeit. Sie können nun Anwendungen von der Nachverfolgung der letzten Zugriffszeit ausschließen. Wenn eine Anwendung auf eine Datei zugreift, wird die Uhrzeit des letzten Zugriffs auf die Datei in der Cloudtiering-Datenbank aktualisiert. Anwendungen, die das Dateisystem scannen (z. B. Virenschutz), führen dazu, dass der Zeitpunkt des letzten Zugriffs für alle Dateien gleich ist. Dies wirkt sich auf das Aufteilen von Dateien aus.

    Um Anwendungen aus der Nachverfolgung der letzten Zugriffszeit auszuschließen, fügen Sie der Registrierungseinstellung HeatTrackingProcessNameExclusionList unter „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync“ den Prozessnamen hinzu.

    Beispiel: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Datendeduplizierungs- und FSRM-Prozesse (File Server Resource Manager) werden standardmäßig ausgeschlossen (hartcodiert), und die Prozessausschlussliste wird alle 5 Minuten aktualisiert.

- Sonstige Verbesserungen bei Leistung und Zuverlässigkeit
    - Die Leistung bei der Änderungserkennung zum Erkennen von in der Azure-Dateifreigabe geänderten Dateien wurde verbessert.
    - Die Uploadleistung bei der Synchronisierung wurde verbessert.
    - Der erste Upload wird jetzt von einer VSS-Momentaufnahme ausgeführt. Dadurch werden die Fehler pro Element und Synchronisierungssitzungsfehler verringert.
    - Die Zuverlässigkeit der Synchronisierung für bestimmte E/A-Muster wurde verbessert.
    - Es wurde ein Fehler behoben, der verhinderte, dass die Synchronisierungsdatenbank bei einem Failover in Failoverclustern in der Zeit zurückläuft.
    - Die Rückrufleistung beim Zugriff auf verteilte Dateien wurde verbessert.

### <a name="evaluation-tool"></a>Auswertungstool
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungstool für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Tool ist ein Azure PowerShell-Cmdlet, das auf potenzielle Probleme mit Ihrem Dateisystem und Dataset prüft, z.B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Anweisungen zur Installation und Verwendung finden Sie im Planungshandbuch im Abschnitt [Auswertungstools](./storage-sync-files-planning.md#evaluation-cmdlet). 

### <a name="agent-installation-and-server-configuration"></a>Agent-Installation und Serverkonfiguration
Weitere Informationen zum Installieren und Konfigurieren des Azure File Sync-Agents mit Windows Server finden Sie unter [Planen einer Bereitstellung der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-planning.md) sowie unter [Bereitstellen von Azure File Sync (Vorschau)](storage-sync-files-deployment-guide.md).

- Das Agent-Installationspaket muss mit erhöhten Berechtigungen (Administratorberechtigungen) installiert werden.
- Der Agent wird für die Bereitstellungsoption „Nano Server“ nicht unterstützt.
- Der Agent wird nur unter Windows Server 2019, Windows Server 2016 und Windows Server 2012 R2 unterstützt.
- Der Agent benötigt mindestens 2 GiB Arbeitsspeicher. Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden. Weitere Informationen finden Sie unter [Empfohlenen Systemressourcen](./storage-sync-files-planning.md#recommended-system-resources).
- Der Dienst „Storage-Synchronisierungs-Agent“ (FileSyncSvc) unterstützt keine Serverendpunkte, die sich auf einem Volume befinden, für das das Verzeichnis „System Volume Information“ (SVI) komprimiert ist. Diese Konfiguration führt zu unerwarteten Ergebnissen.

### <a name="interoperability"></a>Interoperabilität
- Virenschutz, Sicherung und andere Anwendungen, die auf Tieringdateien zugreifen, können zu unerwünschten Rückrufen führen, wenn sie das Offlineattribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Weitere Informationen finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-troubleshoot.md).
- FSRM-Dateiprüfungen (File Server Resource Manager, Ressourcen-Manager für Dateiserver) können zu Fehlern aufgrund einer endlosen Synchronisierung führen, wenn Dateien aufgrund der damit verbundenen Vorgänge blockiert werden.
- Die Ausführung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Der Azure-Dateisynchronisierungs-Agent sollte installiert werden, nachdem das Serverimage bereitgestellt und das Sysprep-Mini-Setup abgeschlossen wurde.

### <a name="sync-limitations"></a>Einschränkungen bei der Synchronisierung
Folgende Elemente werden nicht synchronisiert, aber der restliche Systembetrieb ist nicht beeinträchtigt:
- Dateien mit nicht unterstützten Zeichen. Eine Liste mit den Zeichen, die nicht unterstützt werden, finden Sie im [Leitfaden zur Problembehandlung](storage-sync-files-troubleshoot.md#handling-unsupported-characters).
- Dateien oder Verzeichnisse, die mit einem Punkt enden.
- Pfade, die länger als 2.048 Zeichen sind.
- Der SACL-Teil (System-Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, die für die Überwachung verwendet wird.
- Erweiterte Attribute
- Alternative Datenströme
- Analysepunkte
- Feste Links
- Die Komprimierung (sofern für eine Serverdatei festgelegt) wird nicht beibehalten, wenn Änderungen mit der Datei von anderen Endpunkten synchronisiert werden.
- Mit EFS (oder einer anderen Benutzermodusverschlüsselung) verschlüsselte Dateien, die den Dienst am Lesen der Daten hindern.

    > [!Note]  
    > Bei der Azure-Dateisynchronisierung werden Daten während der Übertragung immer verschlüsselt. Ruhende Daten werden in Azure immer verschlüsselt.
 
### <a name="server-endpoint"></a>Serverendpunkt
- Ein Serverendpunkt kann nur auf einem NTFS-Volume erstellt werden. ReFS, FAT, FAT32 und andere Dateisysteme werden von der Azure-Dateisynchronisierung derzeit nicht unterstützt.
- Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.
- Failoverclustering wird nur mit Clusterdatenträgern, aber nicht mit freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) unterstützt.
- Ein Serverendpunkt kann nicht geschachtelt werden. Er kann auf demselben Volume parallel zu einem anderen Endpunkt vorhanden sein (Koexistenz).
- Speichern Sie keine Betriebssystem- oder Anwendungsauslagerungsdatei an einem Serverendpunkt-Standort.
- Der Servername im Portal wird bei Umbenennung des Servers nicht aktualisiert.

### <a name="cloud-endpoint"></a>Cloudendpunkt
- Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird einmal alle 24 Stunden gestartet. Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.
- Der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe, ein anderes Abonnement oder einen anderen Azure AD-Mandanten verschoben werden. Wenn der Speichersynchronisierungsdienst oder das Speicherkonto verschoben wurde, müssen Sie der Anwendung „Microsoft.StorageSync“ Zugriff auf das Speicherkonto gewähren (siehe [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Beim Erstellen des Cloudendpunkts müssen sich der Speichersynchronisierungsdienst und das Speicherkonto im selben Azure AD-Mandanten befinden. Nach der Erstellung des Cloudendpunkts können der Speichersynchronisierungsdienst und das Speicherkonto in verschiedene Azure AD Mandanten verschoben werden.

### <a name="cloud-tiering"></a>Cloudtiering
- Wenn eine Tieringdatei mit Robocopy an einen anderen Speicherort kopiert wird, ist die sich ergebende Datei keine Tieringdatei. Das Offlineattribut kann festgelegt werden, da dieses Attribut fälschlicherweise von Robocopy in Kopiervorgänge eingefügt wird.
- Verwenden Sie beim Kopieren der Dateien mit Robocopy die Option „/MIR“, um Dateizeitstempel beizubehalten. So wird sichergestellt, dass das Tiering für ältere Dateien früher als für die Dateien durchgeführt wird, auf die zuletzt zugegriffen wurde.

## <a name="agent-version-10100"></a>Agent-Version 10.1.0.0
Die folgenden Versionshinweise gelten für Version 10.1.0.0 des Azure-Dateisynchronisierungs-Agents, die am 5. Juni 2020 veröffentlicht wurde. Sie gelten zusätzlich zu den für die Versionen 10.0.0.0 und 10.0.2.0 angegeben Versionshinweisen.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme

- Unterstützung für private Azure-Endpunkte
    - Synchronisierungsdatenverkehr zum Speichersynchronisierungsdienst kann jetzt an einen privaten Endpunkt gesendet werden. Dies ermöglicht ein Tunneln über eine ExpressRoute- oder VPN-Verbindung. Weitere Informationen finden Sie unter [Konfigurieren von Netzwerkendpunkten für die Azure-Dateisynchronisierung](./storage-sync-files-networking-endpoints.md).
- Die Metrik „Dateien synchronisiert“ zeigt nun bei einer großen Synchronisierung den Fortschritt während der Ausführung an statt am Ende.
- Verschiedene Verbesserungen in Bezug auf die Zuverlässigkeit bei der Agent-Installation, beim Cloudtiering, bei der Synchronisierung und bei der Telemetrie.

## <a name="agent-version-10020"></a>Agent-Version 10.0.2.0
Die folgenden Versionshinweise gelten für Version 10.0.2.0 des Azure-Dateisynchronisierungs-Agents, die am 19. Mai 2020 veröffentlicht wurde. Diese Hinweise gelten zusätzlich zu den Versionshinweisen, die für die Version 10.0.0.0 angegeben sind.

In dieser Version behobenes Problem:  
- Der Storage-Synchronisierungs-Agent (FileSyncSvc) stürzt nach der Installation des Azure-Dateisynchronisierung-Agents, Version 10, häufig ab.

> [!Note]  
>Für diese Version wurde kein Test-Flight für Server durchgeführt, die automatisch aktualisiert werden, wenn eine neue Version verfügbar ist. Verwenden Sie zum Installieren dieses Updates Microsoft Update oder den Microsoft Update-Katalog (Installationsanweisungen finden Sie unter [KB4522412](https://support.microsoft.com/help/4522412)).

## <a name="agent-version-10000"></a>Agent-Version 10.0.0.0
Die folgenden Versionshinweise gelten für Version 10.0.0.0 des Azure-Dateisynchronisierungs-Agents, die am 9. April 2020 veröffentlicht wurde.

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme

- Verbesserter Synchronisierungsfortschritt im Portal
    - Bei Verwendung der Agent-Version V10 wird in Kürze der Typ der aktuell ausgeführten Synchronisierungssitzung im Azure-Portal angezeigt. Beispiel: erster Download, regulärer Download, Hintergrundrückruf (Fälle von schnellen Notfallwiederherstellungen) und ähnliches 

- Verbesserte Benutzeroberfläche des Portals für Cloudtiering
    - Für Dateien mit Tiering- oder Abruffehlern werden jetzt die Tieringfehler in den Eigenschaften des Serverendpunkts angezeigt.
    - Für Serverendpunkte stehen jetzt zusätzliche Cloudtieringinformationen bereit:
        - Größe des lokalen Caches
        - Effizienz der Cachenutzung
        - Details zur Cloudtieringrichtlinie: Volumegröße, aktueller freier Speicherplatz oder der Zeitpunkt des letzten Zugriffs auf die älteste Datei im lokalen Cache.
    - Diese Änderungen werden im Azure-Portal unmittelbar nach dem V10-Agent-Erstrelease bereitgestellt.

- Unterstützung für das Verschieben des Speichersynchronisierungsdiensts und/oder Speicherkontos in einen anderen Azure Active Directory-Mandanten
    - Die Azure-Dateisynchronisierung unterstützt jetzt das Verschieben des Speichersynchronisierungsdiensts und/oder Speicherkontos in eine andere Ressourcengruppe, in ein anderes Abonnement oder in einen anderen Azure AD Mandanten.
    
- Sonstige Verbesserungen bei Leistung und Zuverlässigkeit
    - Die Änderungserkennung für die Azure-Dateifreigabe schlägt möglicherweise fehl, wenn ein virtuelles Netzwerk (VNET) und Firewallregeln für das Speicherkonto konfiguriert sind.
    - Geringerer Arbeitsspeicherverbrauch im Zusammenhang mit Rückrufen 
    - Verbesserte Leistung bei Verwendung des [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)-Cmdlets
    - Verschiedene Verbesserungen im Hinblick auf die Zuverlässigkeit der Synchronisierung 
    
### <a name="evaluation-tool"></a>Auswertungstool
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungstool für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Tool ist ein Azure PowerShell-Cmdlet, das auf potenzielle Probleme mit Ihrem Dateisystem und Dataset prüft, z.B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Anweisungen zur Installation und Verwendung finden Sie im Planungshandbuch im Abschnitt [Auswertungstools](./storage-sync-files-planning.md#evaluation-cmdlet). 

### <a name="agent-installation-and-server-configuration"></a>Agent-Installation und Serverkonfiguration
Weitere Informationen zum Installieren und Konfigurieren des Azure File Sync-Agents mit Windows Server finden Sie unter [Planen einer Bereitstellung der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-planning.md) sowie unter [Bereitstellen von Azure File Sync (Vorschau)](storage-sync-files-deployment-guide.md).

- Das Agent-Installationspaket muss mit erhöhten Berechtigungen (Administratorberechtigungen) installiert werden.
- Der Agent wird für die Bereitstellungsoption „Nano Server“ nicht unterstützt.
- Der Agent wird nur unter Windows Server 2019, Windows Server 2016 und Windows Server 2012 R2 unterstützt.
- Der Agent benötigt mindestens 2 GiB Arbeitsspeicher. Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden.
- Der Dienst „Storage-Synchronisierungs-Agent“ (FileSyncSvc) unterstützt keine Serverendpunkte, die sich auf einem Volume befinden, für das das Verzeichnis „System Volume Information“ (SVI) komprimiert ist. Diese Konfiguration führt zu unerwarteten Ergebnissen.

### <a name="interoperability"></a>Interoperabilität
- Virenschutz, Sicherung und andere Anwendungen, die auf Tieringdateien zugreifen, können zu unerwünschten Rückrufen führen, wenn sie das Offlineattribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Weitere Informationen finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-troubleshoot.md).
- FSRM-Dateiprüfungen (File Server Resource Manager, Ressourcen-Manager für Dateiserver) können zu Fehlern aufgrund einer endlosen Synchronisierung führen, wenn Dateien aufgrund der damit verbundenen Vorgänge blockiert werden.
- Die Ausführung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Der Azure-Dateisynchronisierungs-Agent sollte installiert werden, nachdem das Serverimage bereitgestellt und das Sysprep-Mini-Setup abgeschlossen wurde.

### <a name="sync-limitations"></a>Einschränkungen bei der Synchronisierung
Folgende Elemente werden nicht synchronisiert, aber der restliche Systembetrieb ist nicht beeinträchtigt:
- Dateien mit nicht unterstützten Zeichen. Eine Liste mit den Zeichen, die nicht unterstützt werden, finden Sie im [Leitfaden zur Problembehandlung](storage-sync-files-troubleshoot.md#handling-unsupported-characters).
- Dateien oder Verzeichnisse, die mit einem Punkt enden.
- Pfade, die länger als 2.048 Zeichen sind.
- Der SACL-Teil (System-Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, die für die Überwachung verwendet wird.
- Erweiterte Attribute
- Alternative Datenströme
- Analysepunkte
- Feste Links
- Die Komprimierung (sofern für eine Serverdatei festgelegt) wird nicht beibehalten, wenn Änderungen mit der Datei von anderen Endpunkten synchronisiert werden.
- Mit EFS (oder einer anderen Benutzermodusverschlüsselung) verschlüsselte Dateien, die den Dienst am Lesen der Daten hindern.

    > [!Note]  
    > Bei der Azure-Dateisynchronisierung werden Daten während der Übertragung immer verschlüsselt. Ruhende Daten werden in Azure immer verschlüsselt.
 
### <a name="server-endpoint"></a>Serverendpunkt
- Ein Serverendpunkt kann nur auf einem NTFS-Volume erstellt werden. ReFS, FAT, FAT32 und andere Dateisysteme werden von der Azure-Dateisynchronisierung derzeit nicht unterstützt.
- Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.
- Failoverclustering wird nur mit Clusterdatenträgern, aber nicht mit freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) unterstützt.
- Ein Serverendpunkt kann nicht geschachtelt werden. Er kann auf demselben Volume parallel zu einem anderen Endpunkt vorhanden sein (Koexistenz).
- Speichern Sie keine Betriebssystem- oder Anwendungsauslagerungsdatei an einem Serverendpunkt-Standort.
- Der Servername im Portal wird bei Umbenennung des Servers nicht aktualisiert.

### <a name="cloud-endpoint"></a>Cloudendpunkt
- Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird einmal alle 24 Stunden gestartet. Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.
- Der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe, ein anderes Abonnement oder einen anderen Azure AD-Mandanten verschoben werden. Wenn der Speichersynchronisierungsdienst oder das Speicherkonto verschoben wurde, müssen Sie der Anwendung „Microsoft.StorageSync“ Zugriff auf das Speicherkonto gewähren (siehe [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Beim Erstellen des Cloudendpunkts müssen sich der Speichersynchronisierungsdienst und das Speicherkonto im selben Azure AD-Mandanten befinden. Nach der Erstellung des Cloudendpunkts können der Speichersynchronisierungsdienst und das Speicherkonto in verschiedene Azure AD Mandanten verschoben werden.

### <a name="cloud-tiering"></a>Cloudtiering
- Wenn eine Tieringdatei mit Robocopy an einen anderen Speicherort kopiert wird, ist die sich ergebende Datei keine Tieringdatei. Das Offlineattribut kann festgelegt werden, da dieses Attribut fälschlicherweise von Robocopy in Kopiervorgänge eingefügt wird.
- Verwenden Sie beim Kopieren der Dateien mit Robocopy die Option „/MIR“, um Dateizeitstempel beizubehalten. So wird sichergestellt, dass das Tiering für ältere Dateien früher als für die Dateien durchgeführt wird, auf die zuletzt zugegriffen wurde.

## <a name="agent-version-9100"></a>Agent-Version 9.1.0.0
Die folgenden Versionshinweise gelten für Version 9.1.0.0 des Azure-Dateisynchronisierungs-Agents, die am 12. Dezember 2019 veröffentlicht wurde. Diese Hinweise gelten zusätzlich zu den Versionshinweisen, die für die Version 9.0.0.0 angegeben sind.

In dieser Version behobenes Problem:  
- Fehler bei der Synchronisierung mit einem der folgenden Fehlercodes nach Upgrade auf Version 9.0 des Azure-Dateisynchronisierungs-Agents:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Agent-Version 9.0.0.0
Die folgenden Versionshinweise gelten für Version 9.0.0.0 des Azure-Dateisynchronisierungs-Agents (am 2. Dezember 2019 veröffentlicht).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme

- Unterstützung der Self-Service-Wiederherstellung
    - Benutzer können ihre Dateien nun mithilfe des Features „Vorherige Version“ wiederherstellen. Vor dem V9-Release wurde das Feature für vorherige Versionen auf Volumes mit aktiviertem Cloudtiering nicht unterstützt. Dieses Feature muss für jedes Volume separat aktiviert werden, auf denen ein Endpunkt mit aktiviertem Cloudtiering vorliegt. Weitere Informationen finden Sie unter  
[Self-Service-Wiederherstellung mit „Vorherige Versionen“ und VSS (Volumeschattenkopie-Dienst)](./storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Unterstützung größerer Dateifreigaben 
    - Die Azure-Dateisynchronisierung unterstützt nun bis zu 64 TiB und 100 Millionen Dateien in einem einzelnen Synchronisierungsnamespace.  
 
- Unterstützung der Datendeduplizierung auf Windows Server 2019 
    - Die Datendeduplizierung wird nun mit aktiviertem Cloudtiering auf Windows Server 2019 unterstützt. Für die Unterstützung der Datendeduplizierung auf Volumes mit Cloudtiering muss das Windows-Update [KB4520062](https://support.microsoft.com/help/4520062) installiert sein. 
 
- Verbesserung der Mindestdateigröße für Dateien, für die das Tiering durchgeführt werden soll 
    - Die Mindestdateigröße für Dateien, für die das Tiering durchgeführt werden soll, basiert nun auf der Größe des Dateisystemclusters (das Doppelte der Größe des Dateisystemclusters). Die Standardgröße des NTFS-Dateisystemclusters beträgt beispielsweise 4 KB, weshalb die Mindestdateigröße für eine Datei, für die das Tiering durchgeführt werden soll, 8 KB beträgt. 
 
- Cmdlet zum Testen der Netzwerkkonnektivität 
    - Im Rahmen der Konfiguration der Azure-Dateisynchronisierung müssen mehrere Dienstendpunkte kontaktiert werden. Diese verfügen jeweils über einen eigenen DNS-Namen, der für den Server erreichbar sein muss. Diese URLs sind auch spezifisch für die Region, bei der ein Server registriert ist. Sobald ein Server registriert ist, kann das Cmdlet zum Testen der Konnektivität (PowerShell- und Serverregistrierungs-Hilfsprogramm) verwendet werden, um die Kommunikation mit allen für diesen Server spezifischen URLs zu testen. Dieses Cmdlet kann Sie bei der Problembehandlung unterstützen, wenn unvollständige Kommunikation den Server daran hindert, vollständig mit der Azure-Dateisynchronisierung zu arbeiten. Es kann außerdem zum Optimieren der Proxy- und Firewallkonfigurationen verwendet werden.  
 
        Führen Sie die folgenden PowerShell-Befehle aus, um den Netzwerkkonnektivitätstest auszuführen: 
 
        Import-Module "C:\Programme\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Entfernen der Serverendpunktverbesserung bei aktiviertem Cloudtiering 
    - Das Entfernen eines Serverendpunkts führt wie zuvor nicht dazu, dass Dateien aus der Azure-Dateifreigabe entfernt werden. Jedoch wurde das Verhalten von Analysepunkten auf dem lokalen Server geändert. Analysepunkte (Zeiger, die auf nicht lokale Dateien auf dem Server zeigen) werden nun gelöscht, wenn ein Serverendpunkt entfernt wird. Die vollständig zwischengespeicherten Dateien werden auf dem Server beibehalten. Diese Verbesserung wurde vorgenommen, um [verwaiste mehrstufige Dateien](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) bei, Entfernen eines Serverendpunkts zu verhindern. Wenn der Serverendpunkt neu erstellt wird, werden die Analysepunkte für die mehrstufigen Dateien auf dem Server neu erstellt.  
 
- Verbesserungen von Leistung und Zuverlässigkeit 
    - Die Anzahl der Fehler bei nochmaligem Aufrufen wurde verringert. Die Größe erneuter Aufrufe wird nun automatisch gemäß der Netzwerkbandbreite angepasst. 
    - Die Downloadleistung beim Hinzufügen eines neuen Servers zu einer Synchronisierungsgruppe wurde verbessert. 
    - Die Anzahl der Dateien wurde verringert, die aufgrund von Einschränkungskonflikten nicht synchronisiert werden. 
    - In bestimmten Szenarien schlagen mehrstufige Dateien fehl oder werden unerwartet abgerufen, wenn der Serverendpunktpfad ein Volumebereitstellungspunkt ist.
    
### <a name="evaluation-tool"></a>Auswertungstool
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungstool für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Tool ist ein Azure PowerShell-Cmdlet, das auf potenzielle Probleme mit Ihrem Dateisystem und Dataset prüft, z.B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Anweisungen zur Installation und Verwendung finden Sie im Planungshandbuch im Abschnitt [Auswertungstools](./storage-sync-files-planning.md#evaluation-cmdlet). 

### <a name="agent-installation-and-server-configuration"></a>Agent-Installation und Serverkonfiguration
Weitere Informationen zum Installieren und Konfigurieren des Azure File Sync-Agents mit Windows Server finden Sie unter [Planen einer Bereitstellung der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-planning.md) sowie unter [Bereitstellen von Azure File Sync (Vorschau)](storage-sync-files-deployment-guide.md).

- Das Agent-Installationspaket muss mit erhöhten Berechtigungen (Administratorberechtigungen) installiert werden.
- Der Agent wird für die Bereitstellungsoption „Nano Server“ nicht unterstützt.
- Der Agent wird nur unter Windows Server 2019, Windows Server 2016 und Windows Server 2012 R2 unterstützt.
- Der Agent benötigt mindestens 2 GiB Arbeitsspeicher. Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden.
- Der Dienst „Storage-Synchronisierungs-Agent“ (FileSyncSvc) unterstützt keine Serverendpunkte, die sich auf einem Volume befinden, für das das Verzeichnis „System Volume Information“ (SVI) komprimiert ist. Diese Konfiguration führt zu unerwarteten Ergebnissen.

### <a name="interoperability"></a>Interoperabilität
- Virenschutz, Sicherung und andere Anwendungen, die auf Tieringdateien zugreifen, können zu unerwünschten Rückrufen führen, wenn sie das Offlineattribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Weitere Informationen finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-troubleshoot.md).
- FSRM-Dateiprüfungen (File Server Resource Manager, Ressourcen-Manager für Dateiserver) können zu Fehlern aufgrund einer endlosen Synchronisierung führen, wenn Dateien aufgrund der damit verbundenen Vorgänge blockiert werden.
- Die Ausführung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Der Azure-Dateisynchronisierungs-Agent sollte installiert werden, nachdem das Serverimage bereitgestellt und das Sysprep-Mini-Setup abgeschlossen wurde.

### <a name="sync-limitations"></a>Einschränkungen bei der Synchronisierung
Folgende Elemente werden nicht synchronisiert, aber der restliche Systembetrieb ist nicht beeinträchtigt:
- Dateien mit nicht unterstützten Zeichen. Eine Liste mit den Zeichen, die nicht unterstützt werden, finden Sie im [Leitfaden zur Problembehandlung](storage-sync-files-troubleshoot.md#handling-unsupported-characters).
- Dateien oder Verzeichnisse, die mit einem Punkt enden.
- Pfade, die länger als 2.048 Zeichen sind.
- Der DACL-Teil (besitzerverwaltete Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, sofern dieser größer als 2 KB ist. (Dieses Problem trifft nur zu, wenn Sie für ein einzelnes Element über mehr als ca. 40 Zugriffssteuerungseinträge verfügen.)
- Der SACL-Teil (System-Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, die für die Überwachung verwendet wird.
- Erweiterte Attribute
- Alternative Datenströme
- Analysepunkte
- Feste Links
- Die Komprimierung (sofern für eine Serverdatei festgelegt) wird nicht beibehalten, wenn Änderungen mit der Datei von anderen Endpunkten synchronisiert werden.
- Mit EFS (oder einer anderen Benutzermodusverschlüsselung) verschlüsselte Dateien, die den Dienst am Lesen der Daten hindern.

    > [!Note]  
    > Bei der Azure-Dateisynchronisierung werden Daten während der Übertragung immer verschlüsselt. Ruhende Daten werden in Azure immer verschlüsselt.
 
### <a name="server-endpoint"></a>Serverendpunkt
- Ein Serverendpunkt kann nur auf einem NTFS-Volume erstellt werden. ReFS, FAT, FAT32 und andere Dateisysteme werden von der Azure-Dateisynchronisierung derzeit nicht unterstützt.
- Auf mehrstufige Dateien kann nicht mehr zugegriffen werden, wenn für die Dateien vor dem Löschen des Serverendpunkts kein Rückruf erfolgt. Erstellen Sie den Serverendpunkt neu, um den Zugriff auf die Dateien wiederherzustellen. Nachdem 30 Tage vergangen sind, seitdem der Serverendpunkt gelöscht wurde, oder wenn der Cloudendpunkt gelöscht wurde, sind mehrstufige Dateien, für die kein Rückruf durchgeführt wurde, nicht mehr verwendbar. Weitere Informationen finden Sie unter [Auf mehrstufige Dateien auf dem Server kann nach dem Löschen eines Serverendpunkts nicht zugegriffen werden](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.
- Failoverclustering wird nur mit Clusterdatenträgern, aber nicht mit freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) unterstützt.
- Ein Serverendpunkt kann nicht geschachtelt werden. Er kann auf demselben Volume parallel zu einem anderen Endpunkt vorhanden sein (Koexistenz).
- Speichern Sie keine Betriebssystem- oder Anwendungsauslagerungsdatei an einem Serverendpunkt-Standort.
- Der Servername im Portal wird bei Umbenennung des Servers nicht aktualisiert.

### <a name="cloud-endpoint"></a>Cloudendpunkt
- Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird einmal alle 24 Stunden gestartet. Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.
- Der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe oder ein anderes Abonnement im vorhandenen Azure AD-Mandanten verschoben werden. Wenn das Speicherkonto verschoben wird, müssen Sie dem Hybrid-Dateisynchronisierungsdienst Zugriff auf das Speicherkonto gewähren (siehe [Sicherstellen, dass die Azure-Dateisynchronisierung Zugriff auf das Speicherkonto besitzt](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Die Azure-Dateisynchronisierung unterstützt nicht das Verschieben des Abonnements in einen anderen Azure AD-Mandanten.

### <a name="cloud-tiering"></a>Cloudtiering
- Wenn eine Tieringdatei mit Robocopy an einen anderen Speicherort kopiert wird, ist die sich ergebende Datei keine Tieringdatei. Das Offlineattribut kann festgelegt werden, da dieses Attribut fälschlicherweise von Robocopy in Kopiervorgänge eingefügt wird.
- Verwenden Sie beim Kopieren der Dateien mit Robocopy die Option „/MIR“, um Dateizeitstempel beizubehalten. So wird sichergestellt, dass das Tiering für ältere Dateien früher als für die Dateien durchgeführt wird, auf die zuletzt zugegriffen wurde.
- Wenn sich die Datei „pagefile.sys“ auf einem Volume befindet, für das Cloudtiering aktiviert ist, kann es zu einem Fehler beim Tiering von Dateien kommen. Die Datei „pagefile.sys“ sollte sich auf einem Volume befinden, auf dem Cloudtiering deaktiviert ist.

## <a name="agent-version-8000"></a>Agent-Version 8.0.0.0
Die folgenden Versionshinweise gelten für Version 8.0.0.0 des Azure-Dateisynchronisierungs-Agents (Veröffentlichung: 8. Oktober 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Verbesserungen und behobene Probleme

- Leistungsverbesserungen beim Wiederherstellen
    - Schnellere Wiederherstellungszeiten bei einer Wiederherstellung über Azure Backup. Wiederhergestellte Dateien werden auf Azure-Dateisynchronisierungsservern viel schneller wieder synchronisiert. 
- Verbesserte Benutzeroberfläche des Portals für Cloudtiering  
    - Wenn Sie mehrstufige Dateien haben, deren Abruf fehlschlägt, können Sie jetzt die Abruffehler in den Eigenschaften des Serverendpunkts anzeigen. Außerdem werden bei der Integrität der Serverendpunkte jetzt ein Fehler und Schritte zur Entschärfung angezeigt, wenn der Filtertreiber für das Cloudtiering nicht auf den Server geladen wurde.
- Einfachere Agentinstallation
    - Das PowerShell-Modul „Az\AzureRM“ ist zum Registrieren des Servers nicht mehr erforderlich, sodass die Installation einfacher und schnell durchgeführt werden kann.
- Sonstige Verbesserungen bei Leistung und Zuverlässigkeit

### <a name="evaluation-tool"></a>Auswertungstool
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungstool für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Tool ist ein Azure PowerShell-Cmdlet, das auf potenzielle Probleme mit Ihrem Dateisystem und Dataset prüft, z.B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Anweisungen zur Installation und Verwendung finden Sie im Planungshandbuch im Abschnitt [Auswertungstools](./storage-sync-files-planning.md#evaluation-cmdlet). 

### <a name="agent-installation-and-server-configuration"></a>Agent-Installation und Serverkonfiguration
Weitere Informationen zum Installieren und Konfigurieren des Azure File Sync-Agents mit Windows Server finden Sie unter [Planen einer Bereitstellung der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-planning.md) sowie unter [Bereitstellen von Azure File Sync (Vorschau)](storage-sync-files-deployment-guide.md).

- Das Agent-Installationspaket muss mit erhöhten Berechtigungen (Administratorberechtigungen) installiert werden.
- Der Agent wird für die Bereitstellungsoption „Nano Server“ nicht unterstützt.
- Der Agent wird nur unter Windows Server 2019, Windows Server 2016 und Windows Server 2012 R2 unterstützt.
- Der Agent benötigt mindestens 2 GiB Arbeitsspeicher. Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2.048 MiB Arbeitsspeicher konfiguriert werden.
- Der Dienst „Storage-Synchronisierungs-Agent“ (FileSyncSvc) unterstützt keine Serverendpunkte, die sich auf einem Volume befinden, für das das Verzeichnis „System Volume Information“ (SVI) komprimiert ist. Diese Konfiguration führt zu unerwarteten Ergebnissen.

### <a name="interoperability"></a>Interoperabilität
- Virenschutz, Sicherung und andere Anwendungen, die auf Tieringdateien zugreifen, können zu unerwünschten Rückrufen führen, wenn sie das Offlineattribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Weitere Informationen finden Sie unter [Problembehandlung bei der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-troubleshoot.md).
- FSRM-Dateiprüfungen (File Server Resource Manager, Ressourcen-Manager für Dateiserver) können zu Fehlern aufgrund einer endlosen Synchronisierung führen, wenn Dateien aufgrund der damit verbundenen Vorgänge blockiert werden.
- Die Ausführung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Der Azure-Dateisynchronisierungs-Agent sollte installiert werden, nachdem das Serverimage bereitgestellt und das Sysprep-Mini-Setup abgeschlossen wurde.

### <a name="sync-limitations"></a>Einschränkungen bei der Synchronisierung
Folgende Elemente werden nicht synchronisiert, aber der restliche Systembetrieb ist nicht beeinträchtigt:
- Dateien mit nicht unterstützten Zeichen. Eine Liste mit den Zeichen, die nicht unterstützt werden, finden Sie im [Leitfaden zur Problembehandlung](storage-sync-files-troubleshoot.md#handling-unsupported-characters).
- Dateien oder Verzeichnisse, die mit einem Punkt enden.
- Pfade, die länger als 2.048 Zeichen sind.
- Der DACL-Teil (besitzerverwaltete Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, sofern dieser größer als 2 KB ist. (Dieses Problem trifft nur zu, wenn Sie für ein einzelnes Element über mehr als ca. 40 Zugriffssteuerungseinträge verfügen.)
- Der SACL-Teil (System-Zugriffssteuerungsliste) einer Sicherheitsbeschreibung, die für die Überwachung verwendet wird.
- Erweiterte Attribute
- Alternative Datenströme
- Analysepunkte
- Feste Links
- Die Komprimierung (sofern für eine Serverdatei festgelegt) wird nicht beibehalten, wenn Änderungen mit der Datei von anderen Endpunkten synchronisiert werden.
- Mit EFS (oder einer anderen Benutzermodusverschlüsselung) verschlüsselte Dateien, die den Dienst am Lesen der Daten hindern.

    > [!Note]  
    > Bei der Azure-Dateisynchronisierung werden Daten während der Übertragung immer verschlüsselt. Ruhende Daten werden in Azure immer verschlüsselt.
 
### <a name="server-endpoint"></a>Serverendpunkt
- Ein Serverendpunkt kann nur auf einem NTFS-Volume erstellt werden. ReFS, FAT, FAT32 und andere Dateisysteme werden von der Azure-Dateisynchronisierung derzeit nicht unterstützt.
- Auf mehrstufige Dateien kann nicht mehr zugegriffen werden, wenn für die Dateien vor dem Löschen des Serverendpunkts kein Rückruf erfolgt. Erstellen Sie den Serverendpunkt neu, um den Zugriff auf die Dateien wiederherzustellen. Nachdem 30 Tage vergangen sind, seitdem der Serverendpunkt gelöscht wurde, oder wenn der Cloudendpunkt gelöscht wurde, sind mehrstufige Dateien, für die kein Rückruf durchgeführt wurde, nicht mehr verwendbar. Weitere Informationen finden Sie unter [Auf mehrstufige Dateien auf dem Server kann nach dem Löschen eines Serverendpunkts nicht zugegriffen werden](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.
- Failoverclustering wird nur mit Clusterdatenträgern, aber nicht mit freigegebenen Clustervolumes (Cluster Shared Volumes, CSVs) unterstützt.
- Ein Serverendpunkt kann nicht geschachtelt werden. Er kann auf demselben Volume parallel zu einem anderen Endpunkt vorhanden sein (Koexistenz).
- Speichern Sie keine Betriebssystem- oder Anwendungsauslagerungsdatei an einem Serverendpunkt-Standort.
- Der Servername im Portal wird bei Umbenennung des Servers nicht aktualisiert.

### <a name="cloud-endpoint"></a>Cloudendpunkt
- Die Azure-Dateisynchronisierung unterstützt direkte Änderungen an der Azure-Dateifreigabe. Allerdings müssen alle Änderungen, die Sie an der Azure-Dateifreigabe vornehmen, zuerst von einem Azure-Dateisynchronisierungsauftrag zum Erkennen von Änderungen erkannt werden. Ein Auftrag zum Erkennen von Änderungen für einen Cloudendpunkt wird einmal alle 24 Stunden gestartet. Um Dateien, die in der Azure-Dateifreigabe geändert wurden, sofort zu synchronisieren, kann das PowerShell-Cmdlet [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) verwendet werden, um die Erkennung von Änderungen in der Azure-Dateifreigabe manuell auszulösen. Darüber hinaus bewirken Änderungen, die über das REST-Protokoll an einer Azure-Dateifreigabe vorgenommen wurden, keine Aktualisierung der letzten SMB-Änderungszeit, und die Änderungen sind für eine Synchronisierung nicht zu sehen.
- Der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe oder ein anderes Abonnement im vorhandenen Azure AD-Mandanten verschoben werden. Wenn das Speicherkonto verschoben wird, müssen Sie dem Hybrid-Dateisynchronisierungsdienst Zugriff auf das Speicherkonto gewähren (siehe [Sicherstellen, dass die Azure-Dateisynchronisierung Zugriff auf das Speicherkonto besitzt](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Die Azure-Dateisynchronisierung unterstützt nicht das Verschieben des Abonnements in einen anderen Azure AD-Mandanten.

### <a name="cloud-tiering"></a>Cloudtiering
- Wenn eine Tieringdatei mit Robocopy an einen anderen Speicherort kopiert wird, ist die sich ergebende Datei keine Tieringdatei. Das Offlineattribut kann festgelegt werden, da dieses Attribut fälschlicherweise von Robocopy in Kopiervorgänge eingefügt wird.
- Verwenden Sie beim Kopieren der Dateien mit Robocopy die Option „/MIR“, um Dateizeitstempel beizubehalten. So wird sichergestellt, dass das Tiering für ältere Dateien früher als für die Dateien durchgeführt wird, auf die zuletzt zugegriffen wurde.