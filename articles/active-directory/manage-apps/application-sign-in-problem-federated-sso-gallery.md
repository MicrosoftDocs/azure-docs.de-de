---
title: Probleme beim Anmelden bei einer Kataloganwendung, die für einmaliges Anmelden im Verbund konfiguriert ist | Microsoft-Dokumentation
description: Leitfaden zu den Fehlern beim Anmelden bei einer Anwendung, die für SAML-basiertes einmaliges Anmelden im Verbund mit Azure AD konfiguriert wurde
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
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1985b7bbcfdaab2aa303f67a9b1d090c85eedd5d
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825209"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Probleme beim Anmelden bei einer Kataloganwendung, die für einmaliges Anmelden im Verbund konfiguriert ist

Zur Behandlung der unten aufgeführten Anmeldeprobleme wird empfohlen, die folgenden Vorschläge umzusetzen. So werden bessere Diagnosen bereitgestellt, und Lösungsschritte lassen sich automatisieren:

- Installieren Sie die [Browsererweiterung zur sicheren Anmeldung für„Meine Apps“](access-panel-extension-problem-installing.md), um Azure Active Directory (Azure AD) dabei zu unterstützen, bessere Diagnosen und Lösungen bereitzustellen, wenn Sie die Testumgebung im Azure-Portal nutzen.
- Reproduzieren Sie den Fehler mithilfe der Testfunktionen auf der App-Konfigurationsseite im Azure-Portal. Weitere Informationen finden Sie unter [Debuggen von SAML-basierten Anwendungen für einmaliges Anmelden](../develop/howto-v1-debug-saml-sso-issues.md).


## <a name="application-not-found-in-directory"></a>Die Anwendung wurde im Verzeichnis nicht gefunden.

*Fehler AADSTS70001: Die Anwendung mit dem Bezeichner „https:\//contoso.com“ wurde im Verzeichnis nicht gefunden*.

**Mögliche Ursache**

Das `Issuer`-Attribut, das in der SAML-Anforderung von der Anwendung an Azure AD gesendet wird, entspricht nicht dem Bezeichnerwert, der für die Anwendung in Azure AD konfiguriert wurde.

**Lösung**

Stellen Sie sicher, dass das `Issuer`-Attribut in der SAML-Anforderung mit dem in Azure AD konfigurierten Bezeichnerwert übereinstimmt. Wenn Sie die [Testumgebung](../develop/howto-v1-debug-saml-sso-issues.md) im Azure-Portal mit der Browsererweiterung zur sicheren Anmeldung für„Meine Apps“ verwenden, müssen Sie diese Schritte nicht manuell ausführen.

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

1.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

1.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf das Element **Azure Active Directory**.

1.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

1.  Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

    Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

1.  Wählen Sie die Anwendung aus, die Sie für das einmalige Anmelden konfigurieren möchten.

1.  Nachdem die Anwendung geladen wurde, öffnen Sie **Grundlegende SAML-Konfiguration**. Stellen Sie sicher, dass der Wert im Textfeld „Bezeichner“ mit dem in der Fehlermeldung angezeigten Bezeichnerwert übereinstimmt.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Die Antwortadresse stimmt nicht mit den für die Anwendung konfigurierten Antwortadressen überein

*Fehler AADSTS50011: Die Antwortadresse „https:\//contoso.com“ stimmt nicht mit den für die Anwendung konfigurierten Antwortadressen überein*.

**Mögliche Ursache**

Der `AssertionConsumerServiceURL`-Wert in der SAML-Anforderung stimmt nicht mit dem in Azure AD konfigurierten Wert der Antwort-URL oder mit dem Muster überein. Der `AssertionConsumerServiceURL`-Wert in der SAML-Anforderung ist die URL in der Fehlermeldung.

**Lösung**

Stellen Sie sicher, dass der `AssertionConsumerServiceURL`-Wert in der SAML-Anforderung dem in Azure AD konfigurierten Wert der Antwort-URL entspricht. Wenn Sie die [Testumgebung](../develop/howto-v1-debug-saml-sso-issues.md) im Azure-Portal mit der Browsererweiterung zur sicheren Anmeldung für„Meine Apps“ verwenden, müssen Sie diese Schritte nicht manuell ausführen.

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

1.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

1.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf das Element **Azure Active Directory**.

1.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

1.  Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

    Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

1.  Wählen Sie die Anwendung aus, die Sie für das einmalige Anmelden konfigurieren möchten.

1.  Nachdem die Anwendung geladen wurde, öffnen Sie **Grundlegende SAML-Konfiguration**. Vergewissern Sie sich, dass der Wert im Textfeld für die Antwort-URL dem `AssertionConsumerServiceURL`-Wert in der SAML-Anforderung entspricht, oder aktualisieren Sie ihn entsprechend.    
    
Nachdem Sie den Wert der Antwort-URL in Azure AD aktualisiert haben und er mit dem Wert übereinstimmt, der in der SAML-Anforderung von der Anwendung gesendet wurde, sollten Sie sich bei der Anwendung anmelden können.

## <a name="user-not-assigned-a-role"></a>Benutzer wurde keine Rolle zugewiesen

*Fehler AADSTS50105: Der angemeldete Benutzer „brian\@contoso.com“ ist keiner Anwendungsrolle zugewiesen*.

**Mögliche Ursache**

Dem Benutzer wurde kein Zugriff auf die Anwendung in Azure AD erteilt.

**Lösung**

Führen Sie die unten aufgeführten Schritte aus, um einer Anwendung mindestens einen Benutzer direkt zuzuweisen. Wenn Sie die [Testumgebung](../develop/howto-v1-debug-saml-sso-issues.md) im Azure-Portal mit der Browsererweiterung zur sicheren Anmeldung für„Meine Apps“ verwenden, müssen Sie diese Schritte nicht manuell ausführen.

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** an.

1.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

1.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf das Element **Azure Active Directory**.

1.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

1.  Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

    Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

1.  Wählen Sie aus der Liste der Anwendungen eine aus, der Sie einen Benutzer zuweisen möchten.

1.  Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Benutzer und Gruppen**.

1.  Klicken Sie oberhalb der Liste **Benutzer und Gruppen** auf die Schaltfläche **Hinzufügen**, um den Bereich **Zuweisung hinzufügen** zu öffnen.

1.  Wählen Sie im Bereich **Zuweisung hinzufügen** den Selektor **Benutzer und Gruppen** aus.

1. Geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** den vollständigen Namen oder die E-Mail-Adresse des Benutzers ein, der hinzugefügt werden soll.

1. Zeigen Sie auf den **Benutzer** in der Liste, um ein **Kontrollkästchen** anzuzeigen. Klicken Sie auf das Kontrollkästchen neben dem Bild oder Logo des Benutzerprofils, um den Benutzer der Liste **Ausgewählt** hinzuzufügen.

1. **Optional**: Wenn Sie **mehrere Benutzer hinzufügen möchten**, geben Sie im Suchfeld **Nach Name oder E-Mail-Adresse suchen** einen weiteren vollständigen Namen oder eine weitere E-Mail-Adresse ein, und klicken Sie auf das Kontrollkästchen, um den Benutzer der Liste **Ausgewählt** hinzuzufügen.

1. Wenn Sie alle gewünschten Benutzer ausgewählt haben, klicken Sie auf die Schaltfläche **Auswählen**, um sie der Liste der Benutzer und Gruppen hinzuzufügen, die der Anwendung zugewiesen werden sollen.

1. **Optional**: Klicken Sie im Bereich **Zuweisung hinzufügen** auf das Auswahlfeld **Rolle auswählen**, um eine Rolle auszuwählen, die den ausgewählten Benutzern zugewiesen werden soll.

1. Klicken Sie auf die Schaltfläche **Zuweisen**, um die Anwendung den ausgewählten Benutzern zuzuweisen.

Nach kurzer Zeit können die ausgewählten Benutzer diese Anwendungen mithilfe der Schritte starten, die im Abschnitt mit der Lösungsbeschreibung aufgeführt wurden.

## <a name="not-a-valid-saml-request"></a>Keine gültige SAML-Anforderung

*Fehler AADSTS75005: Die Anforderung ist keine gültige Nachricht im SAML2-Protokoll*.

**Mögliche Ursache**

Azure AD unterstützt die von der Anwendung für einmaliges Anmelden gesendete SAML-Anforderung nicht. Häufige Probleme sind beispielsweise die folgenden:

-   Fehlende erforderliche Felder in der SAML-Anforderung
-   Codierte Methode in der SAML-Anforderung

**Lösung**

1. Erfassen Sie die SAML-Anforderung. Im Tutorial [Debug SAML-based single sign-on to applications in Azure Active Directory (Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure AD)](../develop/howto-v1-debug-saml-sso-issues.md) erhalten Sie Informationen zum Erfassen der SAML-Anforderung.

1. Wenden Sie sich an den Anwendungshersteller, und teilen Sie diesem folgende Informationen mit:

   -   SAML-Anforderung

   -   [Anforderungen des SAML-Protokolls für einmaliges Anmelden bei Azure AD](../develop/single-sign-on-saml-protocol.md)

Der Anwendungshersteller sollte bestätigen, dass er die SAML-Implementierung in Azure AD für einmaliges Anmelden unterstützt.

## <a name="misconfigured-application"></a>Falsch konfigurierte Anwendung

*Fehler AADSTS650056: Falsch konfigurierte Anwendung. Dies kann einen der folgenden Gründe haben: Für den Client werden in der Anwendungsregistrierung des Clients in den angeforderten Berechtigungen keine Berechtigungen für „AAD Graph“ aufgeführt. Ebenfalls möglich ist, dass der Administrator auf dem Mandanten nicht eingewilligt hat. Sie sollten auch den Anwendungsbezeichner in der Anforderung überprüfen, um sicherzustellen, dass er mit dem konfigurierten Clientanwendungsbezeichner übereinstimmt. Wenden Sie sich an Ihren Administrator, um die Konfiguration zu beheben oder im Auftrag des Mandanten einzuwilligen.*

**Mögliche Ursache**

Das `Issuer`-Attribut, das in der SAML-Anforderung von der Anwendung an Azure AD gesendet wird, entspricht nicht dem Bezeichnerwert, der für die Anwendung in Azure AD konfiguriert wurde.

**Lösung**

Stellen Sie sicher, dass das `Issuer`-Attribut in der SAML-Anforderung mit dem in Azure AD konfigurierten Bezeichnerwert übereinstimmt. Wenn Sie die [Testumgebung](../develop/howto-v1-debug-saml-sso-issues.md) im Azure-Portal mit der Browsererweiterung zur sicheren Anmeldung für„Meine Apps“ verwenden, müssen Sie diese Schritte nicht manuell ausführen:

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

1.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

1.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf das Element **Azure Active Directory**.

1.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

1.  Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

    Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

1.  Wählen Sie die Anwendung aus, die Sie für das einmalige Anmelden konfigurieren möchten.

1.  Nachdem die Anwendung geladen wurde, öffnen Sie **Grundlegende SAML-Konfiguration**. Stellen Sie sicher, dass der Wert im Textfeld „Bezeichner“ mit dem in der Fehlermeldung angezeigten Bezeichnerwert übereinstimmt.


## <a name="certificate-or-key-not-configured"></a>Zertifikat oder Schlüssel nicht konfiguriert

*Fehler AADSTS50003: Kein Signaturschlüssel konfiguriert.*

**Mögliche Ursache**

Das Anwendungsobjekt ist beschädigt, und Azure AD erkennt das für die Anwendung konfigurierte Zertifikat nicht.

**Lösung**

Um das Zertifikat zu löschen und ein neues zu erstellen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

1. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

1. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf das Element **Azure Active Directory**.

1. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

1. Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

    Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

1. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

1. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Neues Zertifikat erstellen**.

1. Wählen Sie das Ablaufdatum aus, und klicken Sie anschließend auf **Speichern**.

1. Aktivieren Sie **Neues Zertifikat aktivieren**, um das aktive Zertifikat außer Kraft zu setzen. Klicken Sie oben im Bereich auf **Speichern**, und akzeptieren Sie die Aktivierung des Rolloverzertifikats.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Entfernen**, um das **nicht verwendete** Zertifikat zu entfernen.

## <a name="saml-request-not-present-in-the-request"></a>SAML-Anforderung nicht in der Anforderung vorhanden

*Fehler AADSTS750054: „SAMLRequest“ oder „SAMLResponse“ muss als Abfragezeichenfolgenparameter in der HTTP-Anforderung für die SAML-Umleitungsbindung vorhanden sein.*

**Mögliche Ursache**

Azure AD konnte die SAML-Anforderung in den URL-Parametern in der HTTP-Anforderung nicht identifizieren. Dies kann vorkommen, wenn die Anwendung keine HTTP-Umleitungsbindung beim Senden der SAML-Anforderung an Azure AD verwendet.

**Lösung**

Die Anwendung muss die SAML-Anforderung codiert im Adressheader unter Verwendung der HTTP-Umleitungsbindung senden. Weitere Informationen zur Implementierung finden Sie im Abschnitt zur HTTP-Umleitungsbindung in den [SAML-Protokollspezifikationen](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD sendet das Token an einen falschen Endpunkt

**Mögliche Ursache**

Wenn die Anmeldeanforderung während des einmaligen Anmeldens keine explizite Antwort-URL (Assertionsverbraucherdienst-URL) enthält, wählt Azure AD eine der konfigurierten Antwort-URLs für diese Anwendung aus. Auch wenn für die Anwendung eine explizite Antwort-URL konfiguriert wurde, wird der Benutzer möglicherweise zu https://127.0.0.1:444 umgeleitet. 

Wenn die Anwendung als Nicht-Katalog-App hinzugefügt wurde, hat Azure Active Directory die Antwort-URL als Standardwert erstellt. Dieses Verhalten wurde geändert, und Azure Active Directory fügt die URL nicht mehr standardmäßig hinzu. 

**Lösung**

Löschen Sie nicht verwendete Antwort-URLs, die für die Anwendung konfiguriert sind.

1.  Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2.  Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3.  Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf das Element **Azure Active Directory**.

4.  Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5.  Wählen Sie **Alle Anwendungen**, um eine Liste mit Ihren Anwendungen anzuzeigen.

    Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6.  Wählen Sie die Anwendung aus, die Sie für das einmalige Anmelden konfigurieren möchten.

7.  Nachdem die Anwendung geladen wurde, öffnen Sie **Grundlegende SAML-Konfiguration**. Löschen Sie im Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** nicht verwendete oder standardmäßige Antwort-URLs, die vom System erstellt wurden. Beispiel: `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem beim Anpassen der SAML-Ansprüche, die an eine Anwendung gesendet werden

Informationen zum Anpassen der SAML-Attributsansprüche, die an Ihre Anwendung gesendet werden, finden Sie unter [Anspruchszuordnung in Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Nächste Schritte

[Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure AD](../develop/howto-v1-debug-saml-sso-issues.md)
