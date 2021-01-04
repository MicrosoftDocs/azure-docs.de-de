---
title: Risikobasierter Benutzeranmeldeschutz in Azure Active Directory
description: In diesem Tutorial erfahren Sie, wie Sie Azure AD Identity Protection aktivieren, um Benutzer zu schützen, wenn für deren Konto eine Risikoanmeldung erkannt wird.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97aabfa39954aa6ba937166eb54c05ac4874ea7e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741337"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>Tutorial: Verwenden von Risikoerkennungen für Benutzeranmeldungen, um Azure AD Multi-Factor Authentication oder Kennwortänderungen auszulösen

Zum Schutz Ihrer Benutzer können Sie in Azure Active Directory (Azure AD) risikobasierte Richtlinien konfigurieren, um im Falle von riskantem Verhalten eine automatische Reaktion zu ermöglichen. Durch Azure AD Identity Protection-Richtlinien kann ein Anmeldeversuch automatisch blockiert oder eine zusätzliche Aktion wie etwa eine Kennwortänderung oder die Verwendung von Azure AD Multi-Factor Authentication angefordert werden. Diese Richtlinien können mit bereits vorhandenen Azure AD-Richtlinien für bedingten Zugriff kombiniert werden, um die Organisation noch besser zu schützen. Es kann sein, dass von Benutzern niemals ein riskantes Verhalten in einer dieser Richtlinien ausgelöst wird. Im Falle einer Sicherheitsgefährdung ist Ihre Organisation jedoch geschützt.

> [!IMPORTANT]
> In diesem Tutorial wird für Administratoren veranschaulicht, wie Azure AD Multi-Factor Authentication (risikobasiert) aktiviert wird.
>
> Wenn Ihr IT-Team die Verwendung von Azure AD Multi-Factor Authentication nicht aktiviert hat oder Sie Probleme mit der Anmeldung haben, wenden Sie sich an Ihren Helpdesk.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Grundlegendes zu den verfügbaren Richtlinien für Azure AD Identity Protection
> * Aktivieren der Registrierung für Azure AD Multi-Factor Authentication
> * Aktivieren von risikobasierten Kennwortänderungen
> * Aktivieren von risikobasierter Multi-Factor Authentication
> * Testen risikobasierter Richtlinien für Anmeldeversuche von Benutzern

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Einen funktionierenden Azure AD-Mandanten mit mindestens einer aktivierten Azure AD Premium P2- oder Testlizenz.
    * Erstellen Sie ggf. [ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ein Konto mit Berechtigungen vom Typ *Globaler Administrator*
* Eine für Self-Service-Kennwortzurücksetzung und Azure AD Multi-Factor Authentication konfigurierte Azure AD-Instanz
    * Absolvieren Sie bei Bedarf das [Tutorial zum Aktivieren der Self-Service-Kennwortzurücksetzung von Azure AD](tutorial-enable-sspr.md).
    * Absolvieren Sie bei Bedarf das [Tutorial zum Aktivieren von Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>Übersicht über Azure AD Identity Protection

Im Zuge der Anmeldeversuche von Benutzern werden von Microsoft Tag für Tag Billionen anonymisierter Signale erfasst und analysiert. Diese Signale tragen dazu bei, Muster für ordnungsgemäße Benutzeranmeldungen zu ermitteln und potenziell riskante Anmeldeversuche zu identifizieren. Von Azure AD Identity Protection können Anmeldeversuche von Benutzern überprüft und zusätzliche Maßnahmen eingeleitet werden, wenn ein verdächtiges Verhalten festgestellt wird:

Die Risikoerkennung von Azure AD Identity Protection kann durch einige der folgenden Dinge ausgelöst werden:

* Benutzer mit kompromittierten Anmeldeinformationen
* Anmeldungen über anonyme IP-Adressen
* Unmöglicher Ortswechsel zu atypischen Orten
* Anmeldungen über infizierte Geräte
* Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten
* Anmeldungen an unbekannten Standorten

In Azure AD Identity Protection stehen die drei folgenden Richtlinien zur Verfügung, um Benutzer zu schützen und auf verdächtige Aktivitäten zu reagieren. Sie können die Richtlinienerzwingung aktivieren/deaktivieren und Benutzer oder Gruppen auswählen, auf die die Richtlinie angewendet werden soll. Außerdem können Sie entscheiden, ob der Zugriff bei der Anmeldung blockiert oder ob zu einer zusätzlichen Aktion aufgefordert werden soll.

* Benutzerrisiko-Richtlinie
    * Dient dazu, Benutzerkonten mit möglicherweise kompromittierten Anmeldeinformationen zu identifizieren und darauf zu reagieren. Kann den Benutzer zur Erstellung eines neuen Kennworts auffordern.
* Anmelderisiko-Richtlinie
    * Dient dazu, verdächtige Anmeldeversuche zu identifizieren und darauf zu reagieren. Kann den Benutzer zu einer zusätzlichen Form der Verifizierung per Azure AD Multi-Factor Authentication auffordern.
* MFA-Registrierungsrichtlinie
    * Stellt sicher, dass Benutzer für Azure AD Multi-Factor Authentication registriert sind. Wenn ein Benutzer durch eine Anmelderisiko-Richtlinie zur mehrstufigen Authentifizierung aufgefordert wird, muss er bereits für Azure AD Multi-Factor Authentication registriert sein.

Wenn Sie eine Benutzer- oder Anmelderisiko-Richtlinie aktivieren, können Sie auch den Schwellenwert für die Risikostufe (*Niedrig und höher*, *Mittel und höher* oder *Hoch*) auswählen. Dadurch können Sie flexibel steuern, wie aggressiv die Kontrollen im Zusammenhang mit verdächtigen Anmeldeereignissen sein sollen.

Weitere Informationen zu Azure AD Identity Protection finden Sie im Artikel [Was ist Azure AD Identity Protection?](../identity-protection/overview-identity-protection.md).

## <a name="enable-mfa-registration-policy"></a>Aktivieren der MFA-Registrierungsrichtlinie

Azure AD Identity Protection enthält eine Standardrichtlinie, die dazu beitragen kann, dass sich Benutzer für Azure AD Multi-Factor Authentication registrieren. Falls Sie zusätzliche Richtlinien zum Schutz von Anmeldeereignissen verwenden, müssen Benutzer bereits für die mehrstufige Authentifizierung registriert sein. Wenn Sie diese Richtlinie aktivieren, müssen Benutzer nicht bei jedem Anmeldeereignis eine mehrstufige Authentifizierung durchlaufen. Von der Richtlinie wird lediglich der Registrierungsstatus eines Benutzers überprüft und der Benutzer bei Bedarf dazu aufgefordert, sich vorab zu registrieren.

Es empfiehlt sich, die MFA-Registrierungsrichtlinie für Benutzer zu aktivieren, für die die Aktivierung zusätzlicher Azure AD Identity Protection-Richtlinien geplant ist. Führen Sie zum Aktivieren dieser Richtlinie die folgenden Schritte aus:

1. Melden Sie sich mit dem globalen Administratorkonto am [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie die entsprechende Option aus. Wählen Sie anschließend **Sicherheit**. und dann unter der Menüüberschrift *Schützen* die Option **Identity Protection** aus.
1. Wählen Sie im Menü auf der linken Seite die **MFA-Registrierungsrichtlinie** aus.
1. Die Richtlinie gilt standardmäßig für *alle Benutzer*. Wählen Sie bei Bedarf **Zuweisungen** und anschließend die Benutzer oder Gruppen aus, auf die die Richtlinie angewendet werden soll.
1. Wählen Sie unter *Steuerungen* die Option **Zugriff** aus. Stellen Sie sicher, dass die Option *Azure AD MFA-Registrierung anfordern* aktiviert ist, und wählen Sie **Auswählen** aus.
1. Legen Sie **Richtlinie erzwingen** auf *Ein* fest, und wählen Sie **Speichern** aus.

    ![Screenshot: Festlegen, dass sich Benutzer für die mehrstufige Authentifizierung registrieren müssen (im Azure-Portal)](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Aktivieren der Benutzerrisiko-Richtlinie für eine Kennwortänderung

Microsoft arbeitet mit Ermittlern, Strafverfolgungsbehörden, verschiedenen Sicherheitsteams bei Microsoft und anderen vertrauenswürdigen Quellen zusammen, um Benutzername/Kennwort-Paare zu finden. Wenn eines dieser Paare einem Konto in Ihrer Umgebung entspricht, kann eine risikobasierte Kennwortänderung angefordert werden. Diese Richtlinie und Aktion erfordern, dass der Benutzer vor der Anmeldung sein Kennwort aktualisiert, um sicherzustellen, dass alle zuvor verfügbar gemachten Anmeldeinformationen unwirksam werden.

Führen Sie zum Aktivieren dieser Richtlinie die folgenden Schritte aus:

1. Wählen Sie im Menü auf der linken Seite die **Benutzerrisiko-Richtlinie** aus.
1. Die Richtlinie gilt standardmäßig für *alle Benutzer*. Wählen Sie bei Bedarf **Zuweisungen** und anschließend die Benutzer oder Gruppen aus, auf die die Richtlinie angewendet werden soll.
1. Navigieren Sie unter *Bedingungen* zu **Bedingungen auswählen > Wählen Sie eine Risikostufe aus.** , und wählen Sie dann *Mittel und höher* aus.
1. Wählen Sie **Auswählen** und anschließend **Fertig** aus.
1. Wählen Sie unter *Zugriff* die Option **Zugriff** aus. Stellen Sie sicher, dass die Optionen **Zugriff zulassen** und *Kennwortänderung erforderlich* aktiviert sind, und wählen Sie anschließend **Auswählen** aus.
1. Legen Sie **Richtlinie erzwingen** auf *Ein* fest, und wählen Sie **Speichern** aus.

    ![Screenshot: Aktivieren der Benutzerrisiko-Richtlinie im Azure-Portal](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>Aktivieren der Anmelderisiko-Richtlinie für die mehrstufige Authentifizierung

Das Verhalten der meisten Benutzer ist normal und nachverfolgbar. Im Falle einer Abweichung von dieser Norm ist es möglicherweise riskant, eine erfolgreiche Anmeldung zuzulassen. Stattdessen empfiehlt es sich gegebenenfalls, den Benutzer zu blockieren oder ihn aufzufordern, eine mehrstufige Authentifizierung zu durchlaufen. Ist die MFA-Überprüfung des Benutzers erfolgreich, können Sie den Anmeldeversuch als gültig betrachten und dem Benutzer den Zugriff auf die Anwendung oder den Dienst ermöglichen.

Führen Sie zum Aktivieren dieser Richtlinie die folgenden Schritte aus:

1. Wählen Sie im Menü auf der linken Seite die **Anmelderisiko-Richtlinie** aus.
1. Die Richtlinie gilt standardmäßig für *alle Benutzer*. Wählen Sie bei Bedarf **Zuweisungen** und anschließend die Benutzer oder Gruppen aus, auf die die Richtlinie angewendet werden soll.
1. Navigieren Sie unter *Bedingungen* zu **Bedingungen auswählen > Wählen Sie eine Risikostufe aus.** , und wählen Sie dann *Mittel und höher* aus.
1. Wählen Sie **Auswählen** und anschließend **Fertig** aus.
1. Wählen Sie unter *Zugriff* die Option **Steuerung auswählen** aus. Stellen Sie sicher, dass die Optionen **Zugriff zulassen** und *Mehrstufige Authentifizierung anfordern* aktiviert sind, und wählen Sie anschließend **Auswählen** aus.
1. Legen Sie **Richtlinie erzwingen** auf *Ein* fest, und wählen Sie **Speichern** aus.

    ![Screenshot: Aktivieren der Anmelderisiko-Richtlinie im Azure-Portal](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Testen riskanter Anmeldeereignisse

Die risikobasierten Richtlinien, die in den vorherigen Schritten konfiguriert wurden, werden von den meisten Benutzeranmeldeereignissen nicht ausgelöst. Es kann sein, dass ein Benutzer niemals zu einer zusätzlichen mehrstufigen Authentifizierung oder zum Zurücksetzen seines Kennworts aufgefordert wird. Solange die Anmeldeinformationen des Benutzers sicher sind und sein Verhalten konsistent bleibt, sind die Anmeldeereignisse des Benutzers erfolgreich.

Sie benötigen also eine Möglichkeit, riskantes Verhalten oder potenzielle Angriffe zu simulieren, um die Azure AD Identity Protection-Richtlinien zu testen. Die Schritte für diese Tests sind abhängig von der Azure AD Identity Protection-Richtlinie, die Sie überprüfen möchten. Weitere Informationen zu entsprechenden Szenarien und Schritten finden Sie unter [Simulieren von Risikoerkennungen in Identity Protection](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ihre Tests abgeschlossen haben und die risikobasierten Richtlinien deaktivieren möchten, kehren Sie jeweils zu der Richtlinie zurück, die Sie deaktivieren möchten, und legen Sie **Richtlinie erzwingen** auf *Aus* fest.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie risikobasierte Benutzerrichtlinien für Azure AD Identity Protection aktiviert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Grundlegendes zu den verfügbaren Richtlinien für Azure AD Identity Protection
> * Aktivieren der Registrierung für Azure AD Multi-Factor Authentication
> * Aktivieren von risikobasierten Kennwortänderungen
> * Aktivieren von risikobasierter Multi-Factor Authentication
> * Testen risikobasierter Richtlinien für Anmeldeversuche von Benutzern

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
