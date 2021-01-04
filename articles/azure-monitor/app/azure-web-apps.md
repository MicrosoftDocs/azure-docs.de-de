---
title: Überwachen der Leistung von Azure App Services | Microsoft-Dokumentation
description: Überwachung der Anwendungsleistung für Azure App Services. Ladezeit für Diagramme und Antwortzeit, Informationen zu den Abhängigkeiten und Festlegen von Benachrichtigungen zur Leistung.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: c0ee68659f4729ed8f63b9ea990343adf51513bd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186370"
---
# <a name="monitor-azure-app-service-performance"></a>Überwachen der Leistung von Azure App Service

Das Aktivieren der Überwachung für Ihre ASP.NET- und ASP.NET Core-basierten Webanwendungen unter [Azure App Services](../../app-service/index.yml) ist jetzt einfacher als je zuvor. Während Sie zuvor manuell eine Websiteerweiterung installieren mussten, ist die neueste Erweiterung/der neueste Agent nun standardmäßig in das App Service-Image integriert. Dieser Artikel führt Sie Schritt für Schritt durch das Aktivieren der Application Insights-Überwachung und bietet eine vorläufige Anleitung zur Automatisierung des Prozesses für umfangreiche Bereitstellungen.

> [!NOTE]
> Das manuelle Hinzufügen einer Application Insights-Websiteerweiterung über **Entwicklungstools** > **Erweiterungen** ist veraltet. Diese Methode der Erweiterungsinstallation war von manuellen Updates für jede neue Version abhängig. Die neueste stabile Version der Erweiterung ist jetzt als Teil des App Service-Images [vorinstalliert](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). Die Dateien befinden sich in `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` und werden mit jeder stabilen Version automatisch aktualisiert. Wenn Sie den weiter unten beschriebenen Anweisungen zum Aktivieren der Agent-basierten Überwachung folgen, wird die veraltete Erweiterung automatisch entfernt.

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Es gibt zwei Methoden, um die Überwachung für in Azure App Services gehostete Anwendungen zu aktivieren:

* **Agent-basierte Anwendungsüberwachung** (ApplicationInsightsAgent).  
    * Diese Methode stellt die einfachste Möglichkeit der Aktivierung dar und erfordert keine erweiterte Konfiguration. Sie wird häufig als „Laufzeitüberwachung“ bezeichnet. Für Azure App Services wird empfohlen, mindestens diese Überwachungsstufe zu aktivieren. Anschließend können Sie je nach Ihrem spezifischen Szenario bewerten, ob eine erweiterte Überwachung durch manuelle Instrumentierung erforderlich ist.

* **Manuelles Instrumentieren der Anwendung über Code** durch Installieren des Application Insights SDK.

    * Dieser Ansatz ermöglicht eine wesentlich stärkere Anpassung, erfordert jedoch das [Hinzufügen einer Abhängigkeit von den Application Insights SDK-NuGet-Paketen](./asp-net.md). Diese Methode bedeutet auch, dass Sie die Updates auf die neueste Version der Pakete selbst verwalten müssen.

    * Wenn Sie benutzerdefinierte API-Aufrufe zum Nachverfolgen von Ereignissen/Abhängigkeiten ausführen müssen, die bei der Agent-basierten Überwachung nicht standardmäßig erfasst werden, müssen Sie diese Methode verwenden. Weitere Informationen finden Sie im Artikel zur [API für benutzerdefinierte Ereignisse und Metriken](./api-custom-events-metrics.md). Dies ist derzeit auch die einzige unterstützte Option für Linux-basierte Arbeitsauslastungen.

> [!NOTE]
> Wenn sowohl die Agent-basierte Überwachung als auch die manuelle SDK-basierte Instrumentierung erkannt wird, werden nur die Einstellungen der manuellen Instrumentierung berücksichtigt. Dadurch wird verhindert, dass doppelte Daten gesendet werden. Weitere Informationen dazu finden Sie im [Abschnitt zur Problembehandlung](#troubleshooting) weiter unten.

## <a name="enable-agent-based-monitoring"></a>Aktivieren der Agent-basierten Überwachung

# <a name="aspnet"></a>[ASP.NET](#tab/net)

> [!NOTE]
> Die Kombination von APPINSIGHTS_JAVASCRIPT_ENABLED und urlCompression wird nicht unterstützt. Weitere Informationen enthält die Erläuterung im [Problembehandlungsabschnitt](#troubleshooting).


1. In der Azure-Systemsteuerung für Ihre App Service-Instanz können Sie **Application Insights auswählen**.

    ![Auswählen von „Application Insights“ unter „Einstellungen“](./media/azure-web-apps/settings-app-insights-01.png)

   * Wählen Sie die Option zum Erstellen einer neuen Ressource, sofern Sie nicht bereits eine Application Insights-Ressource für diese Anwendung eingerichtet haben. 

     > [!NOTE]
     > Beim Klicken auf **OK** zum Erstellen der neuen Ressource wird die Aufforderung **Überwachungseinstellungen anwenden** angezeigt. Wenn Sie **Weiter** wählen, wird Ihre neue Application Insights-Ressource mit Ihrer App Service-Instanz verknüpft, und außerdem wird **ein Neustart Ihrer App Service-Instanz ausgelöst**. 

     ![Instrumentieren Ihrer Web-App](./media/azure-web-apps/create-resource-01.png)

2. Nach Angabe der zu verwendenden Ressource können Sie plattformspezifisch auswählen, wie Application Insights Daten für Ihre Anwendung erfassen soll. Die Überwachung von ASP.NET-Apps ist standardmäßig mit zwei verschiedenen Erfassungsstufen aktiviert.

    ![Screenshot der Seite mit Application Insights-Websiteerweiterungen mit ausgewählter Option „Neue Ressource erstellen“](./media/azure-web-apps/choose-options-new.png)
 
 Es folgt eine Übersicht über die gesammelten Daten für jede Route:
        
| Daten | ASP.NET: Basissammlung | ASP.NET: Empfohlene Sammlung |
| --- | --- | --- |
| Fügt CPU-, Speicher- und E/A-Nutzungstrends hinzu. |Ja |Ja |
| Sammelt Nutzungstrends und ermöglicht die Korrelation von Verfügbarkeitsergebnissen und Transaktionen. | Ja |Ja |
| Erfasst Ausnahmen, die vom Hostprozess nicht behandelt werden. | Ja |Ja |
| Verbessert die Genauigkeit der APM-Metriken unter Last, wenn die Stichprobe verwendet wird. | Ja |Ja |
| Korreliert Microservices über Anforderungs-/Abhängigkeitsgrenzen hinweg. | Nein (nur APM-Einzelinstanzfunktionen) |Ja |

3. Zum Konfigurieren von Einstellungen wie der Stichprobe, die Sie zuvor über die Datei „applicationinsights.config“ steuern konnten, können Sie jetzt über die Anwendungseinstellungen mit dem entsprechenden Präfix mit genau diesen Einstellungen interagieren. 

    * Wenn Sie beispielsweise den anfänglichen Prozentsatz für die Stichprobenerstellung ändern möchten, können Sie die Anwendungseinstellung `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` und einen Wert von `100` erstellen.

    * Eine Liste der unterstützten Einstellungen des Telemetrieprozessors für die adaptive Stichprobenerstellung finden Sie im [Code](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) und der [zugehörigen Dokumentation](./sampling.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

Unterstützte ASP.NET Core-Versionen: ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0, ASP.NET Core 3.1

Die Agent-/Erweiterung-basierte Überwachung für das vollständige Framework aus ASP.NET Core, eigenständiger Bereitstellung und Linux-basierten Anwendungen wird derzeit **nicht unterstützt**. (Die [manuelle Instrumentierung](./asp-net-core.md) über Code kann in allen zuvor genannten Szenarien verwendet werden.)

1. In der Azure-Systemsteuerung für Ihre App Service-Instanz können Sie **Application Insights auswählen**.

    ![Auswählen von „Application Insights“ unter „Einstellungen“](./media/azure-web-apps/settings-app-insights-01.png)

   * Wählen Sie die Option zum Erstellen einer neuen Ressource, sofern Sie nicht bereits eine Application Insights-Ressource für diese Anwendung eingerichtet haben. 

     > [!NOTE]
     > Beim Klicken auf **OK** zum Erstellen der neuen Ressource wird die Aufforderung **Überwachungseinstellungen anwenden** angezeigt. Wenn Sie **Weiter** wählen, wird Ihre neue Application Insights-Ressource mit Ihrer App Service-Instanz verknüpft, und außerdem wird **ein Neustart Ihrer App Service-Instanz ausgelöst**. 

     ![Instrumentieren Ihrer Web-App](./media/azure-web-apps/create-resource-01.png)

2. Nach Angabe der zu verwendenden Ressource können Sie plattformspezifisch auswählen, wie Application Insights Daten für Ihre Anwendung erfassen soll. ASP.NET Core bietet die Optionen **Empfohlene Sammlung**  oder **Deaktiviert** für ASP.NET Core 2.1, 2.2, 3.0 und 3.1.

    ![Auswählen plattformspezifischer Optionen](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Wählen Sie in Ihrer App Service-Web-App die Option **Einstellungen** > **Application Insights** > **Aktivieren** aus. Die Agent-basierte Überwachung für Node.js befindet sich derzeit in der Vorschauphase.

# <a name="java"></a>[Java](#tab/java)

Befolgen Sie die Anweisungen für den [Application Insights Java 3.0-Agent](./java-in-process-agent.md), um die automatische Instrumentierung für Ihre Java-Apps zu aktivieren, ohne den Code zu ändern.
Die automatische Integration ist für App Service noch nicht verfügbar.

# <a name="python"></a>[Python](#tab/python)

Auf App Service basierende Python-Webanwendungen unterstützen derzeit keine automatische Überwachung mit Agents/Erweiterungen. Um die Überwachung für Ihre Python-Anwendung zu aktivieren, müssen Sie [Ihre Anwendung manuell instrumentieren](./opencensus-python.md).

---

## <a name="enable-client-side-monitoring"></a>Aktivieren der clientseitigen Überwachung

# <a name="aspnet"></a>[ASP.NET](#tab/net)

Die clientseitige Überwachung für ASP.NET ist optional. Zum Aktivieren der clientseitigen Überwachung gehen Sie folgendermaßen vor:

* **Einstellungen** **>** **Konfiguration**
   * Erstellen Sie unter „Anwendungseinstellungen“ eine **neue Anwendungseinstellung**:

     Name: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wert: `true`

   * **Speichern** Sie die Einstellungen, und **starten Sie die App neu**.

Zum Deaktivieren der clientseitigen Überwachung entfernen Sie entweder das zugeordnete Schlüssel-Wert-Paar aus den Anwendungseinstellungen oder legen Sie den Wert auf „false“ fest.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

Die clientseitige Überwachung für ASP.NET Core-Apps ist **standardmäßig aktiviert** mit **Empfohlene Sammlung**, unabhängig davon, ob die App-Einstellung „APPINSIGHTS_JAVASCRIPT_ENABLED“ vorhanden ist.

Wenn Sie aus irgendeinem Grund die clientseitige Überwachung deaktivieren möchten, gehen Sie folgendermaßen vor:

* **Einstellungen** **>** **Konfiguration**
   * Erstellen Sie unter „Anwendungseinstellungen“ eine **neue Anwendungseinstellung**:

     Name: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wert: `false`

   * **Speichern** Sie die Einstellungen, und **starten Sie die App neu**.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Um die clientseitige Überwachung für Ihre Node.js-Anwendung zu aktivieren, müssen Sie [Ihrer Anwendung das clientseitige JavaScript-SDK manuell hinzufügen](./javascript.md).

# <a name="java"></a>[Java](#tab/java)

Um die clientseitige Überwachung für Ihre Java-Anwendung zu aktivieren, müssen Sie [Ihrer Anwendung das clientseitige JavaScript-SDK manuell hinzufügen](./javascript.md).

# <a name="python"></a>[Python](#tab/python)

Um die clientseitige Überwachung für Ihre Python-Anwendung zu aktivieren, müssen Sie [Ihrer Anwendung das clientseitige JavaScript-SDK manuell hinzufügen](./javascript.md).

---

## <a name="automate-monitoring"></a>Automatisieren der Überwachung

Sie müssen nur die Anwendungseinstellungen festlegen, um die Sammlung von Telemetriedaten mit Application Insights zu aktivieren:

   ![App Service-Anwendungseinstellungen mit verfügbaren Application Insights-Einstellungen](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definitionen von Anwendungseinstellungen

|Name der App-Einstellung |  Definition | Wert |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Die Haupterweiterung, die die Laufzeitüberwachung steuert. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Wichtige Features werden nur im Standardmodus aktiviert, um eine optimale Leistung zu gewährleisten. | `default` oder `recommended` |
|InstrumentationEngine_EXTENSION_VERSION | Legt fest, ob die Engine zum binären erneuten Generieren `InstrumentationEngine` aktiviert ist. Diese Einstellung wirkt sich auf die Leistung aus und beeinträchtigt den Kaltstart/die Startzeit. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Legt fest, ob SQL- und Azure-Tabellentext gemeinsam mit Abhängigkeitsaufrufen erfasst wird. Leistungswarnung: die Kaltstartzeit der Anwendung wird beeinträchtigt. Diese Einstellung erfordert die `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>App Service-Anwendungseinstellungen mit dem Azure Resource Manager.

Sie können die App Service-Anwendungseinstellungen mit [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md) verwalten und konfigurieren. Diese Methode kann bei der Bereitstellung neuer App Service-Ressourcen mit Azure Resource Manager-Automatisierung oder zur Änderung der Einstellungen vorhandener Ressourcen verwendet werden.

Die Grundstruktur des JSON-Codes für die Anwendungseinstellungen für eine App Service-Instanz sieht folgendermaßen aus:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Ein Beispiel für eine Azure Resource Manager-Vorlage mit Anwendungseinstellungen für Application Insights finden Sie in [dieser Vorlage](https://github.com/Andrew-MSFT/BasicImageGallery), insbesondere im Abschnitt ab [Zeile 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisieren der Erstellung einer Application Insights-Ressource und Verknüpfen Ihrer neu erstellten App Service-Instanz

Das Erstellen einer Azure Resource Manager-Vorlage mit allen Application Insights-Standardeinstellungen ähnelt am Anfang dem Erstellen einer neuen Web-App mit aktiviertem Application Insights-Dienst.

Wählen Sie die Option **Automation-Optionen**.

   ![Menü zum Erstellen einer App Service-Web-App](./media/azure-web-apps/create-web-app.png)

Mit dieser Option wird die aktuellste Azure Resource Manager-Vorlage mit allen erforderlichen Einstellungen generiert.

  ![App Service-Web-App-Vorlage](./media/azure-web-apps/arm-template.png)

Es folgt ein Beispiel. Ersetzen Sie alle Instanzen von `AppMonitoredSite` durch Ihren Websitenamen:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Aktivierung über PowerShell

Zum Aktivieren der Anwendungsüberwachung über PowerShell müssen nur die zugrunde liegenden Anwendungseinstellungen geändert werden. Es folgt ein Beispiel, bei dem die Anwendungsüberwachung für eine Website namens „AppMonitoredSite“ in der Ressourcengruppe „AppMonitoredRG“ aktiviert wird und Daten zum Senden an den Instrumentierungsschlüssel „012345678-Abcd-ef01-2345-6789abcd“ konfiguriert werden.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Upgrade der Überwachungserweiterung/des Überwachungsagents

### <a name="upgrading-from-versions-289-and-up"></a>Upgrade von Version 2.8.9 und höher

Das Upgrade von Version 2.8.9 erfolgt automatisch ohne zusätzliche Aktionen. Die neuen Überwachungsfunktionen werden dem Ziel-App-Service im Hintergrund bereitgestellt und beim Neustart der Anwendung übernommen.

Informationen zur aktuell verwendeten Version der Erweiterung finden Sie unter `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

![Screenshot des URL-Pfads http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Upgrade von Version 1.0.0 – 2.6.5

Ab Version 2.8.9 wird die vorinstallierte Websiteerweiterung verwendet. Bei einer früheren Version können Sie eine Aktualisierung auf zwei Arten vornehmen:

* [Upgrade durch Aktivierung über das Portal](#enable-application-insights). (Selbst wenn die Application Insights-Erweiterung für Azure App Service installiert ist, wird nur die Schaltfläche **Aktivieren** auf der Benutzeroberfläche angezeigt. Im Hintergrund wird die alte private Websiteerweiterung entfernt.)

* [Upgrade über PowerShell](#enabling-through-powershell):

    1. Legen Sie die Anwendungseinstellungen so fest, dass die vorinstallierte Websiteerweiterung „ApplicationInsightsAgent“ aktiviert wird. Informationen finden Sie unter [Aktivierung über PowerShell](#enabling-through-powershell).
    2. Entfernen Sie die private Websiteerweiterung mit dem Namen Application Insights-Erweiterung für Azure App Service manuell.

Wenn das Upgrade für eine frühere Version als 2.5.1 ausgeführt wird, vergewissern Sie sich, dass die ApplicationInsights-DLL-Dateien aus dem Ordner „bin“ der Anwendung entfernt werden ([siehe Schritte zur Problembehandlung](#troubleshooting)).

## <a name="troubleshooting"></a>Problembehandlung

Nachfolgend finden Sie schrittweise Anleitungen zur Problembehandlung für die Erweiterung/Agent-basierte Überwachung für Anwendungen, die auf ASP.NET und ASP.NET Core basieren und unter Azure App Services ausgeführt werden.

> [!NOTE]
> Der empfohlene Ansatz zur Überwachung von Java-Anwendungen ist die automatische Instrumentierung ohne Änderung des Codes. Befolgen Sie die Leitlinien für den [Application Insights Java 3.0-Agent](./java-in-process-agent.md).


1. Überprüfen Sie, ob die Anwendung über `ApplicationInsightsAgent` überwacht wird.
    * Überprüfen Sie, ob die App-Einstellung `ApplicationInsightsAgent_EXTENSION_VERSION` auf den Wert „~2“ festgelegt ist.
2. Stellen Sie sicher, dass die Anwendung die Anforderungen für die Überwachung erfüllt.
    * Navigieren Sie zu `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

    ![Screenshot der Ergebnisseite unter https://yoursitename.scm.azurewebsites/applicationinsights](./media/azure-web-apps/app-insights-sdk-status.png)

    * Stellen Sie sicher, dass für `Application Insights Extension Status``Pre-Installed Site Extension, version 2.8.12.1527, is running.`angegeben ist. 
    * Wird diese nicht ausgeführt, folgen Sie den [Anweisungen zum Aktivieren der Application Insights-Überwachung](#enable-application-insights).

    * Stellen Sie sicher, dass die Statusquelle vorhanden ist und folgendermaßen aussieht: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Wenn kein ähnlicher Wert vorhanden ist, bedeutet dies, dass die Anwendung derzeit nicht ausgeführt oder nicht unterstützt wird. Um sicherzustellen, dass die Anwendung ausgeführt wird, rufen Sie die Anwendungs-URL/Anwendungsendpunkte manuell auf, wodurch die Laufzeitinformationen verfügbar werden.

    * Stellen Sie sicher, dass `IKeyExists``true` ist:
        * Wenn der Wert `false` lautet, fügen Sie den Anwendungseinstellungen `APPINSIGHTS_INSTRUMENTATIONKEY` und `APPLICATIONINSIGHTS_CONNECTION_STRING` mit Ihrer Instrumentierungsschlüssel-GUID hinzu.

    * Stellen Sie sicher, dass keine Einträge für `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly` und `AppContainsAspNetTelemetryCorrelationAssembly` vorhanden sind.
        * Wenn einer dieser Einträge vorhanden ist, entfernen Sie die folgenden Pakete aus Ihrer Anwendung: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` und `Microsoft.AspNet.TelemetryCorrelation`.
        * Nur für ASP.NET Core-Apps: Falls sich Ihre Anwendung auf Application Insights Pakete bezieht, wenn Sie Ihre App z. B. bereits zuvor mit dem [ASP.NET Core SDK](./asp-net-core.md) instrumentiert (oder dieses versucht) haben, ist das Aktivieren der App Service-Integration unter Umständen nicht wirksam, und die Daten werden möglicherweise nicht in Application Insights angezeigt. Um das Problem zu beheben, aktivieren Sie im Portal die Option „Interoperabel mit Application Insights SDK“, und Sie sehen die Daten in Application Insights. 
        > [!IMPORTANT]
        > Diese Funktion befindet sich in der Vorschauphase. 

        ![Aktivieren der Einstellung der vorhandenen App](./media/azure-web-apps/netcore-sdk-interop.png)

        Die Daten werden jetzt mithilfe eines codelosen Ansatzes gesendet, auch wenn Application Insights SDK ursprünglich verwendet wurde oder versucht wurde, es zu verwenden.

        > [!IMPORTANT]
        > Wenn die Anwendung das Application Insights SDK verwendet hat, um Telemetriedaten zu senden, werden diese Telemetriedaten deaktiviert – anders ausgedrückt: Benutzerdefinierte Telemetriedaten, sofern vorhanden, z. B. alle Track*()-Methoden und alle benutzerdefinierten Einstellungen, wie z. B. Sampling, werden deaktiviert. 


### <a name="php-and-wordpress-are-not-supported"></a>PHP und WordPress werden nicht unterstützt.

PHP- und WordPress-Sites werden nicht unterstützt. Zurzeit ist kein offiziell unterstütztes SDK/unterstützter Agent für die serverseitige Überwachung dieser Workloads verfügbar. Das manuelle Instrumentieren clientseitiger Transaktionen auf einer PHP- oder WordPress-Website durch Hinzufügen von clientseitigem JavaScript zu Ihren Webseiten kann jedoch mithilfe des [JavaScript SDK](./javascript.md) erreicht werden.

Die folgende Tabelle enthält eine ausführlichere Beschreibung der Bedeutung dieser Werte, der zugrunde liegenden Ursachen und der empfohlenen Problembehebungen:

|Problemwert|Erklärung|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Dieser Wert zeigt an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen. Mögliche Ursache ist ein Verweis auf `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` oder `Microsoft.ApplicationInsights`.  | Entfernen Sie die Verweise. Einige dieser Verweise werden standardmäßig aus bestimmten Visual Studio-Vorlagen hinzugefügt, und ältere Versionen von Visual Studio können Verweise zu `Microsoft.ApplicationInsights` hinzufügen.
|`AppAlreadyInstrumented:true` | Wenn die Anwendung auf ASP.NET Core 2.1 oder 2.2 ausgerichtet ist, zeigt dieser Wert an, dass die Erweiterung erkannt hat, dass ein Teil des SDK bereits in der Anwendung vorhanden ist, und der Vorgang wird abgebrochen. | Kunden mit .NET Core 2.1, 2.2 wird [empfohlen](https://github.com/aspnet/Announcements/issues/287), stattdessen das Microsoft.AspNetCore.App-Metapaket zu verwenden. Aktivieren Sie außerdem „Interoperabel mit Application Insights SDK“ im Portal (siehe die obigen Anweisungen).|
|`AppAlreadyInstrumented:true` | Dieser Wert kann auch durch Vorhandensein der oben genannten DLLs im App-Ordner aus einer früheren Bereitstellung verursacht werden. | Bereinigen Sie den App-Ordner, um sicherzustellen, dass diese DLLs entfernt werden. Überprüfen Sie sowohl das Verzeichnis „bin“ Ihrer lokalen App als auch das Verzeichnis „wwwroot“ im App Service. (Überprüfen des Verzeichnisses „wwwroot“ Ihrer App Service-Web-App: Erweiterte Tools (Kudu) > Debugging-Konsole > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Dieser Wert gibt an, dass die Erweiterung Verweise auf `Microsoft.AspNet.TelemetryCorrelation` in der Anwendung gefunden hat, und der Vorgang wird abgebrochen. | Entfernen Sie den Verweis.
|`AppContainsDiagnosticSourceAssembly**:true`|Dieser Wert gibt an, dass die Erweiterung Verweise auf `System.Diagnostics.DiagnosticSource` in der Anwendung gefunden hat, und der Vorgang wird abgebrochen.| Entfernen Sie für ASP.NET den Verweis. 
|`IKeyExists:false`|Dieser Wert gibt an, dass der Instrumentierungsschlüssel nicht in der App-Einstellung `APPINSIGHTS_INSTRUMENTATIONKEY` vorhanden ist. Mögliche Ursachen: Die Werte wurden möglicherweise versehentlich entfernt, nicht im Automatisierungsskript festgelegt usw. | Stellen Sie sicher, dass die Einstellung in den App Service-Anwendungseinstellungen vorhanden ist.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>Die Kombination von APPINSIGHTS_JAVASCRIPT_ENABLED und urlCompression wird nicht unterstützt.

Wenn Sie „APPINSIGHTS_JAVASCRIPT_ENABLED=true“ in Fällen verwenden, in denen der Inhalt codiert ist, erhalten Sie möglicherweise Fehlermeldungen ähnlich der folgenden: 

- 500-URL-Rewrite-Fehler
- Der 500.53-URL-Rewrite-Modul-Fehler mit der Meldung, dass Ausgangs-Rewrite-Regeln nicht angewendet werden können, wenn der Inhalt der HTTP-Antwort codiert ist („gzip“). 

Dies liegt daran, dass die Anwendungseinstellung APPINSIGHTS_JAVASCRIPT_ENABLED auf „true“ gesetzt und die Inhaltscodierung zur gleichen Zeit vorhanden ist. Dieses Szenario wird noch nicht unterstützt. Die Problemumgehung besteht darin, APPINSIGHTS_JAVASCRIPT_ENABLED aus Ihren Anwendungseinstellungen zu entfernen. Leider bedeutet dies, dass manuelle SDK-Verweise für Ihre Webseiten erforderlich sind, wenn die JavaScript-Instrumentierung auf der Client/Browser-Seite noch erforderlich ist. Führen Sie die [Anweisungen](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) für manuelle Instrumentierung mit dem JavaScript SDK aus.

Aktuelle Informationen zu Application Insights-Agent/Erweiterung finden Sie in den [Versionshinweisen](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Die mit Web-Apps bereitgestellte Standardwebsite unterstützt keine automatische clientseitige Überwachung

Wenn Sie eine Web-App mit der `ASP.NET`- oder `ASP.NET Core`-Runtime in Azure App Services erstellen, wird eine einzelne statische HTML-Seite als Starter-Website bereitgestellt. Die statische Webseite lädt auch ein ASP.NET-verwaltetes Webpart in IIS. Dies ermöglicht das Testen der serverseitigen Überwachung ohne Code, unterstützt jedoch nicht die automatische clientseitige Überwachung.

Wenn Sie die server- und clientseitige Überwachung ohne Code für ASP.NET oder ASP.NET Core in einer Azure App Services-Web-App testen möchten, wird empfohlen, den offiziellen Anleitungen zum [Erstellen einer ASP.NET Core-Web-App](../../app-service/quickstart-dotnetcore.md) und [Erstellen einer ASP.NET Framework-Web-App](../../app-service/quickstart-dotnet-framework.md) zu folgen und anschließend die Anweisungen im aktuellen Artikel zum Aktivieren der Überwachung zu verwenden.

### <a name="connection-string-and-instrumentation-key"></a>Verbindungszeichenfolge und Instrumentierungsschlüssel

Bei Verwendung von Überwachung ohne Code ist nur die Verbindungszeichenfolge erforderlich. Es wird jedoch weiterhin empfohlen, den Instrumentierungsschlüssel festzulegen, um die Abwärtskompatibilität mit älteren Versionen des SDKs aufrechtzuerhalten, wenn eine manuelle Instrumentierung ausgeführt wird.

## <a name="release-notes"></a>Versionshinweise

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](./web-app-extension-release-notes.md).

## <a name="next-steps"></a>Nächste Schritte
* [Ausführen des Profilers in Ihrer Live-App](./profiler.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample): Überwachen Sie Azure Functions mit Application Insights
* [Ermöglichen des Sendens von Azure-Diagnosedaten an Application Insights](../platform/diagnostics-extension-to-application-insights.md)
* [Überwachen von Dienstintegritätsmetriken](../platform/data-platform.md) , um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist
* [Empfangen von Warnbenachrichtigungen](../platform/alerts-overview.md) , wenn ein Vorgangsereignis auftritt oder Metriken einen Schwellenwert überschreiten.
* Verwenden von [Application Insights für JavaScript-Apps und Webseiten](javascript.md), um Clienttelemetriedaten von den Browsern zu erhalten, mit denen auf eine Webseite zugegriffen wird
* [Einrichten von Verfügbarkeitswebtests](monitor-web-app-availability.md), um benachrichtigt zu werden, wenn Ihre Website nicht verfügbar ist