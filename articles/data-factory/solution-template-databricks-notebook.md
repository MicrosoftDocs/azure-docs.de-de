---
title: Transformieren von Daten mithilfe von Databricks in Azure Data Factory | Microsoft-Dokumentation
description: Sie erfahren, wie in Azure Data Factory eine Lösungsvorlage zum Transformieren von Daten mithilfe eines Databricks-Notebooks verwendet wird.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 562ce675acc43002ce468d60f8a8c412410be86c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60395228"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Transformieren von Daten mithilfe von Databricks in Azure Data Factory

In diesem Tutorial erstellen Sie in Data Factory eine End-to-End-Pipeline, welche die Aktivitäten **Nachschlagen**, **Kopieren** und **Databricks-Notebook** enthält.

-   Mit der **Nachschlagen**- oder GetMetadata-Aktivität wird sichergestellt, dass das Quelldataset für die nachgeschaltete Nutzung bereit ist, bevor der Kopier- und Analyseauftrag ausgelöst wird.

-   Mit der **Kopieraktivität** wird die Quelldatei/das Quelldataset in den Senkenspeicher kopiert. Der Senkenspeicher wird als DBFS im Databricks-Notebook bereitgestellt, damit das Dataset direkt von Spark genutzt werden kann.

-   Die **Databricks-Notebook-Aktivität** löst das Databricks-Notebook aus, welches das Datasets transformiert und es dann einem verarbeiteten Ordner/SQL-DW hinzufügt.

Um diese Vorlage einfach zu halten, wird damit kein geplanter Trigger erstellt. Sie können diese Funktion bei Bedarf hinzufügen.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Voraussetzungen

1.  Erstellen Sie ein **Blobspeicherkonto** und einen Container namens `sinkdata`, der als **Senke** verwendet wird. Notieren Sie den **Namen des Speicherkontos**, den **Containernamen** und den **Zugriffsschlüssel**, da später in der Vorlage darauf verwiesen wird.

2.  Vergewissern Sie sich, dass Sie einen **Azure Databricks-Arbeitsbereich** haben, oder erstellen Sie einen neuen.

1.  **Importieren Sie das Notebook für ETL**. Importieren Sie das folgende Transformations-Notebook in den Databricks-Arbeitsbereich. (Es muss sich nicht am gleichen Speicherort wie unten befinden, merken Sie sich aber den verwendeten Pfad für später.) Importieren Sie das Notebook aus der folgenden URL, indem Sie diese URL in das URL-Feld eingeben: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Wählen Sie **Importieren** aus.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Jetzt aktualisieren wir das **Transformations**-Notebook mit den **Informationen für Ihre Speicherverbindung** (Name und Zugriffsschlüssel). Ersetzen Sie **Befehl 5** im importierten Notebook oben durch den folgenden Codeausschnitt, nachdem Sie die hervorgehobenen Werte ersetzt haben. Stellen Sie sicher, dass es sich bei diesem Konto um das zuvor erstellte Speicherkonto handelt und dass es den Container `sinkdata` enthält.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1.  Generieren Sie ein **Databricks Zugriffstoken**, damit Data Factory auf Databricks zugreifen kann. **Speichern Sie das Zugriffstoken** für die spätere Verwendung bei der Erstellung eines mit Databricks verknüpften Diensts, der in etwa folgendermaßen aussieht: dapi32db32cbb4w6eee18b7d87e45exxxxxx.

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Erstellen von verknüpfte Diensten und Datasets

1.  Erstellen Sie neue **verknüpfte Dienste** auf der Data Factory-Benutzeroberfläche, indem Sie zu *Verbindungen > Verknüpfte Dienste + neu* wechseln.

    1.  **Quelle**: für den Zugriff auf Quelldaten. Für dieses Beispiel können Sie den öffentlichen Blobspeicher mit den Quelldateien verwenden.

        Wählen Sie **Blob Storage**, und verwenden Sie den folgenden **SAS-URI** zum Herstellen der Verbindung mit dem Quellspeicher (schreibgeschützter Zugriff).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Senke**: zum Hineinkopieren von Daten.

        Wählen Sie ein Speicherkonto aus, das in Voraussetzung 1 im verknüpften Senkendienst erstellt wurde.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks**: zum Herstellen der Verbindung mit dem Databricks-Cluster.

        Erstellen Sie mithilfe des in Voraussetzung 2.c generierten Zugriffsschlüssels einen verknüpften Databricks-Dienst. Wenn Sie einen *interaktiven Cluster* haben, können Sie diesen auswählen. (In diesem Beispiel wird die Option *Neuer Auftragscluster* verwendet.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  **Erstellen von Datasets**

    1.  Erstellen Sie **„sourceAvailability_Dataset“** , um zu überprüfen, ob Quelldaten verfügbar sind.

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Quelldataset**: zum Kopieren der Quelldaten (mit Binärkopie)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Senkendataset**: zum Kopieren in die Senke/an den Zielspeicherort

        1.  Verknüpfter Dienst: Wählen Sie das in 1.b erstellte Element „sinkBlob_LS“ aus.

        2.  Dateipfad: „sinkdata/staged_sink“

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Erstellen von Aktivitäten

1.  Erstellen Sie die Nachschlageaktivität „**Availability flag**“, um eine Überprüfung der Quellverfügbarkeit durchzuführen („Lookup“ oder „GetMetadata“ kann verwendet werden). Wählen Sie das in 2.a erstellte Element „sourceAvailability_Dataset“ aus.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Erstellen Sie die Kopieraktivität „**file-to-blob**“, um das Dataset aus der Quelle in die Senke zu kopieren. In diesem Fall handelt es sich bei den Daten um eine Binärdatei. Auf den folgenden Screenshots sehen Sie Quell- und Senkenkonfigurationen in der Kopieraktivität.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definieren von **Pipelineparametern**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Erstellen einer **Databricks-Aktivität**

    Wählen Sie den in einem vorherigen Schritt erstellten verknüpften Dienst aus.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Konfigurieren Sie die **Einstellungen**. Erstellen Sie **Basisparameter** wie im Screenshot dargestellt, und erstellen Sie Parameter, die aus Data Factory in Databricks-Notebook übergeben werden. Suchen Sie den in **Voraussetzung 2** hochgeladenen **richtigen Notebook-Pfad**, und **wählen** Sie ihn aus.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Ausführen der Pipeline**. Sie finden einen Link zu Databricks-Protokollen, um ausführlichere Spark-Protokolle zu erhalten.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Sie können die Datendatei auch mit dem Speicher-Explorer überprüfen. (Für die Korrelation mit Data Factory-Pipelineausführungen, wird in diesem Beispiel die Pipelineausführungs-ID aus Data Factory dem Ausgabeordner angefügt. So können Sie die bei den einzelnen Ausführungen generierten Dateien nachverfolgen.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in den Azure Data Factory-Dienst](introduction.md)
