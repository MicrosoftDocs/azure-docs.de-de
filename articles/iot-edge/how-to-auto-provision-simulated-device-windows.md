---
title: Automatische Bereitstellung von Windows-Geräten mit DPS – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden eines simulierten Geräts auf Ihrem Windows-Computer zum Testen der automatischen Gerätebereitstellung für Azure IoT Edge mithilfe des Device Provisioning-Diensts
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 01247dfc0046ef722d70fe48f7ab8ee63f685962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153572"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Erstellen und Bereitstellen eines simulierten TPM-Edge-Geräts unter Windows

Azure IoT Edge-Geräte können genau wie nicht Edge-fähige Geräte mit dem [Device Provisioning-Dienst](../iot-dps/index.yml) automatisch bereitgestellt werden. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie die Informationen unter [Konzepte für die automatische Bereitstellung](../iot-dps/concepts-auto-provisioning.md), bevor Sie fortfahren.

Dieser Artikel beschreibt, wie Sie anhand der folgenden Schritte die automatische Bereitstellung auf einem simulierten Edge-Gerät testen:

* Erstellen Sie eine neue Instanz für den IoT Hub Device Provisioning-Dienst (DPS).
* Erstellen Sie ein simuliertes Gerät auf Ihrem Windows-Computer (VM) mit einem simulierten Trusted Platform Module (TPM) für die Hardwaresicherheit.
* Erstellen Sie eine individuelle Registrierung für das Gerät.
* Installieren Sie IoT Edge-Runtime, und verbinden Sie das Gerät mit einem IoT Hub.

## <a name="prerequisites"></a>Voraussetzungen

* Windows-Entwicklungscomputer. In diesem Artikel wird Windows 10 verwendet.
* Aktiver IoT Hub.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Einrichten des IoT Hub Device Provisioning Service

Erstellen Sie eine neue Instanz des IoT Hub Device Provisioning Service in Azure, und verknüpfen Sie sie mit Ihrem IoT-Hub. Befolgen Sie die Anweisungen unter [Einrichten des IoT Hub Device Provisioning Service](../iot-dps/quick-setup-auto-provision.md).

Nachdem Sie den Device Provisioning-Dienst ausgeführt haben, kopieren Sie den Wert von **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

## <a name="simulate-a-tpm-device"></a>Simulieren eines TPM-Geräts

Erstellen Sie ein simuliertes TPM-Gerät auf dem Windows-Entwicklungscomputer. Rufen Sie die **Registrierungs-ID** und den **Endorsement Key** für Ihr Gerät ab, und verwenden Sie die Werte, um einen individuellen Registrierungseintrag im DPS zu erstellen.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](how-to-deploy-monitor.md) verwendet.

Wählen Sie die SDK-Sprache für die Erstellung des simulierten Geräts, und führen Sie die Schritte durch, um die individuelle Registrierung zu erstellen.

Wenn Sie die individuelle Registrierung erstellt haben, wählen Sie **Aktivieren** aus, um anzugeben, dass das simulierte TPM-Gerät auf Ihrem Windows-Entwicklungscomputer ein **IoT Edge-Gerät** ist.

Simuliertes Gerät und Anleitungen für die individuelle Registrierung:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Nachdem Sie die individuelle Registrierung erstellt haben, speichern Sie den Wert der **Registrierungs-ID**. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.

## <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

Nach Abschluss des vorherigen Abschnitts sollte Ihr neues Gerät als IoT Edge-Gerät unter Ihrem IoT Hub aufgeführt werden. Anschließend müssen Sie die IoT Edge-Runtime auf Ihrem Gerät installieren.

Die IoT Edge-Runtime wird auf allen IoT Edge-Geräten bereitgestellt. Die Komponenten werden in Containern ausgeführt, und Sie können weitere Container auf dem Gerät bereitstellen, um Code im Edge-Bereich auszuführen.  

Führen Sie die Anweisungen zum Installieren von IoT Edge-Runtime auf dem Gerät aus, auf dem die simulierte TPM aus dem vorherigen Abschnitt ausgeführt wird. Stellen Sie sicher, dass Sie die IoT Edge-Runtime für die automatische und nicht für die manuelle Bereitstellung konfigurieren.

Sie müssen Ihren DPS-**ID-Bereich** und die Geräte-**Registrierungs-ID** kennen, bevor Sie IoT Edge auf Ihrem Gerät installieren.

[Installation und automatische Bereitstellung von IoT Edge](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Bestätigen einer erfolgreichen Installation

Wenn die Runtime erfolgreich gestartet wurde, können Sie zu Ihrem IoT Hub navigieren und mit dem Bereitstellen von IoT Edge-Modulen auf Ihrem Gerät beginnen. Verwenden Sie die folgenden Befehle auf Ihrem Gerät, um zu überprüfen, ob das Installieren und Starten der Runtime erfolgreich war.  

Überprüfen Sie den Status des IoT Edge-Diensts.

```powershell
Get-Service iotedge
```

Untersuchen Sie die Dienstprotokolle der letzten fünf Minuten.


```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Führen Sie ausgeführte Module auf.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nächste Schritte

Der Registrierungsprozess des Device Provisioning-Diensts ermöglicht Ihnen, die Geräte-ID und die Tags von Gerätezwillingen beim Bereitstellen des neuen Geräts zu sehen. Sie können diese Werte verwenden, um einzelne Geräte oder Gruppen von Geräten über die automatische Geräteverwaltung als Ziel festzulegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-monitor.md) oder [Verwenden der Azure-Befehlszeilenschnittstelle](how-to-deploy-monitor-cli.md)
