---
title: Glossar mit Azure IoT Hub-Begriffen | Microsoft-Dokumentation
description: Entwicklerhandbuch – ein Glossar mit Erläuterungen einiger allgemeiner Begriffe, die in den Artikeln zu Azure IoT Hub verwendet werden.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.custom:
- amqp
- mqtt
- devx-track-azurecli
ms.openlocfilehash: 3a75b3622ebc2085dac898e2a06e09f16466cbf5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746618"
---
# <a name="glossary-of-iot-hub-terms"></a>Glossar mit IoT Hub-Begriffen

In diesem Artikel sind einige Begriffe aufgeführt, die in den Artikeln zu IoT Hub verwendet werden.

## <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol

[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) ist eines der Messagingprotokolle, die von [IoT Hub](#iot-hub) für die Kommunikation mit Geräten unterstützt werden. Weitere Informationen zu den Messagingprotokollen, die IoT Hub unterstützt, finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Automatische Geräteverwaltung

Die automatische Geräteverwaltung in Azure IoT Hub automatisiert viele der repetitiven und komplexen Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen Gerätebestands über den gesamten Lebenszyklus. Mit der automatischen Geräteverwaltung können Sie eine Gruppe von Geräten auf der Grundlage ihrer Eigenschaften als Ziel festlegen, eine gewünschte Konfiguration definieren und IoT Hub die Geräte aktualisieren lassen, sobald sie in den entsprechenden Bereich fallen.  Umfasst [automatische Gerätekonfigurationen](./iot-hub-automatic-device-management.md) und [automatische IoT Edge-Bereitstellungen](../iot-edge/how-to-deploy-at-scale.md).

## <a name="automatic-device-configuration"></a>Automatische Gerätekonfiguration

Ihr Lösungs-Back-End kann [automatische Gerätekonfigurationen](./iot-hub-automatic-device-management.md) verwenden, um einer Gruppe von [Gerätezwillingen](#device-twin) gewünschte Eigenschaften zuzuweisen und den Status anhand von Systemmetriken und benutzerdefinierten Metriken zu melden. 

## <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle

Die [klassische Azure-Befehlszeilenschnittstelle](/cli/azure/install-classic-cli) ist ein plattformübergreifendes Open-Source-Befehlstool auf Shell-Basis und ermöglicht das Erstellen und Verwalten von Ressourcen in Microsoft Azure. Diese Version der Befehlszeilenschnittstelle sollte nur für klassische Bereitstellungen verwendet werden.

## <a name="azure-cli"></a>Azure CLI

Die [Azure CLI](/cli/azure/install-az-cli2) ist ein plattformübergreifendes Open-Source-Befehlstool auf Shell-Basis und ermöglicht das Erstellen und Verwalten von Ressourcen in Microsoft Azure.

## <a name="azure-iot-device-sdks"></a>Azure IoT-Geräte-SDKs

Es sind _Geräte-SDKs_ für mehrere Sprachen verfügbar, mit denen Sie [Geräte-Apps](#device-app) erstellen können, die mit IoT Hub interagieren. In den IoT Hub-Tutorials wird veranschaulicht, wie Sie diese Geräte-SDKs verwenden. Den Quellcode und weitere Informationen zu den Geräte-SDKs finden Sie in [diesem GitHub-Repository](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-explorer"></a>Azure IoT-Explorer

Mit dem [Azure IoT-Explorer](https://github.com/Azure/azure-iot-explorer) können Sie die Telemetriedaten anzeigen, die das Gerät sendet, Geräteeigenschaften einrichten und Befehle aufrufen. Außerdem können Sie den Explorer für die Interaktion mit Ihren Geräten, zum Testen Ihrer Geräte und zur Verwaltung von Plug & Play-Geräten verwenden.

## <a name="azure-iot-service-sdks"></a>Azure IoT-Dienst-SDKs

Es sind _Dienst-SDKs_ für mehrere Sprachen verfügbar, mit denen Sie [Back-End-Apps](#back-end-app) erstellen können, die mit IoT Hub interagieren. In den IoT Hub-Tutorials wird veranschaulicht, wie Sie diese Dienst-SDKs verwenden. Den Quellcode und weitere Informationen zu den Dienst-SDKs finden Sie in [diesem GitHub-Repository](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Azure IoT-Tools

Die [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) sind eine plattformübergreifende, Open-Source-Erweiterung für Visual Studio Code, die Sie bei der Verwaltung von Azure IoT Hub und Geräten in VS Code unterstützt. Mit Azure IoT-Tools können IoT-Entwicklern ganz einfach IoT-Projekte in Visual Studio Code entwickeln.

## <a name="azure-portal"></a>Azure-Portal

Das [Microsoft Azure-Portal](https://portal.azure.com) ist eine zentrale Anlaufstelle zum Bereitstellen und Verwalten von Azure-Ressourcen. Der Inhalt ist darin in _Blättern_ organisiert.

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/) ist eine Sammlung von Cmdlets, die Sie nutzen können, um Azure mit Windows PowerShell zu verwalten. Sie können diese Cmdlets verwenden, um durch die Azure-Plattform bereitgestellte Lösungen und Dienste zu erstellen, zu testen, bereitzustellen und zu verwalten.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Mit dem [Azure Resource Manager](../azure-resource-manager/management/overview.md) können Sie als Gruppe mit den Ressourcen in Ihrer Lösung arbeiten. Sie können die Ressourcen für Ihre Lösung in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen.

## <a name="azure-service-bus"></a>Azure-Servicebus

[Service Bus](../service-bus-messaging/index.yml) bietet cloudfähige Kommunikation mit Messagingfunktionen für Unternehmen und Relaykommunikation, um Sie bei der Verbindung Ihrer lokalen Lösungen mit der Cloud zu unterstützen. In einigen IoT Hub-Tutorials werden Service Bus-[Warteschlangen](../service-bus-messaging/service-bus-messaging-overview.md) verwendet.

## <a name="azure-storage"></a>Azure Storage

[Azure Storage](../storage/common/storage-introduction.md) ist eine Cloudspeicherlösung. Sie umfasst den Blob Storage-Dienst, den Sie zum Speichern von nicht strukturierten Objektdaten verwenden können. In einigen IoT Hub-Tutorials werden Blobspeicher verwendet.

## <a name="back-end-app"></a>Back-End-App

Im Kontext von [IoT Hub](#iot-hub) ist eine Back-End-App eine App, mit der eine Verbindung mit einem der dienstseitigen Endpunkte von IoT Hub hergestellt wird. Mit einer Back-End-App werden beispielsweise [Gerät-zu-Cloud](#device-to-cloud)-Nachrichten (D2C-Nachrichten) abgerufen, oder sie wird zum Verwalten der [Identitätsregistrierung](#identity-registry) eingesetzt. Eine Back-End-App wird normalerweise in der Cloud ausgeführt, aber in vielen Tutorials handelt es sich bei den Back-End-Apps um Konsolen-Apps, die auf dem lokalen Entwicklungscomputer ausgeführt werden.

## <a name="built-in-endpoints"></a>Integrierte Endpunkte

Jeder IoT Hub umfasst einen mit Event Hub kompatiblen [Endpunkt](iot-hub-devguide-endpoints.md). Sie können einen beliebigen für Event Hubs geeigneten Mechanismus zum Lesen von D2C-Nachrichten von diesem Endpunkt verwenden.

## <a name="cloud-gateway"></a>Cloudgateway

Mit einem Cloudgateway können Verbindungen für Geräte hergestellt werden, für die eine direkte Verbindung mit [IoT Hub](#iot-hub) nicht möglich ist. Ein Cloudgateway wird in der Cloud gehostet, während ein [Bereichsgateway](#field-gateway) lokal auf Ihren Geräten ausgeführt wird. Ein typischer Anwendungsfall für ein Cloudgateway ist die Implementierung der Protokollübersetzung für Ihre Geräte.

## <a name="cloud-to-device"></a>Cloud-zu-Gerät (C2D)

Bezieht sich auf Nachrichten, die von IoT Hub an ein verbundenes Gerät gesendet werden. Diese Nachrichten sind häufig Befehle, mit denen das Gerät angewiesen wird, eine Aktion auszuführen. Weitere Informationen finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfiguration

Im Kontext der [automatischen Gerätekonfiguration](./iot-hub-automatic-device-management.md) definiert eine Konfiguration in IoT Hub die gewünschte Konfiguration für eine Gruppe von Gerätezwillingen und bietet einen Satz von Metriken zum Melden von Status und Fortschritt.

## <a name="connection-string"></a>Verbindungszeichenfolge

Sie können Verbindungszeichenfolgen in Ihrem App-Code verwenden, um die Informationen zu kapseln, die zum Herstellen der Verbindung mit einem Endpunkt erforderlich sind. Eine Verbindungszeichenfolge enthält üblicherweise die Adresse des Endpunkts und Sicherheitsinformationen, aber das Format der Verbindungszeichenfolge variiert für die verschiedenen Dienste. Im Zusammenhang mit dem IoT Hub-Dienst gibt es zwei Arten von Verbindungszeichenfolgen:

- *Geräteverbindungszeichenfolgen:* Ermöglichen es Geräten, eine Verbindung mit den geräteseitigen Endpunkten einer IoT Hub-Instanz herzustellen.

- *IoT Hub-Verbindungszeichenfolgen:* Ermöglichen es Back-End-Apps, eine Verbindung mit den dienstseitigen Endpunkten einer IoT Hub-Instanz herzustellen.

## <a name="custom-endpoints"></a>Benutzerdefinierte Endpunkte

Sie können benutzerdefinierte [Endpunkte](iot-hub-devguide-endpoints.md) auf einen IoT Hub erstellen, um von einer [Routingregel](#routing-rules) versendete Nachrichten weiterzuleiten. Benutzerdefinierte Endpunkte stellen eine direkte Verbindung mit einem Event Hub, einer Service Bus-Warteschlange oder einem Service Bus-Thema her.

## <a name="custom-gateway"></a>Benutzerdefiniertes Gateway

Mit einem Gateway können Verbindungen für Geräte hergestellt werden, für die eine direkte Verbindung mit [IoT Hub](#iot-hub) nicht möglich ist. Sie können Azure IoT Edge zum Erstellen von benutzerdefinierten Gateways verwenden, bei denen benutzerdefinierte Logik implementiert wird, um Nachrichten und benutzerdefinierte Protokollkonvertierungen zu verarbeiten und andere Edge-Verarbeitungsvorgänge durchzuführen.

## <a name="data-point-message"></a>Datenpunktnachricht

Eine Datenpunktnachricht ist eine [D2C-Nachricht](#device-to-cloud), die [Telemetriedaten](#telemetry) wie Windgeschwindigkeit oder Temperatur enthält.

## <a name="desired-configuration"></a>Gewünschte Konfiguration

Im Kontext eines [Gerätezwillings](iot-hub-devguide-device-twins.md) bezieht sich die gewünschte Konfiguration auf den vollständigen Satz von Eigenschaften und Metadaten im Gerätezwilling, die mit dem Gerät synchronisiert werden sollen.

## <a name="desired-properties"></a>Gewünschte Eigenschaften

Im Kontext eines [Gerätezwillings](iot-hub-devguide-device-twins.md) handelt es sich bei gewünschten Eigenschaften um einen Unterabschnitt, der zusammen mit [gemeldeten Eigenschaften](#reported-properties) verwendet wird, um die Gerätekonfiguration oder den Gerätezustand zu synchronisieren. Gewünschte Eigenschaften können nur von einer [Back-End-App](#back-end-app) festgelegt werden und werden von der [Geräte-App](#device-app) erkannt.

## <a name="device-to-cloud"></a>Gerät-zu-Cloud (D2C)

Bezieht sich auf Nachrichten, die von einem verbundenen Gerät an [IoT Hub](#iot-hub) gesendet werden. Bei diesen Nachrichten kann es sich um [datenpunktbezogene](#data-point-message) oder um [interaktive](#interactive-message) Nachrichten handeln. Weitere Informationen finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Sicherungsmedium

Im Kontext von IoT ist ein Gerät in der Regel eine kleines, eigenständiges Datenverarbeitungsgerät, das Daten sammeln oder andere Geräte steuern kann. Ein Gerät kann beispielsweise ein Umweltüberwachungsgerät oder ein Regler für Bewässerungs- und Lüftungsanlagen in einem Gewächshaus sein. Der [Gerätekatalog](https://catalog.azureiotsolutions.com/) enthält eine Liste mit Hardwaregeräten, die für die Verwendung mit [IoT Hub](#iot-hub) zertifiziert sind.

## <a name="device-app"></a>Geräte-App

Eine Geräte-App wird auf Ihrem [Gerät](#device) ausgeführt und führt die Kommunikation mit [IoT Hub](#iot-hub) durch. Normalerweise nutzen Sie eines der [Azure IoT-Geräte-SDKs](#azure-iot-device-sdks), wenn Sie eine Geräte-App implementieren. In vielen IoT-Tutorials verwenden Sie der Einfachheit halber ein [simuliertes Gerät](#simulated-device).

## <a name="device-condition"></a>Gerätezustand

Bezieht sich auf Informationen zum Gerätezustand, z.B. die derzeit verwendete Verbindungsmethode, die von einer [Geräte-App](#device-app) gemeldet wird. [Geräte-Apps](#device-app) können auch ihre Funktionen melden. Sie können Informationen zum Zustand und zu den Funktionen abfragen, indem Sie Gerätezwillinge verwenden.

## <a name="device-data"></a>Gerätedaten

Gerätedaten sind Daten, die pro Gerät in der IoT Hub-[Identitätsregistrierung](#identity-registry) gespeichert werden. Es ist möglich, diese Daten zu importieren und zu exportieren.

## <a name="device-explorer"></a>Device Explorer

Der Device Explorer wurde durch den [Azure IoT-Explorer](https://github.com/Azure/azure-iot-explorer) ersetzt. Mit diesem Explorer können Sie die Telemetriedaten anzeigen, die das Gerät sendet, sowie Geräteeigenschaften einrichten und Befehle aufrufen. Außerdem können Sie den Explorer für die Interaktion mit Ihren Geräten, zum Testen Ihrer Geräte und zur Verwaltung von Plug & Play-Geräten verwenden.

## <a name="device-identity"></a>Geräteidentität

Die Geräteidentität ist der eindeutige Bezeichner, der jedem Gerät zugewiesen wird, das in der [Identitätsregistrierung](#identity-registry) registriert ist.

## <a name="device-management"></a>Geräteverwaltung

Die Geräteverwaltung umfasst den vollständigen Lebenszyklus, der der Verwaltung der Geräte in Ihrer IoT-Lösung zugeordnet ist, z.B. Planen, Bereitstellen, Konfigurieren, Überwachen und Ausmustern.

## <a name="device-management-patterns"></a>Geräteverwaltungsmuster

[IoT Hub](#iot-hub) ermöglicht allgemeine Schritte zur Geräteverwaltung, z.B. Neustart, Zurücksetzung auf die Werkseinstellungen und Durchführung von Firmwareupdates auf Ihren Geräten.

## <a name="device-rest-api"></a>Geräte-REST-API

Sie können die [Geräte-REST-API](/rest/api/iothub/device) auf einem Gerät nutzen, um D2C-Nachrichten an IoT Hub zu senden und [C2D-Nachrichten](#cloud-to-device) von IoT Hub zu empfangen. Es ist in der Regel ratsam, eines der allgemeinen [Geräte-SDKs](#azure-iot-device-sdks) zu nutzen, wie in den IoT Hub-Tutorials veranschaulicht.

## <a name="device-provisioning"></a>Gerätebereitstellung

Die Gerätebereitstellung ist der Prozess des Hinzufügens der ersten [Gerätedaten](#device-data) zu den Speichern in Ihrer Lösung. Damit ein neues Gerät eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie der IoT Hub-[Identitätsregistrierung](#identity-registry) eine neue Geräte-ID und Schlüssel hinzufügen. Im Rahmen des Bereitstellungsprozesses müssen Sie unter Umständen gerätespezifische Daten in anderen Lösungsspeichern initialisieren.

## <a name="device-twin"></a>Gerätezwilling

Ein [Gerätezwilling](iot-hub-devguide-device-twins.md) ist ein JSON-Dokument, mit dem Informationen zum Gerätezustand gespeichert werden, z.B. Metadaten, Konfigurationen und Bedingungen. [IoT Hub](#iot-hub) speichert einen Gerätezwilling für jedes Gerät, das Sie für IoT Hub bereitstellen. Mit Gerätezwillingen können Sie [Gerätebedingungen](#device-condition) und Konfigurationen zwischen dem Gerät und dem Lösungs-Back-End synchronisieren. Sie können Gerätezwillinge abfragen, um bestimmte Geräte zu ermitteln und den Status von Vorgängen mit langer Ausführungsdauer abzufragen.

## <a name="direct-method"></a>Direkte Methode

Eine [direkte Methode](iot-hub-devguide-direct-methods.md) ist eine Möglichkeit zum Auslösen einer auf einem Gerät auszuführenden Methode, indem eine API auf Ihrem IoT Hub aufgerufen wird.

## <a name="endpoint"></a>Endpunkt

Ein IoT Hub macht mehrere [Endpunkte](iot-hub-devguide-endpoints.md) verfügbar, mit deren Hilfe Ihre Apps eine Verbindung mit dem IoT Hub herstellen können. Es gibt geräteseitige Endpunkte, die Geräten das Durchführen von Vorgängen ermöglichen, z.B. das Senden von [D2C](#device-to-cloud)-Nachrichten und das Empfangen von [C2D](#cloud-to-device)-Nachrichten. Es gibt dienstseitige Verwaltungsendpunkte, die [Back-End-Apps](#back-end-app) das Durchführen von Vorgängen ermöglichen, z.B. die Verwaltung der [Geräteidentität](#device-identity) und von Gerätezwillingen. Es gibt dienstseitige [integrierte Endpunkte](#built-in-endpoints) zum Lesen von D2C-Nachrichten. Sie können [benutzerdefinierte Endpunkte](#custom-endpoints) erstellen, um von einer [Routingregel](#routing-rules) versendete D2C-Nachrichten zu empfangen.

## <a name="event-hubs-service"></a>Event Hubs-Dienst

[Event Hubs](../event-hubs/event-hubs-about.md) ist ein hoch skalierbarer Datenerfassungsdienst, mit dem Millionen von Ereignissen pro Sekunde erfasst werden können. Mit diesem Dienst können Sie umfangreiche Datenmengen verarbeiten und analysieren, die von Ihren verbundenen Geräten und Anwendungen generiert werden. Einen Vergleich mit dem IoT Hub-Dienst finden Sie unter [Vergleich zwischen Azure IoT Hub und Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Event Hub-kompatibler Endpunkt

Zum Lesen von [D2C](#device-to-cloud)-Nachrichten, die an Ihren IoT Hub gesendet werden, können Sie sich mit einem Endpunkt auf Ihrem Hub verbinden und eine beliebige Event Hub-kompatible Methode zum Lesen dieser Nachrichten nutzen. Event Hub-kompatible Methoden sind beispielsweise das Verwenden von [Event Hubs SDKs](../event-hubs/event-hubs-programming-guide.md) und [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Bereichsgateway

Ein Bereichsgateway ermöglicht Verbindungen für Geräte, die keine direkte Verbindung mit dem [IoT Hub](#iot-hub) herstellen können. Es wird meist lokal mit Ihren Geräten bereitgestellt. Weitere Informationen finden Sie unter [Was ist Azure IoT Hub?](about-iot-hub.md).

## <a name="free-account"></a>Kostenloses Konto

Sie können ein [kostenloses Azure-Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, um die IoT Hub-Tutorials durchführen und mit dem IoT Hub-Dienst (und anderen Azure-Diensten) experimentieren zu können.

## <a name="gateway"></a>Gateway

Mit einem Gateway können Verbindungen für Geräte hergestellt werden, für die eine direkte Verbindung mit [IoT Hub](#iot-hub) nicht möglich ist. Siehe auch [Bereichsgateway](#field-gateway), [Cloudgateway](#cloud-gateway)und [Benutzerdefiniertes Gateway](#custom-gateway).

## <a name="identity-registry"></a>Identitätsregistrierung

Die [Identitätsregistrierung](iot-hub-devguide-identity-registry.md) ist die integrierte Komponente in IoT Hub, in der Informationen zu den einzelnen Geräten gespeichert werden, die sich mit einem IoT Hub verbinden dürfen.

## <a name="interactive-message"></a>Interaktive Nachricht

Eine interaktive Nachricht ist eine [C2D](#cloud-to-device)-Nachricht, die eine sofortige Aktion im Lösungs-Back-End auslöst. Beispielsweise kann ein Gerät einen Alarm zu einem Fehler senden, der automatisch in einem CRM-System protokolliert werden soll.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub

IoT Hub ist ein vollständig verwalteter Azure-Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure IoT Hub?](about-iot-hub.md). Mit Ihrem [Azure-Abonnement](#subscription) können Sie IoT-Hubs für die Verarbeitung Ihrer IoT-Messagingworkloads erstellen.

## <a name="iot-hub-metrics"></a>IoT Hub-Metriken

Mit [IoT Hub-Metriken](monitor-iot-hub-reference.md#metrics) erhalten Sie Daten zum Zustand von IoT Hubs in Ihrem [Azure-Abonnement](#subscription). Auf der Grundlage von IoT Hub-Metriken können Sie die allgemeine Integrität des Diensts und der mit ihm verbundenen Geräte beurteilen. Anhand von IoT Hub-Metriken können Sie sich über den Status Ihrer IoT Hub-Instanz informieren und den Ursachen von Fehlern nachgehen, ohne sich dafür an den Azure-Support wenden zu müssen. Weitere Informationen finden Sie unter [Überwachen von Azure IoT Hub](monitor-iot-hub.md).

## <a name="iot-hub-query-language"></a>IoT Hub-Abfragesprache

Die [IoT Hub-Abfragesprache](iot-hub-devguide-query-language.md) ist eine SQL-ähnliche Sprache, mit der Sie Ihre [Aufträge](#job) und Gerätezwillinge abfragen können.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub-Ressourcen-REST-API

Sie können die [IoT Hub-Ressourcen-REST-API](/rest/api/iothub/iothubresource) verwenden, um die IoT Hub-Instanzen in Ihrem [Azure-Abonnement](#subscription) zu verwalten, in dem Vorgänge wie das Erstellen, Aktualisieren und Löschen von Hubs durchgeführt werden.

## <a name="iot-solution-accelerators"></a>IoT Solution Accelerators

In Azure IoT-Solution Accelerators werden mehrere Azure-Dienste zu Lösungen zusammengepackt. Diese Lösungen ermöglichen eine schnelle End-to-End-Implementierung gängiger IoT-Szenarien. Weitere Informationen finden Sie unter [Vergleich von Azure IoT-Optionen](../iot-accelerators/about-iot-accelerators.md).

## <a name="the-iot-extension-for-azure-cli"></a>Die IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle 

Die [IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension) ist ein plattformübergreifendes Befehlszeilentool. Mit dem Tool können Sie Ihre Geräte in der [Identitätsregistrierung](#identity-registry) verwalten, Nachrichten und Dateien für Ihre Geräte senden und empfangen und die IoT Hub-Vorgänge überwachen.

## <a name="job"></a>Auftrag

Ihr Lösungs-Back-End kann mithilfe von [Aufträgen](iot-hub-devguide-jobs.md) Aktivitäten für eine Gruppe von Geräten planen und nachverfolgen, die bei Ihrem IoT Hub registriert sind. Aktivitäten sind beispielsweise das Aktualisieren der [gewünschten Eigenschaften](#desired-properties) von Gerätezwillingen und ihrer [Tags](#tags) sowie das Aufrufen [direkter Methoden](#direct-method). [IoT Hub](#iot-hub) nutzt zudem Aufträge zum [Importieren und Exportieren](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) aus der [Identitätsregistrierung](#identity-registry).

## <a name="modules"></a>Module

Auf der Geräteseite können Sie mithilfe der IoT Hub-Geräte-SDKs [Module](iot-hub-devguide-module-twins.md) erstellen, die jeweils eine unabhängige Verbindung mit IoT Hub herstellen. Durch diese Funktionalität können Sie separate Namespaces für unterschiedliche Komponenten auf Ihrem Gerät verwenden.

Modulidentität und Modulzwilling bieten die gleichen Funktionen wie [Geräteidentität](#device-identity) und [Gerätezwilling](#device-twin), aber mit einer höheren Granularität. Dank dieser höheren Granularität können geeignete Geräte (etwa betriebssystembasierte Geräte oder Firmwaregeräte, die mehrere Komponenten verwalten) Konfigurationen und Zustände für die einzelnen Komponenten isolieren.

## <a name="module-identity"></a>Modulidentität

Bei der Modulidentität handelt es sich um den eindeutigen Bezeichner, der jedem Modul zugeordnet ist, das einem Gerät angehört. Die Modulidentität wird auch in der [Identitätsregistrierung](#identity-registry) registriert.

## <a name="module-twin"></a>Modulzwilling

Modulzwillinge ähneln Gerätezwillingen und sind JSON-Dokumente, in denen Modulstatusinformationen wie Metadaten, Konfigurationen und Zustände gespeichert werden. IoT Hub speichert einen Modulzwilling für jede Modulidentität, die Sie unter einer Geräteidentität in Ihrer IoT Hub-Instanz bereitstellen. Mit Modulzwillingen können Sie Modulbedingungen und Konfigurationen zwischen dem Modul und dem Lösungs-Back-End synchronisieren. Sie können Modulzwillinge abfragen, um bestimmte Module zu ermitteln und den Status von Vorgängen mit langer Ausführungsdauer abzufragen.

## <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) ist eines der Messagingprotokolle, die von [IoT Hub](#iot-hub) für die Kommunikation mit Geräten unterstützt werden. Weitere Informationen zu den Messagingprotokollen, die IoT Hub unterstützt, finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Vorgangsüberwachung

Die IoT Hub-[Vorgangsüberwachung](iot-hub-operations-monitoring.md) ermöglicht Ihnen das Überwachen des Status von Vorgängen auf Ihrem IoT Hub in Echtzeit. [IoT Hub](#iot-hub) verfolgt Ereignisse in verschiedenen Vorgangskategorien nach. Sie können festlegen, dass Ereignisse aus einer oder mehreren Kategorien zur Verarbeitung an einen IoT Hub-Endpunkt gesendet werden. Sie können die Daten auf Fehler überwachen oder eine komplexere auf Datenmustern basierende Verarbeitung einrichten.

## <a name="physical-device"></a>Physisches Gerät

Ein physisches Gerät ist ein echtes Gerät, z.B. ein Raspberry Pi mit einer Verbindung mit einem IoT Hub. Der Einfachheit halber werden in vielen IoT Hub-Tutorials [simulierte Geräte](#simulated-device) verwendet, damit Sie Beispiele auf Ihrem lokalen Computer ausführen können.

## <a name="primary-and-secondary-keys"></a>Primär- und Sekundärschlüssel

Wenn Sie einen geräteseitigen oder dienstseitigen Endpunkt mit einem IoT Hub verbinden, enthält die [Verbindungszeichenfolge](#connection-string) einen Schlüssel zum Gewähren des Zugriffs. Wenn Sie der [Identitätsregistrierung](#identity-registry) ein Gerät hinzufügen oder dem Hub eine [SAS-Richtlinie](#shared-access-policy) hinzufügen, generiert der Dienst einen Primär- und einen Sekundärschlüssel. Dank der zwei Schlüssel können Sie ein Rollover von einem Schlüssel zum anderen Schlüssel durchführen, wenn Sie einen Schlüssel aktualisieren, ohne den Zugriff auf den IoT Hub zu verlieren.

## <a name="protocol-gateway"></a>Protokollgateway

Ein Protokollgateway wird der Regel in der Cloud bereitgestellt und bietet Protokollübersetzungsdienste für Geräte, die sich mit [IoT Hub](#iot-hub) verbinden. Weitere Informationen finden Sie unter [Was ist Azure IoT Hub?](about-iot-hub.md).

## <a name="quotas-and-throttling"></a>Kontingente und Drosselung

Es gibt verschiedene [Kontingente](iot-hub-devguide-quotas-throttling.md), die für Ihre Verwendung von [IoT Hub](#iot-hub) gelten. Viele Kontingente variieren je nach IoT Hub-Ebene. [IoT Hub](#iot-hub) wendet außerdem [Drosselungen](iot-hub-devguide-quotas-throttling.md) auf Ihre Nutzung des Diensts zur Laufzeit an.

## <a name="reported-configuration"></a>Gemeldete Konfiguration

Im Kontext eines [Gerätezwillings](iot-hub-devguide-device-twins.md) bezieht sich die gemeldete Konfiguration auf den vollständigen Satz von Eigenschaften und Metadaten im Gerätezwilling, die an das Lösungs-Back-End gemeldet werden sollen.

## <a name="reported-properties"></a>Gemeldete Eigenschaften

Im Kontext eines [Gerätezwillings](iot-hub-devguide-device-twins.md) handelt es sich bei gemeldeten Eigenschaften um einen Unterabschnitt, der zusammen mit [gewünschten Eigenschaften](#desired-properties) verwendet wird, um die Gerätekonfiguration oder den Gerätezustand zu synchronisieren. Gemeldete Eigenschaften können nur von der [Geräte-App](#device-app) festgelegt werden. Das Lesen und Abfragen ist per [Back-End-App](#back-end-app) möglich.

## <a name="resource-group"></a>Resource group

[Azure Resource Manager](#azure-resource-manager) nutzt Ressourcengruppen, um zusammengehörige Ressourcen zu gruppieren. Sie können eine Ressourcengruppe verwenden, um Vorgänge für alle Ressourcen der Gruppe gleichzeitig durchzuführen.

## <a name="retry-policy"></a>Wiederholungsrichtlinie

Sie verwenden eine Wiederholungsrichtlinie, um [vorübergehende Fehler](/azure/architecture/best-practices/transient-faults) zu verarbeiten, wenn Sie eine Verbindung mit einem Clouddienst herstellen.

## <a name="routing-rules"></a>Routingregeln

Sie konfigurieren [Routingregeln](iot-hub-devguide-messages-read-custom.md) in Ihrem IoT Hub, um D2C-Nachrichten an einen [integrierten Endpunkt](#built-in-endpoints) oder [benutzerdefinierte Endpunkte](#custom-endpoints) für die Verarbeitung durch Ihr Lösungs-Back-End weiterzuleiten.

## <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN ist ein Protokoll, das vom AMQP-Protokoll zum Übertragen von Sicherheitstoken verwendet wird.

## <a name="service-rest-api"></a>Dienst-REST-API

Sie können die [Dienst-REST-API](/rest/api/iothub/service/configuration) des Lösungs-Back-Ends zur Verwaltung Ihrer Geräte verwenden. Mithilfe der API können Sie Eigenschaften von [Gerätezwillingen](#device-twin) abrufen und aktualisieren, [direkte Methoden](#direct-method) aufrufen und [Aufträge](#job) planen. Es ist in der Regel ratsam, ein allgemeines [Dienst-SDK](#azure-iot-service-sdks) zu nutzen, wie in den IoT Hub-Tutorials veranschaulicht.

## <a name="shared-access-signature"></a>Shared Access Signature (SAS)

Bei Shared Access Signatures handelt es sich um einen Authentifizierungsmechanismus, der auf sicheren Hashes (SHA-256) oder URIs basiert. Die SAS-Authentifizierung besteht aus zwei Komponenten: einer _Richtlinie für den gemeinsamen Zugriff_ und einer _Shared Access Signature_ (häufig auch als „Token“ bezeichnet). Ein Gerät verwendet eine SAS für die Authentifizierung mit IoT Hub. [Back-End-Apps](#back-end-app) nutzen ebenfalls eine SAS für die Authentifizierung mit den dienstseitigen Endpunkten von IoT Hub. Normalerweise binden Sie das SAS-Token in die [Verbindungszeichenfolge](#connection-string) ein, die von einer App verwendet wird, um eine Verbindung mit IoT Hub herzustellen.

## <a name="shared-access-policy"></a>SAS-Richtlinie

Mit einer SAS-Richtlinie werden die Berechtigungen definiert, die allen Personen mit einem [Primär- oder Sekundärschlüssel](#primary-and-secondary-keys), der dieser Richtlinie zugeordnet ist, gewährt werden. Sie können die SAS-Richtlinien und Schlüssel für den Hub im [Portal](#azure-portal) verwalten.

## <a name="simulated-device"></a>Simuliertes Gerät

Der Einfachheit halber werden in vielen IoT Hub-Tutorials simulierte Geräte verwendet, damit Sie Beispiele auf Ihrem lokalen Computer ausführen können. Ein [physisches Gerät](#physical-device) ist dagegen ein echtes Gerät, z.B. ein Raspberry Pi mit einer Verbindung mit IoT Hub.

## <a name="solution"></a>Lösung
Eine _Lösung_ kann eine Visual Studio-Lösung (Projektmappe) sein, die mindestens ein Projekt enthält. Eine _Lösung_ kann auch eine IoT-Lösung sein, die Elemente wie Geräte, [Geräte-Apps](#device-app), einen IoT Hub, andere Azure-Dienste und [Back-End-Apps](#back-end-app) enthält.

## <a name="subscription"></a>Subscription

Dies ist ein Azure-Abonnement, für das die Abrechnung durchgeführt wird. Jede Azure-Ressource, die Sie erstellen, und jeder Azure-Dienst, den Sie verwenden, ist einem einzelnen Abonnement zugeordnet. Viele Kontingente gelten auch für die Ebene eines Abonnements.

## <a name="system-properties"></a>Systemeigenschaften

Im Kontext eines [Gerätezwillings](iot-hub-devguide-device-twins.md) sind Systemeigenschaften schreibgeschützt und enthalten Informationen zur Gerätenutzung, z.B. den Zeitpunkt der letzten Aktivität und den Verbindungsstatus.

## <a name="tags"></a>`Tags`

Im Kontext eines [Gerätezwillings](iot-hub-devguide-device-twins.md) sind Tags Gerätemetadaten, die vom Lösungs-Back-End in Form eines JSON-Dokuments gespeichert und abgerufen werden. Tags sind für Apps auf einem Gerät nicht sichtbar.

## <a name="telemetry"></a>Telemetrie

Mit Geräten werden Telemetriedaten erfasst, z.B. Windgeschwindigkeit oder Temperatur, und es werden Datenpunktnachrichten verwendet, um Telemetriedaten an IoT Hub zu senden.

## <a name="token-service"></a>Tokendienst

Sie können einen Tokendienst verwenden, um einen Authentifizierungsmechanismus für Ihre Geräte zu implementieren. Er verwendet eine [SAS-Richtlinie](#shared-access-policy) von IoT Hub mit **DeviceConnect** -Berechtigungen, um Token mit *Gerätebereich* zu erstellen. Mit diesen Token kann ein Gerät eine Verbindung mit Ihrem IoT Hub herstellen. Ein Gerät verwendet einen benutzerdefinierten Authentifizierungsmechanismus für die Authentifizierung mit dem Tokendienst. Sofern die Authentifizierung des Geräts erfolgreich ist, stellt der Tokendienst ein SAS-Token für das Gerät bereit, mit dem auf IoT Hub zugegriffen werden kann.

## <a name="twin-queries"></a>Zwillingsabfragen

Für [Geräte- und Modulzwillingsabfragen](iot-hub-devguide-query-language.md) wird die SQL-ähnliche IoT Hub-Abfragesprache verwendet, um Informationen von ihren Geräte- oder Modulzwillingen abzufragen. Sie können die gleiche IoT Hub-Abfragesprache verwenden, um Informationen zu einem [Auftrag](#job) abzurufen, der in IoT Hub ausgeführt wird.

## <a name="twin-synchronization"></a>Zwillingssynchronisierung

Bei der Zwillingssynchronisierung werden die [gewünschten Eigenschaften](#desired-properties) in Ihren Geräte- oder Modulzwillingen verwendet, um die Geräte oder Module zu konfigurieren und [gemeldete Eigenschaften](#reported-properties) von Ihren Geräten oder Modulen abzurufen und im entsprechenden Zwilling zu speichern.

## <a name="x509-client-certificate"></a>X.509-Clientzertifikat

Ein Gerät kann ein X.509-Zertifikat für die Authentifizierung beim [IoT Hub](#iot-hub) verwenden. Der Einsatz eines X.509-Zertifikats ist eine Alternative zur Verwendung eines [SAS-Tokens](#shared-access-signature).
