---
title: 'Application Insights: Programmiersprachen, Plattformen und Integrationsmöglichkeiten | Microsoft Docs'
description: Verfügbare Programmiersprachen, Plattformen und Integrationsmöglichkeiten für Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/16/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 9ef5c9e8b4a46b4bf2bed25027602fb98a09dbf0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256103"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Entwickleranalysen: Programmiersprachen, Plattformen und Integrationsmöglichkeiten
Bei diesen Elementen handelt es sich um Implementierungen von [Application Insights](../../azure-monitor/app/app-insights-overview.md), von denen wir gehört haben. Einige davon stammen von Drittanbietern.

## <a name="languages---officially-supported-by-application-insights-team"></a>Programmiersprachen – offiziell vom Application Insights-Team unterstützt
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript-Webseiten](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>Programmiersprachen – von der Community unterstützt
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [Go](https://github.com/Microsoft/ApplicationInsights-go)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Alles andere](#projects)

## <a name="platforms-and-frameworks"></a>Plattformen und Frameworks
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET – Für Apps, die bereits aktiv sind.](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md): Einschließlich Web- und Workerrollen
* [Azure-Funktionen](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE Stack](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Universelle Windows-App](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows-Desktopanwendungen, -Dienste und -Workerrollen](../../azure-monitor/app/windows-desktop.md)
* [Alles andere](#projects)

## <a name="logging-frameworks"></a>Protokollierungsframeworks
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog oder System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J oder Logback](../../azure-monitor/app/java-trace-logs.md)
* [Semantic Logging (SLAB):](https://github.com/fidmor89/SLAB_AppInsights) In [Semantic Logging Application Block](https://msdn.microsoft.com/library/dn440729.aspx) integriert
* [Cloudbasierte Auslastungstests](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash-Plug-in](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>Content Management-Systeme
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Export und Datenanalyse
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> Erstellen eines eigenen SDK
Wenn für Ihre Programmiersprache oder Plattform noch kein SDK vorhanden ist, können Sie eins erstellen. Sehen Sie sich den Code der vorhandenen SDKs an, die im [Application Insights-SDK-Projekt auf GitHub](https://github.com/Microsoft/AppInsights-Home)aufgeführt sind.
