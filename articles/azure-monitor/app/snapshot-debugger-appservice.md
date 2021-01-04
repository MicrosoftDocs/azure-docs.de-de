---
title: Aktivieren des Momentaufnahmedebuggers für .NET-Apps in Azure App Service | Microsoft-Dokumentation
description: Aktivieren des Momentaufnahmedebuggers für .NET-Apps in Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6d111379cbeb0ea4eeac9ea0868b6d263a0a540c
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435721"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Aktivieren des Momentaufnahmedebuggers für .NET-Apps in Azure App Service

Der Momentaufnahmedebugger kann derzeit für ASP.NET- und ASP.NET Core-Apps verwendet werden, die unter Azure App Service in Windows-Dienstplänen ausgeführt werden. Wenn Sie den Debugger für Momentaufnahmen verwenden, empfehlen wir, Ihre Anwendung auf der Dienstebene „Basic“ oder höher auszuführen. Bei den meisten Anwendungen haben die Dienstebenen „Free“ und „Shared“ nicht genügend Arbeitsspeicher zum Speichern von Momentaufnahmen.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Aktivieren des Momentaufnahmedebuggers
Befolgen Sie die unten angegebenen Anweisungen, um den Momentaufnahmedebugger für eine App zu aktivieren. Wenn Sie eine andere Art von Azure-Dienst ausführen, helfen Ihnen diese Anweisungen zum Aktivieren des Momentaufnahmedebuggers auf anderen unterstützten Plattformen weiter:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-Dienste](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines und VM-Skalierungsgruppen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokale virtuelle oder physische Computer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Wenn Sie eine Vorschauversion von .NET Core verwenden, führen Sie zuerst die Anweisungen unter [Aktivieren des Momentaufnahmedebuggers für andere Umgebungen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) aus, um das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in die Anwendung zu integrieren, und führen Sie dann die übrigen nachstehenden Anweisungen aus. 

Der Application Insights-Momentaufnahmedebugger ist als Teil der App Services-Runtime zwar bereits vorinstalliert, muss aber noch aktiviert werden, um Momentaufnahmen für Ihre App Service-Apps zu erhalten. Führen Sie die unten angegebenen Schritte zum Aktivieren des Momentaufnahmedebuggers aus, nachdem Sie eine App bereitgestellt haben (auch wenn Sie das Application Insights SDK in den Quellcode eingefügt haben).

1. Navigieren Sie zur Azure-Systemsteuerung für Ihre App Service-Instanz.
2. Wechseln Sie zur Seite **Einstellungen > Application Insights**.

   ![Aktivieren von App Insights im App Services-Portal](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Befolgen Sie die Anweisungen auf der Seite, um eine neue Ressource zu erstellen, oder wählen Sie eine vorhandene App Insights-Ressource aus, um Ihre App zu überwachen. Stellen Sie außerdem sicher, dass beide Schalter für den Momentaufnahmedebugger auf **Ein** festgelegt sind.

   ![Hinzufügen der App Insights-Websiteerweiterung][Enablement UI]

4. Der Momentaufnahmedebugger wurde mithilfe einer App Services-App-Einstellung aktiviert.

    ![App-Einstellung für den Momentaufnahmedebugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Deaktivieren des Momentaufnahmedebuggers

Führen Sie die gleichen Schritte wie zum **Aktivieren des Momentaufnahmedebuggers** aus, legen Sie jedoch beide Schalter für den Momentaufnahmedebugger auf **Aus** fest.
Es wird empfohlen, den Momentaufnahmedebugger für alle Ihre Apps zu aktivieren, um die Diagnose von Anwendungsausnahmen zu erleichtern.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

Bei einer Azure App Service-App können Sie App-Einstellungen in einer Azure Resource Manager-Vorlage festlegen, um den Momentaufnahmedebugger und Profiler zu aktivieren. Sie fügen eine Konfigurationsressource hinzu, die die App-Einstellungen als untergeordnete Ressource der Website enthält:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Nächste Schritte

- Generieren Sie Datenverkehr für Ihre Anwendung, der eine Ausnahme auslösen kann. Warten Sie dann 10 bis 15 Minuten, bis die Momentaufnahmen an die Application Insights-Instanz gesendet werden.
- Weitere Informationen finden Sie unter [Momentaufnahmen](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) im Azure-Portal.
- Hilfe bei der Behandlung von Problemen mit dem Momentaufnahmedebugger finden Sie unter [Problembehandlung für Momentaufnahmedebugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

