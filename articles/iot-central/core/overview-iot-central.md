---
title: Was ist Azure IoT Central? | Microsoft-Dokumentation
description: Azure IoT Central ist eine IoT-Anwendungsplattform, die das Erstellen von IoT-Lösungen vereinfacht und den Aufwand und die Kosten für IoT-Verwaltungsvorgänge und -Entwicklung senkt. Dieser Artikel enthält eine Übersicht über die Features von Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: b5f4b6d42d67b69bd8b6be1ada6f5651d0cb8675
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029837"
---
# <a name="what-is-azure-iot-central"></a>Was ist Azure IoT Central?

IoT Central ist eine IoT-Anwendungsplattform, die Aufwand und Kosten für die Entwicklung, Verwaltung und Wartung von IoT-Lösungen auf Unternehmensniveau verringert. Die Entscheidung zur Entwicklung mit IoT Central gibt Ihnen die Möglichkeit, Zeit, Geld und Energie auf die Transformation Ihres Unternehmens mit IoT-Daten zu konzentrieren, anstatt sich lediglich mit der Wartung und Aktualisierung einer komplexen und sich ständig weiterentwickelnden IoT-Infrastruktur zu beschäftigen.

Mit der Webbenutzeroberfläche können Sie Gerätebedingungen überwachen, Regeln erstellen und Millionen von Geräten und zugehöriger Daten über ihren gesamten Lebenszyklus hinweg verwalten. Darüber hinaus können Sie auf Geräteerkenntnisse reagieren, indem Sie die IoT-Intelligenz auf Branchenanwendungen erweitern.

In diesem Artikel werden folgende Punkte für IoT Central behandelt:

- Typische Personas eines Projekts
- Erstellung einer Anwendung
- Herstellung einer Verbindung zwischen Ihren Geräten und Ihrer Anwendung
- Verwaltung Ihrer Anwendung
- Azure IoT Edge-Funktionen in IoT Central
- Vorgehensweise zum Verbinden Ihrer auf der Azure IoT Edge-Runtime basierenden Geräte mit Ihrer Anwendung

## <a name="personas"></a>Personas

In der Dokumentation von IoT Central werden vier Personas verwendet, die mit einer IoT Central-Anwendung interagieren:

- Ein _Lösungsentwickler_ ist zuständig für das [Erstellen einer Anwendung](quick-deploy-iot-central.md), das [Konfigurieren von Regeln und Aktionen](quick-configure-rules.md) und das [Definieren von Integrationen mit anderen Diensten](howto-export-data.md) sowie für die weitere Anpassung der Anwendung für Operatoren und Geräteentwickler.
- Ein _Operator_ [verwaltet die Geräte](howto-manage-devices.md), die mit der Anwendung verbunden sind.
- Ein _Administrator_ kümmert sich um administrative Aufgaben – etwa um die [Verwaltung von Benutzern und Rollen](howto-administer.md) innerhalb der Anwendung.
- Ein _Geräteentwickler_ [erstellt den Code, der auf einem mit der Anwendung verbundenen Gerät](concepts-telemetry-properties-commands.md) oder [IoT Edge-Modul ausgeführt wird](concepts-iot-edge.md).

## <a name="create-your-iot-central-application"></a>Erstellen der IoT Central-Anwendung

Als Lösungsentwickler verwenden Sie IoT Central, um eine benutzerdefinierte, in der Cloud gehostete IoT-Lösung für Ihre Organisation zu erstellen. Eine benutzerdefinierte IoT-Lösung umfasst in der Regel Folgendes:

- Eine cloudbasierte Anwendung, die Telemetriedaten von Ihren Geräten empfängt und die Verwaltung dieser Geräte ermöglicht
- Mehrere Geräte, auf denen benutzerdefinierter Code ausgeführt wird und die mit Ihrer cloudbasierten Anwendung verbunden sind

Sie können eine neue IoT Central-Anwendung schnell bereitstellen und anschließend in Ihrem Browser an Ihre spezifischen Anforderungen anpassen. Sie können mit einer generischen _Anwendungsvorlage_ oder mit einer der branchenspezifischen Anwendungsvorlagen für [Einzelhandel](../retail/overview-iot-central-retail.md), [Energiebranche](../energy/overview-iot-central-energy.md), [Behörden](../government/overview-iot-central-government.md) oder [Gesundheitswesen](../healthcare/overview-iot-central-healthcare.md) beginnen.

Als Lösungsentwickler können Sie mithilfe der webbasierten Tools eine _Gerätevorlage_ für die Geräte erstellen, die eine Verbindung mit Ihrer Anwendung herstellen. Eine Gerätevorlage ist die Blaupause zum Definieren der Merkmale und des Verhaltens eines Gerätetyps. Hierzu zählt beispielsweise Folgendes:

- Telemetriedaten, die das Gerät sendet Beispiele hierfür sind die Temperatur und die Luftfeuchtigkeit. Bei Telemetriedaten handelt es sich um Streamingdaten.
- Geschäftliche Eigenschaften, die ein Bediener ändern kann Beispiele hierfür sind eine Kundenadresse und das Datum der letzten Wartung.
- Geräteeigenschaften, die von einem Gerät festgelegt werden und in der Anwendung schreibgeschützt sind Beispielsweise der Zustand eines Ventils (geöffnet oder geschlossen).
- Vom Bediener festgelegte Eigenschaften, die das Verhalten des Geräts bestimmen Beispielsweise eine Zieltemperatur für das Gerät.
- Befehle, die von einem Operator aufgerufen werden können und auf einem Gerät ausgeführt werden. Ein Beispiel hierfür ist ein Befehl für einen Remoteneustart eines Geräts.

Diese [Gerätevorlage](howto-set-up-template.md) enthält Folgendes:

- Ein _Gerätemodell_ zur Beschreibung der Funktionen, die von einem Gerät implementiert werden sollen. Die Gerätefunktionen umfassen Folgendes:

  - Die Telemetriedaten, die an IoT Central gestreamt werden.
  - Die schreibgeschützten Eigenschaften, die zum Melden des Status an IoT Central verwendet werden.
  - Die schreibbaren Eigenschaften, die von IoT Central zum Festlegen des Gerätestatus empfangen werden.
  - Die Befehle, die aus IoT Central aufgerufen werden.

- Cloudeigenschaften, die nicht auf dem Gerät gespeichert werden
- Anpassungen, Dashboards und Formulare, die Teil der IoT Central-Anwendung sind

### <a name="create-device-templates"></a>Erstellen von Gerätevorlagen

Als Lösungsentwickler haben Sie mehrere Möglichkeiten zum Erstellen von Gerätevorlagen:

- Entwerfen Sie die Gerätevorlage in IoT Central, und implementieren Sie dann das entsprechende Gerätemodell in Ihrem Gerätecode.
- Erstellen Sie ein Gerätemodell mit Visual Studio-Code, und veröffentlichen Sie es in einem Repository. Implementieren Sie den Gerätecode aus dem Modell, und verbinden Sie Ihr Gerät mit Ihrer IoT Central-Anwendung. IoT Central verwendet das Gerätemodell aus dem Repository und erstellt eine einfache Gerätevorlage für Sie.
- Erstellen Sie ein Gerätemodell mit Visual Studio-Code. Implementieren Sie Ihren Gerätecode aus dem Modell. Importieren Sie das Gerätemodell manuell in Ihre IoT Central-Anwendung, und fügen Sie dann alle Cloudeigenschaften, Anpassungen und Dashboards hinzu, die Ihre IoT Central-Anwendung benötigt.

Als Lösungsentwickler können Sie mit IoT Central Code für Testgeräte generieren, um Ihre Gerätevorlagen zu überprüfen.

Wenn Sie ein Geräteentwickler sind, finden Sie unter [Übersicht über die Geräteentwicklung für IoT Central](./overview-iot-central-developer.md) eine Einführung in die Implementierung von Geräten, bei denen diese Gerätevorlagen verwendet werden.

### <a name="customize-the-ui"></a>Anpassen der Benutzeroberfläche

Als Lösungsentwickler können Sie auch die Benutzeroberfläche der IoT Central-Anwendung für die Bediener anpassen, die die Anwendung tagtäglich verwenden. Die Anpassungsmöglichkeiten für Lösungsentwickler umfassen Folgendes:

- Definieren des Layouts von Eigenschaften und Einstellungen für eine Gerätevorlage
- Konfigurieren benutzerdefinierter Dashboards, um Bediener bei der Gewinnung von Erkenntnissen sowie bei der schnelleren Behebung von Problemen zu unterstützen
- Konfigurieren benutzerdefinierter Analysen zur Untersuchung von Zeitreihendaten Ihrer verbundenen Geräte

## <a name="manage-your-devices"></a>Verwalten von Geräten

Als Operator verwenden Sie die IoT Central-Anwendung zur [Verwaltung der Geräte](howto-manage-devices.md) in Ihrer IoT Central-Lösung. Bediener führen Aufgaben wie die folgenden aus:

- Überwachen der mit der Anwendung verbundenen Geräte
- Behandeln und Beheben von Problemen mit Geräten
- Bereitstellen neuer Geräte

Als Lösungsentwickler können Sie [benutzerdefinierte Regeln und Aktionen definieren](howto-configure-rules.md), die über Datenstreaming von verbundenen Geräten ausgeführt werden. Bediener können diese Regeln auf der Geräteebene aktivieren oder deaktivieren, um Aufgaben innerhalb der Anwendung zu steuern und zu automatisieren.

Für jede skalierbare IoT-Lösung ist eine strukturierte Geräteverwaltung unverzichtbar. Es reicht nicht aus, Ihre Geräte einfach nur mit der Cloud zu verbinden. Sie müssen auch die Konnektivität und die Integrität Ihrer Geräte gewährleisten. Operatoren stehen folgende IoT Central-Funktionen zur Verfügung, um Ihre Geräte über den gesamten Anwendungslebenszyklus hinweg zu verwalten:

### <a name="dashboards"></a>Dashboards

Integrierte [Dashboards](./howto-set-up-template.md#generate-default-views) bieten eine anpassbare Benutzeroberfläche für die Überwachung von Geräteintegrität und -telemetrie. Beginnen Sie mit einem vorgefertigten Dashboard aus einer [Anwendungsvorlage](howto-use-app-templates.md), oder erstellen Sie eigene Dashboards, die auf die Anforderungen Ihrer Operatoren zugeschnitten sind. Dashboards können für alle Benutzer in Ihrer Anwendung freigegeben oder als privat konfiguriert werden.

### <a name="rules-and-actions"></a>Regeln und Aktionen

Erstellen Sie [benutzerdefinierte Regeln](tutorial-create-telemetry-rules.md) auf der Grundlage von Gerätestatus und -telemetrie, um schnell Geräte zu erkennen, für die Maßnahmen erforderlich sind. Konfigurieren Sie Aktionen, um die richtigen Personen zu benachrichtigen und somit sicherzustellen, dass zeitnah Korrekturmaßnahmen ergriffen werden.

### <a name="jobs"></a>Aufträge

[Aufträge](howto-run-a-job.md) ermöglichen die Anwendung von Einzel- oder Massenupdates auf Geräte durch Festlegen von Eigenschaften oder Aufrufen von Befehlen.

## <a name="integrate-with-other-services"></a>Integration in andere Dienste

Als Anwendungsplattform ermöglicht IoT Central die Transformierung Ihrer IoT-Daten in geschäftliche Erkenntnisse, die zu verwertbaren Ergebnissen führen. [Regeln](./tutorial-create-telemetry-rules.md), [Datenexport](./howto-export-data.md) und die [öffentliche REST-API](/learn/modules/manage-iot-central-apps-with-rest-api/) sind Beispiele für Möglichkeiten zur Integration von IoT Central in Branchenanwendungen:

![Transformationsmöglichkeiten für Ihre IoT-Daten mit IoT Central](media/overview-iot-central/transform.png)

Sie können geschäftliche Erkenntnisse generieren, indem Sie beispielsweise Computereffizienztrends ermitteln oder den künftigen Energieverbrauch auf der Werksebene vorhersagen. Hierzu können Sie benutzerdefinierte Analysepipelines erstellen, um Telemetriedaten von Ihren Geräten zu verarbeiten und die Ergebnisse zu speichern. Konfigurieren Sie Datenexporte in Ihrer IoT Central-Anwendung, um Telemetriedaten sowie Änderungen an Geräteeigenschaften und -vorlagen in andere Dienste zu exportieren, wo Sie sie dann mit Ihren bevorzugten Tools analysieren, speichern und visualisieren können.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Erstellen benutzerdefinierter IoT-Lösungen und -Integrationen in APIs

Erstellen Sie IoT-Lösungen wie etwa:

- Mobile Begleit-Apps zur Remoteeinrichtung und -steuerung von Geräten
- Benutzerdefinierte Integrationen, die es bereits vorhandenen Branchenanwendungen ermöglichen, mit Ihren IoT-Geräten und -Daten zu interagieren
- Geräteverwaltungsanwendungen für Gerätemodellierung, Onboarding, Verwaltung und Datenzugriff

## <a name="administer-your-application"></a>Verwalten Ihrer Anwendung

IoT Central-Anwendungen werden vollständig von Microsoft gehostet, was den Verwaltungsaufwand für Ihre Anwendungen verringert. Administratoren verwalten über [Benutzerrollen und Berechtigungen](howto-administer.md) den Zugriff auf Ihre Anwendung.

## <a name="pricing"></a>Preise

Sie können eine IoT Central-Anwendung mit einer kostenlosen siebentägigen Testversion erstellen oder einen Standard-Tarif verwenden.

- Im Tarif *Free* erstellte Anwendungen sind sieben Tage lang kostenlos und unterstützen bis zu fünf Geräte. Sie können bis zum Ablauftermin jederzeit in Anwendungen mit Standard-Tarif konvertiert werden.
- Mit dem Tarif *Standard* erstellte Anwendungen werden pro Gerät abgerechnet. Sie können zwischen **Standard 0**, **Standard 1** und **Standard 2** wählen. Die ersten beiden Geräte sind kostenlos. Weitere Informationen finden Sie unter [IoT Central – Preise](https://aka.ms/iotcentral-pricing).

## <a name="quotas"></a>Kontingente

Für jedes Azure-Abonnement gelten Standardkontingente, die den Umfang Ihrer IoT-Lösung beeinträchtigen könnten. Derzeit ist die Anzahl der Anwendungen, die Sie in einem Abonnement bereitstellen können, in IoT Central auf 10 begrenzt. Wenn Sie diesen Grenzwert erhöhen möchten, wenden Sie sich an den [Microsoft-Support](https://azure.microsoft.com/support/options/).

## <a name="known-issues"></a>Bekannte Probleme

- Der fortlaufende Datenexport unterstützt das Avro-Format nicht (Inkompatibilität).
- GeoJSON wird derzeit nicht unterstützt.
- Kartenkachel wird derzeit nicht unterstützt.
- Array-Schematypen werden nicht unterstützt.
- Nur das C-Geräte-SDK und die Node.js-Geräte-SDKs und -Dienst-SDKs werden unterstützt.
- IoT Central ist derzeit in den Regionen USA, Europa, Asien-Pazifik, Australien, Vereinigtes Königreich und Japan verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun einen Überblick über IoT Central verschafft haben, können Sie als Nächstes mit folgenden Schritten fortfahren:

- Lernen Sie die verfügbaren [Azure-Technologien und -Dienste für die Erstellung von IoT-Lösungen](../../iot-fundamentals/iot-services-and-technologies.md) kennen.
- Wenn Sie Geräteentwickler sind und sich näher mit dem Code beschäftigen möchten, wird als nächster Schritt das [Erstellen und Verbinden einer Clientanwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md) empfohlen.
- Machen Sie sich mit der [Benutzeroberfläche von Azure IoT Central](overview-iot-central-tour.md) vertraut.
- [Erstellen Sie eine Azure IoT Central-Anwendung.](quick-deploy-iot-central.md)
- Erfahren Sie, wie Sie [eine Verbindung für ein Azure IoT Edge-Gerät herstellen](./tutorial-add-edge-as-leaf-device.md).
- Machen Sie sich eingehender mit [Azure IoT-Technologien und -Diensten](../../iot-fundamentals/iot-services-and-technologies.md) vertraut.