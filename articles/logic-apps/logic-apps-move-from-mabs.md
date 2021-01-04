---
title: Migrieren von Apps von BizTalk Services zu Azure Logic Apps
description: Vorgehensweise zum Verschieben Ihrer Apps und Lösungen von Microsoft Azure BizTalk Services (MABS) zu Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 6c07ab4b18c017bd29723d2640129b8e67374e3c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023651"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migrieren Ihrer Apps und Lösungen von BizTalk Services zu Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) läuft demnächst aus. Um Ihre MABS-Integrationslösungen nach [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zu verschieben, befolgen Sie die Anleitung in diesem Artikel. 

## <a name="introduction"></a>Einführung

BizTalk Services besteht aus zwei untergeordneten Diensten:

* Microsoft BizTalk Services Hybrid Connections
* Auf EAI- und EDI-Bridge basierende Integration

[Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) ersetzt BizTalk Services Hybrid Connections. Azure Hybrid Connections ist mit Azure App Service im Azure-Portal verfügbar. Dieser Dienst bietet einen Azure-Hybridverbindungs-Manager zum Verwalten vorhandener BizTalk Services-Hybridverbindungen und auch neuer Hybridverbindungen, die Sie im Portal erstellen. 

[Logic Apps](../logic-apps/logic-apps-overview.md) ersetzt EAI- und EDI-bridgebasierte Integration mit den gleichen Funktionen in BizTalk Services und mehr. Dieser Dienst bietet Features für verbrauchsbasierte Workflows auf Cloudebene und die Orchestrierung, mit denen Sie schnell und einfach komplexe Integrationslösungen mithilfe eines Browsers oder Visual Studio erstellen können.

Diese Tabelle ordnet BizTalk Services-Funktionen Logic Apps zu.

| BizTalk Services   | Logic Apps            | Zweck                      |
| ------------------ | --------------------- | ---------------------------- |
| Connector          | Connector             | Senden und Empfangen von Daten   |
| Bridge             | Logik-App             | Pipelineprozessor           |
| Überprüfungsphase     | XML-Überprüfungsaktion | Überprüfen eines XML-Dokuments anhand eines Schemas | 
| Erweiterungsphase       | Datentoken           | Hochstufen von Eigenschaften in Nachrichten oder für Routingentscheidungen |
| Transformationsphase    | Transformationsaktion      | Konvertieren von XML-Nachrichten von einem Format in ein anderes |
| Decodierungsphase       | Flatfile-Decodierungsaktion | Konvertieren von Flatfile-Dateien nach XML |
| Codierungsphase       | Flatfile-Codierungsaktion | Konvertieren von XML nach Flatfile |
| Nachrichteninspektor  | Azure Functions oder API-Apps | Ausführen von benutzerdefiniertem Code in Ihren Integrationen |
| Routingaktion       | Bedingung oder Switch | Weiterleiten von Nachrichten an einen der angegebenen Connectors |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services-Artefakte

BizTalk Services verfügt über verschiedene Arten von Artefakten. 

## <a name="connectors"></a>Connectors

BizTalk Services-Connectors erlauben das Senden und Empfangen von Daten über Bridges. Dazu gehören auch bidirektionale Bridges, die HTTP-basierte Anforderungs-/Antwort-Interaktionen ermöglichen. Logic Apps verwendet dieselbe Terminologie und besitzt Hunderte Connectors, die durch Herstellen einer Verbindung mit einer Vielzahl von Technologien und Diensten demselben Zweck dienen. Connectors sind z.B. für SaaS-und PaaS-Clouddienste wie OneDrive, Office 365, Dynamics CRM und viele mehr verfügbar, plus lokale Systeme über das On-Premises Data Gateway, das den BizTalk Adapter Service für BizTalk Services ersetzt. Die Quellen in BizTalk Services sind auf FTP, SFTP und das Abonnement für Service Bus-Warteschlangen oder -Themen beschränkt.

![Diagramm, das den BizTalk Services-Fluss zeigt.](media/logic-apps-move-from-mabs/sources.png)

Jede Bridge verfügt standardmäßig über einen HTTP-Endpunkt, der mit der Laufzeitadresse und den relativen Adresseigenschaften der Bridge konfiguriert ist. Um dieselben Ergebnisse in Logic Apps zu erreichen, verwenden Sie die Aktionen [Anforderung und Antwort](../connectors/connectors-native-reqres.md).

## <a name="xml-processing-and-bridges"></a>XML-Verarbeitung und Bridges

In BizTalk Services entspricht eine Bridge einer Verarbeitungspipeline. Eine Bridge kann die von einem Connector empfangenen Daten verarbeiten und dann die Ergebnisse an ein anderes System senden. Logic Apps ermöglicht dasselbe durch die Unterstützung derselben pipelinebasierten Interaktionsmuster wie BizTalk Services. Darüber hinaus werden noch andere Integrationsmuster bereitgestellt. Die [XML-Anforderungs-/Antwort-Bridge](/previous-versions/azure/hh689781(v=azure.100)) in BizTalk Services wird als VETER-Pipeline bezeichnet und umfasst Phasen, die folgende Aufgaben durchführen:

* Überprüfen (Validate – V)
* Erweitern (E)
* Transformieren (T)
* Erweitern (E)
* Routing (R)

Diese Abbildung zeigt, wie die Verarbeitung zwischen Anforderung und Antwort aufgeteilt ist. Dies ermöglicht die getrennte Steuerung der Anforderungs- und Antwortpfade (z.B. mit unterschiedlichen Zuordnungen für jeden Pfad):

![Screenshot, der zeigt, wie die Verarbeitung zwischen Anforderung und Antwort aufgeteilt wird.](media/logic-apps-move-from-mabs/xml-request-reply.png)

Darüber hinaus fügt eine unidirektionale XML-Bridge Decodier- und Codierphasen am Anfang und Ende der Verarbeitung hinzu. Die Passthrough-Bridge enthält eine einzelne Erweiterungsphase.

### <a name="message-processing-decoding-and-encoding"></a>Nachrichtenverarbeitung, Codierung und Decodierung

In BizTalk Services empfangen Sie XML-Nachrichten verschiedener Typen und bestimmen das passende Schema für die empfangene Nachricht. Dies erfolgt in der Phase *Nachrichtentypen* der Empfangsverarbeitungs-Pipeline. Anschließend verwendet die Decodierungsphase den ermittelten Nachrichtentyp, um die Nachricht mithilfe des bereitgestellten Schemas zu decodieren. Wenn das Schema ein Flatfile-Schema ist, konvertiert diese Phase die eingehende Flatfile nach XML. 

Logic Apps bietet ähnliche Funktionen. Sie erhalten eine Flatfile über verschiedene Protokolle mit unterschiedlichen Connectortriggern (Dateisystem, FTP, HTTP usw.) und verwenden die [Flatfile-Decodierungsaktion](../logic-apps/logic-apps-enterprise-integration-flatfile.md), um die eingehenden Daten nach XML zu konvertieren. Sie können Ihre vorhandenen Flatfile-Schemas ohne Änderungen direkt nach Logic Apps verschieben und dann Schemas in Ihr Integrationskonto hochladen.

### <a name="validation"></a>Überprüfen

Nachdem die eingehenden Daten nach XML konvertiert wurden (oder wenn XML das Eingangsnachrichtenformat war), wird eine Überprüfung durchgeführt, um festzustellen, ob die Nachricht dem XSD-Schema entspricht. Verwenden Sie hierzu in Logic Apps die Aktion [XML-Überprüfung](../logic-apps/logic-apps-enterprise-integration-xml-validation.md). Sie können dieselben Schemas wie in BizTalk Services ohne Änderungen anwenden.

### <a name="transform-messages"></a>Transformieren von Nachrichten

In BizTalk Services werden während der Transformationsphase XML-basierte Nachrichtenformate in andere konvertiert. Dies erfolgt durch Anwendung einer Zuordnung mithilfe der TRFM-basierten Zuordnung. In Logic Apps ist der Prozess ähnlich. Die Transformationsaktion führt eine Zuordnung aus Ihrem Integrationskonto durch. Der Hauptunterschied besteht darin, dass die Zuordnungen in Logic Apps im XSLT-Format vorliegen. XSLT bietet die Möglichkeit, vorhandenen XSLT-Code wiederzuverwenden. Dazu gehören auch die Zuordnungen, die für BizTalk Server erstellt wurden und Funktoide enthalten. 

### <a name="routing-rules"></a>Routingregeln

BizTalk Services trifft Routingentscheidungen anhand des Endpunkts bzw. Connectors, an den eingehende Nachrichten bzw. Daten gesendet werden sollen. Die Auswahl eines Endpunkts aus vorkonfigurierten Endpunkten ist mit der Filteroption für das Routing möglich:

![Screenshot, der die Filteroption für das Routing zeigt.](media/logic-apps-move-from-mabs/route-filter.png)

Sofern nur zwei Optionen zur Verfügung stehen, erfolgt das Konvertieren von Routingfiltern in BizTalk Services am besten mithilfe einer *Bedingung*. Wenn es mehr als zwei sind, verwenden Sie einen **Switch**.

Logic Apps bietet eine komplexere Logik und erweiterte Möglichkeiten für Ablaufsteuerung und Routing per [Bedingungs-](../logic-apps/logic-apps-control-flow-conditional-statement.md) und [Switch-Anweisungen](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Erweitern

Bei der Verarbeitung in BizTalk Services werden dem Nachrichtenkontext, der den empfangenen Daten zugeordnet ist, von der Erweiterungsphase Eigenschaften hinzugefügt. Dazu gehört z.B. das Höherstufen einer Eigenschaft, die für das Routing verwendet wird, aus einer Datenbanksuche oder das Extrahieren eines Werts unter Verwendung eines XPath-Ausdrucks. Logic Apps ermöglicht den Zugriff auf alle Ausgaben von kontextbezogenen Daten aus vorhergehenden Aktionen, sodass dasselbe Verhalten einfach repliziert werden kann. Wenn Sie z.B. die SQL-Verbindungsaktion `Get Row` verwenden, geben Sie Daten aus einer SQL Server-Datenbank zurück und verwenden diese Daten in einer Entscheidungsaktion für das Routing. Auf dieselbe Weise sind Eigenschaften von eingehenden Service Bus-Nachrichten in der Warteschlange über einen Trigger sowie per XPath mit dem Ausdruck aus der XPath-Workflow Definition Language adressierbar.

### <a name="run-custom-code"></a>Ausführen von benutzerdefiniertem Code

Mit BizTalk Services können Sie [benutzerdefinierten Code ausführen](/previous-versions/azure/dn232389(v=azure.100)), der in Ihre eigenen Assemblys hochgeladen wurde. Diese Funktionalität wird über die [IMessageInspector]()-Schnittstelle implementiert. Jede Phase in der Bridge enthält zwei Eigenschaften („On Enter Inspector“ und „On Exit Inspector“), die den .NET-Typ bereitstellen, den Sie erstellt haben und der diese Schnittstelle implementiert. Benutzerdefinierter Code ermöglicht ihnen die Ausführung komplexerer Verarbeitungsaufgaben mit den Daten sowie das Wiederverwenden vorhandenen Codes in Assemblys, mit denen allgemeine Geschäftslogik ausgeführt wird. 

Logic Apps bietet zwei bevorzugte Möglichkeiten für das Ausführen von benutzerdefiniertem Code: Azure Functions und API-Apps. Mit Azure Functions können Funktionen erstellt und von Logic Apps aus aufgerufen werden. Weitere Informationen finden Sie unter [Hinzufügen und Ausführen von benutzerdefiniertem Code für Logik-Apps über Azure Functions](../logic-apps/logic-apps-azure-functions.md). Verwenden Sie API-Apps (eine Komponente von Azure App Service), um eigene Trigger und Aktionen zu erstellen. Weitere Informationen finden Sie unter [Erstellen einer benutzerdefinierten API zur Verwendung mit Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Wenn Sie über benutzerdefinierten Code in Assemblys verfügen, den Sie aus BizTalk Services aufrufen, können Sie diesen Code entweder nach Azure Functions verschieben oder benutzerdefinierte APIs mit API-Apps erstellen. Die Vorgehensweise hängt von Ihrer Implementierung ab. Wenn Sie z.B. Code verwenden, der einen anderen Dienst umschließt, für den Logic Apps über keinen Connector verfügt, erstellen Sie eine API-App und verwenden die Aktionen, die die API-App in Ihrer Logik-App bereitstellt. Wenn Sie über Hilfsfunktionen oder Bibliotheken verfügen, ist Azure Functions wahrscheinlich am besten geeignet.

### <a name="edi-processing-and-trading-partner-management"></a>EDI-Verarbeitung und Handelspartnerverwaltung

BizTalk Services und Logic Apps bieten EDI- und B2B-Verarbeitung mit Unterstützung für AS2 (Applicability Statement 2), X12 und EDIFACT. In BizTalk Services erstellen Sie EDI-Bridges und erstellen bzw. verwalten Handelspartner und Vereinbarungen im dedizierten Nachverfolgungs- und Verwaltungsportal.
In Logic Apps erhalten Sie diese Funktion durch das [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). Das EIP umfasst das [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) und B2B-Aktionen für die EDI- und B2B-Verarbeitung. Das Integrationskonto dient auch dem Erstellen und Verwalten von [Handelspartnern](../logic-apps/logic-apps-enterprise-integration-partners.md) und [Vereinbarungen](../logic-apps/logic-apps-enterprise-integration-agreements.md). Nachdem Sie ein Integrationskonto erstellt haben, können Sie mit dem Konto Logik-Apps verknüpfen. Dann können Sie die B2B-Aktionen für den Zugriff auf Handelspartnerinformationen von Ihrer Logik-App aus verwenden. Die folgenden Aktionen werden bereitgestellt:

* AS2-Codierung
* AS2-Decodierung
* X12-Codierung
* X12-Decodierung
* Codieren von EDIFACT-Nachrichten
* Decodieren von EDIFACT-Nachrichten

Diese Aktionen werden im Gegensatz zu BizTalk Services von den Transportprotokollen entkoppelt. Wenn Sie Ihre Logik-Apps erstellen, haben Sie daher mehr Flexibilität bei der Connectorauswahl für das Senden und Empfangen von Daten. Sie können z.B. X12-Dateien als Anlagen von E-Mail-Nachrichten empfangen und diese Dateien dann in einer Logik-App weiterzuverarbeiten. 

## <a name="manage-and-monitor"></a>Verwalten und Überwachen

In BizTalk Services bot ein dediziertes Portal Nachverfolgungsfunktionen für die Überwachung und Problembehandlung an. Logic Apps bietet umfangreichere Nachverfolgungs- und Überwachungsfunktionen [zum Überwachen von Logik-Apps im Azure-Portal](../logic-apps/monitor-logic-apps.md). Dies umfasst auch eine mobile App für die Überwachung unterwegs.

## <a name="high-availability"></a>Hochverfügbarkeit

Für Hochverfügbarkeit (High Availability, HA) in BizTalk Services können Sie die Verarbeitungslast aufteilen, indem Sie mehrere Instanzen in einer bestimmten Region verwenden. Logic Apps bietet ohne zusätzliche Kosten HA in der Region. 

In BizTalk Services ist für die regionsübergreifende Notfallwiederherstellung für die B2B-Verarbeitung ein Prozess für die Sicherung und Wiederherstellung erforderlich. Zur Geschäftskontinuität wird in Logic Apps eine regionsübergreifende, aktive/passive [Notfallwiederherstellungsfunktion](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) bereitgestellt, die die Synchronisierung von B2B-Daten über Integrationskonten hinweg in unterschiedlichen Regionen erlaubt.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Erstellen Ihres ersten Logik-App-Workflows zur Automatisierung von Prozessen zwischen Cloud-Apps und Cloud-Diensten](../logic-apps/quickstart-create-first-logic-app-workflow.md) oder [Konfigurieren eines Workflows mithilfe einer vorgefertigten Vorlage oder eines Musters für den schnellen Einstieg](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Anzeigen aller verfügbaren Connectors](../connectors/apis-list.md), die Sie in Logik-Apps verwenden können