---
title: Aktivieren des Momentaufnahmedebuggers für .NET-Apps in Azure App Service | Microsoft-Dokumentation
description: Aktivieren des Momentaufnahmedebuggers für .NET-Apps in Azure App Service
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60784077"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Aktivieren des Momentaufnahmedebuggers für .NET-Apps in Azure App Service

Der Momentaufnahmedebugger kann derzeit für ASP.NET- und ASP.NET Core-Apps verwendet werden, die unter Azure App Service in Windows-Dienstplänen ausgeführt werden.

## <a id="installation"></a> Aktivieren des Momentaufnahmedebuggers
Befolgen Sie die unten angegebenen Anweisungen, um den Momentaufnahmedebugger für eine App zu aktivieren. Wenn Sie eine andere Art von Azure-Dienst ausführen, helfen Ihnen diese Anweisungen zum Aktivieren des Momentaufnahmedebuggers auf anderen unterstützten Plattformen weiter:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-Dienste](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines und VM-Skalierungsgruppen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokale virtuelle oder physische Computer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Der Application Insights-Momentaufnahmedebugger ist als Teil der App Services-Runtime zwar bereits vorinstalliert, muss aber noch aktiviert werden, um Momentaufnahmen für Ihre App Service-Apps zu erhalten. Führen Sie die unten angegebenen Schritte zum Aktivieren des Momentaufnahmedebuggers aus, nachdem Sie eine App bereitgestellt haben (auch wenn Sie das Application Insights SDK in den Quellcode eingefügt haben).

1. Wechseln Sie zum Bereich **App Services** im Azure-Portal.
2. Navigieren Sie zum Bereich **Einstellungen > Application Insights**.

   ![Aktivieren von App Insights im App Services-Portal](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Befolgen Sie die Anweisungen im Bereich zum Erstellen einer neuen Ressource, oder wählen Sie eine vorhandene App Insights-Ressource aus, um Ihre App zu überwachen. Stellen Sie außerdem sicher, dass beide Schalter für den Momentaufnahmedebugger auf **Ein** festgelegt sind.

   ![Hinzufügen der App Insights-Websiteerweiterung][Enablement UI]

4. Der Momentaufnahmedebugger wurde mithilfe einer App Services-App-Einstellung aktiviert.

    ![App-Einstellung für den Momentaufnahmedebugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Deaktivieren des Momentaufnahmedebuggers

Führen Sie die gleichen Schritte wie zum **Aktivieren des Momentaufnahmedebuggers** aus, legen Sie jedoch beide Schalter für den Momentaufnahmedebugger auf **Aus** fest.
Es wird empfohlen, den Momentaufnahmedebugger für alle Ihre Apps zu aktivieren, um die Diagnose von Anwendungsausnahmen zu erleichtern.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Azure Application Insights in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

