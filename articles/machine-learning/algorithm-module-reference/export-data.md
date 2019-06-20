---
title: 'Export Data (Daten exportieren): Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul „Export Data“ (Daten exportieren) in Azure Machine Learning Service verwenden, um Ergebnisse, Zwischendaten und Arbeitsdaten aus Ihren Experimenten in Cloudspeicherzielen außerhalb von Azure Machine Learning zu speichern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027672"
---
# <a name="export-data-module"></a>Modul „Export Data“ (Daten exportieren)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um Ergebnisse, Zwischendaten und Arbeitsdaten aus Ihren Experimenten in Cloudspeicherzielen außerhalb von Azure Machine Learning zu speichern.

Dieses Modul unterstützt das Exportieren oder Speichern von Daten in folgenden Clouddatendiensten:


- **Export to Azure Blob Storage** (Daten in Azure Blob Storage exportieren): Speichert Daten im Blob-Dienst in Azure. Daten im Blob-Dienst können öffentlich freigegeben oder in gesicherten Anwendungsdatenspeichern gespeichert werden.

  
## <a name="how-to-configure-export-data"></a>Gewusst wie: Konfigurieren von „Export Data“

1. Fügen Sie das Modul **Export Data** Ihrem Experiment in der Oberfläche hinzu. Sie finden dieses Modul in der Kategorie **Input and Output** (Eingabe und Ausgabe).

2. Verbinden Sie **Export Data** mit dem Modul, das die zu exportierenden Daten enthält.

3. Doppelklicken Sie auf **Export Data**, um den Bereich **Properties** (Eigenschaften) zu öffnen.

4. Wählen Sie für **Data destination** (Datenziel) den Typ des Cloudspeichers aus, in dem Sie Ihre Daten speichern möchten. Wenn Sie diese Option ändern, werden alle anderen Eigenschaften zurückgesetzt. Legen Sie deshalb diese Option zuerst fest!

5. Geben Sie einen Kontonamen und eine Authentifizierungsmethode an, die für den Zugriff auf das angegebene Speicherkonto erforderlich sind.

    **Export to Azure Blob Storage** (In Azure Blob Storage exportieren) ist die einzige Option in der privaten Vorschau. Im Folgenden erfahren Sie, wie Sie Einstellungen für das Modul festlegen.
    1. Der Blob-Dienst in Azure dient zum Speichern großer Datenmengen, einschließlich binärer Daten. Es gibt zwei Blob-Speichertypen: öffentliche Blobs und Blobs, die Anmeldeinformationen erfordern.

    2. Wählen Sie für **Authentication type** (Authentifizierungstyp) die Option **Public (SAS)** (Öffentlich, SAS) aus, wenn Sie sicher sind, dass der Speicher den Zugriff über eine SAS-URL unterstützt.

          Eine SAS-URL ist ein bestimmter URL-Typ, der mithilfe eines Azure Storage-Hilfsprogramms generiert werden kann und nur begrenzte Zeit zur Verfügung steht.  Die URL enthält alle Informationen, die für die Authentifizierung und für Downloads erforderlich sind.

        Unter **URI** geben oder fügen Sie den vollständigen URI ein, der das Konto und den öffentlichen Blob definiert.

        Als Dateiformate werden CSV und TSV unterstützt.

    3. Wählen Sie für private Konten **Account** (Konto) aus, und geben Sie den Kontonamen und Kontoschlüssel an, damit das Experiment Schreibzugriff auf das Speicherkonto erhält.

         - **Kontoname**: Geben oder fügen Sie den Namen des Kontos ein, unter dem Sie die Daten speichern möchten. Wenn die vollständige URL des Speicherkontos z.B. `http://myshared.blob.core.windows.net` lautet, geben Sie `myshared` ein.

        - **Account key**: Fügen Sie den Speicherzugriffsschlüssel ein, der dem Konto zugeordnet ist.

        -  **Path to container, directory, or blob** (Pfad zum Container, Verzeichnis oder Blob): Geben Sie den Namen des Blobs ein, in dem die exportierten Daten gespeichert werden sollen. Wenn Sie die Ergebnisse Ihres Experiments in einem neuen Blob namens **results01.csv** im Container **predictions** unter einem Konto namens **mymldata** speichern möchten, lautet die vollständige URL des Blobs: `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Daher würden Sie den Container- und Blob-Namen im Feld **Path to container, directory, or blob** wie folgt eingeben: `predictions/results01.csv`

        - Wenn Sie den Namen eines Blobs angeben, der noch nicht vorhanden ist, wird der Blob in Azure für Sie erstellt.

       -  Beim Schreiben in einen bestehenden Blob können Sie mithilfe der Eigenschaft **Azure blob storage write mode** (Azure Blob Storage im Schreibmodus) angeben, dass der aktuelle Inhalt des Blobs überschrieben werden soll. Diese Eigenschaft wird standardmäßig auf **Error** (Fehler) festgelegt. Das bedeutet, dass ein Fehler gemeldet wird, sobald eine bestehende Blob-Datei mit demselben Namen gefunden wird.


    4. Wählen Sie für **File format for blob file** (Dateiformat für Blob-Datei) das Format aus, in dem die Daten gespeichert werden sollen.

        - **CSV**: Durch Trennzeichen getrennte Werte (CSV) ist das Standardspeicherformat. Um Spaltenüberschriften zusammen mit den Daten zu exportieren, wählen Sie die Option **Write blob header row** (Blob-Kopfzeile schreiben) aus.  Weitere Informationen zum CSV-Format (durch Trennzeichen getrennt), das in Azure Machine Learning verwendet wird, finden Sie unter [Convert to CSV (In CSV konvertieren)](./convert-to-csv.md).

        - **TSV**: Durch Tabstopps getrennte Werte (TSV) ist ein Format, das mit vielen Machine Learning-Tools kompatibel ist. Um Spaltenüberschriften zusammen mit den Daten zu exportieren, wählen Sie die Option **Write blob header row** (Blob-Kopfzeile schreiben) aus.  

 
    5. **Use cached results** (Zwischengespeicherte Ergebnisse verwenden): Wählen Sie diese Option, wenn Sie verhindern möchten, dass Ergebnisse bei jeder Ausführung des Experiments erneut in die Blob-Datei geschrieben werden. Sofern es keine weiteren Änderungen an den Modulparametern gibt, werden die Ergebnisse vom Experiment in zwei Fällen in die Datei geschrieben: bei der ersten Ausführung des Moduls oder wenn sich Daten ändern.

    6. Führen Sie das Experiment aus.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 