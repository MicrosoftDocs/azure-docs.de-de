---
title: Überwachen und Verwalten von Pipelines mit dem Azure-Portal und PowerShell
description: Es wird beschrieben, wie Sie von Ihnen erstellte Azure Data Factorys und Pipelines mithilfe des Azure-Portals und Azure PowerShell überwachen und verwalten.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 486f12c29c473d46e3aff73abe747f8aa5a2ef8d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380405"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Überwachen und Verwalten von Azure Data Factory-Pipelines mit dem Azure-Portal und PowerShell
> [!div class="op_single_selector"]
> * [Verwenden von Azure-Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Verwenden der App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Überwachen und Verwalten von Data Factory-Pipelines](../monitor-visually.md).

In diesem Artikel wird das Überwachen, Verwalten und Debuggen Ihrer Pipelines mithilfe des Azure-Portals und von PowerShell beschrieben.

> [!IMPORTANT]
> Die Anwendung „Überwachung und Verwaltung“ bietet eine bessere Unterstützung der Überwachung und Verwaltung Ihrer Datenpipelines und der Problembehandlung. Unter [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md) erfahren Sie mehr über die Anwendung. 

> [!IMPORTANT]
> Azure Data Factory Version 1 verwendet jetzt die neue [Azure Monitor-Warnungsinfrastruktur](../../azure-monitor/platform/alerts-metric.md). Die alte Warnungsinfrastruktur ist veraltet. Folglich funktionieren Ihre für Data Factorys der Version 1 konfigurierten vorhandenen Warnungen nicht mehr. Ihre vorhandenen Warnungen für Data Factorys der Version 1 werden nicht automatisch migriert. Sie müssen diese Warnungen in der neuen Warnungsinfrastruktur neu erstellen. Melden Sie sich beim Azure-Portal an, und wählen Sie **Überwachen** zum Erstellen neuer Warnungen zu Metriken (z.B. fehlerhafte oder erfolgreiche Ausführungen) für Ihre Data Factorys der Version 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Grundlegendes zu Pipelines und Aktivitätsstatus
Im Azure-Portal haben Sie folgende Möglichkeiten:

* Anzeigen Ihrer Data Factory als Diagramm
* Anzeigen von Aktivitäten in einer Pipeline
* Anzeigen von Datasets für Ein- und Ausgabe

In diesem Abschnitt wird auch beschrieben, wie ein Slice eines Datasets von einem Status in einen anderen wechselt.   

### <a name="navigate-to-your-data-factory"></a>Navigieren zu Ihrer Data Factory
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im Menü auf der linken Seite auf **Data Factorys**. Wenn die Option nicht angezeigt wird, klicken Sie auf **Weitere Dienste** und anschließend unter der Kategorie **INTELLIGENCE + ANALYSE** auf **Data Factorys**.

   ![Alle durchsuchen > Data Factorys](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Wählen Sie auf dem Blatt **Data Factorys** die gewünschte Data Factory aus.

    ![Data Factory auswählen](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Die Startseite für die Data Factory wird angezeigt.

   ![Blatt "Data Factory"](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Diagrammansicht Ihrer Data Factory
Die Ansicht **Diagramm** einer Data Factory bietet eine zentrale Konsole zum Überwachen und Verwalten der Data Factory und ihrer Ressourcen. Klicken Sie auf der Data Factory-Startseite auf **Diagramm**, um die Ansicht **Diagramm** Ihrer Data Factory anzuzeigen.

![Diagrammansicht](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Sie können die Optionen „Vergrößern“, „Verkleinern“, „Mit Zoom anpassen“, „Auf 100 % vergrößern“ verwenden und das Layout des Diagramms sperren sowie Pipelines und Datasets automatisch positionieren. Außerdem können Sie die Informationen zur Datenherkunft anzeigen (also vor- und nachgeschaltete Elemente von ausgewählten Elementen).

### <a name="activities-inside-a-pipeline"></a>Aktivitäten innerhalb einer Pipeline
1. Klicken Sie mit der rechten Maustaste auf die Pipeline, und klicken Sie dann auf **Pipeline öffnen**, um alle Aktivitäten in der Pipeline sowie Ein- und Ausgabedatasets für die Aktivitäten anzuzeigen. Diese Funktion ist hilfreich, wenn Ihre Pipeline mehr als eine Aktivität enthält und Sie die operative Herkunft einer einzelnen Pipeline verstehen möchten.

    ![Menü "Pipeline öffnen"](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. Im folgenden Beispiel befindet sich eine Kopieraktivität mit einer Eingabe und einer Ausgabe in der Pipeline. 

    ![Aktivitäten innerhalb einer Pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Sie können zurück zur Data Factory-Startseite navigieren, indem Sie in der Brotkrümelnavigation links oben auf den Link **Data Factory** klicken.

    ![Zurück zur Data Factory navigieren](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Anzeigen des Status jeder Aktivität innerhalb einer Pipeline
Sie können den aktuellen Status einer Aktivität anzeigen, indem Sie sich den Status eines der Datasets ansehen, das von der Aktivität erzeugt wird.

Durch Doppelklicken auf **OutputBlobTable** in der Ansicht **Diagramm** werden alle Slices angezeigt, die innerhalb einer Pipeline von verschiedenen Aktivitätsausführungen erzeugt wurden. Sie stellen fest, dass die Kopieraktivität in den letzten acht Stunden erfolgreich ausgeführt wurde und Slices mit dem Status **Bereit** erzeugt hat.  

![Status der Pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Die Datasetslices in der Data Factory können einen der folgenden Status haben:

<table>
<tr>
    <th align="left">State</th><th align="left">Unterzustand</th><th align="left">BESCHREIBUNG</th>
</tr>
<tr>
    <td rowspan="8">Warten</td><td>ScheduleTime</td><td>Der Zeitpunkt für die Sliceausführung ist noch nicht erreicht.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Die Upstream-Abhängigkeiten sind nicht bereit.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Die Compute-Ressourcen sind nicht verfügbar.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alle Aktivitätsinstanzen führen andere Slices aus.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Die Aktivität wurde angehalten und kann die Slices erst ausführen, wenn sie fortgesetzt wird.</td>
</tr>
<tr>
<td>Wiederholen</td><td>Es wird versucht, die Ausführung der Aktivität zu wiederholen.</td>
</tr>
<tr>
<td>Überprüfen</td><td>Die Überprüfung wurde noch nicht gestartet.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Es wird darauf gewartet, die Überprüfung zu wiederholen.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Die Überprüfen erfolgt.</td><td>Die Überprüfung wird ausgeführt.</td>
</tr>
<td>-</td>
<td>Der Slice wird verarbeitet.</td>
</tr>
<tr>
<td rowspan="4">Fehler</td><td>TimedOut</td><td>Die Aktivitätsausführung dauerte länger, als für die Aktivität zulässig ist.</td>
</tr>
<tr>
<td>Canceled</td><td>Der Slice wurde durch eine Benutzeraktion abgebrochen.</td>
</tr>
<tr>
<td>Überprüfen</td><td>Fehler bei der Überprüfung.</td>
</tr>
<tr>
<td>-</td><td>Der Slice konnte nicht generiert bzw. überprüft werden.</td>
</tr>
<td>Bereit</td><td>-</td><td>Der Slice ist für die Verwendung bereit.</td>
</tr>
<tr>
<td>Ausgelassen</td><td>Keine</td><td>Der Slice wird nicht verarbeitet.</td>
</tr>
<tr>
<td>Keine</td><td>-</td><td>Ein Slice, der zuvor einen anderen Status hatte, aber zurückgesetzt wurde.</td>
</tr>
</table>



Sie können die Details zu einem Slice anzeigen, indem Sie auf dem Blatt **Zuletzt aktualisierte Slices** auf den Eintrag eines Slice klicken.

![Slicedetails](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Wenn der Slice mehrere Male ausgeführt wurde, enthält die Liste **Aktivitätsausführungen** mehrere Zeilen. Sie können Details zu einer Aktivitätsausführung anzeigen, indem Sie in der Liste **Aktivitätsausführungen** auf einen Ausführungseintrag klicken. In der Liste werden alle Protokolldateien zusammen mit einer Fehlermeldung, falls vorhanden, angezeigt. Diese Funktion ist nützlich zum Anzeigen und Debuggen von Protokollen, ohne dass Sie Ihre Data Factory verlassen müssen.

![Aktivitätsausführung – Details](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Wenn der Slice nicht den Status **Bereit** hat, sehen Sie die vorgelagerten Slices, die nicht bereit sind und das Ausführen des aktuellen Slice blockieren, in der Liste **Vorgelagerte Slices, die nicht bereit sind**. Diese Funktion ist nützlich, wenn der Slice den Status **Warten** hat und Sie die vorgelagerten Abhängigkeiten verstehen möchten, auf die der Slice wartet.

![Vorgelagerte Slices, die nicht bereit sind](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Statusdiagramm für Datasets
Nachdem Sie eine Data Factory bereitgestellt haben und die Pipelines einen gültigen aktiven Zeitraum aufweisen, gehen die Datasetslices von einem Status in einen anderen über. Derzeit werden die Status von Slices im folgenden Statusdiagramm abgebildet:

![Statusdiagramm](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Der Statusübergang für das Dataset in der Data Factory läuft wie folgt ab: Warten > In Bearbeitung/In Bearbeitung (Wird überprüft) > Bereit/Fehler.

Der Slice startet im Status **Warten**. Es wird gewartet, bis die Vorbedingungen erfüllt sind, bevor die Ausführung beginnen kann. Anschließend beginnt die Ausführung der Aktivität, und der Slice wechselt in den Status **In Bearbeitung**. Die Ausführung der Aktivität kann erfolgreich oder nicht erfolgreich sein. Der Slice wird basierend auf dem Ergebnis der Ausführung als **Bereit** oder **Fehler** markiert.

Sie können den Slice vom Status **Bereit** oder **Fehler** in den Status **Warten** zurücksetzen. Außerdem können Sie als Status für den Slice auch **Überspringen** angeben, wodurch die Aktivität nicht ausgeführt und der Slice nicht verarbeitet wird.

## <a name="pause-and-resume-pipelines"></a>Anhalten und Fortsetzen von Pipelines
Sie können Ihre Pipelines mit Azure PowerShell verwalten. Sie können z. B. mit Azure PowerShell-Cmdlets Pipelines anhalten und fortsetzen. 

> [!NOTE] 
> Die Diagrammansicht unterstützt nicht das Anhalten und Fortsetzen von Pipelines. Wenn Sie eine Benutzeroberfläche nutzen möchten, verwenden Sie die Anwendung „Überwachung und Verwaltung“. Im Artikel [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md) erfahren Sie mehr über die Anwendung. 

Sie können Pipelines mit dem PowerShell-Cmdlet **Suspend-AzDataFactoryPipeline** anhalten. Dieses Cmdlet ist nützlich, wenn Sie Ihre Pipelines nicht ausführen möchten, bis ein Problem behoben wurde. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Beispiel:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Nach der Behebung des Problems in der Pipeline können Sie die angehaltene Pipeline mit dem folgenden PowerShell-Befehl fortsetzen:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Beispiel:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Debuggen von Pipelines
Azure Data Factory bietet über das Azure-Portal und Azure PowerShell umfangreiche Funktionen zum Debuggen und Beheben von Problemen von Pipelines.

> [!NOTE] 
> Die Behebung von Problemen ist in der App „Überwachung und Verwaltung“ wesentlich einfacher. Im Artikel [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md) erfahren Sie mehr über die Anwendung. 

### <a name="find-errors-in-a-pipeline"></a>Suchen von Fehlern in einer Pipeline
Wenn eine Aktivitätsausführung in einer Pipeline nicht erfolgreich ist, hat das von der Pipeline erstellte Dataset aufgrund des Fehlers den Status „Fehler“. Sie können Fehler in Azure Data Factory mit den folgenden Methoden debuggen und beheben.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Verwenden des Azure-Portals zum Debuggen eines Fehlers
1. Klicken Sie auf dem Blatt **Tabelle** auf den Problemslice, für den **Status** auf **Fehler** festgelegt ist.

   ![Blatt "Tabelle" mit Problemslice](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Klicken Sie auf dem Blatt **Datenslice** auf die fehlerhafte Aktivitätsausführung.

   ![Datenslice mit einem Fehler](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Auf dem Blatt **Aktivitätsausführung – Details** können Sie die mit der Verarbeitung von HDInsight verknüpften Dateien herunterladen. Klicken Sie für „Status/stderr“ auf **Download**, um die Fehlerprotokolldatei herunterzuladen, die Einzelheiten zum Fehler enthält.

   ![Blatt "Aktivitätsausführung – Details" mit Fehler](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Verwenden von PowerShell zum Debuggen eines Fehlers
1. Starten Sie **PowerShell**.
2. Führen Sie den Befehl **Get-AzDataFactorySlice** aus, um die Slices und deren Status anzuzeigen. Ein Slice mit dem Status **Fehler** sollte angezeigt werden.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Beispiel:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Ersetzen Sie **StartDateTime** durch die Startzeit der Pipeline. 
3. Führen Sie nun das **Get-AzDataFactoryRun**-Cmdlet zum Abrufen von Details zur Aktivitätsausführung für den Slice aus.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Beispiel:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Der Wert von „StartDateTime“ ist die Startzeit für den Fehler-/Problemslice, den Sie im vorherigen Schritt notiert haben. Datum und Uhrzeit sollte in doppelte Anführungszeichen eingeschlossen werden.
4. Die Ausgabe mit Einzelheiten zum Fehler sieht in etwa wie folgt aus:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Sie können das **Save-AzDataFactoryLog**-Cmdlet mit dem ID-Wert ausführen, den Sie in der Ausgabe finden, und die Protokolldateien mithilfe der Option **-DownloadLogsoption** für das Cmdlet herunterladen.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Wiederholen nach Fehlern in einer Pipeline

> [!IMPORTANT]
> Das Beheben von Fehlern und erneute Ausführen fehlerhafter Slices ist in der App „Überwachung und Verwaltung“ wesentlich einfacher. Unter [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md) erfahren Sie mehr über die Anwendung. 

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals
Nachdem Sie eine Problembehandlung und das Debuggen für Fehler in einer Pipeline ausgeführt haben, können Sie Fehler wiederholen, indem Sie zum fehlerhaften Slice navigieren und dann in der Befehlsleiste auf die Schaltfläche **Ausführen** klicken.

![Wiederholen eines fehlerhaften Slices](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Für den Fall, dass der Slice die Überprüfung aufgrund eines Richtlinienfehlers (z.B. Daten nicht verfügbar) nicht besteht, können Sie den Fehler korrigieren und die Überprüfung erneut ausführen, indem Sie auf der Befehlsleiste auf **Überprüfen** klicken.

![Beheben von Fehlern und Überprüfen](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell
Sie können Ausführungen, bei denen Fehler aufgetreten sind, mit dem **Set-AzDataFactorySliceStatus**-Cmdlet wiederholen. Im Thema [Set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus) finden Sie die Syntax und ausführliche Informationen zum Cmdlet.

**Beispiel:**

Im folgenden Beispiel wird der Status aller Slices für die Tabelle „DAWikiAggregatedData“ in der Azure Data Factory „WikiADF“ auf „Waiting“ festgelegt.

„UpdateType“ ist auf „UpstreamInPipeline“ festgelegt. Dies bedeutet, dass die Status der einzelnen Slices für die Tabelle und alle abhängigen (vorgeschalteten) Tabellen auf „Waiting“ festgelegt sind. Der andere mögliche Wert für diesen Parameter ist „Individual“.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Erstellen von Warnungen im Azure-Portal

1.  Melden Sie sich beim Azure-Portal an, und wählen Sie **Überwachen -> Warnungen** aus, um die Seite „Warnungen“ zu öffnen.

    ![Öffnen Sie die Seite „Warnungen“.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Wählen Sie **+ Neue Warnungsregel** aus, um eine neue Warnung zu erstellen.

    ![Erstellen einer neuen Warnung](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definieren Sie die **Warnungsbedingung**. (Achten Sie darauf, im Feld **Nach Ressourcentyp filtern** die Option **Data Factorys** auszuwählen.) Sie können auch Werte für **Dimensionen** angeben.

    ![Warnungsbedingung definieren – Ziel auswählen](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Warnungsbedingung definieren – Warnungskriterien hinzufügen](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Warnungsbedingung definieren – Warnungslogik hinzufügen](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definieren Sie die **Warnungsdetails**.

    ![Definieren der Warnungsdetails](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definieren Sie die **Aktionsgruppe**.

    ![Definieren der Aktionsgruppe – neue Aktionsgruppe erstellen](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definieren der Aktionsgruppe – Eigenschaften festlegen](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definieren der Aktionsgruppe – neue Aktionsgruppe erstellt](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Verschieben einer Data Factory in eine andere Ressourcengruppe oder ein anderes Abonnement
Sie können eine Data Factory mithilfe der Schaltfläche **Verschieben** in der Befehlsleiste auf der Homepage Ihrer Data Factory in eine andere Ressourcengruppe oder ein anderes Abonnement verschieben.

![Data Factory verschieben](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Sie können auch alle zugehörigen Ressourcen (z.B. mit der Data Factory verknüpfte Warnungen) zusammen mit der Data Factory verschieben.

![Dialogfeld „Ressourcen verschieben“](./media/data-factory-monitor-manage-pipelines/MoveResources.png)