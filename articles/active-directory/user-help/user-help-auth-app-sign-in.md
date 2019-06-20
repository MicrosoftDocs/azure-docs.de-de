---
title: Anmelden an Ihren Konten per Microsoft Authenticator-App – Azure Active Directory | Microsoft-Dokumentation
description: Verwenden Sie die Microsoft Authenticator-App, um sich an Ihrem Geschäfts-, Schul- oder Unikonto oder Ihrem persönlichen Microsoft-Konto und anderen Konten anzumelden, indem Sie entweder die zweistufige Überprüfung oder die Anmeldung per Telefon nutzen.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc136cdc88c67e3b26aefc4982f49a2547e456db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60474016"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Anmelden an Ihren Konten per Microsoft Authenticator-App
Die Microsoft Authenticator-App unterstützt Sie bei der Kontoanmeldung, wenn Sie die zweistufige Überprüfung verwenden. Mit der zweistufigen Überprüfung können Sie die Sicherheit des Zugriffs auf Ihre Konten erhöhen, insbesondere, wenn Sie vertrauliche Informationen anzeigen. Kennwörter können vergessen, gestohlen oder kompromittiert werden. Die zweistufige Überprüfung ist eine zusätzliche Sicherheitsmaßnahme, die zum Schutz Ihres Kontos beiträgt, indem sie den Zugriff durch Unbefugte erschwert.

Die Verwendung der Microsoft Authenticator-App kann auf verschiedene Arten erfolgen:

- Lassen Sie sich nach der Anmeldung mit Ihrem Benutzernamen und Kennwort zu einer zweiten Überprüfungsmethode auffordern.

- Stellen Sie die Anmeldung ohne Kennwort bereit, indem der Benutzername und das mobile Gerät mit Fingerabdruck, Gesichtserkennung oder einer PIN verwendet wird.

  >[!Important]
  >Diese Methode der Anmeldung per Telefon funktioniert nur mit Geschäfts-, Schul- oder Unikonten bzw. Ihrem persönlichen Konto von Microsoft. Für alle Konten von anderen Anbietern als Microsoft müssen Sie den Standardprozess der zweistufigen Überprüfung verwenden.

## <a name="prerequisites"></a>Voraussetzungen
Sie müssen Folgendes durchführen, bevor Sie die Microsoft Authenticator-App verwenden können:

 1. Laden Sie die Microsoft Authenticator-App herunter, und installieren Sie sie. Falls Sie dies noch nicht getan haben, helfen Ihnen die Informationen unter [Erste Schritte mit der Microsoft Authenticator-App](user-help-auth-app-download-install.md) weiter.

 2. Fügen Sie Ihre Geschäfts-, Schul- oder Unikonten, persönlichen Konten und Drittanbieterkonten der Microsoft Authenticator-App hinzu. Eine ausführliche Beschreibung der Schritte finden Sie unter [Hinzufügen Ihres Geschäfts-, Schul- oder Unikontos](user-help-auth-app-add-work-school-account.md), [Hinzufügen Ihrer persönlichen Microsoft-Konten](user-help-auth-app-add-personal-ms-account.md) und [Hinzufügen Ihrer persönlichen Nicht-Microsoft-Konten](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Aktivieren und Verwenden der Anmeldung per Telefon für Ihr Geschäfts-, Schul- oder Unikonto
Die Anmeldung per Telefon ist eine Art der zweistufigen Überprüfung. Sie müssen Ihre Identität trotzdem bestätigen, indem Sie eine Ihnen bekannte Information und einen in Ihrem Besitz befindlichen Gegenstand angeben. Bei der Anmeldung per Telefon können Sie aber das Eingeben Ihres Kennworts überspringen und die gesamte Identitätsüberprüfung auf Ihrem mobilen Gerät durchführen.

Bevor Sie die Anmeldung per Telefon aktivieren können, müssen Sie die zweistufige Überprüfung aktivieren. Weitere Informationen zur Aktivierung der zweistufigen Überprüfung für ein Konto finden Sie unter [Add your work or school account](user-help-auth-app-add-work-school-account.md) (Hinzufügen Ihrer Geschäfts-, Schul- oder Unikonten) und [Add your personal Microsoft accounts](user-help-auth-app-add-personal-ms-account.md) (Hinzufügen Ihrer persönlichen Microsoft-Konten).

Die Anmeldung per Telefon ist auf iOS- und Android-Geräten mit Android 6.0 oder höher verfügbar.

### <a name="turn-on-phone-sign-in"></a>Aktivieren der Anmeldung per Telefon 

- Öffnen Sie die Microsoft Authenticator-App, navigieren Sie zu Ihrem Geschäfts-, Schul- oder Unikonto, und aktivieren Sie die Anmeldung per Telefon:

    - **Bei Anzeige des folgenden Symbols: ![Symbol zum Abschluss der Einrichtung](media/user-help-auth-app-sign-in/icon.png).** Wenn dieses Symbol neben dem Namen Ihres Geschäfts-, Schul- oder Unikontos angezeigt wird, bedeutet dies, dass Sie die telefonische Anmeldung für das Konto bereits durchgeführt haben. Unter Umständen werden Sie aufgefordert, Pushbenachrichtigungen für Ihr Konto hinzuzufügen, damit Sie außerhalb der App über Authentifizierungsanforderungen unterrichtet werden können.

    - **Bei Nutzung der App für die zweistufige Überprüfung:** Wenn Sie die App und die zweistufige Überprüfung bereits genutzt haben, können Sie den Dropdownpfeil neben dem Kontonamen und dann die Option **Anmeldung per Telefon aktivieren** wählen.
    
    - **Wenn Sie Ihr Geschäfts-, Schul- oder Unikonto nicht finden können:** Falls Sie Ihr Geschäfts-, Schul- oder Unikonto in der App unter **Konten** nicht finden können, bedeutet dies, dass Sie es der App noch nicht hinzugefügt haben. Fügen Sie Ihr Geschäfts-, Schul- oder Unikonto hinzu, indem Sie die Schritte im Artikel [Add your work or school account](user-help-auth-app-add-work-school-account.md) (Hinzufügen Ihrer Geschäfts-, Schul- oder Unikonten) ausführen.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Anmelden an Ihrem Konto mit der Anmeldung per Telefon
Nachdem Sie die Anmeldung per Telefon aktiviert haben, können Sie sich anmelden, indem Sie nur die Microsoft Authenticator-App nutzen.

1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an.

    Nach der Eingabe Ihres Benutzernamens wird das Fenster **Anmeldung genehmigen** mit einer zweistelligen Zahl angezeigt, und Sie werden aufgefordert, sich mit der Microsoft Authenticator-App anzumelden. Wenn Sie diese Anmeldemethode nicht verwenden möchten, können Sie **Stattdessen Ihr Kennwort verwenden** auswählen und sich mit Ihrem Kennwort anmelden.

    ![Feld „Anmeldung genehmigen“ auf dem Computer](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Öffnen Sie die Benachrichtigung bzw. die Microsoft Authenticator-App auf Ihrem Gerät, und tippen Sie dann die Zahl ein, die mit der Zahl auf dem Bildschirm **Anmeldung genehmigen** auf Ihrem Computer übereinstimmt.

    ![Feld „Anmeldung genehmigen“ auf dem Gerät](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Wählen Sie **Genehmigen** aus, wenn Sie den Anmeldeversuch anerkennen. Andernfalls wählen Sie **Verweigern** aus.

4. Verwenden Sie die Smartphone-PIN oder Ihren biometrischen Schlüssel, um die Authentifizierung abzuschließen.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Aktivieren und Verwenden der Anmeldung per Telefon für Ihre persönlichen Microsoft-Konten
Sie können die Anmeldung per Telefon für Ihr persönliches Microsoft-Konto aktivieren, z.B. für das Konto, mit dem Sie sich bei Outlook.com, Xbox oder Skype anmelden.

>[!NOTE]
>Zum Schutz Ihres Kontos ist es bei der Microsoft Authenticator-App erforderlich, dass Ihr Gerät mit einer PIN oder einem biometrischen Schlüssel gesperrt ist. Falls Sie Ihr Smartphone ohne Sperre verwenden, werden Sie vor der Aktivierung der Anmeldung per Telefon von der App zur Einrichtung einer Sicherheitssperre aufgefordert.

### <a name="turn-on-phone-sign-in"></a>Aktivieren der Anmeldung per Telefon 

- Öffnen Sie die Microsoft Authenticator-App, navigieren Sie zu Ihrem persönlichen Microsoft-Konto, und aktivieren Sie die Anmeldung per Telefon:

    - **Bei Anzeige des folgenden Symbols: ![Symbol zum Abschluss der Einrichtung](media/user-help-auth-app-sign-in/icon.png).** Wenn dieses Symbol neben dem Namen Ihres Kontos angezeigt wird, bedeutet dies, dass Sie die telefonische Anmeldung für das Konto bereits durchgeführt haben. Unter Umständen werden Sie aufgefordert, Pushbenachrichtigungen für Ihr Konto hinzuzufügen, damit Sie außerhalb der App über Authentifizierungsanforderungen unterrichtet werden können.

    - **Bei Nutzung der App für die zweistufige Überprüfung:** Wenn Sie die App und die zweistufige Überprüfung bereits genutzt haben, können Sie den Dropdownpfeil neben dem Kontonamen und dann die Option **Anmeldung per Telefon aktivieren** wählen.
    
    - **Wenn Sie Ihr Konto nicht finden können:** Falls Sie Ihr Konto in der App unter **Konten** nicht finden können, bedeutet dies, dass Sie es der App noch nicht hinzugefügt haben. Fügen Sie Ihr persönliches Microsoft-Konto hinzu, indem Sie die Schritte im Artikel [Add your personal Microsoft account](user-help-auth-app-add-personal-ms-account.md#add-your-personal-microsoft-account) (Hinzufügen Ihres persönlichen Microsoft-Kontos) ausführen.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Anmelden an Ihrem Konto mit der Anmeldung per Telefon

1. Wechseln Sie zur Anmeldeseite Ihres persönlichen Microsoft-Kontos, und wählen Sie den Link **Use the Microsoft Authenticator app instead** (Stattdessen die Microsoft Authenticator-App verwenden) aus, anstatt Ihr Kennwort einzugeben. 

    Microsoft sendet daraufhin eine Benachrichtigung an Ihr Smartphone. 

2. Bestätigen Sie die Benachrichtigung. 

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Anmelden per zweistufiger Überprüfung für Ihr Konto
Bei der Standardmethode der zweistufigen Überprüfung müssen Sie Ihren Benutzernamen und das zugehörige Kennwort auf dem Gerät eingeben, an dem Sie sich anmelden. Wählen Sie anschließend aus, ob über die Microsoft Authenticator-App eine Benachrichtigung gesendet werden soll oder ob Sie den zugehörigen Prüfcode im Fenster **Konten** der Microsoft Authenticator-App kopieren möchten. Sie aktivieren die zweistufige Überprüfung für Ihr Konto im Rahmen des Prozesses zum Hinzufügen des Kontos zur Microsoft Authenticator-App.

>[!Note]
>Falls Ihr Geschäfts-, Schul- oder Unikonto oder Ihr persönliches Konto im Fenster **Konten** der Microsoft Authenticator-App nicht angezeigt wird, bedeutet dies, dass Sie es der Microsoft Authenticator-App noch nicht hinzugefügt haben. Informationen zum Hinzufügen Ihres Kontos finden Sie unter [Add your work or school account](user-help-auth-app-add-work-school-account.md) (Hinzufügen Ihrer Geschäfts-, Schul- oder Unikonten) bzw. [Add your personal accounts](user-help-auth-app-add-personal-ms-account.md) (Hinzufügen Ihrer persönlichen Microsoft-Konten).

Die erforderlichen Schritte für die Anmeldung an Ihrem Geschäfts-, Schul- oder Unikonto oder Ihrem persönlichen Konto mit den unterschiedlichen Methoden der zweistufigen Überprüfung finden Sie unter [Anmelden mit der zweistufigen Überprüfung oder einer Sicherheitsinformation](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
|**Frage**|**Lösung**|
|--------------|-------------|
|**Inwiefern ist die Anmeldung per Telefon sicherer als die Eingabe eines Kennworts?**|Die meisten Benutzer melden sich heutzutage mit einem Benutzernamen und einem Kennwort bei einer Website oder App an. Leider können Kennwörter verlorengehen, gestohlen oder von Hackern erraten werden.<br><br>Nachdem Sie die Microsoft Authenticator-App eingerichtet haben, wird auf Ihrem Telefon ein Schlüssel zum Entsperren Ihres Kontos eingerichtet, der von der PIN Ihres Smartphones oder per biometrischer Sperre geschützt wird. Dieser Schlüssel wird dann verwendet, um bei der Anmeldung Ihre Identität zu bestätigen.<br><br>**Wichtig**<br>Ihre Daten werden nur verwendet, um Ihren Schlüssel lokal zu schützen. Er wird weder an die Cloud gesendet noch dort gespeichert.|
|**Wird die zweistufige Überprüfung durch die Anmeldung per Telefon ersetzt? Sollte ich sie deaktivieren?**|Die Anmeldung per Telefon ist eine Art der zweistufigen Überprüfung, bei der beide Schritte auf dem mobilen Gerät ausgeführt werden. Sie sollten die zweistufige Überprüfung aktiviert lassen, um für Ihr Konto für zusätzliche Sicherheit zu sorgen.|
|**Muss ich zwei Benachrichtigungen bestätigen, wenn ich die zweistufige Überprüfung für mein Konto aktiviert lasse?**| Nein. Die Anmeldung bei Ihrem Microsoft-Konto mit Ihrem Telefon zählt ebenfalls als zweistufige Überprüfung, sodass keine zweite Genehmigung erforderlich ist.|
|**Was ist, wenn ich mein Smartphone verliere oder nicht zur Hand habe? Wie kann ich auf mein Konto zugreifen?**| Sie können auf der Anmeldeseite jederzeit den Link „Stattdessen Ihr Kennwort verwenden“ auswählen und wieder Ihr Kennwort verwenden. Bei Verwendung der zweistufigen Überprüfung benötigen Sie jedoch weiterhin eine zweite Methode, um Ihre Identität zu bestätigen.<br><br>**Wichtig**<br>Sie sollten unbedingt sicherstellen, dass Sie Ihrem Konto mehr als eine Überprüfungsmethode zuordnen, die auf dem aktuellen Stand ist.<br><br>Sie können Ihre Überprüfungsmethoden für persönliche Konten auf Ihrer [Sicherheitseinstellungen](https://account.live.com/proofs/manage)-Seite verwalten. Für Geschäfts-, Schul- oder Unikonten können Sie zur Seite [Zusätzliche Sicherheitsüberprüfung](https://aka.ms/MFASetup) oder **Schützen Sie Ihr Konto** Ihrer Organisation wechseln, wenn Ihr Administrator die Sicherheitsinfos aktiviert hat. Weitere Informationen zu Sicherheitsinformationen finden Sie unter [Übersicht über die Sicherheitsinformationen (Vorschau)](user-help-security-info-overview.md).<br><br>Wenn Sie Ihre Überprüfungsmethoden nicht verwalten können, müssen Sie sich an Ihren Administrator wenden.|
|**Wie kann ich die Verwendung des Features beenden und wieder zur Verwendung meines Kennworts zurückkehren?**|Wählen Sie für persönliche Konten bei der Anmeldung den Link **Stattdessen Ihr Kennwort verwenden** aus. Ihre aktuelle Auswahl wird gespeichert und standardmäßig bei Ihrer nächsten Anmeldung angeboten. Falls Sie einmal zur Anmeldung per Telefon zurückkehren möchten, können Sie während der Anmeldung den Link **Stattdessen eine App verwenden** auswählen.<br><br>Für Geschäfts-, Schul- oder Unikonten müssen Sie entweder die Registrierung des Geräts auf der Seite **Einstellungen** der Microsoft Authenticator-App aufheben oder das Gerät im Bereich **Geräte und Aktivität** Ihres Profils deaktivieren. Weitere Informationen zum Deaktivieren Ihres Geräts in Ihrem Profil finden Sie unter [Aktualisieren Ihrer Profil- und Kontoinformationen im Portal „Meine Apps“](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information).|
|**Warum kann nicht ich mehr als ein Geschäfts-, Schul- oder Uni-Konto für die Anmeldung per Telefon verwenden?**| Ein Telefon muss für ein einzelnes Geschäfts-, Schul- oder Unikonto registriert sein. Wenn Sie die Anmeldung per Telefon für ein anderes Geschäfts-, Schul- oder Unikonto aktivieren möchten, müssen Sie zuerst die Registrierung des alten Geräts über die Seite **Einstellungen** aufheben.|
|**Kann ich mich mit meinem Telefon bei meinem Computer anmelden?**| Bei Ihrem Computer sollten Sie sich mithilfe von Windows Hello unter Windows 10 anmelden. Mit Windows Hello können Sie sich mit Ihrem Gesicht, Fingerabdruck oder Ihrer PIN anmelden.|

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie Probleme beim Abrufen des Prüfcodes für Ihr persönliches Microsoft-Konto haben, lesen Sie im Artikel **Sicherheitsinformation und Prüfcodes zum Microsoft-Konto** den Abschnitt [Problembehandlung beim Prüfcode](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes).

- Antworten auf allgemeinere Fragen zur App finden Sie unter [Microsoft Authenticator-App – häufig gestellte Fragen](user-help-auth-app-faq.md).

- Weitere Informationen zur zweistufigen Überprüfung finden Sie unter [Einrichten meines Kontos für die Überprüfung in zwei Schritten](multi-factor-authentication-end-user-first-time.md).

- Weitere Informationen zu Sicherheitsinformationen finden Sie unter [Übersicht über die Sicherheitsinformationen (Vorschau)](user-help-security-info-overview.md).
