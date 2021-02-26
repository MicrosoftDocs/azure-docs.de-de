---
title: include file
description: include file
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98954957"
---
## <a name="enable-logging"></a>Aktivieren der Protokollierung

Zum Debuggen und für die Problembehandlung von Authentifizierungsfehlern stellt MSAL eine integrierte Unterstützung für die Protokollierung bereit. Die Protokollierung in den einzelnen Bibliotheken wird in den folgenden Artikeln behandelt:

:::row:::
    :::column:::
        - [Protokollierung in MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Protokollierung in MSAL für Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Protokollierung in MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Protokollierung in MSAL für iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Protokollierung in MSAL für Java](../articles/active-directory/develop/msal-logging-java.md)
        - [Protokollierung in MSAL für Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Hier einige Vorschläge für Daten, deren Erfassung sich lohnen kann:

- Benutzer bitten bei Problemen möglicherweise um Unterstützung. Eine bewährte Vorgehensweise besteht darin, Protokolle zu erfassen und temporär zu speichern. Geben Sie einen Speicherort an, an den Benutzer die Protokolle hochladen können. MSAL stellt Protokollierungserweiterungen zur Verfügung, mit denen sich detaillierte Authentifizierungsinformationen erfassen lassen.

- Falls Telemetriedaten verfügbar sind, aktivieren Sie die Sammlung von Telemetriedaten mithilfe der MSAL, um zu ermitteln, wie sich Benutzer bei Ihrer App anmelden.


## <a name="validate-your-integration"></a>Überprüfen Ihrer Integration

Testen Sie Ihre Integration anhand der [Checkliste für die Integration von Microsoft Identity Platform](../articles/active-directory/develop/identity-platform-integration-checklist.md).
