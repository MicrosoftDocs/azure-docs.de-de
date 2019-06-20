---
title: XML-Nachrichten für die B2B-Unternehmensintegration – Azure Logic Apps | Microsoft-Dokumentation
description: Verarbeiten, Überprüfen, Transformieren und Anreichern von XML-Nachrichten für B2B-Lösungen in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996572"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-Nachrichten und Flatfiles in Azure Logic Apps mit Enterprise Integration Pack

Mit Logik-Apps können Sie XML-Nachrichten verarbeiten, die Sie senden und empfangen. Dieses Feature ist im Enterprise Integration Pack enthalten. Für Benutzer mit einem BizTalk Server-Hintergrund bietet Ihnen das Enterprise Integration Pack ähnliche Möglichkeiten zum Transformieren und Überprüfen von Nachrichten, Arbeiten mit Flatfiles und sogar Verwenden von XPath, um eine Nachricht mit bestimmten Eigenschaften anzureichern oder bestimmte Eigenschaften aus einer Nachricht zu extrahieren. 

Diese Features erweitern die Art und Weise, in der Sie Nachrichten innerhalb Ihres Workflows für Benutzer verarbeiten, für die dieser Bereich neu ist. Wenn Sie beispielsweise in einem Business-to-Business-Szenario mit bestimmten XML-Schemas arbeiten, können Sie das Enterprise Integration Pack verwenden, um die Art der Verarbeitung dieser Nachrichten durch Ihr Unternehmen zu verbessern. 

Das Enterprise Integration Pack enthält: 

* [XML-Überprüfung](logic-apps-enterprise-integration-xml-validation.md "Informationen zur Überprüfung von XML-Nachrichten") – Überprüfen Sie eine ein- oder ausgehende XML-Nachricht anhand eines bestimmten Schemas.
* [XML-Transformation](../logic-apps/logic-apps-enterprise-integration-transform.md "Informationen zu XML-Nachrichtentransformationen und Zuordnungen") – Konvertieren Sie eine XML-Nachricht basierend auf Ihren Anforderungen oder den Anforderungen eines Partners, bzw. passen Sie sie an.
* [Flatfilecodierung und -decodierung](logic-apps-enterprise-integration-flatfile.md "Informationen zur Flatfilecodierung und -decodierung") – Codieren oder decodieren Sie eine Flatfile. SAP akzeptiert und sendet z.B. IDOC-Dateien im Flatfileformat. Viele Integrationsplattformen einschließlich Logic Apps erstellen XML-Nachrichten. Daher können Sie eine Logik-App erstellen, die mit dem Flatfileencoder XML-Dateien in Flatfiles „konvertiert“. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) – Reichern Sie eine Nachricht an, und extrahieren Sie bestimmte Eigenschaften aus der Nachricht. Mithilfe der extrahierten Eigenschaften können Sie die Nachricht an einen Ziel- oder Zwischenendpunkt weiterleiten.

## <a name="try-it-out"></a>Ausprobieren
[Stellen Sie eine voll funktionsfähige Logik-App](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-Beispiel) mit den XML-Features von Azure Logic Apps bereit.

## <a name="learn-more"></a>Weitere Informationen
[Weitere Informationen zum Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack")
