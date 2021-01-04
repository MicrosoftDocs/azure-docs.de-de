---
title: Konzepte und Definitionen für benutzerdefinierte Befehle – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie mehr über Konzepte und Definitionen für Anwendungen für benutzerdefinierte Befehle.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561096"
---
# <a name="custom-commands-concepts-and-definitions"></a>Konzepte und Definitionen für benutzerdefinierte Befehle

Dieser Artikel dient als Referenz für Konzepte und Definitionen für Anwendungen für benutzerdefinierte Befehle.

## <a name="commands-configuration"></a>Befehlskonfiguration
Befehle sind die Grundbausteine einer Anwendung für benutzerdefinierte Befehle. Ein Befehl besteht aus einer Reihe von Konfigurationen, die zur Durchführung einer bestimmten, von einem Benutzer definierten Aufgabe erforderlich sind.

### <a name="example-sentences"></a>Beispielsätze
Beispieläußerungen sind die festgelegten Beispiele, die der Benutzer sagen kann, um einen bestimmten Befehl auszulösen. Sie müssen lediglich eine Auswahl von Äußerungen und keine umfassende Liste bereitstellen. 

### <a name="parameters"></a>Parameter
Parameter sind Informationen, die von den Befehlen zur Durchführung einer Aufgabe erforderlich sind. In komplexen Szenarien können Sie Parameter auch zur Definition von Bedingungen verwenden, durch die benutzerdefinierte Aktionen ausgelöst werden.

### <a name="completion-rules"></a>Vervollständigungsregeln
Bei Vervollständigungsregeln handelt es sich um eine Reihe von Regeln, die ausgeführt werden, nachdem der Befehl für die Ausführung bereit ist (also beispielsweise, wenn alle Bedingungen der Regeln erfüllt sind).

### <a name="interaction-rules"></a>Interaktionsregeln
Interaktionsregeln sind zusätzliche Regeln für spezifischere oder komplexere Situationen. Sie können zusätzliche Validierungen hinzufügen oder erweiterte Features konfigurieren (beispielsweise Bestätigungen oder eine einstufige Korrektur). Sie können auch eigene benutzerdefinierte Interaktionsregeln erstellen.

## <a name="parameters-configuration"></a>Parameterkonfiguration

Parameter sind Informationen, die von den Befehlen zur Durchführung einer Aufgabe erforderlich sind. In komplexen Szenarien können Sie Parameter auch zur Definition von Bedingungen verwenden, durch die benutzerdefinierte Aktionen ausgelöst werden.

### <a name="name"></a>Name
Ein Parameter wird durch die Name-Eigenschaft identifiziert. Sie sollten einem Parameter immer einen beschreibenden Namen zuweisen. Auf einen Parameter kann in verschiedenen Abschnitten verwiesen werden – etwa beim Erstellen von Bedingungen, Sprachantworten oder anderen Aktionen.
 
### <a name="isglobal"></a>IsGlobal
Mithilfe dieses Kontrollkästchens wird angegeben, ob der Bereich dieses Parameters von allen Befehlen in der Anwendung gemeinsam genutzt wird. Der Wert eines globalen Parameters kann potenziell über jeden beliebigen Befehlsbereich angegeben werden. Nachdem ein Wert zugewiesen wurde, kann darauf in jedem der Befehle verwiesen werden. 

### <a name="required"></a>Erforderlich
Mithilfe dieses Kontrollkästchens wird angegeben, ob ein Wert für diesen Parameter zur Befehlserfüllung oder -vervollständigung erforderlich ist. Ist ein Parameter als erforderlich markiert, müssen Antworten konfiguriert werden, in denen der Benutzer zur Angabe eines Werts aufgefordert wird.

Wenn Sie einen **erforderlichen Parameter** so konfiguriert haben, dass er einen **Standardwert** aufweist, beachten Sie, das System dennoch explizit nach dem Wert des Parameters fragt.

### <a name="type"></a>Typ
Von benutzerdefinierten Befehlen werden folgende Parametertypen unterstützt:

* Datum/Uhrzeit
* Geografie
* Number
* String

Alle diese Parametertypen unterstützen mit Ausnahme der Geografie die Konfiguration von Standardwerten, die Sie über das Portal konfigurieren können.

### <a name="configuration"></a>Konfiguration
Konfiguration ist eine Parametereigenschaft, die nur für den Zeichenfolgentyp definiert ist. Die folgenden Werte werden unterstützt:

* **None** (Keine).
* **Accept full input** (Vollständige Eingabe akzeptieren): Ist diese Option aktiviert, wird von einem Parameter jede beliebige Eingabeäußerung akzeptiert. Dies ist hilfreich, wenn der Benutzer einen Parameter mit der vollständigen Äußerung benötigt. Ein Beispiel hierfür wären etwa Postanschriften.
* **Accept predefined input values from an external catalog** (Vordefinierte Eingabewerte aus einem externen Katalog akzeptieren): Dieser Wert dient zum Konfigurieren eines Parameters, der ein breites Spektrum von Werten annehmen kann. Ein Beispiel wäre etwa ein Produktkatalog. In diesem Fall wird der Katalog auf einem externen Webendpunkt gehostet und kann unabhängig konfiguriert werden.
* **Accept predefined input values from internal catalog** (Vordefinierte Eingabewerte aus internem Katalog akzeptieren): Dieser Wert dient zum Konfigurieren eines Parameters, der einige wenige Werte annehmen kann. In diesem Fall müssen Werte in Speech Studio konfiguriert werden.


### <a name="validation"></a>Validierung
Validierungen sind Konstrukte für bestimmte Parametertypen, mit denen Sie Einschränkungen für den Wert eines Parameters konfigurieren können. Aktuell werden von benutzerdefinierten Befehlen Validierungen für die folgenden Parametertypen unterstützt:

* Datum/Uhrzeit
* Number

## <a name="rules-configuration"></a>Konfiguration von Regeln
Eine Regel in benutzerdefinierten Befehlen wird durch einen Satz von *Bedingungen* definiert, durch die eine Reihe von *Aktionen* ausgeführt wird, wenn sie erfüllt wurden. Mithilfe von Regeln können Sie außerdem den *Status nach der Ausführung* sowie die *Erwartungen* für die nächste Runde konfigurieren.

### <a name="types"></a>Typen
Von benutzerdefinierten Befehlen werden folgende Regelkategorien unterstützt:

* **Vervollständigungsregeln**: Diese Regeln müssen bei der Befehlserfüllung ausgeführt werden. Alle in diesem Abschnitt konfigurierten Regeln, für die die Bedingungen erfüllt sind, werden ausgeführt. 
* **Interaktionsregeln**: Diese Regeln können verwendet werden, um zusätzliche benutzerdefinierte Validierungen, Bestätigungen und eine einstufige Korrektur zu konfigurieren oder um eine beliebige andere benutzerdefinierte Dialoglogik zu erhalten. Interaktionsregeln werden in jeder Runde bei der Verarbeitung ausgewertet und können zum Auslösen von Vervollständigungsregeln verwendet werden.

Die verschiedenen als Teil einer Regel konfigurierten Aktionen werden in der Reihenfolge ausgeführt, in der sie im Erstellungsportal angezeigt werden.

### <a name="conditions"></a>Bedingungen
Bedingungen sind die Anforderungen, die erfüllt sein müssen, damit eine Regel ausgeführt wird. Folgende Arten von Regelbedingungen sind möglich:

* **Parameterwert ist gleich**: Der Wert des konfigurierten Parameters entspricht einem bestimmten Wert.
* **Kein Parameterwert**: Die konfigurierten Parameter dürfen keinen Wert haben.
* **Erforderliche Parameter**: Der konfigurierte Parameter hat einen Wert.
* **Alle erforderlichen Parameter**: Alle als erforderlich markierten Parameter haben einen Wert.
* **Aktualisierte Parameter**: Mindestens ein Parameterwert wurden infolge der Verarbeitung der aktuellen Eingabe (Äußerung oder Aktivität) aktualisiert.
* **Bestätigung war erfolgreich**: Die Eingabeäußerung oder -aktivität war eine erfolgreiche Bestätigung („Ja“).
* **Bestätigung wurde verweigert**: Die Eingabeäußerung oder -aktivität war keine erfolgreiche Bestätigung („Nein“).
* **Vorheriger Befehl muss aktualisiert werden**: Diese Bedingung wird in Fällen verwendet, in denen Sie eine negierte Bestätigung zusammen mit einer Aktualisierung abfangen möchten. Dies wird im Hintergrund für den Fall konfiguriert, dass das Dialogmodul eine negative Bestätigung erkennt, bei der die Absicht dieselbe ist wie bei der vorherigen Runde, und der Benutzer mit einer Aktualisierung reagiert hat.

### <a name="actions"></a>Aktionen
* **Sprachantwort senden**: Dient zum Senden einer Sprachantwort an den Client.
* **Parameterwert aktualisieren**: Dient zum Aktualisieren des Werts eines Befehlsparameters auf einen bestimmten Wert.
* **Parameterwert löschen**: Dient zum Löschen des Befehlsparameterwerts.
* **Webendpunkt aufrufen**: Dient zum Aufrufen eines Webendpunkts.
* **Aktivität an Client senden**: Dient zum Senden einer benutzerdefinierten Aktivität an den Client.

### <a name="expectations"></a>Erwartungen
Erwartungen werden verwendet, um Hinweise für die Verarbeitung der nächsten Benutzereingabe zu konfigurieren. Die folgenden Typen werden unterstützt:

* **Bestätigung vom Benutzer erwarten**: Durch diese Erwartung wird angegeben, dass die Anwendung für die nächste Benutzereingabe eine Bestätigung („Ja“/“Nein“) erwartet.
* **Parametereingabe(n) vom Benutzer erwarten**: Durch diese Erwartung wird mindestens ein Befehlsparameter angegeben, den die Anwendung in der Benutzereingabe erwartet.

### <a name="post-execution-state"></a>Status nach der Ausführung
Der Status nach der Ausführung ist der Dialogzustand nach der Verarbeitung der aktuellen Eingabe (Äußerung oder Aktivität). Folgende Typen sind verfügbar:

* **Aktuellen Zustand beibehalten**: Nur den aktuellen Zustand beibehalten.
* **Befehl ausführen**: Der Befehl wird abgeschlossen, und es werden keine weiteren Regeln des Befehls verarbeitet.
* **Vervollständigungsregeln ausführen**: Alle gültigen Vervollständigungsregeln werden ausgeführt.
* **Auf Benutzereingabe warten**: Es wird auf die nächste Benutzereingabe gewartet.



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen finden Sie in den Beispielen auf GitHub](https://aka.ms/speech/cc-samples)
