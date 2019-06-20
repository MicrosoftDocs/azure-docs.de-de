---
title: Azure IoT-Geräteverwaltung mit IoT-Erweiterung für Azure CLI | Microsoft-Dokumentation
description: Verwenden Sie das Tool IoT-Erweiterung für Azure CLI mit den Verwaltungsmethoden für direkte Methoden und gewünschte Eigenschaften von Gerätezwillingen.
author: chrissie926
manager: ''
keywords: Azure Iot-Geräteverwaltung, Azure IoT Hub-Geräteverwaltung, IoT-Geräteverwaltung, IoT Hub-Geräteverwaltung
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 6b1029c5532e106c269b47e6e184b9c93faf8d09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399602"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Verwenden der IoT-Erweiterung für Azure CLI für die Verwaltung von Azure IoT Hub-Geräten

![Lückenloses Diagramm](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Die IoT-Erweiterung für Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ist eine neue Open Source-IoT-Erweiterung, die die Funktionen der [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) ergänzt. Die Azure CLI enthält Befehle zum Interagieren mit Azure Resource Manager- und Verwaltungsendpunkten. So können Sie beispielsweise die Azure CLI verwenden, um einen virtuellen Azure-Computer oder einen IoT Hub zu erstellen. Eine CLI-Erweiterung ermöglicht es einem Azure-Dienst, die Azure-Befehlszeilenschnittstelle zu ergänzen, wodurch Sie Zugriff auf zusätzliche dienstspezifische Funktionen erhalten. Die IoT-Erweiterung ermöglicht IoT-Entwicklern Befehlszeilenzugriff auf alle IoT Hub-, IoT Edge- und IoT Hub Device Provisioning-Dienst-Funktionen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Verwaltungsoption          | Aufgabe  |
|----------------------------|-----------|
| Direkte Methoden             | Lassen Sie ein Gerät beispielsweise mit dem Senden von Nachrichten beginnen oder dies beenden, oder starten Sie es neu.                                        |
| Gewünschte Eigenschaften von Gerätezwillingen    | Setzen Sie ein Gerät in bestimmte Status, stellen Sie z.B. das Leuchten einer grünen LED ein, oder legen Sie das Telemetriesendeintervall auf 30 Minuten fest.         |
| Berichtete Eigenschaften von Gerätezwillingen   | Rufen Sie den berichteten Status eines Geräts ab. Das Gerät meldet z.B., das die LED jetzt blinkt.                                    |
| Gerätezwillingstags                  | Speichern gerätespezifischer Metadaten in der Cloud – beispielsweise den Aufstellungsort eines Automaten.                         |
| Gerätezwillingabfragen        | Fragen Sie alle Gerätezwillinge ab, um diejenigen abzurufen, die beliebige Bedingungen erfüllen; identifizieren Sie z.B. die zur Verwendung verfügbaren Geräte. |

Eine ausführlichere Erläuterung zu den Unterschieden und Anweisungen zur Verwendung dieser Optionen finden Sie im [Leitfaden zur D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md) und [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md).

Gerätezwillinge sind JSON-Dokumente, in denen Gerätestatusinformationen (Metadaten, Konfigurationen und Bedingungen) gespeichert werden. Von IoT Hub wird für jedes Gerät, das eine Verbindung herstellt, dauerhaft ein Gerätezwilling gespeichert. Weitere Informationen zu Gerätezwillingen finden Sie unter [Erste Schritte mit Gerätezwillingen (Node)](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Lerninhalt

Sie lernen, wie Sie die IoT-Erweiterung für Azure CLI mit verschiedenen Verwaltungsoptionen auf Ihrem Entwicklungscomputer verwenden können.

## <a name="what-you-do"></a>Aufgaben

Sie führen Azure CLI und die IoT-Erweiterung für Azure CLI mit verschiedenen Verwaltungsoptionen aus.

## <a name="what-you-need"></a>Voraussetzungen

* Sie müssen das Tutorial [Verbinden des Raspberry Pi-Onlinesimulators mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-web-simulator-get-started.md) oder eines der gerätespezifischen Tutorials wie [Verbinden von Raspberry Pi mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-kit-node-get-started.md) abgeschlossen haben. In diesen werden folgende Anforderungen beschrieben:

  - Ein aktives Azure-Abonnement.
  - Ein Azure IoT Hub in Ihrem Abonnement.
  - Eine Clientanwendung, die Nachrichten an Ihren Azure IoT Hub sendet.

* Stellen Sie sicher, dass Ihr Gerät im Verlauf dieses Tutorials mit der Clientanwendung ausgeführt wird.

* [Python 2.7x oder Python 3.x](https://www.python.org/downloads/)

* Die Azure-Befehlszeilenschnittstelle Installationsinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Ihre Azure CLI-Version muss mindestens 2.0.24 lauten. Verwenden Sie `az –version`, um dies zu überprüfen. 

* Installieren Sie die IoT-Erweiterung. Die einfachste Möglichkeit ist die Ausführung von `az extension add --name azure-cli-iot-ext`. In der [Infodatei zur IoT-Erweiterung](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) werden mehrere Wege zum Installieren der Erweiterung beschrieben.

## <a name="log-in-to-your-azure-account"></a>Melden Sie sich bei Ihrem Azure-Konto an.

Melden Sie sich mithilfe des folgenden Befehls bei Ihrem Azure-Konto an:

```bash
az login
```

## <a name="direct-methods"></a>Direkte Methoden

```bash
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Gewünschte Eigenschaften von Gerätezwillingen

Legen Sie mit folgendem Befehl für die gewünschte Eigenschaft ein Intervall von 3.000 fest:

```bash
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Diese Eigenschaft kann von Ihrem Gerät gelesen werden.

## <a name="device-twin-reported-properties"></a>Gemeldete Eigenschaften von Gerätezwillingen

Zeigen Sie mithilfe des folgenden Befehls die berichteten Eigenschaften des Geräts an:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Eine der beiden gemeldeten Eigenschaften ist „$metadata.$lastUpdated“, die anzeigt, wann die Geräte-App das letzte Mal ihren gemeldeten Eigenschaftssatz aktualisiert hat.

## <a name="device-twin-tags"></a>Tags von Gerätezwillingen

Zeigen Sie mithilfe des folgenden Befehls die Tags und Eigenschaften des Geräts an:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Fügen Sie mit folgendem Befehl dem Gerät eine Feldrolle „temperature&humidity“ hinzu:

```bash
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Gerätezwillingabfragen

Fragen Sie mit folgendem Befehl Geräte mit einem Rollentag „temperature&humidity“ ab:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Fragen Sie mit folgendem Befehl alle Geräte außer denen mit einem Rollentag „temperature&humidity“ ab:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, Gerät-zu-Cloud-Nachrichten zu überwachen und Cloud-zu-Gerät-Nachrichten zwischen dem IoT-Gerät und Azure IoT Hub zu senden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
