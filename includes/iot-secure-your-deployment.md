---
title: include file
description: include file
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010696"
---
Dieser Artikel bietet weitere Details zum Schützen Ihrer auf Azure IoT basierenden IoT-Infrastruktur (Internet of Things, Internet der Dinge). Geboten werden Links zu Details auf Implementierungsebene für die Konfiguration und Bereitstellung der einzelnen Komponenten. Darüber hinaus finden Sie Vergleiche, um aus verschiedenen konkurrierenden Methoden die gewünschte zu wählen.

Der Schutz der Azure IoT-Bereitstellung kann in die folgenden drei Bereiche unterteilt werden:

* **Gerätesicherheit**: Schützen des IoT-Geräts während seiner Bereitstellung in der Praxis.

* **Verbindungssicherheit**: Sicherstellen, dass alle Daten, die zwischen IoT-Gerät und IoT Hub übertragen werden, vertraulich und fälschungssicher sind.

* **Cloudsicherheit**: Bieten einer Methode zum Schützen von Daten während der Übertragung und Speicherung in der Cloud.

![Drei Sicherheitsbereiche](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Sichere Gerätebereitstellung und -authentifizierung

Die IoT Solution Accelerators schützen IoT-Geräte mithilfe der beiden folgenden Methoden:

* Durch Bereitstellen eines eindeutigen Identitätsschlüssels (Sicherheitstokens) für jedes Gerät, der vom Gerät für die Kommunikation mit dem IoT Hub verwendet werden kann.

* Durch Verwenden eines [X.509-Zertifikats](https://www.itu.int/rec/T-REC-X.509-201210-S) und privaten Schlüssels auf dem Gerät als Mittel zur Authentifizierung des Geräts bei IoT Hub. Diese Authentifizierungsmethode stellt sicher, dass der private Schlüssel auf dem Gerät außerhalb des Geräts stets unbekannt ist, was für mehr Sicherheit sorgt.

Die Sicherheitstokenmethode ermöglicht, dass das Gerät bei jedem Aufruf des IoT Hubs authentifiziert wird, indem dem Aufruf der symmetrische Schlüssel zugeordnet wird. Die X.509-basierte Authentifizierung ermöglicht die Authentifizierung eines IoT-Geräts auf physischer Ebene im Rahmen des TLS-Verbindungsaufbaus. Die auf Sicherheitstoken basierende Methode kann ohne X.509-Authentifizierung genutzt werden, dies ist allerdings weniger sicher. Die Wahl zwischen den beiden Methoden wird in erster Linie davon bestimmt, wie sicher die Geräteauthentifizierung erfolgen muss und ob ein sicherer Speicher auf dem Gerät vorhanden ist (in dem der private Schlüssel geschützt gespeichert wird).

## <a name="iot-hub-security-tokens"></a>IoT Hub-Sicherheitstoken

IoT Hub verwendet Sicherheitstoken zum Authentifizieren von Geräten und Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. Darüber hinaus sind Sicherheitstoken im Hinblick auf Gültigkeitszeitraum und Bereich beschränkt. Azure IoT SDKs generieren Token automatisch, ohne dass eine spezielle Konfiguration erforderlich ist. In einigen Szenarien muss der Benutzer Sicherheitstoken jedoch direkt generieren und verwenden. Zu diesen Szenarien gehören die direkte Verwendung von MQTT, AMQP oder HTTP und die Implementierung des Tokendienstmusters.

Weitere Informationen zur Struktur des Sicherheitstokens und seiner Verwendung finden Sie in den folgenden Artikeln:

* [Struktur von Sicherheitstoken](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Verwenden von SAS-Token als Gerät](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Jeder IoT Hub verfügt über eine [Identitätsregistrierung](../articles/iot-hub/iot-hub-devguide-identity-registry.md), die Sie zum Erstellen von gerätebasierten Ressourcen im Dienst verwenden können. Hierzu zählt beispielsweise eine Warteschlange mit gesendeten C2D-Nachrichten. Außerdem wird der Zugriff auf geräteseitige Endpunkte ermöglicht. Die IoT Hub-Identitätsregistrierung ermöglicht die sichere Speicherung von Geräteidentitäten und Sicherheitsschlüsseln für eine Lösung. Geräteidentitäten können einzeln oder in Gruppen einer Zulassungsliste oder Blockierungsliste hinzugefügt werden, um die vollständige Kontrolle über den Gerätezugriff zu haben. Die folgenden Artikel enthalten weitere Informationen zur Struktur der Identitätsregistrierung und zu unterstützten Vorgängen.

[IoT Hub unterstützt Protokolle wie MQTT, AMQP und HTTP.](../articles//iot-hub/iot-hub-devguide-security.md) Jedes dieser Protokolle verwendet Sicherheitstoken des IoT-Geräts für IoT Hub unterschiedlich:

* AMQP: SASL PLAIN- und AMQP Claims-basierte Sicherheit (`{policyName}@sas.root.{iothubName}` bei Token auf Ebene des IoT-Hubs, `{deviceId}` bei auf Geräte bezogenen Token).

* MQTT: Das CONNECT-Paket verwendet `{deviceId}` als `{ClientId}`, `{IoThubhostname}/{deviceId}` im Feld **Benutzername** und ein SAS-Token im Feld **Kennwort**.

* HTTP: Ein gültiges Token befindet sich im Autorisierungsheader der Anforderung.

Mithilfe der Identitätsregistrierung von IoT Hub können Sie Sicherheitsanmeldeinformationen und die Zugriffssteuerung gerätebezogen konfigurieren. Auch wenn eine IoT-Lösung bereits erheblichen Anteil an einer [benutzerdefinierten Geräteidentitätsregistrierung bzw. einem Authentifizierungsschema](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication) hat, können Sie diese in eine vorhandene Infrastruktur in IoT Hub integrieren, indem ein Tokendienst erstellt wird.

### <a name="x509-certificate-based-device-authentication"></a>Auf X.509-Zertifikat basierende Geräteauthentifizierung

Die Verwendung eines [gerätebasierten X.509-Zertifikats](../articles/iot-hub/iot-hub-devguide-security.md) und des zugehörigen Paares aus privatem und öffentlichem Schlüssel ermöglicht eine zusätzliche Authentifizierung auf physischer Ebene. Der private Schlüssel wird sicher auf dem Gerät gespeichert und ist außerhalb des Geräts nicht ermittelbar. Das X.509-Zertifikat enthält Informationen zum Gerät, z.B. die Geräte-ID und andere organisatorische Details. Eine Signatur des Zertifikats wird mithilfe des privaten Schlüssels generiert.

Allgemeiner Ablauf der Gerätebereitstellung:

* Zuordnen eines Bezeichners zu einem physischen Gerät: Eine Geräte-Identität und/oder ein X.509-Zertifikat wird dem Gerät während der Fertigung oder Inbetriebnahme zugeordnet.

* Erstellen eines entsprechenden Eintrags für die Identität in IoT Hub: Die Geräte-Identität und zugehörige Informationen zum Gerät werden der IoT Hub-Identitätsregistrierung hinzugefügt.

* Sicheres Speichern des X.509-Zertifikatfingerabdrucks in der IoT Hub-Identitätsregistrierung.

### <a name="root-certificate-on-device"></a>Stammzertifikat auf dem Gerät

Beim Herstellen einer sicheren TLS-Verbindung mit IoT Hub authentifiziert sich das IoT-Gerät beim IoT Hub mithilfe eines Stammzertifikats, das Teil des Geräte-SDK ist. Beim SDK für C-Clients befindet sich das Zertifikat im Ordner „\\c\\certs“ unter dem Stammverzeichnis des Repositorys. Obwohl diese Stammzertifikate langlebig sind, können sie dennoch abgelaufen oder widerrufen worden sein. Wenn es keine Möglichkeit gibt, das Zertifikat auf dem Gerät zu aktualisieren, kann sich das Gerät ggf. in der Folge nicht mit dem IoT Hub (oder einem anderen Clouddienst) verbinden. Durch eine Möglichkeit zum Aktualisieren des Stammzertifikats, nachdem das IoT-Gerät bereitgestellt wurde, wird dieses Risiko effektiv verringert.

## <a name="securing-the-connection"></a>Schützen der Verbindung

Die Internetverbindung zwischen IoT-Gerät und IoT Hub wird standardmäßig mit TLS (Transport Layer Security) geschützt. Azure IoT unterstützt [TLS 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 und TLS 1.0 in der angegebenen Reihenfolge. Unterstützung für TLS 1.0 wird nur zum Zweck der Abwärtskompatibilität geboten. Über die [TLS-Unterstützung in IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) können Sie herausfinden, wie Sie Ihren Hub für die Verwendung von TLS 1.2 konfigurieren, da diese Version die höchste Sicherheit bietet.

## <a name="securing-the-cloud"></a>Schützen der Cloud

Azure IoT Hub ermöglicht für jeden Sicherheitsschlüssel die Definition von [Richtlinien für die Zugriffssteuerung](../articles/iot-hub/iot-hub-devguide-security.md). IoT Hub verwendet den folgenden Satz von Berechtigungen, um Zugriff auf die Endpunkte jedes IoT Hubs zu gewähren. Berechtigungen beschränken den Zugriff auf einen IoT Hub basierend auf der Funktionalität.

* **RegistryRead**. Diese Berechtigung gewährt Lesezugriff auf die Identitätsregistrierung. Weitere Informationen finden Sie unter [Identitätsregistrierung](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Diese Berechtigung gewährt Lese- und Schreibzugriff auf die Identitätsregistrierung. Weitere Informationen finden Sie unter [Identitätsregistrierung](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Diese Berechtigung gewährt Zugriff auf die clouddienstseitigen Endpunkte für Kommunikation und Überwachung. Beispielsweise wird Back-End-Clouddiensten die Berechtigung erteilt, D2C-Nachrichten zu empfangen, C2D-Nachrichten zu senden und die zugehörigen Übermittlungsbestätigungen zu empfangen.

* **DeviceConnect**. Diese Berechtigung gewährt Zugriff auf die geräteseitigen Endpunkte. Beispielsweise wird die Berechtigung zum Senden von D2C-Nachrichten und das Empfangen von C2D-Nachrichten erteilt. Diese Berechtigung wird von Geräten verwendet.

Es gibt zwei Methoden zum Abrufen von **DeviceConnect**-Berechtigungen mit IoT Hub mit [Sicherheitstoken](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): Identitätsschlüssel für das Gerät oder Shared Access-Schlüssel. Darüber hinaus ist zu beachten, dass alle Funktionen, auf die auf Geräten zugegriffen werden kann, für Endpunkte mit dem Präfix `/devices/{deviceId}`absichtlich verfügbar gemacht werden.

[Dienstkomponenten können nur Sicherheitstoken mithilfe von SAS-Richtlinien generieren](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components), die die entsprechenden Berechtigungen erteilen.

Azure IoT Hub und andere Dienste, die möglicherweise Teil der Lösung sind, erlauben die Verwaltung von Benutzern mithilfe von Azure Active Directory.

Von Azure IoT Hub erfasste Daten können von einer Vielzahl von Diensten wie Azure Stream Analytics und Azure Blob Storage genutzt werden. Diese Dienste ermöglichen einen Verwaltungszugriff. Hier finden Sie weitere Informationen zu diesen Diensten und verfügbaren Optionen:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Ein skalierbarer, vollständig indizierter Datenbankdienst für semistrukturierte Daten, mit dem die Metadaten für die bereitgestellten Geräte verwaltet werden, z.B. Attribute, Konfiguration und Sicherheitseigenschaften. Azure Cosmos DB bietet eine Verarbeitung mit hoher Leistung und hohem Durchsatz, eine schemaagnostische Indizierung von Daten und eine umfassende SQL-Abfrageschnittstelle.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Datenstromverarbeitung in Echtzeit in der Cloud, mit der eine schnelle Entwicklung und Bereitstellung einer kostengünstigen Analyselösung ermöglicht wird, mit der Sie in Echtzeit basierend auf Geräte-, Sensor-, Infrastruktur- und Anwendungsdaten wichtige Einblicke erhalten. Die Daten aus diesem vollständig verwalteten Dienst können auf beliebige Volumina skaliert werden, während trotzdem ein hoher Durchsatz, eine geringe Latenz und Resilienz erzielt werden.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): Cloudplattform für leistungsstarke mobile Apps und Web-Apps, für die eine Verbindung mit cloudbasierten oder lokalen Datenquellen hergestellt werden kann. Entwickeln Sie benutzerfreundliche mobile Apps für iOS, Android und Windows. Profitieren Sie von einer Integration in Ihre SaaS- (Software as a Service) und Unternehmensanwendungen und vom standardmäßigen Zugriff auf Dutzende von cloudbasierten Diensten und Unternehmensanwendungen. Programmieren Sie in Ihrer bevorzugten Sprache und IDE, z.B. .NET, Node.js, PHP, Python oder Java, um schneller als je zuvor Web-Apps und APIs zu entwickeln.

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/): Das Logic Apps-Feature von Azure App Service dient als Hilfe bei der Integration Ihrer IoT-Lösung in Ihre vorhandenen branchenspezifischen Systeme und bei der Automatisierung von Workflowprozessen. Mit Logik-Apps können Entwickler Workflows entwerfen, die über einen Auslöser gestartet werden und dann eine Reihe von Schritten ausführen – Regeln und Aktionen, für die leistungsfähige Connectors zur Integration in Ihre Geschäftsprozesse eingesetzt werden. Logik-Apps ermöglichen standardmäßig Verbindungen mit einem umfassenden Ökosystem von SaaS-, cloudbasierten und lokalen Anwendungen.

* [Azure Blob Storage](https://azure.microsoft.com/services/storage/): zuverlässiger, kostengünstiger Cloudspeicher für die Daten, die von Ihren Geräten an die Cloud gesendet werden.

## <a name="conclusion"></a>Zusammenfassung

Dieser Artikel bietet eine Übersicht über die Details auf Implementierungsebene zum Entwerfen und Bereitstellen einer IoT-Infrastruktur mithilfe von Azure IoT. Die sicherheitsorientierte Konfiguration aller Komponenten ist entscheidend für den Schutz der gesamten IoT-Infrastruktur. Die in Azure IoT verfügbaren Entwurfsoptionen bieten ein Maß an Flexibilität und Auswahlmöglichkeiten, von denen jede allerdings bestimmte Auswirkungen auf die Sicherheit hat. Es wird empfohlen, jede dieser Optionen hinsichtlich Risiken und Kosten zu bewerten.
