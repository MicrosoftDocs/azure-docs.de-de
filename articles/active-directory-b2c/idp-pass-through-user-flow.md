---
title: 'Übergeben eines Zugriffstokens über einen Benutzerflow an Ihre Anwendung: Azure Active Directory B2C | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie ein Zugriffstoken für OAuth 2.0-Identitätsanbieter als Anspruch in einem Benutzerflow in Azure Active Directory B2C übergeben können.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: db4b799aa31a4132609b0dd158b65070fb2474b7
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510962"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Übergeben eines Zugriffstokens über einen Benutzerflow an Ihre Anwendung in Azure Active Directory B2C

> [!NOTE]
> Dieses Feature ist zurzeit als öffentliche Preview verfügbar.

Ein [Benutzerflow](active-directory-b2c-reference-policies.md) in Azure Active Directory B2C (Azure AD) ermöglicht es Benutzern Ihrer Anwendung, sich mit einem Identitätsanbieter zu registrieren oder anzumelden. Bei diesem Vorgang empfängt Azure AD B2C zunächst ein [Zugriffstoken](active-directory-b2c-reference-tokens.md) vom Identitätsanbieter. Azure AD B2C verwendet dieses Token, um Informationen zum Benutzer abzurufen. Sie aktivieren einen Anspruch in Ihrem Benutzerflow, um das Token an die Anwendungen zu übergeben, die Sie in Azure AD B2C registrieren.

Azure AD B2C unterstützt aktuell nur die Übergabe des Zugriffstokens für [OAuth 2.0](active-directory-b2c-reference-oauth-code.md)-Identitätsanbieter, zu denen [Facebook](active-directory-b2c-setup-fb-app.md) und [Google](active-directory-b2c-setup-goog-app.md) gehören. Für alle weiteren Identitätsanbieter wird ein leerer Anspruch zurückgegeben.

## <a name="prerequisites"></a>Voraussetzungen

- Ihre Anwendung muss einen [v2-Benutzerflow](user-flow-versions.md) verwenden.
- Ihr Benutzerflow ist mit einem OAuth 2.0-Identitätsanbieter konfiguriert.

## <a name="enable-the-claim"></a>Aktivieren des Anspruchs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im oberen Menü **Directory and subscription filter** (Verzeichnis- und Abonnementfilter) und dann das Verzeichnis aus, das Ihren Mandanten enthält.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
4. Wählen Sie **Benutzerflows (Richtlinien)** und dann Ihren Benutzerflow aus. Beispiel: **B2C_1_signupsignin1**.
5. Wählen Sie **Anwendungsansprüche** aus.
6. Aktivieren Sie den Anspruch **Zugriffstoken des Identitätsanbieters**.

    ![Aktivieren des Anspruchs „Zugriffstoken des Identitätsanbieters“](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Klicken Sie auf **Speichern**, um den Benutzerflow zu speichern.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

Wenn Sie Ihre Anwendungen in Azure AD B2C testen, kann es nützlich sein, das Azure AD B2C-Token an `https://jwt.ms` zurückzugeben, um die darin enthaltenen Ansprüche zu überprüfen.

1. Wählen Sie auf der Übersichtsseite des Benutzerflows die Option **Benutzerflow ausführen** aus.
2. Wählen Sie für **Anwendung** Ihre Anwendung aus, die Sie zuvor registriert haben. Um das Token im nachstehenden Beispiel anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
3. Klicken Sie auf **Benutzerflow ausführen**, und melden Sie sich dann mit den Anmeldeinformationen für Ihr Konto an. Es sollte das Zugriffstoken des Identitätsanbieters im Anspruch **idp_access_token** angezeigt werden.

    Sie sollten eine Ausgabe ähnlich wie im folgenden Beispiel sehen:

    ![Decodiertes Token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Azure AD B2C: Tokenreferenz](active-directory-b2c-reference-tokens.md).




