---
title: Azure Storage-Explorer – Leitfaden zur Blobversionsverwaltung | Microsoft-Dokumentation
description: Leitfaden zur Blobversionsverwaltung für Azure Storage-Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783826"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure Storage-Explorer – Leitfaden zur Blobversionsverwaltung

Microsoft Azure Storage-Explorer ermöglicht einfachen Zugriff und Verwaltung von Blobversionen. Dieser Leitfaden soll Ihnen helfen zu verstehen, wie die Blobversionsverwaltung in Storage-Explorer funktioniert. Bevor Sie den Vorgang fortsetzen, sollten Sie mehr über [Blobversionsverwaltung](../blobs/versioning-overview.md) erfahren.

## <a name="terminology"></a>Begriff

Dieser Abschnitt enthält einige Definitionen, die Ihnen helfen sollen, die Verwendung bestimmter Begriffe in diesem Artikel zu verstehen.

- Vorläufiges Löschen: Ein alternatives Feature zum automatischen Datenschutz. Weitere Informationen zum vorläufigen Löschen finden Sie [hier](../blobs/soft-delete-blob-overview.md).
- Aktives Blob: Ein Blob oder eine Blobversion wird im aktiven Zustand erstellt. Sie können nur mit Blobs oder Blobversionen im aktiven Status arbeiten.
- Vorläufig gelöschtes Blob: Ein Blob oder eine Blobversion, das bzw. die als „vorläufig gelöscht“ gekennzeichnet wurde. Vorläufig gelöschte Blobs werden nur während ihres Aufbewahrungszeitraums beibehalten.
- Blobversion: Ein Blob, das bei aktivierter Blobversionsverwaltung erstellt wurde. Jeder Blobversion wird eine Versions-ID zugeordnet.
- Aktuelle Version: Eine Blobversion, die als die „aktuelle Version“ gekennzeichnet wurde.
- Vorgängerversion: Eine Blobversion, die nicht die aktuelle Version ist.
- Blob ohne Versionsangabe: Ein Blob, das bei deaktivierter Blobversionsverwaltung erstellt wurde. Ein Blob ohne Versionsangabe hat keine Versions-ID.

## <a name="view-blob-versions"></a>Anzeigen von Blobversionen

Storage-Explorer unterstützt vier verschiedene Ansichten zum Anzeigen von Blobs.

| Sicht | Aktive Blobs ohne Versionsangabe | Vorläufig gelöschte Blobs ohne Versionsangabe | Blobversionen |
| ---- | :----------: | :-----------: | :------------------: |
| Aktive Blobs | Ja | Nein | Nur aktuelle Version |
| Aktive Blobs und vorläufig gelöschte Blobs | Ja | Ja | Nur aktuelle Version |
| Aktive Blobs und Blobs ohne aktuelle Version | Ja | Nein | Aktuelle Version oder neueste aktive Version |
| Alle Blobs und Blobs ohne aktuelle Version | Ja | Ja | Aktuelle Version oder neueste Version |

### <a name="active-blobs"></a>Aktive Blobs

In dieser Ansicht zeigt Storage-Explorer Folgendes an:

- Aktive Blobs ohne Versionsangabe
- Aktuelle Versionen

### <a name="active-blobs-and-soft-deleted-blobs"></a>Aktive Blobs und vorläufig gelöschte Blobs

In dieser Ansicht zeigt Storage-Explorer Folgendes an:

- Aktive Blobs ohne Versionsangabe
- Vorläufig gelöschte Blobs ohne Versionsangabe
- Aktuelle Versionen.

### <a name="active-blobs-and-blobs-without-current-version"></a>Aktive Blobs und Blobs ohne aktuelle Version

In dieser Ansicht zeigt Storage-Explorer Folgendes an:

- Aktive Blobs ohne Versionsangabe
- Aktuelle Versionen
- Neueste aktive Vorgängerversionen. 

Bei Blobs, die über keine aktuelle Version, aber eine aktive Vorgängerversion verfügen, zeigt Storage-Explorer deren neueste aktive Vorgängerversion als Darstellung des jeweiligen Blobs an.

### <a name="all-blobs-and-blobs-without-current-version"></a>Alle Blobs und Blobs ohne aktuelle Version

In dieser Ansicht zeigt Storage-Explorer Folgendes an:

- Aktive Blobs ohne Versionsangabe
- Vorläufig gelöschte Blobs ohne Versionsangabe
- Aktuelle Versionen
- Neueste Vorgängerversionen. 

Bei Blobs, die über keine aktuelle Version verfügen, zeigt Storage-Explorer deren neueste Vorgängerversion als Darstellung des jeweiligen Blobs an.

> [!Note]
> Aufgrund einer Diensteinschränkung benötigt Storage-Explorer einige zusätzliche Verarbeitungsschritte, damit beim Auflisten von Blobversionen eine hierarchische Ansicht Ihrer virtuellen Verzeichnisse angezeigt wird. Das Auflisten von Blobs in den folgenden Ansichten dauert länger:
> 
> - Aktive Blobs und Blobs ohne aktuelle Version
> - Alle Blobs und Blobs ohne aktuelle Version

## <a name="manage-blob-versions"></a>Verwalten von Blobversionen

### <a name="view-versions-of-a-blob"></a>Anzeigen der Versionen eines Blobs

Storage-Explorer enthält den Befehl **Versionen verwalten** zum Anzeigen aller Versionen eines Blobs. Wenn Sie die Versionen eines Blobs anzeigen möchten, wählen Sie das gewünschte Blob und dann in der Symbolleiste oder im Kontextmenü **Verlauf verwalten &rarr; Versionen verwalten** aus.

### <a name="download-blob-versions"></a>Herunterladen von Blobversionen

Wenn Sie eine oder mehrere Blobversionen herunterladen möchten, wählen Sie die gewünschten Versionen und dann in der Symbolleiste oder im Kontextmenü **Herunterladen** aus.

Wenn Sie mehrere Versionen eines Blobs herunterladen, steht am Anfang der Namen der heruntergeladenen Dateien die jeweilige Versions-ID.

### <a name="delete-blob-versions"></a>Löschen von Blobversionen

Wenn Sie eine oder mehrere Blobversionen löschen möchten, wählen Sie die gewünschten Versionen und dann in der Symbolleiste oder im Kontextmenü **Löschen** aus.

Blobversionen unterliegen Ihrer Richtlinie für vorläufiges Löschen. Wenn vorläufiges Löschen aktiviert wurde, werden Blobversionen vorläufig gelöscht. Ein Sonderfall ist das Löschen einer aktuellen Version. Wenn Sie eine aktuelle Version löschen, wird sie automatisch zu einer aktiven Vorgängerversion.

### <a name="promote-blob-version"></a>Höherstufen einer Blobversion

Sie können den Inhalt eines Blobs wiederherstellen, indem Sie eine Vorgängerversion auf die aktuelle Version höherstufen. Wählen Sie die Blobversion, die Sie höherstufen möchten, und dann in der Symbolleiste oder im Kontextmenü **Version höherstufen** aus.

Blobs ohne Versionsangabe werden durch die höhergestufte Blobversion überschrieben. Vergewissern Sie sich, dass Sie diese Daten nicht mehr benötigen, oder sichern Sie selbst die Daten, bevor Sie den Vorgang bestätigen. Aktuelle Versionen werden automatisch zu Vorgängerversionen, damit Storage-Explorer nicht zu einer Bestätigung auffordert.

### <a name="undelete-blob-version"></a>Wiederherstellen einer Blobversion

Blobversionen können nicht einzeln wiederhergestellt werden. Sie müssen alle gleichzeitig wiederhergestellt werden. Wenn Sie alle Blobversionen eines Blobs wiederherstellen möchten, wählen Sie eine der Versionen des Blobs und dann in der Symbolleiste oder im Kontextmenü **Auswahl wiederherstellen** aus.

### <a name="change-access-tier-of-blob-versions"></a>Ändern der Zugriffsebene von Blobversionen

Jede Blobversion hat eine eigene Zugriffsebene. Wenn Sie die Zugriffsebene von Blobversionen ändern möchten, wählen Sie die dafür vorgesehenen Versionen und dann im Kontextmenü **Zugriffsebene ändern...** aus.

## <a name="see-also"></a>Weitere Informationen

* [Blobversionsverwaltung](../blobs/versioning-overview.md)
* [Vorläufiges Löschen für Blobs](../blobs/soft-delete-blob-overview.md)