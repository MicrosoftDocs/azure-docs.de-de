---
title: include file
description: include file
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389496"
---
| Domänennamen                                          | Ausgehende Ports | BESCHREIBUNG                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Öffentliche Cloud: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> China: `*.servicebus.chinacloudapi.cn`   | 443            | Erforderlich für die selbstgehostete Integration Runtime zur interaktiven Erstellung |
| Öffentliche Cloud: `{datafactory}.{region}.datafactory.azure.net`<br> oder `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> China: `{datafactory}.{region}.datafactory.azure.cn` | 443            | Erforderlich für die selbstgehostete Integration Runtime, um Verbindungen mit dem Azure Data Factory-Dienst herzustellen. <br>Bei einer neu erstellten Data Factory in der öffentlichen Cloud finden Sie den vollqualifizierten Domänenname im Schlüssel der selbstgehosteten Integration Runtime, der das folgende Format hat: {datafactory}.{region}.datafactory.azure.net. Kann bei einer alten Data Factory der FQDN nicht über den Schlüssel der selbstgehosteten Integration Runtime ermittelt werden, verwenden Sie stattdessen „*.frontend.clouddatahub.net“. |
| `download.microsoft.com`    | 443            | Erforderlich für die selbstgehostete Integration Runtime zum Herunterladen der Aktualisierungen. Wenn Sie die automatische Aktualisierung deaktiviert haben, können Sie die Konfiguration dieser Domäne überspringen. |
| Schlüsseltresor-URL | 443           | Erforderlich für Azure Key Vault, wenn Sie die Anmeldeinformationen in Key Vault speichern. |
