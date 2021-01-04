---
title: Allgemeine Probleme mit der zweistufigen Kontoauthentifizierung – Azure AD
description: Lösungen für einige häufiger auftretende Probleme mit der zweistufigen Überprüfung in Verbindung mit Ihrem Geschäfts-, Schul- oder Unikonto.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/01/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: contperf-fy21q1
ms.openlocfilehash: caa2c66d66cce09f5b5b0fe0948ab924389d756e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033781"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Allgemeine Probleme mit der zweistufigen Überprüfung in Verbindung mit Ihrem Geschäfts-, Schul- oder Unikonto

Es gibt einige gängige Probleme bei der zweistufigen Überprüfung, die offenbar häufiger auftreten, als uns lieb ist. Dieser Artikel wurde zusammengestellt, um die Lösungen für die häufigsten Probleme bereitzustellen.

Ihre Azure Active Directory-Organisation (Azure AD) kann die zweistufige Überprüfung für Ihr Konto aktivieren. Wenn die zweistufige Überprüfung aktiviert ist, erfordert Ihre Kontoanmeldung eine Kombination aus den folgenden Daten:

- Ihr Benutzername
- Ihr Kennwort
- mobiles Gerät oder Smartphone

Die zweistufige Überprüfung ist sicherer als die einfache Verwendung eines Kennworts, da sie auf zwei Anforderungen basiert: Sie müssen etwas _wissen_, und Sie müssen etwas _haben_. Kein Hacker verfügt über Ihr physisches Smartphone.

>[!Important]
>Administratoren finden in der [Azure AD-Dokumentation](../index.yml) weitere Informationen zum Einrichten und Verwalten der Azure AD-Umgebung.

Dieser Inhalt kann Ihnen bei Ihrem Geschäfts-, Schul- oder Unikonto helfen, also dem Konto, das Ihnen von Ihrer Organisation zur Verfügung gestellt wird (z. B. dritan@contoso.com). Bei Problemen mit der zweistufigen Überprüfung in Verbindung mit einem persönlichen Microsoft-Konto, also dem Konto, das Sie für sich selbst eingerichtet haben (z. B. danielle@outlook.com) lesen Sie den Artikel [Aktivieren oder Deaktivieren der Überprüfung in zwei Schritten für Ihr Microsoft-Konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Ich habe mein mobiles Gerät nicht bei mir

Das kommt vor. Sie haben Ihr mobiles Gerät zu Hause gelassen und können es jetzt nicht verwenden, um Ihre Identität zu bestätigen. Vielleicht haben Sie zuvor eine alternative Methode für die Anmeldung bei Ihrem Konto hinzugefügt (z. B. Geschäftssmartphone). In diesem Fall können Sie diese alternative Methode jetzt verwenden. Wenn Sie keine zusätzliche Überprüfungsmethode hinzugefügt haben, können Sie den Helpdesk Ihrer Organisation um Unterstützung bitten.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>So melden Sie sich mit einer anderen Überprüfungsmethode bei Ihrem Geschäfts-, Schul- oder Unikonto an

1. Melden Sie sich bei Ihrem Konto an, wählen Sie aber auf der Seite **Zweistufige Überprüfung** den Link **Auf andere Weise anmelden** aus.

    ![Ändern der Überprüfungsmethode für die Anmeldung](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Wenn Sie den Link **Auf andere Weise anmelden** nicht sehen, bedeutet dies, dass Sie keine anderen Überprüfungsmethoden eingerichtet haben. Wenden Sie sich an Ihren Administrator, damit er Ihnen bei der Anmeldung bei Ihrem Konto hilft.

2. Wählen Sie Ihre alternative Überprüfungsmethode aus, und fahren Sie mit dem zweistufigen Überprüfungsprozess fort.

## <a name="i-cant-turn-two-factor-verification-off"></a>Ich kann die zweistufige Überprüfung nicht deaktivieren

- Wenn Sie die zweistufige Überprüfung mit einem persönlichen Konto für einen Microsoft-Dienst verwenden (z. B. alain@outlook.com), können Sie [das Feature aktivieren und deaktivieren](https://account.live.com/proofs/Manage).

- Wenn Sie die zweistufige Überprüfung mit Ihrem Geschäfts-, Schul- oder Unikonto verwenden, bedeutet dies höchstwahrscheinlich, dass Ihre Organisation entschieden hat, dass Sie dieses zusätzliche Sicherheitsfeature verwenden müssen. Es gibt keine Möglichkeit, es einzeln zu deaktivieren.

Wenn Sie die zweistufige Überprüfung nicht deaktivieren können, liegt dies möglicherweise auch an den Sicherheitsstandards, die auf Organisationsebene angewendet wurden. Weitere Informationen zu Sicherheitsstandards finden Sie unter [Was sind Sicherheitsstandards?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="my-device-was-lost-or-stolen"></a>Mein Gerät ist verloren gegangen oder wurde gestohlen.

Wenn Sie Ihr mobiles Gerät verloren haben oder dies gestohlen wurde, können Sie eine der folgenden Aktionen ausführen:

- Anmelden mithilfe einer anderen Methode
- Helpdesk Ihrer Organisation bitten, die Einstellungen zu löschen

Es wird dringend empfohlen, den Helpdesk Ihrer Organisation zu informieren, wenn Sie Ihr Smartphone verloren haben oder es gestohlen wurde. Der Helpdesk kann dann die entsprechenden Updates für Ihr Konto vornehmen. Nachdem Ihre Einstellungen gelöscht wurden, werden Sie bei der nächsten Anmeldung aufgefordert, sich für die [zweistufige Überprüfung zu registrieren](multi-factor-authentication-end-user-first-time.md).

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Ich empfange den Prüfcode nicht, der an mein mobiles Gerät gesendet wurde

Es ist ein häufiges Problem, den Prüfcode nicht zu erhalten. Das Problem bezieht sich in der Regel auf Ihr mobiles Gerät und dessen Einstellungen. Hier werden einige Aktionen beschrieben, die Sie ausprobieren können.

Versuchen Sie Folgendes | Info und Anleitung
--------- | ------------
Verwenden Sie die Microsoft Authenticator-App oder Bestätigungscodes. | Bei der Anmeldung wird die Fehlermeldung „Sie haben das Limit für Verifizierungsanrufe erreicht.“ oder „Sie haben das Limit für SMS-Prüfcodes erreicht.“ angezeigt. <br/><br/>Microsoft begrenzt möglicherweise wiederholte Authentifizierungsversuche, die innerhalb kurzer Zeit vom gleichen Benutzer durchgeführt werden. Diese Einschränkung gilt nicht für die Verifizierung per Microsoft Authenticator oder Prüfcode. Wenn diese Grenzwerte erreicht sind, können Sie die Authenticator-App oder einen Prüfcode verwenden oder nach einigen Minuten erneut versuchen, sich anzumelden.
Starten Sie Ihr mobiles Gerät neu | Gelegentlich benötigt Ihr Gerät nur einen Neustart. Wenn Sie Ihr Gerät neu starten, werden alle Hintergrundprozesse und Dienste beendet. Beim Neustart werden auch die Kernkomponenten des Geräts heruntergefahren. Jeder Dienst oder jede Komponente wird aktualisiert, wenn Sie Ihr Gerät neu starten.
Überprüfen Sie, ob Ihre Sicherheitsinformationen richtig sind | Stellen Sie sicher, dass die Informationen zu Ihrer Sicherheitsüberprüfungsmethode zutreffend sind, insbesondere Ihre Telefonnummern. Wenn Sie eine falsche Telefonnummer eingeben, landen alle Ihre Benachrichtigungen bei dieser falschen Nummer. Glücklicherweise kann dieser Benutzer mit den Benachrichtigungen nichts anfangen, aber es wird Ihnen auch nicht helfen, sich bei Ihrem Konto anzumelden. Um sicherzustellen, dass Ihre Informationen richtig sind, lesen Sie die Anweisungen im Artikel [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md).
Überprüfen Sie, ob Benachrichtigungen aktiviert sind | Stellen Sie sicher, dass die Benachrichtigungen auf Ihrem mobilen Gerät aktiviert sind. Vergewissern Sie sich, dass folgende Benachrichtigungsmodi zulässig sind: <br/><br/> &bull; Telefonanrufe <br/> &bull; Ihre Authentifizierungs-App <br/> &bull; Ihre Messaging-App <br/><br/> Stellen Sie sicher, dass diese Modi eine Warnung erstellen, die auf Ihrem Gerät _sichtbar_ ist.
Stellen Sie sicher, dass Sie über ein Gerätesignal und eine Internetverbindung verfügen | Vergewissern Sie sich, dass Ihre Telefonanrufe und SMS-Nachrichten zu Ihrem mobilen Gerät gelangen. Bitten Sie einen Freund, Sie anzurufen und Ihnen eine SMS zu schicken, um sicherzustellen, dass beides möglich ist. Wenn Sie den Anruf oder die Textnachricht nicht erhalten, überprüfen Sie zunächst, ob Ihr mobiles Gerät eingeschaltet ist. Wenn Ihr Gerät eingeschaltet ist, Sie den Anruf oder die Nachricht aber noch immer nicht empfangen, liegt wahrscheinlich ein Problem mit Ihrem Netzwerk vor. Sie müssen Ihren Anbieter kontaktieren. Wenn Sie häufig signalbedingte Probleme haben, empfiehlt sich die Installation und Verwendung der [Microsoft Authenticator-App](user-help-auth-app-download-install.md) auf Ihrem mobilen Gerät. Diese App kann zufällige Sicherheitscodes für die Anmeldung generieren, ohne ein Mobilfunksignal oder eine Internetverbindung vorauszusetzen.
Deaktivieren Sie „Nicht stören“ | Stellen Sie sicher, dass das Funktionsmerkmal **Nicht stören** für Ihr mobiles Gerät deaktiviert ist. Wenn dieses Funktionsmerkmal aktiviert ist, sind keine Benachrichtigungen auf Ihrem Mobilgerät möglich. In der Bedienungsanleitung Ihres Mobilgeräts finden Sie Anweisungen, wie Sie dieses Funktionsmerkmal deaktivieren können.
Aufheben der Sperre von Telefonnummern | In den USA stammen Anrufe von Microsoft von folgenden Telefonnummern: + 1 (866) 539 4191, + 1 (855) 330 8653 und + 1 (877) 668 6536.
Überprüfen Sie akkubezogene Einstellungen | Wenn Sie Ihre Akkuoptimierung so eingerichtet haben, dass seltener genutzte Apps nicht im Hintergrund aktiv bleiben, ist Ihr Benachrichtigungssystem höchstwahrscheinlich betroffen. Versuchen Sie, die Akkuoptimierung für Ihre Authentifizierungs- und Messaging-App zu deaktivieren. Versuchen Sie dann noch mal, sich bei Ihrem Konto anzumelden.
Deaktivieren von Sicherheits-Apps von Drittanbietern | Einige Sicherheits-Apps für Smartphones blockieren Textnachrichten und Anrufe von lästigen unbekannten Anrufern. Eine Sicherheits-Apps verhindern möglicherweise, dass Ihr Smartphone den Prüfcode empfängt. Versuchen Sie, alle Sicherheits-Apps von Drittanbietern auf Ihrem Telefon zu deaktivieren, und fordern Sie dann einen weiteren Prüfcode an.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Ich werde nicht zur Eingabe meiner zweiten Überprüfungsinformation aufgefordert

Melden Sie sich mit Ihrem Benutzernamen und Kennwort bei Ihrem Geschäfts-, Schul- oder Unikonto an. Als Nächstes sollten Sie aufgefordert werden, zusätzliche Sicherheitsüberprüfungsinformationen anzugeben. Wenn Sie nicht dazu aufgefordert werden, haben Sie Ihr Gerät möglicherweise noch nicht für derartige Vorgänge eingerichtet. Ihr Mobilgerät muss entsprechend eingerichtet sein, damit es mit Ihrer speziellen zusätzlichen Sicherheitsüberprüfungsmethode funktioniert.

Eventuell haben Sie Ihr Gerät noch nicht eingerichtet. Ihr mobiles Gerät muss entsprechend eingerichtet sein, damit es mit Ihrer speziellen zusätzlichen Sicherheitsüberprüfungsmethode funktioniert. Weitere Informationen zu den Schritten, mit denen Sie Ihr mobiles Gerät für die Verwendung mit Ihrer Überprüfungsmethode verfügbar machen, finden Sie unter [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md). Wenn Sie wissen, dass Sie Ihr Gerät oder Ihr Konto noch nicht eingerichtet haben, befolgen Sie die Schritte im Artikel [Einrichten meines Kontos für die zweistufige Überprüfung](multi-factor-authentication-end-user-first-time.md).

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Ich habe eine neue Telefonnummer und möchte diese hinzufügen

Wenn Sie eine neue Telefonnummer haben, müssen Sie die Details der Sicherheitsüberprüfungsmethode aktualisieren. Auf diese Weise werden die Überprüfungsanforderungen an den richtigen Empfänger gesendet. Führen Sie die Schritte im Abschnitt **Hinzufügen oder Ändern Ihrer Telefonnummer** des Artikels [Ändern der zweistufigen Überprüfungsmethode und der Einstellungen](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) aus, um die Überprüfungsmethode zu aktualisieren.

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Ich habe ein neues mobiles Gerät und möchte dieses hinzufügen

Wenn Sie über ein neues mobiles Gerät verfügen, müssen Sie es so einrichten, dass es mit der zweistufigen Überprüfung funktioniert. Dies ist ein mehrstufiger Prozess:

1. Richten Sie Ihr Gerät für Ihr Konto ein, indem Sie die Schritte im Artikel [Worum handelt es sich bei der Seite „Zusätzliche Sicherheitsüberprüfung“?](multi-factor-authentication-end-user-first-time.md) ausführen.

1. Aktualisieren Sie Ihre Konto- und Geräteinformationen auf der Seite **Zusätzliche Sicherheitsüberprüfung**. Führen Sie das Update aus, indem Sie das alte Gerät löschen und das neue Gerät hinzufügen. Weitere Informationen finden Sie im Artikel [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md).

Optionale Schritte:

- Sie können die Microsoft Authenticator-App auf Ihrem mobilen Gerät installieren, indem Sie die Schritte im Artikel [Herunterladen und Installieren der Microsoft Authenticator-App](user-help-auth-app-download-install.md) ausführen.

- Aktivieren Sie die zweistufige Überprüfung auf Ihren vertrauenswürdigen Geräten durch Ausführen der Schritte in **Erneutes Anfordern der zweistufigen Überprüfung auf einem als vertrauenswürdig markierten Gerät** des Artikels [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Ich habe auf Reisen Probleme bei der Anmeldung bei meinem mobilen Gerät

Möglicherweise ist es schwieriger, eine Überprüfungsmethode für mobile Geräte wie z. B. SMS zu nutzen, wenn Sie im Ausland sind. Es ist auch möglich, dass Ihr mobiles Gerät Roaminggebühren verursacht. Für diese Situation empfehlen wir Ihnen, die Microsoft Authenticator-App zu verwenden, die die Möglichkeit bietet, sich mit einem WLAN-Hotspot zu verbinden. Weitere Informationen zum Einrichten der Microsoft Authenticator-App auf Ihrem mobilen Gerät finden Sie im Artikel [Herunterladen und Installieren der Microsoft Authenticator-App](user-help-auth-app-download-install.md).

## <a name="i-cant-get-my-app-passwords-to-work"></a>Meine App-Kennwörter funktionieren nicht

App-Kennwörter ersetzen normale Kennwörter für ältere Desktopanwendungen, die die zweistufige Überprüfung nicht unterstützen. Stellen Sie zunächst sicher, dass Sie das Kennwort korrekt eingegeben haben. Wenn das Problem dadurch nicht behoben wird, versuchen Sie, ein neues Kennwort für die App zu erstellen. Führen Sie hierzu die Schritte im Abschnitt **Erstellen und Löschen von App-Kennwörtern auf der Seite „Zusätzliche Sicherheitsüberprüfung“** des Artikels [Verwalten von App-Kennwörtern für die zweistufige Überprüfung](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) aus.

## <a name="i-cant-turn-off-two-factor-verification"></a>Ich kann die zweistufige Überprüfung nicht deaktivieren

Wenn Sie die zweistufige Überprüfung mit Ihrem Geschäfts-, Schul- oder Unikonto (z. B. alain@contoso.com) verwenden, bedeutet dies höchstwahrscheinlich, dass Ihre Organisation entschieden hat, dass Sie dieses zusätzliche Sicherheitsfeature verwenden müssen. Da Ihre Organisation die Verwendung des Features vorgeschrieben hat, kann es nicht individuell deaktiviert werden. Wenn Sie jedoch die zweistufige Überprüfung mit einem persönlichen Konto wie alain@outlook.comverwenden, können Sie das Feature aktivieren und deaktivieren. Anweisungen zum Steuern der zweistufigen Überprüfung für Ihre persönlichen Konten finden Sie unter [Aktivieren oder Deaktivieren der Überprüfung in zwei Schritten für Ihr Microsoft-Konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Wenn Sie die zweistufige Überprüfung nicht deaktivieren können, liegt dies möglicherweise auch an den Sicherheitsstandards, die auf Organisationsebene angewendet wurden. Weitere Informationen zu Sicherheitsstandards finden Sie unter [Was sind Sicherheitsstandards?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Ich konnte keine Lösung für mein Problem finden

Wenn Sie diese Schritte ausgeführt haben, aber weiterhin Probleme auftreten, wenden Sie sich zwecks Unterstützung an den Helpdesk Ihrer Organisation.

## <a name="related-articles"></a>Verwandte Artikel

- [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md)

- [Einrichten meines Kontos für die zweistufige Überprüfung](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator-App – häufig gestellte Fragen](user-help-auth-app-faq.md)