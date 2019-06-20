---
title: Überwachen der Gerätekonnektivität mit dem Azure IoT Central-Explorer
description: Überwachen Sie Gerätemeldungen, und beobachten Sie Änderungen bei Gerätezwillingen über die Befehlszeilenschnittstelle des IoT Central-Explorers.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4d17f0e5273c7397bd9c6a71d14b7992d8652768
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165859"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Überwachen der Gerätekonnektivität mit dem Azure IoT Central-Explorer

*Dieses Thema gilt für Generatoren und Administratoren.*

Verwenden Sie die Befehlszeilenschnittstelle des IoT Central-Explorers, um Nachrichten anzuzeigen, die Ihre Geräte an IoT Central senden, und Änderungen im IoT Hub-Zwilling zu beobachten. Mithilfe dieses Open Source-Tools können Sie tiefere Einblicke in den Status der Gerätekonnektivität gewinnen und Probleme von Gerätemeldungen diagnostizieren, die die Cloud oder Geräte nicht erreichen, die auf Änderungen beim Zwilling nicht reagieren.

[Besuchen Sie das Repository „iotc-explorer“ in GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Voraussetzungen

+ Node.js, Version 8.x oder höher – https://nodejs.org
+ Ein Administrator Ihrer Anwendung muss ein Zugriffstoken generieren, das Sie in „iotc-explorer“ verwenden können.

## <a name="install-iotc-explorer"></a>Installieren von iotc-explorer

Führen Sie den folgenden Befehl an der Befehlszeile zum Installieren aus:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Normalerweise müssen Sie in Unix-ähnlichen Umgebungen den Befehl „install“ mit `sudo` ausführen.

## <a name="run-iotc-explorer"></a>Ausführen von iotc-explorer

Die folgenden Abschnitte beschreiben allgemeine Befehle und Optionen, die Sie beim Ausführen von `iotc-explorer` verwenden können. Wenn Sie die vollständige Sammlung von Befehlen und Optionen anzeigen möchten, übergeben Sie `--help` an `iotc-explorer` oder einen seiner Unterbefehle.

### <a name="login"></a>Anmeldung

Bevor Sie beginnen können, müssen Sie einen Administrator für Ihre IoT Central-Anwendung bitten, ein Zugriffstoken für Sie zu generieren. Der Administrator führt die folgenden Schritte aus:

1. Navigieren Sie zu **Verwaltung** und dann zu **Zugriffstoken**.
1. Wählen Sie **Token generieren** aus.
    ![Screenshot der Seite mit Zugriffstokens](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Geben Sie einen Tokennamen ein, und wählen Sie **Weiter**  und dann **Kopieren** aus.
    > [!NOTE]
    > Der Tokenwert wird nur einmal angezeigt, daher muss er vor dem Schließen des Dialogfelds kopiert werden. Nach dem Schließen des Dialogfelds wird er nie wieder angezeigt.

    ![Screenshot des Dialogfelds zum Kopieren des Zugriffstokens](media/howto-use-iotc-explorer/copyaccesstoken.png)

Mit dem Token können Sie sich wie folgt an der Befehlszeilenschnittstelle anmelden:

```cmd/sh
iotc-explorer login "<Token value>"
```

Wenn Sie das Token lieber nicht in Ihrem Shell-Verlauf beibehalten möchten, können Sie es weglassen und stattdessen bei der folgenden Aufforderung eingeben:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Überwachen von Gerätemeldungen

Sie können die von einem bestimmten Gerät oder allen Geräten in Ihrer Anwendung ankommenden Meldungen mit dem Befehl `monitor-messages` überwachen. Dieser Befehl startet einen Watcher, der kontinuierlich neue Nachrichten ausgibt, sobald sie eintreffen:

Um alle Geräte in Ihrer Anwendung zu überwachen, führen Sie den folgenden Befehl aus:

```cmd/sh
iotc-explorer monitor-messages
```

Ausgabe:

![Ausgabe des Befehls „monitor-messages“](media/howto-use-iotc-explorer/monitormessages.png)

Um ein bestimmtes Gerät zu überwachen, fügen Sie die Geräte-ID einfach am Ende des Befehls hinzu:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Sie können auch ein computerfreundlicheres Format ausgeben, indem Sie dem Befehl die `--raw`-Option hinzufügen:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Gerätezwilling abrufen

Mit dem Befehl `get-twin` können Sie die Inhalte des Zwillings für ein IoT Central-Gerät abrufen. Führen Sie zu diesem Zweck den folgenden Befehl aus:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Ausgabe:

![Ausgabe des Befehls „get-twin“](media/howto-use-iotc-explorer/getdevicetwin.png)

So wie bei `monitor-messages` erhalten Sie eine computerfreundlichere Ausgabe, indem Sie die `--raw`-Option übergeben:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie der IoT Central-Explorer verwendet wird, erkunden Sie idealerweise als nächsten Schritt das [Verwalten von Geräten mit IoT Central](howto-manage-devices.md).
