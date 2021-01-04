---
title: Vorbereiten der technischen Ressourcen für das IoT Edge-Modul – Azure Marketplace
description: Informieren Sie sich über die technischen Anforderungen und die Anforderungen an die Konfiguration, die von den technischen Ressourcen für Ihr IoT Edge-Modul (Internet of Things) erfüllt werden müssen, damit Sie diese in Azure Marketplace veröffentlichen können.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: aca27b89a3b92b410fa560c8b4bd7eb3d4e0a935
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346773"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Vorbereiten der technischen Ressourcen für das IoT Edge-Modul

In diesem Artikel werden die Anforderungen beschrieben, die die technischen Ressourcen für Ihr IoT Edge-Modul (Internet of Things) vor der Veröffentlichung in Azure Marketplace erfüllen müssen.

## <a name="get-started"></a>Erste Schritte

Ein IoT Edge-Modul ist ein Docker-kompatibler Container, der auf einem IoT Edge-Gerät ausgeführt wird.

- Weitere Informationen zu IoT Edge-Modulen finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](../../iot-edge/iot-edge-modules.md).
- Informationen zu den ersten Schritten bei der Entwicklung Ihres IoT Edge-Moduls finden Sie unter [Entwickeln eigener IoT Edge-Module](../../iot-edge/module-development.md).

## <a name="technical-requirements"></a>Technische Anforderungen

Ihr IoT Edge-Modul muss die folgenden technischen Anforderungen erfüllen, damit es in Azure Marketplace zertifiziert und veröffentlicht werden kann.

### <a name="platform-support"></a>Plattformunterstützung

Ihr IoT Edge-Modul muss eine der folgenden Plattformoptionen unterstützen:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Von IoT Edge unterstützte Plattformen der Ebene 1

Ihr Modul muss alle Plattformen der Ebene 1 unterstützen, die von IoT Edge unterstützt werden (wie unter [Azure IoT Edge-Support](../../iot-edge/support.md) dargestellt). Diese Option wird empfohlen, da so die Benutzerfreundlichkeit verbessert wird. Module, die diese Kriterien erfüllen, werden vorgestellt. Für ein Modul mit dieser Plattformoption muss Folgendes gelten:

- Es muss ein latest-Tag und ein Versionstag (z. B. 1.0.1) bereitstellen. Dabei handelt es sich um Manifesttags, die mit dem [Manifesttool von GitHub](https://github.com/estesp/manifest-tool) erstellt werden.

- Verwenden Sie die Registerkarte „Angebotsliste“ in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace), um unter dem Abschnitt **Nützliche Links** dem [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)-Gerätekatalog einen Link hinzuzufügen.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Eine Teilmenge der von IoT Edge unterstützten Plattformen der Ebene 1

Ihr Modul muss eine Teilmenge (mindestens eine) der Plattformen der Ebene 1 unterstützen, die von IoT Edge unterstützt werden (wie unter [Azure IoT Edge-Support](../../iot-edge/support.md) dargestellt). Für ein Modul mit dieser Plattformoption muss Folgendes gelten:

- Es muss ein latest-Tag und ein Versionstag (z. B. 1.0.1) bereitstellen. Dabei handelt es sich um Manifesttags, die mit dem [Manifesttool](https://github.com/estesp/manifest-tool) von GitHub erstellt werden, wenn mehr als eine Plattform unterstützt wird. Manifesttags sind optional, wenn nur eine Plattform unterstützt wird.
- Verwenden Sie die Registerkarte „Angebotsliste“ in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace), um unter dem Abschnitt **Nützliche Links** mindestens einem IoT Edge-Gerät im [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/)-Gerätekatalog einen Link hinzuzufügen.

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Abbildung: Abschnitt „Angebotsliste“ in „Partner Center“":::

### <a name="device-dimensions"></a>Gerätegrößen

IoT Edge-Modulgrößen (wie CPU, RAM, Speicher und GPU) auf IoT Edge-Zielgeräten müssen die folgenden Anforderungen erfüllen:

- Das Modul muss mit mindestens einem IoT Edge-Gerät aus dem [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/)-Gerätekatalog funktionieren.

- Die minimalen Hardwareanforderungen müssen im letzten Absatz in der Beschreibung des Angebots dokumentiert werden (unter der Registerkarte „Angebotsliste“ in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace)). Optional können Sie auch die empfohlenen Hardwareanforderungen auflisten, wenn sie sich deutlich unterscheiden. Fügen Sie z.B. folgenden Abschnitt am Ende der Angebotsbeschreibung hinzu:

Kopieren Sie diesen HTML-Text, oder verwenden Sie die entsprechenden Rich-Text-Funktionen im Bearbeitungsfenster.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfiguration

Ihr Modul muss zudem auch Standardkonfigurationseinstellungen enthalten, um die Bereitstellung auf einem IoT Edge-Gerät so weit wie möglich zu vereinfachen. Diese Informationen können auf der Seite **Technische Konfiguration** für den Plan in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) bereitgestellt werden. Darüber hinaus kann der Container das SDK für IoT Edge-Module enthalten, um die Kommunikation mit edgeHub und IoT Hub zu ermöglichen.

#### <a name="default-configuration"></a>Standardkonfiguration

IoT Edge-Module müssen mit den Standardeinstellungen starten können, die auf der Seite **Technische Konfiguration** für den Plan in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) angegeben sind. Die folgenden Standardeinstellungen sind verfügbar:

- **Standardrouten**
- **Gewünschte Standardeigenschaften für Modulzwilling**
- Standardmäßige **Umgebungsvariablen**
- **Standardoptionen für die Containererstellung**

In einem Szenario, in dem ein für einen Standardwert erforderlicher Parameter nicht sinnvoll ist (z. B. die IP-Adresse eines Kundenservers), fügen Sie einen Parameter als Standardwert hinzu. Dieser Wert wird in Großbuchstaben geschrieben und in Klammern eingeschlossen. In diesem Beispiel würden Sie die folgende standardmäßige Umgebungsvariable festlegen:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurationsdokumentation

Alle Konfigurationseinstellungen eines IoT Edge-Moduls müssen eindeutig dokumentiert werden. So müssen Sie beispielsweise die Verwendung der Routen, gewünschte Eigenschaften von Gerätezwillingen, Umgebungsvariablen, „createOptions“ usw. dokumentieren. Sie müssen entweder einen Link zur Dokumentation angeben oder die Dokumentation in Ihre Angebots- bzw. Planbeschreibung aufnehmen. Sie können diese Informationen auf der Seite **Angebotsliste** und der Seite **Planlisting** in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) bereitstellen.

#### <a name="tags-and-versioning"></a>Tags und Versionsverwaltung

Kunden müssen ein Modul leicht bereitstellen und automatisch Updates aus dem Marketplace abrufen können (in einem Entwicklerszenario). Sie müssen auch in der Lage sein, eine exakte Version zu verwenden und einzufrieren, die sie getestet haben (in einem Produktionsszenario).

Um diese Kundenerwartungen zu erfüllen und im Marketplace veröffentlicht zu werden, müssen IoT Edge-Module die folgenden Anforderungen erfüllen:

- Sie müssen ein latest-Manifesttag enthalten, das auf die neueste Version auf allen unterstützten Plattformen verweist.
- Sie müssen Versionstags im Format „X.Y.Z“ enthalten. „X“, „Y“ und „Z“ sind dabei ganze Zahlen.
- Sie müssen ein Versionstag enthalten, z. B. 1.0.1, das auf eine bestimmte Version auf allen unterstützten Plattformen verweist.
- Versionstags wie 1.0.1 dürfen nicht aktualisiert werden, da sie nicht geändert werden dürfen.

> [!NOTE]
> Optional kann die Versionsverwaltung Tags für „parallele Versionen“, z. B. 2.0 und 1.0, enthalten. Dies unterstützt die Beibehaltung mehrerer paralleler Hauptversionen.

### <a name="telemetry"></a>Telemetrie

Module, die das SDK für IoT-Module verwenden, müssen für Telemetriedaten den eindeutigen Modulbezeichner auf „PublisherId.OfferId.SkuId“ festlegen. Mit einem eindeutigen Bezeichner kann im Azure Marketplace die Anzahl der Modulinstanzen, die ausgeführt werden, identifiziert werden.

Verwenden Sie eine der folgenden Methoden aus den SDKs für IoT-Module, um „ProductInfo“ auf diesen Bezeichner festzulegen:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

Bei Modulen, die das SDK für IoT-Module nicht verwenden, sind weniger präzise Erkenntnisse in Partner Center verfügbar, z. B. die Anzahl der Downloads.

### <a name="security"></a>Sicherheit

IoT Edge-Module müssen [privilegierte Module](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) vermeiden. Fragen Sie stattdessen nach einem Hostzugriff mit möglichst geringen Berechtigungen.

### <a name="module-iot-sdk"></a>SDK für IoT-Module

Die Aufnahme des SDK für IoT-Module ist keine Voraussetzung für die Zertifizierung. Durch die Aufnahme des SDK für IoT-Module kann jedoch die Benutzerfreundlichkeit verbessert werden. Beispielsweise kann das Weiterleiten oder Senden von Nachrichten an die Cloud unterstützt werden.

Das SDK für IoT-Module ist erforderlich, um Telemetriedaten über die Anzahl der ausgeführten Modulinstanzen zu erhalten.

## <a name="recertification-process"></a>Prozess zur erneuten Zertifizierung

Partner werden benachrichtigt, sobald eine wichtige Änderung vorgenommen wird, die ihre Module betrifft. Beispiele:

- Unterstützungsmatrix für Betriebssysteme/Architekturen der Ebene 1, die von IoT Edge unterstützt werden
- SDK für IoT-Module
- IoT Edge-Laufzeit
- Richtlinien für die IoT Edge-Modulzertifizierung

Partner müssen ihre Angebote aktualisieren und neu zertifizieren, indem Sie sie in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) erneut veröffentlichen.

Ihr Angebot wird auch dann neu zertifiziert, wenn Sie es aktualisieren, indem Sie beispielsweise neue Imagetags hinzufügen.

## <a name="host-module-in-azure-container-registry"></a>Hostmodul in Azure Container Registry

Um Ihr IoT Edge-Modul in Azure Marketplace hochzuladen, müssen Sie es zuerst in [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) hosten. Das Modul muss alle Tags enthalten, die Sie veröffentlichen möchten, einschließlich der Imagetags, auf die durch ein Manifesttag verwiesen wird. Weitere Informationen hierzu finden Sie im Tutorial [Erstellen einer Azure-Containerregistrierung und Übertragen eines Containerimages per Pushvorgang](../../container-instances/container-instances-tutorial-prepare-acr.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines IoT Edge-Modulangebots](azure-iot-edge-module-creation.md)