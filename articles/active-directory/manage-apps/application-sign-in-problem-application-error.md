---
title: Fehler auf der Seite einer Anwendung nach dem Anmelden | Microsoft-Dokumentation
description: Beheben von Problemen mit der Azure AD-Anmeldung, wenn die Anwendung selbst einen Fehler ausgibt
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 234fc94a0a2ad3d3bee49853cf1dfc6a805a0166
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825403"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Fehler auf der Seite einer Anwendung nach dem Anmelden

In diesem Szenario hat Azure AD den Benutzer angemeldet, die Anwendung zeigt jedoch einen Fehler an, durch den der Benutzer den Anmeldevorgang nicht ordnungsgemäß abschließen kann. Bei diesem Szenario liegt das Problem darin, dass die Anwendung die Antwort von Azure AD nicht annimmt.

Es gibt mehrere mögliche Gründe, warum die Anwendung die Antwort von Azure AD nicht akzeptiert hat. Wenn der Fehler in der Anwendung nicht deutlich genug ist, um zu erkennen, was in der Antwort fehlt, gilt Folgendes:

-   Wenn sich die Anwendung im Azure AD-Katalog befindet, vergewissern Sie sich, dass Sie alle im Artikel [Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging) beschriebenen Schritte ausgeführt haben.

-   Verwenden Sie ein Tool wie [Fiddler](https://www.telerik.com/fiddler), um die SAML-Anforderung, die SAML-Antwort und das SAML-Token zu erfassen.

-   Teilen Sie die SAML-Antwort dem Hersteller der Anwendung mit, um zu erfahren, welche Elemente fehlen.

## <a name="missing-attributes-in-the-saml-response"></a>Fehlende Attribute in der SAML-Antwort

Führen Sie die folgenden Schritte aus, um in der Azure AD-Konfiguration ein Attribut hinzuzufügen, das in der Azure AD-Antwort gesendet werden soll:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

8. Klicken Sie im Bereich **Benutzerattribute** auf **Alle weiteren Benutzerattribute anzeigen und bearbeiten**, um die Attribute zu bearbeiten, die bei der Anmeldung durch die Benutzer im SAML-Token an die Anwendung gesendet werden sollen.

   So fügen Sie ein Attribut hinzu

   * Klicken Sie auf **Attribut hinzufügen**. Geben Sie einen **Namen** ein, und wählen Sie in der Dropdownliste einen **Wert** aus.

   * Klicken Sie auf **Speichern**. Das neue Attribut wird in der Tabelle angezeigt.

9. Speichern Sie die Konfiguration.

Bei der nächsten Anmeldung des Benutzers bei der Anwendung sendet Azure AD das neue Attribut in der SAML-Antwort.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>Anderer Wert oder anderes Format für die Benutzer-ID von der Anwendung erwartet

Beim Anmelden bei der Anwendung tritt ein Fehler auf, da in der SAML-Antwort Attribute wie z.B. Rollen fehlen oder die Anwendung ein anderes Format für das EntityID-Attribut erwartet.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Fügen Sie in der Konfiguration der Azure AD-Anwendung ein Attribut hinzu:

Um den Benutzer-ID-Wert zu ändern, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

8. Wählen Sie unter **Benutzerattribute** in der Dropdownliste **Benutzer-ID** den eindeutigen Bezeichner für die Benutzer aus.

## <a name="change-entityid-user-identifier-format"></a>Ändern des Formats für EntityID (Benutzer-ID)

Wenn die Anwendung ein anderes Format für das EntityID-Attribut erwartet, können Sie das Format für EntityID (Benutzer-ID), das von Azure AD nach der Benutzerauthentifizierung in der Antwort an die Anwendung gesendet wird, nicht auswählen.

In Azure AD wird das Format für das NameID-Attribut (Benutzer-ID) auf Grundlage des ausgewählten Werts oder des Formats festgelegt, das von der Anwendung in der SAML-Authentifizierungsanforderung angefordert wird. Weitere Informationen finden Sie im Artikel [SAML-Protokoll für einmaliges Anmelden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) im Abschnitt zu NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>Andere Signaturmethode für die SAML-Antwort von der Anwendung erwartet

Eventuell müssen Sie ändern, welche Teile des SAML-Tokens von Azure Active Directory digital signiert werden. Folgen Sie diesen Schritten:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

8. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Erweiterte Einstellungen für die Zertifikatsignatur**.

9. Wählen Sie die entsprechende **Signaturoption** aus, die von der Anwendung erwartet wird:

   * SAML-Antwort signieren

   * SAML-Antwort und -Assertion signieren

   * SAML-Assertion signieren

Bei der nächsten Anmeldung des Benutzers bei der Anwendung signiert Azure AD den ausgewählten Teil der SAML-Antwort.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>Signaturalgorithmus SHA-1 von der Anwendung erwartet

Standardmäßig signiert Azure AD das SAML-Token mit dem sichersten Algorithmus. Das Ändern des Signaturalgorithmus in SHA-1 wird nicht empfohlen, sofern dies nicht von der Anwendung erfordert wird.

Um den Signaturalgorithmus zu ändern, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

8. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Erweiterte Einstellungen für die Zertifikatsignatur**.

9. Wählen Sie unter **Signaturalgorithmus** SHA-1 aus.

Bei der nächsten Anmeldung des Benutzers bei der Anwendung signiert Azure AD das SAML-Token mit dem Algorithmus SHA-1.

## <a name="next-steps"></a>Nächste Schritte
[Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
