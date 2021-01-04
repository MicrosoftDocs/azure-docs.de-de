---
title: Einrichten der Anmeldung für eine Azure AD-Organisation
titleSuffix: Azure AD B2C
description: Einrichten der Anmeldung für eine bestimmte Azure Active Directory-Organisation in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a079cfe155119a6afe8575767dd3e7c09a564f9a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445630"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung für eine bestimmte Azure Active Directory-Organisation in Azure Active Directory B2C

Wenn Sie Azure Active Directory (Azure AD) als [Identitätsanbieter](authorization-code-flow.md) in Azure AD B2C verwenden möchten, müssen Sie eine Anwendung für seine Darstellung erstellen. In diesem Artikel erfahren Sie, wie Sie die Anmeldung für Benutzer einer bestimmten Azure AD-Organisation über einen Benutzerflow in Azure AD B2C aktivieren.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurieren von Azure AD als Identitätsanbieter

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das den Azure AD B2C-Mandanten enthält. Wählen Sie im Hauptmenü den **Verzeichnis- und Abonnementfilter** aus, und wählen Sie das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Identitätsanbieter** und dann **Neuer OpenID Connect-Anbieter** aus.
1. Geben Sie einen **Namen** ein. Geben Sie beispielsweise *Contoso Azure AD* ein.
1. Geben Sie für **Metadaten-URL** die folgende URL ein, und ersetzen Sie dabei `{tenant}` durch den Domänennamen Ihres Azure AD-Mandanten.

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Beispiel: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Beispiel: `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Geben Sie für **Client-ID** die zuvor notierte Anwendungs-ID ein.
1. Geben Sie im Feld **Geheimer Clientschlüssel** den zuvor notierten geheimen Clientschlüssel ein.
1. Geben Sie für den **Bereich** das `openid profile` ein.
1. Übernehmen Sie die Standardwerte für **Antworttyp** und **Antwortmodus**.
1. (Optional) Geben Sie als **Domänenhinweis** die Zeichenfolge `contoso.com` ein. Weitere Informationen finden Sie unter [Einrichten einer direkten Anmeldung mit Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Wählen Sie unter **Zuordnung von Identitätsanbieteransprüchen** die folgenden Ansprüche aus:

    * **Benutzer-ID**: *oid*
    * **Anzeigename**: *name*
    * **Vorname**: *given_name*
    * **Nachname**: *family_name*
    * **E-Mail**: *preferred_username*

1. Wählen Sie **Speichern** aus.
