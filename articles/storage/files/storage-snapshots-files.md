---
title: Übersicht über Freigabemomentaufnahmen für Azure Files | Microsoft-Dokumentation
description: Eine Freigabemomentaufnahme ist eine schreibgeschützte Version einer Azure Files-Freigabe, die zu einem bestimmten Zeitpunkt erstellt wird, um die Freigabe zu sichern.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d83cf20c856d37d337f4eb22c30ee9b6823d096b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235820"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Übersicht über Freigabemomentaufnahmen für Azure Files 
Azure Files bietet die Möglichkeit, Freigabemomentaufnahmen von Dateifreigaben zu erstellen. Freigabemomentaufnahmen erfassen den Freigabestatus zum jeweiligen Zeitpunkt. In diesem Artikel erfahren Sie, welche Möglichkeiten Freigabemomentaufnahmen bieten und wie Sie in Ihrem speziellen Fall von ihnen profitieren können.

## <a name="when-to-use-share-snapshots"></a>Verwendung von Freigabemomentaufnahmen

### <a name="protection-against-application-error-and-data-corruption"></a>Schutz vor Anwendungsfehlern und Datenbeschädigung
Anwendungen, die Azure-Dateifreigaben verwenden, führen beispielsweise Schreib- und Lesevorgänge sowie Speicher-, Übertragungs- oder Verarbeitungsvorgänge durch. Eine falsche Anwendungskonfiguration oder ein Fehler in der Anwendung kann dazu führen, dass einige Datenblöcke versehentlich überschrieben oder beschädigt werden. Zum Schutz vor solchen Szenarien können Sie vor dem Bereitstellen von neuem Anwendungscode eine Freigabemomentaufnahme erstellen. Sollte die neue Bereitstellung einen Fehler enthalten, können Sie zu einer früheren Version Ihrer Daten in der Dateifreigabe zurückkehren. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Schutz vor versehentlichem Löschen oder unbeabsichtigten Änderungen
Angenommen, Sie arbeiten an einer Textdatei in einer Dateifreigabe. Nach dem Schließen der Textdatei können Sie Ihre Änderungen nicht mehr rückgängig machen. In einem solchen Fall müssen Sie eine frühere Version der Datei wiederherstellen. Mit Freigabemomentaufnahmen können Sie frühere Versionen der Datei wiederherstellen, wenn sie versehentlich umbenannt oder gelöscht wurde.

### <a name="general-backup-purposes"></a>Allgemeine Sicherung
Nach dem Erstellen einer Dateifreigabe können Sie regelmäßig eine Freigabemomentaufnahme der Dateifreigabe als Datensicherung erstellen. Regelmäßig erstellte Freigabemomentaufnahmen helfen bei der Verwaltung vorheriger Versionen von Daten, die für künftige Überprüfungsanforderungen oder zur Notfallwiederherstellung verwendet werden können.

## <a name="capabilities"></a>Funktionen
Eine Freigabemomentaufnahme ist eine schreibgeschützte Kopie Ihrer Daten zu einem bestimmten Zeitpunkt. Sie können Momentaufnahmen mithilfe der REST-API erstellen, löschen und verwalten. Die gleichen Funktionen stehen auch in der Clientbibliothek, über die Azure-Befehlszeilenschnittstelle und im Azure-Portal zur Verfügung. 

Sie können Momentaufnahmen einer Freigabe über die REST-API und über SMB anzeigen. Sie können die Liste mit den Versionen des Verzeichnisses oder der Datei abrufen und eine spezifische Version direkt als Laufwerk einbinden (nur für Windows verfügbar; siehe [Einschränkungen](#limits)). 

Erstellte Freigabemomentaufnahmen können gelesen, kopiert oder gelöscht, aber nicht geändert werden. Es ist nicht möglich, eine vollständige Momentaufnahme in ein anderes Speicherkonto zu kopieren. Die Dateien müssen einzeln kopiert werden – beispielsweise mit AzCopy oder einem anderen Kopiermechanismus.

Die Freigabemomentaufnahme-Funktion wird auf der Ebene der Dateifreigabe bereitgestellt. Abrufe finden auf der Ebene der jeweiligen Datei statt, um die Wiederherstellung einzelner Dateien zu ermöglichen. Sie können eine vollständige Dateifreigabe über SMB, die REST-API, das Portal, die Clientbibliothek oder über PowerShell/die Befehlszeilenschnittstelle wiederherstellen.

Eine Freigabemomentaufnahme einer Dateifreigabe ist mit ihrer Basisdateifreigabe identisch. Der einzige Unterschied besteht darin, dass an den Freigabe-URI ein **DateTime**-Wert angehängt wird, der den Erstellungszeitpunkt der Freigabemomentaufnahme angibt. Wenn ein Dateifreigabe-URI http://storagesample.core.file.windows.net/myshare ist, lautet der Freigabemomentaufnahmen-URI z.B. in etwa:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Freigabemomentaufnahmen werden beibehalten, bis sie explizit gelöscht werden. Eine Freigabemomentaufnahme kann die zugehörige Basisdateifreigabe nicht überdauern. Sie können alle einer Basisdateifreigabe zugeordneten Momentaufnahmen auflisten, um die aktuell vorhandenen Momentaufnahmen nachzuverfolgen. 

Wenn Sie eine Freigabemomentaufnahme einer Dateifreigabe erstellen, werden die Dateien in den Systemeigenschaften der Freigabe mit den gleichen Werten in die Freigabemomentaufnahme kopiert. Die Basisdateien und die Metadaten der Dateifreigabe werden ebenfalls in die Freigabemomentaufnahme kopiert, sofern Sie beim Erstellen keine separaten Metadaten für die Freigabemomentaufnahme angeben.

Wenn Sie eine Freigabe mit Freigabemomentaufnahmen löschen möchten, müssen Sie zunächst alle Freigabemomentaufnahmen löschen.

## <a name="space-usage"></a>Speicherplatz 
Freigabemomentaufnahmen sind in der Regel inkrementell. Es werden also nur die Daten gespeichert, die sich seit der letzten Freigabemomentaufnahme geändert haben. Dadurch reduziert sich die erforderliche Zeit zum Erstellen der Freigabemomentaufnahme, und zudem werden Speicherkosten eingespart. Alle Schreibvorgänge in das Objekt und alle Vorgänge zum Aktualisieren von Eigenschaften oder Metadaten werden als Inhaltsänderung betrachtet und in der Freigabemomentaufnahme gespeichert. 

Um Speicherplatz zu sparen, können Sie die Freigabemomentaufnahme für den Zeitraum mit den meisten Änderungen löschen.

Obwohl Freigabemomentaufnahmen inkrementell gespeichert werden, müssen Sie nur die jeweils letzte Freigabemomentaufnahme aufbewahren, um die Freigabe wiederherstellen zu können. Wenn Sie eine Freigabemomentaufnahme löschen, werden nur die für diese Freigabemomentaufnahme eindeutigen Daten entfernt. Aktive Momentaufnahmen enthalten sämtliche Informationen, die Sie benötigen, um die Daten (von dem Zeitpunkt, zu dem die Freigabemomentaufnahme erstellt wurde) zu durchsuchen und am ursprünglichen oder an einem anderen Speicherort wiederherzustellen. Die Wiederherstellung kann auf der Elementebene durchgeführt werden.

Momentaufnahmen zählen nicht zu Ihrem 5-TB-Freigabelimit. Es gibt keine Beschränkung für die Speicherplatzbelegung durch Freigabemomentaufnahmen. Die Beschränkungen für Speicherkonten gelten weiterhin.

## <a name="limits"></a>Einschränkungen
Derzeit sind in Azure Files maximal 200 Freigabemomentaufnahmen zulässig. Nach 200 Freigabemomentaufnahmen müssen ältere Freigabemomentaufnahmen gelöscht werden, damit neue Freigabemomentaufnahmen erstellt werden können. 

Die Anzahl gleichzeitiger Aufrufe zum Erstellen von Freigabemomentaufnahmen ist nicht beschränkt. Für den Speicherplatz, den Freigabemomentaufnahmen einer bestimmten Dateifreigabe belegen können, gelten keine Einschränkungen. 

Derzeit ist es nicht möglich, Freigabemomentaufnahmen unter Linux bereitzustellen. Das liegt daran, dass der Linux-SMB-Client das Bereitstellen von Momentaufnahmen nicht wie Windows unterstützt.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopieren von Daten aus einer Freigabemomentaufnahme zurück in eine Freigabe
Für Kopiervorgänge, die Dateien und Freigabemomentaufnahmen betreffen, gelten folgende Regeln:

Sie können einzelne Dateien in einer Dateifreigabemomentaufnahme in die zugehörige Basisfreigabe oder in jeden anderen Speicherort kopieren. Sie können eine frühere Version einer Datei oder die gesamte Dateifreigabe wiederherstellen, indem Sie die einzelnen Dateien nacheinander aus der Freigabemomentaufnahme kopieren. Die Freigabemomentaufnahme wird nicht zur Basisfreigabe heraufgestuft. 

Die Freigabemomentaufnahme bleibt nach dem Kopieren intakt. Die Basisdateifreigabe wird hingegen durch eine in der Freigabemomentaufnahme verfügbare Kopie der Daten überschrieben. Alle wiederhergestellten Dateien werden als Inhaltsänderung betrachtet.

Sie können eine Datei in einer Freigabemomentaufnahme in ein Ziel mit einem anderen Namen kopieren. Die entsprechende Zieldatei ist eine beschreibbare Datei und keine Freigabemomentaufnahme.

Wenn eine Zieldatei durch eine Kopie überschrieben wird, bleiben alle der ursprünglichen Zieldatei zugeordneten Freigabemomentaufnahmen erhalten.

## <a name="general-best-practices"></a>Allgemeine bewährte Methoden 
Bei der Verwendung von Infrastruktur in Azure sollten Sicherungen für die Datenwiederherstellung nach Möglichkeit automatisiert werden. Automatisierte Aktionen sind zuverlässiger als manuelle Vorgänge und verbessern so den Schutz und die Wiederherstellbarkeit von Daten. Für die Automatisierung können Sie die REST-API, das Client SDK oder Skripts verwenden.

Überlegen Sie vor der Bereitstellung des Freigabemomentaufnahmen-Planers sorgfältig, in welchem Intervall die Freigabemomentaufnahmen erstellt werden sollen und welche Aufbewahrungseinstellungen Sie festlegen möchten, um unnötige Kosten zu vermeiden.

Freigabemomentaufnahmen bieten nur Schutz auf der Dateiebene. Freigabemomentaufnahmen verhindern keine versehentlichen Löschvorgänge für eine Dateifreigabe oder ein Speicherkonto. Zum Schutz vor versehentlichem Löschen können Sie das Speicherkonto oder die Ressourcengruppe sperren.

## <a name="next-steps"></a>Nächste Schritte
- Verwenden von Azure-Dateifreigabemomentaufnahmen:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [BEFEHLSZEILENSCHNITTSTELLE (CLI)](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Häufig gestellte Fragen zu Azure Files](storage-files-faq.md#share-snapshots)
