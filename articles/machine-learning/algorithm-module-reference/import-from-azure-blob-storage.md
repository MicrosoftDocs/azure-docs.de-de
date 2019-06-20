---
title: 'Importieren aus Azure Blob Storage: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: In diesem Thema wird beschrieben, wie Sie das Modul „Importieren aus Azure Blob Storage“ im Azure Machine Learning Service verwenden, um Daten aus dem Azure Blob Storage zu lesen, um sie in einem Machine Learning-Experiment zu verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027830"
---
# <a name="import-from-azure-blob-storage-module"></a>Importieren aus einem Azure Blob Storage-Modul

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für den Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um Daten aus Azure Blob Storage zu lesen, die Sie dann in einem Machine Learning-Experiment verwenden können.  

Azure Blob Service dient zum Speichern großer Datenmengen, einschließlich binärer Daten. Azure-Blobs können von überall aus über HTTP oder HTTPS aufgerufen werden. Je nach Art des Blobspeichers kann eine Authentifizierung erforderlich sein. 

- Öffentliche Blobs können von jedem oder von Benutzern mit einer SAS-URL aufgerufen werden.
- Für private Blobs sind Anmeldeinformationen erforderlich.

Das Importieren aus dem Blobspeicher erfordert, dass Daten in Blobs gespeichert werden, die das **Blockblob**-Format verwenden. Die im Blob gespeicherten Dateien müssen entweder kommagetrennte (CSV) oder tabulatorgetrennte (TSV) Formate verwenden. Wenn Sie die Datei lesen, werden die Datensätze und alle zutreffenden Attributüberschriften zeilenweise als Datensatz in den Arbeitsspeicher geladen.


Es wird dringend empfohlen, vor dem Importieren Datenprofile zu erstellen, um sicherzustellen, dass das Schema wie erwartet ausfällt. Beim Importvorgang werden einige Kopfzeilen durchsucht, um das Schema zu bestimmen, aber spätere Zeilen können zusätzliche Spalten oder Daten enthalten, die Fehler verursachen.



## <a name="manually-set-properties-in-the-import-data-module"></a>Manuelles Festlegen der Eigenschaften im Modul „Daten importieren“

Die folgenden Schritte beschreiben, wie Sie die Importquelle manuell konfigurieren.

1. Fügen Sie das Modul **Daten importieren** zu Ihrem Experiment hinzu. Sie finden dieses Modul in der Schnittstelle unter **Dateneingabe und -ausgabe**.

2. Wählen Sie **Azure Blob Storage** als **Datenquelle** aus.

3. Wählen Sie **Öffentlich (SAS-URL)** als **Authentifizierungstyp** aus, wenn Sie wissen, dass die Informationen als öffentliche Datenquelle bereitgestellt wurden. Eine SAS-URL ist eine zeitgebundene URL für den öffentlichen Zugriff, die Sie mit einem Azure Storage-Hilfsprogramm generieren können.

    Andernfalls wählen Sie **Konto** aus.

4. Wenn sich Ihre Daten in einem **öffentlichen** Blob befinden, auf den über eine SAS-URL zugegriffen werden kann, benötigen Sie keine zusätzlichen Anmeldeinformationen, da die URL-Zeichenfolge alle Informationen enthält, die für den Download und die Authentifizierung erforderlich sind.

    Im Feld **URI** tippen oder fügen Sie die vollständige URI ein, die das Konto und den öffentlichen Blob definiert.



5. Wenn sich Ihre Daten in einem **privaten** Konto befinden, müssen Sie Anmeldeinformationen angeben, einschließlich des Kontonamens und des Schlüssels.

    - Für **Kontoname** tippen oder fügen Sie den Namen des Kontos ein, das den Blob enthält, auf den Sie zugreifen möchten.

        Wenn die vollständige URL des Speicherkontos z.B. `http://myshared.blob.core.windows.net` lautet, geben Sie `myshared` ein.

    - Fügen Sie für **Kontenschlüssel** den Speicherzugriffsschlüssel ein, der dem Konto zugeordnet ist.

        Wenn Sie den Zugriffsschlüssel nicht kennen, lesen Sie den Abschnitt „Verwalten Ihrer Azure-Speicherkonten“ in diesem Artikel: [Informationen zu Azure-Speicherkonten](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Geben Sie für **Pfad zum Container, Verzeichnis oder Blob** den Namen des Blobs ein, den Sie abrufen möchten.

    Angenommen, Sie haben eine Datei namens **data01.csv** in den Container **trainingdata** in einem Konto namens **mymldata** hochgeladen, dann wäre die vollständige URL für die Datei: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`.

    Daher würden Sie im Feld **Pfad zum Container, Verzeichnis oder Blob** Folgendes eingeben: `trainingdata/data01.csv`.

    Um mehrere Dateien zu importieren, können Sie die Platzhalterzeichen `*` (Sternchen) oder `?` (Fragezeichen) verwenden.

    Angenommen, der Container `trainingdata` enthält mehrere Dateien eines kompatiblen Formats, dann könnten Sie beispielsweise die folgende Spezifikation verwenden, um alle Dateien zu lesen, die mit `data` beginnen, und sie zu einem einzigen Datensatz verketten:

    `trainingdata/data*.csv`

    In Containernamen können Sie keine Platzhalter verwenden. Wenn Sie Dateien aus mehreren Containern importieren müssen, verwenden Sie eine separate Instanz des Moduls **Daten importieren** für jeden Container, und führen Sie die Datensätze dann mit dem Modul [Zeilen hinzufügen](./add-rows.md) zusammen.

    > [!NOTE]
    > Wenn Sie die Option **Zwischengespeicherte Ergebnisse verwenden** ausgewählt haben, lösen Änderungen, die Sie an den Dateien im Container vornehmen, keine Aktualisierung der Daten im Experiment aus.

7. Wählen Sie für **Blobdateiformat** eine Option aus, die das Format der im Blob gespeicherten Daten angibt, damit Azure Machine Learning die Daten entsprechend verarbeiten kann. Die folgenden Formate werden unterstützt:

    - **CSV**: Kommagetrennte Werte (CSV) sind das Standardspeicherformat für den Export und Import von Dateien in Azure Machine Learning. Wenn die Daten bereits eine Kopfzeile enthalten, vergewissern Sie sich, dass Sie die Option **Die Datei enthält eine Kopfzeile** ausgewählt haben. Ansonsten wird die Kopfzeile als Datenzeile behandelt.

       

    - **TSV**: Tabulatorgetrennte Werte (TSV) sind ein von vielen Machine Learning-Tools verwendetes Format. Wenn die Daten bereits eine Kopfzeile enthalten, vergewissern Sie sich, dass Sie die Option **Die Datei enthält eine Kopfzeile** ausgewählt haben. Ansonsten wird die Kopfzeile als Datenzeile behandelt.

       

    - **ARFF**: Dieses Format unterstützt das Importieren von Dateien in dem vom Weka-Toolset verwendeten Format. 

   

8. Führen Sie das Experiment aus.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 