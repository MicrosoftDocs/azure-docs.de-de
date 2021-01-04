---
title: Erstellen der Azure IoT Central-Geräte-Bridge | Microsoft-Dokumentation
description: Erstellen Sie die IoT Central-Geräte-Bridge, um für Ihre IoT Central-App eine Verbindung mit anderen IoT-Clouds (beispielsweise mit Sigfox, Particle oder The Things Network) herzustellen.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: fc8ea41e804344735cfa2400d5d763622d8811c8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026249"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Erstellen der Azure IoT Central-Geräte-Bridge, um weitere IoT-Clouds mit IoT Central zu verbinden

*Dieses Thema gilt für Administratoren.*

Die IoT Central-Geräte-Bridge ist eine Open-Source-Lösung, die Sigfox, Particle, The Things Network und andere Clouds mit Ihrer IoT Central-App verbindet. Mit der IoT Central-Geräte-Bridge können Sie direkt von der Leistungsfähigkeit von IoT Central profitieren – ob Sie nun Geräte zur Ressourcennachverfolgung verwenden, die mit dem Low Power Wide Area Network von Sigfox verbunden sind, Geräte zur Überwachung der Luftqualität in der Gerätecloud von Particle nutzen oder in TTN Geräte zur Messung der Bodenfeuchtigkeit verwenden. Die Geräte-Bridge verbindet andere IoT-Clouds mit IoT Central, indem sie die Daten, die Ihre Geräte an die anderen Clouds senden, an Ihre IoT Central-App weiterleitet. In Ihrer IoT Central-App können Sie Regeln erstellen und Analysen für die Daten ausführen, Workflows in Power Automate und Azure-Logik-Apps erstellen, Daten exportieren u. v. m. [IoT Central-Geräte-Bridge](https://aka.ms/iotcentralgithubdevicebridge) von GitHub abrufen

## <a name="what-is-it-and-how-does-it-work"></a>Allgemeine Informationen und Funktionsweise
Die IoT Central-Geräte-Bridge ist eine Open-Source-Lösung in GitHub. Über die Schaltfläche „Deploy to Azure“ (In Azure bereitstellen) wird eine benutzerdefinierte Azure Resource Manager-Vorlage mit mehreren Azure-Ressourcen in Ihrem Azure-Abonnement bereitgestellt. Die Ressource umfasst Folgendes:
-    Azure-Funktions-App
-    Azure Storage-Konto
-    Verbrauchstarif
-    Azure-Schlüsseltresor

Die entscheidende Komponente der Geräte-Bridge ist die Funktions-App. Sie empfängt HTTP POST-Anforderungen von anderen IoT-Plattformen oder anderen benutzerdefinierten Plattformen über eine einfache Webhookintegration. Wir haben Beispiele bereitgestellt, die zeigen, wie Sie eine Verbindung mit Sigfox-, Particle- und TTN-Clouds herstellen. Diese Lösung lässt sich problemlos erweitern, um eine Verbindung mit Ihrer benutzerdefinierten IoT-Cloud herzustellen – vorausgesetzt, Ihre Plattform kann HTTP POST-Anforderungen an Ihre Funktions-App senden.
Die Funktions-App transformiert die Daten in ein Format, das von IoT Central akzeptiert wird, und leitet sie über DPS-APIs weiter.

![Screenshot von Azure Functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Wenn Ihre IoT Central-App das Gerät anhand der Geräte-ID in der weitergeleiteten Nachricht erkennt, wird für dieses Gerät eine neue Messung angezeigt. Ist Ihrer IoT Central-App die Geräte-ID noch nicht bekannt, versucht Ihre Funktions-App, ein neues Gerät mit dieser Geräte-ID zu registrieren, und das Gerät wird in Ihrer IoT Central-App als nicht zugeordnetes Gerät angezeigt. 

## <a name="how-do-i-set-it-up"></a>Einrichtung
Die Infodatei im GitHub-Repository enthält eine ausführliche Anleitung. 

## <a name="pricing"></a>Preise
Die Azure-Ressourcen werden in Ihrem Azure-Abonnement gehostet. Weitere Informationen zu Preisen finden Sie in der [Infodatei](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich hier mit der Erstellung der IoT Central-Geräte-Bridge vertraut gemacht haben, können Sie mit dem folgenden Schritt fortfahren:

> [!div class="nextstepaction"]
> [Verwalten von Geräten](howto-manage-devices.md)
