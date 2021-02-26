---
title: Importieren von Kennwörtern in die Microsoft Authenticator-App – Azure AD
description: Importieren von Kennwörtern aus gängigen Kennwort-Managern in die Microsoft Authentication-App
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806154"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Importieren von Kennwörtern in die Microsoft Authenticator-App

Microsoft Authenticator unterstützt das Importieren von Kennwörtern aus Google Chrome, Firefox, LastPass, Bitwarden und RoboForm. Wenn Ihr vorhandener Kennwort-Manager von Microsoft derzeit nicht unterstützt wird, können Sie die [Anmeldeinformationen manuell in eine Textdateivorlage (.csv)](https://go.microsoft.com/fwlink/?linkid=2134938) eingeben. Exportieren Sie einfach Ihre Kennwörter aus Ihrem vorhandenen Kennwort-Manager in das CSV-Format, um sie in die Authenticator-App zu importieren und dort zu verwalten. Importieren Sie dann die exportierte CSV-Datei in Authenticator über die Browsererweiterung von Chrome oder direkt in die Authenticator-App (Android und iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importieren aus Google Chrome oder Android Smart Lock

Sie können Ihre Kennwörter aus Google Chrome oder Android Smart Lock in Authenticator entweder auf Ihrem Smartphone oder auf Ihrem Desktopcomputer importieren. Ihre Möglichkeiten:

- [Importieren aus Chrome unter Android und iOS](#import-from-chrome-on-android-and-ios)
- [Importieren aus Chrome-Desktopbrowser](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importieren aus Chrome unter Android und iOS

Google Chrome-Benutzer können ihre Kennwörter mit wenigen einfachen Schritten direkt von ihrem Android- oder Apple-Smartphone aus importieren.

1. Installieren Sie die Authenticator-App auf Ihrem Smartphone, und öffnen Sie die Registerkarte **Kennwörter**.

1. Melden Sie sich auf Ihrem Smartphone bei Google Chrome an.

1. Tippen Sie oben rechts (unter Android) bzw. unten rechts (unter iOS) auf das ![Google Chrome-Menü mit den Auslassungspunkten (...)](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) und dann auf **Einstellungen**.

   Plattform | Link
   ---------- | --------
   Android | ![Menü für Google Chrome-Einstellungen](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Menüsymbol der Google Chrome-Einstellungen](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. Tippen Sie auf **Einstellungen** und dann auf **Kennwörter**.

   Plattform | Link
   ---------- | --------
   Android | ![Option für Chrome-Kennwörter (Android)](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Option für Chrome-Kennwörter (iOS)](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Tippen Sie oben rechts (unter Android) bzw. unten rechts (unter iOS) auf das ![Google Chrome-Menü mit den Auslassungspunkten (...)](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) und dann auf **Kennwörter exportieren**.

   Plattform | Link
   ---------- | --------
   Android | ![Option zum Exportieren von Chrome-Kennwörtern (Android)](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Option zum Exportieren von Chrome-Kennwörtern (iOS)](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   Sie benötigen eine PIN, einen Fingerabdruck oder eine Gesichtserkennung. Bestätigen Sie Ihre Identität, und tippen Sie noch mal auf **Kennwörter exportieren**, um den Exportvorgang zu starten.

1. Nach dem Exportieren der Kennwörter werden Sie von Chrome aufgefordert auszuwählen, in welche App sie importiert werden sollen. Wählen Sie **Authenticator** aus, um die Kennwörter zu importieren. Sie werden informiert, wenn der Importgang abgeschlossen ist.

   Plattform | Link
   ---------- | --------
   Android | ![Option zum Importieren von Chrome-Kennwörtern (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Option zum Importieren von Chrome-Kennwörtern (iOS)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importieren aus Chrome-Desktopbrowser

Installieren Sie zunächst die [Microsoft AutoAusfüllen-Erweiterung](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) in Ihrem Chrome-Browser, und melden Sie sich an.

1. Öffnen Sie den [Google Passwortmanager](https://passwords.google.com) in einem beliebigen Browser. Melden Sie sich bei Ihrem Google-Konto an, wenn noch nicht geschehen.

1. Klicken Sie auf das Zahnradsymbol, ![Zahnradsymbol für Passwortmanager (Desktopcomputer)](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) um die Seite mit den Kennworteinstellungen zu öffnen.

1. Klicken Sie auf **Exportieren** und im sich öffnenden Fenster noch mal auf **Exportieren**, um Ihre Kennwörter zu exportieren. Geben Sie Ihr Google-Kennwort an, wenn Sie zur Bestätigung Ihrer Identität aufgefordert werden. Sie werden informiert, wenn der Importgang abgeschlossen ist.

   ![Befehl zum Exportieren der Kennwörter aus dem Chrome-Desktopbrowser](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Klicken Sie auf die AutoAusfüllen-Erweiterung für Chrome und dann auf **Einstellungen**.

   ![Einstellungen der AutoAusfüllen-Erweiterung für den Chrome-Desktopbrowser](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Klicken Sie auf **Daten importieren**. Klicken Sie im sich öffnenden Dialogfeld auf **Datei auswählen**, und navigieren Sie zur CSV-Datei, um sie zu importieren.

   ![Importieren der Chrome-Desktopbrowser-Daten (CSV-Datei)](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importieren aus Firefox

Firefox ermöglicht das Exportieren von Kennwörtern nur aus dem Desktopbrowser. Stellen Sie deshalb sicher, dass Sie Zugriff auf den Firefox-Desktopbrowser haben, bevor Sie Kennwörter aus Firefox importieren.

1. Melden Sie sich bei Firefox (aktuelle Version) auf Ihrem Desktopcomputer an, und klicken Sie auf das ![Hamburgermenü auf der Firefox-Seite](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) Menü oben rechts auf dem Bildschirm.

1. Klicken Sie auf **Zugangsdaten und Passwörter**.

   ![Option „Zugangsdaten und Passwörter“ im Firefox-Desktopbrowser](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Klicken Sie auf der Firefox Lockwise-Seite auf das ![Firefox-Menü mit den Auslassungspunkten (...)](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png), wählen Sie **Zugangsdaten exportieren** aus, und klicken Sie auf **Exportieren**. Sie werden aufgefordert, Ihre Identität mit Ihrer PIN, dem Gerätekennwort oder Ihrem Fingerabdruck zu bestätigen. Nach erfolgreicher Identifizierung exportiert Firefox Ihre Kennwörter im CSV-Format und speichert sie in den ausgewählten Speicherort.

   ![Option zum Exportieren von Kennwörtern aus dem Firefox-Desktopbrowser](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. Sie können Ihre Kennwörter über einen Desktopbrowser oder über Ihr iOS- oder Android-Smartphone in Authenticator importieren. So importieren Sie Ihre Kennwörter in die Authenticator-App über Ihr Smartphone

      1. Übermitteln Sie die exportierte CSV-Datei auf Ihr Android- oder iOS-Smartphone mit der bevorzugten sicheren Methode, und laden Sie sie herunter. Geben Sie als Nächstes die CSV-Datei für die Authenticator-App frei, um den Importvorgang zu starten.

         Plattform | Link
         ---------- | --------
         Android | ![Option zum Importieren von Chrome-Kennwörtern (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Option zum Importieren von Chrome-Kennwörtern (iOS)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nachdem die Kennwörter erfolgreich in die Authenticator-App importiert wurden, löschen Sie die CSV-Datei auf Ihrem Desktopcomputer bzw. Mobiltelefon.

## <a name="import-from-lastpass"></a>Importieren aus LastPass

LastPass unterstützt das Exportieren von Kennwörtern nur über einen Desktopbrowser. Stellen Sie deshalb sicher, dass Sie Zugriff auf einen Desktopbrowser haben, bevor Sie Ihre Kennwörter importieren.

1. Melden Sie sich bei der [LastPass-Website](https://lastpass.com) an, klicken Sie auf **Erweiterte Optionen**, und wählen Sie dann **Exportieren** aus.

   ![Option zum Exportieren von Kennwörtern in LastPass (Desktop)](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Bestätigen Sie Ihre Identität, indem Sie Ihr Master-Passwort eingeben. Die exportierten Kennwörter werden jetzt auf der Webseite angezeigt.

1. Kopieren Sie den Inhalt auf der Webseite.

1. Öffnen Sie Editor (oder Ihren bevorzugten Text-Editor), und fügen Sie den kopierten Inhalt ein.

1. Speichern Sie die Editor-Datei, indem Sie auf **Datei** &gt; **Speichern unter** klicken. Benennen Sie die Datei, fügen Sie die Endung „.csv“ (z. B. „LastPass.csv“) hinzu, und speichern Sie sie an einem sicheren Speicherort auf Ihrem Desktopcomputer.

   ![Speichern der CSV-Datei in LastPass (Desktop)](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. Sie können Ihre Kennwörter über einen Desktopbrowser oder über Ihr iOS- oder Android-Smartphone in Authenticator importieren. So importieren Sie Ihre Kennwörter in die Authenticator-App über Ihr Smartphone

      1. Übermitteln Sie die exportierte CSV-Datei an Ihr Smartphone mit der bevorzugten sicheren Methode, und laden Sie sie herunter. Geben Sie dann die CSV-Datei für die Authenticator-App frei, um den Importvorgang zu starten.

         Plattform | Link
         ---------- | --------
         Android | ![Option zum Importieren von Kennwörtern in LastPass (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Option zum Importieren von Kennwörtern in LastPass (iOS)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nachdem die Kennwörter erfolgreich in die Authenticator-App importiert wurden, löschen Sie die CSV-Datei auf Ihrem Desktopcomputer bzw. Mobiltelefon.

## <a name="import-from-bitwarden"></a>Importieren aus Bitwarden

Bitwarden unterstützt das Exportieren von Kennwörtern nur über einen Desktopbrowser. Stellen Sie deshalb sicher, dass Sie Zugriff auf einen Desktopbrowser haben, bevor Sie Ihre Kennwörter importieren.

1. Melden Sie sich bei https://vault.bitwarden.com/ an, und wählen Sie **Tools** (Extras) &gt; **Export vault** (Tresor exportieren) aus. Wählen Sie das CSV-Dateiformat aus, geben Sie Ihr Masterkennwort ein, und klicken Sie dann auf **Export vault** (Tresor exportieren), um den Exportvorgang zu starten.

   ![Option zum Exportieren des Tresors in Bitwarden](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. Sie können Ihre Kennwörter über einen Desktopbrowser oder über Ihr iOS- oder Android-Smartphone in Authenticator importieren. So importieren Sie Ihre Kennwörter in die Authenticator-App über Ihr Smartphone

      1. Übermitteln Sie die exportierte CSV-Datei an Ihr Smartphone mit der bevorzugten sicheren Methode, und laden Sie sie herunter. Geben Sie dann die CSV-Datei für die Authenticator-App frei, um den Importvorgang zu starten.

         Plattform | Link
         ---------- | --------
         Android | ![Option zum Importieren von Kennwörtern in Bitwarden (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Option zum Importieren von Kennwörtern in Bitwarden (iOS)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nachdem die Kennwörter erfolgreich in die Authenticator-App importiert wurden, löschen Sie die CSV-Datei auf Ihrem Desktopcomputer bzw. Mobiltelefon.

## <a name="import-from-roboform"></a>Importieren aus RoboForm

RoboForm ermöglicht das Exportieren von Kennwörtern nur aus der Desktop-App. Stellen Sie deshalb sicher, dass Sie Zugriff auf die RoboForm-App auf einem Desktopcomputer haben, bevor Sie Ihre Kennwörter importieren.

1. Starten Sie RoboForm auf Ihrem Desktopclient, und melden Sie sich bei Ihrem Konto an.

1. Wählen Sie im Menü **RoboForm** **Optionen** aus.

   ![Menü „Optionen“ in RoboForm (Desktop)](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Klicken Sie auf **Kontodaten** &gt; **Exportieren**.

   ![Befehl zum Exportieren der Kennwörter aus RoboForm (Desktop)](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Wählen Sie einen sicheren Speicherort zum Speichern der exportierten Datei aus. Aktivieren Sie unter **Daten** die Option **Anmeldungen**, wählen Sie „.csv-Datei“ als Format aus, und klicken Sie dann auf **Exportieren**.

   ![Dialogfeld „Daten aus RoboForm exportieren“](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Klicken Sie auf „Ja“, um den Vorgang fortzusetzen. Die CSV-Datei wird in den ausgewählten Speicherort exportiert.

   ![Dialogfeld in RoboForm zum Bestätigen des Exportvorgangs (Desktop)](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. Sie können Ihre Kennwörter über einen Desktopbrowser oder über Ihr iOS- oder Android-Smartphone in Authenticator importieren. So importieren Sie Ihre Kennwörter in die Authenticator-App über Ihr Smartphone

      1. Übermitteln Sie die exportierte CSV-Datei an Ihr Smartphone mit der bevorzugten sicheren Methode, und laden Sie sie herunter. Geben Sie dann die CSV-Datei für die Authenticator-App frei, um den Importvorgang zu starten.

         Plattform | Link
         ---------- | --------
         Android | ![Option zum Importieren von Kennwörtern in RoboForm (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Option zum Importieren von Kennwörtern in RoboForm (iOS)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nachdem die Kennwörter erfolgreich in die Authenticator-App importiert wurden, löschen Sie die CSV-Datei auf Ihrem Desktopcomputer bzw. Mobiltelefon.

## <a name="import-by-creating-a-csv"></a>Importieren durch Erstellen einer CSV-Datei

Wenn in diesem Artikel die Schritte zum Importieren von Kennwörtern aus Ihrem Kennwort-Manager nicht aufgeführt sind, können Sie zum Importieren Ihrer Kennwörter in Authenticator eine CSV-Datei erstellen. Führen Sie die folgenden Schritte auf einem Desktopcomputer aus, um die Datei einfacher formatieren zu können:

1. [Laden Sie die Importvorlage auf Ihren Desktopcomputer herunter, und öffnen Sie sie.](https://go.microsoft.com/fwlink/?linkid=2134938). Wenn Sie ein Apple iPhone-Gerät, Safari und Keychain verwenden, fahren Sie jetzt mit Schritt 4 fort.

1. Exportieren Sie Ihre Kennwörter aus Ihrem vorhandenen Kennwort-Manager in eine nicht verschlüsselte CSV-Datei.

1. Kopieren Sie die relevanten Spalten aus der exportierten CSV-Datei in die CSV-Vorlage, und speichern Sie sie.

1. Wenn Sie über keine exportierte CSV-Datei verfügen, können Sie die einzelnen Anmeldenamen aus Ihrem vorhandenen Kennwort-Manager in die CSV-Vorlage kopieren. Entfernen oder ändern Sie die Kopfzeile nicht. Überprüfen Sie die Integrität der Daten, bevor Sie mit dem nächsten Schritt fortfahren.

1. Sie können Ihre Kennwörter über einen Desktopbrowser oder über Ihr iOS- oder Android-Smartphone in Authenticator importieren. So importieren Sie Ihre Kennwörter in die Authenticator-App über Ihr Smartphone

      1. Übermitteln Sie die exportierte CSV-Datei an Ihr Smartphone mit der bevorzugten sicheren Methode, und laden Sie sie herunter. Geben Sie dann die CSV-Datei für die Authenticator-App frei, um den Importvorgang zu starten.

         Plattform | Link
         ---------- | --------
         Android | ![Option zum Importieren von Kennwörtern mit CSV-Datei (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Option zum Importieren von Kennwörtern mit CSV-Datei (iOS)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Nachdem die Kennwörter erfolgreich in die Authenticator-App importiert wurden, löschen Sie die CSV-Datei auf Ihrem Desktopcomputer bzw. Mobiltelefon.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Die häufigste Ursache für das Fehlschlagen von Importvorgängen ist die falsche Formatierung in der CSV-Datei. Führen Sie die folgenden Schritte aus, um das Problem zu beheben.

- Lesen Sie diesen Artikel, um zu erfahren, ob das Importieren von Kennwörtern aus Ihrem aktuellen Kennwort-Manager unterstützt wird. Wenn dies der Fall ist, wiederholen Sie den Importvorgang, indem Sie die für den jeweiligen Anbieter angegebenen Schritte ausführen.

- Wenn das Importieren von Kennwörtern aus dem von Ihnen verwendeten Kennwort-Manager derzeit nicht unterstützt wird, können Sie den Vorgang wiederholen, indem Sie die [CSV-Datei manuell erstellen](#import-by-creating-a-csv).

- Sie können die Integrität von CSV-Daten wie folgt überprüfen:

  - Die erste Zeile muss einen Header mit den folgenden drei Spalten aufweisen: **URL**, **Benutzername** und **Kennwort**.

  - In den Spalten **URL** und **Kennwörter** jeder Zeile muss ein Wert enthalten sein.

- Sie können die CSV-Datei neu erstellen, indem Sie die Inhalte in die [CSV-Vorlagendatei](https://go.microsoft.com/fwlink/?linkid=2134938) einfügen.

- Wenn das Problem mit keinem dieser Schritte behoben werden kann, melden Sie das Problem über den Link **Feedback** in den Authenticator-App-Einstellungen.
