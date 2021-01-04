---
title: Gateways für nachgeschaltete Geräte – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden Sie Azure IoT Edge zum Erstellen eines transparenten oder nicht transparenten Gateway-Geräts oder eines Proxygatewaygeräts, das Daten von mehreren nachgeschalteten Geräten in die Cloud sendet oder lokal verarbeitet.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 83e8089073f7e7e7634ddf00f7276e12aaf645b0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536437"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Verwendung eines IoT Edge-Geräts als Gateway

IoT Edge-Geräte können als Gateways fungieren, die eine Verbindung zwischen anderen Geräten im Netzwerk und IoT Hub bereitstellen.

Das IoT Edge-Hubmodul verhält sich wie IoT Hub und kann somit Verbindungen von allen Geräten verarbeiten, die über eine Identität mit IoT Hub verfügen, einschließlich anderer IoT Edge-Geräte. Dieser Gatewaymustertyp wird als *transparent* bezeichnet, weil Nachrichten von nachgeschalteten Geräten an IoT Hub übergeben werden können, als gäbe es kein Gateway zwischen ihnen.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Ab Version 1.2 von IoT Edge können transparente Gateways Downstreamverbindungen von anderen IoT Edge-Geräten verarbeiten.
::: moniker-end

Für Geräte, die keine eigene Verbindung mit IoT Hub herstellen (können), können IoT Edge-Gateways diese Verbindung bereitstellen. Dieser Gatewaymustertyp wird als *Übersetzung* bezeichnet, weil das IoT Edge-Gerät eingehende Nachrichten nachgeschalteter Geräte verarbeiten muss, bevor sie an IoT Hub weitergeleitet werden können. In diesen Szenarien sind zusätzliche Module auf dem IoT Edge-Gateway für die Ausführung der Verarbeitungsschritte erforderlich.

Die Gatewaymuster „transparent“ und „Übersetzung“ schließen sich nicht gegenseitig aus. Ein einzelnes IoT Edge-Gerät kann sowohl als transparentes Gateway als auch als Übersetzungsgateway fungieren.

Alle Gatewaymuster bieten folgende Vorteile:

* **Edgeanalysen**: Verwenden Sie lokal KI-Dienste zum Verarbeiten von Daten, die von nachgeschalteten Geräten stammen, ohne vollständige Telemetriedaten in die Cloud zu senden. Suchen und reagieren Sie lokal auf Informationen, senden Sie nur eine Teilmenge von Daten an IoT Hub.
* **Isolierung von nachgeschalteten Geräten**: Das Gatewaygerät kann alle nachgeschalteten Geräte vor dem Zugriff über das Internet schützen. Es kann sich zwischen einem OT-Netzwerk (Operational Technology) ohne Konnektivität und einem IT-Netzwerk (Information Technology) mit Internetzugriff befinden. Auf ähnliche Weise können Geräte, die keine eigene Verbindung mit IoT Hub herstellen können, stattdessen eine Verbindung mit einem Gatewaygerät herstellen.
* **Multiplexing der Verbindung**: Alle Geräte, die über ein IoT Edge-Gateway mit IoT Hub verbunden sind, verwenden dieselbe zugrunde liegende Verbindung.
* **Glättung des Datenverkehrs**: Das IoT Edge-Gerät implementiert automatisch exponentielles Backoff, wenn IoT Hub den Datenverkehr drosselt, wobei die Nachrichten lokal beibehalten werden. Durch diesen Vorteil wird Ihre Lösung unempfindlich gegenüber Spitzen im Datenverkehr.
* **Offlineunterstützung**: Das Gatewaygerät speichert Nachrichten und Zwillingsupdates, die nicht an IoT Hub übermittelt werden können.

## <a name="transparent-gateways"></a>Transparente Gateways

Beim Gatewaymuster „transparent“ können Geräte, die theoretisch eine Verbindung mit IoT Hub herstellen könnten, stattdessen eine Verbindung mit einem Gatewaygerät herstellen. Die nachgeschalteten Geräte verfügen über ihre eigenen IoT Hub-Identitäten und stellen die Verbindung mithilfe von MQTT- oder AMQP-Protokollen her. Das Gateway übergibt einfach die Kommunikation zwischen den Geräten und IoT Hub. Weder die Geräte noch die Benutzer, die damit über IoT Hub interagieren, wissen, dass ein Gateway ihre Kommunikation vermittelt. Dieses fehlende Wissen bedeutet, dass das Gateway als *transparent* betrachtet wird.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

IoT Edge-Geräte können keinem IoT Edge-Gateway nachgeschaltet werden.

![Diagramm des Gatewaymusters „Transparent“](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

Ab Version 1.2.0 können IoT Edge-Geräte über transparente Gateways eine Verbindung herstellen.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Beziehungen zwischen über- und untergeordneten Geräten

Sie deklarieren transparente Gatewaybeziehungen in IoT Hub, indem Sie das IoT Edge-Gateway als *übergeordnetes* Gerät eines nachgeschalteten *untergeordneten* Geräts festlegen, das eine Verbindung damit herstellt.

Die Beziehung zwischen über- und untergeordneten Geräten wird in der Gatewaykonfiguration an drei Punkten hergestellt:

#### <a name="cloud-identities"></a>Cloudidentitäten

In einem Szenario mit transparentem Gateway benötigen alle Geräte Cloudidentitäten, damit sie sich bei IoT Hub authentifizieren können. Wenn Sie eine Geräteidentität erstellen oder aktualisieren, können Sie die über- oder untergeordneten Geräte des Geräts festlegen. Diese Konfiguration autorisiert das übergeordnete Gatewaygerät, die Authentifizierung für seine untergeordneten Geräte zu übernehmen.

Untergeordnete Geräte können nur über ein übergeordnetes Gerät verfügen. Jedes übergeordnete Gerät kann bis zu 100 untergeordnete Geräte aufweisen.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
In transparenten Gatewaybeziehungen können IoT Edge-Geräte sowohl übergeordnete als auch untergeordnete Geräte sein. Es kann eine Hierarchie von mehreren IoT Edge-Geräten erstellt werden, die einander Bericht erstatten. Der oberste Knoten in einer Gatewayhierarchie kann bis zu fünf Generationen untergeordneter Geräte aufweisen. Beispielsweise kann ein IoT Edge-Gerät fünf Ebenen von IoT Edge-Geräten aufweisen, die als untergeordnete Geräte darunter verknüpft sind. Das IoT Edge-Gerät in der fünften Generation kann jedoch keine untergeordneten Geräte, keine IoT Edge-Geräte und keine anderen Elemente aufweisen.
::: moniker-end

#### <a name="gateway-discovery"></a>Gatewayermittlung

Ein untergeordnetes Gerät muss sein übergeordnetes Gerät im lokalen Netzwerk finden können. Konfigurieren Sie Gatewaygeräte mit einem **Hostnamen**, entweder mit einem vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) oder einer IP-Adresse, der von den untergeordneten Geräten zum Auffinden verwendet wird.

Verwenden Sie für nachgeschaltete IoT-Geräte in der Verbindungszeichenfolge den Parameter **gatewayHostname**, um auf das übergeordnete Gerät zu verweisen.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Verwenden Sie für nachgeschaltete IoT Edge-Geräte in der Datei „config.yaml“ den Parameter **parent_hostname**, um auf das übergeordnete Gerät zu verweisen.
::: moniker-end

#### <a name="secure-connection"></a>Sichere Verbindung

Übergeordnete und untergeordnete Geräte müssen auch ihre Verbindungen untereinander authentifizieren. Jedes Gerät benötigt eine Kopie eines freigegebenen Zertifikats der Stammzertifizierungsstelle, mit dem die untergeordneten Geräte sicherstellen, dass sie eine Verbindung mit dem richtigen Gateway herstellen.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Wenn mehrere IoT Edge-Gateways in einer Gatewayhierarchie miteinander verbunden sind, sollten alle Geräte in der Hierarchie eine einzige Zertifikatkette verwenden.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Gerätefunktionen hinter transparenten Gateways

Alle IoT Hub-Primitiven, die mit der Messagingpipeline von IoT Edge arbeiten, unterstützen auch Szenarien mit transparenten Gateways. Jedes IoT Edge-Gateway verfügt über Funktionen zum Speichern und Weiterleiten der darüber gesendeten Nachrichten.

Der folgenden Tabelle können Sie entnehmen, wie die verschiedenen IoT Hub-Funktionen für Geräte und für Geräte hinter Gateways unterstützt werden.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

| Funktion | IoT-Gerät | IoT hinter einem Gateway |
| ---------- | ---------- | -------------------- |
| [D2C-Nachrichten](../iot-hub/iot-hub-devguide-messages-d2c.md) (Device-to-Cloud, Gerät-zu-Cloud) |  ![Ja – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [C2D-Nachrichten](../iot-hub/iot-hub-devguide-messages-c2d.md) (Cloud-to-Device, Cloud-zu-Gerät) | ![Ja – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [Direkte Methoden](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Ja – IoT, direkte Methode](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT, direkte Methode](./media/iot-edge-as-gateway/check-yes.png) |
| [Gerätezwillinge](../iot-hub/iot-hub-devguide-device-twins.md) und [Modulzwillinge](../iot-hub/iot-hub-devguide-module-twins.md) | ![Ja – IoT-Zwillinge](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnete IoT-Zwillinge](./media/iot-edge-as-gateway/check-yes.png) |
| [Hochladen von Dateien mit IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md) | ![Ja – IoT, Dateiupload](./media/iot-edge-as-gateway/check-yes.png) | ![Nein – untergeordnetes IoT, Dateiupload](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Funktion | IoT-Gerät | IoT hinter einem Gateway | IoT Edge-Gerät | IoT Edge hinter einem Gateway |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [D2C-Nachrichten](../iot-hub/iot-hub-devguide-messages-d2c.md) (Device-to-Cloud, Gerät-zu-Cloud) |  ![Ja – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [C2D-Nachrichten](../iot-hub/iot-hub-devguide-messages-c2d.md) (Cloud-to-Device, Cloud-zu-Gerät) | ![Ja – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Nein – IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![Nein – untergeordnetes IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [Direkte Methoden](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Ja – IoT, direkte Methode](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT, direkte Methode](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – IoT Edge, direkte Methode](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT Edge, direkte Methode](./media/iot-edge-as-gateway/check-yes.png) |
| [Gerätezwillinge](../iot-hub/iot-hub-devguide-device-twins.md) und [Modulzwillinge](../iot-hub/iot-hub-devguide-module-twins.md) | ![Ja – IoT-Zwillinge](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnete IoT-Zwillinge](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – IoT Edge-Zwillinge](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnete IoT Edge-Zwillinge](./media/iot-edge-as-gateway/check-yes.png) |
| [Hochladen von Dateien mit IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md) | ![Ja – IoT, Dateiupload](./media/iot-edge-as-gateway/check-yes.png) | ![Nein – untergeordnetes IoT, Dateiupload](./media/iot-edge-as-gateway/crossout-no.png) | ![Nein – IoT Edge, Dateiupload](./media/iot-edge-as-gateway/crossout-no.png) | ![Nein – untergeordnetes IoT Edge, Dateiupload](./media/iot-edge-as-gateway/crossout-no.png) |
| Containerimage-Pullvorgänge |   |   | ![Ja – IoT Edge, Containerpullvorgang](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT Edge, Containerpullvorgang](./media/iot-edge-as-gateway/check-yes.png) |
| Blobupload |   |   | ![Ja – IoT Edge, Blobupload](./media/iot-edge-as-gateway/check-yes.png) | ![Ja – untergeordnetes IoT Edge, Blobupload](./media/iot-edge-as-gateway/check-yes.png) |

**Containerimages** können von übergeordneten Geräten für untergeordnete Geräte heruntergeladen, gespeichert und bereitgestellt werden.

**Blobs** (einschließlich Supportbundle und Protokolle) können von untergeordneten Geräten auf übergeordnete Geräte hochgeladen werden.

::: moniker-end

## <a name="translation-gateways"></a>Übersetzungsgateways

Wenn nachgeschaltete Geräte keine Verbindung mit IoT Hub herstellen können, muss das IoT Edge-Gateway als Übersetzer fungieren. Dieses Muster ist häufig für Geräte erforderlich, die MQTT, AMQP oder HTTP nicht unterstützen. Da diese Geräte keine Verbindung mit IoT Hub herstellen können, ist ohne Vorverarbeitung auch das Herstellen einer Verbindung mit dem IoT Edge-Hubmodul nicht möglich.

Benutzerdefinierte Module oder Module von Drittanbietern, die häufig für die Hardware oder für das Protokoll des nachgeschalteten Geräts spezifisch sind, müssen für das IoT Edge-Gateway bereitgestellt werden. Diese Übersetzungsmodule nehmen die eingehenden Nachrichten an und verwandeln sie in ein Format, das von IoT Hub verstanden werden kann.

Es gibt zwei Muster für Übersetzungsgateways: *Protokollübersetzung* und *Identitätsübersetzung*.

![Diagramm der Muster von Übersetzungsgateways](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Protokollübersetzung

Beim Gatewaymuster „Protokollübersetzung“ verfügt nur das IoT Edge-Gateway über eine Identität mit IoT Hub. Das Übersetzungsmodul empfängt Nachrichten von nachgeschalteten Geräten, übersetzt sie in ein unterstütztes Protokoll, und dann sendet das IoT Edge-Gerät die Nachrichten im Auftrag der nachgeschalteten Geräte. Alle Informationen scheinen von einem Gerät – dem Gateway – zu stammen. Nachgeschaltete Geräte müssen zusätzliche identifizierende Informationen in ihre Nachrichten einbetten, wenn Cloudanwendungen die Daten pro Gerät analysieren möchten. Außerdem werden IoT Hub-Primitive wie Zwillinge und direkte Methoden nur für das Gatewaygerät und nicht für nachgeschaltete Geräte unterstützt. Gateways in diesem Muster werden im Gegensatz zu transparenten Gateways als *intransparent* betrachtet, weil sie die Identitäten von nachgeschalteten Geräten verschleiern.

Die Protokollübersetzung unterstützt Geräte mit begrenzten Ressourcen. Zahlreiche vorhandene Geräte erzeugen Daten, aus denen geschäftliche Erkenntnissen gewonnen werden können; sie wurden jedoch nicht speziell für Cloudkonnektivität konzipiert. Nicht transparente Gateways ermöglichen das Entsperren und Verwenden dieser Daten in einer IoT-Lösung.

### <a name="identity-translation"></a>Identitätsübersetzung

Das Gatewaymuster „Identitätsübersetzung“ basiert auf der Protokollübersetzung, aber das IoT Edge-Gateway stellt auch eine IoT Hub-Geräteidentität für die nachgeschalteten Geräte bereit. Das Übersetzungsmodul ist dafür verantwortlich, das von den nachgeschalteten Geräten verwendete Protokoll zu verstehen, die Identität bereitzustellen und die Nachrichten in IoT Hub-Primitive zu übersetzen. Nachgeschaltete Geräte werden in IoT Hub als erstrangige Geräte mit Zwillingen und Methoden angezeigt. Ein Benutzer kann mit den Geräten in IoT Hub interagieren, ohne zu merken, dass ein Gatewaygerät zwischengeschaltet ist.

Die Identitätsübersetzung bietet die Vorteile der Protokollübersetzung und ermöglicht außerdem die vollständige Verwaltbarkeit von nachgeschalteten Geräten über die Cloud. Alle Geräte in Ihrer IoT-Lösung werden unabhängig vom jeweiligen Protokoll in IoT Hub angezeigt.

### <a name="device-capabilities-behind-translation-gateways"></a>Gerätefunktionen hinter Übersetzungsgateways

In der folgenden Tabelle wird erläutert, wie IoT Hub-Funktionen in beiden Übersetzungsgatewaymustern auf nachgeschaltete Geräte erweitert werden.

| Funktion | Protokollübersetzung | Identitätsübersetzung |
| ---------- | -------------------- | -------------------- |
| In der IoT Hub-Identitätsregistrierung gespeicherte Identitäten | Nur die Identität des Gatewaygeräts | Identitäten aller verbundenen Geräte |
| Gerätezwilling | Nur das Gateway hat einen Geräte- und Modulzwilling | Jedes verbundene Gerät hat einen eigenen Gerätezwilling |
| Direkte Methoden und C2D-Nachrichten | Die Cloud kann nur das Gatewaygerät adressieren | Die Cloud kann jedes verbundene Gerät einzeln adressieren |
| [IoT Hub-Drosselungen und -Kontingente](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Gelten für das Gatewaygerät | Gelten für jedes Gerät |

Bei Verwendung des Musters „Protokollübersetzung“ verwenden alle Geräte, die eine Verbindung über dieses Gateway herstellen, dieselbe C2D-Warteschlange, die maximal 50 Nachrichten enthalten kann. Verwenden Sie dieses Muster nur, wenn wenige Geräte eine Verbindung über die einzelnen Bereichsgateways herstellen und der C2D-Datenverkehr gering ist.

Die IoT Edge-Runtime bietet keine Protokoll- oder Identitätsübersetzungsfunktionen. Diese Muster erfordern benutzerdefinierte Module oder Module von Drittanbietern, die häufig für die verwendete Hardware und für das verwendete Protokoll spezifisch sind. Im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) stehen mehrere Protokollübersetzungsmodule zur Auswahl. Ein Beispiel, in dem das Identitätsübersetzungsmuster verwendet wird, finden Sie unter [Azure IoT Edge LoRaWAN Starter Kit](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die drei Schritte zum Einrichten eines transparenten Gateways:

* [Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md)
* [Authentifizieren eines nachgeschalteten Geräts bei Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Verbinden eines nachgeschalteten Geräts mit einem Azure IoT Edge-Gateway](how-to-connect-downstream-device.md)
