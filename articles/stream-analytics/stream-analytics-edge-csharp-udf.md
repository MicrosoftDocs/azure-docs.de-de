---
title: 'Tutorial: Schreiben benutzerdefinierter C#-Funktionen für Azure Stream Analytics-Aufträge in Visual Studio (Vorschauversion)'
description: In diesem Tutorial wird beschrieben, wie Sie benutzerdefinierte C#-Funktionen für Stream Analytics-Aufträge in Visual Studio schreiben.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: d53f13cb740b3feb39dc64ce012ff320afbb1db5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130492"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Tutorial: Schreiben einer benutzerdefinierten C#-Funktion für einen Azure Stream Analytics-Auftrag (Vorschauversion)

Mit benutzerdefinierten C#-Funktionen (UDFs), die in Visual Studio erstellt wurden, können Sie die Azure Stream Analytics-Abfragesprache um eigene Funktionen erweitern. Sie können vorhandenen Code wiederverwenden (z.B. DLLs) und mit C# mathematische bzw. komplexe Logik verwenden. Zum Implementieren von UDFs stehen drei Methoden zur Auswahl: CodeBehind-Dateien in einem Stream Analytics-Projekt, benutzerdefinierte Funktionen aus einem lokalen C#-Projekt oder UDFs aus einem vorhandenen Paket eines Speicherkontos. In diesem Tutorial wird die CodeBehind-Methode genutzt, um eine grundlegende C#-Funktion zu implementieren. Das UDF-Feature für Stream Analytics-Aufträge befindet sich derzeit in der Vorschauphase und sollte nicht für Produktionsworkloads verwendet werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer benutzerdefinierten C#-Funktion mit CodeBehind
> * Lokales Testen Ihres Stream Analytics-Auftrags
> * Veröffentlichen Ihres Auftrags in Azure

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen.
* Installieren Sie die [Stream Analytics-Tools für Visual Studio](stream-analytics-tools-for-visual-studio-install.md) und die Workload **Azure-Entwicklung** oder **Datenspeicherung und -verarbeitung**.
* Sehen Sie sich den vorhandenen [Entwicklungsleitfaden zu Stream Analytics Edge](stream-analytics-tools-for-visual-studio-edge-jobs.md) an, wenn Sie einen IoT Edge-Auftrag erstellen.

## <a name="create-a-container-in-your-azure-storage-account"></a>Erstellen eines Containers in Ihrem Azure Storage-Konto

Der von Ihnen erstellte Container wird zum Speichern des kompilierten C#-Pakets verwendet. Wenn Sie einen Edge-Auftrag erstellen, wird dieses Speicherkonto auch zum Bereitstellen des Pakets auf Ihrem IoT Edge-Gerät verwendet. Verwenden Sie einen dedizierten Container für jeden Stream Analytics-Auftrag. Die Wiederverwendung desselben Containers für mehrere Stream Analytics-Edge-Aufträge wird nicht unterstützt. Wenn Sie bereits über ein Speicherkonto mit vorhandenen Containern verfügen, können Sie diese verwenden. Wenn nicht, müssen Sie einen [neuen Container erstellen](../storage/blobs/storage-quickstart-blobs-portal.md). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Erstellen eines Stream Analytics-Projekts in Visual Studio

1. Starten Sie Visual Studio.

2. Wählen Sie **Datei > Neu > Projekt** aus.

3. Wählen Sie in der Vorlagenliste links die Option **Stream Analytics** und dann **Azure Stream Analytics Edge Application** (Azure Stream Analytics-Edge-Anwendung) oder **Azure Stream Analytics Application** (Azure Stream Analytics-Anwendung) aus.

4.  Geben Sie **Name** , **Speicherort** und **Projektmappenname** für Ihr Projekt ein, und klicken Sie auf **OK**.

    ![Erstellen eines Azure Stream Analytics-Edge-Projekts in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Konfigurieren des Assembly-Paketpfads

1. Öffnen Sie Visual Studio, und navigieren Sie zum **Projektmappen-Explorer**.

2. Doppelklicken Sie auf die Datei `EdgeJobConfig.json` für die Auftragskonfiguration.

3. Erweitern Sie den Abschnitt mit der **benutzerdefinierten Codekonfiguration** , und geben Sie die folgenden vorgeschlagenen Werte für die Konfiguration an:

   |**Einstellung**|**Empfohlener Wert**|
   |-------|---------------|
   |Global Storage Settings Resource (Ressource für globale Speichereinstellungen)|Datenquelle aus aktuellem Konto wählen|
   |Global Storage Settings Subscription (Abonnement für globale Speichereinstellungen)| <Ihr Abonnement>|
   |Global Storage Settings Storage Account (Speicherkonto für globale Speichereinstellungen)| <Ihr Speicherkonto>|
   |Custom Code Storage Settings Resource (Ressource für Speichereinstellungen für benutzerdefinierten Code)|Datenquelle aus aktuellem Konto wählen|
   |Custom Code Storage Settings Storage Account (Speicherkonto für Speichereinstellungen für benutzerdefinierten Code)|<Ihr Speicherkonto>|
   |Custom Code Storage Settings Container (Container für Speichereinstellungen für benutzerdefinierten Code)|<Ihr Speichercontainer>|


## <a name="write-a-c-udf-with-codebehind"></a>Schreiben einer benutzerdefinierten C#-Datei mit CodeBehind
Eine CodeBehind-Datei ist eine C#-Datei, die einem einzelnen ASA-Abfrageskript zugeordnet ist. Visual Studio-Tools führen für die CodeBehind-Datei automatisch das Zippen durch und laden sie nach der Übermittlung in Ihr Azure-Speicherkonto hoch. Alle Klassen müssen als *öffentlich* und alle Objekte als *statisch öffentlich* definiert werden.

1. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **Script.asql** , um auf die CodeBehind-Datei **Script.asaql.cs** zuzugreifen.

2. Ersetzen Sie den Code durch das folgende Beispiel:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implementieren der benutzerdefinierten Funktion

1. Öffnen Sie im **Projektmappen-Explorer** die Datei **Script.asaql**.

2. Ersetzen Sie den vorhandenen Code durch Folgendes:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Lokales Testen

1. Laden Sie die [Datei mit den Beispieldaten für den Temperatursimulator](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json) herunter.

2. Erweitern Sie im **Projektmappen-Explorer** den Eintrag **Eingaben** , klicken Sie mit der rechten Maustaste auf **Input.json** , und wählen Sie **Lokale Eingabe hinzufügen**.

   ![Hinzufügen einer lokalen Eingabe zu einem Stream Analytics-Auftrag in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Geben Sie den lokalen Pfad der Eingabedatei für die Beispieldaten an, die Sie heruntergeladen haben, und wählen Sie **Speichern**.

    ![Konfiguration der lokalen Eingabe für einen Stream Analytics-Auftrag in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Klicken Sie im Skript-Editor auf **Lokal ausführen**. Nachdem die lokale Ausführung die Ausgabeergebnisse erfolgreich gespeichert hat, können Sie eine beliebige Taste drücken, um die Ergebnisse im Tabellenformat anzuzeigen. 

    ![Lokales Ausführen eines Azure Stream Analytics-Auftrags mit Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Sie können auch **Ergebnisordner öffnen** wählen, um die unformatierten Dateien im JSON- und CSV-Format anzuzeigen.

    ![Anzeigen der Ergebnisse des lokalen Azure Stream Analytics-Auftrags mit Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Debuggen einer benutzerdefinierten Funktion
Sie können Ihre benutzerdefinierte C#-Funktion lokal genauso wie C#-Standardcode debuggen. 

1. Fügen Sie Ihrer C#-Funktion Breakpoints hinzu.

    ![Hinzufügen von Breakpoints zu einer benutzerdefinierten Stream Analytics-Funktion in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Drücken Sie **F5** , um das Debuggen zu starten. Das Programm hält wie erwartet an den von Ihnen festgelegten Breakpoints an.

    ![Anzeigen der Ergebnisse des Debuggens einer benutzerdefinierten Stream Analytics-Funktion](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Veröffentlichen Ihres Auftrags in Azure
Nachdem Sie Ihre Abfrage lokal getestet haben, können Sie im Skript-Editor die Option **An Azure übermitteln** wählen, um den Auftrag in Azure zu veröffentlichen.

![Übermitteln Ihres Stream Analytics-Edge-Auftrags an Azure aus Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Bereitstellen auf IoT Edge-Geräten
Wenn Sie sich für die Erstellung eines Stream Analytics-Edge-Auftrags entschieden haben, kann dieser nun als IoT Edge-Modul bereitgestellt werden. Befolgen Sie die [Schnellstartanleitung für IoT Edge](../iot-edge/quickstart.md), um einen IoT-Hub zu erstellen, ein IoT Edge-Gerät zu registrieren und die IoT Edge-Runtime auf Ihrem Gerät zu installieren und zu starten. Befolgen Sie anschließend die Anleitung im Tutorial zum [Bereitstellen des Auftrags](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job), um Ihren Stream Analytics-Auftrag als IoT Edge-Modul bereitzustellen. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine einfache benutzerdefinierte C#-Funktion mit CodeBehind erstellt, Ihren Auftrag in Azure veröffentlicht und den Auftrag in Azure oder auf einem IoT Edge-Gerät bereitgestellt. 

Weitere Informationen zu den verschiedenen Möglichkeiten für die Verwendung von benutzerdefinierten C#-Funktionen für Stream Analytics-Aufträge finden Sie in diesem Artikel:

> [!div class="nextstepaction"]
> [Entwickeln von benutzerdefinierten .NET Standard-Funktionen für Azure Stream Analytics-Edge-Aufträge (Vorschau)](stream-analytics-edge-csharp-udf-methods.md)