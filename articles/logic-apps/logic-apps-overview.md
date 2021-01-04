---
title: Übersicht über Azure Logic Apps
description: Azure Logic Apps ist eine Cloudlösung für die Erstellung und Orchestrierung automatisierter Workflows, die Apps, Daten, Dienste und Systeme mit minimalem Code für Szenarien auf Unternehmensniveau integrieren.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 03/11/2020
ms.openlocfilehash: 4c4e8c8f809452286fee493e20dbef2cc538c8bd
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921539"
---
# <a name="what-is-azure-logic-apps"></a>Was ist Azure Logic Apps?

[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps) ist ein Clouddienst, der Sie beim Planen, Automatisieren und Orchestrieren von Aufgaben, Geschäftsprozessen und [Workflows](#logic-app-concepts) unterstützt, wenn Sie Apps, Daten, Systeme und Dienste unternehmens- und organisationsübergreifend integrieren müssen. Logic Apps vereinfacht das Entwerfen und Erstellen von skalierbaren Lösungen für die App-[Integration](https://azure.microsoft.com/product-categories/integration/), Datenintegration, Systemintegration, Enterprise Application Integration (EAI) und B2B-Kommunikation (Business-to-Business) – in der Cloud, lokal oder an beiden Orten.

Hier sind beispielsweise einige Workloads angegeben, die Sie mit Logik-Apps automatisieren können:

* Verarbeiten und Weiterleiten von Bestellungen für lokale Systeme und Clouddienste

* Senden von E-Mail-Benachrichtigungen mit Office 365, wenn Ereignisse in verschiedenen Systemen, Apps und Diensten eintreten

* Verschieben von hochgeladenen Dateien von einem SFTP- oder FTP-Server in Azure Storage

* Überwachen von Tweets für ein bestimmtes Thema, Analysieren der Stimmung und Erstellen von Warnungen oder Aufgaben für zu prüfende Elemente

Zum Erstellen von Lösungen für die Unternehmensintegration mit Azure Logic Apps können Sie aus einem ständig wachsenden Katalog mit [Hunderten einsatzbereiter Connectors](../connectors/apis-list.md) wählen. Diese umfassen Dienste wie Azure Service Bus, Azure Functions, Azure Storage, SQL Server, Office 365, Dynamics, Salesforce, BizTalk, SAP, Oracle DB, Dateifreigaben und mehr. [Connectors](#logic-app-concepts) stellen [Trigger](#logic-app-concepts), [Aktionen](#logic-app-concepts) oder beides zum Erstellen von Logik-Apps bereit, die auf sichere Weise auf Daten zugreifen und diese in Echtzeit verarbeiten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

## <a name="how-do-logic-apps-work"></a>Wie funktionieren Logik-Apps? 

Jeder Logik-App-Workflow beginnt mit einem Trigger, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder neue verfügbare Daten bestimmte Kriterien erfüllen. Viele von den Connectors in Logic Apps bereitgestellten Trigger enthalten grundlegende Planungsfunktionen, damit Sie festlegen können, wie regelmäßig Ihre Workloads ausgeführt werden. Für eine komplexere Zeitplanung oder fortgeschrittene Serien können Sie einen Recurrence-Trigger als ersten Schritt in einem Workflow verwenden. Informieren Sie sich über [zeitplanbasierte Workflows](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Bei jeder Auslösung des Triggers erstellt das Logic Apps-Modul eine Logik-App-Instanz, mit der die Aktionen im Workflow ausgeführt werden. Diese Aktionen können auch Datenkonvertierungen und Workflowsteuerungen umfassen, z. B. bedingte Anweisungen, switch-Anweisungen, Schleifen und Verzweigungen. Diese Logik-App beginnt beispielsweise mit einem Dynamics 365-Trigger mit dem integrierten Kriterium „When a record is updated“ (Wenn ein Datensatz aktualisiert wird). Wenn der Trigger ein Ereignis erkennt, das mit diesem Kriterium übereinstimmt, wird der Trigger ausgelöst und führt die Aktionen des Workflows aus. Hier umfassen diese Aktionen eine XML-Transformation, Datenaktualisierungen, Entscheidungsverzweigungen und E-Mail-Benachrichtigungen.

![Designer für Logik-Apps – Beispiel für Logik-App](./media/logic-apps-overview/azure-logic-apps-designer.png)

Sie können Ihre Logik-Apps visuell mit dem Designer für Logik-Apps erstellen, der im Azure-Portal über Ihren Browser und in Visual Studio verfügbar ist. Für stärker benutzerdefinierte Logik-Apps können Sie Logik-App-Definitionen im JSON-Format (JavaScript Object Notation) erstellen bzw. bearbeiten, indem Sie den Editor „Codeansicht“ nutzen. Sie können auch Azure PowerShell-Befehle und Azure Resource Manager-Vorlagen für bestimmte Aufgaben verwenden. Logik-Apps werden in der Cloud unter Azure bereitgestellt und ausgeführt. Sehen Sie sich dieses Video an, um eine detailliertere Einführung zu erhalten: [Use Azure Enterprise Integration Services to run cloud apps at scale](https://channel9.msdn.com/Events/Connect/2017/T119/) (Verwenden von Azure Enterprise Integration Services zum bedarfsorientierten Ausführen von Cloud-Apps)

## <a name="why-use-logic-apps"></a>Gründe für die Verwendung von Logik-Apps

Unternehmen setzen immer stärker auf die Digitalisierung, und mithilfe von Logik-Apps können Sie sowohl Legacysysteme als auch hochmoderne Systeme einfacher und schneller verbinden, indem Sie vordefinierte APIs als von Microsoft verwaltete Connectors angeben. So können Sie sich auf die Geschäftslogik und Funktionalität Ihrer Apps konzentrieren. Sie müssen sich nicht mit dem Erstellen, Hosten, Skalieren, Verwalten, Pflegen und Überwachen Ihrer Apps beschäftigen. Diese Aufgaben übernimmt Logic Apps für Sie. Außerdem zahlen Sie beim nutzungsabhängigen [Preismodell](../logic-apps/logic-apps-pricing.md) nur für die tatsächlich verwendeten Ressourcen.

In vielen Fällen ist es nicht erforderlich, Code zu schreiben. Falls Sie Code schreiben müssen, können Sie Codeausschnitte mit [Azure Functions](../azure-functions/functions-overview.md) erstellen und diesen Code bedarfsabhängig über Logik-Apps ausführen. Wenn Ihre Logik-Apps mit Ereignissen von Azure-Diensten, benutzerdefinierten Apps oder anderen Lösungen interagieren müssen, können Sie [Azure Event Grid](../event-grid/overview.md) mit Ihren Logik-Apps für die Überwachung, das Routing und die Veröffentlichung nutzen.

Logic Apps, Functions und Event Grid werden von Microsoft Azure vollständig verwaltet, sodass Sie sich keine Gedanken über das Erstellen, Hosten, Skalieren, Verwalten, Überwachen und Pflegen Ihrer Lösungen machen müssen. Dank der Funktion zum Erstellen von [„serverlosen“ Apps und Lösungen](../logic-apps/logic-apps-serverless-overview.md) können Sie sich auf die Geschäftslogik konzentrieren. Für diese Dienste wird eine automatische Skalierung durchgeführt, um Ihre Anforderungen zu erfüllen, Integrationen zu beschleunigen und Sie beim Erstellen von stabilen Cloud-Apps mit minimalem Codeaufwand zu unterstützen.

Lesen Sie diese [Kundenberichte](https://aka.ms/logic-apps-customer-stories), um zu erfahren, wie Unternehmen ihre Flexibilität verbessert haben und sich stärker auf ihr Kerngeschäft konzentrieren können, indem sie Logic Apps mit anderen Azure-Diensten und Microsoft-Produkten kombiniert haben.

Hier sind weitere Details zu den Funktionen und Vorteilen von Logic Apps angegeben:

### <a name="visually-build-workflows-with-easy-to-use-tools"></a>Visuelles Erstellen von Workflows mit benutzerfreundlichen Tools

Sparen Sie Zeit, und vereinfachen Sie komplexe Prozesse, indem Sie Tools für das visuelle Entwerfen verwenden. Erstellen Sie Logik-Apps von Anfang bis Ende, indem Sie den Designer für Logik-Apps über Ihren Browser im Azure-Portal oder in Visual Studio verwenden. Starten Sie Ihren Workflow mit einem Trigger, und fügen Sie eine beliebige Anzahl von Aktionen über den [Katalog mit Connectors](../connectors/apis-list.md) hinzu.

### <a name="get-started-faster-with-logic-app-templates"></a>Schnelleres Einsteigen mit Logik-App-Vorlagen

Erstellen Sie häufig genutzte Lösungen in kürzerer Zeit, indem Sie vordefinierte Workflows aus dem [Vorlagenkatalog](../logic-apps/logic-apps-create-logic-apps-from-templates.md) auswählen. Die Arten von Vorlagen reichen von einfacher Konnektivität für SaaS-Apps (Software-as-a-Service) bis zu erweiterten B2B-Lösungen und „Just-for-fun“-Vorlagen. Informieren Sie sich über das [Erstellen von Logik-App-Workflows aus vorgefertigten Vorlagen](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

### <a name="connect-disparate-systems-across-different-environments"></a>Übergreifendes Verbinden von verschiedenartigen Systemen für mehrere Umgebungen

Einige Muster und Workflows sind leicht zu beschreiben, können im Code aber nur schwer implementiert werden. Mit Logic-Apps können Sie verschiedenartige Systeme übergreifend für lokale Umgebungen und Cloudumgebungen nahtlos verbinden. Beispielsweise können Sie eine Cloudmarketinglösung mit einem lokalen Abrechnungssystem verbinden oder das Messaging für APIs und Systeme per Enterprise Service Bus zentralisieren. Logic-Apps stellen eine schnelle, zuverlässige und einheitliche Möglichkeit zum Bereitstellen von wiederverwendbaren und rekonfigurierbaren Lösungen für diese Szenarien dar.

### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>Erstklassige Unterstützung für Unternehmensintegration und B2B-Szenarien

Unternehmen und Organisationen kommunizieren auf elektronischem Wege über Nachrichtenprotokolle und -formate miteinander, die zwar alle dem Branchenstandard entsprechen, aber unterschiedlicher Art sein können, z.B. EDIFACT, AS2 und X12. Mit den Features im [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md) können Sie Logik-Apps erstellen, mit denen von Ihren Partnern verwendete Nachrichtenformate in Formate transformiert werden, die von den Systemen Ihrer Organisation interpretiert und verarbeitet werden können. Mit Logic Apps wird dieser Nachrichtenaustausch reibungslos und außerdem sicher durchgeführt, indem Verschlüsselungen und digitale Signaturen verwendet werden.

Beginnen Sie klein mit Ihren aktuellen Systemen und Diensten, und wachsen Sie Schritt für Schritt so schnell, wie es für Sie machbar ist. Wenn Sie bereit sind, erhalten Sie über Logic Apps und das EIP Unterstützung beim Implementieren und Hochskalieren auf anspruchsvollere Integrationsszenarien, indem diese und weitere Funktionen genutzt werden:

* Nutzen Sie die folgenden Produkte und Dienste:

  * [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server)
  * [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)
  * [Azure-Funktionen](../azure-functions/functions-overview.md)
  * [Azure API Management](../api-management/api-management-key-concepts.md)

* Verarbeiten von [XML-Nachrichten](../logic-apps/logic-apps-enterprise-integration-xml.md)

* Verarbeiten von [Flatfiles](../logic-apps/logic-apps-enterprise-integration-flatfile.md)

* Austauschen von Nachrichten mit den Protokollen [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md), [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md) und [X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Speichern und verwalten Sie diese und weitere B2B-Artefakte zentral mithilfe von [Integrationskonten](./logic-apps-enterprise-integration-create-integration-account.md):

  * [Partner](../logic-apps/logic-apps-enterprise-integration-partners.md)
  * [Vereinbarungen](../logic-apps/logic-apps-enterprise-integration-agreements.md) 
  * [Zuordnungen für die XML-Datentransformation](../logic-apps/logic-apps-enterprise-integration-maps.md)
  * [Schemas für die XML-Überprüfung](../logic-apps/logic-apps-enterprise-integration-schemas.md)

Wenn Sie beispielsweise Microsoft BizTalk Server nutzen, können Logik-Apps über den [BizTalk Server-Connector](../connectors/apis-list.md#on-premises-connectors) mit Ihrer BizTalk Server-Instanz kommunizieren. Sie können dann BizTalk-ähnliche Vorgänge in Ihren Logik-Apps erweitern bzw. durchführen, indem Sie [Connectors für Integrationskonten](../connectors/apis-list.md#integration-account-connectors) einfügen, die über das Enterprise Integration Pack verfügbar sind.

In der anderen Richtung kann BizTalk Server eine Verbindung mit Logik-Apps herstellen und damit kommunizieren, indem der [Microsoft BizTalk Server Adapter für Logic Apps](https://www.microsoft.com/download/details.aspx?id=54287) verwendet wird. Informieren Sie sich darüber, wie Sie für Ihre BizTalk Server-Instanz den [BizTalk Server Adapter einrichten und nutzen](/biztalk/core/logic-app-adapter).

### <a name="write-once-reuse-often"></a>Einmal schreiben, häufig wiederverwenden

Erstellen Sie Ihre Logik-Apps als Azure Resource Manager-Vorlagen, sodass Sie die [Bereitstellung von Logik-Apps](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) über mehrere Umgebungen und Regionen hinweg automatisieren können.

### <a name="access-resources-inside-azure-virtual-networks"></a>Zugreifen auf Ressourcen in virtuellen Azure-Netzwerken

Logik-Apps können auf geschützte Ressourcen zugreifen, z. B. virtuelle Computer (VMs) und andere Systeme oder Dienste, die sich in einem [virtuellen Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) befinden, wenn Sie eine [*Integrationsdienstumgebung* (Integration Service Environment, ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erstellen. Eine ISE ist eine dedizierte Instanz des Logic Apps-Diensts, die dedizierte Ressourcen verwendet und getrennt vom „globalen“ mehrinstanzenfähigen Logic Apps-Dienst ausgeführt wird.

Das Ausführen von Logik-Apps als eigene separate dedizierte Instanz trägt dazu bei, mögliche Auswirkungen anderer Azure-Mandanten auf die Leistung Ihrer Apps zu verringern. Dies ist auch als [„Noisy Neighbors“-Problem](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) bekannt. Eine ISE bietet darüber hinaus die folgenden Vorteile:

* Ihre eigenen statischen IP-Adressen. Diese sind gesondert von den statischen IP-Adressen, die von den Logik-Apps im mehrinstanzenfähigen Dienst gemeinsam verwendet werden. Sie können auch eine einzelne öffentliche, statische und vorhersagbare ausgehende IP-Adresse für die Kommunikation mit Zielsystemen einrichten. Auf diese Weise müssen Sie nicht für jede ISE an den Zielsystemen zusätzliche Firewallzugänge einrichten.

* Höhere Grenzwerte für Ausführungsdauer, Speicheraufbewahrung, Durchsatz, Zeitlimits für HTTP-Anforderungen und -Antworten, Nachrichtengröße und benutzerdefinierte Connectoranforderungen. Weitere Informationen finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

Wenn Sie eine ISE erstellen, *injiziert* Azure diese ISE bzw. stellt sie in Ihrem virtuellen Azure-Netzwerk bereit. Anschließend können Sie diese ISE als Speicherort für Logik-Apps und Integrationskonten verwenden, die Zugriff benötigen. Weitere Informationen zum Erstellen einer ISE finden Sie unter [Verbinden mit Azure Virtual Networks über Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

### <a name="built-in-extensibility"></a>Integrierte Erweiterbarkeit

Wenn Sie den gewünschten Connector zum Ausführen von benutzerdefiniertem Code nicht finden, können Sie Logik-Apps erweitern, indem Sie Ihre eigenen Codeausschnitte bedarfsabhängig mit [Azure Functions](../azure-functions/functions-overview.md) erstellen und aufrufen. Erstellen Sie Ihre eigenen [APIs](../logic-apps/logic-apps-create-api-app.md) und [benutzerdefinierten Connectors](../logic-apps/custom-connector-overview.md), die Sie aus Logik-Apps aufrufen können.

### <a name="pay-only-for-what-you-use"></a>Zahlen nur für wirklich genutzte Ressourcen
  
Für Logic Apps werden nutzungsabhängige [Preise und Verbrauchseinheiten](../logic-apps/logic-apps-pricing.md) verwendet, sofern Sie nicht über Logik-Apps verfügen, die zuvor mit App Service-Plänen erstellt wurden.

Informieren Sie sich in diesen Einführungsvideos eingehender über Logic Apps:

* [Integration with Logic Apps – Go from zero to hero](https://channel9.msdn.com/Events/Build/2017/C9R17) (Integration in Logic Apps – Von „Zero“ zu „Hero“)
* [Enterprise integration with Microsoft Azure Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2188) (Unternehmensintegration mit Microsoft Azure Logic Apps)
* [Building advanced business processes with Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3179) (Erstellen von erweiterten Geschäftsprozessen mit Logic Apps)

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>Wie unterscheidet sich Logic Apps von Functions, WebJobs und Power Automate?

All diese Dienste dienen Ihnen als Hilfe beim Verknüpfen bzw. Verbinden von verschiedenartigen Systemen. Jeder Dienst hat bestimmte Vorteile, sodass eine Kombination der jeweiligen Funktionen die beste Möglichkeit darstellt, schnell ein skalierbares, Integrationssystem mit vollem Funktionsumfang zu erstellen. Weitere Informationen finden Sie unter [Wählen zwischen Logic Apps, Functions, WebJobs und Power Automate](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>Schlüsselbegriffe

* **Workflow**: Sie können Geschäftsprozesse als eine Folge von Schritten visualisieren, entwerfen, erstellen, automatisieren und bereitstellen.

* **Verwaltete Connectors**: Ihre Logik-Apps benötigen Zugriff auf Daten, Dienste und Systeme. Sie können vorgefertigte von Microsoft verwaltete Connectors verwenden, die für das Verbinden, Zugreifen auf und Arbeiten mit Ihren Daten ausgelegt sind. Weitere Informationen finden Sie unter [Connectors für Azure Logic Apps](../connectors/apis-list.md).

* **Trigger**: Bei vielen von Microsoft verwalteten Connectors werden Trigger bereitgestellt, die ausgelöst werden, wenn Ereignisse oder neue Daten bestimmte Bedingungen erfüllen. Ein Ereignis kann beispielsweise der Empfang einer E-Mail oder die Erkennung von Änderungen in Ihrem Azure Storage-Konto sein. Bei jeder Auslösung des Triggers erstellt das Logic Apps-Modul eine neue Logik-App-Instanz zur Ausführung des Workflows.

* **Aktionen**: Als Aktionen werden alle Schritte bezeichnet, die nach dem Trigger ausgeführt werden. Jede Aktion wird normalerweise einem Vorgang zugeordnet, der durch einen verwalteten Connector, eine benutzerdefiierte API oder einen benutzerdefinierten Connector definiert wird.

* **Enterprise Integration Pack**: Für anspruchsvollere Integrationsszenarien enthält Logic Apps Funktionen von BizTalk Server. Über das Enterprise Integration Pack werden Connectors bereitgestellt, die für Logik-Apps die einfache Durchführung von Überprüfungen, Transformationen usw. ermöglichen.

## <a name="get-started"></a>Erste Schritte

Logic Apps ist einer der vielen Dienste, die unter Microsoft Azure gehostet werden. Bevor Sie beginnen können, benötigen Sie also ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

Wenn Sie über ein Azure-Abonnement verfügen, können Sie [diese Schnellstartanleitung zum Erstellen Ihrer ersten Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) ausprobieren. Darin wird beschrieben, wie damit neuer Inhalt auf einer Website per RSS-Feed überwacht und beim Erscheinen von neuem Inhalt E-Mails gesendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen des Datenverkehrs mit einer zeitplanbasierten Logik-App](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
* Weitere Informationen zu [serverlosen Lösungen mit Azure](../logic-apps/logic-apps-serverless-overview.md)
* Weitere Informationen zur [B2B-Integration mit dem Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
