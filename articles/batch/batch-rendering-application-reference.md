---
title: Verwenden von Renderinganwendungen – Azure Batch
description: Verwenden von Renderinganwendungen mit Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4c93abdfb5c523d48ce115ed7d3251a346937f5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775388"
---
# <a name="rendering-applications"></a>Renderinganwendungen

Renderinganwendungen werden verwendet, indem Batch-Aufträge und -Aufgaben erstellt werden. Mit der Befehlszeileneigenschaft für die Aufgabe werden die entsprechende Befehlszeile und die Parameter angegeben.  Die einfachste Möglichkeit zur Erstellung der Auftragsaufgaben ist das Verwenden der Batch Explorer-Vorlagen, wie in [diesem Artikel](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer) angegeben.  Die Vorlagen können angezeigt werden, und bei Bedarf können auch geänderte Versionen erstellt werden.

Dieser Artikel enthält eine kurze Beschreibung dazu, wie die einzelnen Renderinganwendungen ausgeführt werden.

## <a name="rendering-with-autodesk-3ds-max"></a>Rendering mit Autodesk 3ds Max

### <a name="renderer-support"></a>Renderunterstützung

Zusätzlich zu den in 3ds Max integrierten Renderern sind die folgenden Renderer in den Rendering-VM-Images verfügbar, und in der 3ds Max-Szenendatei kann darauf verwiesen werden:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Befehlszeile für Aufgabe

Rufen Sie die Anwendung `3dsmaxcmdio.exe` auf, um für einen Poolknoten das Rendering per Befehlszeile durchzuführen.  Diese Anwendung befindet sich unter dem Pfad, wenn die Aufgabe ausgeführt wird. Die Anwendung `3dsmaxcmdio.exe` verfügt über die gleichen verfügbaren Parameter wie die Anwendung `3dsmaxcmd.exe`. Dies ist in der [3ds Max-Hilfe](https://help.autodesk.com/view/3DSMAX/2018/ENU/) dokumentiert (Abschnitt „Rendering“ | „Command-Line Rendering“).

Beispiel:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Hinweise:

* Es muss sorgfältig sichergestellt werden, dass die Medienobjektdateien gefunden werden können.  Vergewissern Sie sich, dass die Pfade korrekt sind und dass es sich um relative Pfade handelt, indem Sie das Fenster **Asset Tracking** oder den Parameter `-bitmapPath` in der Befehlszeile verwenden.
* Prüfen Sie, ob für das Rendern Probleme vorliegen, z.B. Fehler beim Finden von Medienobjekten, indem Sie in der Datei `stdout.txt` nachsehen, die von 3ds Max beim Ausführen der Aufgabe geschrieben wird.

### <a name="batch-explorer-templates"></a>Batch Explorer-Vorlagen

Auf Pool- und Auftragsvorlagen kann in Batch Explorer über den **Katalog** zugegriffen werden.  Die Quelldateien der Vorlagen sind im [Batch Explorer-Datenrepository auf GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax) verfügbar.

## <a name="rendering-with-autodesk-maya"></a>Rendering mit Autodesk Maya

### <a name="renderer-support"></a>Renderunterstützung

Zusätzlich zu den in Maya integrierten Renderern sind die folgenden Renderer Rendering-VM-Images verfügbar, und in der 3ds Max-Szenendatei kann darauf verwiesen werden:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Befehlszeile für Aufgabe

Der Befehlszeilenrenderer `renderer.exe` wird in der Befehlszeile für die Aufgabe verwendet. Der Befehlszeilenrenderer ist in der [Maya-Hilfe](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4) dokumentiert.

Im folgenden Beispiel wird eine Aufgabe für die Auftragsvorbereitung genutzt, um die Szenendateien und Medienobjekte in das Arbeitsverzeichnis für die Auftragsvorbereitung zu kopieren. Es wird ein Ausgabeordner verwendet, um das Renderingbild zu speichern, und Frame 10 wird gerendert.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Für das V-Ray-Rendering gibt die Maya-Szenendatei normalerweise V-Ray als Renderer an.  Dies kann auch in der Befehlszeile angegeben werden:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Für das Arnold-Rendering gibt die Maya-Szenendatei normalerweise Arnold als Renderer an.  Dies kann auch in der Befehlszeile angegeben werden:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer-Vorlagen

Auf Pool- und Auftragsvorlagen kann in Batch Explorer über den **Katalog** zugegriffen werden.  Die Quelldateien der Vorlagen sind im [Batch Explorer-Datenrepository auf GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya) verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die Pool- und Auftragsvorlagen aus dem [Datenrepository auf GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) mit Batch Explorer.  Erstellen Sie neue Vorlagen, oder ändern Sie eine der bereitgestellten Vorlagen, falls dies erforderlich ist.