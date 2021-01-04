---
title: Gerätekonnektivität in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel werden die wichtigsten Konzepte in Bezug auf die Gerätekonnektivität in Azure IoT Central vorgestellt.
author: dominicbetts
ms.author: dobett
ms.date: 10/22/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 1a050daa3a4b3ae9be5ef40961c40adaa90dc72b
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121808"
---
# <a name="get-connected-to-azure-iot-central"></a>Herstellen einer Verbindung mit Azure IoT Central

*Dieser Artikel gilt für Operatoren und Geräteentwickler.*

In diesem Artikel wird beschrieben, wie Geräte die Verbindung mit einer Azure IoT Central-Anwendung herstellen. Bevor ein Gerät Daten mit IoT Central austauschen kann, muss es folgende Aktionen ausführen:

- *Authentifizieren*. Bei der Authentifizierung bei der IoT Central-Anwendung wird entweder ein _Shared Access Signature-Token (SAS)_ oder ein _X.509-Zertifikat_ verwendet. In Produktionsumgebungen werden X.509-Zertifikate empfohlen.
- *Registrieren*. Geräte müssen bei der IoT Central-Anwendung registriert werden. Sie können registrierte Geräte auf der Seite **Geräte** in der Anwendung anzeigen.
- *Zuordnen zu einer Gerätevorlage*. In einer IoT Central-Anwendung definieren Gerätevorlagen die Benutzeroberfläche, die von Operatorn zum Anzeigen und Verwalten verbundener Geräte verwendet wird.

IoT Central unterstützt die folgenden zwei Geräteregistrierungsszenarien:

- *Automatische Registrierung*. Das Gerät wird beim ersten Herstellen der Verbindung automatisch registriert. Dieses Szenario ermöglicht OEMs außerdem die Massenfertigung von Geräten, die eine Verbindung herstellen können, ohne zuvor registriert zu werden. Ein OEM generiert geeignete Anmeldeinformationen und konfiguriert die Geräte im Werk. Optional können Sie festlegen, dass ein Operator das Gerät genehmigen muss, bevor es mit dem Senden von Daten beginnt. In diesem Szenario müssen Sie eine X.509- oder SAS-_Gruppenregistrierung_ in der Anwendung konfigurieren.
- *Manuelle Registrierung*. Operator registrieren einzelne Geräte auf der Seite **Geräte** oder [importieren eine CSV-Datei](howto-manage-devices.md#import-devices) zur Massenregistrierung von Geräten. In diesem Szenario können Sie eine X.509- oder SAS-_Gruppenregistrierung_ oder _individuelle X.509- oder SAS-Registrierung_ verwenden.

Geräte, die eine Verbindung mit IoT Central herstellen, sollten die *IoT Plug & Play-Konventionen* befolgen. Eine dieser Konventionen ist, dass ein Gerät die _Modell-ID_ des Gerätemodells senden soll, das beim Herstellen der Verbindung implementiert wird. Die Modell-ID ermöglicht der IoT Central-Anwendung, das Gerät der richtigen Gerätevorlage zuzuordnen.

IoT Central nutzt für die Verwaltung des Verbindungsvorgangs den [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md). Ein Gerät stellt zunächst eine Verbindung mit einem DPS-Endpunkt her, um die Informationen abzurufen, die für die Verbindung mit Ihrer Anwendung erforderlich sind. Intern verarbeitet Ihre IoT Central Anwendung Verbindungen mit Geräten über einen IoT-Hub. DPS ermöglicht Folgendes:

- IoT Central kann Onboarding und das Verbinden von Geräten nach Maß unterstützen.
- Sie können jetzt Geräteanmeldeinformationen generieren und die Geräte offline konfigurieren, ohne sie über IoT Central zu registrieren.
- Sie können Ihre eigenen Geräte-IDs verwenden, um Geräte in IoT Central zu registrieren. Die Verwendung Ihrer eigenen Geräte-IDs vereinfacht die Integration in vorhandene Backofficesysteme.
- Eine einheitliche Möglichkeit, Geräte mit IoT Central zu verbinden.

In diesem Artikel werden die folgenden Schritte der Geräteverbindung beschrieben:

- [Gruppenregistrierung mit X.509](#x509-group-enrollment)
- [Gruppenregistrierung mit SAS](#sas-group-enrollment)
- [Individuelle Registrierung](#individual-enrollment)
- [Geräteregistrierung](#device-registration)
- [Zuordnen eines Geräts zu einer Gerätevorlage](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>Gruppenregistrierung mit X.509

In einer Produktionsumgebung ist die Verwendung von X.509-Zertifikaten der empfohlene Mechanismus zur Geräteauthentifizierung für IoT Central. Weitere Informationen finden Sie unter [Geräteauthentifizierung mit X.509-Zertifikaten](../../iot-hub/iot-hub-x509ca-overview.md).

So stellen Sie eine Verbindung eines Geräts mit einem X.509-Zertifikat mit Ihrer Anwendung her:

1. Erstellen Sie eine *Registrierungsgruppe*, die den Nachweistyp **X.509-Zertifikate** verwendet.
1. Fügen Sie ein X.509-Zwischen- oder Stammzertifikat in der Registrierungsgruppe hinzu, und überprüfen Sie es.
1. Generieren Sie ein untergeordnetes Zertifikat aus dem Stamm- oder Zwischenzertifikat in der Registrierungsgruppe. Senden Sie das untergeordnete Zertifikat vom Gerät aus, wenn es eine Verbindung mit der Anwendung herstellt.

Weitere Informationen finden Sie unter [Informationen zum Verbinden von Geräten mit X.509-Zertifikaten mithilfe des Node.js-Geräte-SDKs für IoT Central-Anwendung](how-to-connect-devices-x509.md).

### <a name="for-testing-purposes-only"></a>Nur für Testzwecke

Mit den folgenden Hilfsprogrammen können Sie ausschließlich zu Testzwecken Stamm-, Zwischen- und Gerätezertifikate generieren:

- [Tools for the Azure IoT Device Provisioning Device SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md) (Tools für das Azure IoT Device Provisioning-Geräte-SDK): Sammlung von Node.js-Tools, mit denen Sie X.509-Zertifikate und -Schlüssel generieren und überprüfen können.
- Wenn Sie ein DevKit-Gerät verwenden, generiert dieses [Befehlszeilentool](https://aka.ms/iotcentral-docs-dicetool) ein Zertifizierungsstellenzertifikat, das Sie Ihrer IoT Central-Anwendung zum Überprüfen der Zertifikate hinzufügen können.
- [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (Verwalten von Test-Zertifizierungsstellenzertifikaten für Beispiele und Tutorials): Sammlung von PowerShell- und Bash-Skripts für folgende Zwecke:
  - Erstellen einer Zertifikatkette.
  - Speichern Sie die Zertifikate als CER-Dateien zum Hochladen in Ihre IoT Central-Anwendung.
  - Verwenden Sie den Überprüfungscode aus der IoT Central-Anwendung, um das Verifizierungszertifikat zu generieren.
  - Erstellen Sie untergeordnete Zertifikate für Ihre Geräte unter Verwendung Ihrer Geräte-IDs als Parameter für das Tool.

## <a name="sas-group-enrollment"></a>Gruppenregistrierung mit SAS

So stellen Sie eine Verbindung eines Geräts mit einem Geräte-SAS-Schlüssel mit Ihrer Anwendung her:

1. Erstellen Sie eine *Registrierungsgruppe*, die den Nachweistyp **Shared Access Signature (SAS)** verwendet.
1. Kopieren Sie den Gruppenprimärschlüssel oder -sekundärschlüssel aus der Registrierungsgruppe.
1. Verwenden Sie die Azure CLI, um einen Geräteschlüssel aus dem Gruppenschlüssel zu generieren:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Verwenden Sie den generierten Geräteschlüssel, wenn das Gerät eine Verbindung mit ihrer IoT Central-Anwendung herstellt.

## <a name="individual-enrollment"></a>Individuelle Registrierung

Kunden, die Geräte mit gerätespezifischen Authentifizierungsanmeldeinformationen verbinden, verwenden individuelle Registrierungen. Eine individuelle Registrierung ist ein Eintrag für ein einzelnes Gerät, das verbunden werden darf. Individuelle Registrierungen verwenden als Nachweismechanismen X.509-Blattzertifikate oder SAS-Token (von einem physischen oder virtuellen Trusted Platform Module). Eine Geräte-ID kann Buchstaben, Ziffern und das Zeichen `-` enthalten. Weitere Informationen finden Sie unter [Individuelle DPS-Registrierung](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Wenn Sie eine individuelle Registrierung für ein Gerät erstellen, hat diese Vorrang vor den Standardoptionen für Gruppenregistrierungen in Ihrer IoT Central-Anwendung.

### <a name="create-individual-enrollments"></a>Erstellen individueller Registrierungen

IoT Central unterstützt die folgenden Nachweismechanismen für individuelle Registrierungen:

- **Nachweis des symmetrischen Schlüssels:** Der Nachweis des symmetrischen Schlüssels ist eine einfache Methode zum Authentifizieren eines Geräts bei einer DPS-Instanz. Öffnen Sie zum Erstellen einer individuellen Registrierung, bei der symmetrische Schlüssel verwendet werden, die Seite **Geräteverbindung** für das Gerät, und wählen Sie **Individuelle Registrierung** als Verbindungsmethode und **Shared Access Signature (SAS)** als Mechanismus aus. Geben Sie Base64-codierte primäre und sekundäre Schlüssel ein, und speichern Sie die Änderungen. Verwenden Sie den **ID-Bereich**, die **Geräte-ID** und den primären oder den sekundären Schlüssel, um Ihr Gerät zu verbinden.

    > [!TIP]
    > Zum Testen können Sie **OpenSSL** verwenden, um Base64-codierte Schlüssel zu generieren: `openssl rand -base64 64`

- **X.509-Zertifikate:** Öffnen Sie zum Erstellen einer individuellen Registrierung mit X.509-Zertifikaten die Seite **Geräteverbindung**, und wählen Sie **Individuelle Registrierung** als Verbindungsmethode und **Zertifikate (X.509)** als Mechanismus aus. Für Gerätezertifikate, die mit einem Eintrag für eine individuelle Registrierung verwendet werden, müssen der Aussteller und der Antragsteller-CN auf die Geräte-ID festgelegt werden.

    > [!TIP]
    > Zum Testen können Sie die [Tools für das Azure IoT Device Provisioning-Geräte-SDK für Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) verwenden, um ein selbstsigniertes Zertifikat zu generieren: `node create_test_cert.js device "mytestdevice"`

- **Nachweis mit Trusted Platform Module (TPM):** Ein [TPM](../../iot-dps/concepts-tpm-attestation.md) ist ein Typ von Hardwaresicherheitsmodulen. Die Verwendung eines TPM stellt eine der sichersten Möglichkeiten dar, ein Gerät zu verbinden. In diesem Artikel wird davon ausgegangen, dass Sie ein diskretes, integriertes oder Firmware-TPM verwenden. Durch Software emulierte TPMs eignen sich gut für die Prototyperstellung oder für Tests, bieten aber nicht das gleiche Maß an Sicherheit wie diskrete, integrierte oder Firmware-TPMs. Verwenden Sie keine Software-TPMs in Produktionsumgebungen. Öffnen Sie zum Erstellen einer individuellen Registrierung mit TPM die Seite **Geräteverbindung**, und wählen Sie **Individuelle Registrierung** als Verbindungsmethode und **TPM** als Mechanismus aus. Geben Sie den TPM-Endorsement Key ein, und speichern Sie die Geräteverbindungsinformationen.

## <a name="device-registration"></a>Geräteregistrierung

Bevor ein Gerät eine Verbindung mit einer IoT Central-Anwendung herstellen kann, muss es in der Anwendung registriert werden:

- Geräte können sich automatisch registrieren, wenn sie zum ersten Mal eine Verbindung herstellen. Um diese Option verwenden zu können, müssen Sie entweder [X. 509-Gruppenregistrierung](#x509-group-enrollment) oder [SAS-Gruppenregistrierung](#sas-group-enrollment) verwenden.
- Ein Operator kann eine CSV-Datei importieren, um eine Massenregistrierung einer Liste von Geräten in der Anwendung durchzuführen.
- Ein Operator kann ein einzelnes Gerät manuell auf der Seite **Geräte** in der Anwendung registrieren.

IoT Central ermöglicht OEMs die Massenfertigung von Geräten, die sich automatisch registrieren können. Ein OEM generiert geeignete Anmeldeinformationen und konfiguriert die Geräte im Werk. Wenn ein Kunde ein Gerät erstmalig einschaltet, stellt es eine Verbindung mit DPS her und wird dadurch automatisch mit der richtigen IoT Central-Anwendung verbunden. Optional können Sie festlegen, dass ein Operator das Gerät genehmigen muss, bevor es mit dem Senden von Daten an die Anwendung beginnt.

> [!TIP]
> Auf der Seite **Verwaltung > Geräteverbindung** steuert die Option **Automatisch genehmigen**, ob ein Operator das Gerät manuell genehmigen muss, bevor es mit dem Senden von Daten beginnen kann.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Automatisches Registrieren von Geräten, die X.509-Zertifikate verwenden

1. Generieren Sie die untergeordneten Zertifikate für Ihre Geräte mit dem Stamm- oder Zwischenzertifikat, das Sie in Ihre [X.509-Registrierungsgruppe](#x509-group-enrollment) hochgeladen haben. Verwenden Sie in den Blattzertifikaten Geräte-IDs als `CNAME`. Eine Geräte-ID kann Buchstaben, Ziffern und das Zeichen `-` enthalten.

1. Schreiben Sie als OEM für jedes Gerät eine Geräte-ID, ein generiertes untergeordnetes X.509-Zertifikat und den **ID-Bereich**-Wert der Anwendung in den Flashspeicher. Der Gerätecode sollte auch die Modell-ID des Gerätemodells senden, das er implementiert.

1. Wenn Sie ein Gerät einschalten, stellt es zuerst eine Verbindung mit DPS her, um seine IoT Central-Verbindungsinformationen abzurufen.

1. Das Gerät verwendet die Informationen von DPS, um eine Verbindung mit Ihrer IoT Central-Anwendung herzustellen und sich bei ihr zu registrieren.

Die IoT Central-Anwendung verwendet die vom Gerät gesendete Modell-ID, um [das registrierte Gerät einer Gerätevorlage zuzuordnen](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Automatisches Registrieren von Geräten, die SAS-Token verwenden

1. Kopieren Sie den Gruppenprimärschlüssel aus der **SAS-IoT-Devices**-Registrierungsgruppe:

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Gruppenprimärschlüssel aus der SAS-IoT-Devices-Registrierungsgruppe":::

1. Verwenden Sie zum Generieren der SAS-Schlüssel für das Gerät den Befehl `az iot central device compute-device-key`. Verwenden Sie den primären Gruppenschlüssel aus dem vorherigen Schritt. Die Geräte-ID kann Buchstaben, Ziffern und das Zeichen `-` enthalten:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Schreiben Sie als OEM für jedes Gerät die Geräte-ID, den generierten Geräte-SAS-Schlüssel und den **ID-Bereich**-Wert der Anwendung in den Flashspeicher. Der Gerätecode sollte auch die Modell-ID des Gerätemodells senden, das er implementiert.

1. Wenn Sie ein Gerät einschalten, stellt es zuerst eine Verbindung mit DPS her, um seine IoT Central-Registrierungsinformationen abzurufen.

1. Das Gerät verwendet die Informationen von DPS, um eine Verbindung mit Ihrer IoT Central-Anwendung herzustellen und sich bei ihr zu registrieren.

Die IoT Central-Anwendung verwendet die vom Gerät gesendete Modell-ID, um [das registrierte Gerät einer Gerätevorlage zuzuordnen](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Registrieren einer großen Zahl von Geräten im voraus

Um eine große Anzahl von Geräten bei Ihrer IoT Central-Anwendung zu registrieren, verwenden Sie eine CSV-Datei zum [Importieren der Geräte-IDs und Gerätenamen](howto-manage-devices.md#import-devices).

Wenn Ihre Geräte SAS-Token für die Authentifizierung verwenden, [exportieren Sie eine CSV-Datei aus Ihrer IoT Central-Anwendung](howto-manage-devices.md#export-devices). Die exportierte CSV-Datei enthält die Geräte-IDs und die SAS-Schlüssel.

Wenn Ihre Geräte X.509-Zertifikate für die Authentifizierung verwenden, generieren Sie untergeordnete X.509-Zertifikate für Ihre Geräte mit dem Stamm- oder Zwischenzertifikat, das Sie in Ihre X.509-Registrierungsgruppe hochgeladen haben. Verwenden Sie die Geräte-IDs, die Sie als `CNAME`-Wert in die untergeordneten Zertifikate importiert haben.

Geräte müssen den **ID-Bereich**-Wert für Ihre Anwendung verwenden und eine Modell-ID senden, wenn sie eine Verbindung herstellen.

> [!TIP]
> Den **ID-Bereich**-Wert finden Sie unter **Verwaltung > Geräteverbindung**.

### <a name="register-a-single-device-in-advance"></a>Registrieren eines einzelnen Geräts im voraus

Dieser Ansatz ist nützlich, wenn Sie mit IoT Central experimentieren oder Geräte testen. Wählen Sie **+ Neu** auf der Seite **Geräte** aus, um ein einzelnes Gerät zu registrieren. Sie können die SAS-Schlüssel für Geräteverbindungen verwenden, um das Gerät mit Ihrer IoT Central-Anwendung zu verbinden. Kopieren Sie den _Geräte-SAS-Schlüssel_ aus den Verbindungsinformationen für ein registriertes Gerät:

![SAS-Schlüssel für ein einzelnes Gerät](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Zuordnen eines Geräts zu einer Gerätevorlage

Wenn das Gerät eine Verbindung herstellt, ordnet IoT Central automatisch einem Gerät eine Gerätevorlage zu. Ein Gerät sendet eine Modell-ID, wenn es eine Verbindung herstellt. IoT Central verwendet die Modell-ID, um die Gerätevorlage für dieses bestimmte Gerätemodell zu identifizieren. Der Ermittlungsvorgang funktioniert wie folgt:

1. Wenn die Gerätevorlage bereits in der IoT Central-Anwendung veröffentlicht ist, ist das Gerät der Gerätevorlage zugeordnet.
1. Wenn die Gerätevorlage nicht bereits in der IoT Central-Anwendung veröffentlicht ist, sucht IoT Central im öffentlichen Modellrepository nach dem Gerätemodell. Wenn IoT Central das Modell findet, wird es verwendet, um eine einfache Gerätevorlage zu generieren.
1. Wenn IoT Central das Modell nicht im öffentlichen Modellrepository findet, wird das Gerät als **Nicht zugeordnet** gekennzeichnet. Ein Operator kann eine Gerätevorlage für das Gerät erstellen und dann das nicht zugeordnete Gerät zur neuen Gerätevorlage migrieren.

## <a name="device-status-values"></a>Werte für den Gerätestatus

Wenn ein echtes Gerät eine Verbindung mit Ihrer IoT Central-Anwendung herstellt, ändert sich der Gerätestatus wie folgt:

1. Der Gerätestatus lautet zuerst **Registriert**. Dieser Status bedeutet, dass das Gerät in IoT Central erstellt ist und über eine Geräte-ID verfügt. Ein Gerät ist registriert, wenn Folgendes zutrifft:
    - Ein neues echtes Gerät wird auf der Seite **Geräte** hinzugefügt.
    - Eine Gruppe von Geräten wird auf der Seite **Geräte** mithilfe der Option **Importieren** hinzugefügt.

1. Der Gerätestatus ändert sich in **Bereitgestellt**, wenn das Gerät, das mit gültigen Anmeldeinformationen eine Verbindung mit Ihrer IoT Central-Anwendung hergestellt hat, den Bereitstellungsschritt abschließt. In diesem Schritt verwendet das Gerät DPS, um automatisch eine Verbindungszeichenfolge vom IoT-Hub Ihrer IoT Central-Anwendung abzurufen. Das Gerät kann jetzt eine Verbindung mit IoT Central herstellen und Daten senden.

1. Ein Operator kann ein Gerät blockieren. Wenn ein Gerät blockiert wird, kann es keine Daten an Ihre IoT Central-Anwendung senden. Blockierte Geräte weisen den Status **Blockiert** auf. Ein Operator muss das Gerät zurücksetzen, bevor es wieder Daten senden kann. Wenn ein Operator die Blockierung eines Geräts aufhebt, wird der Status auf den vorherigen Wert (**Registriert** oder **Bereitgestellt**) zurückgesetzt.

1. Wenn der Gerätestatus **Warten auf Genehmigung** lautet, ist die Option **Automatische Genehmigung** deaktiviert. Ein Operator muss ein Gerät explizit genehmigen, bevor es mit dem Senden von Daten beginnt. Geräte, die auf der Seite **Geräte** nicht manuell registriert wurden, aber eine Verbindung mit gültigen Anmeldeinformationen hergestellt haben, weisen den Gerätestatus **Warten auf Genehmigung** auf. Diese Geräte können auf der Seite **Geräte** mithilfe der Schaltfläche **Genehmigen** von Operatoren genehmigt werden.

1. Wenn der Gerätestatus **Nicht zugeordnet** lautet, ist den Geräten, die eine Verbindung mit IoT Central herstellen, keine Gerätevorlage zugeordnet. Diese Situation ist in den folgenden Szenarien typisch:

    - Eine Gruppe von Geräten wurde auf der Seite **Geräte** mithilfe der Option **Importieren** hinzugefügt, ohne die Gerätevorlage anzugeben.
    - Ein Geräte wurde auf der Seite **Geräte** manuell hinzugefügt, ohne die Gerätevorlage anzugeben. Das Gerät stellte dann eine Verbindung mit gültigen Anmeldeinformationen her.  

    Der Operator kann auf der Seite **Geräte** mithilfe der Schaltfläche **Migrieren** einem Gerät eine Gerätevorlage zuordnen.

## <a name="best-practices"></a>Bewährte Methoden

Sie sollten die Geräteverbindungszeichenfolge, die von DPS zurückgegeben wird, wenn Sie das Gerät zum ersten Mal verbinden, nicht speichern oder zwischenspeichern. Um eine Verbindung mit einem Gerät wiederherzustellen, durchlaufen Sie den Standardflow für die Geräteregistrierung, um die richtige Geräteverbindungszeichenfolge zu erhalten. Wenn das Gerät die Verbindungszeichenfolge zwischenspeichert, besteht die Gefahr einer veralteten Verbindungszeichenfolge in der Gerätesoftware. Wenn IoT Central die zugrunde liegende verwendete Azure IoT-Hub-Instanz aktualisiert, kann ein Gerät mit einer veralteten Verbindungszeichenfolge keine Verbindung herstellen.

## <a name="sdk-support"></a>SDK-Unterstützung

Die Azure-Geräte-SDKs bieten Ihnen die einfachste Möglichkeit, Ihren Gerätecode zu implementieren. Derzeit sind folgende Geräte-SDKs verfügbar:

- [Azure IoT-SDK für C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT-SDK für Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT-SDK für Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT-SDK für Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT-SDK für .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-Features und IoT Hub-Konnektivität

Bei der gesamten Gerätekommunikation mit IoT Hub werden die folgenden IoT Hub-Konnektivitätsoptionen verwendet:

- [Nachrichten, die von Geräten an die Cloud gesendet werden](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Gerätezwillinge](../../iot-hub/iot-hub-devguide-device-twins.md)

In der folgenden Tabelle wird die Zuordnung von Azure IoT Central-Gerätefeatures zu IoT Hub-Features zusammengefasst:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetrie | Nachrichten, die von Geräten an die Cloud gesendet werden |
| Eigenschaft | Gemeldete Eigenschaften von Gerätezwillingen |
| Eigenschaft (schreibbar) | Gewünschte und gemeldete Eigenschaften von Gerätezwillingen |
| Get-Help | Direkte Methoden |

### <a name="protocols"></a>Protokolle

Die Geräte-SDKs unterstützen die folgenden Netzwerkprotokolle zum Herstellen einer Verbindung mit einem IoT Hub:

- MQTT
- AMQP
- HTTPS

Informationen zu diesen verschiedenen Protokollen sowie eine Anleitung zu deren Auswahl finden Sie unter [Auswählen eines Kommunikationsprotokolls](../../iot-hub/iot-hub-devguide-protocols.md).

Wenn Ihr Gerät keines der unterstützten Protokolle verwenden kann, führen Sie mithilfe von Azure IoT Edge eine Protokollkonvertierung durch. IoT Edge unterstützt weitere Intelligence-on-the-Edge-Szenarien, um die Verarbeitung von der Azure IoT Central-Anwendung auszulagern.

## <a name="security"></a>Sicherheit

Alle Daten, die zwischen Geräten und Azure IoT Central ausgetauscht werden, werden verschlüsselt. IoT Hub authentifiziert jede Anforderung von einem Gerät, das eine Verbindung mit einem der geräteseitigen IoT Hub-Endpunkte herstellt. Um den unverschlüsselten Austausch von Anmeldeinformationen zu vermeiden, verwendet ein Gerät signierte Token zur Authentifizierung. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie ein Geräteentwickler sind, werden einige der folgenden nächsten Schritte empfohlen:

- Beispielcode, der die Verwendung von SAS-Token zeigt, finden Sie in [Tutorial: Erstellen einer Node.js-Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung (Node.js)](tutorial-connect-device.md)
- Erfahren Sie mehr über [Informationen zum Verbinden von Geräten mit X.509-Zertifikaten mithilfe des Node.js-Geräte-SDK für IoT Central-Anwendung](how-to-connect-devices-x509.md).
- Informieren Sie sich über [Überwachen der Gerätekonnektivität per Azure CLI](./howto-monitor-devices-azure-cli.md).
- Informieren Sie sich über [Definieren eines neuen IoT-Gerätetyps in Ihrer Azure IoT Central-Anwendung](./howto-set-up-template.md).
- Informieren Sie sich über [Azure IoT Edge-Geräte und Azure IoT Central](./concepts-iot-edge.md).
