---
title: 'Azure AD v2 – Windows-Desktop: Erste Schritte – Konfiguration | Microsoft-Dokumentation'
description: Informationen, wie Windows Desktop .NET (XAML)-Anwendungen eine API aufrufen können, die Zugriffstoken vom Azure Active Directory v2-Endpunkt anfordert.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef32cef67ae5f32959e026157f5126647b01401d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540579"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Hinzufügen der Registrierungsinformationen der Anwendung zu Ihrer App
In diesem Schritt müssen Sie die Anwendungs-ID Ihrem Projekt hinzufügen.

1.  Öffnen Sie `App.xaml.cs`, und ersetzen Sie die Zeile mit der `ClientId` durch:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Wie geht es weiter?

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
