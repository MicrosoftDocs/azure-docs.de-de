---
title: Kopieren neuer und geänderter Dateien nach „LastModifiedDate“
description: Erfahren Sie, wie Sie eine Lösungsvorlage verwenden können, um neue und geänderte Dateien mit Azure Data Factory nach „LastModifiedDate“ zu kopieren.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 99d90e4d93f0e4a70350a5a33a65700c3e14acb4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000809"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopieren neuer und geänderter Dateien nach „LastModifiedDate“ mit Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird eine Lösungsvorlage beschrieben, die Sie verwenden können, um neue und geänderte Dateien nur nach „LastModifiedDate“ aus einem dateibasierten Speicher in einen Zielspeicher zu kopieren. 

## <a name="about-this-solution-template"></a>Informationen zu dieser Lösungsvorlage

Diese Vorlage wählt die neuen und geänderten Dateien nur nach deren **LastModifiedDate**-Attributen aus und kopiert die ausgewählten Dateien anschließend von der Datenquelle zum Zieldatenspeicher.

Die Vorlage enthält eine Aktivität:
- **Copy** (Kopieren). Mit dieser Aktivität können Sie neue und geänderte Dateien nur nach „LastModifiedDate“ von einem Dateispeicher zu einem Zielspeicher kopieren.

Die Vorlage definiert sechs Parameter:
-  *FolderPath_Source* stellt den Ordnerpfad dar, über den Sie die Dateien vom Quellspeicher lesen können. Sie müssen den Standardwert durch Ihren eigenen Ordnerpfad ersetzen.
-  *Directory_Source* stellt den Unterordnerpfad dar, über den Sie die Dateien vom Quellspeicher lesen können. Sie müssen den Standardwert durch Ihren eigenen Unterordnerpfad ersetzen.
-  *FolderPath_Destination* stellt den Ordnerpfad des Zielspeichers dar, in den Sie die Dateien kopieren möchten. Sie müssen den Standardwert durch Ihren eigenen Ordnerpfad ersetzen.
-  *Directory_Destination* stellt den Unterordnerpfad des Zielspeichers dar, in den Sie die Dateien kopieren möchten. Sie müssen den Standardwert durch Ihren eigenen Unterordnerpfad ersetzen.
-  *LastModified_From* wird verwendet, um die Dateien auszuwählen, deren LastModifiedDate-Attribut größer oder gleich diesem datetime-Wert ist.  Wenn Sie nur die neuen Dateien auswählen möchten, die im letzten Vorgang nicht kopiert wurden, kann dieser datetime-Wert dem Zeitpunkt entsprechen, zu dem die Pipeline das letzte Mal ausgelöst wurde. Sie können den Standardwert „2019-02-01T00:00:00Z“ durch den erwarteten LastModifiedDate-Wert in der UTC-Zeitzone ersetzen. 
-  *LastModified_To* wird verwendet, um die Dateien auszuwählen, deren LastModifiedDate-Attribut kleiner als dieser datetime-Wert ist. Wenn Sie nur die neuen Dateien auswählen möchten, die im letzten Vorgang nicht kopiert wurden, kann dieser datetime-Wert der aktuellen Zeit entsprechen.  Sie können den Standardwert „2019-02-01T00:00:00Z“ durch den erwarteten LastModifiedDate-Wert in der UTC-Zeitzone ersetzen. 

## <a name="how-to-use-this-solution-template"></a>So verwenden Sie diese Lösungsvorlage

1. Navigieren Sie zur Vorlage **Copy new files only by LastModifiedDate** (Neue Dateien nur nach LastModifiedDate kopieren). Erstellen Sie eine **neue Verbindung** mit Ihrem Quellspeicher. Aus dem Quellspeicher sollen die Dateien kopiert werden.

    ![Erstellen einer neuen Verbindung mit der Quelle](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Erstellen Sie eine **neue Verbindung** mit Ihrem Zielspeicher. Die Dateien sollen in den Zielspeicher kopiert werden. 

    ![Erstellen einer neuen Verbindung mit dem Ziel](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Klicken Sie auf **Diese Vorlage verwenden**.

    ![„Diese Vorlage verwenden“](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Die im Bereich verfügbare Pipeline wird angezeigt, wie im folgenden Beispiel dargestellt:

    ![Pipeline anzeigen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Klicken Sie auf **Debuggen**, geben Sie den Wert für die **Parameter** ein, und klicken Sie auf **Fertig stellen**.  Auf der Abbildung wurden die Parameter folgendermaßen festgelegt:
   - **FolderPath_Source** = Quellordner
   - **Directory_Source** = Unterordner
   - **FolderPath_Destination** = Zielordner
   - **Directory_Destination** = Unterordner
   - **LastModified_From** =  2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    Im Beispiel werden die Dateien, die zwischen **2019-02-01T00:00:00Z** und **2019-03-01T00:00:00Z** geändert wurden, vom Quellpfad **sourcefolder/subfolder** in den Zielpfad **destinationfolder/subfolder** kopiert.  Sie können für diese Parameter eigene Werte eingeben.

    ![Führen Sie die Pipeline aus.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Überprüfen Sie das Ergebnis. Nur die Dateien werden angezeigt, die innerhalb des festgelegten Zeitraums geändert und in den Zielspeicher kopiert wurden.

    ![Überprüfen des Ergebnisses](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Nun können Sie einen Auslöser für rollierende Fenster hinzufügen, um diese Pipeline zu automatisieren. Dadurch kann die Pipeline neue und geänderte Dateien nur anhand von „LastModifiedDate“ regelmäßig kopieren.  Klicken Sie auf **Trigger hinzufügen**, und wählen Sie **Neu/Bearbeiten** aus.

    ![Screenshot mit hervorgehobener Menüoption „Neu/Bearbeiten“, die bei Auswahl von „Trigger hinzufügen“ angezeigt wird](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Wählen Sie im Fenster **Add Triggers** (Trigger hinzufügen) die Option **+ Neu** aus.

9. Wählen Sie **Rollierendes Fenster** als Auslösertyp aus, und legen Sie die Häufigkeit auf **Alle 15 Minuten** fest (Sie können das Intervall jederzeit ändern). Wählen Sie für das Feld „Aktiviert“ die Option **Ja** aus, und wählen Sie dann **OK** aus.

    ![Trigger erstellen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Legen Sie den Wert für die **Triggerausführungsparameter** wie im Folgenden veranschaulicht fest, und wählen Sie **Fertig stellen** aus.
    - **FolderPath_Source** = **Quellordner**.  Sie können diesen Wert durch Ihren Ordner im Quelldatenspeicher ersetzen.
    - **Directory_Source** = **Unterordner**.  Sie können diesen Wert durch Ihren Unterordner im Quelldatenspeicher ersetzen.
    - **FolderPath_Destination** = **Zielordner**.  Sie können diesen Wert durch Ihren Ordner im Zieldatenspeicher ersetzen.
    - **Directory_Destination** = **Unterordner**.  Sie können diesen Wert durch Ihren Unterordner im Zieldatenspeicher ersetzen.
    - **LastModified_From** =   **\@trigger().outputs.windowStartTime**.  Hierbei handelt es sich um eine Systemvariable des Auslösers, die den Zeitpunkt bestimmt, zu dem die Pipeline zuletzt ausgelöst wurde.
    - **LastModified_To** =  **\@trigger().outputs.windowEndTime**.  Hierbei handelt es sich um eine Systemvariable des Auslösers, die den Zeitpunkt bestimmt, zu dem die Pipeline dieses Mal ausgelöst wird.
    
    ![Eingabeparameter](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Wählen Sie **Alle veröffentlichen**.
    
    ![Alle veröffentlichen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Erstellen Sie neue Dateien im Quellordner des Quelldatenspeichers.  Warten Sie nun, bis die Pipeline automatisch ausgelöst wird. Sie werden feststellen, dass nur die neuen Dateien in den Zielspeicher kopiert werden.

13. Wählen Sie die Registerkarte **Überwachen** im linken Navigationsbereich aus, und warten Sie 15 Minuten, sofern die Häufigkeit für den Auslöser auf „Alle 15 Minuten“ festgelegt wurde. 

14. Überprüfen Sie das Ergebnis. Sie werden sehen, dass Ihre Pipeline automatisch alle 15 Minuten ausgelöst wird und dass bei jeder Ausführung der Pipeline nur die neuen oder geänderten Dateien aus dem Quellspeicher in den Zielspeicher kopiert werden.

    ![Screenshot der Ergebnisse, die bei Auslösen der Pipeline zurückgegeben werden](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Nächste Schritte

- [Einführung in den Azure Data Factory-Dienst](introduction.md)
