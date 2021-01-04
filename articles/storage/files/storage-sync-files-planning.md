---
title: Planen einer Azure-Dateisynchronisierungsbereitstellung | Microsoft-Dokumentation
description: Hier finden Sie Informationen zum Planen der Bereitstellung der Azure-Dateisynchronisierung. Azure-Dateisynchronisierung ist ein Dienst, mit dem Sie mehrere Azure-Dateifreigaben auf einem lokalen Computer unter Windows Server oder einer Cloud-VM zwischenspeichern können.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 32aa94c986c90b7bd46b9f5561021c34c0f142af
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492091"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planung für die Bereitstellung einer Azure-Dateisynchronisierung

:::row:::
    :::column:::
        [![Interview und Demo von Azure-Dateisynchronisierung. Klicken Sie hierauf, um die Wiedergabe zu starten.](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure-Dateisynchronisierung ist ein Dienst, mit dem Sie mehrere Azure-Dateifreigaben auf einem lokalen Computer mit Windows Server oder einer Cloud-VM zwischenspeichern können. 
        
        In diesem Artikel werden die Konzepte und Features von Azure-Dateisynchronisierung vorgestellt. Sobald Sie mit Azure-Dateisynchronisierung vertraut sind, finden Sie im [Bereitstellungsleitfaden für Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md) eine Anleitung, wie Sie diesen Dienst ausprobieren können.        
    :::column-end:::
:::row-end:::

Die Dateien werden in der Cloud in [Azure-Dateifreigaben](storage-files-introduction.md) gespeichert. Azure-Dateifreigaben können auf zwei Weisen genutzt werden: durch direktes Einbinden dieser serverlosen Azure-Dateifreigaben (SMB) oder durch lokales Zwischenspeichern von Azure-Dateifreigaben mithilfe von Azure-Dateisynchronisierung. Welche Bereitstellungsoption Sie wählen, ändert die Aspekte, die Sie beim Planen der Bereitstellung berücksichtigen müssen. 

- **Direktes Einbinden einer Azure-Dateifreigabe**: Da Azure Files SMB-Zugriff bietet, können Sie Azure-Dateifreigaben lokal oder in der Cloud mithilfe des standardmäßigen SMB-Clients einbinden, der unter Windows, macOS und Linux verfügbar ist. Da Azure-Dateifreigaben serverlos sind, erfordert die Bereitstellung für Produktionsszenarien keine Verwaltung eines Dateiservers oder NAS-Geräts. Dies bedeutet, dass Sie keine Softwarepatches anwenden oder physische Datenträger austauschen müssen. 

- **Lokales Zwischenspeichern von Azure-Dateifreigaben mit Azure-Dateisynchronisierung**: Die Azure-Dateisynchronisierung ermöglicht das Zentralisieren der Dateifreigaben Ihrer Organisation in Azure Files, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Die Azure-Dateisynchronisierung transformiert Ihre lokalen Windows Server-Computer in einen schnellen Cache für Ihre Azure-Dateifreigabe. 

## <a name="management-concepts"></a>Verwaltungskonzepte
Eine Azure-Dateisynchronisierungsbereitstellung umfasst drei grundlegende Verwaltungsobjekte:

- **Azure-Dateifreigabe**: Eine Azure-Dateifreigabe ist eine serverlose Clouddateifreigabe, die den *Cloudendpunkt* einer Azure-Dateisynchronisierungs-Synchronisierungsbeziehung bereitstellt. Auf Dateien in einer Azure-Dateifreigabe kann direkt mit SMB oder dem FileREST-Protokoll zugegriffen werden. Wir empfehlen Ihnen jedoch, auf die Dateien hauptsächlich über den Windows Server-Cache zuzugreifen, wenn die Azure-Dateifreigabe mit Azure-Dateisynchronisierung verwendet wird. Das liegt daran, dass Azure Files derzeit über keinen effizienten Mechanismus zur Erkennung von Änderungen verfügt, wie es bei Windows Server der Fall ist, sodass direkte Änderungen an der Azure-Dateifreigabe Zeit benötigen, um erneut an die Serverendpunkte weitergegeben zu werden.
- **Serverendpunkt**: Der Pfad auf dem Windows-Server, der mit einer Azure-Dateifreigabe synchronisiert wird. Dies kann ein bestimmter Ordner auf einem Volume oder das Stammverzeichnis des Volumes sein. Mehrere Serverendpunkte können auf demselben Volume vorhanden sein, wenn sich deren Namespaces nicht überschneiden.
- **Synchronisierungsgruppe**: Das-Objekt, das die Synchronisierungsbeziehung zwischen einem **Cloudendpunkt** oder einer Azure-Dateifreigabe und einem Serverendpunkt definiert. Endpunkte innerhalb einer Synchronisierungsgruppe bleiben miteinander synchron. Wenn Sie beispielsweise über zwei unterschiedliche Sätze von Dateien verfügen, die Sie mit der Azure-Dateisynchronisierung verwalten möchten, würden Sie zwei Synchronisierungsgruppen erstellen und beiden unterschiedliche Endpunkte hinzufügen.

### <a name="azure-file-share-management-concepts"></a>Konzepte der Azure-Dateifreigabeverwaltung
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Konzepte der Azure-Dateisynchronisierungsverwaltung
Synchronisierungsgruppen werden in **Speichersynchronisierungsdiensten** bereitgestellt. Dabei handelt es sich um Objekte der obersten Ebene, die Server für die Verwendung mit Azure-Dateisynchronisierung registrieren und die Synchronisierungsgruppenbeziehungen enthalten. Die Speichersynchronisierungsdienst-Ressource ist der Speicherkontoressource gleichgeordnet und kann in ähnlicher Weise in Azure-Ressourcengruppen bereitgestellt werden. Ein Speichersynchronisierungsdienst kann Synchronisierungsgruppen erstellen, die Azure-Dateifreigaben für mehrere Speicherkonten und mehrere registrierte Windows-Server enthalten.

Bevor Sie eine Synchronisierungsgruppe in einem Speichersynchronisierungsdienst erstellen können, müssen Sie zunächst einen Windows-Server beim Speichersynchronisierungsdienst registrieren. Dadurch wird ein **registriertes Serverobjekt** erstellt, das eine Vertrauensstellung zwischen Ihrem Server oder Cluster und dem Speichersynchronisierungsdienst darstellt. Um einen Speichersynchronisierungsdienst zu registrieren, müssen Sie zunächst den Azure-Dateisynchronisierungs-Agent auf dem Server installieren. Ein einzelner Server oder Cluster kann zu einem beliebigen Zeitpunkt jeweils nur bei einem Speichersynchronisierungsdienst registriert sein.

Eine Synchronisierungsgruppe enthält einen Cloudendpunkt oder eine Azure-Dateifreigabe und mindestens einen Serverendpunkt. Das Serverendpunktobjekt enthält die Einstellungen, die die **Cloudtieringfunktion** konfigurieren, die die Zwischenspeicherungsfunktion der Azure-Dateisynchronisierung bereitstellt. Um eine Synchronisierung mit einer Azure-Dateifreigabe durchführen zu können, muss sich das Speicherkonto, das die Azure-Dateifreigabe enthält, in der gleichen Azure-Region befinden wie der Speichersynchronisierungsdienst.

### <a name="management-guidance"></a>Leitfaden zur Verwaltung
Beim Bereitstellen von Azure-Dateisynchronisierung wird Folgendes empfohlen:

- Bereitstellen von Azure-Dateifreigaben im Verhältnis 1:1 mit Windows-Dateifreigaben. Das Serverendpunktobjekt bietet Ihnen ein hohes Maß an Flexibilität bei der Einrichtung der Synchronisierungstopologie auf der Serverseite der Synchronisierungsbeziehung. Um die Verwaltung zu vereinfachen, sollte der Pfad des Serverendpunkts mit dem Pfad der Windows-Dateifreigabe übereinstimmen. 

- Verwenden von so wenig Speichersynchronisierungsdiensten wie möglich. Dadurch wird die Verwaltung vereinfacht, wenn Synchronisierungsgruppen mehrere Serverendpunkte enthalten, da ein Windows-Server nur jeweils bei einem Speichersynchronisierungsdienst registriert sein kann. 

- Beachten Sie beim Bereitstellen von Azure-Dateifreigaben die IOPS-Einschränkungen eines Speicherkontos. Im Idealfall würden Sie Dateifreigaben Speicherkonten im Verhältnis 1:1 zuordnen. Dies ist jedoch ggf. aufgrund verschiedener Beschränkungen und Einschränkungen in Ihrer Organisation oder in Azure nicht immer möglich. Wenn es nicht möglich ist, dass nur eine Dateifreigabe in einem Speicherkonto bereitgestellt wird, sollten Sie berücksichtigen, welche Freigaben sehr aktiv sein werden und welche Freigaben weniger aktiv sind, um sicherzustellen, dass die aktivsten Dateifreigaben nicht zusammen im gleichen Speicherkonto gruppiert werden.

## <a name="windows-file-server-considerations"></a>Überlegungen zu Windows-Dateiservern
Um die Synchronisierungsfunktion unter Windows Server zu aktivieren, müssen Sie den herunterladbaren Azure-Dateisynchronisierungs-Agent installieren. Der Azure-Dateisynchronisierungs-Agent stellt zwei Hauptkomponenten bereit: `FileSyncSvc.exe`, den Windows-Hintergrunddienst, der für die Überwachung von Änderungen an den Serverendpunkten und das Initiieren von Synchronisierungssitzungen zuständig ist, und `StorageSync.sys`, einen Dateisystemfilter, der das Cloudtiering und schnelle Notfallwiederherstellung ermöglicht.  

### <a name="operating-system-requirements"></a>Betriebssystemanforderungen
Die Azure-Dateisynchronisierung wird mit den folgenden Versionen von Windows Server unterstützt:

| Version | Unterstützte SKUs | Unterstützte Bereitstellungsoptionen |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard und IoT | Vollständig und Core |
| Windows Server 2016 | Datacenter, Standard und Storage Server | Vollständig und Core |
| Windows Server 2012 R2 | Datacenter, Standard und Storage Server | Vollständig und Core |

Zukünftige Versionen von Windows Server werden hinzugefügt, sobald sie veröffentlicht werden.

> [!Important]  
> Es wird empfohlen, alle Servercomputer, die für die Azure-Dateisynchronisierung verwendet werden, mit den neuesten Updates von Windows Update auf dem neuesten Stand zu halten. 

### <a name="minimum-system-resources"></a>Mindestens erforderliche Systemressourcen
Für die Azure-Dateisynchronisierung ist entweder ein physischer oder ein virtueller Server mit mindestens einer CPU und mindestens 2 GiB Arbeitsspeicher erforderlich.

> [!Important]  
> Wenn der Server auf einem virtuellen Computer ausgeführt wird, für den dynamischer Arbeitsspeicher aktiviert ist, muss der virtuelle Computer mit mindestens 2048 MiB Arbeitsspeicher konfiguriert werden.

Bei den meisten Produktionsworkloads empfiehlt es sich nicht, einen Azure-Dateisynchronisierungsserver nur mit den Mindestanforderungen zu konfigurieren. Weitere Informationen finden Sie unter [Empfohlenen Systemressourcen](#recommended-system-resources).

### <a name="recommended-system-resources"></a>Empfohlene Systemressourcen
Ebenso wie jede Serverfunktion oder -anwendung werden die Systemressourcenanforderungen für die Azure-Dateisynchronisierung durch die Skalierung der Bereitstellung bestimmt. Größere Bereitstellungen auf einem Server erfordern umfangreichere Systemressourcen. Für die Azure-Dateisynchronisierung wird die Skalierung durch die Anzahl der Objekte an den Serverendpunkten und die Änderung für das Dataset bestimmt. Ein einzelner Server kann über mehrere Serverendpunkte in mehreren Synchronisierungsgruppen verfügen, und die Anzahl der in der folgenden Tabelle aufgeführten Objekte gilt für den vollständigen Namespace, an den ein Server angefügt ist. 

Beispiel: Serverendpunkt A mit 10 Millionen Objekten + Serverendpunkt B mit 10 Millionen Objekten = 20 Millionen Objekte. Für diese Beispielbereitstellung wird die Verwendung von 8 CPUs, 16 GiB Arbeitsspeicher für den stabilen Status und (falls möglich) 48 GiB Arbeitsspeicher für die Erstmigration empfohlen.
 
Namespacedaten werden aus Leistungsgründen im Arbeitsspeicher gespeichert. Aus diesem Grund benötigen größere Namespaces mehr Arbeitsspeicher, um eine gute Leistung zu gewährleisten, und umfangreiche Änderungen erfordern mehr CPU-Leistung für die Verarbeitung. 
 
In der folgenden Tabelle sind sowohl die Größe des Namespace als auch eine Konvertierung in die Kapazität typischer universeller Dateifreigaben angegeben. Dabei beträgt die durchschnittliche Dateigröße 512 KiB. Wenn Ihre Dateigrößen geringer sind, sollten Sie ggf. zusätzlichen Arbeitsspeicher für die gleiche Kapazität hinzufügen. Verwenden Sie als Grundlage für Ihre Arbeitsspeicherkonfiguration die Größe des Namespace.

| Namespacegröße: Dateien und Verzeichnisse (Millionen)  | Typische Kapazität (TiB)  | CPU-Kerne  | Empfohlener Arbeitsspeicher (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (Erstsynchronisierung) / 2 (normale Änderungen)      |
| 5        | 2.3     | 2        | 16 (Erstsynchronisierung) / 4 (normale Änderungen)    |
| 10       | 4,7     | 4        | 32 (Erstsynchronisierung) / 8 (normale Änderungen)   |
| 30       | 14,0    | 8        | 48 (Erstsynchronisierung) / 16 (normale Änderungen)   |
| 50       | 23,3    | 16       | 64 (Erstsynchronisierung) / 32 (normale Änderungen)  |
| 100*     | 46,6    | 32       | 128 (Erstsynchronisierung) / 32 (normale Änderungen)  |

\*Die Synchronisierung von mehr als 100 Millionen Dateien und Verzeichnisse wird zurzeit nicht empfohlen. Dies ist eine weiche Einschränkung basierend auf unseren getesteten Schwellenwerten. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Files](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> Die Erstsynchronisierung eines Namespace ist ein aufwendiger Vorgang. Es wird daher empfohlen, bis zum Abschluss der Erstsynchronisierung mehr Arbeitsspeicher zuzuordnen. Dies ist nicht zwingend erforderlich, kann jedoch die Erstsynchronisierung beschleunigen. 
> 
> Eine normale Änderung umfasst 0,5 % des Namespace pro Tag. Wenn bei Ihnen mehr Änderungen auftreten, sollten Sie weitere CPUs hinzufügen. 

- Ein lokal angeschlossenes Volume, das mit dem NTFS-Dateisystem formatiert ist

### <a name="evaluation-cmdlet"></a>Auswertungs-Cmdlet
Vor der Bereitstellung der Azure-Dateisynchronisierung müssen Sie mit dem Auswertungs-Cmdlet für die Azure-Dateisynchronisierung auswerten, ob Kompatibilität mit Ihrem System gegeben ist. Dieses Cmdlet prüft auf potenzielle Probleme mit Ihrem Dateisystem und Dataset, z. B. nicht unterstützte Zeichen oder eine nicht unterstützte Betriebssystemversion. Damit werden die meisten – aber nicht alle – der unten genannten Features überprüft. Es wird empfohlen, dass Sie den verbleibenden Teil dieses Abschnitts sorgfältig durchgehen, um sicherzustellen, dass Ihre Bereitstellung reibungslos verläuft. 

Sie können das Auswertungs-Cmdlet installieren, indem Sie das PowerShell-Modul „Az“ anhand der folgenden Anweisungen installieren: [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Verwendung  
Sie können das Auswertungstool in unterschiedlicher Weise aufrufen: Sie können die Systemprüfungen, die Datasetprüfungen oder beide Prüfungen durchführen. So führen Sie sowohl die System- als auch die Datasetprüfungen durch: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

So prüfen Sie nur Ihr Dataset:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
So testen Sie nur die Systemanforderungen:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
So zeigen Sie die Ergebnisse in einer CSV-Datei an:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Dateisystemkompatibilität
Azure-Dateisynchronisierung wird nur auf direkt zugeordneten NTFS-Volumes unterstützt. Direkt zugeordneter Speicher (DAS) unter Windows Server bedeutet, dass das Windows Server-Betriebssystem der Besitzer des Dateisystems ist. DAS kann durch physisches Anfügen von Datenträgern an den Dateiserver, das Anfügen von virtuellen Datenträgern an eine Dateiserver-VM (z. B. eine von Hyper-V gehostete VM) oder sogar durch iSCSI bereitgestellt werden.

Nur NTFS-Volumes werden unterstützt. ReFS, FAT, FAT32 und andere Dateisysteme werden nicht unterstützt.

In der folgenden Tabelle wird der Interop-Status der NTFS-Dateisystemfeatures gezeigt: 

| Funktion | Status der Unterstützung | Notizen |
|---------|----------------|-------|
| Zugriffssteuerungslisten (ACLs) | Vollständig unterstützt. | Diskrete Zugriffssteuerungslisten im Windows-Stil werden von der Azure-Dateisynchronisierung beibehalten und von Windows Server für Serverendpunkte erzwungen. ACLs können auch erzwungen werden, wenn die Azure-Dateifreigabe direkt eingebunden wird. Hierfür ist jedoch zusätzliche Konfiguration erforderlich. Weitere Informationen finden Sie im Abschnitt [Identität](#identity). |
| Feste Links | Ausgelassen | |
| Symbolische Links | Ausgelassen | |
| Bereitstellungspunkte | Teilweise unterstützt | Bereitstellungspunkte können der Stamm eines Serverendpunkts sein, aber sie werden übersprungen, wenn sie im Namespace des Serverendpunkts enthalten sind. |
| Verbindungen | Ausgelassen | Beispiel: Die Ordner „DfrsrPrivate“ und „DFSRoots“ des verteilten Dateisystems. |
| Analysepunkte | Ausgelassen | |
| NTFS-Komprimierung | Vollständig unterstützt. | |
| Sparsedateien | Vollständig unterstützt. | Sparsedateien werden synchronisiert (werden nicht blockiert), werden jedoch als vollständige Dateien in die Cloud synchronisiert. Wenn sich der Inhalt der Datei in der Cloud (oder auf einem anderen Server) ändert, handelt es sich bei der Datei nicht länger um eine Sparsedatei, sobald die Änderung heruntergeladen wird. |
| Alternative Datenströme (ADS) | Beibehalten, aber nicht synchronisiert | Beispielsweise werden Klassifizierungstags, die von der Dateiklassifizierungsinfrastruktur erstellt werden, nicht synchronisiert. Vorhandene Klassifizierungstags in Dateien auf den einzelnen Serverendpunkten bleiben hiervon unberührt. |

<a id="files-skipped"></a>Die Azure-Dateisynchronisierung überspringt auch bestimmte temporäre Dateien und Systemordner:

| Datei/Ordner | Hinweis |
|-|-|
| pagefile.sys | Systemspezifische Datei |
| Desktop.ini | Systemspezifische Datei |
| thumbs.db | Temporäre Datei für Miniaturansichten |
| ehthumbs.db | Temporäre Datei für Miniaturansichten von Medien |
| ~$\*.\* | Temporäre Office-Datei |
| \*.tmp | Temporäre Datei |
| \*.laccdb | Access-DB-Sperrdatei|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Interne Synchronisierungsdatei|
| \\System Volume Information | Volumespezifischer Ordner |
| $RECYCLE.BIN| Ordner |
| \\SyncShareState | Ordner für die Synchronisierung |

### <a name="failover-clustering"></a>Failoverclustering
Windows Server-Failoverclustering wird von der Azure-Dateisynchronisierung für die Bereitstellungsoption „Dateiserver zur allgemeinen Verwendung“ unterstützt. Failoverclustering wird auf einem „Dateiserver mit horizontaler Skalierung für Anwendungsdaten“ (SOFS) oder auf freigegebenen Volumes (CSV) nicht unterstützt.

> [!Note]  
> Der Azure-Dateisynchronisierungs-Agent muss auf jedem Knoten in einem Failovercluster installiert sein, damit die Synchronisierung ordnungsgemäß funktioniert.

### <a name="data-deduplication"></a>Datendeduplizierung
**Windows Server 2016 und Windows Server 2019**   
Die Datendeduplizierung wird auf Volumes mit aktiviertem Cloudtiering unter Windows Server 2016 und Windows Server 2019 unterstützt. Durch das Aktivieren der Datendeduplizierung auf einem Volume mit aktiviertem Cloudtiering können Sie weitere Dateien lokal zwischenspeichern, ohne mehr Speicher bereitstellen zu müssen. 

Wenn die Datendeduplizierung für ein Volume mit aktiviertem Cloudtiering aktiviert ist, wird das Tiering von für die Deduplizierung optimierten Dateien innerhalb des Serverendpunkt-Speicherorts ähnlich wie bei einer normalen Datei basierend auf den Richtlinieneinstellungen für Cloudtiering ausgeführt. Nachdem das Tiering der für die Deduplizierung optimierten Dateien ausgeführt wurde, wird der Garbage Collection-Auftrag „Datendeduplizierung“ automatisch ausgeführt, um Speicherplatz freizugeben, indem unnötige Blöcke entfernt werden, die nicht mehr von anderen Dateien auf dem Volume referenziert werden.

Beachten Sie, dass die Volumeeinsparungen nur für den Server gelten. Ihre Daten in der Azure-Dateifreigabe werden nicht dedupliziert.

> [!Note]  
> Zur Unterstützung der Datendeduplizierung auf Volumes mit aktiviertem Cloudtiering unter Windows Server 2019 müssen Windows-Update [KB4520062](https://support.microsoft.com/help/4520062) und mindestens Version 9.0.0.0 des Agents für die Azure-Dateisynchronisierung installiert sein.

**Windows Server 2012 R2**  
Datendeduplizierung und Cloudtiering auf demselben Volume unter Windows Server 2012 R2 werden von der Azure-Dateisynchronisierung nicht unterstützt. Wenn die Datendeduplizierung auf einem Volume aktiviert wird, muss Cloudtiering deaktiviert werden. 

**Hinweise**
- Wenn die Datendeduplizierung vor dem Azure-Dateisynchronisierungs-Agent installiert wird, ist ein Neustart erforderlich, damit die Datendeduplizierung und das Cloudtiering auf demselben Volume unterstützt werden.
- Wenn die Datendeduplizierung nach dem Cloudtiering auf einem Volume aktiviert wird, werden durch den ersten Auftrag zur Optimierung der Deduplizierung Dateien auf dem Volume optimiert, für die noch kein Tiering erfolgt ist. Dies hat folgende Auswirkungen auf das Cloudtiering:
    - Die Richtlinie für die Freigabe von Speicherplatz bewirkt, dass Dateien entsprechend dem freien Speicherplatz auf dem Volume, der anhand des Wärmebilds ermittelt wird, weiterhin umgelagert werden.
    - Die Datumsrichtlinie bewirkt, dass das Tiering für Dateien übersprungen wird, die eigentlich infrage gekommen wären. Dies liegt daran, dass durch den Auftrag zur Optimierung der Deduplizierung auf die Dateien zugegriffen wird.
- Wenn die Datei nicht bereits umgelagert wurde, wird das Cloudtiering mit Datumsrichtlinie bei laufenden Aufträgen zur Optimierung der Deduplizierung verzögert. Dies liegt an der [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume?view=win10-ps)-Einstellung der Datendeduplizierung. 
    - Beispiel: Wenn die MinimumFileAgeDays-Einstellung sieben Tage beträgt und die Datumsrichtlinie für das Cloudtiering 30 Tage vorsieht, werden Dateien gemäß der Datumsrichtlinie nach 37 Tagen umgelagert.
    - Hinweis: Sobald eine Datei von der Azure-Dateisynchronisierung umgelagert wurde, wird sie vom Auftrag zur Optimierung der Deduplizierung übersprungen.
- Wenn ein Server unter Windows Server 2012 R2 mit installiertem Azure-Dateisynchronisierungs-Agent auf Windows Server 2016 oder Windows Server 2019 aktualisiert wird, sind die folgenden Schritte erforderlich, damit die Datendeduplizierung und das Cloudtiering auf demselben Volume unterstützt werden:  
    - Deinstallieren Sie den Azure-Dateisynchronisierungs-Agent für Windows Server 2012 R2, und starten Sie den Server neu.
    - Laden Sie den Azure-Dateisynchronisierungs-Agent für die neue Serverbetriebssystemversion (Windows Server 2016 oder Windows Server 2019) herunter.
    - Installieren Sie den Azure-Dateisynchronisierungs-Agent, und starten Sie den Server neu.  
    
    Hinweis: Bei der Deinstallation und Neuinstallation des Agents werden die Konfigurationseinstellungen der Azure-Dateisynchronisierung auf dem Server beibehalten.

### <a name="distributed-file-system-dfs"></a>Verteiltes Dateisystem (Distributed File System, DFS)
Die Azure-Dateisynchronisierung unterstützt die Interoperabilität mit DFS-Namespaces (DFS-N) und DFS-Replikation (DFS-R).

**DFS-Namespaces (DFS-N)** : Die Azure-Dateisynchronisierung wird auf DFS-N-Servern vollständig unterstützt. Sie können den Azure-Dateisynchronisierungs-Agent auf einem oder mehreren DFS-N-Membern installieren, um Daten zwischen den Serverendpunkten und dem Cloudendpunkt zu synchronisieren. Weitere Informationen finden Sie unter [Übersicht über DFS-Namespaces](/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS-Replikation (DFS-R)** : Da DFS-R und die Azure-Dateisynchronisierung beides Replikationslösungen sind, empfehlen wir in den meisten Fällen das Ersetzen von DFS-R durch die Azure-Dateisynchronisierung. Es gibt aber mehrere Szenarien, in denen die parallele Nutzung von DFS-R und Azure-Dateisynchronisierung sinnvoll sein kann:

- Sie migrieren von einer DFS-R-Bereitstellung zu einer Azure-Dateisynchronisierungsbereitstellung. Weitere Informationen finden Sie unter [Migrieren einer DFS-R-Bereitstellung (DFS-Replikation) zur Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nicht jeder lokale Server, für den eine Kopie Ihrer Dateidaten erforderlich ist, kann direkt mit dem Internet verbunden werden.
- Bei Teilstrukturservern werden Daten auf einem einzelnen Hubserver zusammengefasst, für den Sie die Azure-Dateisynchronisierung verwenden möchten.

Für die parallele Nutzung von Azure-Dateisynchronisierung und DFS-R gilt Folgendes:

1. Das Azure-Dateisynchronisierungs-Cloudtiering muss auf Volumes mit replizierten DFS-R-Ordnern deaktiviert sein.
2. Serverendpunkte sollten nicht in schreibgeschützten DFS-R-Replikationsordnern konfiguriert werden.

Weitere Informationen finden Sie unter [Übersicht über DFS-Namespaces und DFS-Replikation](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11)).

### <a name="sysprep"></a>Sysprep
Die Verwendung von Sysprep auf einem Server, auf dem der Azure-Dateisynchronisierungs-Agent installiert ist, wird nicht unterstützt und kann zu unerwarteten Ergebnissen führen. Die Agent-Installation und Serverregistrierung sollte nach der Bereitstellung des Serverimages und nach Abschluss des Mini-Setups für Sysprep erfolgen.

### <a name="windows-search"></a>Windows-Suche
Wenn auf einem Serverendpunkt Cloudtiering aktiviert ist, werden Tieringdateien in der Windows-Suche übersprungen und nicht indiziert. Dateien ohne Tiering werden ordnungsgemäß indiziert.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andere Lösungen für hierarchisches Speichermanagement (HSM)
Es sollten keine anderen HSM-Lösungen in Verbindung mit der Azure-Dateisynchronisierung verwendet werden.

## <a name="identity"></a>Identity
Die Azure-Dateisynchronisierung funktioniert mit Ihrer AD-basierten Standardidentität, ohne dass ein besonderes Setup über das Einrichten der Synchronisierung hinaus erforderlich ist. Wenn Sie Azure-Dateisynchronisierung verwenden, besteht die allgemeine Erwartung darin, dass die meisten Zugriffe die Azure-Dateisynchronisierungs-Cacheserver und nicht die Azure-Dateifreigabe durchlaufen. Da sich die Serverendpunkte unter Windows Server befinden und Windows Server seit langer Zeit ACLs im AD- und Windows-Stil unterstützt, ist nichts weiter erforderlich, als sicherzustellen, dass die beim Speichersynchronisierungsdienst registrierten Windows-Dateiserver zur Domäne gehören. Die Azure-Dateisynchronisierung speichert ACLs für die Dateien in der Azure-Dateifreigabe und repliziert sie auf alle Serverendpunkte.

Auch wenn Änderungen, die direkt an der Azure-Dateifreigabe vorgenommen werden, für die Synchronisierung mit den Serverendpunkten in der Synchronisierungsgruppe länger dauern, können Sie dennoch sicherstellen, dass Sie Ihre AD-Berechtigungen für Ihre Dateifreigabe auch direkt in der Cloud erzwingen können. Zu diesem Zweck müssen Sie Ihr Speicherkonto mit Ihrem lokalen AD in die Domäne einbinden, ebenso wie die Windows-Dateiserver in die Domäne eingebunden werden. Weitere Informationen zum Domänenbeitritt Ihres Speicherkontos zu einem Active Directory im Besitz des Kunden finden Sie unter [Azure Files Active Directory: Übersicht](storage-files-active-directory-overview.md).

> [!Important]  
> Der Domänenbeitritt Ihres Speicherkontos zu Active Directory ist für die erfolgreiche Bereitstellung der Azure-Dateisynchronisierung nicht erforderlich. Dies ist ein streng optionaler Schritt, der es der Azure-Dateifreigabe ermöglicht, lokale ACLs zu erzwingen, wenn Benutzer die Azure-Dateifreigabe direkt einbinden.

## <a name="networking"></a>Netzwerk
Der Azure-Dateisynchronisierungs-Agent kommuniziert mit dem Speichersynchronisierungsdienst und der Azure-Dateifreigabe unter Verwendung des Azure-Dateisynchronisierungs-REST-Protokolls und des FileREST-Protokolls. Beide Protokolle verwenden immer HTTPS über Port 443. SMB wird nie zum Hochladen oder Herunterladen von Daten zwischen Ihrem Windows-Server und der Azure-Dateifreigabe verwendet. Da die meisten Unternehmen HTTPS-Datenverkehr über Port 443 als Voraussetzung für den Besuch der meisten Websites zulassen, ist für die Bereitstellung von Azure-Dateisynchronisierung normalerweise keine spezielle Netzwerkkonfiguration erforderlich.

Je nach den Richtlinien Ihres Unternehmens oder gesetzlichen Anforderungen müssen Sie möglicherweise eine restriktivere Kommunikation mit Azure erzwingen. Daher bietet die Azure-Dateisynchronisierung verschiedene Möglichkeiten, Netzwerke zu konfigurieren. Basierend auf Ihren Anforderungen können Sie Folgendes ausführen:

- Tunneln der Synchronisierung und des Datenverkehrs für Uploads/Downloads über ExpressRoute oder ein Azure-VPN. 
- Nutzen von Azure Files- und Azure-Netzwerkfeatures, z. B. von Dienstendpunkten und privaten Endpunkten.
- Konfigurieren der Azure-Dateisynchronisierung zur Unterstützung Ihres Proxys in Ihrer Umgebung.
- Drosseln der Netzwerkaktivität aus der Azure-Dateisynchronisierung.

Weitere Informationen zu Azure-Dateisynchronisierung und -Netzwerken finden Sie unter [Netzwerküberlegungen zur Azure-Dateisynchronisierung](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Verschlüsselung
Wenn Sie Azure-Dateisynchronisierung verwenden, sind drei verschiedene Verschlüsselungsebenen zu beachten: Verschlüsselung im ruhenden Speicher von Windows Server, Verschlüsselung während der Übertragung zwischen dem Azure-Dateisynchronisierungs-Agent und Azure und Verschlüsselung im Ruhezustand Ihrer Daten in der Azure-Dateifreigabe. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server-Verschlüsselung ruhender Daten 
Es gibt zwei Strategien für die Verschlüsselung von Daten unter Windows Server, die in der Regel mit der Azure-Dateisynchronisierung funktionieren: Verschlüsselung unterhalb des Dateisystems, sodass das Dateisystem und alle Daten, die in das Dateisystem geschrieben werden, verschlüsselt sind, und Verschlüsselung im Dateiformat selbst. Diese Methoden schließen sich nicht gegenseitig aus. Sie können bei Bedarf auch zusammen verwendet werden, da sich der Verschlüsselungszweck unterscheidet.

Um Verschlüsselung unterhalb des Dateisystems bereitzustellen, bietet Windows Server einen BitLocker-Posteingang. BitLocker ist für die Azure-Dateisynchronisierung vollständig transparent. Der Hauptgrund für die Verwendung eines Verschlüsselungsmechanismus wie BitLocker besteht darin, die physische Exfiltration von Daten durch Diebstahl der Datenträger aus Ihrem lokalen Rechenzentrum zu verhindern, sowie das Querladen eines nicht autorisierten Betriebssystems zu verhindern, um nicht autorisierte Lese- und Schreibvorgänge für Ihre Daten auszuführen. Weitere Informationen zu BitLocker finden Sie unter [BitLocker: Übersicht](/windows/security/information-protection/bitlocker/bitlocker-overview).

Produkte von Drittanbietern, die ähnlich wie BitLocker funktionieren, da sie sich unterhalb des NTFS-Volumes befinden, sollten auf ähnliche Weise vollständig transparent mit der Azure-Dateisynchronisierung zusammenarbeiten. 

Die andere Hauptmethode zum Verschlüsseln von Daten besteht darin, den Datenstrom der Datei zu verschlüsseln, wenn die Datei von der Anwendung gespeichert wird. Einige Anwendungen können dies nativ ausführen. Dies ist jedoch in der Regel nicht der Fall. Ein Beispiel für eine Methode zum Verschlüsseln des Datenstroms der Datei ist Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Der Hauptgrund für die Verwendung eines Verschlüsselungsmechanismus wie AIP/RMS besteht darin, die Exfiltration von Daten aus Ihrer Dateifreigabe durch Benutzer zu verhindern, die sie an andere Speicherorte kopieren (z. B. auf ein Flashlaufwerk) oder per E-Mail an eine nicht autorisierte Person senden. Wenn der Datenstrom einer Datei als Teil des Dateiformats verschlüsselt ist, wird diese Datei weiterhin auf der Azure-Dateifreigabe verschlüsselt sein. 

Azure-Dateisynchronisierung kann nicht mit NTFS EFS (NTFS Encrypted File System) oder Verschlüsselungslösungen von Drittanbietern zusammenarbeiten, die sich oberhalb des Dateisystems, aber unterhalb des Datenstroms der Datei befinden. 

### <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung

> [!NOTE]
> Die Unterstützung für TLS 1.0 und 1.1 im Azure-Dateisynchronisierungsdienst wird am 1. August 2020 entfernt. Alle unterstützten Versionen des Azure-Dateisynchronisierungs-Agents verwenden standardmäßig bereits TLS 1.2. Die Verwendung einer früheren Version von TLS kann auftreten, wenn TLS 1.2 auf dem Server deaktiviert wurde oder ein Proxy verwendet wird. Wenn Sie einen Proxy verwenden, sollten Sie die Proxykonfiguration überprüfen. Alle Regionen für den Azure-Dateisynchronisierungsdienst, die nach dem 01.05.2020 hinzugefügt werden, unterstützen ausschließlich TLS 1.2. Die Unterstützung für TLS 1.0 und 1.1 wird in den vorhandenen Regionen am 1. August 2020 entfernt.  Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

Der Azure-Dateisynchronisierungs-Agent kommuniziert mit dem Speichersynchronisierungsdienst und der Azure-Dateifreigabe unter Verwendung des Azure-Dateisynchronisierungs-REST-Protokolls und des FileREST-Protokolls. Beide Protokolle verwenden immer HTTPS über Port 443. Die Azure-Dateisynchronisierung sendet keine unverschlüsselten Anforderungen über HTTP. 

Azure-Speicherkonten enthalten einen Switch, der Verschlüsselung während der Übertragung erfordert. Dieser ist standardmäßig aktiviert. Auch wenn der Switch auf Speicherkontoebene deaktiviert ist (was bedeutet, dass unverschlüsselte Verbindungen mit Ihren Azure-Dateifreigaben möglich sind), verwendet die Azure-Dateisynchronisierung weiterhin nur verschlüsselte Kanäle für den Zugriff auf Ihre Dateifreigabe.

Der Hauptgrund für die Deaktivierung der Verschlüsselung während der Übertragung für ein Speicherkonto ist die Unterstützung einer älteren Anwendung, die unter einem älteren Betriebssystem wie z. B. Windows Server 2008 R2 oder einer älteren Linux-Distribution ausgeführt werden muss und direkt mit einer Azure-Dateifreigabe kommuniziert. Wenn die Legacyanwendung mit dem Windows Server-Cache der Dateifreigabe kommuniziert, hat das Umschalten dieser Einstellung keine Auswirkungen. 

Wir empfehlen dringend, sicherzustellen, dass die Verschlüsselung von Daten während der Übertragung aktiviert ist.

Weitere Informationen zur Verschlüsselung während der Übertragung finden Sie unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Verschlüsselung der Azure-Dateifreigabe im Ruhezustand
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Speicherebenen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Aktivieren von Standarddateifreigaben für bis zu 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regionale Verfügbarkeit
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Regionale Verfügbarkeit der Azure-Dateisynchronisierung
Azure-Dateisynchronisierung ist in den folgenden Regionen verfügbar:

| Azure Cloud | Geografische Region | Azure-Region | Regionscode |
|-------------|-------------------|--------------|-------------|
| Öffentlich | Asia | Asien, Osten | `eastasia` |
| Öffentlich | Asia | Asien, Südosten | `southeastasia` |
| Öffentlich | Australien | Australien (Osten) | `australiaeast` |
| Öffentlich | Australien | Australien, Südosten | `australiasoutheast` |
| Öffentlich | Brasilien | Brasilien Süd | `brazilsouth` |
| Öffentlich | Canada | Kanada, Mitte | `canadacentral` |
| Öffentlich | Canada | Kanada, Osten | `canadaeast` |
| Öffentlich | Europa | Nordeuropa | `northeurope` |
| Öffentlich | Europa | Europa, Westen | `westeurope` |
| Öffentlich | Frankreich | Frankreich, Mitte | `francecentral` |
| Öffentlich | Frankreich | Frankreich, Süden* | `francesouth` |
| Öffentlich | Indien | Indien, Mitte | `centralindia` |
| Öffentlich | Indien | Indien (Süden) | `southindia` |
| Öffentlich | Japan | Japan, Osten | `japaneast` |
| Öffentlich | Japan | Japan, Westen | `japanwest` |
| Öffentlich | Korea | Korea, Mitte | `koreacentral` |
| Öffentlich | Korea | Korea, Süden | `koreasouth` |
| Öffentlich | Südafrika | Südafrika, Norden | `southafricanorth` |
| Öffentlich | Südafrika | Südafrika, Westen* | `southafricawest` |
| Öffentlich | Vereinigte Arabische Emirate | VAE, Mitte* | `uaecentral` |
| Öffentlich | Vereinigte Arabische Emirate | Vereinigte Arabische Emirate, Norden | `uaenorth` |
| Öffentlich | UK | UK, Süden | `uksouth` |
| Öffentlich | UK | UK, Westen | `ukwest` |
| Öffentlich | US | USA (Mitte) | `centralus` |
| Öffentlich | US | East US | `eastus` |
| Öffentlich | US | USA (Ost) 2 | `eastus2` |
| Öffentlich | US | USA Nord Mitte | `northcentralus` |
| Öffentlich | US | USA Süd Mitte | `southcentralus` |
| Öffentlich | US | USA, Westen-Mitte | `westcentralus` |
| Öffentlich | US | USA (Westen) | `westus` |
| Öffentlich | US | USA, Westen 2 | `westus2` |
| US Gov | US | US Gov Arizona | `usgovarizona` |
| US Gov | US | US Gov Texas | `usgovtexas` |
| US Gov | US | US Government, Virginia | `usgovvirginia` |

Die Azure-Dateisynchronisierung unterstützt nur die Synchronisierung mit einer Azure-Dateifreigabe in der gleichen Region wie der Speichersynchronisierungsdienst.

Für die mit Sternchen gekennzeichneten Regionen müssen Sie sich an den Azure-Support wenden, um Zugriff auf Azure Storage in diesen Regionen anzufordern. Der Prozess ist in [diesem Dokument](https://azure.microsoft.com/global-infrastructure/geographies/) beschrieben.

## <a name="redundancy"></a>Redundanz
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Georedundanter und geozonenredundanter Speicher können ein manuelles Failover des Speichers in die sekundäre Region durchführen. Es wird empfohlen, so nicht außerhalb eines Notfalls vorzugehen, wenn Sie Azure-Dateisynchronisierung verwenden, weil die Wahrscheinlichkeit von Datenverlusten hoch ist. Bei einem Notfall, bei dem Sie ein manuelles Failover des Speichers initiieren möchten, müssen Sie eine Supportanfrage bei Microsoft öffnen, damit die Azure-Dateisynchronisierung die Synchronisierung mit dem sekundären Endpunkt fortsetzt.

## <a name="migration"></a>Migration
Wenn Sie über einen vorhandenen Windows-Dateiserver verfügen, kann die Azure-Dateisynchronisierung direkt installiert werden, ohne dass Daten auf einen neuen Server verschoben werden müssen. Wenn Sie die Migration zu einem neuen Windows-Dateiserver im Rahmen der Einführung von Azure-Dateisynchronisierung planen, gibt es mehrere mögliche Ansätze zum Verschieben von Daten:

- Erstellen Sie Serverendpunkte für die alte Dateifreigabe und die neue Dateifreigabe, und lassen Sie die Azure-Dateisynchronisierung die Daten zwischen den Serverendpunkten synchronisieren. Der Vorteil dieses Ansatzes besteht darin, dass es sehr einfach ist, den Speicher auf dem neuen Dateiserver zu überschreiben, da die Azure-Dateisynchronisierung Cloudtiering unterstützt. Wenn Sie bereit sind, können Sie Endbenutzer an die Dateifreigabe auf dem neuen Server übergeben und den Serverendpunkt der alten Dateifreigabe entfernen.

- Erstellen Sie einen Serverendpunkt nur auf dem neuen Dateiserver, und kopieren Sie Daten aus der alten Dateifreigabe mithilfe von `robocopy`. Abhängig von der Topologie der Dateifreigaben auf dem neuen Server (wie viele Freigaben auf den einzelnen Volumes vorhanden sind, wie leer die einzelnen Volumes sind usw.) müssen Sie möglicherweise vorübergehend zusätzlichen Speicher bereitstellen, da davon ausgegangen wird, dass `robocopy` von Ihrem alten Server auf Ihren neuen Server in Ihrem lokalen Rechenzentrum schneller ausgeführt wird, als die Azure-Dateisynchronisierung Daten in Azure verschiebt.

Es ist auch möglich, Data Box zum Migrieren von Daten zu einer Azure-Dateisynchronisierungsbereitstellung zu verwenden. Wenn Kunden Data Box zum Erfassen von Daten verwenden möchten, geschieht dies meist, weil sie glauben, dass dies die Geschwindigkeit ihrer Bereitstellung erhöht oder weil es bei Szenarien mit eingeschränkter Bandbreite hilfreich ist. Obwohl die Verwendung einer Data Box zum Erfassen von Daten in Ihrer Azure-Dateisynchronisierungsbereitstellung die Bandbreitenauslastung verringert, ist es wahrscheinlich in den meisten Szenarien schneller, einen Onlinedatenupload über eine der oben beschriebenen Methoden einzusetzen. Weitere Informationen zum Verwenden von Data Box zum Erfassen von Daten in Ihrer Azure-Dateisynchronisierungsbereitstellung finden Sie unter [Migrieren von Daten in die Azure-Dateisynchronisierung mit Azure Data Box](storage-sync-offline-data-transfer.md).

Ein häufiger Fehler, den Kunden beim Migrieren von Daten in ihre neue Azure-Dateisynchronisierungsbereitstellung machen, besteht darin, Daten direkt in die Azure-Dateifreigabe zu kopieren, anstatt auf ihre Windows-Dateiserver. Obwohl die Azure-Dateisynchronisierung alle neuen Dateien in der Azure-Dateifreigabe identifiziert und sie zurück mit Ihren Windows-Dateifreigaben synchronisiert, ist dies im allgemeinen deutlich langsamer als das Laden von Daten über den Windows-Dateiserver. Wenn Sie Azure-Kopiertools wie AzCopy nutzen, ist es wichtig, die neueste Version zu verwenden. Überprüfen Sie die [Tabelle zu den Dateikopiertools](storage-files-migration-overview.md#file-copy-tools), um einen Überblick über die Azure-Kopiertools zu erhalten und sicherzustellen, dass Sie alle wichtigen Metadaten einer Datei (z. B. Zeitstempel und ACLs) kopieren können.

## <a name="antivirus"></a>Virenschutz
Da für den Virenschutz Dateien auf bekannte Schadsoftware überprüft werden müssen, kann ein Virenschutzprodukt den Rückruf von Tieringdateien verursachen und damit zu hohen Ausgangsgebühren führen. Ab Version 4.0 der Azure-Dateisynchronisierung-Agents ist für mehrstufige Dateien das sichere Windows-Attribut „FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS“ festgelegt. Es empfiehlt es sich, bei Ihrem Softwareanbieter nachzufragen, wie die Lösung so konfiguriert werden kann, dass das Lesen von Dateien mit diesem festgelegten Attribut übersprungen wird (bei vielen ist dies automatisch der Fall). 

Die internen Virenschutzlösungen von Microsoft – Windows Defender und System Center Endpoint Protection (SCEP) – überspringen beide automatisch das Lesen von Dateien, für die dieses Attribut festgelegt ist. Wir haben sie getestet und ein kleineres Problem identifiziert: Wenn Sie einer bestehenden Synchronisierungsgruppe einen Server hinzufügen, werden Dateien, die kleiner als 800 Byte sind, auf dem neuen Server zurückgerufen (heruntergeladen). Diese Dateien verbleiben auf dem neuen Server und werden nicht in Speicherebenen aufgeteilt, da sie nicht die Größenanforderungen für das Tiering (> 64 KB) erfüllen.

> [!Note]  
> Anbieter von Antivirensoftware können die Kompatibilität zwischen ihrem Produkt und der Azure-Dateisynchronisierung mithilfe der [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322) überprüfen, die aus dem Microsoft Download Center heruntergeladen werden kann.

## <a name="backup"></a>Backup 
Wenn Cloudtiering aktiviert ist, sollten keine Lösungen verwendet werden, die den Serverendpunkt oder einen virtuellen Computer, auf dem sich der Serverendpunkt befindet, direkt sichern. Cloudtiering bewirkt, dass nur eine Teilmenge der Daten auf dem Serverendpunkt gespeichert wird, während sich das vollständige Dataset in Ihrer Azure-Dateifreigabe befindet. Abhängig von der verwendeten Sicherungslösung werden mehrstufige Dateien entweder übersprungen und nicht gesichert (da für sie das Attribut FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS festgelegt ist), oder sie werden auf den Datenträger zurückgerufen, sodass hohe Ausgangsgebühren anfallen. Es wird empfohlen, die Azure-Dateifreigabe direkt mithilfe einer Cloudsicherungslösung zu sichern. Weitere Informationen finden Sie unter [Informationen zum Sichern von Azure-Dateifreigaben](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json), oder wenden Sie sich an Ihren Sicherungsanbieter, um zu erfahren, ob dieser das Sichern von Azure-Dateifreigaben unterstützt.

Wenn Sie eine lokale Sicherungslösung bevorzugen, sollten die Sicherungen auf einem Server in der Synchronisierungsgruppe ausgeführt werden, auf dem das Cloudtiering deaktiviert ist. Wenn Sie eine Wiederherstellung durchführen, verwenden Sie die Wiederherstellungsoptionen auf Volume- oder Dateiebene. Mithilfe der Wiederherstellungsoption auf Dateiebene wiederhergestellte Dateien werden auf allen Endpunkten in der Synchronisierungsgruppe synchronisiert. Dabei werden vorhandene Dateien durch die aus der Sicherung wiederhergestellte Version ersetzt.  Bei der Wiederherstellung auf Volumeebene werden die neueren Dateiversionen in der Azure-Dateifreigabe oder auf anderen Serverendpunkten nicht ersetzt.

> [!Note]  
> Bare-Metal-Recovery (BMR) kann zu unerwarteten Ergebnissen führen und wird derzeit nicht unterstützt.

> [!Note]  
> Bei Version 9 des Azure-Dateisynchronisierungs-Agents werden VSS-Momentaufnahmen (einschließlich der Registerkarte „Vorherige Versionen“) jetzt auf Volumes mit aktiviertem Cloudtiering unterstützt. Allerdings müssen Sie die vorherige Versionskompatibilität über PowerShell aktivieren. [Weitere Informationen](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="azure-file-sync-agent-update-policy"></a>Updaterichtlinie für den Azure-Dateisynchronisierungs-Agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nächste Schritte
* [Berücksichtigen von Firewall- und Proxyeinstellungen](storage-sync-files-firewall-and-proxy.md)
* [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
* [Bereitstellen von Azure Files](storage-files-deployment-guide.md)
* [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
* [Überwachen der Azure-Dateisynchronisierung](storage-sync-files-monitoring.md)