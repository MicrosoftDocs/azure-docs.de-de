---
title: 'Schnellstart: Speech SDK für Java (Windows, Linux, macOS): Plattformeinrichtung – Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Leitfaden, um Ihre Plattform für die Verwendung von Java (Windows, Linux, macOS) mit dem Speech Service SDK einzurichten.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: c58132cfa422eae39fd5f4030afb2ff004c0e71d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552055"
---
In diesem Leitfaden erfahren Sie, wie Sie das [Sprach-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) für 64-Bit-Java 8 JRE installieren. Wenn Sie nur den Paketnamen benötigen, um selbständig einzusteigen, ist das Java SDK im zentralen Maven-Repository nicht verfügbar. Unabhängig davon, ob Sie Gradle oder eine Abhängigkeitsdatei vom Typ `pom.xml` verwenden, müssen Sie ein benutzerdefiniertes Repository hinzufügen, das auf `https://csspeechstorage.blob.core.windows.net/maven/` verweist. (Der Paketname ist weiter unten angegeben.)

> [!NOTE]
> Weitere Informationen zum SDK für sprachaktivierte Geräte und zum Roobo-Gerät finden Sie unter [SDK für sprachaktivierte Geräte](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

- Das Java-Sprach-SDK-Paket ist für die folgenden Betriebssysteme verfügbar:
  - Windows: Nur 64-Bit
  - Mac: macOS X Version 10.13 oder höher
  - Linux: Zeigen Sie die Liste mit den [unterstützten Linux-Distributionen und Zielarchitekturen](~/articles/cognitive-services/speech-service/speech-sdk.md) an.

## <a name="prerequisites"></a>Voraussetzungen

- Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) für Ihre Plattform. Bei der Erstinstallation ist möglicherweise ein Neustart erforderlich.

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) oder [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (erfordert, dass Java bereits installiert ist)
- Für unterstützte Linux-Plattformen müssen bestimmte Bibliotheken installiert sein (`libssl` für die Unterstützung von Secure Sockets Layer und `libasound2` für Audiounterstützung). Im Anschluss finden Sie die Befehle für die Installation der richtigen Versionen dieser Bibliotheken für Ihre Distribution.

  - Führen Sie unter Ubuntu/Debian die folgenden Befehle aus, um die erforderlichen Pakete zu installieren:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Wenn libssl1.0.0 nicht verfügbar ist, sollten Sie stattdessen libssl1.0.x (wobei x größer als 0 ist) oder libssl1.1 installieren.

  - Führen Sie unter RHEL/CentOS die folgenden Befehle aus, um die erforderlichen Pakete zu installieren:

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - Befolgen Sie in RHEL/CentOS 7 die Anweisungen zum [Konfigurieren von RHEL/CentOS 7 für das Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Befolgen Sie unter RHEL/CentOS 8 die Anweisungen zum [Konfigurieren von OpenSSL für Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- Unter Windows benötigen Sie [Microsoft Visual C++ Redistributable für Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) für Ihre Plattform. Beachten Sie, dass Sie bei der erstmaligen Installation vielleicht einen Windows-Neustart ausführen müssen, bevor Sie mit diesem Handbuch fortfahren.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Erstellen eines Eclipse-Projekts und Installieren des Sprach-SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [windows](../quickstart-list.md)]
