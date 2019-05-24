---
title: Automatisches Abrufen eines Tokens (Microsoft Authentication Library für .NET) | Azure
description: Erfahren Sie, wie ein Zugriffstoken mithilfe von Microsoft Authentication Library für .NET (MSAL.NET) automatisch (aus dem Tokencache) abgerufen werden kann.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544238"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Abrufen eines Tokens aus dem Tokencache mithilfe von MSAL.NET

Wenn Sie ein Zugriffstoken mithilfe von Microsoft Authentication Library für .NET (MSAL.NET) abrufen, wird das Token zwischengespeichert. Wenn die Anwendung ein Token benötigt, sollte sie zunächst die `AcquireTokenSilent`-Methode aufrufen, um zu überprüfen, ob sich ein akzeptables Token im Cache befindet. In vielen Fällen ist es möglich, anhand eines Tokens im Cache ein weiteres Token mit zusätzlichen Bereichen abzurufen. Es ist auch möglich, ein Token zu aktualisieren, wenn es bald abläuft (da der Tokencache auch ein Aktualisierungstoken enthält).

Das empfohlene Muster besteht darin, zuerst die `AcquireTokenSilent`-Methode aufzurufen.  Wenn `AcquireTokenSilent` fehlschlägt, wird dann ein Token mit anderen Methoden abgerufen.

Im folgenden Beispiel versucht die Anwendung zunächst, ein Token aus dem Tokencache abzurufen.  Wenn eine `MsalUiRequiredException` -Ausnahme ausgelöst wird, ruft die Anwendung interaktiv ein Token ab. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```