---
title: Bereitstellungsoptionen
description: Erfahren Sie mehr über die ersten Schritte mit dem grundlegenden Workflow der Features und des Diensts von Defender für IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 6aa525fd7f2d82194baa2e2a0c910cb71509c2d5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340015"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Erste Schritte mit Azure Defender für IoT

In diesem Artikel werden die Bereitstellungs- und Onboardingprozesse beschrieben, die zum Ausführen von Azure Defender für IoT erforderlich sind. Weitere Schritte sind ebenfalls erforderlich. Es wird empfohlen, diese sich mit diesen Schritten auseinanderzusetzen und sich mit den Informationen in der Begleitdokumentation vertraut zu machen.

Nachdem Sie alle Schritte ausgeführt haben, wird Ihr Netzwerk von Azure Defender für IoT-Sensoren überwacht. Je nachdem, wie Sie Ihre Lösung einrichten, können Erkennungen auch an die lokale Verwaltungskonsole oder an die IoT Hub-Instanz gesendet werden.

Führen Sie die folgenden Schritte aus, um Azure Defender für IoT einzurichten und auszuführen.

## <a name="1-set-up-azure"></a>1. Einrichten von Azure

- Richten Sie ein Azure-Konto ein. Weitere Informationen finden Sie unter [Erstellen eines Azure-Kontos](/learn/modules/create-an-azure-account/).

- Firewall oder Proxy: Wenn Sie eine Firewall oder ein ähnliches zwischengeschaltetes Netzwerkgerät haben, das nur bestimmte Verbindungen zulässt, überprüfen Sie, ob „*.azure-devices.net:443“ für die Firewall oder den Proxy geöffnet ist. Wenn keine Platzhalter unterstützt werden oder Sie mehr Kontrolle wünschen, sollte der spezifische IoT Hub-FQDN in der Firewall bzw. dem Proxy geöffnet werden. Weitere Informationen finden Sie unter [Referenz: IoT Hub-Endpunkte](../iot-hub/iot-hub-devguide-endpoints.md).

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. Bereitstellen von Hardware und Software sowie Onboarding auf dem Sensor

- Erwerben Sie die Sensorhardware, und installieren Sie die Software. Führen Sie die hier beschriebenen Schritte aus. Weitere Informationen finden Sie in diesem Artikel und im [Hardwarehandbuch zu Defender für IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) sowie im [Installationshandbuch](https://aka.ms/AzureDefenderforIoTInstallSensorISO).

  - Notieren Sie sich nach der Installation die Anmeldeinformationen für den Sensor. Sie benötigen die Anmeldeinformationen, um die Aktivierungsdatei in den Sensor hochzuladen.

  - Wenn Sie mit Sensoren arbeiten, die lokal verwaltet werden, notieren Sie die IP-Adresse des Sensors oder den Sensornamen, der in der Installation festgelegt ist. Sie können die Information bei der Erstellung eines Sensornamens während der Sensorregistrierung im Defender für IoT-Portal verwenden. Diese können Sie später nutzen, um eine einfachere Nachverfolgung und konsistente Benennung zwischen dem Registrierungsnamen im Azure Defender für IoT-Portal und der IP-Adresse des bereitgestellten Sensors zu ermöglichen, der in der Sensorkonsole angezeigt wird.

- Registrieren Sie den Sensor beim Defender für IoT-Portal, und laden Sie eine Sensoraktivierungsdatei herunter.

- Laden Sie die Aktivierungsdatei auf Ihren Sensor hoch.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. Netzwerkeinrichtung für die Sensorüberwachung und -verwaltung

- Verbinden Sie den Sensor mit dem Netzwerk. Die Beschreibung finden Sie im [Handbuch zur Netzwerkeinrichtung](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="4-start-discovering-your-network"></a>4. Starten der Netzwerkerkennung

- Optimieren Sie die Systemeinstellungen in der Sensorkonsole.

- Verbinden Sie Sensoren mit einer lokalen Verwaltungskonsole.

Weitere Informationen finden Sie im [Benutzerhandbuch zum Azure Defender für IoT-Sensor](https://aka.ms/AzureDefenderforIoTUserGuide) und im [Benutzerhandbuch für die Defender für IoT-Verwaltungskonsole](https://aka.ms/DefenderForIoTManagementConsole).

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. Senden von Warnungsinformationen an Azure Sentinel

- Konfigurieren Sie Azure Sentinel so, dass Warnungsinformationen an Azure Sentinel gesendet werden: [Verbinden Ihrer Daten aus Defender für IoT mit Azure Sentinel](how-to-configure-with-sentinel.md).
 

## <a name="next-steps"></a>Nächste Schritte

- Aktivieren von [Defender für IoT](quickstart-onboard-iot-hub.md)
- [Quickstart: Configure your IoT solution](quickstart-configure-your-solution.md) (Schnellstart: Konfigurieren Ihrer IoT-Lösung)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Schnellstart: Erstellen eines azureiotsecurity-Modulzwillings)
- [Quickstart: Create custom alerts](quickstart-create-custom-alerts.md) (Schnellstart: Erstellen benutzerdefinierter Benachrichtigungen)
- [Select and deploy a security agent on your IoT device](how-to-deploy-agent.md) (Auswählen und Bereitstellen eines Sicherheits-Agents auf Ihrem IoT-Gerät)