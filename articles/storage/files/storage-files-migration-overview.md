---
title: Migrieren zu Azure-Dateifreigaben
description: Hier erfahren Sie mehr über Migrationen zu Azure-Dateifreigaben und finden entsprechende Migrationsleitfäden.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 995ae176a8eec58f8dc9522e6fac6fd78170014d
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628916"
---
# <a name="migrate-to-azure-file-shares"></a>Migrieren zu Azure-Dateifreigaben

In diesem Artikel werden die grundlegenden Aspekte einer Migration zu Azure-Dateifreigaben behandelt.

Dieser Artikel enthält Grundlagen zur Migration und eine Tabelle mit Migrationsleitfäden. Diese Leitfäden helfen Ihnen dabei, Ihre Dateien in Azure-Dateifreigaben zu verschieben. Sie sind nach dem Speicherort Ihrer Daten und dem Bereitstellungsmodell (nur Cloud oder Hybrid) organisiert, zu dem Sie wechseln.

## <a name="migration-basics"></a>Grundlagen der Migration

In Azure sind mehrere Cloudspeichertypen verfügbar. Ein grundlegender Aspekt der Dateimigration zu Azure ist das Ermitteln der richtigen Azure-Speicheroption für Ihre Daten.

[Azure-Dateifreigaben](storage-files-introduction.md) sind für allgemeine Dateidaten geeignet. Dazu gehören alle Daten, für die Sie eine lokale SMB- oder NFS-Freigabe verwenden. Mit der [Azure-Dateisynchronisierung](storage-sync-files-planning.md) können Sie den Inhalt mehrerer Azure-Dateifreigaben auf Windows-Servern lokal zwischenspeichern.

Für eine App, die derzeit auf einem lokalen Server ausgeführt wird, ist das Speichern von Dateien in einer Azure-Dateifreigabe möglicherweise eine gute Wahl. Sie können die App zu Azure verschieben und Azure-Dateifreigaben als freigegebenen Speicher verwenden. Sie können für dieses Szenario auch [Azure-Datenträger](../../virtual-machines/managed-disks-overview.md) in Erwägung ziehen.

Einige Cloud-Apps sind nicht von SMB oder lokalen Datenzugriff bzw. freigegebenen Zugriff abhängig. Für diese Apps ist Objektspeicher wie [Azure-Blobs](../blobs/storage-blobs-overview.md) oft die beste Wahl.

Der Schlüssel bei jeder Migration liegt in der Erfassung sämtlicher anwendbarer Dateigenauigkeit bei der Migration Ihrer Dateien vom aktuellen Speicherort zu Azure. Wie groß die durch die Azure-Speicheroption unterstützte Genauigkeit ist und wie viel Genauigkeit Ihr Szenario erfordert, sind ebenfalls Faktoren bei der Auswahl des richtigen Azure-Speichers. Allgemeine Dateidaten hängen gewöhnlich von Dateimetadaten ab. Bei App-Daten ist dies möglicherweise nicht der Fall.

Hier sind die beiden grundlegenden Komponenten einer Datei:

- **Datenstrom**: Der Datenstrom einer Datei speichert den Dateiinhalt.
- **Dateimetadaten**: Die Dateimetadaten weisen diese Unterkomponenten auf:
   * Dateiattribute, z. B. Schreibschutz
   * Dateiberechtigungen, als *NTFS-Berechtigungen* oder *Datei- und Ordner-ACLs* bezeichnet
   * Zeitstempel, insbesondere für Erstellung und letzte Änderung
   * Ein alternativer Datenstrom, der als Ort zum Speichern größerer Mengen nicht standardmäßiger Eigenschaften dient

Die Dateigenauigkeit einer Migration kann als die folgenden Fähigkeiten definiert werden:

- Speichern aller zutreffenden Dateiinformationen der Quelle
- Übertragen von Dateien mit dem Migrationstool
- Speichern von Dateien im Zielspeicher der Migration

Um sicherzustellen, dass die Migration reibungslos verläuft, ermitteln Sie [das optimale Kopiertool für Ihre Anforderungen](#migration-toolbox) und passen ein Speicherziel an Ihre Quelle an.

Unter Berücksichtigung der vorgenannten Informationen ist deutlich, dass als Zielspeicher für allgemeine Dateien in Azure [Azure-Dateifreigaben](storage-files-introduction.md) zu verwenden sind.

Anders als bei der Objektspeicherung in Azure-Blobs können Dateimetadaten in einer Azure-Dateifreigabe nativ gespeichert werden. Azure-Dateifreigaben wahren außerdem die Datei- und Ordnerhierarchie, die Attribute und die Berechtigungen. NTFS-Berechtigungen können für Dateien und Ordner gespeichert werden, da sie lokal vorhanden sind.

Ein Benutzer, der Active Directory als lokalen Domänencontroller verwendet, kann nativ auf eine Azure-Dateifreigabe zugreifen. Dies ist auch für einen Benutzer von Azure Active Directory Domain Services (Azure AD DS) möglich. Jeder verwendet seine aktuelle Identität, um Zugriff auf der Grundlage von Freigabeberechtigungen sowie Datei- und Ordner-ACLs zu erhalten. Dieses Verhalten ähnelt dem Herstellen einer Verbindung mit einer lokalen Dateifreigabe durch einen Benutzer.

Der alternative Datenstrom ist der primäre Aspekt der Dateigenauigkeit, der derzeit nicht für eine Datei in einer Azure-Dateifreigabe gespeichert werden kann. Bei Verwendung der Azure-Dateisynchronisierung bleibt er lokal erhalten.

Erfahren Sie mehr über die [Authentifizierung mit Azure AD](storage-files-identity-auth-active-directory-enable.md) und die [Authentifizierung mit Azure AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) für Azure-Dateifreigaben.

## <a name="migration-guides"></a>Migrationsleitfäden

In der folgenden Tabelle sind detaillierte Migrationsleitfäden aufgelistet.

Verwenden der Tabelle:

1. Suchen Sie die Zeile für das Quellsystem, in dem Ihre Dateien aktuell gespeichert sind.

1. Wählen Sie eines dieser Ziele aus:

   - Eine Hybridbereitstellung mit Azure-Dateisynchronisierung zum lokalen Zwischenspeichern des Inhalts von Azure-Dateifreigaben
   - Azure-Dateifreigaben in der Cloud

   Wählen Sie die Zielspalte aus, die Ihrer Auswahl entspricht.

1. In der Tabellenzelle am Schnittpunkt von Quelle und Ziel sind verfügbare Migrationsszenarios aufgelistet. Wählen Sie eines als direkten Link zum ausführlichen Migrationsleitfaden aus.

Für ein Szenario ohne Link wurde derzeit noch kein Migrationsleitfaden veröffentlicht. Prüfen Sie diese Tabelle gelegentlich auf Aktualisierungen. Neue Leitfäden werden veröffentlicht, sobald sie verfügbar sind.

| `Source` | Ziel: </br>Hybridbereitstellung | Ziel: </br>Reine Cloudbereitstellung |
|:---|:--|:--|
| | Toolkombination:| Toolkombination: |
| Windows Server 2012 R2 und höher | <ul><li>[Azure-Dateisynchronisierung ](storage-sync-files-deployment-guide.md)</li><li>[Azure-Dateisynchronisierung und Azure Data Box](storage-sync-offline-data-transfer.md)</li><li>[Azure-Dateisynchronisierung und vorab per Seeding hinzugefügte Dateien in der Cloud](storage-sync-offline-data-transfer.md#azure-file-sync-and-pre-seeded-files-in-the-cloud)</li><li>Azure-Dateisynchronisierung und Speichermigrationsdienst</li></ul> | <ul><li>Azure-Dateisynchronisierung</li><li>Azure-Dateisynchronisierung und Data Box</li><li>Azure-Dateisynchronisierung und Speichermigrationsdienst</li><li>RoboCopy</li></ul> |
| Windows Server 2012 und früher | <ul><li>Azure-Dateisynchronisierung und Data Box</li><li>Azure-Dateisynchronisierung und Speichermigrationsdienst</li></ul> | <ul><li>Azure-Dateisynchronisierung und Speichermigrationsdienst</li><li>RoboCopy</li></ul> |
| Network Attached Storage (NAS) | <ul><li>[Azure-Dateisynchronisierung und RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux oder Samba | <ul><li>[Azure-Dateisynchronisierung und RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 oder StorSimple Cloud Appliance 8600 | <ul><li>[Azure-Dateisynchronisierung und StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure-Dateisynchronisierung ](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Toolbox für die Migration

### <a name="file-copy-tools"></a>Tools zum Kopieren von Dateien

Es sind mehrere Tools zum Kopieren von Dateien von Microsoft und anderen Anbietern verfügbar. Um das richtige Tool für Ihr Migrationsszenario auszuwählen, müssen Sie diese grundlegenden Fragen stellen:

* Unterstützt das Tool den Quell- und Zielspeicherort für Ihre Dateikopie?

* Unterstützt das Tool Ihren Netzwerkpfad oder die verfügbaren Protokolle (z. B. REST, SMB oder NFS) zwischen den Quell- und Zielspeicherorten?

* Bewahrt das Kopiertool die erforderliche Dateigenauigkeit, die von Ihren Quell- und Zielspeicherorten unterstützt wird?

    In einigen Fällen unterstützt der Zielspeicher nicht dieselbe Genauigkeit wie die Quelle. Wenn der Zielspeicher für Ihre Anforderungen ausreicht, muss das Tool nur die Dateigenauigkeitsfunktionen des Ziels erfüllen.

* Weist das Tool Features auf, durch die es in Ihre Migrationsstrategie passt?

    Berücksichtigen Sie beispielsweise, ob Sie mit dem Tool Ihre Ausfallzeiten minimieren können.
    
    Wenn ein Tool eine Option zum Spiegeln einer Quelle an einem Ziel unterstützt, können Sie es häufig mehrmals mit derselben Quelle und demselben Ziel ausführen, während der Zugriff auf die Quelle möglich bleibt.

    Beim ersten Ausführen des Tools wird ein Großteil der Daten kopiert. Diese erste Ausführung kann eine Weile dauern. Es dauert häufig länger, als Sie die Datenquelle für Ihre Geschäftsprozesse offline nehmen möchten.

    Indem Sie eine Quelle an einem Ziel spiegeln (z. B. mit **robocopy /MIR**), können Sie das Tool mit derselben Quelle und demselben Ziel erneut ausführen. Die Ausführung läuft viel schneller ab, da nur Änderungen an der Quelle transportiert werden müssen, die nach der vorherigen Ausführung aufgetreten sind. Ein Kopiertool auf diese Weise erneut auszuführen, kann die Ausfallzeit erheblich verringern.

In der folgenden Tabelle sind Microsoft-Tools und deren aktuelle Eignung für Azure-Dateifreigaben klassifiziert:

| Empfohlen | Tool | Unterstützung von Azure-Dateifreigaben | Beibehaltung der Dateigenauigkeit |
| :-: | :-- | :---- | :---- |
|![Ja, empfohlen](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Unterstützt. Azure-Dateifreigaben können als Netzwerklaufwerke eingebunden werden. | Vollständige Genauigkeit* |
|![Ja, empfohlen](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure-Dateisynchronisierung | Nativ in Azure-Dateifreigaben integriert. | Vollständige Genauigkeit* |
|![Ja, empfohlen](media/storage-files-migration-overview/circle-green-checkmark.png)| Speichermigrationsdienst | Indirekt unterstützt. Azure-Dateifreigaben können als Netzwerklaufwerke auf SMS-Zielservern eingebunden werden. | Vollständige Genauigkeit* |
|![Ja, empfohlen](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy, Version 10.4 oder höher| Unterstützt. | Vollständige Genauigkeit* |
|![Ja, empfohlen](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Unterstützt. | DataBox unterstützt Metadaten jetzt vollständig. [Data Box kann auch in Kombination mit der Azure-Dateisynchronisierung verwendet werden](storage-sync-offline-data-transfer.md). |
|![Nicht vollständig empfohlen](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage-Explorer, Version 1.14 | Unterstützt. | Kopiert keine Zugriffssteuerungslisten (ACLs). Unterstützt Zeitstempel.  |
|![Nicht empfohlen](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Unterstützt. | Kopiert keine Metadaten. |
|||||

*\* Vollständige Genauigkeit: entspricht den Funktionen der Azure-Dateifreigabe oder übertrifft diese.*

### <a name="migration-helper-tools"></a>Hilfstools für die Migration

In diesem Abschnitt werden Tools beschrieben, die Ihnen beim Planen und Ausführen von Migrationsvorgängen helfen.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy der Microsoft Corporation

RoboCopy ist eines der für Dateimigrationsvorgänge am besten geeigneten Tools. Es ist Teil von Windows. Die [Hauptdokumentation zu RoboCopy](/windows-server/administration/windows-commands/robocopy) ist eine hilfreiche Quelle für die vielen Optionen dieses Tools.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize von der JAM Software GmbH

Die Azure-Dateisynchronisierung wird in erster Linie entsprechend der Anzahl von Elementen (Dateien und Ordner) skaliert und nicht entsprechend der Speichergesamtmenge. Mit dem Tool TreeSize können Sie die Anzahl von Elementen auf Ihren Windows Server-Volumes ermitteln.

Sie können mit dem Tool vor einer [Bereitstellung der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md) einen Ausblick erstellen. Sie können es auch verwenden, wenn das Cloudtiering nach der Bereitstellung aktiviert wird. In diesem Szenario sehen Sie die Anzahl der Elemente und die Verzeichnisse, die Ihren Servercache am meisten auslasten.

Die getestete Version des Tools ist Version 4.4.1. Es ist kompatibel mit Dateien mit Cloudtierung. Während des normalen Betriebs löst das Tool keinen Rückruf von Dateien mit Tiering aus.

## <a name="next-steps"></a>Nächste Schritte

1. Erstellen Sie einen Plan für die gewünschte Bereitstellung von Azure-Dateifreigaben (nur Cloud oder Hybrid).
1. Suchen Sie in der Liste der verfügbaren Migrationsleitfäden nach der detaillierten Anleitung, die Ihrer Quelle und Bereitstellung von Azure-Dateifreigaben entspricht.

Weitere Informationen zu den in diesem Artikel erwähnten Azure Files-Technologien finden Sie in den folgenden Artikeln:

* [Übersicht über Azure-Dateifreigaben](storage-files-introduction.md)
* [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
* [Azure-Dateisynchronisierung: Cloudtiering](storage-sync-cloud-tiering.md)