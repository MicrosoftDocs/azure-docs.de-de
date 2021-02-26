---
title: Aktivität „Prüfung“ in Azure Data Factory
description: Die Aktivität „Prüfung“ setzt die Ausführung der Pipeline erst fort, nachdem sie das angefügte Dataset mit bestimmten vom Benutzer angegebenen Kriterien überprüft hat.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e00090fc31c129f35632d30b9a5ed6c39c13a414
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385250"
---
# <a name="validation-activity-in-azure-data-factory"></a>Aktivität „Prüfung“ in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sie können mithilfe einer Prüfung in einer Pipeline sicherstellen, dass diese die Ausführung nur fortsetzt, nachdem sie überprüft hat, ob der angefügte Datasetverweis vorhanden ist und die angegebenen Kriterien erfüllt oder ob das Timeout erreicht wurde.


## <a name="syntax"></a>Syntax

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Name der Aktivität „Prüfung“. | String | Ja |
type | Muss auf **Prüfung** festgelegt werden. | String | Ja |
dataset | Die Aktivität blockiert die Ausführung, bis sie überprüft hat, ob dieser Datasetverweis vorhanden ist und die angegebenen Kriterien erfüllt oder ob das Timeout erreicht wurde. Das bereitgestellte Dataset sollte eine der Eigenschaften „MinimumSize“ oder „ChildItems“ unterstützen. | Datasetverweis | Ja |
timeout | Gibt das Zeitlimit für die Ausführung der Aktivität an. Wenn kein Wert angegeben wird, ist der Standardwert 7 Tage („7.00:00:00“). Das Format ist „d.hh:mm:ss“. | String | Nein |
sleep | Eine Verzögerung in Sekunden zwischen Prüfungsversuchen. Wenn kein Wert angegeben wird, ist der Standardwert 10 Sekunden. | Integer | Nein |
childItems | Überprüft, ob der Ordner untergeordnete Elemente enthält. Kann festgelegt werden auf–„true“: Überprüfen, ob der Ordner vorhanden ist und Elemente enthält. Blockiert, bis mindestens ein Element im Ordner vorhanden oder der Timeoutwert erreicht ist.–„false“: Überprüfen, ob der Ordner vorhanden und leer ist. Blockiert, bis der Ordner leer oder der Timeoutwert erreicht ist. Wenn kein Wert angegeben wird, blockiert die Aktivität, bis der Ordner vorhanden oder das Timeout erreicht ist. | Boolean | Nein |
minimumSize | Mindestgröße einer Datei in Bytes. Wenn kein Wert angegeben wird, ist der Standardwert 0 Bytes. | Integer | Nein |


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter anderen Ablaufsteuerungsaktivitäten, die von Data Factory unterstützt werden:

- [Aktivität „If Condition“](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)
