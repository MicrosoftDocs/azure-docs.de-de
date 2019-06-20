---
title: Problembehandlung bei der zweistufigen Überprüfung – Azure Active Directory | Microsoft-Dokumentation
description: Enthält Anweisungen für Benutzer zu den Schritten, die bei einem Problem mit Azure Multi-Factor Authentication und der zweistufigen Überprüfung auszuführen sind.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: b74049833b055caa112c346b74798893f2c0febf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60476659"
---
# <a name="get-help-with-two-step-verification"></a>Hilfe bei der zweistufigen Überprüfung

Die zweistufige Überprüfung ist eine Sicherheitsfunktion, mit der Ihre Organisation Ihre Konten schützt. Die zweistufige Überprüfung ist sicherer als die einfache Verwendung eines Kennworts, da sie auf zwei Authentifizierungsfaktoren basiert: Sie müssen etwas wissen – in diesem Fall Ihr Kennwort –, und Sie müssen etwas haben (Ihr Smartphone oder ein ähnliches geeignetes Gerät). Das, was Sie wissen, ist Ihr Kennwort, und das, was Sie bei sich tragen, ist Ihr Smartphone oder ein Gerät. Mithilfe der zweistufigen Überprüfung können Sie auch verhindern, dass sich böswillige Hacker, die Ihr Kennwort kennen, anmelden können.

Microsoft bietet die zweistufige Überprüfung, aber Ihre Organisation entscheidet, ob Sie das Feature verwenden. Sie können das Feature nicht deaktivieren, wenn es in Ihrer Organisation erforderlich ist – ähnlich wie bei einem Kennwort, das Sie verwenden müssen, um Ihr Konto zu schützen.

>[!Note]
>Weitere Informationen zur Verwendung der zweistufigen Überprüfung mit Ihrem persönlichen Microsoft-Konto finden Sie im Artikel [Informationen zur zweistufigen Überprüfung](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="why-do-i-need-to-use-another-verification-method"></a>Warum muss ich eine andere Überprüfungsmethode verwenden?

Es gibt verschiedene Gründe, weshalb Sie möglicherweise eine andere Überprüfungsmethode mit Ihrem Konto verwenden müssen. Beispiel:

- **Sie haben Ihr Smartphone oder Gerät vergessen.** An manchen Tagen lassen Sie Ihr Smartphone möglicherweise zu Hause, müssen sich aber dennoch bei der Arbeit anmelden. Zunächst sollten Sie versuchen, sich mit einer anderen Methode anzumelden, für die Ihr Smartphone nicht benötigt wird.

- **Sie haben Ihr Smartphone verloren oder haben eine neue Telefonnummer.** Wenn Sie Ihr Smartphone verloren oder eine neue Nummer erhalten haben, können Sie sich mit einer anderen Methode anmelden oder Ihren Administrator bitten, Ihre Einstellungen zu löschen. Es wird dringend empfohlen, dass Sie Ihren Administrator informieren, wenn Sie Ihr Smartphone verloren haben oder wenn es gestohlen wurde, damit Ihr Konto entsprechend aktualisiert werden kann. Nachdem Ihre Einstellungen gelöscht wurden, werden Sie bei der nächsten Anmeldung aufgefordert, sich für die [zweistufige Überprüfung zu registrieren](multi-factor-authentication-end-user-first-time.md).

- **Keinen Authentifizierungstext oder keinen Telefonanruf erhalten.** Es gibt verschiedene Gründe, warum Sie die SMS oder den Telefonanruf möglicherweise nicht erhalten. Wenn Sie zuvor bereits erfolgreich SMS-Nachrichten oder Telefonanrufe erhalten haben, liegt das Problem möglicherweise beim Telefonanbieter, nicht bei Ihrem Konto. Wenn Sie häufig Verzögerungen aufgrund eines schlechten Empfangs bemerken, empfiehlt sich die Verwendung der [Microsoft Authenticator-App](user-help-auth-app-download-install.md) auf Ihrem mobilen Gerät. Diese App kann zufällige Sicherheitscodes für die Anmeldung generieren, ohne ein Mobilfunksignal oder eine Internetverbindung vorauszusetzen.<br><br>Wenn Sie eine SMS erhalten möchten, bitten Sie einen Freund, Ihnen eine Test-SMS zu senden. So können Sie sicherstellen, dass der SMS-Empfang ordnungsgemäß funktioniert. Wenn Sie mehrere SMS erhalten haben, müssen Sie den Code der zuletzt erhaltenen verwenden.

- **Ihre App-Kennwörter funktionieren nicht.** App-Kennwörter ersetzen normale Kennwörter für ältere Desktopanwendungen, die die zweistufige Überprüfung nicht unterstützen. Stellen Sie zunächst sicher, dass Sie das Kennwort korrekt eingegeben haben. Wenn das Problem dadurch nicht behoben wird, [erstellen Sie ein neues App-Kennwort](multi-factor-authentication-end-user-app-passwords.md), oder wenden Sie sich an Ihren Administrator, um [Ihre vorhandenen App-Kennwörter zu löschen](../authentication/howto-mfa-userdevicesettings.md), damit Sie ein neues erstellen können.

## <a name="sign-in-using-another-verification-method"></a>Anmelden mit einer anderen Überprüfungsmethode

1. Melden Sie sich wie gewohnt bei Ihrem Konto an, und klicken Sie auf der Seite **Zweistufige Überprüfung** auf den Link **Auf andere Weise anmelden**.

    ![Ändern der Überprüfungsmethode für die Anmeldung](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Wenn Sie den Link **Auf andere Weise anmelden** nicht sehen, bedeutet dies, dass Sie keine anderen Überprüfungsmethoden eingerichtet haben. Wenden Sie sich an Ihren Administrator, damit er Ihnen bei der Anmeldung bei Ihrem Konto hilft. Fügen Sie nach der Anmeldung zusätzliche Überprüfungsmethoden hinzu. Weitere Informationen zum Hinzufügen von Überprüfungsmethoden finden Sie im Artikel [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md).<br><br>Wenn Sie zwar den Link, aber keine anderen Überprüfungsmethoden sehen, müssen Sie sich an Ihren Administrator wenden, damit dieser Ihnen bei der Anmeldung bei Ihrem Konto hilft.

2. Wählen Sie Ihre alternative Überprüfungsmethode aus, und fahren Sie mit dem zweistufigen Überprüfungsprozess fort.

3. Wenn Sie wieder zu Ihrem Konto zurückgekehrt sind, können Sie die Überprüfungsmethoden (bei Bedarf) aktualisieren. Weitere Informationen zum Hinzufügen oder Ändern von Methoden finden Sie im Artikel [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Ich konnte keine Lösung für mein Problem finden

Wenn Sie diese Schritte ausgeführt haben, aber weiterhin Probleme auftreten, wenden Sie sich an Ihren Administrator, um Hilfe zu erhalten.

## <a name="related-topics"></a>Verwandte Themen

* [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md)

* [Microsoft Authenticator-App – häufig gestellte Fragen](user-help-auth-app-faq.md)
