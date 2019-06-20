---
title: Gerätekonnektivität in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel werden die wichtigsten Konzepte in Bezug auf die Gerätekonnektivität in Azure IoT Central vorgestellt.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 533327ca1d202f15376e580d20317ddaf0b1d302
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693190"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Gerätekonnektivität in Azure IoT Central

In diesem Artikel werden die wichtigsten Konzepte in Bezug auf die Gerätekonnektivität in Microsoft Azure IoT Central vorgestellt.

Für Azure IoT Central wird der [Azure IoT Hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) zur Verwaltung aller Geräteregistrierungen und -verbindungen verwendet.

DPS ermöglicht Folgendes:

- IoT Central kann Onboarding und das Verbinden von Geräten nach Maß unterstützen.
- Sie können jetzt Geräteanmeldeinformationen generieren und die Geräte offline konfigurieren, ohne sie über IoT Central zu registrieren.
- Geräte können per Shared Access Signatures (SAS) Verbindungen herstellen.
- Geräte können mithilfe von X.509-Zertifikaten nach Industriestandard Verbindungen herstellen.
- Sie können Ihre eigenen Geräte-IDs verwenden, um Geräte in IoT Central zu registrieren. Die Verwendung Ihrer eigenen Geräte-IDs vereinfacht die Integration in vorhandene Backofficesysteme.
- Eine einheitliche Möglichkeit, Geräte mit IoT Central zu verbinden.

In diesem Artikel werden die folgenden vier Anwendungsfälle beschrieben:

1. [Schnelles Verbinden eines einzelnen Geräts mit SAS](#connect-a-single-device)
1. [Verbinden von Geräten nach Maß mit SAS](#connect-devices-at-scale-using-sas)
1. [Verbinden von Geräten nach Maß mit X.509-Zertifikaten](#connect-devices-using-x509-certificates) ist die empfohlene Vorgehensweise für Produktionsumgebungen.
1. [Verbinden ohne vorherige Registrierung der Geräte](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Verbinden eines einzelnen Geräts

Dieser Ansatz ist nützlich, wenn Sie mit IoT Central experimentieren oder Geräte testen. Sie können die Geräte-Verbindungsinformationen aus Ihrer IoT Central-Anwendung verwenden, um die Verbindungszeichenfolge für ein Gerät zu generieren. Detaillierte Schritte dazu finden Sie unter [Generieren einer Geräte-Verbindungszeichenfolge für die Verbindung mit einer Azure IoT Central-Anwendung](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Verbinden von Geräten nach Maß mit SAS

Um nach Maß mithilfe von SAS Verbindungen von Geräten mit IoT Central herstellen zu können, müssen Sie die Geräte registrieren und dann einrichten:

### <a name="register-devices-in-bulk"></a>Registrieren einer großen Zahl von Geräten

Um eine große Anzahl von Geräten bei Ihrer IoT Central-Anwendung zu registrieren, verwenden Sie eine CSV-Datei zum [Importieren der Geräte-IDs und Gerätenamen](howto-manage-devices.md#import-devices).

Um die Verbindungsinformationen für die importierten Geräte abzurufen, [exportieren Sie eine CSV-Datei aus Ihrer IoT Central-Anwendung](howto-manage-devices.md#export-devices).

> [!NOTE]
> Wie Sie Geräte verbinden können, ohne sie zuerst in IoT Central zu registrieren, erfahren Sie unter [Verbinden ohne vorherige Registrierung der Geräte](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Einrichten Ihrer Geräte

Verwenden Sie die Verbindungsinformationen aus der Exportdatei in Ihrem Gerätecode, um das Herstellen einer Verbindung Ihres Geräts mit Ihrer IoT Central-Anwendung und das Senden von Daten an IoT zu ermöglichen. Weitere Informationen zum Verbinden von Geräten finden Sie unter [Nächste Schritte](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Verbinden von Geräten mit X.509-Zertifikaten

In einer Produktionsumgebung ist die Verwendung von X.509-Zertifikaten der empfohlene Mechanismus zur Geräteauthentifizierung für IoT Central. Weitere Informationen finden Sie unter [Geräteauthentifizierung mit X.509-Zertifikaten](../iot-hub/iot-hub-x509ca-overview.md).

In den folgenden Schritten wird das Herstellen der Verbindung von Geräten mit IoT Central mithilfe von X.509-Zertifikaten beschrieben:

1. In Ihrer IoT Central-Anwendung müssen Sie _das X.509-Zwischenzertifikat oder -Stammzertifikat hinzufügen und überprüfen_, das Sie zum Generieren von Gerätezertifikaten verwenden:

    - Navigieren Sie zu **Verwaltung > Geräteverbindung > Zertifikate (X.509)** , und fügen Sie das X.509-Zwischenzertifikat oder -Stammzertifikat hinzu, das Sie zum Generieren der Blattgerätzertifikate verwenden.

      ![Verbindungseinstellungen](media/concepts-connectivity/connection-settings.png)

      Wenn eine Sicherheitsverletzung auftritt oder Ihr primäres Zertifikat abläuft, verwenden Sie das sekundäre Zertifikat, um Downtime zu verringern. Sie können weiterhin Geräte mit dem sekundären Zertifikat bereitstellen, während Sie das primäre Zertifikat aktualisieren.

    - Durch das Verifizieren des Zertifikatbesitzes wird sichergestellt, dass der Benutzer, der das Zertifikat hochgeladen hat, im Besitz des privaten Schlüssels für das Zertifikat ist. So verifizieren Sie das Zertifikat:
        - Wählen Sie die Schaltfläche neben **Überprüfungscode** aus, um einen Code zu generieren.
        - Erstellen Sie ein X.509-Verifizierungszertifikat mit dem Überprüfungscode, den Sie im vorherigen Schritt generiert haben. Speichern Sie das Zertifikat als CER-Datei.
        - Laden Sie das signierte Verifizierungszertifikat hoch, und wählen Sie **Überprüfen** aus.

          ![Verbindungseinstellungen](media/concepts-connectivity/verify-cert.png)

1. Verwenden Sie eine CSV-Datei zum _Importieren und Registrieren von Geräten_ in Ihrer IoT Central-Anwendung.

1. _Richten Sie Ihre Geräte ein._ Generieren Sie die untergeordneten Zertifikate, indem Sie das hochgeladene Stammzertifikat verwenden. Verwenden Sie die **Geräte-ID** als CNAME-Wert in den untergeordneten Zertifikaten. Die Geräte-ID muss aus Kleinbuchstaben bestehen. Programmieren Sie Ihre Geräte dann mit Bereitstellungsdienstinformationen. Wenn ein Gerät erstmalig eingeschaltet wird, ruft es seine Verbindungsinformationen für Ihre IoT Central-Anwendung vom DPS ab.

### <a name="further-reference"></a>Weitere Referenz

- Beispielimplementierung für [Raspberry Pi](https://aka.ms/iotcentral-docs-Raspi-releases).

- [Beispiel für einen Geräteclient in C](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Nur für Testzwecke

Mit diesen Hilfsprogrammen können Sie ausschließlich zu Testzwecken Zertifizierungsstellenzertifikate und Gerätezertifikate generieren.

- Wenn Sie ein DevKit-Gerät verwenden, generiert dieses [Befehlszeilentool](https://aka.ms/iotcentral-docs-dicetool) ein Zertifizierungsstellenzertifikat, das Sie Ihrer IoT Central-Anwendung zum Überprüfen der Zertifikate hinzufügen können.

- Verwenden Sie [dieses Befehlszeilentool](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) für Folgendes:
  - Erstellen einer Zertifikatkette. Führen Sie Schritt 2 im GitHub-Artikel aus.
  - Speichern Sie die Zertifikate als CER-Dateien zum Hochladen in Ihre IoT Central-Anwendung.
  - Verwenden Sie den Überprüfungscode aus der IoT Central-Anwendung, um das Verifizierungszertifikat zu generieren. Führen Sie Schritt 3 im GitHub-Artikel aus.
  - Erstellen Sie untergeordnete Zertifikate für Ihre Geräte unter Verwendung Ihrer Geräte-IDs als Parameter für das Tool. Führen Sie Schritt 4 im GitHub-Artikel aus.

## <a name="connect-without-registering-devices"></a>Verbinden ohne Registrierung der Geräte

Ein grundlegendes Szenario, das IoT Central OEMs ermöglicht, ist die Massenfertigung von Geräten, die mit einer IoT Central-Anwendung eine Verbindung herstellen können, ohne zuerst registriert zu werden. Ein Hersteller muss geeignete Anmeldeinformationen generieren und die Geräte im Werk konfigurieren. Wenn ein Gerät zum ersten Mal eingeschaltet wird, stellt es automatisch eine Verbindung mit einer IoT Central-Anwendung her. Ein IoT Central-Operator muss das Gerät genehmigen, bevor es Daten senden kann.

Im folgenden Diagramm ist dieser Ablauf dargestellt:

![Verbindungseinstellungen](media/concepts-connectivity/device-connection-flow1.png)

In den folgenden Schritten wird dieser Prozess detaillierter beschrieben. Die Schritte unterscheiden sich geringfügig, je nachdem, ob Sie SAS- oder X.509-Zertifikate für die Geräteauthentifizierung verwenden:

1. Konfigurieren der Verbindungseinstellungen:

    - **X.509-Zertifikate:** [Fügen Sie das Stamm- bzw. Zwischenzertifikat hinzu, und verifizieren Sie es](#connect-devices-using-x509-certificates). Verwenden Sie es dann im nächsten Schritt, um die Gerätezertifikate zu generieren.
    - **SAS:** Kopieren Sie den Primärschlüssel. Dieser Schlüssel ist der Gruppen-SAS-Schlüssel für die IoT Central-Anwendung. Verwenden Sie den Schlüssel im folgenden Schritt zum Generieren der Geräte-SAS-Schlüssel.
    ![Verbindungseinstellungen für SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Generieren Ihrer Geräteanmeldeinformationen
    - **X.509-Zertifikate:** Generieren Sie die untergeordneten Zertifikate für Ihre Geräte, indem Sie das Stamm- bzw. Zwischenzertifikat verwenden, das Sie Ihrer IoT Central-Anwendung hinzugefügt haben. Achten Sie darauf, dass Sie die **Geräte-ID** in Kleinbuchstaben in den untergeordneten Zertifikaten als CNAME verwenden. Verwenden Sie nur für Testzwecke [dieses Befehlszeilentool](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ), um Gerätezertifikate zu generieren.
    - **SAS:** Verwenden Sie dieses [Befehlszeilentool](https://www.npmjs.com/package/dps-keygen) zum Generieren von SAS-Schlüsseln für Geräte. Verwenden Sie die Gruppe **Primärschlüssel** aus dem vorherigen Schritt. Die Geräte-ID muss aus Kleinbuchstaben bestehen.

      Führen Sie zum Installieren des [Schlüsselgenerator-Hilfsprogramms](https://github.com/Azure/dps-keygen) den folgenden Befehl aus:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Um einen Geräteschlüssel aus dem SAS-Primärschlüssel der Gruppe zu generieren, führen Sie den folgenden Befehl aus:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Um Ihre Geräte einzurichten, speichern Sie auf jedem Gerät **Bereichs-ID**, **Geräte-ID** und **X.509-Gerätezertifikat** oder **SAS-Schlüssel**.

1. Schalten Sie dann das Gerät ein, damit es eine Verbindung mit Ihrer IoT Central-Anwendung herstellt. Wenn Sie ein Gerät einschalten, stellt es zuerst eine Verbindung mit DPS her, um seine IoT Central-Registrierungsinformationen abzurufen.

1. Das verbundene Gerät wird anfänglich als **Nicht zugeordnetes Gerät** auf der **Geräte-Explorer**-Seite angezeigt. Der Bereitstellungsstatus des Geräts lautet **Registriert**. Sie können das Gerät nun der entsprechenden Gerätevorlage **zuordnen** und es genehmigen, um die Verbindung mit Ihrer IoT Central-Anwendung herzustellen. Das Gerät kann anschließend eine Verbindungszeichenfolge von IoT Hub abrufen und beginnen, Daten zu senden. Die Gerätebereitstellung ist jetzt abgeschlossen, und der Bereitstellungsstatus lautet jetzt **Bereitgestellt**.

## <a name="provisioning-status"></a>Bereitstellungsstatus

Wenn ein reales Gerät eine Verbindung mit Ihrer IoT Central-Anwendung herstellt, ändert sich der Bereitstellungsstatus wie folgt:

1. Der Bereitstellungsstatus des Geräts lautet zuerst **Registriert**. Dieser Status bedeutet, dass das Gerät in IoT Central erstellt ist und über eine Geräte-ID verfügt. Ein Gerät ist registriert, wenn Folgendes zutrifft:
    - Ein neues echtes Gerät wird auf der Seite **Geräte-Explorer** hinzugefügt.
    - Eine Gruppe von Geräten wird mit **Importieren** auf der Seite **Geräte-Explorer** hinzugefügt.
    - Ein Gerät wurde nicht manuell auf der Seite **Geräte-Explorer** registriert, aber seine Verbindung mit gültigen Anmeldeinformationen hergestellt, und es wird als **nicht zugeordnetes** Gerät auf der Seite **Geräte-Explorer** angezeigt.

1. Der Bereitstellungsstatus des Geräts ändert sich in **Bereitgestellt**, wenn das Gerät, das mit gültigen Anmeldeinformationen eine Verbindung mit Ihrer IoT Central-Anwendung hergestellt hat, den Bereitstellungsschritt ausführt. In diesem Schritt ruft das Gerät eine Verbindungszeichenfolge von IoT Hub ab. Das Gerät kann jetzt eine Verbindung mit IoT Hub herstellen und beginnen, Daten zu senden.

1. Ein Operator kann ein Gerät blockieren. Wenn ein Gerät blockiert wird, kann es keine Daten an Ihre IoT Central-Anwendung senden. Blockierte Geräte haben den Bereitstellungsstatus **Blockiert**. Ein Operator muss das Gerät zurücksetzen, bevor es wieder Daten senden kann. Wenn ein Operator das Blockieren eines Geräts aufhebt, wird der Bereitstellungsstatus auf den vorherigen Wert zurückgesetzt – **Registriert** oder **Bereitgestellt**.

## <a name="sdk-support"></a>SDK-Unterstützung

Die Azure-Geräte-SDKs bieten Ihnen die einfachste Möglichkeit, Ihren Gerätecode zu implementieren. Derzeit sind folgende Geräte-SDKs verfügbar:

- [Azure IoT-SDK für C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT-SDK für Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT-SDK für Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT-SDK für Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT-SDK für .NET](https://github.com/azure/azure-iot-sdk-csharp)

Jedes Gerät stellt eine Verbindung über eine eindeutige Verbindungszeichenfolge her, die das Gerät identifiziert. Ein Gerät kann nur eine Verbindung mit dem IoT-Hub herstellen, bei dem es registriert ist. Wenn Sie ein reales Gerät in Ihrer Azure IoT Central-Anwendung erstellen, generiert die Anwendung die Informationen, die Sie zum Erstellen einer Verbindungszeichenfolge benötigen, mittels `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-Features und IoT Hub-Konnektivität

Bei der gesamten Gerätekommunikation mit IoT Hub werden die folgenden IoT Hub-Konnektivitätsoptionen verwendet:

- [Nachrichten, die von Geräten an die Cloud gesendet werden](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Gerätezwillinge](../iot-hub/iot-hub-devguide-device-twins.md)

In der folgenden Tabelle wird die Zuordnung von Azure IoT Central-Gerätefeatures zu IoT Hub-Features zusammengefasst:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Messung: Telemetrie | Nachrichten, die von Geräten an die Cloud gesendet werden |
| Geräteeigenschaften | Gemeldete Eigenschaften von Gerätezwillingen |
| Einstellungen | Gewünschte und gemeldete Eigenschaften von Gerätezwillingen |

Weitere Informationen zur Verwendung der Geräte-SDKs sowie Beispielcode finden Sie in einem der folgenden Artikel:

- [Verbinden eines generischen Node.js-Clients mit Ihrer Azure IoT Central-Anwendung](howto-connect-nodejs.md)
- [Verbinden eines Raspberry Pi-Geräts mit Ihrer Azure IoT Central-Anwendung](howto-connect-raspberry-pi-python.md)
- [Verbinden eines DevDiv-Kit-Geräts mit Ihrer Azure IoT Central-Anwendung](howto-connect-devkit.md)

### <a name="protocols"></a>Protokolle

Die Geräte-SDKs unterstützen die folgenden Netzwerkprotokolle zum Herstellen einer Verbindung mit einem IoT Hub:

- MQTT
- AMQP
- HTTPS

Informationen zu diesen verschiedenen Protokollen sowie eine Anleitung zu deren Auswahl finden Sie unter [Auswählen eines Kommunikationsprotokolls](../iot-hub/iot-hub-devguide-protocols.md).

Wenn Ihr Gerät keines der unterstützten Protokolle verwenden kann, können Sie mithilfe von Azure IoT Edge eine Protokollkonvertierung durchführen. IoT Edge unterstützt weitere Intelligence-on-the-Edge-Szenarien, um die Verarbeitung von der Azure IoT Central-Anwendung auf den Edge auszulagern.

## <a name="security"></a>Sicherheit

Alle Daten, die zwischen Geräten und Azure IoT Central ausgetauscht werden, werden verschlüsselt. IoT Hub authentifiziert jede Anforderung von einem Gerät, das eine Verbindung mit einem der geräteseitigen IoT Hub-Endpunkte herstellt. Um den unverschlüsselten Austausch von Anmeldeinformationen zu vermeiden, verwendet ein Gerät signierte Token zur Authentifizierung. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit der Gerätekonnektivität in Azure IoT Central vertraut gemacht haben, werden Ihnen als Nächstes die folgenden Schritte empfohlen:

- [Vorbereiten und Verbinden eines DevKit-Geräts](howto-connect-devkit.md)
- [Vorbereiten und Verbinden eines Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Verbinden eines generischen Node.js-Clients mit Ihrer Azure IoT Central-Anwendung](howto-connect-nodejs.md)
- [C SDK: Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) (C SDK: Bereitstellung des Geräteclient-SDK)
