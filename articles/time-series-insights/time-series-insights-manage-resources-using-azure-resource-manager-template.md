---
title: Verwalten Ihrer Umgebung mit Azure Resource Manager-Vorlagen – Azure Time Series Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Azure Time Series Insights-Umgebung mit dem Azure Resource Manager programmgesteuert verwalten.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 493750e69b1fdc935b04d6dc705cfd046b6b086e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96011658"
---
# <a name="create-azure-time-series-insights-gen-1-resources-using-azure-resource-manager-templates"></a>Erstellen von Azure Time Series Insights Gen1-Ressourcen mit Azure Resource Manager-Vorlagen

> [!CAUTION]
> Dies ist ein Artikel zu Azure Time Series Insights Gen1.

In diesem Artikel wird das Erstellen und Bereitstellen von Azure Time Series Insights-Ressourcen mithilfe von [Azure Resource Manager-Vorlagen](../azure-resource-manager/index.yml), PowerShell und des Azure Time Series Insights-Ressourcenanbieters beschrieben.

Azure Time Series Insights unterstützt die folgenden Ressourcen:

   | Resource | BESCHREIBUNG |
   | --- | --- |
   | Environment | Eine Azure Time Series Insights-Umgebung ist eine logische Gruppierung von Ereignissen, die aus Ereignisbrokern gelesen, gespeichert und zur Abfrage zur Verfügung gestellt werden. Weitere Informationen finden Sie unter [Planen Ihrer Azure Time Series Insights-Umgebung](time-series-insights-environment-planning.md). |
   | Ereignisquelle | Eine Ereignisquelle ist eine Verbindung mit einem Ereignisbroker, von dem Azure Time Series Insights Daten liest und Ereignisse für die Umgebung erfasst. Derzeit werden IoT Hub und Event Hub als Ereignisquellen unterstützt. |
   | Verweisdataset | Verweisdatasets stellen Metadaten zu den Ereignissen der Umgebung bereit. Metadaten in den Verweisdaten werden während der Erfassung mit Ereignissen verknüpft. Verweisdatasets werden über die dazugehörigen Ereignisschlüsseleigenschaften als Ressourcen definiert. Die eigentlichen Metadaten, aus denen das Verweisdataset besteht, wird über Datenebenen-APIs hochgeladen oder geändert. |
   | Zugriffsrichtlinie | Zugriffsrichtlinien gewähren Berechtigungen für die Erstellung von Datenabfragen, für die Bearbeitung von Verweisdaten in der Umgebung und für die Freigabe gespeicherter Abfragen und Perspektiven, die der Umgebung zugeordnet sind. Weitere Informationen finden Sie unter [Gewähren von Datenzugriff für eine Azure Time Series Insights-Umgebung mit Azure-Portal](./concepts-access-policies.md). |

Eine Resource Manager-Vorlage ist eine JSON-Datei, mit der die Infrastruktur und Konfiguration von Ressourcen in einer Ressourcengruppe definiert wird. In den folgenden Dokumenten werden Vorlagendateien ausführlicher beschrieben:

- [Azure Resource Manager templates](../azure-resource-manager/templates/overview.md) (Azure Resource Manager-Vorlagen)
- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Microsoft.TimeSeriesInsights-Ressourcentypen](/azure/templates/microsoft.timeseriesinsights/allversions)

Die Schnellstartvorlage [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) ist auf GitHub verfügbar. Mit dieser Vorlage werden eine Azure Time Series Insights-Umgebung, eine untergeordnete Ereignisquelle, die für die Nutzung von Ereignissen von einem Event Hub konfiguriert ist, und Zugriffsrichtlinien erstellt, mit denen Zugriff auf die Daten der Umgebung gewährt wird. Wenn Sie keinen vorhandenen Event Hub angeben, wird einer für die Bereitstellung erstellt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Angeben der Bereitstellungsvorlage und der Parameter

Im folgenden Verfahren wird beschrieben, wie Sie PowerShell zum Bereitstellen einer Azure Resource Manager-Vorlage verwenden, mit der eine Azure Time Series Insights-Umgebung, eine untergeordnete Ereignisquelle, die für die Nutzung von Ereignissen von einem Event Hub konfiguriert ist, und Zugriffsrichtlinien erstellen, mit denen Zugriff auf die Daten der Umgebung gewährt wird. Wenn Sie keinen vorhandenen Event Hub angeben, wird einer für die Bereitstellung erstellt.

1. Installieren Sie Azure PowerShell gemäß den Anweisungen unter [Erste Schritte mit Azure PowerShell-Cmdlets](/powershell/azure/get-started-azureps).

1. Klonen oder kopieren Sie die Vorlage [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) von GitHub.

   - Erstellen einer Parameterdatei

     Kopieren Sie die Datei [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json), um eine Parameterdatei zu erstellen.

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   - Erforderliche Parameter

     | Parameter | BESCHREIBUNG |
     | --- | --- |
     | eventHubNamespaceName | Der Namespace der Quelle (Event Hub). |
     | eventHubName | Der Name der Quelle (Event Hub). |
     | consumerGroupName | Der Name der Consumergruppe, die vom Azure Time Series Insights-Dienst verwendet wird, um die Daten vom Event Hub zu lesen. **HINWEIS:** Zur Vermeidung von Ressourcenkonflikten muss diese Consumergruppe dediziert für den Azure Time Series Insights-Dienst verwendet werden und sollte nicht für andere Leser freigegeben werden. |
     | environmentName | Der Name der Umgebung. Der Name darf folgende Zeichen nicht enthalten: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/` und keine Steuerzeichen. Alle anderen Zeichen sind zulässig.|
     | eventSourceName | Der Name der untergeordneten Ressource der Ereignisquelle. Der Name darf folgende Zeichen nicht enthalten: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/` und keine Steuerzeichen. Alle anderen Zeichen sind zulässig. |

    <div id="optional-parameters"></div>

   - Optionale Parameter

     | Parameter | BESCHREIBUNG |
     | --- | --- |
     | existingEventHubResourceId | Eine optionale Ressourcen-ID eines vorhandenen Event Hub, der über die Ereignisquelle mit der Azure Time Series Insights-Umgebung verbunden wird. **HINWEIS:** Der Benutzer, der die Vorlage bereitstellt, muss über Berechtigungen zum Durchführen des Vorgangs „listkeys“ auf dem Event Hub verfügen. Wenn kein Wert übergeben wird, wird von der Vorlage ein neuer Event Hub erstellt. |
     | environmentDisplayName | Ein optionaler Anzeigenamen, der in Tools oder Benutzeroberflächen anstelle des Umgebungsnamens angezeigt wird. |
     | environmentSkuName | Der Name der SKU. Weitere Informationen finden Sie auf der Seite [Azure Time Series Insights – Preise](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Die Einheitenkapazität der SKU. Weitere Informationen finden Sie auf der Seite [Azure Time Series Insights – Preise](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | Die Mindestzeitspanne der Ereignisse einer Umgebung ist für Abfragen verfügbar. Der Wert muss im ISO 8601-Format angegeben werden, z.B. `P30D` für eine Aufbewahrungsrichtlinie von 30 Tagen. |
     | eventSourceDisplayName | Ein optionaler Anzeigename, der in Tools oder Benutzeroberflächen anstelle des Ereignisquellennamens angezeigt wird. |
     | eventSourceTimestampPropertyName | Die Ereigniseigenschaft, die als Zeitstempel der Ereignisquelle verwendet wird. Wenn für timestampPropertyName kein Name oder wenn Null oder eine leere Zeichenfolge angegeben wird, wird der Zeitpunkt der Ereigniserstellung verwendet. |
     | eventSourceKeyName | Der Name des freigegebenen Zugriffsschlüssels, der vom Azure Time Series Insights-Dienst verwendet wird, um eine Verbindung mit dem Event Hub herzustellen. |
     | accessPolicyReaderObjectIds | Eine Liste mit Objekt-IDs der Benutzer oder Anwendungen in Azure AD, die über Lesezugriff auf die Umgebung verfügen sollen. Die Objekt-ID des Dienstprinzipals kann abgerufen werden, indem das **Get-AzADUser** oder das **Get-AzADServicePrincipal**-Cmdlet aufgerufen wird. Die Erstellung einer Zugriffsrichtlinie für Azure AD-Gruppen wird noch nicht unterstützt. |
     | accessPolicyContributorObjectIds | Eine Liste mit Objekt-IDs der Benutzer oder Anwendungen in Azure AD, die über den Zugriff „Mitwirkender“ auf die Umgebung verfügen sollen. Die Objekt-ID des Dienstprinzipals kann abgerufen werden, indem das **Get-AzADUser** oder das **Get-AzADServicePrincipal**-Cmdlet aufgerufen wird. Die Erstellung einer Zugriffsrichtlinie für Azure AD-Gruppen wird noch nicht unterstützt. |

   - Beispielsweise wird die folgende Parameterdatei verwendet, um eine Umgebung und eine Ereignisquelle zu erstellen, über die Ereignisse von einem vorhandenen Event Hub gelesen werden. Außerdem werden hiermit zwei Zugriffsrichtlinien erstellt, mit denen der Zugriff „Mitwirkender“ auf die Umgebung gewährt wird.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }
         }
     }
     ```

   - Weitere Informationen finden Sie im Artikel [Parameter](../azure-resource-manager/templates/parameter-files.md).

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Lokales Bereitstellen der Schnellstartvorlage mit PowerShell

> [!IMPORTANT]
> Die unten angezeigten Befehlszeilenvorgänge beschreiben das [Az-PowerShell-Modul](/powershell/azure/).

1. Melden Sie sich in PowerShell bei Ihrem Azure-Konto an.

    - Führen Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl aus:

      ```powershell
      Connect-AzAccount
      ```

    - Sie werden aufgefordert, sich bei Ihrem Azure-Konto anzumelden. Führen Sie nach der Anmeldung den folgenden Befehl aus, um Ihre verfügbaren Abonnements anzuzeigen:

      ```powershell
      Get-AzSubscription
      ```

    - Dieser Befehl gibt eine Liste der verfügbaren Azure-Abonnements zurück. Wählen Sie ein Abonnement für die aktuelle Sitzung aus, indem Sie folgenden Befehl ausführen. Ersetzen Sie `<YourSubscriptionId>` durch die GUID des Azure-Abonnements, das Sie verwenden möchten:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Erstellen Sie eine neue Ressourcengruppe, falls noch keine vorhanden ist.

   - Wenn noch keine Ressourcengruppe vorhanden ist, erstellen Sie mit dem Befehl **New-AzResourceGroup** eine neue Ressourcengruppe. Geben Sie den Namen der gewünschten Ressourcengruppe und den gewünschten Speicherort ein. Beispiel:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   - Nach erfolgreicher Ausführung wird eine Zusammenfassung der neuen Ressourcengruppe angezeigt:

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Testen Sie die Bereitstellung.

   - Überprüfen Sie Ihre Bereitstellung, indem Sie das Cmdlet `Test-AzResourceGroupDeployment` ausführen. Geben Sie beim Testen der Bereitstellung die Parameter exakt so an wie beim Ausführen der Bereitstellung.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Erstellen der Bereitstellung

    - Um die neue Bereitstellung zu erstellen, führen Sie das Cmdlet `New-AzResourceGroupDeployment` aus, und geben Sie bei entsprechender Aufforderung die erforderlichen Parameter an. Die Parameter enthalten einen Namen für Ihre Bereitstellung, den Namen Ihrer Ressourcengruppe und den Pfad oder die URL zur Vorlagendatei. Wenn der Parameter **Mode** nicht angegeben wurde, wird der Standardwert **Incremental** verwendet. Weitere Informationen finden Sie unter [Inkrementelle und vollständige Bereitstellungen](../azure-resource-manager/templates/deployment-modes.md).

    - Mit dem folgenden Befehl werden Sie aufgefordert, die fünf erforderlichen Parameter im PowerShell-Fenster einzugeben:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    - Um stattdessen eine Parameterdatei zu verwenden, geben Sie folgenden Befehl ein:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    - Sie können beim Ausführen des Bereitstellungs-Cmdlets auch Inlineparameter verwenden. Der Befehl lautet wie folgt:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    - Legen Sie zum Ausführen einer [vollständigen](../azure-resource-manager/templates/deployment-modes.md) Bereitstellung den Parameter **Mode** auf **Complete** fest:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Überprüfen der Bereitstellung

    - Wenn die Ressourcen erfolgreich bereitgestellt wurden, wird im PowerShell-Fenster eine Zusammenfassung der Bereitstellung angezeigt:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Bereitstellen der Schnellstartvorlage über das Azure-Portal

   - Die Startseite der Schnellstartvorlage auf GitHub enthält auch die Schaltfläche **Deploy to Azure** (In Azure bereitstellen). Wenn Sie darauf klicken, wird im Azure-Portal die Seite „Benutzerdefinierte Bereitstellung“ geöffnet. Auf dieser Seite können Sie Werte für jeden Parameter aus der Tabelle mit den [erforderlichen Parametern](#required-parameters) oder den [optionalen Parametern](#optional-parameters) eingeben oder auswählen. Wenn Sie nach dem Angeben der Einstellungen auf die Schaltfläche **Kauf** klicken, wird die Vorlagenbereitstellung initiiert.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png" alt="The Deploy to Azure button."/>
    </a>

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur programmgesteuerten Verwaltung von Azure Time Series Insights-Ressourcen mit REST-APIs finden Sie unter [Azure Time Series Insights Management](/rest/api/time-series-insights-management/) (Time Series Insights-Verwaltung).