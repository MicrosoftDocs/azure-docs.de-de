---
title: Übersicht über AMQP 1.0 in Azure Service Bus
description: Erfahren Sie, wie Azure Service Bus das offene Standardprotokoll AMQP (Advance Message Queueing Protocol) unterstützt.
ms.topic: article
ms.date: 11/20/2020
ms.openlocfilehash: e585fcc905d1651d49008b623b01a6c2f8a04fcc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498777"
---
# <a name="amqp-10-support-in-service-bus"></a>Unterstützung für AMQP 1.0 in Service Bus
Der Azure Service Bus-Clouddienst verwendet das [Advanced Message Queueing Protocol (AMQP) 1.0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) als Hauptkommunikationsmittel. In den letzten zehn Jahren hat Microsoft mit Partnern aus der Branche, sowohl Kunden als auch Lieferanten konkurrierender Nachrichtenbroker, AMQP entwickelt, wobei neue Erweiterungen im [OASIS AMQP Technical Committee](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) entwickelt wurden. AMQP 1.0 ist ein ISO- und IEC-Standard ([ISO 19464:20149](https://www.iso.org/standard/64955.html)). 

AMQP gibt Ihnen die Möglichkeit, plattformübergreifende Hybridanwendungen mit einem anbieter- und implementierungsneutralen, offenen Standardprotokoll zu erstellen. Sie können Anwendungen erstellen, deren Komponenten unter Verwendung unterschiedlicher Sprachen und Frameworks erstellt wurden und unter unterschiedlichen Betriebssystemen ausgeführt werden. Alle diese Komponenten können über Service Bus eine Verbindung herstellen und strukturierte Geschäftsnachrichten effizient und sicher nahtlos austauschen.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Einführung: Was ist AMQP 1.0 und warum ist es wichtig?
Traditionell nutzen nachrichtenorientierte Middlewareprodukte proprietäre Protokolle für die Kommunikation zwischen Clientanwendungen und Brokern. Dies bedeutet, dass Sie nach der Entscheidung für den Messagingbroker eines bestimmten Anbieters auch dessen Bibliotheken zur Anbindung der Clientanwendungen an den Broker verwenden müssen. Dies bringt eine gewisse Anbieterabhängigkeit mit sich, da eine Portierung einer Anwendung auf ein anderes Produkt Codeänderungen an allen verbundenen Anwendungen erforderlich machen. In der Java-Community haben sprachspezifische API-Standards wie z. B. Java Message Service (JMS) und die Abstraktionen des Spring Frameworks diese Probleme etwas verringert, doch ihr Funktionsumfang ist sehr schmal, und sie schließen Entwickler aus, die andere Sprachen verwenden.

Zudem ist es schwierig, Messagingbroker unterschiedlicher Anbieter zu kombinieren. In der Regel ist hierfür eine Überbrückung auf Anwendungsebene erforderlich, um Nachrichten von einem System in ein anderes zu übertragen und die jeweils proprietären Nachrichtenformate zu konvertieren. Dies ist eine häufig gestellte Anforderung, etwa um eine neue, vereinheitlichte Oberfläche für ältere, dezentrale Systeme bereitzustellen oder nach einem Unternehmenszusammenschluss die IT-Systeme zu integrieren. AMQP ermöglicht das direkte Herstellen von Verbindungen zwischen Brokern, z. B. die Verwendung von Routern wie [Apache Qpid Dispatch Router](https://qpid.apache.org/components/dispatch-router/index.html) oder brokernativen „Shovels“, wie z. B. die von [RabbitMQ](service-bus-integrate-with-rabbitmq.md).

Die Softwarebranche ist äußerst schnelllebig, und neue Programmiersprachen und Anwendungsframeworks werden in einem manchmal verblüffenden Tempo eingeführt. Ganz ähnlich entwickeln sich die Anforderungen von IT-Systemen im Laufe der Zeit, und Entwickler möchten die Vorteile der neuesten Plattformfeatures nutzen. Manchmal jedoch unterstützt der gewählte Messaginganbieter nicht die gewünschten Plattformen. Wenn Messagingprotokolle proprietär sind, ist es nicht möglich, dass Dritte die Bibliotheken für diese neuen Plattformen bereitstellen. Daher müssen Sie Ansätze wie z. B. Gateways oder Bridges entwickeln, damit Sie das Messagingprodukt weiterhin verwenden können.

All diese Probleme waren der Grund für die Entwicklung von AMQP (Advanced Message Queuing Protocol) 1.0. Das Projekt wurde angestoßen von JP Morgan Chase, da dieses Unternehmen wie die meisten Finanzdienstleister nachrichtenorientierte Middleware in großem Umfang nutzt. Das Ziel war ganz einfach: ein Messagingprotokoll mit offenem Standard zu entwickeln, das die Erstellung nachrichtenbasierter Anwendungen aus Komponenten ermöglicht, die auf unterschiedlichen Sprachen, Frameworks und Betriebssystemen beruhen, um so die jeweils besten Komponenten verschiedenster Hersteller einsetzen zu können.

## <a name="amqp-10-technical-features"></a>Technische Merkmale von AMQP 1.0
AMQP 1.0 ist ein effizientes, zuverlässiges Messagingprotokoll auf Wire-Ebene, mit dem Sie robuste und plattformübergreifende Messaginganwendungen erstellen können. Das Protokoll hat einen eindeutigen Zweck: die Mechanismen der sicheren, zuverlässigen und effizienten Nachrichtenübertragung zwischen zwei Parteien zu definieren. Die Nachrichten selbst werden mithilfe einer portierbaren Datendarstellung codiert, die den Austausch strukturierter Geschäftsnachrichten zwischen heterogenen Sendern und Empfängern bei voller Vertraulichkeit ermöglicht. Die folgende Zusammenfassung listet die wichtigsten Merkmale auf:

* **Effizient:** AMQP 1.0 ist ein verbindungsorientiertes Protokoll, das eine binäre Codierung für die Protokollanweisungen und die darüber übertragenen Geschäftsnachrichten nutzt. Es beinhaltetet ausgereifte Schemata zur Flusssteuerung, um die Auslastung von Netzwerk und angeschlossenen Komponenten zu maximieren. Vor diesem Hintergrund verfolgt das Protokoll das Ziel, ein Gleichgewicht zwischen Effizienz, Flexibilität und Interoperabilität zu gewährleisten.
* **Zuverlässig:** Mit dem AMQP 1.0-Protokoll können Nachrichten mit Verlässlichkeitsgarantien unterschiedlicher Niveaus gesendet werden: von „Fire-and-Forget“ bis hin zu zuverlässiger, genau einmal bestätigter Lieferung.
* **Flexibel:** AMQP 1.0 ist ein flexibles Protokoll, das zur Unterstützung unterschiedlicher Topologien genutzt werden kann. Ein und dasselbe Protokoll kann für die Kommunikation von Client zu Client, von Client zu Broker und von Broker zu Broker dienen.
* **Unabhängig vom Brokermodell:** Die AMQP 1.0-Spezifikation stellt keine Anforderungen an das von einem Broker verwendete Messagingmodell. Vorhandene Messagingbroker können also ganz einfach um die Unterstützung von AMQP 1.0 erweitert werden.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 ist ein Standard (im wahrsten Sinne des Wortes)
AMQP 1.0 ist ein internationaler Standard, der von ISO und IEC gemäß ISO/IEC 19464:2014 genehmigt wurde.

Die Entwicklung von AMQP 1.0 wird seit 2008 von einer Kerngruppe aus über 20 Unternehmen – Technologielieferanten wie auch Endnutzern – vorangetrieben. In diesem Prozess haben die Abnehmerfirmen vermittelt, welche Anforderungen im echten Geschäftsleben auftreten, und die Technologieanbieter daraus ein entsprechend ausgelegtes Protokoll entwickelt. Während der Entwicklungszeit haben die Hersteller in Workshops die Interoperabilität ihrer Implementierungen gemeinsam überprüft.

Im Oktober 2011 ging die Entwicklungsarbeit an einen technischen Ausschuss der Organization for the Advancement of Structured Information Standards (OASIS) über; der OASIS AMQP 1.0-Standard wurde im Oktober 2012 veröffentlicht. Während der Entwicklung des Standards waren die folgenden Unternehmen im technischen Ausschuss vertreten:

* **Technologieanbieter:** Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Abnehmerfirmen:** Bank of America, Credit Suisse, Deutsche Börse, Goldman Sachs, JPMorgan Chase.

Die derzeitigen Vorsitzenden des [OASIS AMQP Technical Committee] (https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) repräsentieren Red Hat und Microsoft.

Zu den am häufigsten genannten Vorteilen offener Standards gehören:

* Geringere Gefahr von Anbieterabhängigkeit
* Interoperabilität
* Breite Verfügbarkeit von Bibliotheken und Werkzeugen
* Schutz vor Obsoleszenz
* Verfügbarkeit qualifizierter Mitarbeiter
* Geringeres und überschaubares Risiko

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 und Service Bus
Durch die AMQP 1.0-Unterstützung in Azure Service Bus können Sie die Brokermessagingfunktionen für Warteschlangen und Veröffentlichungen/Abonnements von Service Bus mithilfe eines effizienten binären Protokolls auf unterschiedlichen Plattformen nutzen. Zudem können Sie Anwendungen erstellen, deren Komponenten mit einer Mischung aus Sprachen, Frameworks und Betriebssystemen erstellt wurden.

Die folgende Abbildung zeigt das Beispiel einer Bereitstellung, bei der auf Linux ausgeführte Java-Clients, die mit der Standard-Programmierschnittstelle Java Message Service (JMS) geschrieben wurden, und auf Windows ausgeführte .NET-Clients unter Verwendung von AMQP 1.0 Nachrichten über Service Bus austauschen.

![Abbildung, die eine Service Bus-Instanz zeigt, die Nachrichten mit zwei Linux-Umgebungen und zwei Windows-Umgebungen austauscht.][0]

**Abbildung 1: Beispielszenario für eine Bereitstellung mit plattformübergreifender Nachrichtenübermittlung mittels Service Bus und AMQP 1.0**

Alle unterstützten Service Bus-Clientbibliotheken, die über das Azure SDK verfügbar sind, verwenden AMQP 1.0.

- [Azure Service Bus für .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true&view=azure-dotnet)
- [Azure Service Bus-Bibliotheken für Java](/java/api/overview/azure/servicebus?preserve-view=true&view=azure-java-stable)
- [Azure Service Bus-Anbieter für Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Azure Service Bus-Module für JavaScript und TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true&view=azure-node-latest)
- [Azure Service Bus-Bibliotheken für Python](/python/api/overview/azure/servicebus?preserve-view=true&view=azure-python)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Außerdem können Sie Service Bus von einem beliebigen AMQP 1.0-kompatiblen Protokollstapel aus verwenden:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Zusammenfassung
* AMQP 1.0 ist ein offenes, zuverlässiges Messagingprotokoll, mit dem Sie plattformübergreifende Hybridanwendungen erstellen können. AMQP 1.0 ist ein OASIS-Standard.

## <a name="next-steps"></a>Nächste Schritte
Möchten Sie mehr erfahren? Nutzen Sie die folgenden Links:

* [Verwenden von Service Bus aus .NET mit AMQP]
* [Verwenden von Service Bus aus Java mit AMQP]
* [Installieren von Apache Qpid Proton-C auf einem virtuellen Azure-Linux-Computer]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Verwenden von Service Bus aus .NET mit AMQP]: service-bus-amqp-dotnet.md
[Verwenden von Service Bus aus Java mit AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md
[Installieren von Apache Qpid Proton-C auf einem virtuellen Azure-Linux-Computer]::