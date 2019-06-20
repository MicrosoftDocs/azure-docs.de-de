---
title: Includedatei
description: Includedatei
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162597"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Als Nächstes erstellen Sie eine Geräteidentität und speichern den zugehörigen Schlüssel für die spätere Verwendung. Diese Geräteidentität wird von der Simulationsanwendung verwendet, um Nachrichten an die IoT Hub-Instanz zu senden. Diese Funktion ist in PowerShell oder bei Verwendung einer Azure Resource Manager-Vorlage nicht verfügbar. Die folgenden Schritte zeigen, wie Sie das simulierte Gerät über das [Azure-Portal](https://portal.azure.com) erstellen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und melden Sie sich bei Ihrem Azure-Konto an.

2. Wählen Sie **Ressourcengruppen** und anschließend Ihre Ressourcengruppe aus. In diesem Tutorial wird **ContosoResources** verwendet.

3. Wählen Sie in der Ressourcenliste Ihre IoT Hub-Instanz aus. In diesem Tutorial wird **ContosoTestHub** verwendet. Wählen Sie im Bereich „Hub“ die Option **IoT-Geräte** aus.

4. Wählen Sie **+ Hinzufügen**. Geben Sie im Bereich „Gerät hinzufügen“ die Geräte-ID an. In diesem Tutorial wird **Contoso-Test-Device** verwendet. Lassen Sie die Felder für die Schlüssel leer, und aktivieren Sie das Kontrollkästchen **Schlüssel automatisch generieren**. Stellen Sie sicher, dass die Option **Gerät mit IoT Hub verbinden** aktiviert ist. Wählen Sie **Speichern** aus.

   ![Bildschirm „Gerät hinzufügen“](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Nachdem das Gerät erstellt wurde, wählen Sie es aus, um die generierten Schlüssel anzuzeigen. Wählen Sie für den primären Schlüssel das Kopiersymbol aus, und speichern Sie den Schlüssel für die Testphase dieses Tutorials beispielsweise im Editor.

   ![Gerätedetails einschließlich Schlüssel](./media/iot-hub-include-create-simulated-device-portal/device-details.png)