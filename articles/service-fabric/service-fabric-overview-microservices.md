---
title: Einführung in Microservices in Azure
description: Sie erhalten einen Überblick, warum das Erstellen von Cloudanwendungen mit einem Microservice-Ansatz für die moderne Anwendungsentwicklung wichtig ist und wie Azure Service Fabric dafür eine Plattform bereitstellt.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d20e04820c87a7390d9c20e511259ee9860c27f5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575651"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Gründe für einen Microservices-Ansatz zum Erstellen von Anwendungen

Für Softwareentwickler ist das Einbeziehen einer Anwendung in Komponententeile nichts Neues. In der Regel wird ein mehrstufiger Ansatz mit einem Back-End-Speicher, Geschäftslogik auf der mittleren Ebene und einer Front-End-Benutzeroberfläche verwendet. Was sich im Laufe der letzten Jahre geändert *hat*, ist die Tatsache, dass Entwickler verteilte Anwendungen für die Cloud erstellen.

Veränderte geschäftliche Anforderungen sind:

* Ein Dienst, der erstellt und bedarfsorientiert betrieben wird, um Kunden in neuen geografischen Regionen zu erreichen
* Schnellere Bereitstellung von Features und Funktionen, damit flexibel auf Kundenanforderungen reagiert werden kann
* Verbesserte Ressourcenauslastung zur Kostenreduzierung

Diese geschäftlichen Anforderungen beeinflussen, *wie* wir Anwendungen erstellen.

Weitere Informationen zum Azure-Ansatz in Bezug auf Microservices finden Sie unter [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: Eine cloudbasierte Anwendungsrevolution).

## <a name="monolithic-vs-microservices-design-approach"></a>Vergleich von monolithischem und Microservice-Entwurf

Anwendungen entwickeln sich im Laufe der Zeit weiter. Erfolgreiche Anwendungen entwickeln sich weiter, indem Sie für die Benutzer nützlich sind. Nicht erfolgreiche Anwendungen werden nicht weiterentwickelt und schließlich als veraltet markiert. Es stellt sich nun folgende Frage: Wie viel wissen Sie über Ihre heutigen Anforderungen und deren zukünftige Entwicklung? Angenommen, Sie erstellen eine Berichterstellungsanwendung für eine Abteilung in Ihrem Unternehmen. Sie sind sicher, dass die Anwendung nur innerhalb Ihres Unternehmens verwendet wird und die Berichte nicht lange aufbewahrt werden. In diesem Fall wählen Sie einen anderen Ansatz als beispielsweise bei der Erstellung eines Diensts zur Bereitstellung von Videoinhalten für Millionen von Kunden.

In einigen Fällen ist die Fertigstellung als Proof of Concept ausschlaggebend. Sie wissen, dass die Anwendung später umgestaltet werden kann. Es ist nicht sehr sinnvoll, zu viel Engineeringaufwand für etwas zu betreiben, das niemals verwendet wird. Wenn Unternehmen für die Cloud entwickeln, werden aber auch Wachstum und gute Auslastung erwartet. Wachstums- und Skalierungsanforderungen sind jedoch nicht vorhersagbar. Am liebsten würden wir schnell Prototypen erstellen können und gleichzeitig die Sicherheit haben, dass wir uns damit auf dem Weg zu zukünftigem Erfolg befinden. Dies entspricht dem schlanken Startup-Ansatz: Erstellen, Messen, Lernen und Wiederholen.

Während der Client/Server-Ära haben wir eher Anwendungen mit mehreren Ebenen erstellt, indem auf jeder Ebene spezielle Technologie verwendet wurde. Hierfür hat sich der Begriff *monolithische* Anwendung etabliert. Die Schnittstellen befanden sich meist zwischen den Ebenen, und innerhalb der einzelnen Ebenen wurde ein Entwurf mit engerer Kopplung zwischen den Komponenten genutzt. Die Entwickler haben also Klassen entworfen und eingebunden, die zu Bibliotheken kompiliert und über einige ausführbare und DLL-Dateien verknüpft wurden.

Dieser monolithische Entwurfsansatz hat auch Vorteile. Monolithische Anwendungen sind häufig einfacher zu entwerfen und verfügen über schnellere Aufrufe zwischen Komponenten, da diese Aufrufe oft per prozessübergreifender Kommunikation (Interprocess Communication, IPC) durchgeführt werden. Außerdem wird jeweils ein einzelnes Produkt getestet, was in Bezug auf den Personalaufwand meist effizienter ist. Der Nachteil ist, dass eine enge Kopplung zwischen mehrstufigen Ebenen entsteht und Sie einzelne Komponenten nicht skalieren können. Wenn Sie Probleme beheben oder Upgrades durchführen möchten, müssen Sie warten, bis andere Personen ihre Tests abgeschlossen haben. Agilität ist schwieriger umzusetzen.

Microservices stellen die Antwort auf diese Nachteile dar, und sie sind enger an die oben beschriebenen geschäftlichen Anforderungen angelehnt. Aber auch sie haben sowohl Vor- als auch Nachteile. Der Vorteil von Microservices ergibt sich daraus, dass jeder Microservice normalerweise einfachere Geschäftsfunktionen kapselt, die Sie einzeln auf- und abskalieren, testen, bereitstellen und verwalten können. Ein wichtiger Vorteil des Microservice-Ansatzes ist, dass Teams stärker durch Geschäftsszenarien als durch Technologie beeinflusst werden. Kleinere Teams entwickeln basierend auf einem Kundenszenario unter Verwendung beliebiger Technologien ihrer Wahl einen Microservice.

Anders ausgedrückt: Das Unternehmen muss Technologie nicht zur Verwaltung von Microserviceanwendungen standardisieren. Einzelne für Dienste zuständige Teams können die Vorgehensweise wählen, die aufgrund des Know-hows im Team sinnvoll oder am besten zur Behebung des Problems geeignet ist. In der Praxis ist eine Gruppe empfohlener Technologien vorzuziehen, z. B. ein bestimmter NoSQL-Speicher oder ein Webanwendungsframework.

Der Nachteil von Microservices sind die Verwaltung einer höheren Anzahl separater Entitäten und die höhere Komplexität von Bereitstellung und Versionsverwaltung. Der Netzwerkdatenverkehr zwischen Microservices und auch die entsprechenden Netzwerklatenzen nehmen zu. Eine große Anzahl von sehr präzisen Diensten mit vielen Einzelaufrufen kann in Bezug auf die Leistung schnell zu einem Albtraum werden. Und ohne Tools zum Anzeigen dieser Abhängigkeiten ist es nicht einfach, das gesamte System zu überblicken.

Der Microservice-Ansatz funktioniert aufgrund von Standards. Anstatt starre Verträge zu nutzen, wird angegeben, wie kommuniziert wird, und es werden nur die Dinge toleriert, die Sie von einem Dienst benötigen. Da Dienste unabhängig voneinander aktualisiert werden, ist es wichtig, diese Verträge im Voraus im Entwurf festzulegen. „Serviceorientierte Architektur (SOA) mit feiner Abstufung“ ist eine weitere Beschreibung, die für das Entwerfen mit einem Microservice-Ansatz gilt.

***Im einfachsten Fall geht es beim Microservice-Entwurfsansatz um einen entkoppelten Verbund von Diensten, an denen Änderungen unabhängig voneinander vorgenommen werden und für die bestimmte Kommunikationsstandards vereinbart sind.** _

Mit der steigenden Zahl von Cloudanwendungen haben immer mehr Entwickler festgestellt, dass diese Zerlegung der gesamten Anwendung in unabhängige Dienste für bestimmte Szenarien langfristig den besseren Ansatz darstellt.

## <a name="comparison-between-application-development-approaches"></a>Vergleich zwischen Ansätzen zur Anwendungsentwicklung

![Service Fabric-Plattform: Anwendungsentwicklung][Image1]

1) Eine monolithische Anwendung enthält domänenspezifische Funktionen und ist normalerweise in Funktionsebenen unterteilt, z. B. Web, Geschäft und Daten.

2) Sie skalieren monolithische Anwendungen, indem Sie sie über mehrere Server, VMs bzw. Container hinweg klonen.

3) Bei einer Microservice-Anwendung werden die Funktionen auf separate kleinere Dienste aufgeteilt.

4) Beim Microservice-Ansatz erfolgt das horizontale Hochskalieren, indem jeder Dienst unabhängig bereitgestellt wird und Instanzen dieser Dienste über Server, VMs bzw. Container hinweg erstellt werden.

Das Entwerfen eines Microservice-Ansatzes ist kein Patentrezept für alle Projekte. Dieser Ansatz ist jedoch enger an die oben beschriebenen geschäftlichen Ziele angelehnt. Die anfängliche Verwendung eines monolithischen Ansatzes kann sinnvoll sein, wenn Sie wissen, dass Sie später die Gelegenheit haben, den Code zu überarbeiten und in einen Microservice-Entwurf zu ändern. Normalerweise beginnen Sie mit einer monolithischen Anwendung und teilen diese dann nach und nach in Phasen auf. Sie beginnen hierbei mit den Funktionsbereichen, die besser skalierbar oder flexibler werden sollen.

Der Microservice-Ansatz bedeutet, dass sich Ihre Anwendung aus vielen kleinen Diensten zusammensetzt. Diese Dienste werden in Containern ausgeführt, die auf einem Cluster mit Computern bereitgestellt werden. Kleinere Teams entwickeln Dienste für bestimmte Szenarien. Jeder dieser Dienste wird einzeln getestet, versioniert, bereitgestellt und skaliert, um die gesamte Anwendung weiterzuentwickeln.

## <a name="what-is-a-microservice"></a>Was ist ein Microservice?

Es gibt verschiedene Definitionen von Microservices. Die meisten der folgenden Merkmale von Microservices sind jedoch allgemein anerkannt:

_ Kapseln eines Kunden- oder Geschäftsszenarios Welches Problem möchten Sie beheben?
* Durchführung der Entwicklung in einem kleinen Engineering-Team
* Verwendung einer beliebigen Programmiersprache und eines beliebigen Frameworks
* Zusammensetzung aus Code und (optional) einem Zustand, die beide unabhängig voneinander versioniert, bereitgestellt und skaliert werden
* Interaktion mit anderen Microservices über gut definierte Schnittstellen und Protokolle
* Eindeutige Namen (URLs), die zum Auflösen ihres Speicherorts verwendet werden
* Aufrechterhaltung der Konsistenz und Verfügbarkeit beim Auftreten von Fehlern

Zusammengefasst:

***Microservice-Anwendungen bestehen aus kleinen, einzeln versionierten und skalierbaren Diensten für Kunden, die über Standardprotokolle mit gut definierten Schnittstellen miteinander kommunizieren.** _

### <a name="written-in-any-programming-language-using-any-framework"></a>Verwendung einer beliebigen Programmiersprache und eines beliebigen Frameworks

Als Entwickler sollten Sie frei wählen können, welche Sprache oder welches Framework Sie verwenden möchten, und zwar je nach Ihren Fähigkeiten bzw. den Anforderungen des Diensts. Bei einigen Diensten kann es sein, dass die Leistungsvorteile von C++ überwiegen. Bei anderen steht eventuelle die vereinfachte verwaltete Entwicklung in C# oder Java im Vordergrund. In einigen Fällen müssen Sie unter Umständen eine bestimmte Partnerbibliothek, Datenspeichertechnologie oder Methode zum Verfügbarmachen des Diensts für Clients verwenden.

Nachdem Sie sich für eine Technologie entschieden haben, müssen Sie sich mit der Betriebs- oder Lebenszyklusverwaltung und der Skalierung des Diensts befassen.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Code und Zustand mit unabhängiger Versionierung, Bereitstellung und Skalierung

Unabhängig davon, wie Sie Ihre Microservices schreiben, sollten der Code und optional der Zustand getrennt voneinander bereitgestellt, aktualisiert und skaliert werden. Dies ist ein schwieriges Problem, das gelöst werden muss, da es hierbei um die Wahl der Technologie geht. Im Hinblick auf die Skalierung stellt das Verständnis der Partitionierung (Sharding) von Code und Zustand eine Herausforderung dar. Wenn für den Code und den Zustand unterschiedliche Technologien verwendet werden, was heutzutage häufiger der Fall ist, müssen die Bereitstellungsskripts für Ihren Microservice beide skalieren können. Bei dieser Trennung geht es auch um die Flexibilität, nur einige Microservices aktualisieren zu können, ohne dass alle auf einmal aktualisiert werden müssen.

Kehren wir zum Vergleich von monolithischem und Microservice-Ansatz zurück. Dieses Diagramm zeigt die Unterschiede bei den Ansätzen für die Zustandsspeicherung:

#### <a name="state-storage-for-the-two-approaches"></a>Speichern des Zustands für die beiden Ansätze

![Service Fabric-Plattform: Zustandsspeicherung][Image2]

_*_Der monolithische Ansatz auf der linken Seite enthält einen Singleton und die spezifischen Technologieebenen._*_

_*_Der Microservice-Ansatz auf der rechten Seite verfügt über einen Graphen mit verbundenen Microservices, wobei der Zustand meist auf den Microservice beschränkt ist und verschiedene Technologien verwendet werden._*_

Bei einem monolithischen Ansatz wird von der Anwendung in der Regel eine Einzeldatenbank genutzt. Der Vorteil der Verwendung einer Datenbank besteht darin, dass ein einzelner Speicherort verwendet und so die Bereitstellung vereinfacht wird. Jede Komponente kann eine einzelne Tabelle zum Speichern des Zustands enthalten. Die Teams müssen den Zustand strikt trennen. Dies ist eine ziemliche Herausforderung. Die Versuchung, einer vorhandenen Kundentabelle eine neue Spalte hinzuzufügen, Tabellen zu verknüpfen und Abhängigkeiten auf Speicherebene zu erstellen, ist natürlich groß. Wenn dies passiert, können Sie einzelne Komponenten nicht skalieren.

Beim Microservice-Ansatz verwaltet und speichert jeder Dienst seinen eigenen Zustand. Jeder Dienst ist dafür verantwortlich, Code und Zustand gemeinsam zu skalieren, um die Anforderungen des Diensts zu erfüllen. Dadurch ergibt sich allerdings der Nachteil, dass Sie zum Erstellen von Sichten oder Abfragen für die Daten Ihrer Anwendung verschiedene Zustandsspeicher abfragen müssen. Normalerweise wird dieses Problem mit einem separaten Microservice gelöst, mit dem eine Sicht für eine Sammlung von Microservices erstellt wird. Falls Sie für die Daten mehrere Ad-hoc-Abfragen durchführen müssen, sollten Sie für jeden Microservice erwägen, dessen Daten für die Offlineanalyse in einen Data Warehousing-Dienst zu schreiben.

Microservices sind versionsspezifisch. Es ist möglich, verschiedene Versionen eines Microservice parallel auszuführen. Bei einer neueren Version eines Microservice kann während des Upgrades ein Fehler auftreten, sodass ein Rollback auf eine frühere Version erforderlich ist. Die Versionsverwaltung ist zudem für die Durchführung von A/B-Tests hilfreich, bei denen unterschiedliche Benutzer unterschiedliche Versionen des Diensts erhalten. Es ist beispielsweise üblich, einen Microservice für eine bestimmte Gruppe von Kunden zu aktualisieren, um neue Funktionen vor dem größeren Rollout zu testen.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagiert mit anderen Microservices über gut definierte Schnittstellen und Protokolle

In den letzten zehn Jahren wurden zahlreiche Informationen veröffentlicht, in denen die Kommunikationsmuster in dienstorientierten Architekturen beschrieben werden. Im Allgemeinen wird für die Dienstkommunikation ein REST-Ansatz mit HTTP- und TCP-Protokollen sowie XML oder JSON als Serialisierungsformat eingesetzt. Aus Sicht der Benutzeroberfläche geht es hierbei um den Webdesignansatz. Es hindert Sie aber nichts daran, binäre Protokolle oder eigene Datenformate zu verwenden. Denken Sie aber daran, dass die Verwendung Ihrer Microservices für Benutzer schwieriger ist, wenn diese Protokolle und Formate nicht öffentlich verfügbar sind.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Eindeutiger Name (URL), der zum Auflösen des Speicherorts verwendet wird

Ihr Microservice muss unabhängig vom Ort der Ausführung adressierbar sein. Wenn Sie anfangen, sich Gedanken über Computer und den Ort der Ausführung bestimmter Microservices zu machen, führt dies schnell zu Problemen.

Ebenso wie der DNS eine bestimmte URL auf einem bestimmten Computer auflöst, muss Ihr Microservice über einen eindeutigen Namen verfügen, damit sein aktueller Speicherort ermittelt werden kann. Für Microservices sind adressierbare Namen erforderlich, damit sie unabhängig von der Infrastruktur sind, auf der sie ausgeführt werden. Dies impliziert, dass eine Interaktion zwischen der Bereitstellung und der Ermittlung Ihres Diensts besteht, weil eine Dienstregistrierung vorhanden sein muss. Der Registrierungsdienst muss Sie im Fall eines Computerausfalls darüber informieren, wohin der Dienst verschoben wurde.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Bleibt beim Auftreten von Fehlern konsistent und verfügbar

Die Behandlung unerwarteter Fehler ist eines der schwierigsten Probleme, die in einem verteilten System gelöst werden müssen. Ein Großteil des Codes, den Entwickler schreiben, dient der Ausnahmebehandlung. Außerdem verbringen sie auch während der Tests die meiste Zeit mit der Behandlung von Ausnahmen. Der Prozess ist komplexer als das Schreiben von Code zum Behandeln von Fehlern. Was passiert, wenn der Computer ausfällt, auf dem der Microservice ausgeführt wird? Sie müssen in einem solchen Fall den Ausfall erkennen – was allein genommen schon ein schwieriges Problem ist. Sie müssen aber auch den Microservice neu starten.

Aus Verfügbarkeitsgründen muss ein Microservice ausfallsicher sein und auf einem anderen Computer neu gestartet werden können. Zusätzlich zu diesen Anforderungen an die Resilienz dürfen außerdem auch keine Daten verloren gehen, und die Daten müssen jederzeit konsistent bleiben.

Resilienz ist bei Fehlern während eines Anwendungsupgrades schwierig zu erzielen. Der Microservice, der mit dem Bereitstellungssystem eingesetzt wird, muss nicht wiederhergestellt werden können. Es muss entschieden werden, ob der Schritt zur neueren Version möglich ist oder ein Rollback auf die vorherige Version ausgeführt werden muss, um Konsistenz sicherzustellen. Sie müssen dabei beispielsweise folgende Fragen beantworten: Sind genügend Computer verfügbar, um die neue Version einzuführen? Und wie werden vorherige Versionen des Microservice wiederhergestellt? Für diese Entscheidungen müssen vom Microservice Informationen zur Integrität bereitgestellt werden.

### <a name="reports-health-and-diagnostics"></a>Melden der Integrität und Diagnose

Es ist eigentlich offensichtlich und wird doch oft übersehen, dass der Microservice seine Integritäts- und den Diagnosedaten melden muss. Andernfalls sind aus betrieblicher Sicht nur wenige Einblicke in die Integrität möglich. Die Herausforderung besteht darin, Diagnoseereignisse in einer Gruppe von unabhängigen Diensten zu korrelieren und Abweichungen der Computeruhren zum Ermitteln der richtigen Ereignisreihenfolge zu berücksichtigen. Genauso wie Sie mit einem Microservice über vereinbarte Protokolle und Datenformate interagieren, müssen Sie die Protokollierung von Integritäts- und Diagnoseereignissen standardisieren, die letztendlich zur Abfrage und Anzeige in einem Ereignisspeicher gespeichert werden. Bei einem Microservice-Ansatz ist es wichtig, dass sich die unterschiedlichen Teams auf ein gemeinsames Protokollierungsformat einigen. In der gesamten Anwendung muss ein einheitlicher Ansatz zum Anzeigen von Diagnoseereignissen verwendet werden.

Die Integrität unterscheidet sich von der Diagnose. Bei der Integrität geht es darum, dass der Microservice seinen aktuellen Zustand meldet, damit geeignete Maßnahmen ergriffen werden können. Ein gutes Beispiel ist die Verwendung der Upgrade- und Bereitstellungsmechanismen zum Wahren der Verfügbarkeit. Auch wenn ein Dienst aufgrund eines Prozessabsturzes oder eines Computerneustarts zu einem bestimmten Zeitpunkt unter Umständen keine volle Integrität aufweist, kann er dennoch betriebsbereit sein. Sie sollten auf jeden Fall vermeiden, diesen Zustand zu verschlimmern, indem Sie ein Upgrade starten. Die beste Vorgehensweise besteht darin, zuerst eine Untersuchung durchzuführen oder dem Microservice Zeit zum Beheben des Fehlers zu lassen. Anhand von Integritätsereignissen eines Microservice können wir fundierte Entscheidungen treffen und selbstreparierende Dienste erstellen.

## <a name="guidance-for-designing-microservices-on-azure"></a>Leitfaden zum Entwerfen von Microservices in Azure

Besuchen Sie das Azure Architecture Center. Dort finden Sie Anleitungen zum [Entwerfen und Erstellen von Microservices in Azure](/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric als Microservice-Plattform

Azure Service Fabric ist entstanden, als Microsoft die Umstellung von „Paketprodukten“, die meist monolithisch waren, auf Dienste vollzogen hat. Aus den Erfahrungen, die bei der Erstellung und dem Betrieb großer Dienste wie Azure SQL-Datenbank und Azure Cosmos DB gesammelt wurden, ist Service Fabric entstanden. Die Plattform entwickelte sich im Laufe der Zeit weiter und wurde von immer mehr Diensten genutzt. Service Fabric konnte nicht nur in Azure ausgeführt werden, sondern auch in eigenständigen Windows Server-Bereitstellungen.

_*_Die Ziele von Service Fabric sind das Lösen der schwierigen Probleme, die mit der Erstellung und Ausführung eines Diensts verbunden sind, und die effiziente Nutzung von Infrastrukturressourcen, damit Teams geschäftliche Probleme mit einem Microservice-Ansatz lösen können._*_

In diesem kurzen Video werden Service Fabric und Microservices vorgestellt:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

Service Fabric unterstützt Sie durch die Bereitstellung von Folgendem bei der Anwendungserstellung mit einem Microservice-Ansatz:

_ Diese Plattform bietet Systemdienste zum Bereitstellen, Upgraden, Erkennen und Neustarten ausgefallener Dienste und zum Ermitteln von Diensten, Weiterleiten von Nachrichten, Verwalten des Zustands und Überwachen der Integrität.
* Die Fähigkeit, Anwendungen entweder in Containern oder als Prozesse bereitzustellen. Service Fabric ist ein Container- und Prozessorchestrator.
* Produktive Programmier-APIs zum Erstellen von Anwendungen als Microservices: [ASP.NET Core, Reliable Actors und Reliable Services](service-fabric-choose-framework.md). Beispielsweise erhalten Sie entweder Integritäts- und Diagnoseinformationen, oder Sie können die integrierte Hochverfügbarkeit nutzen.

***Service Fabric ist hinsichtlich der Erstellung Ihres Diensts agnostisch, und Sie können eine beliebige Technologie verwenden. Es bietet jedoch integrierte Programmier-APIs, die die Erstellung von Microservices vereinfachen.** _

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrieren von vorhandenen Anwendungen zu Service Fabric

Service Fabric ermöglicht das Wiederverwenden von vorhandenem Code, der durch neue Microservices modernisiert werden kann. Es gibt fünf Phasen der Aktualisierung von Anwendungen. Sie können mit jeder beliebigen Phase beginnen und aufhören. Die Phasen sind:

1) Beginnen Sie mit einer herkömmlichen monolithischen Anwendung.  
2) Migration: Verwenden Sie Container oder ausführbare Gastanwendungsdateien, um vorhandenen Code in Service Fabric zu hosten.  
3) Modernisierung: Fügen Sie neue Microservices gemeinsam mit vorhandenem Code in Containern hinzu.  
4) Innovation: Zerlegen Sie eine monolithische Anwendung nach Ihrem Bedarf in Microservices.  
5) Transformation von Anwendungen in Microservices: Transformieren Sie bestehende monolithische Anwendungen, oder erstellen Sie neue Anwendungen.

![Migration zu Microservices][Image3]

Denken Sie daran: Sie können _in jeder dieser Phasen beginnen und aufhören*. Sie müssen nicht mit der nächsten Stufe fortfahren. 

Sehen Sie sich nun Beispiele für jede dieser Phasen an.

**Migrieren**  
Viele Unternehmen migrieren bestehende monolithische Anwendungen in Container. Dafür gibt es zwei Gründe:

* Kostensenkung entweder durch die Konsolidierung und das Entfernen der vorhandenen Hardware oder durch das Ausführen von Anwendungen mit höherer Dichte.
* Konsistenter Bereitstellungvertrag zwischen der Entwicklung und dem Betrieb.

Kostensenkungen sind selbsterklärend. Bei Microsoft sind viele vorhandene Anwendungen in Containern verpackt, wodurch Millionen von Dollar eingespart werden. Konsistente Bereitstellung ist schwieriger zu bewerten, jedoch ebenso wichtig. Sie bedeutet, dass Entwickler die geeigneten Technologien selbst auswählen können, aber die Vorgänge nur eine einzige Methode für das Bereitstellen und Verwalten der Anwendungen akzeptieren. So müssen die Vorgänge nicht unterschiedliche, komplexe Technologien unterstützen. Zudem müssen sich Entwickler nicht nur für eine entscheiden. Im Wesentlichen wird jede Anwendung in Container in eigenständigen Bereitstellungsimages verschoben.

Viele Organisationen fahren hier nicht fort. Sie nutzen bereits die Vorteile von Containern, und Service Fabric bietet alle Verwaltungsmöglichkeiten – von der Bereitstellung über Upgrades, Versionskontrolle und Rollbacks bis hin zur Überwachung der Integrität.

**Modernisierung**  
Bei der Modernisierung werden neue Dienste gemeinsam mit vorhandenem Code in Containern hinzugefügt. Wenn Sie neuen Code schreiben möchten, empfiehlt es sich, kleine Schritte auf dem Weg zu Microservices zu machen. Dies kann das Hinzufügen eines neuen REST-API-Endpunkts oder neue Geschäftslogik sein. Auf diese Weise haben Sie die ersten Schritte auf dem Weg zu Microservices gemacht und können sich gleichzeitig mit dem Entwickeln und Bereitstellen dieser vertraut machen.

**Innovation**  
Ein Microservices-Ansatz ist auf Veränderungen der geschäftlichen Anforderungen vorbereitet. In dieser Phase müssen Sie entscheiden, ob Sie die monolithische Anwendung in Dienste aufteilen möchten oder ob Sie eine Innovation anstreben. Ein klassisches Beispiel ist, wenn eine Datenbank, die als Workflowwarteschlange verwendet wird, zu einem Verarbeitungsengpass wird. Mit zunehmender Anzahl an Workflowanfragen muss die Arbeit zur Skalierung verteilt werden. Für diesen spezifischen Bereich der Anwendung, der nicht die Skalierung ist, oder den Sie häufiger aktualisieren müssen, teilen Sie dies in einen Microservice auf und führen eine Innovation durch.

**Transformation von Anwendungen in Microservices**  
In dieser Phase besteht Ihre Anwendung komplett aus Microservices oder ist in Microservices unterteilt. Wenn Sie diesen Punkt erreichen, haben Sie die Journey zu Microservices abgeschlossen. Sie können hier starten. Wenn Sie dies jedoch ohne eine unterstützende Microservice-Plattform machen, erfordert dies eine erhebliche Investition.

### <a name="are-microservices-right-for-my-application"></a>Sind Microservices für meine Anwendung geeignet?

Vielleicht. Als bei Microsoft immer mehr Teams damit begannen, aus geschäftlichen Gründen Anwendungen für die Cloud zu entwickeln, erkannten viele von ihnen die Vorteile eines Microservice-Ansatzes. Für Bing werden beispielsweise schon seit Jahren Microservices genutzt. Für andere Teams war der Microservice-Ansatz neu. Die Teams haben festgestellt, dass es schwierige Probleme außerhalb ihrer Kernkompetenzbereiche zu lösen galt. Dies sind die Gründe, warum Service Fabric immer häufiger als Technologie für die Erstellung von Diensten eingesetzt wird.

Das Ziel von Service Fabric ist die Reduzierung der Komplexität bei der Erstellung von Microservice-Anwendungen, damit bei Ihnen nicht mehr so viele teure Überarbeitungen anfallen. Beginnen Sie klein, führen Sie bei Bedarf eine Skalierung durch, mustern Sie Dienste aus, fügen Sie neue hinzu, und entwickeln Sie die Lösung während der Nutzung durch die Kunden weiter. Uns ist auch bewusst, dass noch viele andere Probleme gelöst werden müssen, damit Microservices für die Mehrzahl der Entwickler noch besser zugänglich werden. Container und das Programmiermodell mit Akteuren sind Beispiele für kleine Schritte in dieser Richtung. Wir sind sicher, dass weitere Innovationen folgen werden, um den Microservice-Ansatz noch einfacher zu machen.

## <a name="next-steps"></a>Nächste Schritte

* [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: Eine cloudbasierte Anwendungsrevolution)
* [Azure Architecture Center: Erstellen von Microservices in Azure](/azure/architecture/microservices/)
* [Best Practices für Azure Service Fabric-Anwendungen und -Cluster](service-fabric-best-practices-overview.md)
* [Übersicht über Service Fabric-Terminologie](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
