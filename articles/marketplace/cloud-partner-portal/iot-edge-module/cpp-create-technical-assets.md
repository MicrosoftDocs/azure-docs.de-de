---
title: Erstellen der technischen Ressourcen eines Azure IoT Edge-Moduls | Azure Marketplace
description: Erstellen Sie technische Ressourcen für ein IoT Edge-Modul.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 9f40e2272388e6f467b8c3d0b01a6639bf652e80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942394"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Vorbereiten der technischen Ressourcen für das IoT Edge-Modul

Dieser Artikel beschreibt die Anforderungen, die Ihre technischen Ressourcen für das IoT Edge-Modul vor der Veröffentlichung im Azure Marketplace erfüllen müssen.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Grundlegendes zu IoT Edge-Modulen und erste Schritte

Ein IoT Edge-Modul ist ein Docker-kompatibler Container, der zur Ausführung auf einem IoT Edge-Gerät erstellt wurde.

- Weitere Informationen zu IoT Edge-Modulen finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Informationen zu den ersten Schritten bei der Entwicklung Ihres IoT-Edge-Moduls finden Sie unter [Grundlegendes zu den Anforderungen und Tools für die Entwicklung von IoT Edge-Modulen](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Technische Anforderungen

Die folgenden technischen Anforderungen müssen erfüllt sein, damit Ihr IoT Edge-Modul im Azure Marketplace zertifiziert und veröffentlicht werden kann.

### <a name="platform-support"></a>Plattformunterstützung

Ihr IoT Edge-Modul muss eine der folgenden Plattformoptionen unterstützen.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Von IoT Edge unterstützte Plattformen der Ebene 1

Bieten Sie Unterstützung für alle Plattformen der Ebene 1, die von IoT Edge unterstützt werden (wie unter [Azure IoT Edge-Support](https://docs.microsoft.com/azure/iot-edge/support) dargestellt). Diese Option wird empfohlen, da so die Benutzerfreundlichkeit verbessert wird. Module, die diese Kriterien erfüllen, werden vorgestellt. Für ein Modul mit dieser Plattformoption muss Folgendes gelten:

- Es muss ein `latest`-Tag und ein Versionstag (z.B. `1.0.1`) bereitstellen, bei denen es sich um Manifesttags handelt, die mit dem [Manifesttool](https://github.com/estesp/manifest-tool) von GitHub erstellt wurden.
- Es muss die [Registerkarte „Marketplace“](./cpp-marketplace-tab.md) verwenden, um einen Link zu [kompatiblen, für IoT Edge zertifizierten Geräten](https://aka.ms/iot-edge-certified) hinzuzufügen. Dieser Link wird in `https://aka.ms/iot-edge-certified` aufgelöst. Dies ist eine Website, auf der Kunden nach zertifizierten Geräten suchen können. Diese Website wird auch als [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/)-Gerätekatalog bezeichnet.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Eine Teilmenge der von IoT Edge unterstützten Plattformen der Ebene 1
  
Bieten Sie Unterstützung für eine Teilmenge (mindestens eine) der Plattformen der Ebene 1, die von IoT Edge unterstützt werden (wie unter [Azure IoT Edge-Support](https://docs.microsoft.com/azure/iot-edge/support) dargestellt). Für ein Modul mit dieser Plattformoption muss Folgendes gelten:

- Es muss ein `latest`-Tag und ein Versionstag (z.B. `1.0.1`) bereitstellen, bei denen es sich um Manifesttags handelt, die mit dem [Manifesttool](https://github.com/estesp/manifest-tool) von GitHub erstellt wurden, wenn mehr als eine Plattform unterstützt wird. Manifesttags sind optional, wenn nur eine Plattform unterstützt wird.
- Es muss die [Registerkarte „Marketplace“](./cpp-marketplace-tab.md) verwenden, um einen Link für mindestens ein IoT Edge-Gerät aus dem [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/)-Gerätekatalog bereitzustellen.

### <a name="device-dimensions"></a>Gerätegrößen

IoT Edge-Modulgrößen (CPU, RAM, Speicher, GPU usw.) auf IoT Edge-Zielgeräten müssen die folgenden Anforderungen erfüllen:

- Das Modul muss **mit mindestens einem für IoT Edge zertifizierten** Gerät aus dem [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/)-Gerätekatalog funktionieren.
- Die **minimalen Hardwareanforderungen** müssen im letzten Absatz in der Beschreibung des Angebots dokumentiert werden (unter der [Registerkarte „Marketplace“](./cpp-marketplace-tab.md)). Optional können Sie auch die empfohlenen Hardwareanforderungen auflisten, wenn sie sich deutlich unterscheiden. Fügen Sie z.B. folgenden Abschnitt am Ende der Angebotsbeschreibung hinzu:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfiguration

Enthalten sind auch Standardkonfigurationseinstellungen, um die Bereitstellung auf einem IoT Edge-Gerät so weit wie möglich zu vereinfachen. Darüber hinaus kann der Container das SDK für IoT Edge-Module enthalten, um die Kommunikation mit edgeHub und IoT Hub zu ermöglichen.

#### <a name="default-configuration"></a>Standardkonfiguration

IoT Edge-Module müssen mit den Standardeinstellungen, die auf der [SKU-Registerkarte des Cloud-Partnerportals](./cpp-skus-tab.md) angegeben sind, starten können. Die folgenden Standardeinstellungen sind verfügbar:

- **Standardrouten**
- Standardmäßige **gewünschte Eigenschaften von Gerätezwillingen**
- Standardmäßige **Umgebungsvariablen**
- Standardmäßige **createOptions**

In einem Szenario, in dem ein für einen Standardwert erforderlicher Parameter nicht sinnvoll ist (z.B. die IP-Adresse eines Kundenservers), fügen Sie einen Parameter als Standardwert hinzu. Dieser Wert wird in Klammern eingeschlossen und in Großbuchstaben geschrieben. In diesem Beispiel würden Sie die folgende standardmäßige Umgebungsvariable festlegen:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurationsdokumentation

Alle Konfigurationseinstellungen eines IoT Edge-Moduls müssen eindeutig dokumentiert werden (Verwendung der Routen, gewünschte Eigenschaften von Gerätezwillingen, Umgebungsvariablen, „createOptions“ usw.). Geben Sie einen Link zur Dokumentation an. Andernfalls muss die Dokumentation Teil Ihrer Angebots-/SKU-Beschreibung sein.

### <a name="tags-and-versioning"></a>Tags und Versionsverwaltung

Kunden müssen ein Modul leicht bereitstellen und automatisch Updates aus dem Marketplace abrufen können (in einem Entwicklerszenario). Sie müssen auch in der Lage sein, eine exakte Version zu verwenden und einzufrieren, die sie getestet haben (in einem Produktionsszenario).

Um diese Kundenerwartungen zu erfüllen und im Marketplace veröffentlicht zu werden, müssen IoT Edge-Module die folgenden Anforderungen erfüllen:

- Sie müssen ein `latest`-Manifesttag enthalten, das auf die neueste Version auf allen unterstützten Plattformen verweist.
- Versionstags müssen im Format „X.Y.Z“ angegeben werden. „X“, „Y“ und „Z“ sind dabei ganze Zahlen.
- Sie müssen ein Versionstag enthalten, z.B. `1.0.1`, das auf eine bestimmte Version auf allen unterstützten Plattformen verweist.
- Versionstags, z.B. `1.0.1`, dürfen nicht aktualisiert werden, da sie unveränderlich sein müssen.

>[!Note]
>Optional kann die Versionsverwaltung Tags für „parallele Versionen“, z.B. `2.0` und `1.0`, enthalten. Dies unterstützt die Beibehaltung mehrerer paralleler Hauptversionen.

### <a name="telemetry"></a>Telemetrie

Module, die das SDK für IoT-Module verwenden, müssen für Telemetriedaten den eindeutigen Modulbezeichner auf `PublisherId.OfferId.SkuId` festlegen. Mit einem eindeutigen Bezeichner kann im Azure Marketplace die Anzahl der Modulinstanzen, die ausgeführt werden, identifiziert werden.

 Verwenden Sie die folgenden Methoden aus den SDKs für IoT-Module, um `ProductInfo` auf diesen Bezeichner festzulegen:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Bei Modulen, die das SDK für IoT-Module nicht verwenden, sind weniger präzise Erkenntnisse im Cloud-Partnerportal verfügbar, z.B. die Anzahl der Downloads.

### <a name="security"></a>Sicherheit

IoT Edge-Module müssen Zugriff mit möglichst geringen Berechtigungen auf den Host erhalten. [Privilegierte Module](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) sollten vermieden werden.

### <a name="module-iot-sdk"></a>SDK für IoT-Module

Die Aufnahme des SDK für IoT-Module ist keine Voraussetzung für die Zertifizierung. Durch die Aufnahme des SDK für IoT-Module kann jedoch die Benutzerfreundlichkeit verbessert werden. Beispielsweise kann das Weiterleiten oder Senden von Nachrichten an die Cloud unterstützt werden.

Das SDK für IoT-Module ist erforderlich, um Telemetriedaten über die Anzahl der ausgeführten Modulinstanzen zu erhalten.


## <a name="recertification-process"></a>Prozess zur erneuten Zertifizierung

<!-- Add legal time windows-->
Partner werden benachrichtigt, sobald eine wichtige Änderung vorgenommen wird, die ihre Module betrifft. Beispiele:

- Unterstützungsmatrix für Betriebssysteme/Architekturen der Ebene 1, die von IoT Edge unterstützt werden
- SDK für IoT-Module
- IoT Edge-Runtime
- Richtlinien für die IoT Edge-Modulzertifizierung

Partner müssen ihre Module mit dem Cloud-Partnerportal aktualisieren und erneut zertifizieren.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hosten des IoT Edge-Moduls in Azure Container Registry

Um Ihr IoT Edge-Modul in das Cloud-Partnerportal hochzuladen, müssen Sie es zuerst in [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) hosten. Das Modul muss alle Tags enthalten, die Sie veröffentlichen möchten, einschließlich der Imagetags, auf die durch ein Manifesttag verwiesen wird.


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Ihres IoT Edge-Modulangebots](./cpp-create-offer.md)
