---
title: Hilfe zum Portal „Meine Apps“ in Azure Active Directory | Microsoft-Dokumentation
description: Hier erhalten Sie Hilfe für die Anmeldung am Portal „Meine Apps“ und für das Ausführen häufig anfallender Aufgaben.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60475026"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Problembehandlung beim Portal „Meine Apps“
Wenn Sie Probleme haben, sich beim Portal **Meine Apps** anzumelden oder das Portal zu verwenden, sollten Sie diese Tipps zur Fehlerbehebung ausprobieren, bevor Sie sich an den Helpdesk oder Ihren Administrator wenden.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Ich habe Probleme bei der Installation der My Apps Secure Sign-in-Erweiterung
Wenn Sie Probleme mit der Installation der My Apps Secure Sign-in-Erweiterung haben:

- Vergewissern Sie sich, dass Sie einen unterstützten Browser verwenden:

    - **Microsoft Edge.** Ausführung unter Windows 10 Anniversary Edition oder höher.
    - **Google Chrome.** Ausführung unter Windows 7 oder höher und Mac OS X oder höher.
    - **Mozilla Firefox 26.0 oder höher.** Ausführung unter Windows XP SP2 oder höher und Mac OS X 10.6 oder höher.
    - **Internet Explorer 11.** Ausführung unter Windows 7 oder höher (eingeschränkte Unterstützung).

- Stellen Sie sicher, dass die Erweiterungseinstellungen Ihres Browsers aktiviert sind.

- Starten Sie den Browser neu und versuchen Sie noch einmal, sich am Portal **Meine Apps** anzumelden.

- Versuchen Sie, die Cookies im Browser zu löschen, den Browser neu zu starten und sich erneut am Portal **Meine Apps** anzumelden.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Ich kann mich nicht am Portal **Meine Apps** anmelden
Wenn Sie Probleme haben, sich am Portal **Meine Apps** anzumelden, können Sie Folgendes versuchen:

- Stellen Sie sicher, dass Sie die richtige URL verwenden. Hierbei muss es sich um https://myapps.microsoft.com oder um eine benutzerdefinierte Seite Ihrer Organisation handeln, z. B. https://myapps.microsoft.com/contoso.com.

- Stellen Sie sicher, dass Ihr Kennwort richtig ist und nicht abgelaufen ist. Weitere Informationen finden Sie unter [Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos](active-directory-passwords-update-your-own-password.md).

- Stellen Sie sicher, dass Ihre Überprüfungsinformationen aktuell und korrekt sind. Weitere Informationen finden Sie unter [Was ist Azure Multi-Factor Authentication?](multi-factor-authentication-end-user.md) oder [Changing your security info methods and information (Ändern Ihrer Sicherheitsinformationsmethoden und Sicherheitsinformationen)](security-info-add-update-methods-overview.md).

- Fügen Sie die URL des Portals **Meine Apps** zur Einstellung **Interneteigenschaften > Sicherheit > Vertrauenswürdige Sites** hinzu.

- Löschen Sie den Cache des Browsers, und versuchen Sie erneut, sich anzumelden.

## <a name="my-password-isnt-working"></a>Mein Kennwort funktioniert nicht
Wenn Sie Ihr Kennwort ändern möchten, es vergessen oder nie von Ihrer Organisation erhalten haben oder aus Ihrem Konto ausgesperrt wurden, finden Sie unter [Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos](active-directory-passwords-update-your-own-password.md) weitere Informationen.

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Ich möchte mein Kennwort eigenständig zurücksetzen können
Um Ihr Kennwort eigenständig zurücksetzen zu können, muss Ihr Administrator zunächst dieses Feature für Ihre Organisation aktivieren. Anschließend müssen Sie die benötigten Überprüfungsmethoden aktualisieren und überprüfen. Weitere Informationen dazu, wie Sie Ihre Überprüfungsmethoden aktualisieren, finden Sie unter [Registrieren für die Self-Service-Kennwortzurücksetzung](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Mir wird die Meldung „Zugriff verweigert“ angezeigt, wenn ich eine App starte
Wenn Sie die Meldung **Zugriff verweigert** erhalten, nachdem Sie eine App aus dem Portal **Meine Apps** starten, können Sie Folgendes versuchen:

- Vergewissern Sie sich, dass Sie die [My Apps Secure Sign-in-Erweiterung ](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) installiert haben und einen [unterstützten Browser](my-apps-portal-end-user-access.md#supported-browsers) verwenden.

- Stellen Sie sicher, dass Sie die richtige URL für die App verwenden und dass die URL in der Liste unter **Interneteigenschaften > Sicherheit > Vertrauenswürdige Sites** aufgeführt ist.

- Stellen Sie sicher, dass Ihr Kennwort stimmt und nicht abgelaufen ist. Weitere Informationen finden Sie unter [Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos](active-directory-passwords-update-your-own-password.md).

- Stellen Sie sicher, dass Ihre Überprüfungsinformationen aktuell und korrekt sind. Weitere Informationen finden Sie unter [Was ist Azure Multi-Factor Authentication?](multi-factor-authentication-end-user.md) oder [Changing your security info methods and information (Ändern Ihrer Sicherheitsinformationsmethoden und Sicherheitsinformationen)](security-info-add-update-methods-overview.md).

- Löschen Sie den Cache des Browsers, und versuchen Sie erneut, sich anzumelden.

Wenn Sie nach dem Ausprobieren dieser Maßnahmen noch immer nicht auf Ihre App zugreifen können, müssen Sie den Helpdesk Ihrer Organisation um Unterstützung bitten.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich am Portal **Meine Apps** angemeldet haben, können Sie auch Ihre Profil- und Kontoinformationen, Ihre Gruppeninformationen und Ihre Informationen für die Zugriffsüberprüfung aktualisieren (sofern Sie über die entsprechenden Berechtigungen verfügen).

- [Access and use apps on the My Apps portal (Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“)](my-apps-portal-end-user-access.md)

- [Ändern Ihrer Profilinformationen](my-apps-portal-end-user-update-profile.md)

- [Anzeigen und Aktualisieren gruppenbezogener Informationen](my-apps-portal-end-user-groups.md)

- [Durchführen eigener Zugriffsüberprüfungen](my-apps-portal-end-user-access-reviews.md)