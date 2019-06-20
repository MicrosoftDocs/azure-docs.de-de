---
title: Erhalten einer E-Mail beim Öffnen einer Tür per SendGrid-Dienst und Azure Functions | Microsoft-Dokumentation
description: Überwachen Sie den Magnetsensor, um zu erkennen, wenn eine Tür geöffnet wird, und verwenden Sie Azure Functions, um eine E-Mail-Benachrichtigung zu senden.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: a620b592a33f9de11de53d623d257f203da2157b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61370275"
---
# <a name="door-monitor"></a>Türüberwachung          

Das MXChip IoT DevKit enthält einen integrierten Magnetsensor. In diesem Projekt erkennen Sie das Vorhandensein oder Fehlen eines starken Magnetfelds in der Nähe, das in diesem Fall von einem kleinen Dauermagnet stammt.

## <a name="what-you-learn"></a>Lerninhalt

In diesem Projekt lernen Sie Folgendes:
- Verwenden des Magnetsensors des MXChip IoT DevKit zum Erkennen der Bewegung eines in der Nähe befindlichen Magneten
- Verwenden des SendGrid-Diensts zum Senden einer Benachrichtigung an Ihre E-Mail-Adresse

> [!NOTE]
> Führen Sie die folgenden Aufgaben durch, um dieses Projekt für den Einsatz in der Praxis zu nutzen:
> - Bringen Sie an einer Türkante ein Magnet an.
> - Bringen Sie das DevKit am Türrahmen auf der Höhe des Magneten an. Beim Öffnen oder Schließen der Tür wird der Sensor ausgelöst, und Sie werden per E-Mail über dieses Ereignis informiert.

## <a name="what-you-need"></a>Voraussetzungen

Befolgen Sie die Anweisungen im [Leitfaden zu den ersten Schritten](iot-hub-arduino-iot-devkit-az3166-get-started.md), um folgende Voraussetzungen zu erfüllen:

* Herstellen einer Wi-Fi-Verbindung mit dem DevKit
* Vorbereiten der Entwicklungsumgebung

Ein aktives Azure-Abonnement. Wenn Sie kein Abonnement haben, können Sie sich mit einer der folgenden Methoden registrieren:

* [Kostenloses Microsoft Azure-Testkonto für 30 Tage](https://azure.microsoft.com/free/) aktivieren
* [Azure-Gutschrift](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) beanspruchen, wenn Sie MSDN- oder Visual Studio-Abonnent sind

## <a name="deploy-the-sendgrid-service-in-azure"></a>Bereitstellen des SendGrid-Diensts in Azure

[SendGrid](https://sendgrid.com/) ist eine cloudbasierte Plattform für die Zustellung von E-Mails. Der Dienst wird zum Senden von E-Mail-Benachrichtigungen verwendet.

> [!NOTE]
> Falls Sie bereits einen SendGrid-Dienst bereitgestellt haben, können Sie direkt mit [Bereitstellen von IoT Hub in Azure](#deploy-iot-hub-in-azure) fortfahren.

### <a name="sendgrid-deployment"></a>SendGrid-Bereitstellung

Verwenden Sie die Schaltfläche **In Azure bereitstellen**, um Azure-Dienste bereitzustellen. Diese Schaltfläche ermöglicht die schnelle und einfache Bereitstellung von Open-Source-Projekten für Microsoft Azure.

Klicken Sie unten auf die Schaltfläche **In Azure bereitstellen**. 

[![In Azure bereitstellen](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Melden Sie sich jetzt an, falls Sie nicht bereits an Ihrem Azure-Konto angemeldet sind. 

Das Formular für die SendGrid-Registrierung wird angezeigt.

![SendGrid-Bereitstellung](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Füllen Sie das Registrierungsformular aus:

   * **Ressourcengruppe**: Erstellen Sie eine Ressourcengruppe zum Hosten des SendGrid-Diensts, oder verwenden Sie eine vorhandene Ressourcengruppe. Siehe [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Name**: Der Name für Ihren SendGrid-Dienst. Wählen Sie einen eindeutigen Namen, der sich von Ihren anderen Diensten unterscheidet.

   * **Kennwort**: Für den Dienst ist ein Kennwort erforderlich, das in diesem Projekt nicht verwendet wird.

   * **E-Mail**: Der SendGrid-Dienst sendet die E-Mail zur Überprüfung an diese E-Mail-Adresse.

Aktivieren Sie die Option **An Dashboard anheften**, damit diese Anwendung später leichter gefunden werden kann. Klicken Sie anschließend auf **Kaufen**, um das Registrierungsformular zu übermitteln.
 
### <a name="sendgrid-api-key-creation"></a>Erstellung des SendGrid-API-Schlüssels

Klicken Sie nach Abschluss der Bereitstellung darauf und anschließend auf die Schaltfläche **Verwalten**. Die Seite für Ihr SendGrid-Konto wird angezeigt, auf der Sie Ihre E-Mail-Adresse überprüfen können.

![Verwalten von SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Klicken Sie auf der SendGrid-Seite auf **Einstellungen** > **API-Schlüssel** > **API-Schlüssel erstellen**.

![SendGrid-API-Erstellung – 1](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Geben Sie auf der Seite **API-Schlüssel erstellen** den **API-Schlüsselnamen** ein, und klicken Sie auf **Erstellen und anzeigen**.

![SendGrid-API-Erstellung – 2](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Ihr API-Schlüssel wird nur einmal angezeigt. Kopieren Sie ihn, und bewahren Sie ihn gut auf, da Sie ihn im nächsten Schritt benötigen.

## <a name="deploy-iot-hub-in-azure"></a>Bereitstellen von IoT Hub in Azure

Mit den folgenden Schritten werden weitere Azure IoT-bezogene Dienste und die Lösung Azure Functions für dieses Projekt bereitgestellt.

Klicken Sie unten auf die Schaltfläche **In Azure bereitstellen**. 

[![In Azure bereitstellen](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Das Registrierungsformular wird angezeigt.

![IoT Hub-Bereitstellung](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Füllen Sie die Felder des Registrierungsformulars aus.

   * **Ressourcengruppe**: Erstellen Sie eine Ressourcengruppe zum Hosten des SendGrid-Diensts, oder verwenden Sie eine vorhandene Ressourcengruppe. Siehe [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/manage-resource-groups-portal.md).

   * **IoT Hub-Name**: Der Name Ihres IoT-Hubs. Wählen Sie einen eindeutigen Namen, der sich von Ihren anderen Diensten unterscheidet.

   * **IoT Hub-SKU**: F1 ist kostenlos (auf 1 pro Abonnement beschränkt). Weitere Preisinformationen finden Sie auf der [Seite mit den Preisen](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Von E-Mail-Adresse**: Dieses Feld sollte die E-Mail-Adresse enthalten, die Sie beim Einrichten des SendGrid-Diensts verwendet haben.

Aktivieren Sie die Option **An Dashboard anheften**, damit diese Anwendung später leichter gefunden werden kann. Klicken Sie anschließend auf **Kaufen**, wenn Sie mit dem nächsten Schritt fortfahren möchten.
 
## <a name="build-and-upload-the-code"></a>Erstellen und Hochladen des Codes

Laden Sie als Nächstes den Beispielcode in VS Code, und stellen Sie die erforderlichen Azure-Dienste bereit.

### <a name="start-vs-code"></a>Starten von Visual Studio Code

- Stellen Sie sicher, dass DevKit **nicht** mit Ihrem Computer verbunden ist.
- Starten Sie Visual Studio Code.
- Verbinden Sie das DevKit mit Ihrem Computer.

> [!NOTE]
> Beim Starten von VS Code erhalten Sie unter Umständen eine Fehlermeldung mit dem Hinweis, dass die Arduino-IDE oder das dazugehörige Boardpaket nicht gefunden werden kann. Gehen Sie wie folgt vor, wenn Sie diesen Fehler erhalten: Schließen Sie VS Code, und starten Sie die Arduino-IDE neu. In VS Code sollte der Pfad der Arduino-IDE jetzt richtig gefunden werden.

### <a name="open-arduino-examples-folder"></a>Öffnen des Ordners „Arduino-Beispiele“

Erweitern Sie auf der linken Seite den Abschnitt **ARDUINO-BEISPIELE**, wechseln Sie zu **Beispiele für MXCHIP AZ3166 > AzureIoT**, und wählen Sie **DoorMonitor**. Mit dieser Aktion wird ein neues Visual Studio Code-Fenster mit einem Projektordner geöffnet.

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Sie können die Beispiel-App auch über die Befehlspalette öffnen. Öffnen Sie mit der Tastenkombination `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) die Befehlspalette, geben Sie **Arduino** ein, suchen Sie dann nach **Arduino: Beispiele**, und wählen Sie den Eintrag aus.

### <a name="provision-azure-services"></a>Bereitstellen von Azure-Diensten

Führen Sie im Lösungsfenster die Aufgabe für die Cloudbereitstellung aus:
- Drücken Sie `Ctrl+P` (macOS: `Cmd+P`).
- Geben Sie `task cloud-provision` in das angezeigte Textfeld ein.

Im Visual Studio Code-Terminal werden Sie über eine interaktive Befehlszeile durch die Bereitstellung der erforderlichen Azure-Dienste geführt. Wählen Sie in der Liste die gleichen Elemente aus, die Sie zuvor unter [Bereitstellen von IoT Hub in Azure](#deploy-iot-hub-in-azure) bereitgestellt haben.

![Cloudbereitstellung](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Wenn die Seite beim Anmelden an Azure im Ladezustand hängen bleibt, helfen Ihnen die Informationen im [Abschnitt „Page hangs when log in Azure“ (Seite hängt beim Anmelden an Azure) der häufig gestellten Fragen zum IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) bei der Behebung dieses Problems weiter. 

### <a name="build-and-upload-the-device-code"></a>Erstellen und Hochladen des Gerätecodes

Als Nächstes laden Sie den Code für das Gerät hoch.

#### <a name="windows"></a>Windows

1. Verwenden Sie `Ctrl+P` zum Ausführen von `task device-upload`.

2. Das Terminal fordert Sie zum Wechseln in den Konfigurationsmodus auf. Halten Sie hierfür Taste A gedrückt, und drücken Sie anschließend gleichzeitig einmal die Reset-Taste. Die DevKit-Identifizierungsnummer und das Wort *Konfiguration* werden angezeigt.

#### <a name="macos"></a>macOS

1. Versetzen Sie das DevKit in den Konfigurationsmodus: Halten Sie hierfür Taste „A“ gedrückt, und drücken Sie anschließend gleichzeitig einmal die Reset-Taste. Auf dem Bildschirm wird „Konfiguration“ angezeigt.

2. Verwenden Sie `Cmd+P`, um `task device-upload` auszuführen.

#### <a name="verify-upload-and-run-the-sample-app"></a>Überprüfen, Hochladen und Ausführen der Beispiel-App

Die Verbindungszeichenfolge, die im Schritt [Bereitstellen von Azure-Diensten](#provision-azure-services) abgerufen wird, wird jetzt festgelegt. 

Anschließend startet VS Code die Überprüfung und den Upload des Arduino-Sketchs in das DevKit.

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

Das DevKit startet neu und beginnt mit der Ausführung des Codes.

> [!NOTE]
> In einigen Fällen wird möglicherweise die Fehlermeldung „Fehler: AZ3166: Unbekanntes Paket“ angezeigt. Dieser Fehler tritt auf, wenn der Boardpaketindex nicht richtig aktualisiert wurde. Informationen zum Beheben dieses Fehlers finden Sie im [Abschnitt „Development“ (Entwicklung) in den häufig gestellten Fragen zum IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testen des Projekts

Das Programm wird zum ersten Mal initialisiert, wenn sich das DevKit in der Nähe eines stabilen Magnetfelds befindet.

Nach der Initialisierung wird `Door closed` angezeigt. Wenn sich das Magnetfeld ändert, ändert sich der Status in `Door opened`. Bei jeder Änderung des Türstatus erhalten Sie eine E-Mail-Benachrichtigung. (Es kann bis zu fünf Minuten dauern, bis diese E-Mail-Nachricht eintrifft.)

![Magnet in der Nähe des Sensors: Tür geschlossen](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magnet in der Nähe des Sensors: Tür geschlossen")

![Magnet nicht in der Nähe des Sensors: Tür geöffnet](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnet nicht in der Nähe des Sensors: Tür geöffnet")

## <a name="problems-and-feedback"></a>Probleme und Feedback

Wenn Probleme auftreten, helfen Ihnen die Informationen in den [häufig gestellten Fragen zum IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) weiter, oder wenden Sie sich über folgende Kanäle an uns:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nächste Schritte

Es wurde beschrieben, wie Sie ein DevKit-Gerät mit Ihrer Azure IoT-Remoteüberwachungslösung verbinden und den SendGrid-Dienst zum Senden einer E-Mail verwenden. Hier sind die empfohlenen nächsten Schritte aufgeführt:

* [Solution Accelerator für die Azure IoT-Remoteüberwachung: Übersicht](https://docs.microsoft.com/azure/iot-suite/)
* [Herstellen einer Verbindung zwischen einem MXChip IoT DevKit-Gerät und Ihrer Azure IoT Central-Anwendung](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
