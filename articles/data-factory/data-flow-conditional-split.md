---
title: 'Azure Data Factory Mapping Data Flow: Transformation für bedingtes Teilen'
description: Azure Data Factory-Datenflusstransformation für bedingtes Teilen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795634"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Transformation für bedingtes Teilen für den Mappingdatenfluss

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Toolbox für bedingtes Teilen](media/data-flow/conditionalsplit2.png "Toolbox für bedingtes Teilen")

Die Transformation für bedingtes Teilen kann Datenzeilen je nach Inhalt der Daten an verschiedene Datenströme routen. Die Implementierung der Transformation für bedingtes Teilen ist mit einer CASE-Entscheidungsstruktur in einer Programmiersprache vergleichbar. Die Transformation wertet Ausdrücke aus und leitet dann die Datenzeile basierend auf den Ergebnissen an den angegebenen Datenstrom weiter. Diese Transformation stellt zudem eine Standardausgabe bereit, damit eine Zeile, die mit keinem Ausdruck übereinstimmt, an die Standardausgabe weitergeleitet wird.

![Bedingtes Teilen](media/data-flow/conditionalsplit1.png "Optionen für bedingtes Teilen")

## <a name="multiple-paths"></a>Mehrere Pfade

Wenn Sie weitere Bedingungen hinzufügen möchten, wählen Sie im unteren Konfigurationsbereich die Option „Datenstrom hinzufügen“ aus, und klicken Sie in das Ausdrucks-Generator-Textfeld, um Ihren Ausdruck zu erstellen.

![Mehrfaches bedingtes Teilen](media/data-flow/conditionalsplit3.png "Mehrfaches bedingtes Teilen")

## <a name="next-steps"></a>Nächste Schritte

Allgemeine Datenflusstransformationen, die mit bedingtem Teilen verwendet werden: [Join-Transformation](data-flow-join.md), [Loopup-Transformation](data-flow-lookup.md), [Select-Transformation](data-flow-select.md)
