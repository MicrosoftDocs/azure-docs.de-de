---
title: Deltakopiervorgänge aus einer Datenbank mithilfe einer Steuertabelle
description: Hier erfahren Sie, wie Sie eine Lösungsvorlage verwenden, um mit Azure Data Factory nur neue oder aktualisierte Zeilen inkrementell aus einer Datenbank zu kopieren.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: b3b5679b254a07b275cc7fd1295ba4ca5b405fbc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362011"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Deltakopiervorgänge aus einer Datenbank mit einer Steuertabelle

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird eine Vorlage zum inkrementellen Laden neuer oder aktualisierter Zeilen aus einer Datenbanktabelle in Azure erläutert, bei der eine externe Steuertabelle verwendet wird, die einen hohen Grenzwert speichert.

Diese Vorlage setzt voraus, dass das Schema der Quelldatenbank eine Zeitstempelspalte oder einen Inkrementierungsschlüssel enthält, um die neuen oder aktualisierten Zeilen identifizieren zu können.

>[!NOTE]
> Wenn eine Zeitstempelspalte in der Quelldatenbank vorhanden ist, mit der neue oder aktualisierte Zeilen ermittelt werden sollen, Sie aber keine externe Steuertabelle für den Deltakopiervorgang erstellen möchten, können Sie stattdessen das [Azure Data Factory-Tool zum Kopieren von Daten](copy-data-tool.md) verwenden, um eine Pipeline zu erstellen. Das Tool verwendet einen von einem Trigger geplanten Zeitpunkt als Variable, um neue Zeilen aus der Quelldatenbank zu lesen.

## <a name="about-this-solution-template"></a>Informationen zu dieser Lösungsvorlage

Diese Vorlage ruft zuerst den alten Grenzwert ab und vergleicht ihn mit dem aktuellen Grenzwert. Auf der Grundlage eines Vergleichs der beiden Grenzwerte werden dann nur die Änderungen aus der Quelldatenbank kopiert. Zuletzt wird der neue hohe Grenzwert in einer externen Steuertabelle gespeichert, damit er beim nächsten Laden von Deltadaten verfügbar ist.

Die Vorlage enthält vier Aktivitäten:
- Eine **Suchaktivität** zum Abrufen des alten hohen Grenzwerts, der in einer externen Steuertabelle gespeichert ist.
- Eine weitere **Suchaktivität** zum Abrufen des aktuellen hohen Grenzwerts aus der Quelldatenbank.
- Die **Kopieraktivität** kopiert nur Änderungen aus der Quelldatenbank in den Zielspeicher. Die Abfrage für das Ermitteln der Änderungen in der Quelldatenbank ähnelt SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” und TIMESTAMP_Column <= “current high-watermark”.
- Eine **SqlServerStoredProcedure**-Aktivität, um den aktuellen hohen Grenzwert in eine externe Steuertabelle zu schreiben, damit er beim nächsten Deltakopiervorgang zur Verfügung steht.

Die Vorlage definiert die folgenden Parameter:
- *Data_Source_Table_Name* entspricht der Tabelle aus der Quelldatenbank, aus der Sie Daten laden möchten.
- *Data_Source_WaterMarkColumn* entspricht dem Spaltennamen in der Quelltabelle, der zum Identifizieren der neuen oder aktualisierten Zeilen verwendet werden kann. Der Typ dieser Spalte ist üblicherweise *datetime*, *int* oder ein ähnlicher Typ.
- *Data_Destination_Container* entspricht dem Stammpfad des Orts, an den die Daten in Ihrem Zielspeicher kopiert werden.
- *Data_Destination_Directory* entspricht dem Verzeichnispfad unter dem Stammelement des Orts, an den die Daten in Ihrem Zielspeicher kopiert werden.
- *Data_Destination_Table_Name* entspricht dem Ort, an den die Daten in Ihren Zielspeicher kopiert werden (zutreffend, wenn „Azure Synapse Analytics“ als Datenziel ausgewählt ist).
- *Data_Destination_Folder_Path* entspricht dem Ort, an den die Daten in Ihren Zielspeicher kopiert werden (zutreffend, wenn "Dateisystem" oder "Azure Data Lake Storage Gen1" als Datenziel ausgewählt ist).
- *Control_Table_Table_Name* entspricht der externen Steuertabelle, in der der hohe Grenzwert gespeichert werden soll.
- *Control_Table_Column_Name* entspricht der Spalte in der externen Steuertabelle, in der der hohe Grenzwert gespeichert werden soll.

## <a name="how-to-use-this-solution-template"></a>So verwenden Sie diese Lösungsvorlage

1. Untersuchen Sie die Quelltabelle, die Sie laden möchten, und definieren Sie die Spalte für den hohen Grenzwert, auf deren Grundlage die neuen oder aktualisierten Zeilen identifiziert werden können. Der Typ dieser Spalte kann *datetime*, *int* oder ein ähnlicher Typ sein. Der Wert dieser Spalte wird erhöht, wenn neue Zeilen hinzugefügt werden. In der folgenden Beispielquelltabelle (data_source_table) können Sie die Spalte *LastModifytime* als Spalte für den hohen Grenzwert verwenden.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Erstellen Sie in einer SQL Server- oder Azure SQL-Datenbank-Instanz eine Steuertabelle, um den hohen Grenzwert für das Laden von Deltadaten zu speichern. Im folgenden Beispiel ist der Name der Steuertabelle *watermarktable*. In der Spalte *WatermarkValue* vom Typ *datetime* dieser Tabelle wird der hohe Grenzwert gespeichert.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Erstellen Sie eine gespeicherte Prozedur in der gleichen SQL Server- oder Azure SQL-Datenbank-Instanz, mit der Sie auch die Steuertabelle erstellt haben. Die gespeicherte Prozedur dient dazu, den neuen hohen Grenzwert in der externen Steuertabelle zu speichern, damit er beim nächsten Laden von Deltadaten zur Verfügung steht.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Wechseln Sie zur Vorlage **Delta copy from Database** (Deltakopie aus einer Datenbank). Stellen Sie eine **neue Verbindung** mit der Quelldatenbank her, aus der Sie Daten kopieren möchten.

    ![Erstellen einer neuen Verbindung mit der Quelltabelle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Stellen Sie eine **neue Verbindung** mit dem Zieldatenspeicher her, in den Sie die Daten kopieren möchten.

    ![Erstellen einer neuen Verbindung mit der Zieltabelle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Stellen Sie eine **neue Verbindung** mit der externen Steuertabelle und der gespeicherten Prozedur her, die Sie in Schritt 2 und Schritt 3 erstellt haben.

    ![Erstellen einer neuen Verbindung mit dem Datenspeicher der Steuertabelle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Klicken Sie auf **Diese Vorlage verwenden**.
    
8. Daraufhin wird die verfügbare Pipeline wie im folgenden Beispiel angezeigt:
  
    ![Überprüfen der Pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Klicken Sie auf **Gespeicherte Prozedur**. Wählen Sie **[dbo].[update_watermark]** für **Name der gespeicherten Prozedur** aus. Klicken Sie auf **Import parameter** (Importparameter), und wählen Sie **Dynamischen Inhalt hinzufügen** aus.  

    ![Festlegen der Aktivität „Gespeicherte Prozedur“](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Schreiben Sie den Inhalt **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** , und klicken Sie dann auf **Fertig stellen**.  

    ![Schreiben des Inhalts für die Parameter der gespeicherten Prozedur](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Klicken Sie auf **Debuggen**, geben Sie die **Parameter** ein, und klicken Sie dann auf **Fertig stellen**.

    ![Auf **Debuggen** klicken](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Die angezeigten Ergebnisse entsprechen etwa folgendem Beispiel:

    ![Überprüfen des Ergebnisses](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Sie können neue Zeilen in Ihrer Quelltabelle erstellen. Hier sehen Sie ein SQL-Beispiel für die Erstellung neuer Zeilen:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Klicken Sie auf **Debuggen**, geben Sie die **Parameter** ein, und klicken Sie dann auf **Fertig stellen**, um die Pipeline erneut auszuführen.

    Sie sehen, dass nur neue Zeilen in das Ziel kopiert wurden.

15. (Optional:) Wenn Sie Azure Synapse Analytics als Zielspeicher für die Daten ausgewählt haben, müssen Sie eine Verbindung mit Azure Blob Storage für den Stagingprozess angeben. Dabei handelt es sich um eine Anforderung von Azure Synapse Analytics PolyBase. Die Vorlage generiert einen Containerpfad für Sie. Überprüfen Sie nach Ausführung der Pipeline, ob der Container im Blobspeicher erstellt wurde.
    
    ![Konfigurieren von PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Nächste Schritte

- [Ausführen von Massenkopiervorgängen mit Steuertabelle für eine Datenbank](solution-template-bulk-copy-with-control-table.md)
- [Kopieren von Dateien aus mehreren Containern mit Azure Data Factory](solution-template-copy-files-multiple-containers.md)
