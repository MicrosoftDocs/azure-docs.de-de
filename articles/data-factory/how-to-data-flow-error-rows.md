---
title: Beheben von fehlerhaften Zeilen mit Zuordnungsdatenflüssen in Azure Data Factory
description: Erfahren Sie, wie Sie SQL-Kürzungsfehler in Azure Data Factory mithilfe von Zuordnungsdatenflüssen beheben.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: c8b0ae7058aecc1813d720a3fbb2a1a1f967cf40
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352599"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Beheben von SQL-Kürzungsfehlern in Zeilen in Data Factory-Zuordnungsdatenflüssen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Als gängiges Szenario in Data Factory bei der Verwendung von Zuordnungsdatenflüssen werden die transformierten Daten in eine Datenbank in Azure SQL-Datenbank geschrieben. In diesem Szenario muss eine mögliche Spaltenkürzung als häufige Fehlerbedingung verhindert werden.

Es gibt zwei Hauptmethoden, um Fehler beim Schreiben von Daten in die Datenbanksenke in ADF-Datenflüssen ordnungsgemäß zu behandeln:

* Legen Sie beim Verarbeiten von Datenbankdaten die [Fehlerzeilenbehandlung](./connector-azure-sql-database.md#error-row-handling) der Senke auf „Bei Fehler fortsetzen“ fest. Dies ist eine automatisierte Catch-All-Methode, für die keine benutzerdefinierte Logik im Datenfluss erforderlich ist.
* Alternativ können Sie die folgenden Schritte ausführen, um die Protokollierung von Spalten zu ermöglichen, die nicht in eine Zielzeichenfolgenspalte passen, sodass der Datenfluss fortgesetzt werden kann.

> [!NOTE]
> Wenn Sie die automatische Fehlerzeilenbehandlung aktivieren und nicht die nachstehende Methode zum Schreiben einer eigenen Fehlerbehandlungslogik verwenden, tritt eine geringe Leistungseinbuße auf und wird ein zusätzlicher Schritt von ADF unternommen, um einen zweistufigen Vorgang zum Abfangen von Fehlern auszuführen.

## <a name="scenario"></a>Szenario

1. Verwendet wird eine Datenbank-Zieltabelle, die eine ```nvarchar(5)```-Spalte mit dem Namen „name“ aufweist.

2. Innerhalb des Datenflusses sollen Filmtitel aus der Senke zur Zielspalte „name“ zugeordnet werden.

    ![Datenfluss für Filme 1](media/data-flow/error4.png)
    
3. Filmtitel passen jedoch nicht ganz in eine Senkenspalte, die nur 5 Zeichen umfassen kann. Beim Ausführen dieses Datenflusses erhalten Sie eine Fehlermeldung ähnlich der folgenden: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Dieses Video führt Sie durch ein Beispiel für das Einrichten der Logik zur Fehlerzeilenbehandlung in Ihrem Datenfluss:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Umgehen dieser Fehlerbedingung

1. In diesem Szenario ist die maximale Länge der Spalte „name“ auf fünf Zeichen beschränkt. Daher fügen wir eine Transformation für bedingtes Teilen hinzu, sodass Zeilen mit Titeln („title“) mit mehr als fünf Zeichen protokolliert werden können und gleichzeitig die restlichen Zeilen, die in diesen Bereich passen, in die Datenbank geschrieben werden können.

    ![Bedingtes Teilen](media/data-flow/error1.png)

2. Mit dieser Transformation für bedingtes Teilen wird die maximale Länge von „title“ auf fünf Zeichen festgelegt. Jede Zeile, die kleiner oder gleich fünf Zeichen ist, wird in den Datenstrom ```GoodRows``` geleitet. Jede Zeile mit mehr als fünf Zeilen wird in den Datenstrom ```BadRows``` geleitet.

3. Nun müssen die fehlerhaften Zeilen protokolliert werden. Fügen Sie dem Datenstrom ```BadRows``` eine Senkentransformation für die Protokollierung hinzu. Hier werden alle Felder automatisch zugeordnet, damit der gesamte Transaktionsdatensatz protokolliert wird. Dies ist die Ausgabe einer CSV-Datei mit Texttrennzeichen in einer einzelnen Datei in Blob Storage. Die Protokolldatei erhält den Namen „badrows.csv“.

    ![Datei „badrows.csv“ mit fehlerhaften Zeilen](media/data-flow/error3.png)
    
4. Der vollständige Datenfluss ist nachfolgend dargestellt. Nun können fehlerhafte Zeilen abgespaltet werden, um SQL-Kürzungsfehler zu vermeiden, und diese Einträge können in einer Protokolldatei eingefügt werden. Fehlerfreie Zeilen können dagegen weiterhin in die Zieldatenbank geschrieben werden.

    ![Vollständiger Datenfluss](media/data-flow/error2.png)

5. Wenn Sie die Option für die Fehlerzeilenbehandlung in der Senkentransformation auswählen und „Fehlerzeilen ausgeben“ festlegen, generiert ADF automatisch eine CSV-Dateiausgabe der Zeilendaten zusammen mit den vom Treiber ausgegebenen Fehlermeldungen. Bei dieser alternativen Option müssen Sie diese Logik nicht manuell zum Datenfluss hinzufügen. Diese Option führt zu einer geringen Leistungseinbuße, damit ADF eine zweistufige Methode implementieren kann, um Fehler abzufangen und zu protokollieren.

    ![Vollständiger Datenfluss mit Fehlerzeilen](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>Nächste Schritte

* Erstellen Sie die restliche Datenflusslogik mithilfe von Mapping Data Flow-[Transformationen](concepts-data-flow-overview.md).