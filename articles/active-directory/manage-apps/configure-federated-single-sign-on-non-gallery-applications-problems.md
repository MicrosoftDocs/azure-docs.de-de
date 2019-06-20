---
title: Probleme beim Konfigurieren des einmaligen Anmeldens im Verbund für eine nicht im Katalog enthaltene Anwendung | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu den häufigsten Problemen, die beim Konfigurieren des einmaligen Anmeldens im Verbund mit einer benutzerdefinierten SAML-Anwendung auftreten können, die nicht im Azure AD-Anwendungskatalog aufgeführt ist.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c70990f003a39def551171b8637615129379b4
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190307"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Probleme beim Konfigurieren des einmaligen Anmeldens im Verbund für eine nicht im Katalog enthaltene Anwendung

Wenn beim Konfigurieren einer Anwendung ein Problem auftritt: Vergewissern Sie sich, dass Sie alle im Artikel [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) beschriebenen Schritte ausgeführt haben.

## <a name="cant-add-another-instance-of-the-application"></a>Es kann keine weitere Instanz der Anwendung hinzugefügt werden.

Wenn Sie eine zweite Instanz einer Anwendung hinzufügen möchten, muss Folgendes möglich sein:

-   Konfigurieren eines eindeutigen Bezeichners für die zweite Instanz. Sie können nicht den gleichen Bezeichner konfigurieren, den Sie bereits für die erste Instanz verwendet haben.

-   Konfigurieren eines anderen Zertifikats als des Zertifikats, das für die erste Instanz verwendet wurde.

Wenn die Anwendung keine der oben genannten Voraussetzungen unterstützt, können Sie keine zweite Instanz konfigurieren.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Wo wird das Format für EntityID (Benutzer-ID) festgelegt?

Sie können das Format für EntityID (Benutzer-ID), das von Azure AD nach der Benutzerauthentifizierung in der Antwort an die Anwendung gesendet wird, nicht auswählen.

Azure AD wählt das Format für das NameID-Attribut (Benutzer-ID) auf Grundlage des ausgewählten Werts oder des von der Anwendung in der SAML-Authentifizierungsanforderung angeforderten Formats aus. Weitere Informationen finden Sie im Artikel [SAML-Protokoll für einmaliges Anmelden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) im Abschnitt zu NameIDPolicy.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Wo erhalte ich die Metadaten für die Anwendung oder das Zertifikat aus Azure AD?

Führen Sie zum Herunterladen der Metadaten oder des Zertifikats für die Anwendung aus Azure AD die folgenden Schritte aus:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfiguriert haben.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

8. Wechseln Sie zum Abschnitt **SAML-Signaturzertifikat**, und klicken Sie auf den Spaltenwert **Herunterladen**. Abhängig davon, welche Werte die Anwendung für die Konfiguration des einmaligen Anmeldens benötigt, wird die Option zum Herunterladen des Metadaten-XML oder des Zertifikats angezeigt.

Azure AD stellt keine URL zum Abrufen der Metadaten bereit. Die Metadaten können nur als XML-Datei abgerufen werden.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Sie wissen nicht, wie man SAML-Ansprüche anpasst, die an eine Anwendung gesendet werden

Weitere Informationen zum Anpassen der SAML-Attributansprüche, die an Ihre Anwendung gesendet werden, finden Sie unter [Zuordnen von Benutzeransprüchen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## <a name="next-steps"></a>Nächste Schritte
[Verwalten von Anwendungen mit Azure Active Directory](what-is-application-management.md)
