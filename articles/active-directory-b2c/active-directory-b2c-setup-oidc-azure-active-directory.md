---
title: 'Einrichten der Anmeldung für eine Azure Active Directory-Organisation: Azure Active Directory B2C | Microsoft-Dokumentation'
description: Einrichten der Anmeldung für eine bestimmte Azure Active Directory-Organisation in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28dbf0382ac151857e72d4bb59e207f07c8ad3f3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508418"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung für eine bestimmte Azure Active Directory-Organisation in Azure Active Directory B2C

>[!NOTE]
> Dieses Feature befindet sich in der Phase der öffentlichen Vorschau. Verwenden Sie dieses Feature nicht in Produktionsumgebungen.

Wenn Sie Azure Active Directory (Azure AD) als [Identitätsanbieter](active-directory-b2c-reference-oauth-code.md) in Azure AD B2C verwenden möchten, müssen Sie eine Anwendung für seine Darstellung erstellen. In diesem Artikel erfahren Sie, wie Sie die Anmeldung für Benutzer einer bestimmten Azure AD-Organisation über einen Benutzerflow in Azure AD B2C aktivieren.

## <a name="create-an-azure-ad-app"></a>Erstellen einer Azure AD-App

Um die Anmeldung für Benutzer von einer bestimmten Azure AD-Organisation zu aktivieren, müssen Sie eine Anwendung im Azure AD-Mandanten der Organisation registrieren, der nicht dem Azure AD B2C-Mandanten entspricht.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten enthält. Wählen Sie im Hauptmenü den **Verzeichnis- und Abonnementfilter** aus, und wählen Sie das Verzeichnis aus, das Ihren Azure AD-Mandanten enthält. Dieser Mandant ist nicht mit Ihrem Azure AD B2C-Mandanten identisch.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Neue Registrierung** aus.
5. Geben Sie einen Namen für Ihre Anwendung ein. Beispiel: `Azure AD B2C App`.
6. Übernehmen Sie die Auswahl **Accounts in this organizational directory only** (Nur Konten in diesem Organisationsverzeichnis) für diese Anwendung.
7. Übernehmen Sie den Wert **Web** als **Umleitungs-URI**, geben Sie die folgende URL in Kleinbuchstaben ein, und ersetzen Sie dabei `your-B2C-tenant-name` durch den Namen Ihres Azure AD B2C-Mandanten. Beispiel: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your--B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Alle URLs sollten jetzt [b2clogin.com](b2clogin.md) verwenden.

8. Klicken Sie auf **Registrieren**. Kopieren Sie die **Anwendungs-ID (Client)** zur späteren Verwendung.
9. Wählen Sie **Zertifikate & Geheimnisse** im Anwendungsmenü und dann **Neuer geheimer Clientschlüssel** aus.
10. Geben Sie einen Namen für den geheimen Clientschlüssel ein. Beispiel: `Azure AD B2C App Secret`.
11. Wählen Sie den Ablaufzeitraum aus. Übernehmen Sie für diese Anwendung die Auswahl **In 1 Jahr** aus.
12. Klicken Sie auf **Hinzufügen**, und kopieren Sie den Wert des angezeigten neuen geheimen Clientschlüssels zur späteren Verwendung.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurieren von Azure AD als Identitätsanbieter

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das den Azure AD B2C-Mandanten enthält. Wählen Sie im Hauptmenü den **Verzeichnis- und Abonnementfilter** aus, und wählen Sie das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
2. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
3. Wählen Sie **Identitätsanbieter** und dann **Hinzufügen** aus.
4. Geben Sie einen **Namen** ein. Geben Sie z. B. Folgendes ein: `Contoso Azure AD`.
5. Klicken Sie auf **Identitätsanbietertyp**, wählen Sie **OpenID Connect (Vorschau)** aus, und klicken Sie dann auf **OK**.
6. Klicken Sie auf **Diesen Identitätsanbieter einrichten**.
7. Geben Sie für **Metadaten-URL** die folgende URL ein, in der `your-AD-tenant-domain` durch den Domänennamen Ihres Azure AD-Mandanten ersetzt wird. Beispiel: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Geben Sie für die **Client-ID** die Anwendungs-ID und für **Geheimer Clientschlüssel** den geheimen Clientschlüssel ein, die Sie beide zuvor notiert haben.
9. Geben Sie optional einen Wert für **Domänenhinweis** ein. Beispiel: `ContosoAD`. Dieser Wert wird zum Verweis auf diesen Identitätsanbieter mit *domain_hint* in der Anforderung verwendet. 
10. Klicken Sie auf **OK**.
11. Wählen Sie **Ansprüche dieses Identitätsanbieters zuordnen** aus, und legen Sie die folgenden Ansprüche fest:
    
    - Geben Sie für **Benutzer-ID** `oid` ein.
    - Geben Sie für **Anzeigename** `name` ein.
    - Geben Sie für **Vorname** `given_name` ein.
    - Geben Sie für **Nachname** `family_name` ein.
    - Geben Sie für **E-Mail** `unique_name` ein.

12. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Konfiguration zu speichern.
