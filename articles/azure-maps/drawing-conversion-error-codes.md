---
title: 'Azure Maps: Fehler und Warnungen bei der Zeichnungskonvertierung'
description: Hier finden Sie Informationen zu Konvertierungsfehlern und -warnungen, die bei der Verwendung des Azure Maps-Konvertierungsdiensts auftreten können. Die Informationen enthalten Empfehlungen zur Behebung der Fehler/Warnungen sowie einige Beispiele.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cecc19f0984ce1801d50e5cbda73e98a01e2825b
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906215"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Fehler und Warnungen bei der Zeichnungskonvertierung

Mit dem [Azure Maps-Konvertierungsdienst](/rest/api/maps/conversion) können Sie hochgeladene Zeichnungspakete in Kartendaten konvertieren. Zeichnungspakete müssen die [Anforderungen für Zeichnungspakete](drawing-requirements.md) erfüllen. Bei nicht erfüllten Anforderungen werden vom Konvertierungsdienst entsprechende Fehler oder Warnungen zurückgegeben. Dieser Artikel enthält die Fehler- und Warnungscodes im Zusammenhang mit der Konvertierung sowie Empfehlungen zur Behebung. Darüber hinaus finden Sie hier einige Beispiele für Zeichnungen, die zur Rückgabe dieser Codes durch den Konvertierungsdienst führen können.

Die Konvertierung ist erfolgreich, auch wenn Konvertierungswarnungen vorhanden sind. Es empfiehlt sich jedoch, alle Warnungen zu überprüfen und zu beheben. Im Falle einer Warnung wurde ein Teil der Konvertierung ignoriert oder automatisch korrigiert. Wird die Warnung nicht behoben, kann dies Fehler in späteren Prozessen zur Folge haben.

## <a name="general-warnings"></a>Allgemeine Warnungen

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*Beschreibung für „geometryWarning“*

Eine Warnung vom Typ **geometryWarning** tritt auf, wenn die Zeichnung eine ungültige Entität enthält. Eine ungültige Entität ist eine Entität, die nicht den geometrischen Einschränkungen entspricht. Beispiele für eine ungültige Entität wären etwa ein sich selbst schneidendes Polygon oder eine nicht geschlossene Polylinie in einer Schicht, in der nur geschlossene Geometrie unterstützt wird.

Da auf der Grundlage einer ungültigen Entität kein Kartenfeature erstellt werden kann, wird die Entität vom Konvertierungsdienst ignoriert.

#### <a name="examples-for-geometrywarning"></a>*Beispiele für „geometryWarning“*

* Die beiden folgenden Bilder zeigen Beispiele für sich selbst schneidende Polygone:

     ![Beispiel für ein sich selbst schneidendes Polygon (Beispiel 1)](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Beispiel für ein sich selbst schneidendes Polygon (Beispiel 2)](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Das folgende Bild zeigt eine nicht geschlossene Polylinie: Gehen Sie davon aus, dass in der Schicht nur geschlossene Geometrie unterstützt wird.

    ![Beispiel für eine nicht geschlossene Polylinie](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*Beheben von „geometryWarning“*

Untersuchen Sie die Warnung vom Typ **geometryWarning** für jede Entität, um sich zu vergewissern, dass sie den geometrischen Einschränkungen entspricht.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Beschreibung für „unexpectedGeometryInLayer“*

Eine Warnung vom Typ **unexpectedGeometryInLayer** tritt auf, wenn die Zeichnung Geometrie enthält, die mit dem erwarteten Geometrietyp für eine bestimmte Schicht nicht kompatibel ist. Wenn vom Konvertierungsdienst eine Warnung vom Typ **unexpectedGeometryInLayer** zurückgegeben wird, wird die betreffende Geometrie ignoriert.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Beispiel für „unexpectedGeometryInLayer“*

Das folgende Bild zeigt eine nicht geschlossene Polylinie: Gehen Sie davon aus, dass in der Schicht nur geschlossene Geometrie unterstützt wird.

![Beispiel für eine nicht geschlossene Polylinie](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*Beheben von „unexpectedGeometryInLayer“*

Überprüfen Sie jede Warnung vom Typ **unexpectedGeometryInLayer**, und verschieben Sie die inkompatible Geometrie in eine kompatible Schicht. Sollte sie mit keiner der anderen Schichten kompatibel sein, entfernen Sie sie.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Beschreibung für „unsupportedFeatureRepresentation“*

Die Warnung **unsupportedFeatureRepresentation** tritt auf, wenn die Zeichnung einen nicht unterstützten Entitätstyp enthält.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Beispiel für „unsupportedFeatureRepresentation“*

Das folgende Bild zeigt einen nicht unterstützten Entitätstyp als mehrzeiliges Textobjekt in einer Bezeichnungsschicht:
  
![Beispiel für ein mehrzeiliges Textobjekt in einer Bezeichnungsschicht](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*Beheben von „unsupportedFeatureRepresentation“*

Stellen Sie sicher, dass Ihre DWG-Dateien nur unterstützte Entitätstypen enthalten. Die unterstützten Typen sind im Artikel [Anforderungen für Zeichnungspakete](drawing-requirements.md#drawing-package-requirements) im Abschnitt „Anforderungen für DWG-Dateien“ aufgeführt.

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*Beschreibung für „automaticRepairPerformed“*

Die Warnung **automaticRepairPerformed** tritt auf, wenn ungültige Geometrie automatisch vom Konvertierungsdienst repariert wird.

#### <a name="examples-for-automaticrepairperformed"></a>*Beispiele für „automaticRepairPerformed“*

* Das folgende Bild zeigt, wie ein sich selbst schneidendes Polygon vom Konvertierungsdienst repariert wurde, um eine gültige Geometrie zu erhalten.

    ![Beispiel für die Reparatur eines sich selbst schneidenden Polygons](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* Im folgenden Bild ist zu sehen, wie der erste und der letzte Scheitelpunkt einer nicht geschlossenen Polylinie durch den Konvertierungsdienst zusammengeführt wurden, um eine geschlossene Polylinie zu erhalten, wobei der Abstand zwischen dem ersten und letzten Scheitelpunkt weniger als 1 mm betrug:  

    ![Beispiel für eine zusammengeführte Polylinie](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* Das folgende Bild zeigt, wie in einer Schicht, in der nur geschlossene Polylinien unterstützt werden, mehrere nicht geschlossene Polylinien durch den Konvertierungsdienst repariert wurden. Die nicht geschlossenen Polylinien wurden dabei durch den Dienst zu einer einzelnen geschlossenen Polylinie zusammengeführt, damit sie nicht verworfen werden:

    ![Beispiel für nicht geschlossene Polylinien, die zu einer einzelnen geschlossenen Polylinie zusammengeführt wurden](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Beheben von „automaticRepairPerformed“*

Führen Sie die folgenden Aktionen aus, um eine Warnung vom Typ **automaticRepairPerformed** zu beheben:

1. Untersuchen Sie die Geometrie der jeweiligen Warnung sowie den spezifischen Warnungstext.
2. Überprüfen Sie, ob das Ergebnis der automatischen Reparatur korrekt ist.
3. Falls ja, setzen Sie Ihre Arbeit fort. Falls nicht, beheben Sie die Warnung manuell in der Plandatei.

>[!TIP]
>Wenn in Zukunft keine Warnung mehr ausgelöst werden soll, ändern Sie die ursprüngliche Zeichnung so, dass sie der reparierten Zeichnung entspricht.

## <a name="manifest-warnings"></a>Manifestwarnungen

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*Beschreibung für „redundantAttribution“*

Die Warnung **redundantAttribution** tritt auf, wenn das Manifest redundante oder widersprüchliche Objekteigenschaften enthält.

#### <a name="examples-for-redundantattribution"></a>*Beispiele für „redundantAttribution“*

* Der folgende JSON-Codeausschnitt enthält mindestens zwei Objekte vom Typ `unitProperties` mit dem gleichen Namen (`name`):

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* Im folgende JSON-Codeausschnitt haben mindestens zwei Objekte vom Typ `zoneProperties` den gleichen Namen (`name`):

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*Beheben von „redundantAttribution“*

Zum Beheben einer Warnung vom Typ *redundantAttribution* müssen redundante oder widersprüchliche Objekteigenschaften entfernt werden.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*Beschreibung für „manifestWarning“*

Eine Warnung vom Typ **manifestWarning** tritt auf, wenn das Manifest Objekte vom Typ „unitProperties“ oder „zoneProperties“ enthält, die bei der Konvertierung nicht verwendet werden.

#### <a name="examples-for-manifestwarning"></a>*Beispiele für „manifestWarning“*

* Das Manifest enthält ein Objekt vom Typ `unitProperties` mit einem Einheitennamen (`unitName`), für den keine entsprechende Bezeichnung in einer Schicht vom Typ `unitLabel` vorhanden ist.

* Das Manifest enthält ein Objekt vom Typ `zoneProperties` mit einem Zonennamen (`zoneName`), für den keine entsprechende Bezeichnung in einer Schicht vom Typ `zoneLabel` vorhanden ist.

#### <a name="how-to-fix-manifestwarning"></a>*Beheben von „manifestWarning“*

Entfernen Sie zum Beheben einer Warnung vom Typ **manifestWarning** das nicht verwendete Objekt vom Typ `unitProperties` oder `zoneProperties` aus dem Manifest, oder fügen Sie der Zeichnung eine Einheiten-/Zonenbezeichnung hinzu, damit das Eigenschaftenobjekt bei der Konvertierung verwendet wird.

## <a name="wall-warnings"></a>Warnungen im Zusammenhang mit Wänden

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Beschreibung für „wallOutsideLevel“*

Die Warnung **wallOutsideLevel** tritt auf, wenn die Zeichnung eine Wandgeometrie außerhalb der Grenzen einer Ebenenkontur enthält.

#### <a name="example-for-walloutsidelevel"></a>*Beispiel für „wallOutsideLevel“*

* Das folgende Bild zeigt eine Innenwand (rot), die sich außerhalb der gelben Ebenengrenze befindet:

    ![Beispiel für eine Innenwand außerhalb der Ebenengrenze](./media/drawing-conversion-error-codes/wall-outside-level.png)

* Das folgende Bild zeigt eine Außenwand (rot), die sich außerhalb der gelben Ebenengrenze befindet:

    ![Beispiel für eine Außenwand außerhalb der Ebenengrenze](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Beheben von „wallOutsideLevel“*

Erweitern Sie zum Beheben einer Warnung vom Typ **wallOutsideLevel** die Ebenengeometrie so, dass sie alle Wände einschließt. Alternativ können Sie auch die Wandgrenzen ändern, sodass sie innerhalb der Ebenengrenze liegen.

## <a name="unit-warnings"></a>Warnungen im Zusammenhang mit Einheiten

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Beschreibung für „unitOutsideLevel“*

Eine Warnung vom Typ **unitOutsideLevel** tritt auf, wenn die Zeichnung eine Einheitengeometrie enthält, die sich außerhalb der Ebenenkontur befindet.

#### <a name="example-for-unitoutsidelevel"></a>*Beispiel für „unitOutsideLevel“*

 Im folgenden Bild geht die rot dargestellte Einheitengeometrie über die gelb dargestellte Ebenengrenze hinaus:

 ![Beispiel für eine Einheit, die über die Ebenengrenze hinausgeht](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Beheben von „unitOutsideLevel“*

Erweitern Sie zum Beheben einer Warnung vom Typ **unitOutsideLevel** die Ebenengrenze so, dass sie alle Einheiten einschließt. Alternativ können Sie auch die Einheitengeometrie ändern, sodass sie innerhalb der Ebenengrenze liegt.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*Beschreibung für „partiallyOverlappingUnit“*

Eine Warnung vom Typ **partiallyOverlappingUnit** tritt auf, wenn die Zeichnung eine Einheitengeometrie enthält, die sich teilweise mit einer anderen Einheitengeometrie überschneidet. Einheiten mit Überschneidung werden vom Konvertierungsdienst verworfen.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Beispielszenarien für „partiallyOverlappingUnit“*

Im folgenden Bild ist die Einheit mit der Überschneidung rot hervorgehoben. `UNIT110` und `HALLWAY` werden verworfen.

![Beispiel für sich überschneidende Einheiten](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*Beheben von „partiallyOverlappingUnit“*

Zum Beheben einer Warnung vom Typ **partiallyOverlappingUnit** müssen die sich teilweise überschneidenden Einheiten neu gezeichnet werden, damit sie sich nicht mehr mit anderen Einheiten überschneiden.

## <a name="door-warnings"></a>Warnungen im Zusammenhang mit Türen

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Beschreibung für „doorOutsideLevel“*

Eine Warnung vom Typ **doorOutsideLevel** tritt auf, wenn die Zeichnung eine Türgeometrie enthält, die sich außerhalb der Ebenengeometrie befindet.

#### <a name="example-for-dooroutsidelevel"></a>*Beispiel für „doorOutsideLevel“*

Im folgenden Bild überschneidet sich die rot hervorgehobene Türgeometrie mit der gelben Ebenengrenze:

![Beispiel für eine Tür, die sich mit einer Ebenengrenze überschneidet](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*Beheben von „doorOutsideLevel“*

Zum Beheben einer Warnung vom Typ **doorOutsideLevel** muss die Türgeometrie neu gezeichnet werden, damit sie sich innerhalb der Ebenengrenzen befindet.

## <a name="zone-warnings"></a>Warnungen im Zusammenhang mit Zonen

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*Beschreibung für „zoneWarning“*

Die Warnung **zoneWarning** tritt auf, wenn eine Zone keine Bezeichnung enthält. Zonen ohne Bezeichnung werden vom Konvertierungsdienst verworfen.

#### <a name="example-for-zonewarning"></a>*Beispiel für „zoneWarning“*

Das folgende Bild zeigt eine Zone ohne Bezeichnung:

![Beispiel für eine Zone ohne Bezeichnung](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Beheben von „zoneWarning“*

Stellen Sie zum Beheben einer Warnung vom Typ **zoneWarning** sicher, dass jede Zone über eine einzelne Bezeichnung verfügt.

## <a name="label-warnings"></a>Warnungen im Zusammenhang mit Bezeichnungen

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*Beschreibung für „labelWarning“*

Die Warnung **labelWarning** tritt auf, wenn die Zeichnung mehrdeutige oder widersprüchliche Bezeichnungen enthält.

Eine Warnung vom Typ **labelWarning** tritt aus folgenden Gründen auf:

* Eine Einheitenbezeichnung ist in keiner der Einheiten vorhanden.
* Eine Zonenbezeichnung ist in keiner der Zonen vorhanden.
* Eine Zonenbezeichnung ist in mehreren Zonen vorhanden.

#### <a name="example-for-labelwarning"></a>*Beispiel für „labelWarning“*

Das folgende Bild zeigt eine Bezeichnung in zwei Zonen:

![Beispiel für eine Bezeichnung in zwei Zonen ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*Beheben von „labelWarning“*

Stellen Sie zum Beheben einer Warnung vom Typ **labelWarning** sicher, dass Folgendes erfüllt ist:

* Alle Einheitenbezeichnungen befinden sich innerhalb von Einheiten.
* Alle Zonenbezeichnungen befinden sich innerhalb von Zonen.
* Alle Zonenbezeichnungen befinden sich jeweils in genau einer Zone.

## <a name="drawing-package-errors"></a>Fehler im Zusammenhang mit Zeichnungspaketen

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*Beschreibung für „invalidArchiveFormat“*

Ein Fehler vom Typ **invalidArchiveFormat** tritt auf, wenn das Zeichnungspaket in einem ungültigen Archivformat wie GZIP oder 7-Zip vorliegt. Nur das ZIP-Archivformat wird unterstützt.

Wenn das ZIP-Archiv leer ist, tritt ebenfalls ein Fehler vom Typ **invalidArchiveFormat** auf.

#### <a name="how-to-fix-invalidarchiveformat"></a>*Beheben von „invalidArchiveFormat“*

Stellen Sie zum Beheben eines Fehlers vom Typ **invalidArchiveFormat** sicher, dass Folgendes erfüllt ist:

* Der Name Ihrer Archivdatei endet mit _.zip_.
* Ihr ZIP-Archiv enthält Daten.
* Sie können Ihr ZIP-Archiv öffnen.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*Beschreibung für „invalidUserData“*

Ein Fehler vom Typ **invalidUserData** tritt auf, wenn vom Konvertierungsdienst ein Benutzerdatenobjekt nicht aus dem Speicher gelesen werden kann.

#### <a name="example-scenario-for-invaliduserdata"></a>*Beispielszenario für „invalidUserData“*

Sie haben versucht, ein Zeichnungspaket mit einem falschen Parameter vom Typ `udid` hochzuladen.

#### <a name="how-to-fix-invaliduserdata"></a>*Beheben von „invalidUserData“*

Stellen Sie zum Beheben eines Fehlers vom Typ **invalidUserData** sicher, dass Folgendes erfüllt ist:

* Sie haben für das hochgeladene Paket einen korrekten Parameter vom Typ `udid` angegeben.
* Azure Maps Creator (Vorschau) wurde für das Azure Maps-Konto aktiviert, das Sie zum Hochladen des Zeichnungspakets verwendet haben.
* Die an den Konvertierungsdienst gesendete API-Anforderung enthält den Abonnementschlüssel für das Azure Maps-Konto, das Sie zum Hochladen des Zeichnungspakets verwendet haben.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Beschreibung für „dwgError“*

Ein Fehler vom Typ **dwgError** wird ausgelöst, wenn im Zeichnungspaket ein Problem mit einzelnen oder mehreren DWG-Dateien des hochgeladenen ZIP-Archivs vorliegt.

Der Fehler **dwgError** tritt auf, wenn das Zeichnungspaket eine ungültige oder beschädigte DWG-Datei enthält, die nicht geöffnet werden kann:

* Bei einer DWG-Datei handelt es sich nicht um eine gültige Zeichnung im DWG-Dateiformat von AutoCAD.
* Eine DWG-Datei ist beschädigt.
* Eine in der Datei _manifest.json_ angegebene DWG-Datei ist nicht im ZIP-Archiv enthalten.

#### <a name="how-to-fix-dwgerror"></a>*Beheben von „dwgError“*

Vergewissern Sie sich zum Beheben eines Fehlers vom Typ **dwgError** anhand der Datei _manifest.json_, dass Folgendes erfüllt ist:

* Alle DWG-Dateien in Ihrem ZIP-Archiv sind gültige Zeichnungen im DWG-Format von AutoCAD, die sich jeweils in AutoCAD öffnen lassen. Entfernen oder korrigieren Sie alle ungültigen Zeichnungen.
* Die Liste der DWG-Dateien in _manifest.json_ entspricht den DWG-Dateien im ZIP-Archiv.

## <a name="manifest-errors"></a>Fehler im Zusammenhang mit dem Manifest

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Beschreibung für „invalidJsonFormat“

Ein Fehler vom Typ **invalidJsonFormat** tritt auf, wenn die Datei _manifest.json_ nicht gelesen werden kann.

Die Datei „manifest.json“ kann aufgrund von JSON-Formatierungs- oder -Syntaxfehlern nicht gelesen werden. Weitere Informationen zum JSON-Format und zur JSON-Syntax finden Sie unter [Das JSON-Datenaustauschformat (JavaScript Object Notation)](https://tools.ietf.org/html/rfc7159).

#### <a name="how-to-fix-invalidjsonformat"></a>*Beheben von „invalidJsonFormat“*

Verwenden Sie zum Beheben eines Fehlers vom Typ **invalidJsonFormat** einen JSON-Linter, um JSON-Fehler zu erkennen und zu beheben.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Beschreibung für „missingRequiredField“*

Ein Fehler vom Typ **missingRequiredField** tritt auf, wenn in der Datei _manifest.json_ erforderliche Daten fehlen.

#### <a name="how-to-fix-missingrequiredfield"></a>*Beheben von „missingRequiredField“*

Stellen Sie zum Beheben eines Fehlers vom Typ **missingRequiredField** sicher, dass das Manifest alle erforderlichen Eigenschaften enthält. Eine vollständige Liste der erforderlichen Manifestobjekte finden Sie im Abschnitt [Anforderungen für die Manifestdatei](drawing-requirements.md#manifest-file-requirements) des Artikels „Anforderungen für Zeichnungspakete“.  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*Beschreibung für „missingManifest“*

Der Fehler **missingManifest** tritt auf, wenn die Datei _manifest.json_ im ZIP-Archiv nicht vorhanden ist.

Der Fehler **missingManifest** tritt aus folgenden Gründen auf:

* Die Datei _manifest.json_ ist falsch geschrieben.
* Die Datei _manifest.json_ ist nicht vorhanden.
* Die Datei _manifest.json_ befindet sich nicht im Stammverzeichnis des ZIP-Archivs.

#### <a name="how-to-fix-missingmanifest"></a>*Beheben von „missingManifest“*

Vergewissern Sie sich zum Beheben eines Fehlers vom Typ **missingManifest**, dass auf der Stammebene des ZIP-Archivs eine Datei namens _manifest.json_ vorhanden ist.

### <a name="conflict"></a>**conflict**

#### <a name="description-for-conflict"></a>*Beschreibung für „conflict“*

Der Fehler **conflict** tritt auf, wenn die Datei _manifest.json_ widersprüchliche Informationen enthält.

#### <a name="example-scenario-for-conflict"></a>*Beispielszenario für „conflict“*

Vom Konvertierungsdienst wird ein Fehler vom Typ **conflict** zurückgegeben, wenn mehrere Ebenen mit der gleichen Ebenenordnungszahl definiert sind. Der folgende JSON-Codeausschnitt enthält zwei Ebenen, die mit der gleichen Ordnungszahl definiert sind:

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Beheben von „conflict“*

Untersuchen Sie zum Beheben eines Fehlers vom Typ **conflict** die Datei _manifest.json_, und entfernen Sie alle widersprüchlichen Informationen.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Beschreibung für „invalidGeoreference“*

Der Fehler **invalidGeoreference** tritt auf, wenn eine Datei vom Typ _manifest.json_ eine ungültige Georeferenz enthält.

Der Fehler **invalidGeoreference** tritt aus folgenden Gründen auf:

* Der Benutzer verwendet eine Georeferenz mit einem Breiten- oder Längengradwert, der außerhalb des gültigen Bereichs liegt.
* Der Benutzer verwendet eine Georeferenz mit einem Drehungswert, der außerhalb des gültigen Bereichs liegt.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Beispielszenario für „invalidGeoreference“*

Im folgenden JSON-Codeausschnitt liegt der Breitengrad über der Obergrenze:

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Beheben von „invalidGeoreference“*

Vergewissern Sie sich zum Beheben eines Fehlers vom Typ **invalidGeoreference**, dass die georeferenzierten Werte innerhalb des gültigen Bereichs liegen.

>[!IMPORTANT]
>In GeoJSON wird bei Koordinaten zuerst der Längengrad und dann der Breitengrad angegeben. Bei Verwendung der falschen Reihenfolge wird möglicherweise versehentlich auf einen Breiten- oder Längengradwert verwiesen, der außerhalb des gültigen Bereichs liegt.

## <a name="wall-errors"></a>Fehler im Zusammenhang mit Wänden

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Beschreibung für „wallError“*

Der Fehler **wallError** tritt auf, wenn versucht wird, ein Wandfeature zu erstellen, und die Zeichnung einen Fehler enthält.

#### <a name="example-scenario-for-wallerror"></a>*Beispielszenario für „wallError“*

Das folgende Bild zeigt ein Wandfeature, das sich mit keinen Einheiten überschneidet:

![Beispiel für ein Wandfeature, das sich mit keinen Einheiten überschneidet](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*Beheben von „wallError“*

Zeichnen Sie zum Beheben eines Fehlers vom Typ **wallError** die Wand neu, sodass sie sich mit mindestens einer Einheit überschneidet. Alternativ können Sie auch eine neue Einheit erstellen, die sich mit der Wand überschneidet.

## <a name="vertical-penetration-errors"></a>Fehler im Zusammenhang mit vertikalen Durchdringungen

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*Beschreibung für „verticalPenetrationError“*

Der Fehler **verticalPenetrationError** tritt auf, wenn die Zeichnung ein nicht eindeutiges Feature für eine vertikale Durchdringung enthält.

Der Fehler **verticalPenetrationError** tritt aus folgenden Gründen auf:

* Die Zeichnung enthält einen Bereich mit einer vertikalen Durchdringung, auf den darüber oder darunter befindlichen Ebenen sind jedoch keine entsprechenden Bereiche mit vertikaler Durchdringung vorhanden.
* Das Zeichnungspaket enthält eine Ebene mit mindestens zwei vertikale Durchdringungsfeatures, die sich jeweils mit einem einzelnen vertikalen Durchdringungsfeature auf einer direkt darüber oder darunter befindlichen Ebene überschneiden.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Beispielszenario für „verticalPenetrationError“*

Das folgende Bild zeigt einen Bereich mit einer vertikalen Durchdringung ohne entsprechende Bereiche auf darüber oder darunter befindlichen Ebenen:

![Beispiel für eine vertikale Durchdringung 1](./media/drawing-conversion-error-codes/vrt-2.png)

Das folgende Bild zeigt einen Bereich mit einer vertikalen Durchdringung, der sich mit mehreren Bereichen mit vertikaler Durchdringung auf benachbarten Ebenen überschneidet:

![Beispiel für eine vertikale Durchdringung 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Beheben von „verticalPenetrationError“

Informieren Sie sich zum Beheben eines Fehlers vom Typ **verticalPenetrationError** im Artikel [Anforderungen für Zeichnungspakete](drawing-requirements.md) über die Verwendung eines vertikalen Durchdringungsfeatures.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden der Azure Maps-Schnellansicht für Zeichnungsfehler](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Creator (Vorschau) für Gebäudepläne](creator-indoor-maps.md)