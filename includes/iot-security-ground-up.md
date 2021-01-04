---
title: include file
description: include file
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 234407e6973657ba3ad0d78189e7cb1d363c15e2
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560585"
---
Das Internet der Dinge (Internet of Things, IoT) ist für Unternehmen weltweit mit neuen Anforderungen in Bezug auf Sicherheit, Datenschutz und Compliance verbunden. Im Gegensatz zur herkömmlichen Cyber-Technologie, bei der es um Software und ihre Implementierung geht, liegt der Schwerpunkt bei IoT darauf, was passiert, wenn die Cyber-Welt und die physische Welt aufeinandertreffen. Zum Schützen von IoT-Lösungen müssen die sichere Bereitstellung von Geräten, die sichere Verbindung zwischen diesen Geräten und der Cloud sowie der sichere Schutz von Daten in der Cloud während der Verarbeitung und Speicherung sichergestellt werden. Dieser Funktionalität stehen aber Geräte mit Ressourcenbeschränkung, die geografische Verteilung von Bereitstellungen und eine große Zahl von Geräten innerhalb einer Lösung entgegen.

In diesem Artikel wird erläutert, wie die IoT Solution Accelerators eine sichere und private Cloudlösung für das Internet der Dinge ermöglicht. Die Solution Accelerators bieten eine Komplettlösung, bei der alle Ebenen von Anfang an auf Sicherheit ausgelegt sind. Bei Microsoft ist die Entwicklung von sicherer Software fester Bestandteil der Software Engineering-Routine, die auf Microsofts jahrzehntelanger Erfahrung mit der Entwicklung sicherer Software basiert. Um dies sicherzustellen, wird der Security Development Lifecycle (SDL) als fundamentale Entwicklungsmethodik verwendet, gekoppelt mit vielen verschiedenen Sicherheitsdiensten auf Infrastrukturebene, z.B. Operational Security Assurance (OSA) sowie der Microsoft Digital Crimes Unit, dem Microsoft Security Response Center und dem Microsoft Center zum Schutz vor Malware.

Die Solution Accelerators verfügen über spezielle Features, mit denen das Bereitstellen und Speichern von Daten von IoT-Geräten sowie das Verbinden mit diesen einfach, transparent und vor allem sicher möglich ist. In diesem Artikel werden die Sicherheitsfeatures und Bereitstellungsstrategien der Azure IoT Solution Accelerators beschrieben, mit denen sichergestellt werden kann, dass die Anforderungen an Sicherheit, Datenschutz und Compliance erfüllt werden.

## <a name="introduction"></a>Einführung

Das Internet der Dinge (IoT) ist der Zukunftstrend, der Unternehmen unmittelbare und echte Chancen bietet, die Kosten zu reduzieren, den Umsatz zu steigern und ihr Geschäft zu transformieren. Viele Unternehmen stellen IoT in ihren Abteilungen aber nur zögerlich bereit, da sie Bedenken in Bezug auf die Sicherheit, den Datenschutz und die Compliance haben. Die Hauptsorge beruht auf der Einzigartigkeit der IoT-Infrastruktur, bei der die Cyber-Welt und die physische Welt und damit auch die individuellen Risiken dieser beiden Bereiche zusammentreffen. Bei der Sicherheit von IoT geht es um Folgendes: die Sicherstellung der Integrität des auf Geräten ausgeführten Codes, die Bereitstellung einer Authentifizierung von Gerät und Benutzer, die Definition einer eindeutigen Eigentümerschaft von Geräten (und der von diesen Geräten generierten Daten) sowie die Resilienz gegenüber Cyber-Angriffen und physischen Angriffen.

Außerdem muss der Datenschutz gewährleistet werden. Die Unternehmen wünschen sich Transparenz bei der Datenerfassung, also welche Daten von wem zu welchem Zweck gesammelt werden, wer diese Daten sehen kann, wer den Zugriff steuert usw. Darüber hinaus muss für die Ausrüstung und die Bediener das Problem der allgemeinen Sicherheit gelöst werden, und die Branchenstandards müssen eingehalten werden, um die Compliance sicherzustellen.

Aufgrund der Bedenken in Bezug auf Sicherheit, Datenschutz, Transparenz und Compliance ist die Auswahl des richtigen IoT-Lösungsanbieters eine große Herausforderung. Die Verknüpfung einzelner Komponenten von IoT-Software und -Diensten, die von unterschiedlichen Anbietern bereitgestellt werden, führt zu Lücken bei der Sicherheit, dem Datenschutz, der Transparenz und der Compliance, die sehr schwierig zu erkennen und noch schwieriger zu beheben sein können. Bei der Auswahl der richtigen IoT-Software und des passenden Dienstanbieters sollten Sie Anbieter ermitteln, die über umfassende Erfahrung mit dem Ausführen von Diensten über vertikale Märkte und Regionen hinweg verfügen und gleichzeitig eine sichere und transparente Skalierung ermöglichen. Es ist auch hilfreich, wenn der ausgewählte Anbieter über mehrere Jahrzehnte Erfahrung mit der Entwicklung sicherer Software verfügt, die auf Milliarden von Computern weltweit ausgeführt wird, und sich mit der Bedrohungslandschaft auskennt, die durch diese neue Welt des Internets der Dinge entsteht.

## <a name="secure-infrastructure-from-the-ground-up"></a>Von Anfang an eine sichere Infrastruktur

Die [Microsoft Cloud](https://azure.microsoft.com) -Infrastruktur unterstützt mehr als eine Milliarde Kunden in 127 Ländern/Regionen. Durch die jahrzehntelange Erfahrung von Microsoft mit der Erstellung von Unternehmenssoftware und der Ausführung einiger der größten Onlinedienste der Welt sind mit Microsoft Cloud höhere Ebenen an Sicherheit, Datenschutz und Compliance und bessere Bedrohungsabwehrmaßnahmen möglich, als die meisten auf sich allein gestellte Kunden erreichen können.

Der [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) umfasst einen obligatorischen unternehmensweiten Entwicklungsprozess, bei dem Sicherheitsanforderungen in den gesamten Softwarelebenszyklus eingebettet werden. Damit sichergestellt ist, dass für die Betriebsaktivitäten dieselbe Ebene der Sicherheitsmaßnahmen befolgt wird, finden in SDL rigorose Sicherheitsrichtlinien Anwendung, die im OSA-Prozess (Operational Security Assurance) von Microsoft definiert sind. Microsoft arbeitet auch mit unabhängigen Auditingunternehmen zusammen, damit die Einhaltung von Compliancevorgaben ständig überprüft wird. Außerdem werden große Anstrengungen in Bezug auf die Sicherheit unternommen, indem Microsoft entsprechende Kompetenzzentren geschaffen hat, etwa die Microsoft Digital Crimes Unit, das Microsoft Security Response Center und das Microsoft Center zum Schutz vor Schadsoftware.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure – Sichere IoT-Infrastruktur für Ihr Unternehmen

Microsoft Azure bietet eine vollständige Cloud-Lösung, bei der eine ständig wachsende Gruppe von integrierten Clouddiensten – Analyse, Machine Learning, Speicher, Sicherheit, Netzwerk und Web – mit einer branchenführenden Ausrichtung auf allgemeinen Schutz und den Datenschutz für Ihre Daten kombiniert wird. Bei der [Assume Breach](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/)-Strategie wird ein dediziertes *Red Team* mit Softwaresicherheitsexperten eingesetzt, die Angriffe simulieren und testen, wie gut mit Azure Sicherheitsverletzungen erkannt, der Schutz von neuen Bedrohungen sichergestellt und die Wiederherstellung nach Sicherheitsverletzungen durchgeführt werden kann. Das Microsoft-Team zur [Reaktion auf globale Vorfälle](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) arbeitet rund um die Uhr daran, die Auswirkungen von Angriffen und schädlichen Aktivitäten einzudämmen. Das Team nutzt etablierte Verfahren für die Bereiche Vorfallmanagement, Kommunikation und Wiederherstellung und setzt ermittelbare und vorhersagbare Schnittstellen für interne und externe Partner ein.

Die Systeme von Microsoft bieten eine kontinuierliche Erkennung und Verhinderung von Angriffen, Verhinderung von Angriffen auf Dienste, regelmäßige Penetrationstests und forensische Tools zum Identifizieren und Eindämmen von Bedrohungen. [Multi-Factor Authentication](../articles/active-directory/authentication/concept-mfa-howitworks.md) ist eine zusätzliche Sicherheitsschicht für Endbenutzer, die auf das Netzwerk zugreifen. Für die Anwendung und den Hostanbieter bietet Microsoft außerdem Zugriffssteuerung, Überwachung, Antischadsoftware, Überprüfung auf Sicherheitslücken, Patching und Konfigurationsverwaltung.

Für die Solution Accelerators werden die Sicherheits- und Datenschutzfeatures der Azure-Plattform sowie die SDL- und OSA-Prozesse für die sichere Entwicklung und den sicheren Betrieb der gesamten Microsoft-Software genutzt. Diese Verfahren ermöglichen Infrastrukturschutz, Netzwerkschutz und Identitäts- und Verwaltungsfeatures, die für die Sicherheit jeder Lösung von großer Wichtigkeit sind.

Der [Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) in den [IoT Solution Accelerators](../articles/iot-fundamentals/iot-introduction.md) stellt einen vollständig verwalteten Dienst bereit, der die zuverlässige und sichere bidirektionale Kommunikation zwischen IoT-Geräten und Azure-Diensten wie [Azure Machine Learning](../articles/machine-learning/classic/index.yml) und [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) über gerätebezogene Sicherheitsanmeldeinformationen und Zugriffssteuerung ermöglicht.

Um die in die Azure IoT Solution Accelerators integrierten Sicherheits- und Datenschutzfeatures auf bestmögliche Weise kommunizieren zu können, wird die Suite in diesem Artikel in drei Hauptsicherheitsbereiche unterteilt.

![Azure IoT Solution Accelerators](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Sichere Gerätebereitstellung und -authentifizierung

Mit den Solution Accelerators werden Geräte im Außeneinsatz geschützt, indem ein eindeutiger Identitätsschlüssel für jedes Gerät vergeben wird. Der Schlüssel kann von der IoT-Infrastruktur verwendet werden, um mit dem Gerät zu kommunizieren, während es in Betrieb ist. Der Prozess kann schnell und einfach eingerichtet werden. Der generierte Schlüssel mit einer vom Benutzer ausgewählten Geräte-ID bildet die Basis eines Tokens, das bei der gesamten Kommunikation zwischen dem Gerät und dem Azure IoT Hub verwendet wird.

Geräte-IDs können einem Gerät während der Herstellung zugeordnet werden (z.B. per Flashvorgang in einem Hardware Trust Module), oder es kann eine vorhandene feste Identität als Proxy verwendet werden (z.B. CPU-Seriennummern). Da das Ändern dieser Informationen für die Identifizierung im Gerät nicht einfach ist, ist es wichtig, logische Geräte-IDs einzuführen, falls sich die zugrunde liegende Gerätehardware ändert und das logische Gerät gleich bleibt. In einigen Fällen kann die Zuordnung einer Geräteidentität während der Bereitstellung eines Geräts erfolgen (z.B. wenn ein neues Gerät von einem authentifizierten Außendiensttechniker während der Kommunikation mit dem Lösungs-Back-End physisch konfiguriert wird). Die [Azure IoT Hub-Identitätsregistrierung](../articles/iot-hub/iot-hub-devguide.md) ermöglicht die sichere Speicherung von Geräteidentitäten und Sicherheitsschlüsseln für eine Lösung. Geräteidentitäten können einzeln oder in Gruppen einer Zulassungsliste oder Blockierungsliste hinzugefügt werden, um die vollständige Kontrolle über den Gerätezugriff zu haben.

Azure IoT Hub-Richtlinien für die Zugriffssteuerung in der Cloud ermöglichen die Aktivierung und Deaktivierung jeder Geräteidentität und somit einen neuen Weg, um die Zuordnung eines Geräts zu einer IoT-Bereitstellung bei Bedarf aufzuheben. Diese Zuordnung und Aufhebung der Zuordnung von Geräten basiert jeweils auf der Geräteidentität.

Weitere Sicherheitsfeatures für Geräte:

* Geräte akzeptieren keine unerwünschten Netzwerkverbindungen. Sie stellen alle Verbindungen und Routen nur in ausgehender Richtung her. Damit ein Gerät einen Befehl vom Back-End erhalten kann, muss das Gerät eine Verbindung initiieren, um eine Prüfung auf ausstehende Befehle durchzuführen, die verarbeitet werden müssen. Nachdem eine Verbindung zwischen dem Gerät und dem IoT Hub sicher eingerichtet wurde, können Nachrichten auf transparente Weise aus der Cloud an das Gerät und vom Gerät in die Cloud gesendet werden.

* Geräte stellen Verbindungen nur mit bekannten Peerdiensten her bzw. richten Routen dafür ein, z.B. mit einem Azure IoT Hub.

* Für die Autorisierung und Authentifizierung auf Systemebene werden gerätebezogene Identitäten verwendet, sodass Anmeldeinformationen und Berechtigungen nahezu sofort widerrufbar sind.

### <a name="secure-connectivity"></a>Sichere Verbindungen

Die Dauerhaftigkeit von Nachrichten ist ein wichtiges Feature jeder IoT-Lösung. Die Bedeutung der Stabilität beim Bereitstellen von Befehlen bzw. Empfangen von Daten von Geräten wird durch die Tatsache unterstrichen, dass IoT-Geräte über das Internet oder ähnliche Netzwerke verbunden sind, die unter Umständen unzuverlässig sind. Der Azure IoT Hub sorgt für die Dauerhaftigkeit des Nachrichtenaustauschs zwischen der Cloud und Geräten, indem als Reaktion auf Nachrichten ein System von Bestätigungen genutzt wird. Zusätzliche Dauerhaftigkeit wird für Nachrichten erzielt, indem Nachrichten für Telemetrie bis zu sieben Tage und für Befehle bis zu zwei Tage im IoT Hub gespeichert werden.

Effizienz ist wichtig, um in einer Umgebung mit eingeschränkten Ressourcen die Ressourcenschonung und den Betrieb sicherzustellen. HTTPS (HTTP Secure), der Branchenstandard für die sichere Version des gängigen HTTP-Protokolls, wird vom Azure IoT Hub unterstützt und ermöglicht eine effiziente Kommunikation. Das vom Azure IoT Hub unterstützte Advance Message Queueing Protocol (AMQP) und Message Queuing Telemetry Transport (MQTT) sind nicht nur in Bezug auf die Ressourcennutzung auf Effizienz ausgelegt, sondern auch in Bezug auf die zuverlässige Nachrichtenübermittlung. 

Für die Skalierbarkeit ist es erforderlich, dass eine sichere Interoperabilität mit einer großen Zahl von Geräten besteht. Der Azure IoT Hub ermöglicht eine sichere Verbindung mit IP-fähigen und nicht IP-fähigen Geräten. Für IP-fähige Geräte ist die direkte Verbindungsherstellung und Kommunikation mit dem IoT Hub über eine sichere Verbindung möglich. Nicht IP-fähige Geräte verfügen über eingeschränkte Ressourcen und stellen die Verbindung nur über Protokolle für die Kommunikation über kurze Distanzen her, z.B. Zwave, ZigBee und Bluetooth. Ein Bereichsgateway wird verwendet, um diese Geräte zusammenzufassen, und es führt die Protokollübersetzung durch, um die sichere bidirektionale Kommunikation mit der Cloud zu ermöglichen.

Weitere Features für die Verbindungssicherheit:

* Der Kommunikationspfad zwischen Geräten und dem Azure IoT Hub oder zwischen Gateways und dem Azure IoT Hub wird per branchenüblicher Transport Layer Security (TLS) geschützt, wobei der Azure IoT Hub mit dem X.509-Protokoll authentifiziert wird.

* Um Geräte vor unerwünschten eingehenden Verbindungen zu schützen, öffnet der Azure IoT Hub keine Verbindung mit dem Gerät. Alle Verbindungen werden vom Gerät initiiert.

* Der Azure IoT Hub speichert Nachrichten für Geräte dauerhaft und wartet, bis die Verbindung vom Gerät hergestellt wird. Diese Befehle werden zwei Tage lang gespeichert, damit auch Geräte, die aufgrund von bestimmten Stromversorgungs- oder Verbindungsaspekten nur sporadisch verbunden sind, diese Befehle erhalten. Azure IoT Hub verwaltet für jedes Gerät eine eigene Warteschlange.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Sichere Verarbeitung und Speicherung in der Cloud

Von der verschlüsselten Kommunikation bis zur Verarbeitung von Daten in der Cloud sorgen die Solution Accelerators für die Sicherheit der Daten. Sie bietet Flexibilität bei der Implementierung zusätzlicher Verschlüsselung und der Verwaltung von Sicherheitsschlüsseln.

Indem Azure Active Directory (AAD) für die Benutzerauthentifizierung und -autorisierung verwendet wird, kann mit den Solution Accelerators ein richtlinienbasiertes Autorisierungsmodell für Daten in der Cloud bereitgestellt werden. So ist für eine einfache Zugriffsverwaltung gesorgt, die überwacht und überprüft werden kann. Außerdem ermöglicht dieses Modell das nahezu sofortige Widerrufen des Zugriffs auf Daten in der Cloud und für Geräte, die mit den Solution Accelerators verbunden sind.

Sobald sich Daten in der Cloud befinden, können sie in jedem benutzerdefinierten Workflow verarbeitet und gespeichert werden. Der Zugriff auf die einzelnen Teile der Daten wird mit Azure Active Directory je nach verwendetem Speicherdienst gesteuert.

Alle von der IoT-Infrastruktur verwendeten Schlüssel, werden in der Cloud im sicheren Speicher abgelegt, und es kann ein Rollover durchgeführt werden, falls Schlüssel erneut bereitgestellt werden müssen. Daten können in [Azure Cosmos DB](../articles/cosmos-db/introduction.md) oder in [SQL-Datenbanken](../articles/azure-sql/database/sql-database-paas-overview.md) gespeichert werden, sodass die gewünschte Sicherheitsebene definiert werden kann. Darüber hinaus verfügt Azure über einen Weg zum Überwachen und Überprüfen des gesamten Zugriffs auf Ihre Daten, damit Sie vor allen Angriffen und unberechtigten Zugriffen gewarnt werden können.

## <a name="conclusion"></a>Zusammenfassung

Das Internet der Dinge beginnt mit Ihren „Dingen“, also den Dingen, die Unternehmen am meisten am Herzen liegen. Das IoT kann für ein Unternehmen von hohem Nutzen sein, indem Kosten reduziert werden, der Umsatz gesteigert wird und das Geschäft transformiert wird. Der Erfolg der Transformation hängt größtenteils von der Wahl der richtigen IoT-Software und des richtigen Dienstanbieters ab. Hiermit ist die Wahl eines Anbieters gemeint, der diese Transformation nicht nur fördert und beschleunigt, weil er die geschäftlichen Anforderungen kennt. Der Anbieter sollte auch Dienste und Software mit einem Design bereitstellen, bei dem Sicherheit, Datenschutz, Transparenz und Compliance im Vordergrund stehen. Microsoft verfügt über umfassende Erfahrung in Bezug auf die Entwicklung und Bereitstellung sicherer Software und Dienste und nimmt auch im neuen Zeitalter des Internets der Dinge eine führende Stellung ein.

Sicherheitsmaßnahmen sind in die Solution Accelerators standardmäßig integriert, um die sichere Überwachung von Objekten zu ermöglichen und so für mehr Effizienz zu sorgen, die operative Leistung zu verbessern und Innovationen möglich zu machen und das Geschäft mit erweiterten Datenanalysen zu transformieren. Aufgrund des Ansatzes mit mehreren Ebenen für die Sicherheit, mehreren Sicherheitsfeatures und Entwurfsmustern tragen die Solution Accelerators zur Bereitstellung einer Infrastruktur bei, die für die Transformation jedes Unternehmens geeignet ist.

## <a name="additional-information"></a>Zusätzliche Informationen

Für jeden Solution Accelerator werden Instanzen von Azure-Diensten erstellt, z.B.:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): Das Gateway, über das die Cloud mit Geräten verbunden wird. Sie können eine Skalierung auf Millionen von Verbindungen pro Hub durchführen und riesige Datenmengen mit Authentifizierung pro Gerät verarbeiten, um Ihre Lösung zu schützen.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): Ein skalierbarer, vollständig indizierter Datenbankdienst für semistrukturierte Daten, mit dem die Metadaten für die bereitgestellten Geräte verwaltet werden, z.B. Attribute, Konfiguration und Sicherheitseigenschaften. Azure Cosmos DB bietet eine Verarbeitung mit hoher Leistung und hohem Durchsatz, eine schemaagnostische Indizierung von Daten und eine umfassende SQL-Abfrageschnittstelle.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): Datenstromverarbeitung in Echtzeit in der Cloud. Dadurch wird eine schnelle Entwicklung und Bereitstellung einer kostengünstigen Analyselösung ermöglicht, mit der Sie in Echtzeit – basierend auf Geräte-, Sensor-, Infrastruktur- und Anwendungsdaten – wichtige Einblicke erhalten. Die Daten aus diesem vollständig verwalteten Dienst können auf beliebige Volumina skaliert werden, während trotzdem ein hoher Durchsatz, eine geringe Latenz und Resilienz erzielt werden.

* [**Azure App Services**](https://azure.microsoft.com/services/app-service/): Eine Cloudplattform für leistungsstarke mobile und Web-Apps, für die eine Verbindung mit cloudbasierten oder lokalen Datenquellen hergestellt werden kann. Entwickeln Sie benutzerfreundliche mobile Apps für iOS, Android und Windows. Profitieren Sie von einer Integration in Ihre SaaS- (Software as a Service) und Unternehmensanwendungen und vom standardmäßigen Zugriff auf Dutzende von cloudbasierten Diensten und Unternehmensanwendungen. Programmieren Sie in Ihrer bevorzugten Sprache und IDE, z.B. .NET, Node.js, PHP, Python oder Java, um schneller als je zuvor Web-Apps und APIs zu entwickeln.

* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): Das Logic Apps-Feature von Azure App Service dient als Hilfe bei der Integration Ihrer IoT-Lösung in Ihre vorhandenen branchenspezifischen Systeme und bei der Automatisierung von Workflowprozessen. Mit Logik-Apps können Entwickler Workflows entwerfen, die über einen Auslöser gestartet werden und dann eine Reihe von Schritten ausführen – Regeln und Aktionen, für die leistungsfähige Connectors zur Integration in Ihre Geschäftsprozesse eingesetzt werden. Logik-Apps ermöglichen standardmäßig Verbindungen mit einem umfassenden Ökosystem von SaaS-, cloudbasierten und lokalen Anwendungen.

* [**Azure Blob Storage**](https://azure.microsoft.com/services/storage/): Ein zuverlässiger, kostengünstiger Cloudspeicher für die Daten, die von Ihren Geräten an die Cloud gesendet werden.