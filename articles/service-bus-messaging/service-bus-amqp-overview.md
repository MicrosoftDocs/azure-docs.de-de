---
title: Übersicht über AMQP 1.0 in Azure Service Bus | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Verwendung von AMQP (Advanced Message Queuing Protocol) 1.0 in Azure.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 34829482e570354c1ab1e1fd6cec0c96b993cd83
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60403913"
---
# <a name="amqp-10-support-in-service-bus"></a>Unterstützung für AMQP 1.0 in Service Bus
Sowohl der Azure Service Bus-Clouddienst als auch der lokale [Service Bus für Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) unterstützen das Advanced Message Queueing Protocol (AMQP) 1.0. AMQP gibt Ihnen die Möglichkeit, plattformübergreifende Hybridanwendungen mit einem offenen Standard zu erstellen. Sie können Anwendungen erstellen, deren Komponenten unter Verwendung unterschiedlicher Sprachen und Frameworks erstellt wurden und unter unterschiedlichen Betriebssystemen ausgeführt werden. Alle diese Komponenten können über Service Bus eine Verbindung herstellen und strukturierte Geschäftsnachrichten effizient und sicher nahtlos austauschen.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Einführung: Was ist AMQP 1.0 und warum ist es wichtig?
Traditionell nutzen nachrichtenorientierte Middlewareprodukte proprietäre Protokolle für die Kommunikation zwischen Clientanwendungen und Brokern. Dies bedeutet, dass Sie nach der Entscheidung für den Messagingbroker eines bestimmten Anbieters auch dessen Bibliotheken zur Anbindung der Clientanwendungen an den Broker verwenden müssen. Dies bringt eine gewisse Anbieterabhängigkeit mit sich, da eine Portierung einer Anwendung auf ein anderes Produkt Codeänderungen an allen verbundenen Anwendungen erforderlich machen. 

Zudem ist es schwierig, Messagingbroker unterschiedlicher Anbieter zu kombinieren. In der Regel ist hierfür eine Überbrückung auf Anwendungsebene erforderlich, um Nachrichten von einem System in ein anderes zu übertragen und die jeweils proprietären Nachrichtenformate zu konvertieren. Dies ist eine häufig gestellte Anforderung, etwa um eine neue, vereinheitlichte Oberfläche für ältere, dezentrale Systeme bereitzustellen oder nach einem Unternehmenszusammenschluss die IT-Systeme zu integrieren.

Die Softwarebranche ist äußerst schnelllebig, und neue Programmiersprachen und Anwendungsframeworks werden in einem manchmal verblüffenden Tempo eingeführt. Ganz ähnlich entwickeln sich die Anforderungen von IT-Systemen im Laufe der Zeit, und Entwickler möchten die Vorteile der neuesten Plattformfeatures nutzen. Manchmal jedoch unterstützt der gewählte Messaginganbieter nicht die gewünschten Plattformen. Da die Messagingprotokolle proprietär sind, ist es nicht möglich, dass Dritte die Bibliotheken für diese neuen Plattformen bereitstellen. Daher müssen Sie Ansätze wie z. B. Gateways oder Bridges entwickeln, damit Sie das Messagingprodukt weiterhin verwenden können.

All diese Probleme waren der Grund für die Entwicklung von AMQP (Advanced Message Queuing Protocol) 1.0. Das Projekt wurde angestoßen von JP Morgan Chase, da dieses Unternehmen wie die meisten Finanzdienstleister nachrichtenorientierte Middleware in großem Umfang nutzt. Das Ziel war ganz einfach: ein Messagingprotokoll mit offenem Standard zu entwickeln, das die Erstellung nachrichtenbasierter Anwendungen aus Komponenten ermöglicht, die auf unterschiedlichen Sprachen, Frameworks und Betriebssystemen beruhen, um so die jeweils besten Komponenten verschiedenster Hersteller einsetzen zu können.

## <a name="amqp-10-technical-features"></a>Technische Merkmale von AMQP 1.0
AMQP 1.0 ist ein effizientes, zuverlässiges Messagingprotokoll auf Wire-Ebene, mit dem Sie robuste und plattformübergreifende Messaginganwendungen erstellen können. Das Protokoll hat einen eindeutigen Zweck: die Mechanismen der sicheren, zuverlässigen und effizienten Nachrichtenübertragung zwischen zwei Parteien zu definieren. Die Nachrichten selbst werden mithilfe einer portierbaren Datendarstellung codiert, die den Austausch strukturierter Geschäftsnachrichten zwischen heterogenen Sendern und Empfängern bei voller Vertraulichkeit ermöglicht. Die folgende Zusammenfassung listet die wichtigsten Merkmale auf:

* **Effizient**: AMQP 1.0 ist ein verbindungsorientiertes Protokoll, das eine binäre Codierung für die Protokollanweisungen und die darüber übertragenen Geschäftsnachrichten nutzt. Es beinhaltetet ausgereifte Schemata zur Flusssteuerung, um die Auslastung von Netzwerk und angeschlossenen Komponenten zu maximieren. Vor diesem Hintergrund verfolgt das Protokoll das Ziel, ein Gleichgewicht zwischen Effizienz, Flexibilität und Interoperabilität zu gewährleisten.
* **Zuverlässig**: Mit dem AMQP 1.0-Protokoll können Nachrichten mit Verlässlichkeitsgarantien unterschiedlicher Niveaus gesendet werden: von „Fire-and-Forget“ bis hin zu zuverlässiger, genau einmal bestätigter Lieferung.
* **Flexibel**: AMQP 1.0 ist ein flexibles Protokoll, das zur Unterstützung unterschiedlicher Topologien genutzt werden kann. Ein und dasselbe Protokoll kann für die Kommunikation von Client zu Client, von Client zu Broker und von Broker zu Broker dienen.
* **Unabhängig vom Brokermodell**: Die AMQP 1.0-Spezifikation stellt keine Anforderungen an das von einem Broker verwendete Messagingmodell. Vorhandene Messagingbroker können also ganz einfach um die Unterstützung von AMQP 1.0 erweitert werden.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 ist ein Standard (im wahrsten Sinne des Wortes)
AMQP 1.0 ist ein internationaler Standard, der von ISO und IEC gemäß ISO/IEC 19464:2014 genehmigt wurde.

Die Entwicklung von AMQP 1.0 wird seit 2008 von einer Kerngruppe aus über 20 Unternehmen – Technologielieferanten wie auch Endnutzern – vorangetrieben. In diesem Prozess haben die Abnehmerfirmen vermittelt, welche Anforderungen im echten Geschäftsleben auftreten, und die Technologieanbieter daraus ein entsprechend ausgelegtes Protokoll entwickelt. Während der Entwicklungszeit haben die Hersteller in Workshops die Interoperabilität ihrer Implementierungen gemeinsam überprüft.

Im Oktober 2011 ging die Entwicklungsarbeit an einen technischen Ausschuss der Organization for the Advancement of Structured Information Standards (OASIS) über; der OASIS AMQP 1.0-Standard wurde im Oktober 2012 veröffentlicht. Während der Entwicklung des Standards waren die folgenden Unternehmen im technischen Ausschuss vertreten:

* **Technologieanbieter**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Abnehmerfirmen**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Zu den am häufigsten genannten Vorteilen offener Standards gehören:

* Geringere Gefahr von Anbieterabhängigkeit
* Interoperabilität
* Breite Verfügbarkeit von Bibliotheken und Werkzeugen
* Schutz vor Obsoleszenz
* Verfügbarkeit qualifizierter Mitarbeiter
* Geringeres und überschaubares Risiko

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 und Service Bus
Durch die AMQP 1.0-Unterstützung in Azure Service Bus können Sie jetzt die Brokermessagingfunktionen für Warteschlangen und Veröffentlichungen/Abonnements von Service Bus mithilfe eines effizienten binären Protokolls auf unterschiedlichen Plattformen nutzen. Zudem können Sie Anwendungen erstellen, deren Komponenten mit einer Mischung aus Sprachen, Frameworks und Betriebssystemen erstellt wurden.

Die folgende Abbildung zeigt das Beispiel einer Bereitstellung, bei der auf Linux ausgeführte Java-Clients, die mit der Standard-Programmierschnittstelle Java Message Service (JMS) geschrieben wurden, und auf Windows ausgeführte .NET-Clients unter Verwendung von AMQP 1.0 Nachrichten über Service Bus austauschen.

![][0]

**Abbildung 1: Beispielszenario für eine Bereitstellung mit plattformübergreifender Nachrichtenübermittlung mittels Service Bus und AMQP 1.0**

Aktuell sind die folgenden Clientbibliotheken bekannt, die mit Service Bus arbeiten:

| Sprache | Bibliothek |
| --- | --- |
| Java |Apache Qpid Java Message Service (JMS)-Client<br/>IIT Software SwiftMQ Java-Client |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .NET Lite |

**Abbildung 2: Tabelle der AMQP 1.0 Clientbibliotheken**

## <a name="summary"></a>Zusammenfassung
* AMQP 1.0 ist ein offenes, zuverlässiges Messagingprotokoll, mit dem Sie plattformübergreifende Hybridanwendungen erstellen können. AMQP 1.0 ist ein OASIS-Standard.
* Unterstützung für AMQP 1.0 ist nun für Azure Service Bus und Service Bus für Windows Server (Service Bus 1.1) verfügbar. Die Preise weichen nicht von denen existierender Protokolle ab.

## <a name="next-steps"></a>Nächste Schritte
Möchten Sie mehr erfahren? Nutzen Sie die folgenden Links:

* [Verwenden von Service Bus aus .NET mit AMQP]
* [Verwenden von Service Bus aus Java mit AMQP]
* [Installieren von Apache Qpid Proton-C auf einem virtuellen Azure-Linux-Computer]
* [AMQP in Service Bus für Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Verwenden von Service Bus aus .NET mit AMQP]: service-bus-amqp-dotnet.md
[Verwenden von Service Bus aus Java mit AMQP]: service-bus-amqp-java.md
[Installieren von Apache Qpid Proton-C auf einem virtuellen Azure-Linux-Computer]: service-bus-amqp-apache.md
[AMQP in Service Bus für Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
