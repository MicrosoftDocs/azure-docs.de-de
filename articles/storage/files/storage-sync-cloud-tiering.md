---
title: Grundlegendes zum Cloudtiering der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Cloudtieringfeature der Azure-Dateisynchronisierung.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1851e9b2bb5ff86583228136dee977001cf0a3fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64714949"
---
# <a name="cloud-tiering-overview"></a>Übersicht über Cloudtiering
Cloudtiering ist ein optionales Feature der Azure-Dateisynchronisierung, bei dem häufig verwendete Dateien lokal auf dem Server zwischengespeichert werden, während alle anderen Dateien gemäß Richtlinieneinstellungen in Azure Files ausgelagert werden. Beim Tiering einer Datei ersetzt der Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys) die Datei lokal durch einen Zeiger oder Analysepunkt. Der Analysepunkt stellt eine URL zur Datei in Azure Files dar. Eine per Tiering ausgelagerte Datei weist sowohl das offline-Attribut als auch das in NTFS festgelegte FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS-Attribut auf, sodass Drittanwendungen Tieringdateien sicher identifizieren können.
 
Wenn ein Benutzer eine Tieringdatei öffnet, ruft die Azure-Dateisynchronisierung nahtlos die Dateidaten aus Azure Files ab, ohne dass der Benutzer wissen muss, dass die Datei eigentlich bei Azure gespeichert ist. 
 
 > [!Important]  
 > Cloudtiering wird nicht für Serverendpunkte auf den Windows-Systemvolumes unterstützt, und nur Dateien mit einer Größe von mehr als 64 KiB können in Azure Files ausgelagert werden.
    
Die Azure-Dateisynchronisierung unterstützt kein Tiering von Dateien mit einer Größe unter 64 KiB, da der Verwaltungsaufwand für das Tiering und das Zurückrufen so kleiner Dateien die Speichereinsparungen überwiegen würde.

 > [!Important]  
 > Um Dateien mit Tiering abzurufen, muss die Netzwerkbandbreite mindestens 1 Mbit/s betragen. Wenn die Netzwerkbandbreite kleiner als 1 Mbit/s ist, misslingt ggf. das Abrufen von Dateien mit einem Timeoutfehler.

## <a name="cloud-tiering-faq"></a>Cloudtiering – häufig gestellte Fragen

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Wie funktioniert Cloudtiering?
Der Systemfilter der Azure-Dateisynchronisierung erstellt ein „Wärmebild“ Ihres Namespace auf jedem Serverendpunkt. Er überwacht Zugriffe (Lese- und Schreibvorgänge) im Lauf der Zeit und weist dann jeder Datei basierend auf der Häufigkeit und Aktualität des Zugriffs eine Wärmebewertung zu. Eine häufig verwendete Datei, die vor Kurzem geöffnet wurde, wird als heiß angesehen, während eine Datei, die kaum verwendet wird und auf die seit einiger Zeit nicht zugegriffen wurde, als kalt angesehen wird. Wenn das Dateivolume auf einem Server den festgelegten Schwellenwert für freien Speicherplatz auf dem Volume überschreitet, werden die Dateien mit der niedrigsten Wärmebewertung nach Azure Files ausgelagert, bis der Anteil an freiem Speicherplatz wieder erfüllt ist.

In Version 4.0 und höher des Azure-Dateisynchronisierungs-Agents können Sie zusätzlich eine Datumsrichtlinie für jeden Serverendpunkt angeben, durch die alle Dateien per Tiering ausgelagert werden, auf die innerhalb einer festgelegten Anzahl von Tagen nicht zugegriffen wurde oder die innerhalb dieses Zeitraums nicht geändert wurden.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Wie funktioniert die Richtlinie für freien Speicherplatz auf dem Volume?
Der freie Speicherplatz auf dem Volume ist die Menge an freiem Speicherplatz auf dem Volume, auf dem sich ein Serverendpunkt befindet, die Sie reservieren möchten. Wenn beispielsweise der freie Speicherplatz auf einem Volume, das einen Serverendpunkt aufweist, auf 20 % festgelegt ist, werden bis zu 80 % des Speicherplatzes auf dem Volume von den zuletzt verwendeten Dateien belegt, und etwaige verbleibende Dateien, die nicht in den Speicherplatz passen, werden in Azure ausgelagert. Der freier Speicherplatz auf dem Volume gilt auf Volumeebene und nicht auf Ebene der einzelnen Verzeichnisse oder Synchronisierungsgruppen. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Wie funktioniert die Auslagerungsrichtlinie für den freien Speicherplatz auf dem Volume im Hinblick auf neue Serverendpunkte?
Wenn ein Serverendpunkt neu bereitgestellt und mit einer Azure-Dateifreigabe verbunden wird, ruft der Server zuerst den Namespace ab und dann die eigentlichen Dateien, bis der Schwellenwert für freien Speicherplatz auf dem Volume erreicht ist. Dieser Vorgang wird auch als schnelle Notfallwiederherstellung oder schnelle Namespacewiederherstellung bezeichnet.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Wie wird freier Speicherplatz auf dem Volume interpretiert, wenn ich über mehrere Serverendpunkte auf einem Volume verfüge?
Wenn auf einem Volume mehrere Serverendpunkte vorhanden sind, gilt als freier Speicherplatz auf dem Volume der größte freie Speicherplatz auf dem Volume, der für alle Serverendpunkte auf diesem Volume angegeben wurde. Die Dateien werden entsprechend ihrer Verwendungsmuster, aber unabhängig davon, zu welchem Serverendpunkt sie gehören, eingestuft. Wenn Sie z.B. auf einem Volume über die beiden Serverendpunkte Endpoint1 und Endpoint2 verfügen, wobei Endpoint1 über einen Schwellenwert für freien Speicherplatz auf dem Volume von 25 % und Endpoint2 von 50 % verfügt, beträgt der Schwellenwert für freien Speicherplatz das auf dem Volume für beide Serverendpunkte 50 %. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Wie funktioniert die datumsbasierte Tieringrichtlinie in Verbindung mit der Tieringrichtlinie für freien Speicherplatz auf dem Volume? 
Wenn Sie Cloudtiering auf einem Serverendpunkt aktivieren, legen Sie eine Richtlinie für freien Speicherplatz auf dem Volume fest. Diese Richtlinie hat immer Vorrang vor allen anderen Richtlinien, einschließlich der Datumsrichtlinie. Optional können Sie eine Datumsrichtlinie für jeden Serverendpunkt auf dem Volume aktivieren, sodass nur die Dateien lokal gespeichert werden, auf die innerhalb der festgelegten Anzahl von Tagen in der Richtlinie per Lese- oder Schreibvorgang zugegriffen wurde. Ältere Dateien werden ausgelagert. Denken Sie daran, dass die Richtlinie für freien Speicherplatz auf dem Volume immer Vorrang hat. Ist auf dem Volume nicht genügend freier Speicherplatz für die gemäß der Datumsrichtlinie zu speichernden Dateien verfügbar, lagert Azure File Sync die ältesten Dateien weiter per Tiering aus, bis der erforderliche Prozentsatz an freiem Speicherplatz auf dem Volume erreicht ist.

Angenommen, Sie haben eine datumsbasierte Tieringrichtlinie von 60 Tagen und eine Richtlinie für freien Speicherplatz auf dem Volume von 20 %. Wenn der freie Speicherplatz auf dem Volume nach dem Anwenden der Datumsrichtlinie unter 20 % liegt, setzt die Richtlinie für freien Speicherplatz auf dem Volume die Datumsrichtlinie außer Kraft. Da dadurch mehr Dateien ausgelagert werden, kann die auf dem Server beibehaltene Datenmenge von Daten für 60 Tage auf Daten für 45 Tage reduziert werden. Umgekehrt erzwingt diese Richtlinie auch dann das Tiering von Dateien außerhalb des Zeitbereichs, wenn der Schwellenwert für den freien Speicherplatz nicht erreicht wurde. Eine Datei, die 61 Tage alt ist, wird also selbst dann ausgelagert, wenn das Volume leer ist.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Wie lege ich eine geeignete Menge an freiem Speicherplatz auf dem Volume fest?
Die Menge der Daten, die lokal gespeichert werden sollten, wird durch einige Faktoren bestimmt: Ihre Bandbreite, das Zugriffsmuster Ihres Datasets und Ihr Budget. Wenn Sie über eine Verbindung mit geringer Bandbreite verfügen, sollten Sie möglicherweise einen größeren Teil Ihrer Daten lokal speichern, um sicherzustellen, dass nur minimale Verzögerungen für Ihre Benutzer auftreten. Andernfalls können Sie als Baseline die Änderungsrate in einem bestimmten Zeitraum verwenden. Wenn Ihnen z.B. bekannt ist, dass sich jeden Monat ungefähr 10 % Ihres 1-TB-Datasets ändern oder auf dieses aktiv zugegriffen wird, sollten Sie 100 GB lokal speichern, damit Sie nicht zu häufig Dateien abrufen. Wenn das Volume 2 TB groß ist, sollten Sie 5 % (100 GB) lokal speichern, d.h., die verbleibenden 95 % sind der Anteil des freien Speicherplatzes auf dem Volume. Wir empfehlen jedoch, einen Puffer hinzuzufügen, um Zeiträume höherer Änderungsraten zu berücksichtigen. Anders ausgedrückt, sollten Sie mit einem niedrigeren Anteil des freien Speicherplatzes auf dem Volume beginnen und diesen ggf. später noch einmal anpassen. 

Wenn Sie mehr Daten lokal speichern, fallen geringere Kosten für ausgehenden Datenverkehr an, da weniger Dateien aus Azure zurückgerufen werden, Sie müssen jedoch eine größere Menge von lokalem Speicher verwalten, was ebenfalls mit Kosten verbunden ist. Nachdem Sie eine Instanz der Azure-Dateisynchronisierung bereitgestellt haben, können Sie den ausgehenden Datenverkehr Ihres Speicherkontos einsehen, um abzuschätzen, ob Ihre Einstellungen für den freien Speicherplatz auf dem Volume für Ihre Zwecke geeignet sind. Ausgehend davon, dass das Speicherkonto nur Ihren Cloudendpunkt für die Azure-Dateisynchronisierung (d.h. Ihre Synchronisierungsfreigabe) enthält, bedeutet ein hoher ausgehender Datenverkehr, dass viele Dateien aus der Cloud zurückgerufen werden. In diesem Fall sollten Sie erwägen, den lokalen Cache zu erhöhen.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Ich habe einen neuen Serverendpunkt hinzugefügt. Wann werden meine Dateien auf diesem Server per Tiering ausgelagert?
In Version 4.0 und höher des Azure-Dateisynchronisierungs-Agents werden in die Azure-Dateifreigabe hochgeladene Dateien bei der Ausführung der nächsten Tieringsitzung entsprechend Ihren Richtlinien ausgelagert (d. h. einmal pro Stunde). Auf älteren Agents kann es bis zu 24 Stunden dauern, bis das Tiering stattfindet.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Woran erkenne ich, ob eine Datei per Tiering ausgelagert wurde?
Es gibt mehrere Möglichkeiten, um zu überprüfen, ob eine Datei per Tiering auf Ihre Azure-Dateifreigabe ausgelagert wurde:
    
   *  **Überprüfen Sie die Dateiattribute in der Datei.**
     Klicken Sie mit der rechten Maustaste auf eine Datei, navigieren Sie zu **Details**, und scrollen Sie dann nach unten zur Eigenschaft **Attribute**. In einer per Tiering ausgelagerten Datei sind folgende Attribute festgelegt:     
        
        | Attributbuchstabe | Attribut | Definition |
        |:----------------:|-----------|------------|
        | Eine Datei | Archivieren | Gibt an, dass die Datei durch Sicherungssoftware gesichert werden sollte. Dieses Attribut ist immer festgelegt, unabhängig davon, ob die Datei per Tiering ausgelagert oder vollständig auf dem Datenträger gespeichert wurde. |
        | P | Sparsedatei | Gibt an, dass die Datei eine „Sparsedatei“ (platzsparende Datei) ist. Eine platzsparende Datei ist ein spezieller Typ von Datei, der von NTFS zur effizienten Verwendung bereitgestellt wird, wenn die Datei im Datenträgerdatenstrom nahezu leer ist. Platzsparende Dateien werden von der Azure-Dateisynchronisierung genutzt, da eine Datei entweder vollständig per Tiering ausgelagert oder teilweise zurückgerufen wurde. Bei einer vollständig per Tiering ausgelagerten Datei wird der Dateidatenstrom in der Cloud gespeichert. Bei einer teilweise zurückgerufenen Datei befindet sich dieser Teil der Datei bereits auf dem Datenträger. Wenn eine Datei vollständig auf die Festplatte zurückgerufen wird, konvertiert die Azure-Dateisynchronisierung diese platzsparende Datei in eine normale Datei. |
        | L | Analysepunkt | Gibt an, dass die Datei über einen Analysepunkt verfügt. Ein Analysepunkt ist ein spezieller Zeiger für die Verwendung durch einen Dateisystemfilter. Die Azure-Dateisynchronisierung verwendet Analysepunkte, um den Speicherort für den Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys) zu definieren, an dem die Datei in der Cloud gespeichert ist. Dies erleichtert den nahtlosen Zugriff. Benutzer müssen nicht wissen, dass die Azure-Dateisynchronisierung verwendet wird oder wie der Zugriff auf die Datei in Ihrer Azure-Dateifreigabe möglich ist. Wenn eine Datei vollständig zurückgerufen wird, entfernt die Azure-Dateisynchronisierung den Analysepunkt aus der Datei. |
        | O | Offline | Gibt an, dass ein Teil des Dateiinhalts oder der gesamte Dateiinhalt nicht auf dem Datenträger gespeichert ist. Wenn eine Datei vollständig zurückgerufen wird, entfernt die Azure-Dateisynchronisierung dieses Attribut. |

        ![Dialogfeld „Eigenschaften“ einer Datei mit ausgewählter Registerkarte „Details“](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Sie können die Attribute für alle Dateien in einem Ordner anzeigen, indem das Feld **Attribute** der Tabellenanzeige des Datei-Explorers hinzugefügt wird. Klicken Sie hierzu mit der rechten Maustaste auf eine vorhandene Spalte (z.B. **Größe**). Wählen Sie anschließend **Mehr** und dann in der Dropdownliste **Attribute** aus.
        
   * **Verwenden Sie `fsutil`, um nach Analysepunkten in einer Datei zu suchen.**
       Wie für die vorherige Option beschrieben, verfügt eine per Tiering ausgelagerte Datei immer über einen festgelegten Analysepunkt. Ein Analysepunkt ist ein spezieller Zeiger für den Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys). Führen Sie das Hilfsprogramm `fsutil` an einer Eingabeaufforderung mit erhöhten Rechten oder in einem PowerShell-Fenster aus, um zu überprüfen, ob eine Datei einen Analysepunkt hat:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Wenn die Datei einen Analysepunkt enthält, sollte **Analysentagwert: 0x8000001e** angezeigt werden. Dieser Hexadezimalwert ist der Analysepunktwert, der der Azure-Dateisynchronisierung zuzuordnen ist. Die Ausgabe enthält außerdem die Analysedaten, die für den Pfad zu Ihrer Datei auf der Azure-Dateifreigabe stehen.

        > [!WARNING]  
        > Der Hilfsprogrammbefehl `fsutil reparsepoint` bietet außerdem die Möglichkeit, einen Analysepunkt zu löschen. Führen Sie diesen Befehl nur aus, wenn Sie vom Engineering-Team für die Azure-Dateisynchronisierung dazu aufgefordert werden. Bei der Ausführung dieses Befehls kann es zu Datenverlusten kommen. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Eine Datei, die ich verwenden möchte, wurde ausgelagert. Wie kann ich die Datei auf den Datenträger zurückrufen, um sie lokal zu verwenden?
Der einfachste Weg zum Zurückrufen einer Datei auf den Datenträger besteht darin, sie zu öffnen. Der Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys) lädt die Datei nahtlos von Ihrer Azure-Dateifreigabe herunter, ohne dass Sie irgendetwas tun müssen. Bei Dateitypen, die teilweise gelesen werden können (z.B. Multimedia- oder ZIP-Dateien), wird beim Öffnen einer Datei nicht die gesamte Datei heruntergeladen.

Sie können auch PowerShell nutzen, um für eine Datei das Zurückrufen zu erzwingen. Diese Option kann nützlich sein, wenn Sie mehrere Dateien auf einmal zurückrufen möchten, z.B. alle Dateien in einem Ordner. Öffnen Sie eine PowerShell-Sitzung auf dem Serverknoten, auf dem die Azure-Dateisynchronisierung installiert ist, und führen Sie anschließend die folgenden PowerShell-Befehle aus:
    
    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Warum stimmt die Eigenschaft *Größe auf Datenträger* einer Datei nach der Verwendung der Azure-Dateisynchronisierung nicht mit der Eigenschaft *Größe* überein? 
Der Windows-Datei-Explorer verwendet zwei Eigenschaften zur Darstellung einer Dateigröße: **Größe** und **Größe auf Festplatte**. Diese Eigenschaften unterscheiden sich leicht in ihrer Bedeutung. **Größe** steht für die vollständige Größe der Datei. **Größe auf Datenträger** steht für die Größe des Dateidatenstroms, der auf dem Datenträger gespeichert ist. Die Werte dieser Eigenschaften können aus unterschiedlichen Gründen variieren, z.B. aufgrund einer Komprimierung, einer Datendeduplizierung oder eines Cloudtiering mit der Azure-Dateisynchronisierung. Wenn eine Datei per Tiering auf eine Azure-Dateifreigabe ausgelagert wird, ist die Größe auf dem Datenträger gleich null, da der Dateidatenstrom nicht auf dem Datenträger, sondern in Ihrer Azure-Dateifreigabe gespeichert wird. Für eine Datei kann das Tiering auch teilweise durchgeführt werden (bzw. teilweise zurückgerufen werden). Bei einer teilweise per Tiering ausgelagerten Datei befindet sich ein Teil der Datei auf dem Datenträger. Dies kann vorkommen, wenn Dateien von Anwendungen, z.B. Multimedia-Playern oder ZIP-Hilfsprogrammen, teilweise gelesen werden. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Wie kann ich das Tiering einer Datei oder eines Verzeichnisses erzwingen?
Wenn das Cloudtiering-Feature aktiviert ist, wird beim Cloudtiering automatisch basierend auf dem letzten Zugriff und den Änderungszeiten gefiltert, um den auf dem Cloudendpunkt angegebenen freien Volumespeicherplatz zu erzielen. In einigen Fällen kann es auch erforderlich sein, das Tiering für eine Datei manuell zu erzwingen. Dies kann beispielsweise nützlich sein, wenn Sie eine große Datei speichern, die Sie für längere Zeit nicht mehr verwenden werden, und den freien Speicherplatz auf Ihrem Volume nun für andere Dateien und Ordner nutzen möchten. Sie können das Tiering mithilfe der folgenden PowerShell-Befehle erzwingen:

    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>Nächste Schritte
* [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
