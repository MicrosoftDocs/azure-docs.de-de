---
title: Protokollierung in MSAL-Anwendungen | Azure
description: Hier finden Sie Informationen zur Protokollierung in Microsoft Authentication Library-Anwendungen (MSAL-Anwendungen).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58f18995d46ca61ae68a7b226bbfc9a286e73a0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544093"
---
# <a name="logging"></a>Protokollierung
Mit Microsoft Authentication Library-Apps (MSAL-Apps) werden Protokollmeldungen generiert, die Sie beim Diagnostizieren von Problemen unterstützen und Details angeben können. Eine App kann die Protokollierung mit wenigen Codezeilen konfigurieren und hat benutzerdefinierte Kontrolle über den Detailgrad und darüber, ob personenbezogene Daten und Organisationsdaten protokolliert werden oder nicht. Es wird empfohlen, einen MSAL-Protokollierungsrückruf festzulegen und Benutzern eine Möglichkeit zu bieten, Protokolle zu übermitteln, wenn Authentifizierungsprobleme auftreten.

## <a name="logging-levels"></a>Protokolliergrade

Bei der MSAL-Protokollierung können verschiedene Detailgrade erfasst werden:

- Fehler Gibt an, dass ein Problem aufgetreten ist und ein Fehler generiert wurde. Verwenden Sie diesen Grad zum Debuggen und Identifizieren von Problemen.
- Warnung: Ereignisse, die in Frage kommen und über die die App weitere Informationen benötigt. Es muss nicht unbedingt zu einem Fehler oder Ausfall gekommen sein. Dieser Grad ist für das Diagnostizieren und Ermitteln von Problemen vorgesehen.
- Info: MSAL protokolliert Ereignisse, die für Informationszwecke und nicht unbedingt für das Debuggen vorgesehen sind.
- Verbose: Standard. MSAL protokolliert eine große Menge von Informationen und gibt alle Details zum Verhalten der Bibliothek an.

## <a name="personal-and-organizational-data"></a>Personenbezogene Daten und Organisationsdaten
Die MSAL-Protokollierung erfasst standardmäßig keine streng vertraulichen personenbezogenen Daten oder Organisationsdaten. Die Bibliothek bietet Ihnen jedoch die Option, die Protokollierung von personenbezogenen Daten und Organisationsdaten zu aktivieren.

## <a name="logging-in-msalnet"></a>Protokollierung in MSAL.NET
In MSAL 3.x wird die Protokollierung bei der App-Erstellung pro Anwendung mit dem Generatormodifizierer `.WithLogging` festgelegt. Bei dieser Methode können optionale Parameter verwendet werden:

- Mit *Level* können Sie den gewünschten Protokolliergrad festlegen. Wenn Sie den Parameter auf „Errors“ festlegen, werden nur Fehler protokolliert.
- Mit *PiiLoggingEnabled* können Sie personenbezogene Daten und Organisationsdaten protokollieren, wenn der Parameter auf „true“ festgelegt ist. Dieser Parameter ist standardmäßig auf „false“ festgelegt, damit Ihre Anwendung keine personenbezogenen Daten protokolliert.
- *LogCallback* ist auf einen Delegaten festgelegt, der die Protokollierung ausführt. Wenn *PiiLoggingEnabled* auf „true“ festgelegt ist, empfängt diese Methode die Nachrichten zweimal: einmal mit dem Parameter *containsPii* gleich „false“ mit der Nachricht ohne personenbezogene Daten und ein zweites Mal mit dem Parameter *containsPii* gleich „true“ mit der Nachricht, die personenbezogene Daten enthalten kann. In einigen Fällen (wenn die Nachricht keine personenbezogenen Daten enthält) ist die Nachricht dieselbe.
- *DefaultLoggingEnabled* aktiviert die Standardprotokollierung für die Plattform. Der Parameter ist standardmäßig auf „false“ festgelegt. Wenn Sie den Parameter auf „true“ festlegen, wird in Desktop-/UWP-Anwendungen die Ereignisablaufverfolgung, unter iOS NSLog und unter Android Logcat verwendet.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsnc();
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Protokollierung in MSAL.js

 Sie können die Protokollierung in MSAL.js aktivieren, indem Sie bei der Konfiguration ein Protokollierungsobjekt zum Erstellen einer `UserAgentApplication`-Instanz übergeben. Dieses Protokollierungsobjekt weist die folgenden Eigenschaften auf:

- *localCallback*: Eine Rückrufinstanz, die vom Entwickler bereitgestellt werden kann, um Protokolle benutzerdefiniert zu verarbeiten und zu veröffentlichen. Implementieren Sie die „localCallback“-Methode abhängig davon, wie Sie Protokolle umleiten möchten.

- *level* (optional): Der konfigurierbare Protokolliergrad. Es werden die Protokolliergrade „Error“, „Warning“, „Info“ und „Verbose“ unterstützt. Der Standardwert lautet „Info“.

- *piiLoggingEnabled* (optional): Mit diesem Parameter können Sie personenbezogene Daten und Organisationsdaten protokollieren, wenn er auf „true“ festgelegt ist. Dieser Parameter ist standardmäßig auf „false“ festgelegt, damit Ihre Anwendung keine personenbezogenen Daten protokolliert. Protokolle mit personenbezogenen Daten werden nie in Standardausgaben wie die Konsole, Logcat oder NSLog geschrieben. Standardmäßig ist „false“ festgelegt.

- *correlationId* (optional): Ein eindeutiger Bezeichner, der die Anforderung der Antwort zu Debugzwecken zuordnet. Standardmäßig wird ein in RFC 4122 definierter GUID der Version 4 (128 Bit) verwendet.

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
