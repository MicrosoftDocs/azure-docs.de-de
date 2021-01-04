---
title: Speech-Geräte-SDK „Roobo Smart Audio Dev Kit v1“ – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Voraussetzungen und Anweisungen für den Einstieg in das Speech Devices SDK „Roobo Smart Audio Dev Kit v1“
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015289"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Gerät: Roobo Smart Audio Dev Kit

Dieser Artikel enthält gerätespezifische Informationen für das Roobo Smart Audio Dev Kit.

## <a name="set-up-the-development-kit"></a>Einrichten des Development Kits

1. Das Development Kit hat zwei Micro-USB-Anschlüsse. Der linke Anschluss dient zur Stromversorgung des Development Kits und ist in der folgenden Abbildung mit „Power“ markiert. Der rechte dient zur Steuerung und ist in der Abbildung mit „Debug“ markiert.

    ![Anschließen eines Development Kits](media/speech-devices-sdk/qsg-1.png)

1. Schalten Sie das Development Kit ein, nachdem Sie es mit einem Mikro-USB-Kabel an einen PC oder ein Netzteil angeschlossen haben. Eine grüne Stromanzeige leuchtet unter dem oberen Board auf.

1. Zum Steuern des Development Kit verbinden Sie den Debug-Anschluss mithilfe eines zweiten Micro-USB-Kabels mit einem Computer. Für eine zuverlässige Kommunikation ist es unerlässlich, ein hochwertiges Kabel zu verwenden.

1. Richten Sie Ihr Development Kit für eine kreisförmige oder lineare Konfiguration aus.

    |Development Kit-Konfiguration|Ausrichtung|
    |-----------------------------|------------|
    |Kreisförmig|Aufrecht, Mikrofone zur Decke gerichtet|
    |Linear|Auf der Seite, Mikrofone zu Ihnen gerichtet (siehe folgende Abbildung)|

    ![Lineare Ausrichtung des Development Kits](media/speech-devices-sdk/qsg-2.png)

1. Installieren Sie die Zertifikate, und legen Sie die Berechtigungen für das Soundgerät fest. Geben Sie die folgenden Befehle in einem Eingabeaufforderungsfenster ein:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Diese Befehle verwenden die Android Debug Bridge, `adb.exe`, die Teil der Android Studio-Installation ist. Dieses Tool befindet sich in „C:\Users\[Benutzername]\AppData\Local\Android\Sdk\platform-tools“. Sie können dieses Verzeichnis Ihrem Pfad hinzufügen, um das Aufrufen von `adb` zu vereinfachen. Andernfalls müssen Sie den vollständigen Pfad zu Ihrer Installation von „adb.exe“ in jedem Befehl angeben, der `adb` aufruft.
    >
    > Wenn die Fehlermeldung `no devices/emulators found` auftritt, prüfen Sie, ob das USB-Kabel angeschlossen ist und eine hohe Qualität hat. Mithilfe von `adb devices` können Sie überprüfen, ob Ihr Computer mit dem Development Kit kommunizieren kann. Dem ist so, wenn eine Geräteliste zurückgegeben wird.
    >
    > [!TIP]
    > Schalten Sie das Mikrofon und die Lautsprecher Ihres Computers stumm, um sicherzustellen, dass die Mikrofone des Development Kits verwendet werden. Auf diese Weise lösen Sie nicht versehentlich das Gerät mit Audio vom PC aus.

1. Wenn Sie einen Lautsprecher an das Development Kit anschließen möchten, verwenden Sie dazu den Audioausgang. Wählen Sie einen hochwertigen Lautsprecher mit einem 3,5-mm-Analoganschluss.

    ![Vysor-Audio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informationen zur Entwicklung

Weitere Informationen zur Entwicklung finden Sie im [Roobo-Entwicklungshandbuch](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Audio

Roobo bietet ein Tool, das sämtliche Audiosignale im Flashspeicher erfasst. Es kann Ihnen beim Beheben von Audioproblemen helfen. Für jede Development Kit-Konfiguration wird eine Version des Tools bereitgestellt. Wählen Sie Ihr Gerät auf der [Roobo-Website](http://ddk.roobo.com/) aus, und wählen Sie dann unten auf der Seite den Link **Roobo-Tools** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Ausführen der Android-Beispiel-App](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)