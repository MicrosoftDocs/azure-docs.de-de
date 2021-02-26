---
title: Problembehandlung bei Azure Data Factory
description: Erfahren Sie, wie Sie Probleme mithilfe von Azure Data Factory beheben.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
robots: noindex
ms.openlocfilehash: ed831e5f07eb29110b858dfb16b73f276926424f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388242"
---
# <a name="troubleshoot-data-factory-issues"></a>Problembehandlung bei Data Factory
> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory. 

Dieser Artikel enthält Tipps zur Behandlung von Problemen bei der Verwendung von Azure Data Factory. Dieser Artikel führt nicht alle Probleme auf, die bei Verwendung des Diensts möglicherweise auftreten können, sondern erläutert einige ausgewählte Probleme und die allgemeine Problembehandlung.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Error: Das Abonnement ist nicht für die Verwendung des Namespace „Microsoft.DataFactory“ registriert.
Wenn Sie diesen Fehler erhalten, wurde der Azure Data Factory-Ressourcenanbieter nicht auf Ihrem Computer registriert. Gehen Sie folgendermaßen vor:

1. Starten Sie Azure PowerShell.
2. Melden Sie sich mithilfe des folgenden Befehls bei Ihrem Azure-Konto an.

   ```powershell
   Connect-AzAccount
   ```

3. Führen Sie den folgenden Befehl aus, um den Azure Data Factory-Anbieter zu registrieren.

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
   ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: Autorisierungsfehler beim Ausführen eines Data Factory-Cmdlets
Sie verwenden wahrscheinlich nicht das richtige Azure-Konto oder -Abonnement für Azure PowerShell. Wählen Sie mithilfe der folgenden Cmdlets das richtige Azure-Konto und -Abonnement für die Verwendung mit Azure PowerShell aus.

1. Connect-AzAccount: Verwenden Sie die richtige Benutzer-ID und das richtige Kennwort.
2. Get-AzSubscription: Dient zum Anzeigen aller Abonnements für das Konto.
3. Select-AzSubscription &lt;Name des Abonnements&gt;: Wählen Sie das richtige Abonnement aus. Verwenden Sie dasselbe Abonnement, das Sie auch zum Erstellen einer Data Factory im Azure-Portal verwenden.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: Das Express-Setup für das Datenverwaltungsgateway kann über das Azure-Portal nicht gestartet werden.
Für das Express-Setup des Datenverwaltungsgateways ist Internet Explorer oder ein mit Microsoft ClickOnce kompatibler Webbrowser erforderlich. Wenn das Express-Setup nicht gestartet wird, führen Sie einen der folgenden Schritte aus:

* Verwenden Sie Internet Explorer oder einen mit Microsoft ClickOnce kompatiblen Webbrowser.

    Navigieren Sie bei Verwendung von Chrome zum [Chrome Web Store](https://chrome.google.com/webstore/), und suchen Sie nach „ClickOnce“. Wählen Sie eine der ClickOnce-Erweiterungen aus, und installieren Sie sie.

    Gehen Sie bei Firefox genauso vor (Installation des Add-Ins). Klicken Sie auf der Symbolleiste auf die Schaltfläche „Menü öffnen“ (drei waagerechte Striche oben rechts), klicken Sie auf „Add-Ons“, suchen Sie nach dem Stichwort „ClickOnce“, wählen Sie eine der ClickOnce-Erweiterungen aus, und installieren Sie sie.
* Verwenden Sie den Link **Manuelles Setup** , der im Portal auf dem gleichen Blatt angezeigt wird. Mit dieser Vorgehensweise laden Sie die Installationsdatei herunter und führen Sie manuell aus. Wenn die Installation abgeschlossen ist, wird das Dialogfeld für die Datenverwaltungsgateway-Konfiguration angezeigt. Kopieren Sie den **Schlüssel** auf dem Portalbildschirm, und verwenden Sie ihn im Konfigurations-Manager, um das Gateway manuell für den Dienst zu registrieren.  

### <a name="problem-fail-to-connect-to-sql-server"></a>Problem: Fehler beim Herstellen einer Verbindung mit SQL Server
Starten Sie den **Datenverwaltungsgateway-Konfigurations-Manager** auf dem Gatewaycomputer, und verwenden Sie die Registerkarte **Problembehandlung**, um die Verbindung mit SQL Server über den Gatewaycomputer zu testen. Unter [Problembehandlung bei Gateways](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) finden Sie Tipps zur Behandlung von Verbindungs- bzw. Gatewayproblemen.   

### <a name="problem-input-slices-are-in-waiting-state-forever"></a>Problem: Eingabeslices haben dauerhaft den Status „Warten“.
Die Slices können sich aus verschiedenen Gründen im Status **Warten** befinden. Einer der häufigsten Gründe ist, dass die Eigenschaft **external** nicht auf **true** festgelegt ist. Ein Dataset, das außerhalb des Gültigkeitsbereichs von Azure Data Factory erstellt wird, sollte mit der Eigenschaft **external** gekennzeichnet sein. Diese Eigenschaft weist darauf hin, dass es sich um externe Daten handelt, die nicht von Pipelines innerhalb der Data Factory unterstützt werden. Die Datenslices werden als **Ready** gekennzeichnet, sobald die Daten im entsprechenden Speicher verfügbar sind.

Das folgende Beispiel zeigt die Verwendung der Eigenschaft **external** . Sie können optional **externalData*** angeben, wenn Sie „external“ auf „true“ festlegen.

Weitere Informationen zu dieser Eigenschaft finden Sie im Artikel [Datasets](data-factory-create-datasets.md) .

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Um den Fehler zu beheben, fügen Sie die Eigenschaft **external** und den optionalen Abschnitt **externalData** der JSON-Definition für die Eingabetabelle hinzu und erstellen die Tabelle erneut.

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: Fehler beim Hybridkopiervorgang
Die Schritte zum Behandeln von Problemen beim Kopieren in lokale Datenspeicher und aus lokalen Datenspeichern mit dem Datenverwaltungsgateway finden Sie unter [Problembehandlung bei Gateways](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: Fehler bei der bedarfsgesteuerten HDInsight-Bereitstellung
Wenn Sie einen verknüpften Dienst vom Typ „HDInsightOnDemand“ verwenden, müssen Sie einen „linkedServiceName“ angeben, der auf Azure Blob Storage verweist. Der Data Factory-Dienst verwendet diesen Speicher, um Protokolle und unterstützende Dateien für Ihren bedarfsgesteuerten HDInsight-Cluster zu speichern.  Manchmal schlägt die Bereitstellung eines bedarfsgesteuerten HDInsight-Clusters mit dem folgenden Fehler fehl:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Dieser Fehler gibt normalerweise an, dass der Speicherort des Speicherkontos, das in „linkedServiceName“ angegeben ist, nicht dem Speicherort im Rechenzentrum entspricht, in dem die HDInsight-Bereitstellung erfolgt. Beispiel: Wenn sich Ihre Data Factory in der Region „USA, Westen“ und der Azure-Speicher in der Region „USA, Osten“ befinden, tritt bei der bedarfsgesteuerten Bereitstellung in „USA, Westen“ ein Fehler auf.

Darüber hinaus können in der weiteren JSON-Eigenschaft "additionalLinkedServiceNames" zusätzliche Speicherkonten in bedarfsgesteuertem HDInsight angegeben werden. Diese zusätzlichen verknüpften Speicherkonten müssen sich am gleichen Standort wie der HDInsight-Cluster befinden. Andernfalls tritt derselbe Fehler auf.

### <a name="problem-custom-net-activity-fails"></a>Problem: Fehler bei benutzerdefinierter .NET-Aktivität
Die ausführlichen Schritte finden Sie unter [Debuggen einer Pipeline mit benutzerdefinierten Aktivitäten](data-factory-use-custom-activities.md#troubleshoot-failures) .

## <a name="use-azure-portal-to-troubleshoot"></a>Verwenden des Azure-Portals zur Problembehandlung
### <a name="using-portal-blades"></a>Verwenden von Portalblättern
Entsprechende Schritte finden Sie unter [Überwachen der Pipeline](data-factory-monitor-manage-pipelines.md) .

### <a name="using-monitor-and-manage-app"></a>Verwenden der App „Überwachung und Verwaltung“
Ausführliche Informationen finden Sie unter [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md) .

## <a name="use-azure-powershell-to-troubleshoot"></a>Verwenden von Azure PowerShell zur Problembehandlung
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Verwenden von Azure PowerShell zur Behandlung eines Fehlers
Ausführliche Informationen finden Sie unter [Überwachen von Data Factory-Pipelines mithilfe von Azure PowerShell](data-factory-monitor-manage-pipelines.md) .

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: /previous-versions/azure/dn835050(v=azure.100)

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png