---
title: include file
description: include file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993283"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Führen Sie zum Abrufen der IoT-Hub-Verbindungszeichenfolge für die Richtlinie **registryReadWrite** die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** aus. Wählen Sie die Ressourcengruppe aus, in der sich der Hub befindet, und wählen Sie dann in der Liste der Ressourcen Ihren Hub aus.

2. Wählen Sie im linken Bereich Ihres Hubs **SAS-Richtlinien** aus.

3. Wählen Sie in der Liste der Richtlinien die Richtlinie **registryRead Write** aus.

4. Wählen Sie unter **Schlüssel für gemeinsamen Zugriff** das Kopiersymbol für **Verbindungszeichenfolge – Primärschlüssel** aus, und speichern Sie den Wert.

    ![Abrufen der Verbindungszeichenfolge.](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Weitere Informationen zu SAS-Richtlinien und Berechtigungen für IoT-Hubs finden Sie unter [Access Control und Berechtigungen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
