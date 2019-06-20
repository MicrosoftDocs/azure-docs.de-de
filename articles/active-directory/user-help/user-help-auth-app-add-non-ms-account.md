---
title: Hinzufügen Ihrer Nicht-Microsoft-Konten zur Microsoft Authenticator-App – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie Informationen zum Hinzufügen Ihrer nicht von Microsoft stammenden Konten (z. B. für Google, Facebook oder GitHub) zur Microsoft Authenticator-App für die zweistufige Überprüfung.
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
ms.openlocfilehash: e6f94ba30c06fc6975ab212c895cecefe5d383fa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473905"
---
# <a name="add-your-non-microsoft-accounts"></a>Hinzufügen Ihrer Nicht-Microsoft-Konten
Fügen Sie Ihre nicht von Microsoft stammenden Konten (z. B. für Google, Facebook oder GitHub) zur Microsoft Authenticator-App für die zweistufige Überprüfung hinzu. Die Microsoft Authenticator-App funktioniert mit jeder App, welche die zweistufige Überprüfung verwendet, und mit jedem Konto, das die Standards für zeitbasierte Einmalkennwörter (TOTP) unterstützt.

>[!Important]
>Bevor Sie Ihr Konto hinzufügen können, müssen Sie die Microsoft Authenticator-App herunterladen und installieren. Wenn Sie dies noch nicht getan haben, führen Sie die im Artikel [Herunterladen und Installieren der App](user-help-auth-app-download-install.md) aufgeführten Schritte aus.

## <a name="add-personal-accounts"></a>Hinzufügen persönlicher Konten
Im Allgemeinen müssen Sie für alle Ihre persönlichen Konten wie folgt vorgehen:

1. Melden Sie sich bei Ihrem Konto an, und aktivieren Sie dann die zweistufige Überprüfung auf Ihrem Gerät oder Computer.

2. Fügen Sie das Konto zur Microsoft Authenticator-App hinzu. Im Rahmen dieses Vorgangs werden Sie möglicherweise aufgefordert, einen QR-Code zu scannen.

Wir geben hier den Vorgang für Facebook-, Google-, GitHub- und Amazon-Konten an, aber dieser Vorgang ist für alle anderen Apps (wie z. B. Instagram, Netflix oder Adobe) gleich.

## <a name="add-your-google-account"></a>Hinzufügen Ihres Google-Kontos
Fügen Sie Ihr Google-Konto hinzu, indem Sie die zweistufige Überprüfung aktivieren und dann das Konto zur App hinzufügen.

### <a name="turn-on-two-factor-verification"></a>Aktivieren der zweistufigen Überprüfung

1. Navigieren Sie auf Ihrem Computer zu https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, wählen Sie **Erste Schritte** aus, und bestätigen Sie dann Ihre Identität.

2. Führen Sie die Schritte auf der Seite aus, um die zweistufige Überprüfung für Ihr persönliches Google-Konto zu aktivieren.

### <a name="add-your-google-account-to-the-app"></a>Hinzufügen Ihres Google-Kontos zur App

1. Wechseln Sie auf Ihrem Computer auf der Google-Seite zum Abschnitt **Alternativen zweiten Schritt einrichten**, und wählen Sie im Abschnitt **Authenticator-App** die Option **Einrichten** aus.

2. Wählen Sie auf der Seite **Codes von der Authenticator App abrufen** basierend auf Ihrem Telefontyp entweder **Android** oder **iPhone** aus, und wählen Sie dann **Weiter** aus.

    Sie erhalten einen QR-Code, den Sie verwenden können, um der Microsoft Authenticator-App automatisch Ihr Konto zuzuordnen. Schließen Sie dieses Fenster nicht.

3. Öffnen Sie die Microsoft Authenticator-App, wählen Sie oben rechts über das Symbol zum **Anpassen und Steuern** die Option **Konto hinzufügen** aus, und wählen Sie dann **Anderes Konto (Google, Facebook usw.)** aus.

4. Verwenden Sie die Kamera Ihres Geräts, um auf Ihrem Computer auf der Seite **Authenticator einrichten** den QR-Code zu scannen.

    >[!Note]
    >Wenn die Kamera nicht ordnungsgemäß funktioniert, können Sie den QR-Code und die URL manuell eingeben.

5. Überprüfen Sie die Seite **Konten** der Microsoft Authenticator-App auf Ihrem Gerät, um sicherzustellen, dass Ihre Kontoinformationen korrekt sind und ein zugehöriger sechsstelliger Prüfcode vorhanden ist.

    Um die Sicherheit zu erhöhen, wird der Prüfcode alle 30 Sekunden geändert, um zu verhindern, dass jemand einen Code mehrmals verwendet.

6. Wählen Sie auf Ihrem Computer auf der Seite **Authenticator einrichten** die Option **Weiter** aus, geben Sie den in der App für Ihr Google-Konto angegebenen sechsstelligen Prüfcode ein, und wählen Sie dann **Überprüfen** aus.

7. Ihr Konto wurde überprüft. Danach können Sie **Fertig** auswählen, um die Seite **Authenticator einrichten** zu schließen.

    >[!NOTE]
    >Weitere Informationen zur zweistufigen Überprüfung und zu Ihrem Google-Konto finden Sie unter [Bestätigung in zwei Schritten aktivieren](https://support.google.com/accounts/answer/185839) und [Mehr Informationen zur Bestätigung in zwei Schritten](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Hinzufügen Ihres Facebook-Kontos
Fügen Sie Ihr Facebook-Konto hinzu, indem Sie die zweistufige Überprüfung aktivieren und dann das Konto zur App hinzufügen.

### <a name="turn-on-two-factor-verification"></a>Aktivieren der zweistufigen Überprüfung

1. Öffnen Sie auf Ihrem Computer Facebook, wählen Sie in der oberen rechten Ecke das Dropdownmenü aus, und wechseln Sie dann zu **Einstellungen** > **Sicherheit und Login**.

    Die Seite **Sicherheit und Login** wird angezeigt.

2. Navigieren Sie im Abschnitt **Zweistufige Authentifizierung** nach unten zur Option **Verwende die zweistufige Authentifizierung**, und wählen Sie dann **Bearbeiten** aus.

    Die Seite **Zweistufige Authentifizierung** wird angezeigt.

3. Wählen Sie **Aktivieren** aus.

### <a name="add-your-facebook-account-to-the-app"></a>Hinzufügen Ihres Facebook-Kontos zur App

1. Wechseln Sie auf Ihrem Computer auf der Facebook-Seite zum Abschnitt **Zusätzliche Sicherheit**, und wählen Sie dann im Bereich **Authentifizierungs-App** die Option **Einrichten** aus.

    Sie erhalten einen QR-Code, den Sie verwenden können, um der Microsoft Authenticator-App automatisch Ihr Konto zuzuordnen. Schließen Sie dieses Fenster nicht.

2. Öffnen Sie die Microsoft Authenticator-App, wählen Sie oben rechts über das Symbol zum **Anpassen und Steuern** die Option **Konto hinzufügen** aus, und wählen Sie dann **Anderes Konto (Google, Facebook usw.)** aus.

3. Verwenden Sie die Kamera Ihres Geräts, um auf Ihrem Computer auf der Seite **Zweistufige Authentifizierung** den QR-Code zu scannen.

    >[!Note]
    >Wenn die Kamera nicht ordnungsgemäß funktioniert, können Sie den QR-Code und die URL manuell eingeben.

4. Überprüfen Sie die Seite **Konten** der Microsoft Authenticator-App auf Ihrem Gerät, um sicherzustellen, dass Ihre Kontoinformationen korrekt sind und ein zugehöriger sechsstelliger Prüfcode vorhanden ist.

    Um die Sicherheit zu erhöhen, wird der Prüfcode alle 30 Sekunden geändert, um zu verhindern, dass jemand einen Code mehrmals verwendet.

5. Wählen Sie auf Ihrem Computer auf der Seite **Zweistufige Authentifizierung** die Option **Weiter** aus, und geben Sie dann den in der App für Ihr Facebook-Konto angegebenen sechsstelligen Prüfcode ein.

    Ihr Konto wurde überprüft. Sie können jetzt die App verwenden, um Ihr Konto zu überprüfen.

    >[!NOTE]
    >Unter [Was ist die zweistufige Authentifizierung und wie funktioniert sie?](https://www.facebook.com/help/148233965247823) finden Sie weitere Informationen zur zweistufigen Überprüfung und zu Ihrem Facebook-Konto.

## <a name="add-your-github-account"></a>Hinzufügen Ihres GitHub-Kontos
Fügen Sie Ihr GitHub-Konto hinzu, indem Sie die zweistufige Überprüfung aktivieren und dann das Konto zur App hinzufügen.

### <a name="turn-on-two-factor-verification"></a>Aktivieren der zweistufigen Überprüfung

1. Öffnen Sie auf Ihrem Computer GitHub, wählen Sie in der oberen rechten Ecke Ihr Image und dann **Settings** (Einstellungen) aus.

    Die Seite **Two-factor authentication** (Zweistufige Authentifizierung) wird angezeigt.

2. Wählen Sie auf der Randleiste **Personal settings** (Persönliche Einstellungen) die Option **Security** (Sicherheit) aus, und wählen Sie dann im Bereich **Two-factor authentication** (Zweistufige Authentifizierung) die Option **Enable two-factor authentication** (Zweistufige Authentifizierung aktivieren) aus.

### <a name="add-your-github-account-to-the-app"></a>Hinzufügen Ihres GitHub-Kontos zur App

1. Wählen Sie auf Ihrem Computer auf der Seite **Zweistufige Authentifizierung** die Option **Set up using an app** (Mit einer App einrichten) aus.

2. Speichern Sie Ihre Wiederherstellungscodes, damit Sie wieder auf Ihr Konto zugreifen können, falls Sie den Zugriff verlieren, und wählen Sie dann **Next** (Weiter) aus. 

    Sie können Ihre Codes speichern, indem Sie die Codes auf Ihr Gerät herunterladen, sie ausdrucken oder in ein Kennwort-Manager-Tool kopieren.

3. Wählen Sie auf der Seite **Two-factor authentication** (Zweistufige Authentifizierung) die Option **Set up using an app** (Mit einer App einrichten) aus.

    Daraufhin wird auf der Seite ein QR-Code angezeigt. Schließen Sie diese Seite nicht.

4. Öffnen Sie die Microsoft Authenticator-App, wählen Sie oben rechts über das Symbol zum **Anpassen und Steuern** die Option **Konto hinzufügen** aus, wählen Sie **Anderes Konto (Google, Facebook usw.)** aus, und wählen Sie dann oben auf der Seite die Option **Diesen Textcode eingeben** aus.

    Die Microsoft Authenticator-App kann den QR-Code nicht scannen, daher müssen Sie den Code manuell eingeben.

5. Geben Sie einen **Kontonamen** (z. B. GitHub) ein, geben Sie den **geheimen Schlüssel** aus Schritt 4 ein, und wählen Sie dann **Fertig stellen** aus.

4. Geben Sie auf Ihrem Computer auf der Seite **Zweistufige Authentifizierung** den in der App für Ihr GitHub-Konto angegebenen sechsstelligen Prüfcode ein, und wählen Sie dann **Aktivieren** aus.

    Auf der Seite **Konten** der App werden der Name Ihres Kontos und ein sechsstelliger Prüfcode angezeigt. Um die Sicherheit zu erhöhen, wird der Prüfcode alle 30 Sekunden geändert, um zu verhindern, dass jemand einen Code mehrmals verwendet.

    >[!NOTE]
    >Weitere Informationen zur zweistufigen Überprüfung und zu Ihrem GitHub-Konto finden Sie unter [About two-factor authentication](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Hinzufügen Ihres Amazon-Kontos
Fügen Sie Ihr Amazon-Konto hinzu, indem Sie die zweistufige Überprüfung aktivieren und dann das Konto zur App hinzufügen.

### <a name="turn-on-two-factor-verification"></a>Aktivieren der zweistufigen Überprüfung

1. Öffnen Sie auf Ihrem Computer Amazon, wählen Sie das Dropdownmenü **Konto und Listen** aus, und wählen Sie dann **Mein Konto** aus.

2. Wählen Sie **Anmelden und Sicherheit** aus, melden Sie sich bei Ihrem Amazon-Konto an, und wählen Sie dann im Bereich **Erweiterte Sicherheitseinstellungen** die Option **Bearbeiten** aus.

    Die Seite **Erweiterte Sicherheitseinstellungen** wird angezeigt.

3. Wählen Sie **Erste Schritte** aus.

4. Wählen Sie auf der Seite **Wählen Sie, wie Sie Codes erhalten möchten** die Option **Authentifizierungs-App** aus.

    Daraufhin wird auf der Seite ein QR-Code angezeigt. Schließen Sie diese Seite nicht.

5. Öffnen Sie die Microsoft Authenticator-App, wählen Sie oben rechts über das Symbol zum **Anpassen und Steuern** die Option **Konto hinzufügen** aus, und wählen Sie dann **Anderes Konto (Google, Facebook usw.)** aus.

6. Verwenden Sie die Kamera Ihres Geräts, um auf Ihrem Computer auf der Seite **Wählen Sie, wie Sie Codes erhalten möchten** den QR-Code zu scannen.

    >[!Note]
    >Wenn die Kamera nicht ordnungsgemäß funktioniert, können Sie den QR-Code und die URL manuell eingeben.

5. Überprüfen Sie die Seite **Konten** der Microsoft Authenticator-App auf Ihrem Gerät, um sicherzustellen, dass Ihre Kontoinformationen korrekt sind und ein zugehöriger sechsstelliger Prüfcode vorhanden ist.

    Um die Sicherheit zu erhöhen, wird der Prüfcode alle 30 Sekunden geändert, um zu verhindern, dass jemand einen Code mehrmals verwendet.

6. Geben Sie auf Ihrem Computer auf der Seite **Wählen Sie, wie Sie Codes erhalten möchten** den in der App für Ihr Amazon-Konto angegebenen sechsstelligen Prüfcode ein, und wählen Sie dann **Code verifizieren und fortfahren** aus.

7. Schließen Sie den Registrierungsprozess ab, einschließlich des Hinzufügens einer zusätzlichen Überprüfungsmethode, wie z. B. einer Textnachricht, und wählen Sie dann **Code senden** aus.

8. Geben Sie auf Ihrem Computer auf der Seite **Sicherungsmethode hinzufügen** den von der Sicherungsmethode für Ihr Amazon-Konto angegebenen sechsstelligen Prüfcode ein, und wählen Sie dann **Code verifizieren und fortfahren** aus.

9. Entscheiden Sie auf der Seite „Fast fertig“, ob Ihr Computer als vertrauenswürdiges Gerät gespeichert werden soll, und klicken Sie dann auf **OK. Aktivieren der zweistufigen Überprüfung** aus.

    Die Seite **Erweiterte Sicherheitseinstellungen** wird mit Ihren aktualisierten Details der zweistufigen Überprüfung (Zwei-Schritt-Verifizierung) angezeigt.

    >[!NOTE]
    >Weitere Informationen zur zweistufigen Überprüfung und zu Ihrem Amazon-Konto finden Sie unter [Über Zwei-Schritt-Verifizierung](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) und [Anmelden mit Zwei-Schritt-Verifizierung](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).


## <a name="next-steps"></a>Nächste Schritte

- Nachdem Ihre Konten der App hinzugefügt wurden, können Sie sich unter Verwendung der Authenticator-App auf Ihrem Gerät anmelden. Weitere Informationen finden Sie unter [Anmelden mithilfe der App](user-help-auth-app-sign-in.md).

- Für iOS-Geräte können Sie Ihre Kontoanmeldeinformationen und zugehörige App-Einstellungen, etwa die Reihenfolge Ihrer Konten, in der Cloud sichern. Weitere Informationen finden Sie unter [Sichern und Wiederherstellen von Kontoanmeldeinformationen mithilfe der Microsoft Authenticator-App](user-help-auth-app-backup-recovery.md).
