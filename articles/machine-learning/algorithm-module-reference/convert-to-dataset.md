---
title: 'Convert to Dataset: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Convert to Dataset im Azure Machine Learning-Designer Dateneingaben in das interne Datasetformat konvertieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 671a8f7c6fa4c20ef4fc88f57d4a946a84614389
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420868"
---
# <a name="convert-to-dataset"></a>Konvertieren in ein Dataset

In diesem Artikel erfahren Sie, wie Sie das Modul „Convert to Dataset“ (In Dataset konvertieren) im Azure Machine Learning-Designer verwenden, um Daten für eine Pipeline in das vom Designer verwendete interne Format zu konvertieren.
  
In den meisten Fällen ist keine Konvertierung erforderlich. Azure Machine Learning konvertiert Daten implizit in das native Datasetformat, wenn ein Vorgang für die Daten ausgeführt wird. 

Es empfiehlt sich, Daten im Datasetformat zu speichern, wenn Sie Daten normalisiert oder bereinigt haben und sicherstellen möchten, dass die Änderungen in anderen Pipelines verwendet werden.  
  
> [!NOTE]
> „Convert to Dataset“ (In Dataset konvertieren) ändert nur das Format der Daten. Es wird keine neue Kopie der Daten im Arbeitsbereich gespeichert. Um das Dataset zu speichern, doppelklicken Sie auf den Ausgabeport, wählen Sie **Als Dataset speichern** aus, und geben Sie einen neuen Namen ein.  
  
## <a name="how-to-use-convert-to-dataset"></a>Verwenden von Convert to Dataset  

Es empfiehlt sich, das Dataset mithilfe des Moduls [Edit Metadata](edit-metadata.md) (Metadaten bearbeiten) vorzubereiten, bevor Sie „Convert to Dataset“ (In Dataset konvertieren) verwenden. Sie können Spaltennamen hinzufügen oder ändern, Datentypen anpassen und andere ggf. erforderliche Änderungen vornehmen.

1.  Fügen Sie Ihrer Pipeline das Modul „Convert to Dataset“ (In Dataset konvertieren) hinzu. Das Modul befindet sich im Designer in der Kategorie **Datentransformation**. 

2. Verbinden Sie es mit einem beliebigen Modul, das ein Dataset ausgibt.   

    Solange die Daten [tabellarisch](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) sind, können Sie sie in ein Dataset konvertieren. Dies schließt Daten ein, die mit [Import Data](import-data.md) (Daten importieren) geladen oder mithilfe von [Enter Data Manually](enter-data-manually.md) (Daten manuell eingeben) erstellt wurden, sowie Datasets, die mithilfe von [Apply Transformation](apply-transformation.md) (Transformation anwenden) transformiert wurden.

3.  Geben Sie in der Dropdownliste **Aktion** an, ob vor dem Speichern des Datasets eine Datenbereinigung durchgeführt werden soll:  
  
    - **Keine:**  Daten unverändert verwenden.  
  
    - **SetMissingValue:** Legen Sie einen bestimmten Wert auf einen fehlenden Wert im Dataset fest. Der Standardplatzhalter ist das Fragezeichen (?), Sie können jedoch die Option **Custom missing value** (Benutzerdefinierter fehlender Wert) verwenden, um einen anderen Wert einzugeben. Wenn Sie für **Custom missing value** (Benutzerdefinierter fehlender Wert) z. B. den Wert **Taxi** eingeben, werden alle Vorkommen von **Taxi** im Dataset in den fehlenden Wert geändert.
  
    - **ReplaceValues:** Verwenden Sie diese Option, um einen einzelnen exakten Wert anzugeben, der durch einen anderen exakten Wert ersetzt werden soll. Sie können fehlende oder benutzerdefinierte Werte ersetzen, indem Sie die Methode **Ersetzen** festlegen:

      - **Missing**: Wählen Sie diese Option aus, um fehlende Werte im Eingabedataset zu ersetzen. Geben Sie für **Neuer Wert** den Wert ein, durch den die fehlenden Werte ersetzt werden sollen.
      - **Benutzerdefiniert**: Wählen Sie diese Option aus, um benutzerdefinierte Werte im Eingabedataset zu ersetzen. Geben Sie für **Benutzerdefinierter Wert** den Wert ein, den Sie suchen möchten. Wenn die Daten z. B. die Zeichenfolge `obs` als Platzhalter für fehlende Werte enthalten, geben Sie `obs` ein. Geben Sie für **Neuer Wert** den neuen Wert ein, durch den die ursprüngliche Zeichenfolge ersetzt werden soll.
  
    Beachten Sie, dass der Vorgang **ReplaceValues** nur für exakte Übereinstimmungen gilt. Diese Zeichenfolgen sind z. B. nicht betroffen: `obs.`, `obsolete`.  
 
  
5.  Übermitteln Sie die Pipeline.  

## <a name="results"></a>Ergebnisse

+  Um das resultierende Dataset unter einem neuen Namen zu speichern, wählen Sie das Symbol **Dataset registrieren** unter der Registerkarte **Ausgaben** im rechten Bereich des Moduls aus.  
  
## <a name="technical-notes"></a>Technische Hinweise  

-   Jedes Modul, das ein Dataset als Eingabe akzeptiert, akzeptiert auch Daten im CSV- oder TSV-Format. Vor der Ausführung von Modulcode werden die Eingaben zunächst vorverarbeitet. Die Vorverarbeitung entspricht der Ausführung des Moduls „Convert to Dataset“ (In Dataset konvertieren) für die Eingabe.  
  
-   Das SVMLight-Format kann nicht in ein Dataset konvertiert werden.  
  
-   Wenn Sie einen benutzerdefinierten Ersetzungsvorgang angeben, wird der Such- und Ersetzungsvorgang auf vollständige Werte angewendet. Teilübereinstimmungen sind nicht zulässig. Sie können z. B. eine 3 durch „-1“ oder durch „33“ ersetzen, es ist jedoch nicht möglich, eine 3 in einer zweistelligen Zahl wie 35 zu ersetzen.  
  
-   Bei benutzerdefinierten Ersetzungsvorgängen tritt bei der Ersetzung automatisch ein Fehler auf, wenn Sie ein Ersetzungszeichen verwenden, das nicht dem aktuellen Datentyp der Spalte entspricht.  

  
## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an.