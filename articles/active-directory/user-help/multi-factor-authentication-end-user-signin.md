---
title: Anmelden mit Authentifizierung unter Verwendung eines Geschäfts-, Schul- oder Unikontos – Azure AD
description: Erfahren Sie, wie Sie sich mit den verschiedenen zweistufigen Überprüfungsmethoden bei Ihrem Geschäfts-, Schul- oder Unikonto anmelden.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: 2f27dd7daf310e425b09db7905ad2f7c37fcff81
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834165"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Anmelden bei Ihrem Geschäfts-, Schul- oder Unikonto unter Verwendung der zweistufigen Überprüfung

> [!NOTE]
> Zweck dieses Artikels ist eine exemplarische Vorgehensweise für einen typischen Anmeldevorgang. Hilfe zur Anmeldung oder zur Behebung von Problemen finden Sie unter [Beheben von Problemen mit Azure AD Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Was sieht Ihr Anmeldevorgang aus?
Ihr Anmeldevorgang hängt davon ab, was Sie als zweiten Faktor wählen: einen Telefonanruf, eine Authentication-App oder Textnachrichten. Wählen Sie die Option aus, die am besten auf Sie zutrifft:

| Wie melden Sie sich an? |
| --- |
| [Mit einem Telefonanruf an mein Mobil- oder Bürotelefon](#signing-in-with-a-phone-call) |
| [Mit einer Textnachricht an mein Mobiltelefon](#signing-in-with-a-text-message)
| [Mit Benachrichtigungen aus der Microsoft Authenticator-App](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Mit Bestätigungscodes aus der Microsoft Authenticator-App |
| [Mit einer alternativen Methode, da ich meine bevorzugte Methode derzeit nicht verwenden kann](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Anmelden mit einem Telefonanruf
Die folgenden Informationen beschreiben die zweistufige Überprüfung mit einem Anruf an Ihr Mobil- oder Bürotelefon.

1. Melden Sie sich mit Ihrem Benutzernamen und Kennwort bei einer Anwendung oder einem Dienst wie Microsoft 365 an.  
2. Sie erhalten einen Anruf von Microsoft.  
3. Nehmen Sie den Anruf entgegen, und drücken Sie die #-Taste.  

## <a name="signing-in-with-a-text-message"></a>Anmeldung mit einer Textnachricht
Die folgenden Informationen beschreiben die zweistufige Überprüfung mit einer Textnachricht an Ihr Mobiltelefon:

1. Melden Sie sich mit Ihrem Benutzernamen und Kennwort bei einer Anwendung oder einem Dienst wie Microsoft 365 an.
2. Sie erhalten von Microsoft eine SMS mit einem Zahlencode.
3. Geben Sie den Code in das auf der Anmeldeseite gezeigte Feld ein.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Anmelden mit der Microsoft Authenticator-App
Nachstehend wird der Anmeldevorgang mit der Microsoft Authenticator-App für zweistufige Überprüfung beschrieben. Es gibt zwei Möglichkeiten, die App zu verwenden. Sie können Pushbenachrichtigungen auf Ihrem Gerät erhalten oder die App öffnen, um einen Prüfcode abzurufen.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>So melden Sie sich mit einer von der Microsoft Authenticator-App gesendeten Benachrichtigung an
1. Melden Sie sich mit Ihrem Benutzernamen und Kennwort bei einer Anwendung oder einem Dienst wie Microsoft 365 an.
2. Microsoft sendet eine Benachrichtigung an die Microsoft Authenticator-App auf Ihrem Gerät.

   ![Microsoft sendet Benachrichtigung](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Öffnen Sie die Benachrichtigung auf Ihrem Telefon, und wählen den Schlüssel **Überprüfen** aus. Wenn Ihr Unternehmen eine PIN anfordert, geben Sie sie hier ein.
4. Sie sollten jetzt angemeldet sein.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>So melden Sie sich mit der Microsoft Authenticator-App über einen Überprüfungscode an

Wenn Sie die Microsoft Authenticator-App zum Abrufen von Prüfcodes verwenden, wird Ihnen beim Öffnen der App unter dem Namen Ihres Kontos eine Zahl angezeigt. Diese Zahl ändert sich alle 30 Sekunden, damit Sie die gleiche Zahl nicht zweimal verwenden. Wenn ein Prüfcode angefordert wird, öffnen Sie die App und verwenden die derzeit angezeigte Zahl.

1. Melden Sie sich mit Ihrem Benutzernamen und Kennwort bei einer Anwendung oder einem Dienst wie Microsoft 365 an.
2. Sie werden von Microsoft dazu aufgefordert, einen Bestätigungscode einzugeben.

   ![Eingeben des Überprüfungscodes](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Öffnen Sie die Microsoft Authenticator-App auf Ihrem Smartphone, und geben Sie den Code bei der Anmeldung in das entsprechende Feld ein.

## <a name="signing-in-with-an-alternate-method"></a>Anmelden mit einer alternativen Methode
Mitunter verfügen Sie nicht über das Telefon oder Gerät, das Sie als Ihre bevorzugte Überprüfungsmethode festgelegt haben. Genau aus diesem Grund empfehlen wird, alternative Anmeldemethoden für Ihr Konto einzurichten. Im folgenden Abschnitt erfahren Sie, wie Sie sich über eine alternative Methode anmelden, wenn die primäre Methode nicht verfügbar ist.

1. Melden Sie sich mit Ihrem Benutzernamen und Kennwort bei einer Anwendung oder einem Dienst wie Microsoft 365 an.
2. Wählen Sie **Andere Überprüfungsoption verwenden** aus. Es werden verschiedene Überprüfungsoptionen abhängig davon angezeigt, wie viele Sie eingerichtet haben.
3. Wählen Sie eine alternative Methode aus, und melden Sie sich an.

   ![Anwenden einer alternativen Methode](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Nächste Schritte
- Wenn Sie Probleme bei der Anmeldung mit der zweistufigen Überprüfung haben, finden Sie unter [Probleme mit Azure AD Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md) weitere Informationen.

- Lesen Sie den Artikel [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md).

- In [Erste Schritte mit der Microsoft Authenticator-App](user-help-auth-app-download-install.md) erfahren Sie, wie Sie sich mithilfe von Benachrichtigungen anstelle von SMS oder Telefonanrufen anmelden können.
