---
title: Benutzerflowtypen in Azure Active Directory B2C | Microsoft-Dokumentation
titleSuffix: Azure AD B2C
description: Erfahren Sie etwas über das erweiterbare Richtlinienframework von Azure Active Directory B2C und das Erstellen verschiedener Benutzerflows.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06253b571fd71623501c27fd5b0d9d4013727fc2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840190"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Benutzerflows in Azure Active Directory B2C

Um Ihnen die Einrichtung der gängigsten Identitätsaufgaben für Ihre Anwendungen zu erleichtern, enthält das Azure AD B2C-Portal vordefinierte und konfigurierbare Richtlinien, die als **Benutzerflows** bezeichnet werden. Mithilfe eines Benutzerflows können Sie feststellen, wie Benutzer mit Ihrer Anwendung interagieren, wenn sie Aktionen wie Anmelden, Registrieren, Bearbeiten eines Profils oder Zurücksetzen eines Kennworts ausführen. Mit Benutzerflows können Sie die folgenden Funktionen steuern:

- Kontotypen für die Anmeldung, z.B. Konten für soziale Netzwerke wie Facebook oder lokale Konten
- Attribute, die vom Kunden gesammelt werden, wie z.B. Vorname, Postleitzahl und Schuhgröße
- Azure AD Multi-Factor Authentication
- Anpassung der Benutzeroberfläche
- Informationen, die die Anwendung in Form von Ansprüchen in einem Token erhält

Sie können viele Benutzerflows verschiedener Typen in Ihrem Mandanten erstellen und bei Bedarf in Ihren Anwendungen verwenden. Benutzerflows können anwendungsübergreifend wiederverwendet werden. Durch diese Flexibilität können Sie Benutzeroberflächen im Zusammenhang mit der Identität mit minimalen oder ganz ohne Änderungen am Code definieren und ändern. Ihre Anwendung löst einen Benutzerflow mithilfe einer standardmäßigen HTTP-Authentifizierungsanforderung aus, die Parameter für einen Benutzerflow enthält. Als Antwort wird ein benutzerdefiniertes [Token](tokens-overview.md) empfangen.

Die folgenden Beispiele zeigen den Parameter „p“ der Abfragezeichenfolge, der den zu verwendenden Benutzerflow angibt:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Benutzerflowversionen

Azure AD B2C beinhaltet mehrere Typen von Benutzerflows:

- **Registrierung und Anmeldung:** verarbeitet die Benutzeroberfläche für Registrierung und Anmeldung in ein und derselben Konfiguration. Die Benutzer werden je nach Kontext auf den jeweils entsprechenden Pfad geleitet. Außerdem sind separate Benutzerflows für **Registrierung** oder **Anmeldung** enthalten. Im Allgemeinen wird jedoch der kombinierte Benutzerflow für Registrierung und Anmeldung empfohlen.
- **Profilbearbeitung:** ermöglicht Benutzern, ihre Profilinformationen zu bearbeiten.
- **Zurücksetzen des Kennworts:** ermöglicht Ihnen, festzulegen, ob und wie Benutzer ihr Kennwort zurücksetzen können.

Die meisten Benutzerflowtypen verfügen sowohl über eine Version vom Typ **Empfohlen** als auch über eine Version vom Typ **Standard**. Weitere Informationen finden Sie unter [Benutzerflowversionen in Azure Active Directory B2C](user-flow-versions.md).

> [!IMPORTANT]
> Wenn Sie in Azure AD B2C bereits mit Benutzerflows gearbeitet haben, werden Sie feststellen, dass Benutzerflowversionen jetzt anders angegeben werden. Zuvor wurden V1-Versionen (bereit für die Produktion) sowie V1.1- und V2-Versionen (Vorschauversion) angeboten. Nun wurden Benutzerflows in zwei Versionen zusammengefasst:
>
>- **Empfohlene** Benutzerflows sind die neuen Vorschauversionen von Benutzerflows. Sie wurden gründlich getestet und umfassen alle Features der alten Versionen **V2** und **V1.1**. Die neuen empfohlenen Benutzerflows werden in Zukunft verwaltet und aktualisiert. Nachdem Sie auf diese neuen empfohlenen Benutzerflows umgestellt haben, erhalten Sie Zugriff auf neue Features, sobald diese veröffentlicht werden.
>- **Standardbenutzerflows** wurden zuvor als **V1** bezeichnet und sind allgemein verfügbare und produktionsbereite Benutzerflows. Wenn Ihre Benutzerflows unternehmenskritisch sind und stark von stabilen Versionen abhängen, können Sie weiterhin die Standardbenutzerflows verwenden. Sie sollten jedoch wissen, dass diese Versionen nicht mehr verwaltet und aktualisiert werden.
>
>Alle Benutzerflows der Legacyvorschauversionen (V1.1 und V2) werden zum **1. August 2021** eingestellt. Es wird dringend empfohlen, so bald wie möglich [zu den neuen **empfohlenen** Benutzerflows zu wechseln](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow), damit Sie stets die neuen Features und Updates nutzen können.

## <a name="linking-user-flows"></a>Verknüpfen von Benutzerflows

Ein Benutzerflow für **Registrierung oder Anmeldung** für lokale Konten zeigt auf der ersten Seite der Oberfläche den Link **Kennwort vergessen?** an. Durch Klicken auf diesen Link wird nicht automatisch ein Benutzerflow zum Zurücksetzen des Kennworts ausgelöst.

Stattdessen wird der Fehlercode `AADB2C90118` an Ihre Anwendung zurückgegeben. Die Anwendung muss diesen Fehlercode verarbeiten, indem sie einen bestimmten Benutzerflow zum Zurücksetzen des Kennworts ausführt. Sehen Sie sich ein [einfaches ASP.NET-Beispiel](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) an, das die Verknüpfung von Benutzerflows veranschaulicht.

## <a name="email-address-storage"></a>Speicherung der E-Mail-Adresse

Eine E-Mail-Adresse kann als Teil eines Benutzerflows erforderlich sein. Wenn sich der Benutzer mit einem sozialen Netzwerk als Identitätsanbieter authentifiziert, wird die E-Mail-Adresse in der **otherMails**-Eigenschaft gespeichert. Wenn ein lokales Konto auf einem Benutzernamen basiert, wird die E-Mail-Adresse in einer speziellen Eigenschaft für die sichere Authentifizierung gespeichert. Wenn ein lokales Konto auf einer E-Mail-Adresse basiert, wird die E-Mail-Adresse in der **signInNames**-Eigenschaft gespeichert.

Es ist in diesen Fällen nicht garantiert, dass die E-Mail-Adresse überprüft wird. Ein Mandantenadministrator kann die E-Mail-Überprüfung in den grundlegenden Richtlinien für lokale Konten deaktivieren. Selbst wenn die Überprüfung der E-Mail-Adresse aktiviert ist, werden die Adressen nicht überprüft, wenn sie von einem sozialen Netzwerk als Identitätsanbieter stammen und nicht geändert wurden.

Nur die Eigenschaften **otherMails** und **signInNames** werden über die Microsoft Graph-API verfügbar gemacht. Die E-Mail-Adresse in der Eigenschaft für die sichere Authentifizierung ist nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie zum Erstellen der empfohlenen Benutzerflows die Anweisungen im [Tutorial: Erstellen eines Benutzerflows](tutorial-create-user-flows.md).
