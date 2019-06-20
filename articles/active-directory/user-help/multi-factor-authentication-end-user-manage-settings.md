---
title: Verwalten der Einstellungen für die zweistufige Überprüfung – Azure Active Directory | Microsoft-Dokumentation
description: Verwalten Sie die Verwendung von Azure Multi-Factor Authentication, z. B. das Ändern Ihrer Kontaktinformationen oder das Konfigurieren Ihrer Geräte.
services: active-directory
keywords: Client für Multi-Factor Authentication, Authentifizierungsproblem, Korrelations-ID
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 433c2d712ca4867a5ec59f86c333511070b6d507
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334467"
---
# <a name="manage-your-settings-for-two-step-verification"></a>Verwalten der Einstellungen für die zweistufige Überprüfung
Dieser Artikel enthält Antworten auf Fragen zum Aktualisieren der Einstellungen für die zweistufige Überprüfung oder die Multi-Factor Authentication. Wenn bei der Anmeldung bei Ihrem Konto Probleme auftreten, finden Sie Hilfe unter [Probleme bei der zweistufigen Überprüfung](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="where-to-find-the-settings-page"></a>So finden Sie die Seite mit den Einstellungen
Je nach der Konfiguration von Azure Multi-Factor Authentication in Ihrem Unternehmen gibt es mehrere Stellen, an denen Sie Einstellungen wie Ihre Telefonnummer ändern können.

Wenn der Unternehmenssupport eine bestimmte URL oder Schritte zum Verwalten der zweistufigen Überprüfung gesendet hat, befolgen Sie diese Anweisungen. Andernfalls sollten sich die Schritte mit den folgenden Anweisungen durchführen lassen. Wenn Sie diese Anweisungen befolgen, jedoch nicht die gleichen Optionen angezeigt werden, hat Ihr Unternehmen bzw. Ihre Bildungseinrichtung ein eigenes Portal konfiguriert. Erkundigen Sie sich bei Ihrem Administrator nach dem Link zu Ihrem Azure Multi-Factor Authentication-Portal.

**Für den Wechsel zur Seite „Zusätzliche Sicherheitsüberprüfung“**

- Wechseln Sie zur Adresse https://aka.ms/MFASetup.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

Wenn Sie nicht auf diesen Link klicken können, können Sie auch die Seite **Zusätzliche Sicherheitsüberprüfung** aufrufen, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich bei [https://myapps.microsoft.com](https://myapps.microsoft.com) an.  

2. Wählen Sie oben rechts Ihren Kontonamen aus, und wählen Sie dann **Profil**.

3. Klicken Sie auf **Zusätzliche Sicherheitsüberprüfung**.  

    ![MyApps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. Die Seite „Zusätzliche Sicherheitsüberprüfung“ wird mit Ihren Einstellungen geladen.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Ich möchte meine Telefonnummer ändern oder eine sekundäre Telefonnummer hinzufügen
Konfigurieren Sie unbedingt eine sekundäre Authentifizierungstelefonnummer.  Da sich Ihre primäre Telefonnummer und ihre mobile App wahrscheinlich auf demselben Telefon befinden, ist die sekundäre Telefonnummer die schnellste Möglichkeit, sich wieder bei Ihrem Konto anzumelden, sollte Ihr Telefon verloren gehen oder gestohlen werden.

> [!NOTE]
> Wenn Sie keinen Zugriff auf Ihre primäre Telefonnummer haben und Hilfe beim Zugriff auf Ihr Konto benötigen, finden Sie im Artikel [Probleme bei der zweistufigen Überprüfung](multi-factor-authentication-end-user-troubleshoot.md) weitere Informationen.  

**So ändern Sie Ihre primäre Telefonnummer**  

1. Wählen Sie auf der Seite **Zusätzliche Sicherheitsüberprüfung** das Textfeld mit Ihrer aktuellen Telefonnummer aus, und ändern Sie diese in Ihre neue Telefonnummer.  
2. Wählen Sie **Speichern** aus.  
3. Wenn dies die Telefonnummer ist, die Sie für Ihre bevorzugte Überprüfungsoption verwenden, müssen Sie die neue Nummer bestätigen, bevor Sie sie speichern können.  

**So fügen Sie eine sekundäre Telefonnummer hinzu**  

1. Aktivieren Sie auf der Seite „Zusätzliche Sicherheitsüberprüfung“ das Kontrollkästchen neben **Alternatives Telefon für Authentifizierung**.  
2. Geben Sie im Textfeld die sekundäre Telefonnummer ein.  
3. Wählen Sie **Speichern** aus, und Ihre Änderungen sind abgeschlossen.  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>Erneutes Anfordern der zweistufigen Überprüfung auf einem als vertrauenswürdig markierten Gerät

Abhängig von den Organisationseinstellungen steht möglicherweise ein Kontrollkästchen „Die nächsten **X** Tage nicht erneut fragen“ zur Verfügung, wenn Sie die zweistufige Überprüfung in Ihrem Browser ausführen. Wenn Sie dieses Kontrollkästchen aktivieren und dann Ihr Gerät verlieren oder befürchten, dass Ihr Konto kompromittiert wurde, sollten Sie die zweistufige Überprüfung auf allen Geräten wiederherstellen.

1. Wählen Sie auf der Seite „Zusätzliche Sicherheitsüberprüfung“ die Option **Multi-Factor Authentication auf Geräten wiederherstellen, die zuvor als vertrauenswürdig eingestuft worden sind**.
2. Bei der nächsten Anmeldung bei einem Gerät werden Sie aufgefordert, die zweistufige Überprüfung durchzuführen.

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Wie kann ich Microsoft Authenticator von meinem alten Gerät entfernen und zu einem neuen Gerät wechseln?
Wenn Sie die App von Ihrem Gerät deinstallieren oder das Gerät auf die Werkseinstellungen zurücksetzen, wird die Aktivierung im Back-End nicht entfernt. Weitere Informationen finden Sie unter [Microsoft Authenticator](user-help-auth-app-download-install.md).

## <a name="next-steps"></a>Nächste Schritte
* Unter [Probleme bei der zweistufigen Überprüfung](multi-factor-authentication-end-user-troubleshoot.md) finden Sie Tipps zur Problembehandlung und Hilfe.
* Richten Sie [App-Kennwörter](multi-factor-authentication-end-user-app-passwords.md) für Apps ein, die die zweistufige Überprüfung nicht unterstützen.
