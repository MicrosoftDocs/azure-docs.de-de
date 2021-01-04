---
title: 'StorSimple 1200: Migration zur Azure-Dateisynchronisierung'
description: Erfahren Sie, wie Sie eine virtuelle Appliance der StorSimple 1200-Serie zur Azure-Dateisynchronisierung migrieren.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 78c7953ef6432d37542a7a8b06f226a07f2b701f
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630480"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200: Migration zur Azure-Dateisynchronisierung

Geräte der StorSimple 1200-Serie sind virtuelle Appliances, die in einem lokalen Rechenzentrum ausgeführt werden. Die Daten können von dieser Appliance zu einer Umgebung der Azure-Dateisynchronisierung migriert werden. Die Azure-Dateisynchronisierung ist der standardmäßige und strategisch langfristige Azure-Dienst, zu dem StorSimple-Geräte migriert werden können.

Die StorSimple 1200-Serie erreicht im Dezember 2022 das [Ende des Lebenszyklus](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series).  Es ist wichtig, dass Sie so bald wie möglich mit der Planung der Migration beginnen. Dieser Artikel enthält die erforderlichen Hintergrundinformationen und Migrationsschritte für eine erfolgreiche Migration zur Azure-Dateisynchronisierung. 

## <a name="azure-file-sync"></a>Azure-Dateisynchronisierung

> [!IMPORTANT]
> Microsoft ist bestrebt, Kunden bei der Migration zu unterstützen. Senden Sie eine E-Mail an AzureFilesMigration@microsoft.com, um einen angepassten Migrationsplan sowie Unterstützung bei der Migration zu erhalten.

Die Azure-Dateisynchronisierung ist ein Microsoft-Clouddienst, der auf zwei Hauptkomponenten basiert:

* Dateisynchronisierung und Cloudtiering.
* Dateifreigaben als nativer Speicher in Azure, auf den über verschiedene Protokolle wie SMB und Datei-REST zugegriffen werden kann. Eine Azure-Dateifreigabe ist vergleichbar mit einer Dateifreigabe auf einem Windows-Server, die Sie nativ als Netzwerklaufwerk einbinden können. Sie unterstützt wichtige Genauigkeitsaspekte der Dateiverwaltung wie Attribute, Berechtigungen und Zeitstempel. Anders als bei StorSimple ist keine Anwendung und kein Dienst erforderlich, um die in der Cloud gespeicherten Dateien und Ordner zu interpretieren. Der ideale und flexibelste Ansatz besteht darin, allgemeine Dateiserverdaten und einige Anwendungsdaten in der Cloud zu speichern.

In diesem Artikel werden hauptsächlich die Migrationsschritte behandelt. Wenn Sie vor der Migration mehr über die Azure-Dateisynchronisierung erfahren möchten, empfehlen wir die folgenden Artikel:

* [Azure-Dateisynchronisierung – Übersicht](./storage-sync-files-planning.md "Übersicht")
* [Azure-Dateisynchronisierung – Bereitstellungsleitfaden](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migrationsziele

Das Ziel ist, die Integrität der Produktionsdaten sowie deren Verfügbarkeit zu gewährleisten. Letztere erfordert minimale Ausfallzeiten, sodass sie in normalen Wartungsfenstern stattfinden oder diese nur geringfügig überschreiten.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200: Migrationspfad zur Azure-Dateisynchronisierung

Zum Ausführen eines Azure-Dateisynchronisierungs-Agents ist ein lokaler Windows-Server erforderlich. Auf dem Windows-Server muss mindestens Windows Server 2012 R2 ausgeführt werden, idealerweise jedoch Windows Server 2019.

Es gibt zahlreiche alternative Migrationspfade, und der Artikel würde zu lang werden, wenn wir alle dokumentieren und veranschaulichen würden, warum sie Risiken oder Nachteile gegenüber dem von uns in diesem Artikel als bewährte Methode empfohlenen Pfad darstellen.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Migrationspfad: Übersicht über die Schritte weiter unten in diesem Artikel":::

Die vorstehende Abbildung zeigt die Schritte, die den Abschnitten in diesem Artikel entsprechen.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Schritt 1: Bereitstellen Ihres lokalen Windows-Servers und des Speichers

1. Erstellen Sie einen Server mit Windows Server 2019 (oder mindestens 2012 R2) als virtuellen Computer oder physischen Server. Ein Windows Server-Failovercluster wird ebenfalls unterstützt.
2. Stellen Sie direkt angeschlossenen Speicher (DAS, im Gegensatz zum nicht unterstützten NAS) bereit, oder fügen Sie ihn hinzu. Die Größe des Windows Server-Speichers muss mindestens der verfügbaren Kapazität Ihrer virtuellen StorSimple 1200-Appliance entsprechen.

### <a name="step-2-configure-your-windows-server-storage"></a>Schritt 2: Konfigurieren Ihres Windows Server-Speichers

In diesem Schritt ordnen Sie die StorSimple-Speicherstruktur (Volumes und Freigaben) der Windows Server-Speicherstruktur zu.
Wenn Sie vorhaben, Änderungen an der Speicherstruktur vorzunehmen, d. h. an der Anzahl der Volumes, der Zuordnung von Datenordnern zu Volumes oder der Unterordnerstruktur oberhalb oder unterhalb der aktuellen SMB/NFS-Freigaben, müssen Sie diese Änderungen nun einplanen.
Änderungen an der Datei- und Ordnerstruktur nach dem Konfigurieren der Azure-Dateisynchronisierung sind mühsam und sollten vermieden werden.
In diesem Artikel wird davon ausgegangen, dass Sie eine 1:1-Zuordnung verwenden. Daher müssen Sie Ihre Zuordnungsänderungen berücksichtigen, wenn Sie die Schritte in diesem Artikel befolgen.

* Kein Teil Ihrer Produktionsdaten sollte sich auf dem Windows Server-Systemvolume befinden. Cloudtiering wird auf Systemvolumes nicht unterstützt. Dieses Feature ist jedoch für die Migration und den kontinuierlichen Betrieb als StorSimple-Ersatz erforderlich.
* Stellen Sie die gleiche Anzahl von Volumes auf Ihrem Windows-Server wie auf der virtuellen StorSimple 1200-Appliance bereit.
* Konfigurieren Sie die benötigten Windows Server-Rollen, -Features und -Einstellungen. Es wird empfohlen, Windows Server-Updates zu abonnieren, um Ihr Betriebssystem sicher und auf dem neuesten Stand zu halten. Ebenso empfiehlt es sich, Microsoft Update zu aktivieren, um Microsoft-Anwendungen auf dem neuesten Stand zu halten, einschließlich des Azure-Dateisynchronisierungs-Agents.
* Konfigurieren Sie keine Ordner oder Freigaben, bevor Sie die folgenden Schritte gelesen haben.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Schritt 3: Bereitstellen der ersten Azure-Dateisynchronisierungs-Cloudressource

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Schritt 4: Abgleichen der lokalen Volume- und Ordnerstruktur mit der Azure-Dateisynchronisierung und den Azure-Dateifreigaberessourcen

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Schritt 5: Bereitstellen von Azure-Dateifreigaben

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Schritt 6: Konfigurieren von Windows Server-Zielordnen

In den vorherigen Schritten haben Sie alle Aspekte in Erwägung gezogen, die die Komponenten Ihrer Synchronisierungstopologien festlegen. Jetzt ist es an der Zeit, den Server für den Empfang von Dateien für den Upload vorzubereiten.

Erstellen Sie **alle** Ordner, die jeweils mit einer eigenen Azure-Dateifreigabe synchronisiert werden.
Sie müssen unbedingt die Ordnerstruktur befolgen, die Sie zuvor dokumentiert haben. Wenn Sie beispielsweise mehrere lokale SMB-Freigaben gemeinsam in einer einzelnen Azure-Dateifreigabe synchronisieren möchten, müssen Sie diese in einem gemeinsamen Stammordner auf dem Volume platzieren. Erstellen Sie diesen Zielstammordner jetzt auf dem Volume.

Die Anzahl der bereitgestellten Azure-Dateifreigaben sollte mit der Anzahl der Ordner, die Sie in diesem Schritt erstellt haben, und der Anzahl der Volumes, die Sie auf Stammebene synchronisieren, übereinstimmen.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Schritt 7: Bereitstellen des Azure-Dateisynchronisierungs-Agents

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Schritt 8: Konfigurieren der Synchronisierung

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Aktivieren Sie unbedingt das Cloudtiering!** Dies ist erforderlich, wenn der lokale Server nicht genügend Speicherplatz aufweist, um die gesamten Daten im StorSimple-Cloudspeicher zu speichern. Legen Sie Ihre Tieringrichtlinie vorübergehend für die Migration auf 99 % freien Speicherplatz fest.

Wiederholen Sie die Schritte zum Erstellen einer Synchronisierungsgruppe und zum Hinzufügen des jeweiligen Serverordners als Serverendpunkt für alle Azure-Dateifreigaben/Serverspeicherorte, die für die Synchronisierung konfiguriert werden müssen.

### <a name="step-9-copy-your-files"></a>Schritt 9: Kopieren der Dateien

Der grundlegende Migrationsansatz besteht darin, mit RoboCopy eine Kopie Ihrer virtuellen StorSimple-Appliance auf Ihrem Windows-Server anzulegen und diese mit der Azure-Dateisynchronisierung auf Azure-Dateifreigaben zu synchronisieren.

Erstellen Sie die erste lokale Kopie in Ihrem Windows Server-Zielordner:

* Identifizieren Sie den ersten Speicherort auf Ihrer virtuellen StorSimple-Appliance.
* Identifizieren Sie den entsprechenden Ordner auf dem Windows-Server, auf dem die Azure-Dateisynchronisierung bereits konfiguriert wurde.
* Starten des Kopiervorgangs mit RoboCopy

Mit dem folgenden RoboCopy-Befehl werden Dateien aus dem StorSimple-Azure-Speicher in Ihren lokalen StorSimple-Speicher zurückgerufen und dann in den Windows Server-Zielordner verschoben. Der Windows-Server synchronisiert diesen Ordner mit den Azure-Dateifreigaben. Wenn das lokale Windows Server-Volume voll ist, beginnt das Cloudtiering von Dateien, die bereits erfolgreich synchronisiert wurden. Durch das Cloudtiering wird ausreichend Speicherplatz generiert, um mit dem Kopieren von der virtuellen StorSimple-Appliance fortzufahren. Einmal pro Stunde wird überprüft, was bereits im Cloudtiering synchronisiert wurde, und Speicherplatz freigegeben, um auf dem Volume einen freien Speicherplatz von 99 % zu erreichen.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Hintergrund:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Ermöglicht RoboCopy die Multithread-Ausführung. Der Standardwert ist 8, der Höchstwert 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Gibt den Status als UNICODE in die LOG-Datei aus (überschreibt vorhandenes Protokoll).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Ausgabe an das Konsolenfenster. Wird in Verbindung mit der Ausgabe in eine Protokolldatei verwendet.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Führt RoboCopy in dem Modus aus, den auch eine Sicherungsanwendung verwenden würde. Diese Option ermöglicht RoboCopy, Dateien zu verschieben, für die der aktuelle Benutzer keine Berechtigungen hat.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Ermöglicht, diesen RoboCopy-Befehl mehrmals auf demselben Ziel auszuführen. Dabei werden die bereits kopierten Daten erkannt und ausgelassen. Es werden nur Änderungen, Ergänzungen und „*Löschungen*“ verarbeitet, die seit der letzten Ausführung aufgetreten sind. Wenn der Befehl noch nicht ausgeführt wurde, wird nichts ausgelassen. Dies ist eine hervorragende Option für Quellspeicherorte, die weiterhin aktiv verwendet werden und Änderungen unterliegen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:Kopierflag[s]
   :::column-end:::
   :::column span="1":::
      Genauigkeit der Dateikopie (Standard: „/COPY:DAT“), Kopierflags: D = Daten, A = Attribute, T = Zeitstempel, S = Sicherheit = NTFS-ACLs, O = Eigentümerinformationen, U = Überprüfungsinformationen
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopieren aller Dateiinformationen (äquivalent zu „/COPY:DATSOU“)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:Kopierflag[s]
   :::column-end:::
   :::column span="1":::
      Genauigkeit für Kopien von Verzeichnissen (Standard: „/DCOPY:DA“), Kopierflags: D = Daten, A = Attribute, T = Zeitstempel
   :::column-end:::
:::row-end:::

Wenn Sie den RoboCopy-Befehl zum ersten Mal ausführen, greifen Ihre Benutzer und Anwendungen weiterhin auf die StorSimple-Dateien und -Ordner zu und ändern sie möglicherweise. Es kann vorkommen, dass RoboCopy ein Verzeichnis verarbeitet und mit dem nächsten fortfährt und dann ein Benutzer am Quellspeicherort (StorSimple) eine Datei hinzufügt, ändert oder löscht. Diese wird dann während dieser aktuellen RoboCopy-Ausführung nicht verarbeitet. Das geht.

Der erste Schritt besteht darin, den Großteil der Daten zurück in die lokale Umgebung auf Ihren Windows-Server zu verschieben und über die Azure-Dateisynchronisierung eine Sicherung in der Cloud durchzuführen. Dies kann einige Zeit in Anspruch nehmen, die von folgenden Faktoren abhängig ist:

* Downloadbandbreite
* Rückrufgeschwindigkeit des StorSimple-Clouddiensts
* Uploadbandbreite
* Anzahl von Elementen (Dateien und Ordner), die von einem der Dienste verarbeitet werden müssen

Nachdem die erste Ausführung beendet wurde, führen Sie den Befehl erneut aus.

Beim zweiten Mal wird der Vorgang schneller abgeschlossen, da nur Änderungen, die seit der letzten Ausführung aufgetreten sind, übertragen werden müssen. Diese Änderungen befinden sich wahrscheinlich bereits lokal bei StorSimple, da sie vor Kurzem aufgetreten sind. Dies reduziert die Zeit weiter, da der Bedarf für einen Rückruf aus der Cloud verringert wird. Während dieser zweiten Ausführung können sich auch neue Änderungen ansammeln.

Wiederholen Sie diesen Vorgang, bis Sie der Auffassung sind, dass die bis zum Abschluss benötigte Zeit eine akzeptable Ausfallzeit darstellt.

Wenn Sie die Ausfallzeit als akzeptabel betrachten und bereit sind, den StorSimple-Speicherort offline zu schalten, ist jetzt der Zeitpunkt dafür: Entfernen Sie beispielsweise die SMB-Freigabe, damit kein Benutzer mehr auf den Ordner zugreifen kann, oder nehmen Sie andere geeignete Schritte vor, die das Ändern von Inhalten in diesem Ordner auf der StorSimple-Appliance verhindern.

Führen Sie einen letzten RoboCopy-Durchgang aus. Dadurch werden alle Änderungen übernommen, die möglicherweise ausgelassen wurden.
Wie lange dieser letzte Schritt dauert, hängt von der Geschwindigkeit des RoboCopy-Scans ab. Sie können die Zeit (gleich der Ausfallzeit) schätzen, indem Sie messen, wie lange die vorherige Ausführung gedauert hat.

Erstellen Sie eine Freigabe für den Windows Server-Ordner, und passen Sie Ihre DFS-N-Bereitstellung ggf. so an, dass sie auf diese zeigt. Stellen Sie sicher, dass Sie die gleichen Berechtigungen auf Freigabeebene wie auf Ihrer StorSimple-SMB-Freigabe festlegen.

Sie haben die Migration einer Freigabe/Gruppe von Freigaben zu einem gemeinsamen Stamm oder Volume abgeschlossen. (Dies ist davon abhängig, was Sie zugeordnet haben und was in dieselbe Azure-Dateifreigabe kopiert werden sollte.)

Sie können einige dieser Kopiervorgänge parallel ausführen. Es wird empfohlen, jeweils eine Azure-Dateifreigabe auf einmal zu verarbeiten.

> [!WARNING]
> Nachdem Sie alle Daten von StorSimple auf den Windows-Server verschoben haben und die Migration abgeschlossen ist, gehen Sie wie folgt vor: Kehren Sie zu ***allen** _ Synchronisierungsgruppen im Azure-Portal zurück, und passen Sie den Wert für den freien Speicherplatz auf dem Cloudtieringvolume auf einen Wert an, der für die Cachenutzung besser geeignet ist, z. B. 20 %. 

Die Richtlinie für den freien Speicherplatz für das Cloudtiering wirkt sich auf eine Volumeebene aus, von der aus potenziell mehrere Serverendpunkte synchronisiert werden. Wenn Sie vergessen, den freien Speicherplatz auf einem Serverendpunkt anzupassen, wird für die Synchronisierung weiterhin die restriktivste Regel angewandt, und es wird versucht, 99 % freien Speicherplatz beizubehalten. Der lokale Cache funktioniert in diesem Fall nicht wie erwartet. Dies ist nur dann erstrebenswert, wenn Sie lediglich den Namespace für ein Volume erhalten möchten, das ausschließlich selten genutzte Archivdaten enthält.

## <a name="troubleshoot"></a>Problembehandlung

Das häufigste Problem, auf das Sie stoßen können, besteht darin, dass der RoboCopy-Befehl mit dem Fehler _„Volume voll“* auf Windows Server-Seite beendet wird. Wenn dies der Fall ist, ist die Downloadgeschwindigkeit wahrscheinlich höher als die Uploadgeschwindigkeit. Das Cloudtiering wird einmal stündlich eingesetzt, um Inhalte vom lokalen Windows Server-Datenträger abzurufen, die bereits synchronisiert wurden.

Warten Sie, bis durch den Synchronisierungsvorgang und das Cloudtiering Speicherplatz freigegeben wurde. Sie können dies im Datei-Explorer auf Ihrem Windows-Server beobachten.

Wenn Ihr Windows-Server ausreichende Kapazität aufweist, wird das Problem durch erneutes Ausführen des Befehls behoben. Falls Sie auf diese Situation stoßen, treten keine Schäden auf, und Sie können einfach fortfahren. Der zusätzliche Aufwand durch das erneute Ausführen des Befehls ist die einzige Folge.

Sie können auch auf andere Probleme mit der Azure-Dateisynchronisierung stoßen.
Dies ist sehr unwahrscheinlich, aber falls es geschieht, lesen Sie das **Handbuch zur Problembehandlung bei der Azure-Dateisynchronisierung**.

## <a name="relevant-links"></a>Relevante Links

Inhalte für die Migration:

* [Migrationshandbuch für die StorSimple 8000-Serie](storage-files-migration-storsimple-8000.md)

Inhalte für die Azure-Dateisynchronisierung:

* [AFS-Übersicht](./storage-sync-files-planning.md)
* [AFS-Bereitstellungshandbuch](storage-files-deployment-guide.md)
* [AFS-Problembehandlung](storage-sync-files-troubleshoot.md)