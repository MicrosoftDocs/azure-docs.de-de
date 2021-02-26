---
title: Vereinigungstransformation im Zuordnungsdatenfluss
description: 'Azure Data Factory Mapping Data Flow: Transformation für neue Verzweigung'
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: e61c53cabc8f784ba9f0a3e78b5de01be134b0cd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367842"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Vereinigungstransformation im Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Union kombiniert mehrere Datenströme zu einem einzigen, wobei die SQL Union dieser Datenströme die neue Ausgabe der Union-Transformation ist. Alle Schemas aus jedem Eingabestream werden innerhalb Ihres Datenflusses kombiniert, ohne dass Sie einen Joinschlüssel benötigen.

Sie können die n-Anzahl von Streams in der Einstellungstabelle kombinieren, indem Sie neben jeder konfigurierten Zeile auf das Symbol „+“ klicken, einschließlich sowohl Quelldaten als auch Streams aus vorhandenen Transformationen in Ihrem Datenfluss.

In diesem kurzen Video sehen Sie eine exemplarische Vorgehensweise für eine Vereinigungstransformation in einem Zuordnungsdatenfluss in ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![Vereinigungstransformation](media/data-flow/union.png "Union")

In diesem Fall können Sie unterschiedliche Metadaten aus mehreren Quellen (in diesem Beispiel drei verschiedene Quelldateien) zusammenführen und zu einem einzigen Stream kombinieren:

![Vereinigungstransformation: Übersicht](media/data-flow/union111.png "Vereinigung 1")

Um dies zu erreichen, fügen Sie zusätzliche Zeilen in „Union Settings“ (Vereinigungseinstellungen) hinzu, indem Sie alle Quellen einbeziehen, die Sie hinzufügen möchten. Ein allgemeiner Verweis oder Joinschlüssel ist nicht notwendig:

![Vereinigungstransformation: Einstellungen](media/data-flow/unionsettings.png "Vereinigungseinstellungen")

Wenn Sie nach Ihrer Vereinigung eine Auswahltransformation festlegen, können Sie überlappende Felder oder Felder umbenennen, die in Quellen ohne Kopfzeilen nicht benannt wurden. Klicken Sie auf „Inspekt“ (Überprüfen), um die kombinierten Metadaten mit insgesamt 132 Spalten in diesem Beispiel aus drei verschiedenen Quellen anzuzeigen:

![Endgültige Vereinigungstransformation](media/data-flow/union333.png "Vereinigung 3")

## <a name="name-and-position"></a>Name und Position

Wenn Sie „Union by name“ (Vereinigung anhand des Namens) wählen, wird jeder Spaltenwert mit einem neuen verketteten Metadatenschema aus jeder Quelle in die entsprechende Spalte übernommen.

Wenn Sie "Union by position" (Vereinigung anhand der Position) wählen, wird jeder Spaltenwert aus jeder entsprechenden Quelle an der ursprünglichen Position abgelegt, was zu einem neuen kombinierten Stream aus Daten führt, bei dem die Daten aus jeder Quelle zum gleichen Stream hinzugefügt werden:

![Vereinigungsausgabe](media/data-flow/unionoutput.png "Vereinigungsausgabe")

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie ähnliche Transformationen, einschließlich [Join](data-flow-join.md) und [Exists](data-flow-exists.md).
