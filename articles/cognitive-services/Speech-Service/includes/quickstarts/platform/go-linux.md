---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: cf765145cafa2eb06d77ea2e153e45c296281b71
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551476"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für Linux installieren.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systemanforderungen

Linux: Zeigen Sie die Liste mit den [unterstützten Linux-Distributionen und Zielarchitekturen](~/articles/cognitive-services/speech-service/speech-sdk.md) an.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie Folgendes:

* Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) für Ihre Plattform. Bei der Erstinstallation ist möglicherweise ein Neustart erforderlich.
* gcc
* [Go-Binärdatei (1.13 oder höher)](https://golang.org/dl/)

* Für unterstützte Linux-Plattformen müssen bestimmte Bibliotheken installiert sein (`libssl` für die Unterstützung von Secure Sockets Layer und `libasound2` für Audiounterstützung). Im Anschluss finden Sie die Befehle für die Installation der richtigen Versionen dieser Bibliotheken für Ihre Distribution.

   * Unter Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Wenn libssl1.0.0 nicht verfügbar ist, sollten Sie stattdessen libssl1.0.x (wobei x größer als 0 ist) oder libssl1.1 installieren.

   * Unter RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - Befolgen Sie in RHEL/CentOS 7 die Anweisungen zum [Konfigurieren von RHEL/CentOS 7 für das Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Befolgen Sie unter RHEL/CentOS 8 die Anweisungen zum [Konfigurieren von OpenSSL für Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Konfigurieren der Go-Umgebung

Führen Sie die folgenden Schritte aus, um Ihre Go-Umgebung für die Suche nach dem Speech SDK einzurichten. Ersetzen Sie in beiden Schritten `<architecture>` durch die Prozessorarchitektur Ihrer CPU. Mögliche Werte sind `x86`, `x64`, `arm32` und `arm64`.

1. Da für die Bindungen `cgo` verwendet wird, müssen Sie die Umgebungsvariablen festlegen, damit das SDK von Go gefunden werden kann:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Damit Anwendungen mit dem SDK ausgeführt werden können, muss dem Betriebssystem mitgeteilt werden, wo sich die Bibliotheken befinden:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list-go.md)]
