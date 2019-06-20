---
title: Benutzerflowtypen in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie etwas über das erweiterbare Richtlinienframework von Azure Active Directory B2C und das Erstellen verschiedener Benutzerflows.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 54677dc0771f65b7636b4d1cac77f53f9c04a09d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508935"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Benutzerflows in Azure Active Directory B2C

Das erweiterbare Richtlinienframework von Azure Active Directory (Azure AD) B2C ist der Hauptvorteil des Diensts. Die Richtlinien beschreiben Benutzeroberflächen im Zusammenhang mit der Identität, z.B. Registrierung, Anmeldung oder Profilbearbeitung. Um Ihnen die Einrichtung der gängigsten Identitätsaufgaben zu erleichtern, enthält das Azure AD B2C-Portal vordefinierte und konfigurierbare Richtlinien, die als **Benutzerflows** bezeichnet werden. 

## <a name="what-are-user-flows"></a>Was sind Benutzerflows?

Ein Benutzerflow ermöglicht das Steuern des Verhaltens in Ihren Anwendungen durch Konfigurieren der folgenden Einstellungen:

- Kontotypen für die Anmeldung, z.B. Konten für soziale Netzwerke wie Facebook oder lokale Konten
- Attribute, die vom Kunden gesammelt werden, wie z.B. Vorname, Postleitzahl und Schuhgröße
- Azure Multi-Factor Authentication
- Anpassung der Benutzeroberfläche
- Informationen, die die Anwendung in Form von Ansprüchen in einem Token erhält 

Sie können viele Benutzerflows verschiedener Typen in Ihrem Mandanten erstellen und bei Bedarf in Ihren Anwendungen verwenden. Benutzerflows können anwendungsübergreifend wiederverwendet werden. Durch diese Flexibilität können Sie Benutzeroberflächen im Zusammenhang mit der Identität mit minimalen oder ganz ohne Änderungen am Code definieren und ändern. Ihre Anwendung löst einen Benutzerflow mithilfe einer standardmäßigen HTTP-Authentifizierungsanforderung aus, die Parameter für einen Benutzerflow enthält. Als Antwort wird ein benutzerdefiniertes [Token](active-directory-b2c-reference-tokens.md) empfangen. 

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

Im Azure-Portal werden ständig neue [Versionen von Benutzerflows](user-flow-versions.md) hinzugefügt. Wenn Sie neu bei Azure AD B2C sind, sollten Sie getestete Benutzerflows verwenden. Wenn Sie einen neuen Benutzerflow erstellen, wählen Sie den benötigten Benutzerflow auf der Registerkarte **Empfohlen** aus.

Die folgenden Benutzerflows werden derzeit empfohlen:

- **Registrierung und Anmeldung:** verarbeitet die Benutzeroberfläche für Registrierung und Anmeldung in ein und derselben Konfiguration. Die Benutzer werden je nach Kontext auf den jeweils entsprechenden Pfad geleitet. Sie sollten diesen Benutzerflow anstelle eines **Registrierungs-** oder **Anmeldebenutzerflows** verwenden.
- **Profilbearbeitung:** ermöglicht Benutzern, ihre Profilinformationen zu bearbeiten.
- **Zurücksetzen des Kennworts:** ermöglicht Ihnen, festzulegen, ob und wie Benutzer ihr Kennwort zurücksetzen können.

## <a name="linking-user-flows"></a>Verknüpfen von Benutzerflows

Ein Benutzerflow für **Registrierung oder Anmeldung** für lokale Konten zeigt auf der ersten Seite der Oberfläche den Link **Kennwort vergessen?** an. Durch Klicken auf diesen Link wird nicht automatisch ein Benutzerflow zum Zurücksetzen des Kennworts ausgelöst. 

Stattdessen wird der Fehlercode `AADB2C90118` an Ihre Anwendung zurückgegeben. Die Anwendung muss diesen Fehlercode verarbeiten, indem sie einen bestimmten Benutzerflow zum Zurücksetzen des Kennworts ausführt. Sehen Sie sich ein [einfaches ASP.NET-Beispiel](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) an, das die Verknüpfung von Benutzerflows veranschaulicht.

## <a name="email-address-storage"></a>Speicherung der E-Mail-Adresse

Eine E-Mail-Adresse kann als Teil eines Benutzerflows erforderlich sein. Wenn sich der Benutzer mit einem sozialen Netzwerk als Identitätsanbieter authentifiziert, wird die E-Mail-Adresse in der **otherMails**-Eigenschaft gespeichert. Wenn ein lokales Konto auf einem Benutzernamen basiert, wird die E-Mail-Adresse in einer speziellen Eigenschaft für die sichere Authentifizierung gespeichert. Wenn ein lokales Konto auf einer E-Mail-Adresse basiert, wird die E-Mail-Adresse in der **signInNames**-Eigenschaft gespeichert.
 
Es ist in diesen Fällen nicht garantiert, dass die E-Mail-Adresse überprüft wird. Ein Mandantenadministrator kann die E-Mail-Überprüfung in den grundlegenden Richtlinien für lokale Konten deaktivieren. Selbst wenn die Überprüfung der E-Mail-Adresse aktiviert ist, werden die Adressen nicht überprüft, wenn sie von einem sozialen Netzwerk als Identitätsanbieter stammen und nicht geändert wurden.
 
Nur die Eigenschaften **otherMails** und **signInNames** werden über die Active Directory Graph-API verfügbar gemacht. Die E-Mail-Adresse in der Eigenschaft für die sichere Authentifizierung ist nicht verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie zum Erstellen der empfohlenen Benutzerflows die Anweisungen im [Tutorial: Erstellen eines Benutzerflows](tutorial-create-user-flows.md).


