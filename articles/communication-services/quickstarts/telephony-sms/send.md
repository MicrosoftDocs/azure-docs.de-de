---
title: 'Schnellstart: Senden einer SMS-Nachricht'
titleSuffix: An Azure Communication Services quickstart
description: Hier erfahren Sie, wie Sie mithilfe von Azure Communication Services eine SMS-Nachricht senden.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 60c51de4e4549649c681c961c6ddc1acdb12e698
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659691"
---
# <a name="quickstart-send-an-sms-message"></a>Schnellstart: Senden einer SMS-Nachricht

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!IMPORTANT]
> SMS-Nachrichten können an Telefonnummern in den USA gesendet und von solchen Telefonnummern empfangen werden. Telefonnummern in anderen geografischen Regionen werden von Communication Services-SMS noch nicht unterstützt.
> Weitere Informationen finden Sie unter **[Planen Ihrer Telefonie- und SMS-Lösung](../../concepts/telephony-sms/plan-solution.md)** .

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Problembehandlung

Bei der Behandlung von Problemen im Zusammenhang mit der SMS-Übermittlung können Sie [Übermittlungsberichte mit Event Grid](./handle-sms-events.md) aktivieren, um Übermittlungsdetails zu erfassen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Erfahren Sie mehr über das [Bereinigen von Ressourcen](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie Azure Communication Services zum Senden von SMS-Nachrichten verwenden.

> [!div class="nextstepaction"]
> [Abonnieren von SMS-Ereignissen](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Planen Ihrer PSTN-Lösung](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Weitere Informationen zu SMS](../../concepts/telephony-sms/concepts.md)