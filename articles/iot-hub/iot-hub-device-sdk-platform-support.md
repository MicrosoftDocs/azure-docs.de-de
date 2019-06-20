---
title: Azure IoT-Geräte-SDKs – Plattformunterstützung | Microsoft-Dokumentation
description: 'Konzepte: Liste mit den von den Azure IoT-Geräte-SDKs unterstützten Plattformen'
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: 7bcc1bf6b734abe202c5fec5d515604f4bf8e4a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60398704"
---
# <a name="azure-iot-sdks-platform-support"></a>Azure IoT-SDKs – Plattformunterstützung

Bei den [Azure IoT-SDKs](iot-hub-devguide-sdks.md) handelt es sich um Bibliotheken für die Interaktion mit IoT Hub und dem Device Provisioning Service mit umfassender Unterstützung für verschiedene Sprachen und Plattformen. Die SDKs können auf den meisten Plattformen ausgeführt werden, und Entwickler können das C-SDK für bestimmte Plattformen portieren, indem sie die Informationen unter [Porting Guidance](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md) (Leitfaden zur Portierung) befolgen. 

Microsoft unterstützt zahlreiche Betriebssysteme, Plattformen und Frameworks, und eine Erweiterung zur Verwendung des Azure IoT-C-SDK ist ebenfalls möglich. Einige werden offiziell vom Team unterstützt und in Ebenen gruppiert, die für die für Benutzer zu erwartende Supportebene stehen. *Vollständig unterstützte Plattformen* bedeutet, dass Microsoft für Folgendes sorgt:

- Microsoft erstellt kontinuierlich End-to-End-Tests für den Master und die Versionen mit LTS-Unterstützung und führt diese Tests aus.  Um eine Testabdeckung in verschiedenen Versionen zu gewährleisten, führen wir Tests in der Regel für die neueste LTS-Version und für die am häufigsten verwendete Version durch.  Andere Versionen derselben Plattform werden möglicherweise über die Plattformversionskompatibilität unterstützt.
- Microsoft stellt ggf. Installationsleitfäden oder -pakete bereit.
- Microsoft unterstützt die Plattformen auf GitHub vollständig.

Darüber hinaus haben einige Partner unser C-SDK auf weitere Plattformen portiert und verwalten die Plattformabstraktionsschicht (Platform Abstraction Layer, PAL). Im [Azure Certified for IoT-Gerätekatalog](https://catalog.azureiotsolutions.com/) finden Sie auch eine Liste mit den Betriebssystemplattformen, für die die verschiedenen SDKs getestet wurden. Für die SDKs werden auf diesen Plattformen regelmäßig Builds mit eingeschränkten Tests und Supportleistungen bereitgestellt:

* MBED2
* Arduino
* Windows CE 2013 (veraltet ab Oktober 2018)
* .NET Standard 1.3 mit .NET Core 2.1 und .NET Framework 4.7
* Xamarin iOS, Android, UWP

## <a name="supported-platforms"></a>Unterstützte Plattformen

Es werden verschiedene Plattformen unterstützt.

### <a name="c-sdk"></a>C-SDK

| Betriebssystem                  | Arch | Compiler             | TLS-Bibliothek       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | openssl – 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc-7.3              | WolfSSL – 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | Openssl – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Natives OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3              | Openssl – 1.1.0f  |

### <a name="python-sdk"></a>Python SDK

| Betriebssystem                  | Arch | Compiler   | TLS-Bibliothek |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2,7 | openssl     |
| Windows Server 2016 | x64  | Python 2,7 | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 2,7 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS High Sierra   | x64  | Python 2,7 | openssl     |

### <a name="net-sdk"></a>.NET SDK

| Betriebssystem                  | Arch | Framework            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.7   | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | –               |

### <a name="nodejs-sdk"></a>Node.js SDK

| Betriebssystem                                           | Arch | Node-Version |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (mit Node 6 Docker-Image) | X64  | Node 6       |
| Windows Server 2016                          | X64  | Node 6       |

### <a name="java-sdk"></a>Java-SDK

| Betriebssystem                  | Arch | Java-Version |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| Android API 28 | X64  | Java 8       |
| Android Things | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Unterstützte Partnerplattformen

Kunden können unsere Plattformunterstützung erweitern, indem sie das Azure IoT C SDK portieren, insbesondere durch Erstellung der PAL (Platform Abstraction Layer) des SDK. Microsoft arbeitet mit Partnern zusammen, um erweiterte Unterstützung zu bieten. Verschiedene Partner haben das C SDK auf weitere Plattformen portiert und verwalten die PAL.

| Partner             | Geräte                            | Link                     | Support |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE für IoT-SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4-Serie <br/> STM32F4-Serie <br/>  STM32F7-Serie <br/>  STM32L4 Discovery Kit für IoT-Knoten    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Unterstützung](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF Launchpad <br/> CC3220S Launchpad <br/> MSP432E4 Launchpad      | [Azure IoT-Plug-In für SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2- Forum](https://e2e.ti.com) <br/> [TI E2E-Forum für CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E-Forum für MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Nächste Schritte

* [Geräte- und Dienst-SDKs](iot-hub-devguide-sdks.md)
* [Porting Guidance](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md) (Leitfaden für Portierung)
