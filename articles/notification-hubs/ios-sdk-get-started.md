---
title: Senden von Pushbenachrichtigungen an iOS mit Azure Notification Hubs und dem iOS SDK
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs und dem Apple Push Notification Service Pushbenachrichtigungen an iOS-Geräte senden.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c18814230eda6624e8ab1040797c66108f630e30
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581736"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Tutorial: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs

In diesem Tutorial wird beschrieben, wie Sie Azure Notification Hubs einrichten und Anmeldeinformationen konfigurieren, um über den [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) Pushbenachrichtigungen an ein iOS-Gerät zu senden. 

Das Ausführen dieses Tutorials ist eine Voraussetzung für die nachfolgenden iOS-Tutorials zu Objective C and Swift. Es enthält die folgenden Schritte:

- Erstellen der Datei für Zertifikatsignierungsanforderungen
- Anfordern Ihrer App für Pushbenachrichtigungen
- Erstellen eines Bereitstellungsprofils für die App
- Erstellen eines Notification Hubs
- Konfigurieren des Notification Hubs mit APNS-Informationen

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Einen Monat kostenlos testen](https://azure.microsoft.com/free/).

Außerdem benötigen Sie Folgendes:

- Ein aktives [Apple Developer](https://developer.apple.com/)-Konto.
- Einen Mac mit [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) zusammen mit einem gültigen in Ihrer Keychain installierten Entwicklerzertifikat.
- Ein iPhone oder iPad, auf dem iOS Version 10 oder höher ausgeführt wird.
- Ihr physisches Gerät, das im [Apple-Portal](https://developer.apple.com/) registriert und Ihrem Zertifikat zugeordnet ist.

Wenn Sie mit dem Dienst noch nicht vertraut sind, lesen Sie die [Übersicht über Azure Notification Hubs](notification-hubs-push-notification-overview.md).

> [!NOTE]
> Der Notification Hub wird für die ausschließliche Verwendung des Sandbox-Authentifizierungsmodus konfiguriert. Sie sollten diesen Authentifizierungsmodus nicht für Produktionsworkloads verwenden.

## <a name="generate-the-certificate-signing-request-file"></a>Erstellen der Datei für Zertifikatsignierungsanforderungen

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihrer Pushbenachrichtigungen. Befolgen Sie diese Anweisungen, um das erforderliche Pushzertifikat zum Senden und Empfangen von Benachrichtigungen zu erstellen. Weitere Informationen zu diesen Konzepten finden Sie in der offiziellen Dokumentation zum [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Erstellen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei), die von Apple zur Generierung eines signierten Pushzertifikats verwendet wird:

1. Führen Sie auf Ihrem Mac das Tool "Schlüsselbundverwaltung" aus. Es kann im Ordner **Dienstprogramme** oder im Ordner **Andere** auf dem Launchpad geöffnet werden.

2. Wählen Sie die Option **Schlüsselbundverwaltung** aus, erweitern Sie **Zertifikatsassistent**, und wählen Sie dann **Zertifikat einer Zertifizierungsinstanz anfordern** aus.

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Screenshot, in der die Menüoption „Zertifikat von einer Zertifizierungsstelle anfordern“ hervorgehoben ist.":::

   > [!NOTE]
   > Die Schlüsselbundverwaltung wählt standardmäßig das erste Element in der Liste aus. Dies kann ein Problem darstellen, wenn Sie sich in der Kategorie **Zertifikate** befinden und **Zertifizierungsstelle der Apple Worldwide Developer Relations** nicht das erste Element in der Liste ist. Stellen Sie sicher, dass Sie über ein Element verfügen, das kein Schlüssel ist, bzw. dass der Schlüssel **Zertifizierungsstelle der Apple Worldwide Developer Relations** ausgewählt ist, bevor Sie die Zertifikatsignieranforderung (Certificate Signing Request, CSR) generieren.

3. Wählen Sie Ihre **E-Mail-Adresse des Benutzers** aus, und geben Sie den Wert für **Allgemeiner Name** ein. Vergewissern Sie sich, dass **Saved to disk** (Auf Datenträger gespeichert) aktiviert ist, und wählen Sie dann **Weiter** aus. Lassen Sie das Feld **CA Email Address** (E-Mail der Zertifizierungsstelle) leer, da dieser Wert nicht erforderlich ist.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Erforderliche Zertifikatinformationen":::

4. Geben Sie im Feld **Speichern unter** einen Namen für die CSR-Datei ein, und wählen Sie unter **Where** (Ort) den Speicherort und anschließend **Speichern** aus.

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Auswählen eines Dateinamens":::

   Dadurch wird die CSR-Datei am ausgewählten Speicherort gespeichert. Der Standardspeicherort lautet **Desktop**. Merken Sie sich den für diese Datei festgelegten Speicherort.

Registrieren Sie als Nächstes Ihre App bei Apple, aktivieren Sie Pushbenachrichtigungen, und laden Sie die exportierte CSR-Datei hoch, um ein Pushzertifikat zu erstellen.

## <a name="register-your-app-for-push-notifications"></a>Registrieren der App für Pushbenachrichtigungen

Zum Senden von Pushbenachrichtigungen an eine iOS-App registrieren Sie Ihre Anwendung bei Apple, und registrieren Sie sie auch für Pushbenachrichtigungen.

1. Falls Sie Ihre App noch nicht registriert haben, navigieren Sie im Apple Developer Center zum [iOS-Bereitstellungsportal](https://go.microsoft.com/fwlink/p/?LinkId=272456). Melden Sie sich mit Ihrer Apple-ID beim Portal an, und wählen Sie **Bezeichner** aus. Wählen Sie dann **+** aus, um eine neue App zu registrieren.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Seite mit App-IDs":::

2. Aktivieren Sie auf dem Bildschirm zum **Registrieren eines neuen Bezeichners** das Optionsfeld **App-IDs**. Klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Seite zum Registrieren einer neuen ID":::

3. Aktualisieren Sie die folgenden drei Werte für Ihre neue App, und wählen Sie dann **Weiter** aus:

   - **Beschreibung**: Geben Sie einen aussagekräftigen Namen für Ihre App ein.
   - **Paket-ID**: Geben Sie eine Paket-ID im Format **Organisationsbezeichner.Produktname** ein, wie im [Leitfaden zur App-Verteilung](https://help.apple.com/xcode/mac/current/#/dev91fe7130a) erwähnt. Die unter **Organization Identifier** (Organisationskennung) und **Product Name** (Produktname) eingegebenen Werte müssen der Organisationskennung und dem Produktnamen entsprechen, die Sie beim Erstellen des Xcode-Projekts verwenden. Im folgenden Screenshot wird der Wert **NotificationHubs** als Organisationskennung und **GetStarted** als Produktname verwendet. Stellen Sie sicher, dass der Wert für den **Paketbezeichner** mit dem Wert in Ihrem Xcode-Projekt übereinstimmt, sodass Xcode das richtige Veröffentlichungsprofil verwendet.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Registrieren der App-ID":::

   - **Push Notifications** (Pushbenachrichtigungen): Aktivieren Sie die Option **Pushbenachrichtigungen** im Bereich **Funktionen**.

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Registrieren einer neuen App-ID":::

      Hierdurch wird Ihre App-ID generiert, und Sie werden zur Bestätigung der Daten aufgefordert. Wählen Sie **Weiter** und dann **Registrieren** aus, um die neue App-ID zu bestätigen.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Bestätigen der neuen App-ID":::

      Nachdem Sie **Registrieren** ausgewählt haben, wird die neue App-ID als Position auf der Seite **Zertifikate, Bezeichner und Profile** angezeigt.

4. Suchen Sie auf der Seite **Zertifikate, Bezeichner und Profile** unter **Bezeichner** die soeben erstellte App-ID-Position, und wählen Sie die zugehörige Zeile aus, um den Bildschirm zum **Bearbeiten Ihrer App-ID-Konfiguration** anzuzeigen.

## <a name="create-a-certificate-for-notification-hubs"></a>Erstellen eines Zertifikats für Notification Hubs

> [!NOTE]
> Mit der Veröffentlichung von iOS 13 können Sie automatische Benachrichtigungen nur unter Verwendung der tokenbasierten Authentifizierung erhalten. Wenn Sie zertifikatbasierte Authentifizierung für Ihre APNs-Anmeldeinformationen verwenden, müssen Sie auf tokenbasierte Authentifizierung umsteigen.

Damit der Notification Hub mit **APNs** funktioniert, ist ein Zertifikat erforderlich. Dafür können Sie zwei Methoden verwenden:

- Erstellen Sie eine **P12**-Datei, die direkt in Notification Hubs hochgeladen werden kann.

- Erstellen Sie eine **P8**-Datei, die für die [tokenbasierte Authentifizierung](notification-hubs-push-notification-http2-token-authentication.md) verwendet werden kann (dies ist die neuere Methode).

Die zweite Option bietet eine Reihe von Vorteilen gegenüber der Verwendung von Zertifikaten. Diese sind unter[Tokenbasierte Authentifizierung (HTTP/2) für APNS](notification-hubs-push-notification-http2-token-authentication.md) dokumentiert. Hier finden Sie die Schritte für beide Methoden.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Option 1: Erstellen eines P12-Pushzertifikats, das direkt in Notification Hubs hochgeladen werden kann

1. Scrollen Sie nach unten zur aktivierten Option **Pushbenachrichtigungen**, und wählen Sie dann **Konfigurieren** aus, um das Zertifikat zu erstellen.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="App-ID":::

2. Das Fenster für die **SSL-Zertifikate von Apple Push Notification Service** wird angezeigt. Wählen Sie im Abschnitt **SSL-Zertifikat für die Entwicklung** die Schaltfläche **Zertifikat erstellen** aus.

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Erstellen eines Zertifikats":::

   Der Bildschirm **Ein neues Zertifikat erstellen** wird angezeigt.

   > [!NOTE]
   > In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Achten Sie darauf, dass Sie denselben Zertifikattyp beim Senden von Benachrichtigungen verwenden.

3. Klicken Sie auf **Datei auswählen**, wechseln Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei, und doppelklicken Sie dann auf den Namen des Zertifikats, um es zu laden. Klicken Sie anschließend auf **Weiter**.

4. Wählen Sie nach der Erstellung des Zertifikats im Portal die Schaltfläche **Herunterladen** aus. Speichern Sie das Zertifikat, und merken Sie sich den Speicherort.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Herunterladen des Zertifikats":::

   Das Zertifikat wird heruntergeladen und im Ordner **Downloads** gespeichert.

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Suchen der Zertifikatdatei":::

   Standardmäßig heißt das heruntergeladene Entwicklungszertifikat **aps_development.cer**.

5. Doppelklicken Sie auf das heruntergeladene Pushzertifikat **aps\_development.cer**. Das neue Zertifikat wird im Schlüsselbund installiert, wie in der folgenden Abbildung gezeigt:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Schlüsselbundverwaltung":::

   Der Name in Ihrem Zertifikat kann anders lauten, ihm wird jedoch **Apple Development iOS Push Services** vorangestellt.

6. Klicken Sie in der Kategorie **Zertifikate** in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neu erstellte Pushzertifikat. Wählen Sie die Option **Exportieren**, benennen Sie die Datei, wählen Sie das Format **.p12** aus, und wählen Sie dann die Option **Speichern**.

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Exportieren eines Zertifikats":::

   Sie können das Zertifikat mit einem Kennwort schützen, aber dies ist optional. Wenn Sie die Kennworterstellung umgehen möchten, klicken Sie auf **OK**. Notieren Sie sich den Dateinamen und den Speicherort des exportierten P12-Zertifikats, Sie werden zum Aktivieren der Authentifizierung mit APNS verwendet.

   > [!NOTE]
   > Dateiname und Speicherort Ihrer P12-Datei können sich von den in diesem Tutorial abgebildeten Informationen unterscheiden.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Option 2: Erstellen eines P8-Zertifikats, das für die tokenbasierte Authentifizierung verwendet werden kann

1. Notieren Sie sich folgende Angaben:

   - **App-ID-Präfix** (dies ist eine **Team-ID**)
   - **Paket-ID**

2. Klicken Sie in **Zertifikate, Bezeichner und Profile** auf **Schlüssel**. Wenn Sie bereits einen Schlüssel für **APNS** konfiguriert haben, können Sie das P8-Zertifikat wiederverwenden, das Sie direkt nach der Erstellung heruntergeladen haben. In diesem Fall können Sie die Schritte 3 bis 5 überspringen.

3. Klicken Sie auf die Schaltfläche **+** (oder die Schaltfläche **Schlüssel erstellen**), um einen neuen Schlüssel zu erstellen.

4. Geben Sie einen geeigneten Wert für den **Schlüsselnamen** an, aktivieren Sie die Option **Apple Push Notification Service (APNS)** , und klicken Sie dann auf **Weiter** und auf dem nächsten Bildschirm auf **Registrieren**.

5. Klicken Sie auf **Herunterladen**, und verschieben Sie die **P8**-Datei (mit dem Präfix `AuthKey_` ) in ein sicheres lokales Verzeichnis. Klicken Sie dann auf **Fertig**.

   > [!IMPORTANT]
   > Bewahren Sie die P8-Datei an einem sicheren Ort auf (und speichern Sie eine Sicherungskopie). Nach dem Herunterladen kann der Schlüssel nicht erneut heruntergeladen werden, da die Kopie auf dem Server gelöscht wird.

6. Klicken Sie unter **Schlüssel** auf den gerade erstellten Schlüssel (oder auf einen vorhandenen Schlüssel, falls Sie stattdessen einen solchen verwenden möchten).

7. Notieren Sie sich den Wert der **Schlüssel-ID**.

8. Öffnen Sie das P8-Zertifikat in einer geeigneten Anwendung wie z. B. [Visual Studio Code](https://code.visualstudio.com/), und notieren Sie sich den Schlüsselwert. Dies ist der Wert zwischen **-----BEGIN PRIVATE KEY-----** und **-----END PRIVATE KEY-----** .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Dies ist der Tokenwert, der später verwendet wird, um Notification Hubs zu konfigurieren.

Nach Abschluss dieser Schritte verfügen Sie über folgende Informationen, die Sie später unter [Konfigurieren Ihres Benachrichtigungshubs mit APNS-Informationen](#configure-the-notification-hub-with-apns-information) verwenden werden:

- **Team-ID** (siehe Schritt 1)
- **Bundle-ID** (siehe Schritt 1)
- **Schlüssel-ID** (siehe Schritt 7)
- **Tokenwert** (der P8-Schlüsselwert, siehe Schritt 8)

## <a name="create-a-provisioning-profile"></a>Erstellen eines Bereitstellungsprofils

1. Wechseln Sie zurück zum [iOS-Bereitstellungsportal](https://go.microsoft.com/fwlink/p/?LinkId=272456), wählen Sie **Zertifikate, Bezeichner und Profile** aus, wählen Sie im Menü auf der linken Seite **Profile** und dann **+** aus, um ein neues Profil zu erstellen. Der Bildschirm zum **Registrieren eines neuen Bereitstellungsprofils** wird angezeigt.

2. Wählen Sie unter **Entwicklung**, die Option **iOS-App-Entwicklung** als Bereitstellungsprofiltyp aus, und wählen Sie anschließend **Weiter**.

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Bereitstellungsprofilliste":::

3. Wählen Sie anschließend die von Ihnen erstellte App-ID in der Dropdownliste **App ID** (App-ID) und dann **Weiter** aus.

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Auswählen der App-ID":::

4. Wählen Sie im Fenster **Select certificates** (Zertifikate auswählen) das Entwicklungszertifikat aus, das Sie für die Codesignierung verwenden, und wählen Sie dann **Continue** (Weiter). Dabei handelt es sich nicht um das erstellte Pushzertifikat. Wenn keines vorhanden ist, müssen Sie es erstellen. Wenn ein Zertifikat vorhanden ist, fahren Sie mit dem nächsten Schritt fort. Erstellen eines Entwicklungszertifikat, wenn noch keines vorhanden ist:

   1. Wenn **No Certificates are available** (Keine Zertifikate verfügbar) angezeigt wird, wählen Sie **Create Certificate** (Zertifikat erstellen) aus.
   2. Wählen Sie im Abschnitt **Software** die Option **Apple Development** (Apple-Entwicklung) aus. Klicken Sie anschließend auf **Weiter**.
   3. Wählen Sie auf dem Bildschirm **Neues Zertifikat erstellen** die Option **Datei auswählen**.
   4. Navigieren Sie zum Zertifikat **Zertifikatsignieranforderung**, das Sie zuvor erstellt haben, wählen Sie es aus, und wählen Sie dann **Öffnen** aus.
   5. Wählen Sie **Weiter**.
   6. Laden Sie das Entwicklungszertifikat herunter, und merken Sie sich den Speicherort.

5. Wechseln Sie zurück zur Seite **Zertifikate, Bezeichner und Profile**, wählen Sie im Menü auf der linken Seite **Profile** und dann **+** aus, um ein neues Profil zu erstellen. Der Bildschirm zum **Registrieren eines neuen Bereitstellungsprofils** wird angezeigt.

6. Wählen Sie im Fenster **Select certificates** (Zertifikate auswählen) das soeben erstellte Entwicklungszertifikat aus. Klicken Sie anschließend auf **Weiter**.

7. Wählen Sie nun die Geräte aus, die zum Testen verwendet werden sollen, und wählen Sie dann **Weiter** aus.

8. Wählen Sie schließlich im Feld **Provisioning Profile Name** (Name des Bereitstellungsprofils) einen Namen für das Profil aus, und wählen Sie **Generate** (Generieren) aus.

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Auswählen eines Bereitstellungsprofilnamens":::

9. Nachdem das neue Bereitstellungsprofil erstellt wurde, wählen Sie **Herunterladen** aus. Merken Sie sich den Speicherort des Profils.

10. Navigieren Sie zum Speicherort des Bereitstellungsprofils, und doppelklicken Sie darauf, um es auf dem Xcode-Entwicklungscomputer zu installieren.

## <a name="create-a-notification-hub"></a>Erstellen eines Notification Hubs

In diesem Abschnitt erstellen Sie einen Notification Hub und konfigurieren die Authentifizierung mit APNS unter Verwendung des zuvor erstellten P12-Pushzertifikats oder der tokenbasierten Authentifizierung. Wenn Sie einen bereits erstellten Notification Hub verwenden möchten, können Sie mit Schritt 5 fortfahren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie im Menü auf der linken Seite die Option **Alle Dienste** und dann im Abschnitt **Mobil** die Option **Notification Hubs** aus. Wählen Sie das Sternsymbol neben dem Dienstnamen aus, um den Dienst im linken Menü zum Abschnitt **FAVORITEN** hinzuzufügen. Nachdem Sie **Notification Hubs** unter **FAVORITEN** hinzugefügt haben, wählen Sie diesen Eintrag aus.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure-Portal":::

3. Wählen Sie auf der Seite **Notification Hubs** in der Symbolleiste die Option **Hinzufügen**.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Symbolleistenschaltfläche „Hinzufügen“":::

4. Führen Sie auf der Seite **Notification Hub** die folgenden Schritte aus:

   1. Geben Sie ins Feld **Notification Hub** einen Namen ein.
   2. Geben Sie ins Feld **Neuen Namespace erstellen** einen Namen ein. Ein Namespace enthält mindestens einen Notification Hub.
   3. Wählen Sie im Dropdown-Listenfeld **Standort** einen Wert aus. Dieser Wert gibt den Standort an, an dem der Notification Hub erstellt werden soll.
   4. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe.
   5. Klicken Sie auf **Erstellen**.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Festlegen von Eigenschaften":::

5. Wählen Sie **Benachrichtigungen** (Glockensymbol) und dann **Zu Ressource wechseln** aus. Sie können auch die Liste auf der Seite **Notification Hubs** aktualisieren und Ihren Hub auswählen.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Portalbenachrichtigungen":::

6. Wählen Sie in der Liste die Option **Zugriffsrichtlinien** aus. Notieren Sie sich die beiden Verbindungszeichenfolgen, die für Sie verfügbar sind. Sie werden später für die Behandlung von Pushbenachrichtigungen benötigt.

   > [!IMPORTANT]
   > Verwenden Sie in Ihrer Anwendung nicht die Richtlinie **DefaultFullSharedAccessSignature**. Diese ist nur für die Verwendung in Ihrem Back-End vorgesehen.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Verbindungszeichenfolgen":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Konfigurieren Ihres Notification Hubs mit APNs-Informationen

Wählen Sie unter **Benachrichtigungsdienste** die Option **Apple (APNs)** aus, und führen Sie dann je nach der Methode, die Sie im Abschnitt [Erstellen eines Zertifikats für Notification Hubs](#create-a-certificate-for-notification-hubs) ausgewählt haben, die entsprechenden Schritte aus.

> [!NOTE]
> Verwenden Sie **Produktion** nur dann als **Anwendungsmodus**, wenn Sie Pushbenachrichtigungen an Benutzer senden möchten, die Ihre App im Store erworben haben.

### <a name="option-1-use-a-p12-push-certificate"></a>Option 1: Verwenden eines P12-Pushzertifikats

1. Wählen Sie **Certificate** aus.

2. Wählen Sie das Dateisymbol aus.

3. Wählen Sie die zuvor exportierte P12-Datei aus, und wählen Sie dann **Öffnen**.

4. Geben Sie bei Bedarf das richtige Kennwort an.

5. Wählen Sie den Modus **Sandbox** aus.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Konfigurieren":::

6. Wählen Sie **Speichern** aus.

### <a name="option-2-use-token-based-authentication"></a>Option 2: Verwenden der tokenbasierten Authentifizierung

1. Wählen Sie **Token** aus.

2. Geben Sie die folgenden Werte ein, die Sie zuvor abgerufen haben:

   - **Schlüssel-ID**
   - **Bundle-ID**
   - **Team-ID**
   - **Token**

3. Wählen Sie **Sandbox** aus.

4. Wählen Sie **Speichern** aus.

Sie haben Ihren Notification Hub mit APNS-Informationen konfiguriert. Sie verfügen außerdem über die Verbindungszeichenfolgen, die Sie zum Registrieren Ihrer App und zum Senden von Pushbenachrichtigungen benötigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Notification Hub in Azure erstellt und so konfiguriert, dass Benachrichtigungen über Apple Push Notification Service (APNS) an Ihre Anwendung gesendet werden können. Als Nächstes erstellen Sie eine iOS-Beispielanwendung und integrieren das Azure Notification Hubs SDK, damit es Pushbenachrichtigungen empfangen kann, die über das Azure-Portal gesendet werden. Fahren Sie mit dem folgenden Tutorial für die von Ihnen bevorzugte Sprache fort:

- [Tutorial: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs](ios-sdk-current.md)
