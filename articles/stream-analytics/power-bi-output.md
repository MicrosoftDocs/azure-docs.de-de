---
title: Power BI-Ausgabe für Azure Stream Analytics
description: In diesem Artikel wird beschrieben, wie Daten aus Azure Stream Analytics in Power BI ausgegeben werden.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 29bd4dc5cabe8b29efe7b89395c5e7d36bd0347a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129778"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Power BI-Ausgabe für Azure Stream Analytics

Sie können [Power BI](https://powerbi.microsoft.com/) als Ausgabe für einen Stream Analytics-Auftrag verwenden, um eine umfassende Visualisierungsumgebung für die Analyseergebnisse bereitzustellen. Diese Funktionalität kann für Vorgangsdashboards, die Erstellung von Berichten und eine metrikgesteuerte Berichterstellung verwendet werden.

Die Power BI-Ausgabe aus Stream Analytics ist zurzeit nicht in den Regionen Azure China 21Vianet und Azure Deutschland (T-Systems International) verfügbar.

## <a name="output-configuration"></a>Ausgabekonfiguration

In der folgenden Tabelle sind Eigenschaftsnamen und deren Beschreibungen aufgeführt, um Ihre Power BI-Ausgabe zu konfigurieren.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias |Stellen Sie einen Anzeigenamen bereit, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Power BI-Ausgabe weiterzuleiten. |
| Gruppenarbeitsbereich |Um die gemeinsame Datennutzung mit anderen Power BI-Benutzern zu ermöglichen, können Sie Gruppen in Ihrem Power BI-Konto auswählen. Wählen Sie alternativ **Mein Arbeitsbereich** , wenn Sie nicht in eine Gruppe schreiben möchten. Zum Aktualisieren einer vorhandenen Gruppe muss die Power BI-Authentifizierung erneuert werden. |
| Datasetname |Geben Sie einen Datasetnamen an, den die Power BI-Ausgabe verwenden soll. |
| Tabellenname |Geben Sie einen Tabellennamen unter dem Dataset der Power BI-Ausgabe ein. Derzeit darf die Power BI-Ausgabe von Stream Analytics-Aufträgen nur eine Tabelle pro Dataset aufweisen. |
| Autorisieren der Verbindung | Sie müssen die Autorisierung mit Power BI durchführen, um Ihre Ausgabeeinstellungen zu konfigurieren. Nachdem Sie für diese Ausgabe den Zugriff auf Ihr Power BI-Dashboard gewährt haben, können Sie ihn widerrufen, indem Sie das Kennwort des Benutzerkontos ändern, die Auftragsausgabe löschen oder den Stream Analytics-Auftrag löschen. | 

Eine Schritt-für-Schritt-Anleitung zum Konfigurieren einer Power BI-Ausgabe und eines Power BI-Dashboards erhalten Sie im Tutorial [Azure Stream Analytics und Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Erstellen Sie das Dataset und die Tabelle nicht explizit im Power BI-Dashboard. Das Dataset und die Tabelle werden automatisch mit Daten aufgefüllt, wenn der Auftrag gestartet wird und damit beginnt, Ausgaben an Power BI weiterzuleiten. Wenn die Auftragsabfrage keine Ergebnisse generiert, werden das Dataset und die Tabelle nicht erstellt. Wenn Power BI bereits über ein Dataset und eine Tabelle mit demselben Namen verfügt, der in diesem Stream Analytics-Auftrag angegeben wurde, werden die vorhandenen Daten überschrieben.
>

### <a name="create-a-schema"></a>Erstellen eines Schemas

Azure Stream Analytics erstellt für den Benutzer ein Power BI-Dataset und ein Tabellenschema, falls diese noch nicht vorhanden sind. In allen anderen Fällen wird die Tabelle mit neuen Werten aktualisiert. Derzeit kann innerhalb eines Datasets nur eine Tabelle vorhanden sein. 

Power BI verwendet die FIFO-Aufbewahrungsrichtlinie (First In, First Out). Daten werden in einer Tabelle gesammelt, bis diese 200.000 Zeilen erreicht.

> [!NOTE]
> Es ist nicht zu empfehlen, mehrere Ausgaben zum Schreiben in dasselbe Dataset zu verwenden, da dies mehrere Probleme verursachen kann. Jede Ausgabe versucht, das Power BI-Dataset unabhängig zu erstellen, was dazu führen kann, dass mehrere Datasets denselben Namen haben. Außerdem wird, wenn die Ausgaben keine konsistenten Schemas haben, das Datasetschema bei jedem Schreibvorgang geändert, wodurch eine zu großen Anzahl von Schemaänderungsanforderungen verursacht wird. Selbst wenn sich diese Probleme vermeiden lassen, sind mehrere Ausgaben weniger leistungsfähig als eine einzelne zusammengeführte Ausgabe.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konvertieren eines Datentyps aus Stream Analytics in Power BI

Azure Stream Analytics aktualisiert das Datenmodell dynamisch zur Laufzeit, wenn sich das Ausgabeschema ändert. Nachverfolgt werden Änderungen an Spaltennamen und -typen sowie das Hinzufügen/Entfernen von Spalten.

Die folgende Tabelle enthält die Datentypkonvertierungen von [Stream Analytics-Datentypen](/stream-analytics-query/data-types-azure-stream-analytics) in [EDM-Typen (Entity Data Model)](/dotnet/framework/data/adonet/entity-data-model) von Power BI für den Fall, dass noch kein Power BI-Dataset und noch keine Tabelle vorhanden sind.

Quelle: Stream Analytics | Ziel: Power BI
-----|-----
BIGINT | Int64
nvarchar(max) | String
datetime | Datetime
float | Double
Datensatzarray | Zeichenfolgentyp, Konstantenwert „IRecord“ oder „IArray“

### <a name="update-the-schema"></a>Aktualisieren des Schemas

Stream Analytics leitet das Datenmodellschema vom ersten Ereignissatz in der Ausgabe ab. Später wird das Datenmodellschema bei Bedarf aktualisiert, um eingehende Ereignisse zu berücksichtigen, die unter Umständen nicht in das ursprüngliche Schema passen.

Vermeiden Sie die `SELECT *`-Abfrage, um zeilenübergreifende dynamische Schemaaktualisierungen zu verhindern. Neben einer möglichen Beeinträchtigung der Leistung ist möglicherweise auch der Zeitaufwand für die Ergebnisse ungewiss. Wählen Sie die genauen Felder aus, die auf dem Power BI-Dashboard angezeigt werden sollen. Außerdem müssen die Datenwerte mit dem ausgewählten Datentyp kompatibel sein.

Vorher/Aktuell | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="output-batch-size"></a>Ausgabebatchgröße

Informationen zur Ausgabebatchgröße finden Sie unter [Einschränkungen für Power BI-REST-API](/power-bi/developer/automation/api-rest-api-limitations).

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mit der Azure CLI](quick-create-azure-cli.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe einer ARM-Vorlage](quick-create-azure-resource-manager.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mit Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags in Visual Studio Code](quick-create-visual-studio-code.md)