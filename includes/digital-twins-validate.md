---
author: baanders
description: Includedatei mit einer Beschreibung, wie Azure Digital Twins-Modelle überprüft werden
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026784"
---
> [!TIP]
> Nachdem Sie ein Modell erstellt haben, sollten Sie Ihre Modelle offline validieren, bevor Sie sie in Ihre Azure Digital Twins-Instanz hochladen.

Für die Validierung von Modelldokumenten steht ein sprachunabhängiges Beispiel zur Verfügung, um sicherzustellen, dass die DTDL gültig ist, bevor Sie sie in Ihre Instanz hochladen. Sie finden es an der folgenden Stelle: [**DTDL-Validierungssteuerelement (Beispiel)**](/samples/azure-samples/dtdl-validator/dtdl-validator).

* Das Beispiel für ein DTDL-Validierungssteuerelement basiert auf einer .NET-DTDL-Parserbibliothek, die auf NuGet als clientseitige Bibliothek verfügbar ist: [**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Sie können die Bibliothek auch direkt verwenden, um eine eigene Validierungslösung zu entwerfen. Stellen Sie bei Verwendung der Parserbibliothek sicher, dass die verwendete Version mit der Version kompatibel ist, die von Azure Digital Twins ausgeführt wird. Aktuell ist dies Version *3.12.4*.

Weitere Informationen zu dem Beispiel für ein Validierungssteuerelement und zur Parserbibliothek, einschließlich Verwendungsbeispielen, finden Sie unter [*Vorgehensweise: Clientseitige DTDL-Parserbibliothek*](../articles/digital-twins/how-to-parse-models.md).