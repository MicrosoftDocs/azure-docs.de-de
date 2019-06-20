---
title: Includedatei
description: Includedatei
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162581"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Sie leiten Nachrichten an verschiedene Ressourcen weiter, abhängig von Eigenschaften, die vom simulierten Gerät an die Nachricht angefügt werden. Nachrichten ohne benutzerdefinierte Weiterleitung, werden an den Standardendpunkt gesendet (Nachrichten/Ereignisse). Im nächsten Tutorial werden Nachrichten an IoT Hub gesendet und an verschiedene Ziele weitergeleitet.

|value |Ergebnis|
|------|------|
|level="storage" |Schreibvorgang in Azure Storage.|
|level="critical" |Schreibvorgang in eine Service Bus-Warteschlange. Eine Logik-App ruft die Nachricht aus der Warteschlange ab und sendet sie mithilfe von Office 365.|
|die Standardeinstellung |Anzeigen dieser Daten mithilfe von Power BI.|

Als Erstes muss der Endpunkt eingerichtet werden, an den die Daten weitergeleitet werden. Danach wird die Nachrichtenroute eingerichtet, die diesen Endpunkt verwendet. Nach Einrichtung des Routings können Sie die Endpunkte und Nachrichtenrouten im Portal anzeigen.