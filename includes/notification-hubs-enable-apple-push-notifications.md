---
title: include file
description: include file
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c664e73b39ad48a860661cfd9141ee74df203f3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116649"
---
## <a name="generate-the-certificate-signing-request-file"></a>Erstellen der Zertifikatsignieranforderungsdatei

Der Apple Push Notification Service (APNs) verwendet Zertifikate zur Authentifizierung Ihrer Pushbenachrichtigungen. Befolgen Sie diese Anweisungen, um das erforderliche Pushzertifikat zum Senden und Empfangen von Benachrichtigungen zu erstellen. Weitere Informationen zu diesen Konzepten finden Sie in der offiziellen Dokumentation zum [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Erstellen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei), die von Apple zur Generierung eines signierten Pushzertifikats verwendet wird.

1. Führen Sie auf Ihrem Mac das Tool "Schlüsselbundverwaltung" aus. Es kann im Ordner **Dienstprogramme** oder im Ordner **Andere** auf dem Launchpad geöffnet werden.

1. Wählen Sie die Option **Schlüsselbundverwaltung** aus, erweitern Sie **Zertifikatsassistent**, und wählen Sie dann **Zertifikat einer Zertifizierungsinstanz anfordern** aus.

    ![Anfordern eines neuen Zertifikats mithilfe der Schlüsselbundverwaltung](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Wählen Sie Ihre **E-Mail-Adresse des Benutzers** aus, und geben Sie den Wert für **Allgemeiner Name** ein. Vergewissern Sie sich, dass **Saved to disk** (Auf Datenträger gespeichert) aktiviert ist, und wählen Sie dann **Weiter** aus. Lassen Sie das Feld **CA Email Address** (E-Mail der Zertifizierungsstelle) leer, da dieser Wert nicht erforderlich ist.

    ![Erforderliche Zertifikatinformationen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Geben Sie im Feld **Speichern unter** einen Namen für die CSR-Datei ein, und wählen Sie unter **Where** (Ort) den Speicherort und anschließend **Speichern** aus.

    ![Auswählen eines Dateinamens für das Zertifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Dadurch wird die CSR-Datei am ausgewählten Speicherort gespeichert. Der Standardspeicherort lautet **Desktop**. Merken Sie sich den für diese Datei festgelegten Speicherort.

Registrieren Sie als Nächstes Ihre App bei Apple, aktivieren Sie Pushbenachrichtigungen, und laden Sie die exportierte CSR-Datei hoch, um ein Pushzertifikat zu erstellen.

## <a name="register-your-app-for-push-notifications"></a>Registrieren der App für Pushbenachrichtigungen

Um Pushbenachrichtigungen an eine iOS-App zu senden, registrieren Sie Ihre Anwendung bei Apple, und registrieren Sie sie auch für Pushbenachrichtigungen.  

1. Falls Sie Ihre App noch nicht registriert haben, navigieren Sie im Apple Developer Center zum [iOS-Bereitstellungsportal](https://go.microsoft.com/fwlink/p/?LinkId=272456). Melden Sie sich anschließend mit Ihrer Apple-ID an, und wählen Sie zum Registrieren einer neuen App **Identifiers** (Bezeichner) > **App IDs** (App-IDs) > **+** aus.

    ![iOS-Bereitstellungsportal: Seite mit App-IDs](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Aktualisieren Sie die folgenden drei Werte für Ihre neue App, und wählen Sie dann **Weiter** aus:

   * **Name**: Geben Sie im Abschnitt **App ID Description** (App-ID-Beschreibung) in das Feld **Name** einen beschreibenden Namen für Ihre App ein.

   * **Bundle Identifier** (Paket-ID): Geben Sie im Abschnitt **Explicit App ID** (Explizite App-ID) einen Wert für **Bundle Identifier** (Paket-ID) im Format `<Organization Identifier>.<Product Name>` (entsprechend den Angaben im [Leitfaden zur App-Verteilung](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)) ein. Die unter *Organization Identifier* (Organisationskennung) und *Product Name* (Produktname) eingegebenen Werte müssen der Organisationskennung und dem Produktnamen entsprechen, die Sie beim Erstellen des Xcode-Projekts verwenden. Im folgenden Screenshot wird der Wert *NotificationHubs* als Organisationskennung und *GetStarted* als Produktname verwendet. Stellen Sie sicher, dass der Wert für **Bundle Identifier** (Paket-ID) mit dem Wert in Ihrem Xcode-Projekt übereinstimmt, sodass Xcode das richtige Veröffentlichungsprofil verwendet.

   * **Push Notifications** (Pushbenachrichtigungen): Aktivieren Sie die Option **Push Notifications** (Pushbenachrichtigungen) im Bereich **App Services**.

     ![Formular zum Registrieren einer neuen App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Hierdurch wird Ihre App-ID generiert, und Sie werden zur Bestätigung der Daten aufgefordert. Wählen Sie **Register** (Registrieren) aus, um die neue App-ID zu bestätigen.

     Nachdem Sie **Register** (Registrieren) ausgewählt haben, wird der Bildschirm **Registration complete** (Registrierung abgeschlossen) geöffnet, wie in der folgenden Abbildung gezeigt. Wählen Sie **Fertig**aus.

     ![Abgeschlossene App-ID-Registrierung mit Berechtigungen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. Suchen Sie im Developer Center unter **App IDs** (App-IDs) nach der erstellten App-ID, und wählen Sie die entsprechende Zeile aus.

    ![App-ID-Liste](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Wählen Sie die App-ID aus, um die App-Details anzuzeigen, und wählen Sie dann unten die Schaltfläche **Edit** (Bearbeiten) aus.

    ![Bearbeiten der Seite für App-IDs](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Scrollen Sie zum unteren Bildschirmrand, und wählen Sie im Abschnitt **Development Push SSL Certificate** (SSL-Pushzertifikat für die Entwicklung) die Schaltfläche **Create Certificate** (Zertifikat erstellen) aus.

    ![Schaltfläche zum Erstellen eines Zertifikats für die App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Der Assistent **Add iOS Certificate** (iOS-Zertifikat hinzufügen) wird angezeigt.

    > [!NOTE]
    > In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Achten Sie darauf, dass Sie denselben Zertifikattyp beim Senden von Benachrichtigungen verwenden.

1. Klicken Sie auf **Choose File** (Datei auswählen), wechseln Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei, und wählen Sie dann **Generate** (Generieren).

    ![CSR-Uploadseite für das generierte Zertifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. Wählen Sie nach der Erstellung des Zertifikats im Portal die Schaltfläche **Download** (Herunterladen) und anschließend **Done** (Fertig) aus.

    ![Downloadseite für das generierte Zertifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Das Zertifikat wird heruntergeladen und auf Ihrem Computer im Ordner **Downloads** gespeichert.

    ![Suchen der Zertifikatdatei im Ordner „Downloads“](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Standardmäßig heißt das heruntergeladene Entwicklungszertifikat **aps_development.cer**.

1. Wählen Sie das heruntergeladene Pushzertifikat **aps_development.cer** aus.

    Das neue Zertifikat wird im Schlüsselbund installiert, wie in der folgenden Abbildung gezeigt:

    ![Zertifikatliste der Schlüsselbundverwaltung mit neuem Zertifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Der Name in Ihrem Zertifikat kann anders lauten, ihm wird jedoch **Apple Development iOS Push Services** vorangestellt.

1. Klicken Sie in der Kategorie **Zertifikate** in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neu erstellte Pushzertifikat. Wählen Sie die Option **Exportieren**, benennen Sie die Datei, wählen Sie das Format **.p12** aus, und wählen Sie dann die Option **Speichern**.

    ![Exportieren des Zertifikats im p12-Format](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Notieren Sie sich den Dateinamen und den Speicherort des exportierten P12-Zertifikats. Sie werden zum Aktivieren der Authentifizierung mit APNs verwendet.

    > [!NOTE]
    > In diesem Tutorial wird eine Datei namens **QuickStart.p12** erstellt. Name und Ort Ihrer Datei können verschieden sein.

## <a name="create-a-provisioning-profile-for-the-app"></a>Erstellen eines Bereitstellungsprofils für die App

1. Wählen Sie im [iOS-Bereitstellungsportal](https://go.microsoft.com/fwlink/p/?LinkId=272456) die Optionen **Provisioning Profiles** (Bereitstellungsprofile) > **All** (Alle) > **+** aus, um ein neues Profil zu erstellen. Der Assistent **Add iOS Provisioning Profile** (iOS-Bereitstellungsprofil hinzufügen) wird angezeigt.

    ![Bereitstellungsprofilliste](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Wählen Sie unter **Development** (Entwicklung) die Option **iOS App Development** (iOS-App-Entwicklung) als Bereitstellungsprofiltyp und dann **Weiter** aus.

1. Wählen Sie anschließend die von Ihnen erstellte App-ID in der Dropdownliste **App ID** (App-ID) und dann **Weiter** aus.

    ![Auswählen der App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Wählen Sie im Fenster **Select certificates** (Zertifikate auswählen) das normalerweise für die Codesignatur verwendete Entwicklungszertifikat und dann **Weiter** aus. Dabei handelt es sich nicht um das erstellte Pushzertifikat.

    ![Auswählen des Zertifikats](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Wählen Sie nun die Geräte aus, die zum Testen verwendet werden sollen, und wählen Sie dann **Weiter** aus.

    ![Auswählen der Geräte](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Geben Sie schließlich im Feld **Profile Name** (Profilname) einen Namen für das Profil ein, und wählen Sie **Generate** ( Generieren) aus.

    ![Auswählen eines Bereitstellungsprofilnamens](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Nachdem das neue Bereitstellungsprofil erstellt wurde, wählen Sie es aus, um es herunterzuladen und auf Ihrem Xcode-Entwicklungscomputer zu installieren. Wählen Sie dann **Fertig** aus.

    ![Herunterladen des Bereitstellungsprofils](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Erstellen eines Notification Hubs

In diesem Abschnitt erstellen Sie einen Notification Hub und konfigurieren die Authentifizierung mit APNs unter Verwendung des zuvor erstellten Pushzertifikats vom Typ „.p12“. Wenn Sie einen bereits erstellten Notification Hub verwenden möchten, können Sie mit Schritt 5 fortfahren.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurieren Ihres Notification Hubs mit APNs-Informationen

1. Wählen Sie unter **Notification Services** die Option **Apple (APNS)** aus.

1. Wählen Sie **Certificate**aus.

1. Wählen Sie das Dateisymbol aus.

1. Wählen Sie die zuvor exportierte Datei vom Typ „.p12“ aus.

1. Geben Sie das richtige Kennwort an.

1. Wählen Sie den Modus **Sandbox** aus. Verwenden Sie den Modus **Produktion** nur dann, wenn Sie Pushbenachrichtigungen an Benutzer senden möchten, die Ihre App im Store erworben haben.

    ![Konfigurieren der APNs-Zertifizierung im Azure-Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Sie haben Ihren Notification Hub mit APNs-Informationen konfiguriert. Sie verfügen außerdem über die Verbindungszeichenfolgen, die Sie zum Registrieren Ihrer App und zum Senden von Pushbenachrichtigungen benötigen.
