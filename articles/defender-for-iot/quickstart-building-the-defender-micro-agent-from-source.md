---
title: Erstellen des Micro-Agents für Defender auf der Grundlage von Quellcode (Vorschau)
titleSuffix: Azure Defender for IoT
description: Der Micro-Agent beinhaltet eine Infrastruktur, die Sie zum Anpassen Ihrer Distribution verwenden können.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 782c14c6feee56c4b19d5a234cb41186d51decb2
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99810040"
---
# <a name="build-the-defender-micro-agent-from-source-code"></a>Erstellen des Micro-Agents für Defender auf der Grundlage von Quellcode

Der Micro-Agent beinhaltet eine Infrastruktur, die Sie zum Anpassen Ihrer Distribution verwenden können. Eine Liste der verfügbaren Konfigurationsparameter finden Sie in der Datei `configs/LINUX_BASE.conf`.

Ändern Sie für eine einzelne Distribution die Basisdatei (`.conf`). 

Wenn Sie mehrere Distributionen benötigen, können Sie die Basiskonfiguration als Grundlage verwenden und deren Werte überschreiben. 

So überschreiben Sie die Werte:

1. Erstellen Sie eine neue Datei vom Typ `.dist`.

1. Fügen Sie ganz oben `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` hinzu.
 
1. Definieren Sie neue Werte für alles, was Sie benötigen. Beispiel: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Versehen Sie die Datei vom Typ `.dist` bei der Erstellung mit einem Verweis. Beispiel: 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>Signieren der Baselinekonfiguration 

Der Agent überprüft standardmäßig die Authentizität von Konfigurationsdateien, die auf dem Datenträger platziert werden, um Manipulationen entgegenzuwirken.

Dieser Prozess kann durch Definieren des Präprozessorflags `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` beendet werden.

Für Produktionsumgebungen sollte die Signaturüberprüfung nicht deaktiviert werden. 

Sollten Sie für Produktionsszenarien eine andere Konfiguration benötigen, wenden Sie sich an das Team von Defender für IoT. 

## <a name="prerequisites"></a>Voraussetzungen 

1. Fordern Sie bei Ihrem Konto-Manager Zugriff auf den Quellcode von Defender für IoT an.
 
1. Klonen oder extrahieren Sie den Quellcode in einem Ordner auf dem Datenträger.

1. Navigieren Sie zu diesem Verzeichnis.

1. Pullen Sie die Submodule mithilfe des folgenden Codes:

    ```bash
    git submodule update --init
    ```
    
1. Pullen Sie als Nächstes die Submodule für das Azure IoT SDK mithilfe des folgenden Codes: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Fügen Sie eine Ausführungsberechtigung hinzu, und führen Sie das Setupskript für die Entwicklerumgebung aus:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Erstellen Sie ein Verzeichnis für die Buildausgaben: 

    ```bash
    mkdir cmake 
    ```

1. Optional: Laden Sie das Tool [VS Code](https://code.visualstudio.com/download ) herunter, und installieren Sie es. 

1. Optional: Installieren Sie die [C/C++-Erweiterung](https://code.visualstudio.com/docs/languages/cpp ) für VS Code.

## <a name="building-the-defender-iot-micro-agent"></a>Erstellen des Micro-Agents für Defender für IoT 

1. Öffnen Sie das Verzeichnis mit VS Code. 

1. Navigieren Sie zum Verzeichnis `cmake`. 

1. Führen Sie den folgenden Befehl aus: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Beispiel: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Konfigurieren Ihrer Lösung mit Azure Defender für IoT](quickstart-configure-your-solution.md)
