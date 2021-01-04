---
title: 'Tutorial: Multi-Factor Authentication für B2B – Azure AD'
description: In diesem Tutorial erfahren Sie, wie Sie eine mehrstufige Authentifizierung (Multi-Factor Authentification, MFA) erzwingen, wenn Sie Azure AD zur B2B-Zusammenarbeit mit externen Benutzern und Partnerorganisationen verwenden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f5f7f823d6c5f5860c2e78fe8cacdd1d788c581
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95974118"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Tutorial: Erzwingen der mehrstufigen Authentifizierung für B2B-Gastbenutzer

Bei der Zusammenarbeit mit externen B2B-Gastbenutzern wird empfohlen, Ihre Apps mit Richtlinien für die mehrstufige Authentifizierung (Multi-Factor Authentification, MFA) zu schützen. Denn dann benötigen externe Benutzer mehr als nur einen Benutzernamen und ein Kennwort, um auf Ihre Ressourcen zuzugreifen. In Azure Active Directory (Azure AD) ist dies mit einer Richtlinie für bedingten Zugriff möglich, die MFA für den Zugriff erfordert. MFA-Richtlinien können auf Mandanten-, App- oder individueller Benutzerebene erzwungen werden, so wie sie auch für Mitglieder Ihrer eigenen Organisation aktiviert werden können.

Beispiel:

![Diagramm: Gastbenutzer meldet sich bei Apps eines Unternehmens an](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  Ein Administrator oder Mitarbeiter von Unternehmen A lädt einen Gastbenutzer ein, eine Cloud oder lokale Anwendung zu verwenden, die so konfiguriert ist, dass sie MFA für den Zugriff erfordert.
2.  Der Gastbenutzer meldet sich mit seinem Geschäfts-, Schul- oder Unikonto bzw. mit seiner Identität bei einem sozialen Netzwerk an. 
3.  Der Benutzer wird aufgefordert, eine MFA auszuführen. 
4.  Der Benutzer richtet die MFA bei Unternehmen A ein und wählt deren MFA-Option aus. Dem Benutzer wird der Zugriff auf die Anwendung gewährt.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Testen der Anmeldung vor dem MFA-Setup.
> * Erstellen einer Richtlinie für bedingten Zugriff, die MFA für den Zugriff auf eine Cloud-App in Ihrer Umgebung verlangt. In diesem Tutorial wird dieser Vorgang anhand der Microsoft Azure-Verwaltungs-App veranschaulicht.
> * Verwenden des What If-Tools zum Simulieren der MFA-Anmeldung.
> * Testen der Richtlinie für bedingten Zugriff.
> * Löschen des Testbenutzers und der Richtlinie.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung des Szenarios im Rahmen dieses Tutorials benötigen Sie Folgendes:

 - **Zugriff auf Azure AD Premium**. Zum Funktionsumfang dieser Edition gehört die Richtlinie für bedingten Zugriff. Um MFA zu erzwingen, müssen Sie eine Azure AD-Richtlinie für bedingten Zugriff erstellen. Beachten Sie, dass MFA-Richtlinien in Ihrer Organisation immer durchgesetzt werden, unabhängig davon, ob Partner über MFA-Funktionen verfügen. Wenn Sie MFA für Ihr Unternehmen einrichten, benötigen Sie ausreichend Azure AD Premium-Lizenzen für Ihre Gastbenutzer. 
 - **Ein gültiges externes E-Mail-Konto**, das Sie Ihrem Mandantenverzeichnis als Gastbenutzer hinzufügen und zum Anmelden verwenden können. Wie Sie ein Gastkonto erstellen, erfahren Sie unter [Hinzufügen von B2B-Gastbenutzern im Azure-Portal](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Erstellen eines Testgastbenutzers in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Azure AD-Administrator an.
2. Wählen Sie im linken Bereich **Azure Active Directory** aus.
3.  Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
4.  Klicken Sie auf **Neuer Gastbenutzer**.

    ![Screenshot: Auswahl der Option „Neuer Gastbenutzer“](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  Geben Sie unter **Benutzername** die E-Mail-Adresse des externen Benutzers ein. Geben Sie optional eine Begrüßungsnachricht ein. 

    ![Screenshot: Eingabe der Einladungsnachricht für Gastbenutzer](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Klicken Sie auf **Einladen**. Die Einladung wird daraufhin automatisch an den Gastbenutzer gesendet. Daraufhin wird die Meldung **Benutzer erfolgreich eingeladen.** angezeigt. 
7.  Nach dem Senden der Einladung wird das Benutzerkonto dem Verzeichnis automatisch als Gast hinzugefügt.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Testen der Anmeldung vor dem MFA-Setup
1.  Melden Sie sich mit Ihrem Testbenutzernamen und dem dazugehörigen Kennwort im [Azure-Portal](https://portal.azure.com/) an.
2.  Beachten Sie, dass Sie auf das Azure-Portal nur mit Ihren Anmeldeinformationen zugreifen können. Eine zusätzliche Authentifizierung ist nicht erforderlich.
3.  Melden Sie sich ab.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Erstellen einer Richtlinie für bedingten Zugriff mit erforderlicher MFA
1.  Melden Sie sich am [Azure-Portal](https://portal.azure.com/) als Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
2.  Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus. 
3.  Wählen Sie auf der Seite **Azure Active Directory** im Abschnitt **Sicherheit** **Bedingter Zugriff** aus.
4.  Wählen Sie auf der Seite **Bedingter Zugriff** in der Symbolleiste oben **Neue Richtlinie** aus.
5.  Geben Sie auf der Seite **Neu** im Textfeld **Name** **Mehrstufige Authentifizierung für Zugriff auf B2B-Portal erforderlich** ein.
6.  Wählen Sie im Abschnitt **Zuweisungen** **Benutzer und Gruppen** aus.
7.  Wählen Sie auf der Seite **Benutzer und Gruppen** **Benutzer und Gruppen auswählen** und dann **Sämtliche Gastbenutzer (Vorschau)** aus.

    ![Screenshot: Auswahl aller Gastbenutzer](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Wählen Sie **Fertig** aus.
10. Wählen Sie auf der Seite **Neu** im Abschnitt **Zuweisungen** die Option **Cloud-Apps** aus.
11. Wählen Sie auf der Seite **Cloud-Apps** **Apps auswählen** und dann **Auswählen** aus.

    ![Screenshot: Seite „Cloud-Apps“ und Option „Auswählen“](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Wählen Sie auf der Seite **Auswählen** die Option **Microsoft Azure-Verwaltung** und dann **Auswählen** aus.

    ![Screenshot, auf dem die Option „Microsoft Azure-Verwaltung“ hervorgehoben ist](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Wählen Sie auf der Seite **Cloud-Apps** **Fertig** aus.
14. Wählen Sie auf der Seite **Neu** im Abschnitt **Zugriffskontrollen** die Option **Gewähren** aus.
15. Wählen Sie auf der Seite **Gewähren** die Option **Zugriff gewähren** aus, aktivieren Sie das Kontrollkästchen **Mehrstufige Authentifizierung erforderlich**, und wählen Sie dann **Auswählen** aus.

    ![Screenshot: Option „Mehrstufige Authentifizierung erforderlich“](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. Wählen Sie unter **Richtlinie aktivieren** die Option **An** aus.

    ![Screenshot: Option „Richtlinie aktivieren“ auf „Ein“ festgelegt](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Klicken Sie auf **Erstellen**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Simuliertes Anmelden mit der What If-Option

1.  Wählen Sie auf der Seite **Bedingter Zugriff – Richtlinien** **What If** aus. 

    ![Screenshot, auf dem hervorgehoben ist, wo Sie auf der Seite „Bedingter Zugriff – Richtlinien“ die Option „What if“ auswählen](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Wählen Sie **Benutzer**, Ihren Testgastbenutzer und dann **Auswählen** aus.

    ![Screenshot: ausgewählter Gastbenutzer](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Wählen Sie **Cloud-Apps** aus.
4.  Wählen Sie auf der Seite **Cloud-Apps** die Option **Apps auswählen** aus, und klicken Sie dann auf **Auswählen**. Wählen Sie in der Anwendungsliste **Microsoft Azure-Verwaltung** aus, und klicken Sie dann auf **Auswählen**. 

    ![Screenshot: ausgewählte Microsoft Azure-Verwaltungs-App](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Wählen Sie auf der Seite **Cloud-Apps** **Fertig** aus.
6.  Wählen Sie **What If** aus, und überprüfen Sie, ob Ihre neue Richtlinie auf der Registerkarte **Anzuwendende Richtlinien** unter **Auswertungsergebnisse** angezeigt wird.

    ![Screenshot: Auswahl der Option „What If“](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Testen der Richtlinie für bedingten Zugriff
1.  Melden Sie sich mit Ihrem Testbenutzernamen und dem dazugehörigen Kennwort im [Azure-Portal](https://portal.azure.com/) an.
2.  Sie sollten eine Anforderung für zusätzliche Authentifizierungsmethoden sehen. Beachten Sie, dass es einige Zeit dauern kann, bis die Richtlinie wirksam wird.

    ![Screenshot: Nachricht „Weitere Informationen erforderlich“](media/tutorial-mfa/mfa-required.png)
 
3.  Melden Sie sich ab.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie den Testbenutzer und die Testrichtlinie für bedingten Zugriff, wenn sie nicht mehr benötigt werden.
1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Azure AD-Administrator an.
2.  Wählen Sie im linken Bereich **Azure Active Directory** aus.
3.  Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
4.  Wählen Sie den Testbenutzer und dann **Benutzer löschen** aus.
5.  Wählen Sie im linken Bereich **Azure Active Directory** aus.
6.  Wählen Sie unter **Sicherheit** **Bedingter Zugriff** aus.
7.  Wählen Sie in der Liste **Richtlinienname** das Kontextmenü (...) für Ihre Testrichtlinie und dann **Löschen** aus. Klicken Sie auf **Ja**, um zu bestätigen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie eine Richtlinie für bedingten Zugriff erstellt, die voraussetzt, dass Gastbenutzer zum Anmelden bei einer Ihrer Cloud-Apps MFA verwenden. Weitere Informationen zum Hinzufügen von Gastbenutzern für die Zusammenarbeit finden Sie unter [Hinzufügen von Benutzern für die Azure Active Directory B2B-Zusammenarbeit im Azure-Portal](add-users-administrator.md).
