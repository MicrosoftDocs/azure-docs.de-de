---
title: Profilerstellung für Live-Azure App Service-Apps mit Application Insights | Microsoft-Dokumentation
description: Profilerstellung für Live-Apps in Azure App Service mit Application Insights Profiler
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 41aec98c2a8776ae128389679ad0acb324596ec4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546827"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilerstellung für Live-Azure App Service-Apps mit Application Insights

Sie können Profiler für ASP.NET- und ASP.NET Core-Apps verwenden, die unter Azure App Service mit dem Dienstebene „Basic“ oder höher ausgeführt werden. Profiler unter Linux lässt sich derzeit nur mit [dieser Methode](profiler-aspnetcore-linux.md) aktivieren.

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Aktivieren von Profiler für Ihre App
Befolgen Sie die unten angegebene Anleitung, um Profiler für eine App zu aktivieren. Wenn Sie eine andere Art von Azure-Dienst ausführen, helfen Ihnen diese Anleitungen zum Aktivieren von Profiler auf anderen unterstützten Plattformen weiter:
* [Cloud Services](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric-Anwendungen](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Virtuelle Computer](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler ist als Teil der App Services-Runtime bereits vorinstalliert. In den folgenden Schritten wird erläutert, wie Sie das Tool für Ihren App Service aktivieren. Führen Sie diese Schritte aus, auch wenn Sie das App Insights SDK zum Zeitpunkt der Erstellung in Ihrer Anwendung eingefügt haben.

1. Navigieren Sie zur Azure-Systemsteuerung für Ihre App Service-Instanz.
1. Aktivieren Sie die Einstellung „Always On“ für Ihren App Service. Sie finden diese Einstellung unter **Einstellungen**, Seite **Konfiguration** (siehe Screenshot im nächsten Schritt). Klicken Sie dann auf die Registerkarte **Allgemeine Einstellungen**.
1. Navigieren Sie zur Seite **Einstellungen > Application Insights**.

   ![Aktivieren von App Insights im App Services-Portal](./media/profiler/AppInsights-AppServices.png)

1. Befolgen Sie die Anweisungen im Bereich zum Erstellen einer neuen Ressource, oder wählen Sie eine vorhandene App Insights-Ressource aus, um Ihre App zu überwachen. Vergewissern Sie sich außerdem, dass der Profiler auf **Ein** festgelegt ist. Wenn sich Ihre Application Insights-Ressource in einem anderen Abonnement als Ihr App Service befindet, können Sie Application Insights auf dieser Seite nicht konfigurieren. Sie können die Konfiguration jedoch manuell vornehmen, indem Sie die gewünschten App-Einstellungen manuell erstellen. [Der nächste Abschnitt enthält Anweisungen zum manuellen Aktivieren von Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Hinzufügen der App Insights-Websiteerweiterung][Enablement UI]

1. Der Profiler wurde mithilfe einer App Services-App-Einstellung aktiviert.

    ![App-Einstellung für Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Manuelles Aktivieren von Profiler oder Aktivieren über Azure Resource Manager
Application Insights Profiler kann aktiviert werden, indem Sie App-Einstellungen für Ihren Azure App Service erstellen. Auf der oben abgebildeten Seite mit den Optionen werden diese App-Einstellungen automatisch erstellt. Sie können die Erstellung dieser Einstellungen jedoch auch mithilfe einer Vorlage oder auf andere Weise automatisieren. Diese Einstellungen funktionieren auch dann, wenn sich die Application Insights-Ressource in einem anderen Abonnement als Ihre Azure App Service-Instanz befindet.
Im Folgenden finden Sie die Einstellungen, die zum Aktivieren von Profiler erforderlich sind:

|App-Einstellung    | Wert    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey für Ihre Application Insights-Ressource    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Sie können diese Werte mithilfe von [Azure Resource Manager-Vorlagen](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell](/powershell/module/az.websites/set-azwebapp) oder der [Azure-Befehlszeilenschnittstelle](/cli/azure/webapp/config/appsettings?view=azure-cli-latest) festlegen.

### <a name="enabling-profiler-for-other-clouds-manually"></a>Manuelles Aktivieren von Profiler für andere Clouds

Wenn Sie Profiler für andere Clouds aktivieren möchten, können Sie die folgenden App-Einstellungen verwenden.

|App-Einstellung    | US Government-Werte| China-Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsProfilerEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Deaktivieren von Profiler

Wenn Sie Profiler für die Instanz einer einzelnen App beenden oder neu starten möchten, können Sie unter **WebJobs** den Webauftrag mit dem Namen „ApplicationInsightsProfiler3“ beenden. Auch wenn der Profiler wie oben beschrieben mithilfe des Schalters auf der Application Insights-Seite deaktiviert wurde, wird der Profilerprozess weiterhin ausgeführt. Der Profiler prüft, ob er aktiviert ist. Wenn er deaktiviert ist, wechselt er für einen Zeitraum in den Ruhemodus, bevor er erneut geprüft wird. Bei Deaktivierung führt er keinerlei Profilerstellung aus. Wenn Sie diesen Webauftrag deaktivieren, wird der Profilerprozess gar nicht ausgeführt, auch nicht um zu überprüfen, ob er aktiviert ist.

  ![Deaktivieren von Profiler für einen Webauftrag][disable-profiler-webjob]

Es wird empfohlen, Profiler für alle Apps zu aktivieren, um Leistungsprobleme möglichst schnell zu ermitteln.

Profiler-Dateien können gelöscht werden, wenn Änderungen mithilfe von WebDeploy in der Webanwendung bereitgestellt werden. Sie können die Löschung verhindern, indem Sie festlegen, dass der Ordner „App_Data“ vom Löschen bei der Bereitstellung ausgeschlossen wird. 


## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Azure Application Insights in Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

