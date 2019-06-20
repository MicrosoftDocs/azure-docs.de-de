---
title: Übersicht über Azure Serverless | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Erstellen leistungsstarker Lösungen in der Cloud, ohne über die Infrastruktur nachdenken zu müssen.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 9cd1946d8fa670764bcc95b82298d07cc70417bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60684107"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Übersicht: Azure Serverless mit Azure Logic Apps und Azure Functions

[Serverlose](https://azure.microsoft.com/solutions/serverless/) Anwendungen bieten folgende Vorteile: eine erhöhte Entwicklungsgeschwindigkeit, Reduzierung des erforderlichen Codes und Vereinfachung der Staffelung.  In diesem Artikel werden verschiedene Attribute serverloser Lösungen und Angebote von Azure Serverless behandelt.

## <a name="what-is-serverless"></a>Was bedeutet serverlos?

Serverlos bedeutet nicht, dass keine Server vorhanden sind – es bedeutet lediglich, dass sich Entwickler nicht um ihre Server kümmern müssen.  Ein großer Teil der herkömmlichen Anwendungsentwicklung liegt darin, Fragen zum Skalieren, Hosten und Überwachen von Anwendungen zu beantworten, um die Anforderungen der Anwendung zu erfüllen.  Durch Serverless werden diese Fragen als Teil der Lösung aufgegriffen.  Darüber hinaus werden serverlose Anwendungen auf Basis eines verbrauchsbasierten Plans abgerechnet.  Wenn die Anwendung nie verwendet wird, fallen nie Gebühren an.  Diese Funktionen ermöglichen es Entwicklern, sich ausschließlich auf die Geschäftslogik der Lösung zu fokussieren.

Die Hauptdienste in Azure rund um Serverless sind [Azure Functions](https://azure.microsoft.com/services/functions/) und [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Beide dieser Lösungen folgen den oben beschriebenen Prinzipien und ermöglichen Entwicklern, robuste Cloud-Anwendungen mit minimalem Code zu erstellen.

## <a name="what-are-azure-functions"></a>Was ist Azure Functions?

Azure Functions ist eine Lösung, mit der Sie ganz einfach kleinere Codeelemente (Funktionen) in der Cloud ausführen können. Sie können sich auf das Schreiben des Codes beschränken, der für das jeweilige Problem erforderlich ist, ohne sich über eine gesamte Anwendung oder die Infrastruktur für ihre Ausführung Gedanken machen zu müssen. Mit Functions können Entwickler noch produktiver arbeiten und ihre bevorzugte Programmiersprache verwenden (z.B. C#, F#, Node.js, Python oder PHP). Sie bezahlen nur für die Zeit, in der Ihr Code ausgeführt wird. Azure skaliert flexibel nach Bedarf.

Unter [Erstellen Sie Ihre erste Funktion in Azure Functions](../azure-functions/functions-create-first-azure-function.md)können Sie sich direkt mit der Verwendung von Azure Functions vertraut machen. Technische Informationen zu Functions finden Sie in der [Entwicklerreferenz](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Was ist Azure Logic Apps?

Azure Logic Apps ermöglicht die Vereinfachung und die Implementierung skalierbarer Integrationen und Workflows in die Cloud. Es wird ein visueller Designer bereitgestellt, mit dem Sie Ihre Prozesse in Form von Schritten (als Workflow bezeichnet) modellieren und automatisieren können.  Es gibt [viele Connectors](../connectors/apis-list.md) für Cloud- und lokale Dienste, mit denen Sie schnell eine Verbindung zwischen einer serverlosen App und anderen APIs herstellen können.  Eine Logik-App beginnt mit einem Trigger (z.B. „Wenn Dynamics CRM ein Konto hinzugefügt wird“), und nach der Auslösung sind viele verschiedene Kombinationen von Aktionen, Konvertierungen und Bedingungslogikabläufen möglich.  Logic Apps ist eine gute Wahl zum sorgfältigen Organisieren verschiedener Azure Functions in einem Prozess – besonders, wenn der Prozess die Interaktion mit einem externen System oder einer API benötigt.

Starten Sie zum Einstieg in Logic Apps mit dem [Erstellen Ihrer ersten Logik-App](quickstart-create-first-logic-app-workflow.md).  Technische Informationen zu Logic Apps finden Sie in der [Entwicklerreferenz](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Wie kann ich serverlose Anwendungen in Azure erstellen und bereitstellen?

Azure bietet umfangreiche Tools für die Entwicklung, Bereitstellung und Verwaltung von serverlosen Apps.  Apps können direkt im Azure-Portal oder mit den [Tools von Visual Studio](logic-apps-serverless-get-started-vs.md) (in englischer Sprache) erstellt werden.  Sobald eine Anwendung entwickelt wurde, kann sie [sofort bereitgestellt werden](logic-apps-create-deploy-template.md).  Azure bietet auch eine Überwachung für serverlose Apps.  Auf diese Überwachung kann direkt über das Azure-Portal, über die API oder über SDKs oder alternativ über integrierte Tools für Azure Monitor-Protokolle und Application Insights zugegriffen werden.

## <a name="next-steps"></a>Nächste Schritte

* [Get started building a Serverless app in Visual Studio (Erste Schritte zum Erstellen einer serverlosen App in Visual Studio)](logic-apps-serverless-get-started-vs.md)
* [Erstellen eines Customer Insights-Echtzeit-Dashboards mit Azure Logic Apps und Azure Functions](logic-apps-scenario-social-serverless.md)
* [Erstellen von Vorlagen für die Bereitstellungs- und Versionsverwaltung von Logik-Apps](logic-apps-create-deploy-template.md)
