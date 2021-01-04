---
title: Anzeige einer Fehlermeldung auf der App-Seite nach der Anmeldung | Microsoft-Dokumentation
description: Beheben von Problemen mit der Azure AD-Anmeldung, wenn die App eine Fehlermeldung zurückgibt
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 779286a43f8b20ce9a9a528e14eaa930763d82b4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651582"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>App-Seite, auf der eine Fehlermeldung angezeigt wird, nachdem sich ein Benutzer angemeldet hat

Im folgenden Szenario wird ein Benutzer durch Azure Active Directory (Azure AD) angemeldet. Die Anwendung zeigt allerdings eine Fehlermeldung an, und der Benutzer kann den Anmeldevorgang nicht abschließen. Das Problem besteht darin, dass die App die von Azure AD zurückgegebene Antwort nicht akzeptiert.

Für diese Situation gibt es mehrere mögliche Gründe. Wenn aus der Fehlermeldung nicht deutlich hervorgeht, welche Antwortinformationen fehlen, können Sie Folgendes versuchen:

-   Wenn es sich bei der App um den Azure AD-Katalog handelt, müssen Sie alle Schritte im Artikel [How to debug SAML-based single sign-on to applications in Azure AD (Debuggen des SAML-basierten einmaligen Anmeldens in Azure AD)](./debug-saml-sso-issues.md) ausführen.

-   Verwenden Sie ein Tool wie [Fiddler](https://www.telerik.com/fiddler), um die SAML-Anforderung, die SAML-Antwort und das SAML-Token zu erfassen.

-   Senden Sie die SAML-Antwort dem App-Verkäufer, und erkundigen Sie sich bei ihm, welche Informationen fehlen.

## <a name="attributes-are-missing-from-the-saml-response"></a>In der SAML-Antwort fehlen Attribute

Führen Sie die folgenden Schritte aus, um der Azure AD-Konfiguration ein Attribut hinzuzufügen, das zusammen mit der Azure AD-Antwort gesendet wird:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als Globaler Administrator oder Co-Administrator an.

2. Klicken Sie oben im Navigationsbereich auf der linken Seite auf **Alle Dienste**, um die Azure AD-Erweiterung zu öffnen.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf **Azure Active Directory**.

4. Klicken Sie im Azure AD-Navigationsbereich auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste mit Ihren Apps anzuzeigen.

   > [!NOTE]
   > Wenn eine App nicht wie erwartet angezeigt wird, verwenden Sie das **Filter**-Steuerelement über der Liste **Alle Anwendungen**. Legen Sie für die Option **Anzeigen** „Alle Anwendungen“ fest.

6. Wählen Sie die Anwendung aus, die Sie für das einmalige Anmelden konfigurieren möchten.

7. Warten Sie, bis die App geladen ist, und klicken Sie anschließend im Navigationsbereich auf **Einmaliges Anmelden**.

8. Aktivieren Sie im Abschnitt **Benutzerattribute** das Kontrollkästchen **Alle weiteren Benutzerattribute anzeigen und bearbeiten**. Nun können Sie einstellen, welche Attribute im SAML-Token an die App gesendet werden sollen, wenn sich Benutzer anmelden.

   So fügen Sie ein Attribut hinzu

   1. Klicken Sie auf **Attribut hinzufügen**. Geben Sie unter **Name** einen Namen ein, und wählen Sie aus der Dropdownliste den **Wert** aus.

   1.  Wählen Sie **Speichern** aus. Das neue Attribut wird in der Tabelle angezeigt.

9. Speichern Sie die Konfiguration.

   Wenn sich der Benutzer das nächste Mal bei der App anmeldet, sendet Azure AD das neue Attribut zusammen mit der SAML-Antwort.

## <a name="the-app-doesnt-identify-the-user"></a>Die App identifiziert den Benutzer nicht

Die Anmeldung bei der App schlägt möglicherweise fehl, weil in der SAML-Antwort ein Attribut (beispielsweise eine Rolle) fehlt. Sie kann auch fehlschlagen, wenn die App ein anderes Format oder einen anderen Wert für das **NameID**-Attribut (Benutzer-ID) erwartet.

Wenn Sie die [automatische Benutzerbereitstellung von Azure AD](../app-provisioning/user-provisioning.md) verwenden, um Benutzer in der App zu erstellen, zu verwalten und aus dieser zu löschen, müssen Sie darauf achten, dass der Benutzer in der SaaS-App bereitgestellt wurde. Weitere Informationen finden Sie unter [Es werden keine Benutzer für eine Azure AD-Kataloganwendung bereitgestellt](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Hinzufügen eines Attributs zur Azure AD-App-Konfiguration

Um den Benutzer-ID-Wert zu ändern, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als Globaler Administrator oder Co-Administrator an.

2. Klicken Sie oben im Navigationsbereich auf der linken Seite auf **Alle Dienste** um die Azure AD-Erweiterung zu öffnen.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf **Azure Active Directory**.

4. Klicken Sie im Azure AD-Navigationsbereich auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste mit Ihren Apps anzuzeigen.

   > [!NOTE]
   > Wenn eine App nicht wie erwartet angezeigt wird, verwenden Sie das **Filter**-Steuerelement über der Liste **Alle Anwendungen**. Legen Sie für die Option **Anzeigen** „Alle Anwendungen“ fest.

6. Wählen Sie die App aus, die Sie für SSO konfigurieren möchten.

7. Warten Sie, bis die App geladen ist, und klicken Sie anschließend im Navigationsbereich auf **Einmaliges Anmelden**.

8. Wählen Sie unter **Benutzerattribute** in der Dropdownliste **Benutzer-ID** den eindeutigen Bezeichner für den Benutzer aus.

## <a name="change-the-nameid-format"></a>Ändern des NameID-Formats

Wenn die Anwendung ein anderes Format für das **NameID**-Attribut (Benutzer-ID) erwartet, finden Sie unter [Bearbeiten der NameID](../develop/active-directory-saml-claims-customization.md#editing-nameid) weitere Informationen dazu, wie Sie das NameID-Format anpassen.

Azure AD wählt das Format für das **NameID**-Attribut (Benutzer-ID) auf Grundlage des ausgewählten Werts oder des Formats aus, das in der SAML-Authentifizierungsanforderung von der App angefordert wird. Weitere Informationen finden Sie im Abschnitt „NameIDPolicy“ des Artikels [Single sign-on SAML protocol (SAML-Protokoll für einmaliges Anmelden)](../develop/single-sign-on-saml-protocol.md#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Die App erwartet eine andere Signaturmethode für die SAML-Antwort

Führen Sie folgende Schritte durch, um einzustellen, welche Teile des SAML-Tokens von Azure AD digital signiert werden:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und melden Sie sich als globaler Administrator oder Co-Administrator an.

2. Klicken Sie oben im Navigationsbereich auf der linken Seite auf **Alle Dienste** um die Azure AD-Erweiterung zu öffnen.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf **Azure Active Directory**.

4. Klicken Sie im Azure AD-Navigationsbereich auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste mit Ihren Apps anzuzeigen.

   > [!NOTE]
   > Wenn Ihre Anwendung nicht angezeigt wird, verwenden Sie das **Filter**-Steuerelement über der Liste **Alle Anwendungen**. Legen Sie für die Option **Anzeigen** „Alle Anwendungen“ fest.

6. Wählen Sie die Anwendung aus, die Sie für das einmalige Anmelden konfigurieren möchten.

7. Warten Sie, bis die App geladen ist, und klicken Sie anschließend im Navigationsbereich auf **Einmaliges Anmelden**.

8. Klicken Sie unter **SAML-Signaturzertifikat** auf **Erweiterte Einstellungen für die Zertifikatsignatur**.

9. Wählen Sie aus den folgenden Optionen die **Signaturoption** aus, die von der App erwartet wird:

   * **SAML-Antwort signieren**
   * **SAML-Antwort und -Assertion signieren**
   * **SAML-Assertion signieren**

   Wenn sich der Benutzer das nächste Mal bei der App anmeldet, signiert Azure AD den ausgewählten Teil der SAML-Antwort.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Die App erwartet den Signaturalgorithmus SHA-1

Standardmäßig signiert Azure AD das SAML-Token mit dem sichersten Algorithmus. Sie sollten eine Umstellung auf den Signaturalgorithmus *SHA-1* vermeiden, wenn dieser nicht für die App erforderlich ist.

Um den Signaturalgorithmus zu ändern, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und melden Sie sich als globaler Administrator oder Co-Administrator an.

2. Klicken Sie oben im Navigationsbereich auf der linken Seite auf **Alle Dienste** um die Azure AD-Erweiterung zu öffnen.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf **Azure Active Directory**.

4. Klicken Sie im Azure AD-Navigationsbereich auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

   > [!NOTE]
   > Wenn Ihre Anwendung nicht angezeigt wird, verwenden Sie das **Filter**-Steuerelement über der Liste **Alle Anwendungen**. Legen Sie für die Option **Anzeigen** „Alle Anwendungen“ fest.

6. Wählen Sie die App aus, die Sie für das einmalige Anmelden konfigurieren möchten.

7. Warten Sie, bis die App geladen ist, und klicken Sie anschließend im Navigationsbereich auf der linken Seite auf **Einmaliges Anmelden**.

8. Klicken Sie unter **SAML-Signaturzertifikat** auf **Erweiterte Einstellungen für die Zertifikatsignatur**.

9. Wählen Sie **SHA-1** als **Signaturalgorithmus** aus.

   Wenn sich der Benutzer das nächste Mal bei der App anmeldet, signiert Azure AD das SAML-Token mithilfe des SHA-1-Algorithmus.

## <a name="next-steps"></a>Nächste Schritte
[How to debug SAML-based single sign-on to applications in Azure AD (Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure AD)](./debug-saml-sso-issues.md)