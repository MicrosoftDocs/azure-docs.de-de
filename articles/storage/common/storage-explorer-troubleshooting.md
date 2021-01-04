---
title: Azure Storage-Explorer – Leitfaden zur Problembehandlung | Microsoft-Dokumentation
description: Übersicht über Debugverfahren für Azure Storage-Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 8bffe0c3871eae12f3b875a96301136d11dfc516
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783792"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage-Explorer – Leitfaden zur Problembehandlung

Microsoft Azure Storage-Explorer ist eine eigenständige App, mit der Sie unter Windows, macOS und Linux komfortabel mit Azure Storage-Daten arbeiten können. Die App kann mit Storage-Konten verbunden werden, die in Azure, nationalen Clouds und Azure Stack gehostet werden.

In diesem Leitfaden sind Lösungen für häufig aufgetretene Probleme im Storage-Explorer zusammengefasst.

## <a name="azure-rbac-permissions-issues"></a>Azure RBAC-Berechtigungsprobleme

Die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, [Azure RBAC](../../role-based-access-control/overview.md)) ermöglicht eine hochgradig anpassbare Zugriffsverwaltung von Azure-Ressourcen, indem mehrere Berechtigungen in _Rollen_ kombiniert werden. Hier folgen einige Strategien, um Azure RBAC im Storage-Explorer optimal zu nutzen.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Wie kann ich im Storage-Explorer auf meine Ressourcen zugreifen?

Falls Sie Probleme beim Zugreifen auf Speicherressourcen per Azure RBAC haben, wurden Ihnen möglicherweise nicht die entsprechenden Rollen zugewiesen. In den folgenden Abschnitten werden die Berechtigungen beschrieben, die von Storage-Explorer derzeit zum Zugreifen auf Ihre Speicherressourcen benötigt werden. Wenden Sie sich an Ihren Azure-Kontoadministrator, falls Sie nicht sicher sind, ob Sie über die richtigen Rollen oder Berechtigungen verfügen.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>„Lesen: Auflisten/Abrufen von Speicherkonten“ – Berechtigungsproblem

Sie benötigen die Berechtigung zum Auflisten von Speicherkonten. Ihnen muss die Rolle _Leser_ zugewiesen sein, um diese Berechtigung zu erhalten.

#### <a name="list-storage-account-keys"></a>Auflisten von Speicherkontoschlüsseln

Für Storage-Explorer können auch Kontoschlüssel zum Authentifizieren von Anforderungen verwendet werden. Sie können Zugriff auf Kontoschlüssel über leistungsstärkere Rollen erhalten, z. B. über die Rolle _Mitwirkender_ .

> [!NOTE]
> Mit Zugriffsschlüsseln werden für deren Besitzer uneingeschränkte Berechtigungen gewährt. Daher wird nicht empfohlen, diese Schlüssel an Kontobenutzer weiterzugeben. Wenn Sie Zugriffsschlüssel widerrufen müssen, können Sie sie über das [Azure-Portal](https://portal.azure.com/) neu generieren.

#### <a name="data-roles"></a>Datenrollen

Ihnen muss mindestens eine Rolle zugewiesen sein, über die Zugriff zum Lesen der Daten von Ressourcen gewährt wird. Wenn Sie beispielsweise Blobs auflisten oder herunterladen müssen, benötigen Sie mindestens die Rolle _Storage-Blobdatenleser_ .

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Warum benötige ich eine Rolle auf der Verwaltungsebene, damit meine Ressourcen in Storage-Explorer angezeigt werden?

Azure Storage verfügt über zwei Zugriffsebenen: _Verwaltung_ und _Daten_ . Auf Abonnements und Speicherkonten wird über die Verwaltungsebene zugegriffen. Auf Container, Blobs und andere Datenressourcen wird über die Datenebene zugegriffen. Wenn Sie beispielsweise eine Liste mit Ihren Speicherkonten aus Azure abrufen möchten, senden Sie eine Anforderung an den Verwaltungsendpunkt. Wenn Sie eine Liste mit den Blobcontainern eines Kontos anzeigen möchten, können Sie eine Anforderung an den entsprechenden Dienstendpunkt senden.

Azure-Rollen können Berechtigungen für den Zugriff auf die Verwaltungs- oder Datenebene gewähren. Mit der Rolle „Leser“ wird beispielsweise Lesezugriff auf die Ressourcen der Verwaltungsebene gewährt.

Mit der Rolle „Leser“ werden genau genommen keine Berechtigungen für die Datenebene gewährt, und sie ist für den Zugriff darauf nicht erforderlich.

Storage-Explorer erleichtert Ihnen den Zugriff auf Ihre Ressourcen, indem die benötigten Informationen zum Herstellen einer Verbindung mit Ihren Azure-Ressourcen zusammengestellt werden. Zum Anzeigen Ihrer Blobcontainer sendet Storage-Explorer z. B. eine Anforderung zum Auflisten von Containern an den Blob-Dienstendpunkt. Um diesen Endpunkt zu erhalten, durchsucht Storage-Explorer die Liste mit den Abonnements und Speicherkonten, auf die Sie Zugriff haben. Zur Ermittlung Ihrer Abonnements und Speicherkonten benötigt Storage-Explorer auch Zugriff auf die Verwaltungsebene.

Wenn Sie nicht über eine Rolle verfügen, mit der Berechtigungen für die Verwaltungsebene gewährt werden, kann Storage-Explorer nicht die Informationen abrufen, die zum Herstellen der Verbindung mit der Datenebene benötigt werden.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Wie kann ich vorgehen, wenn ich die benötigten Berechtigungen für die Verwaltungsebene nicht von meinem Administrator erhalten kann?

Wenn Sie auf Blobcontainer oder Warteschlangen zugreifen möchten, können Sie diese Ressourcen mithilfe ihrer Azure-Anmeldeinformationen anfügen.

1. Öffnen Sie das Dialogfeld „Verbinden“.
2. Wählen Sie „Ressource über Azure Active Directory (Azure AD) hinzufügen“ aus. Klicken Sie auf Weiter.
3. Wählen Sie das Benutzerkonto und den Mandanten aus, die der Ressource zugeordnet sind, die angefügt werden soll. Klicken Sie auf Weiter.
4. Wählen Sie den Ressourcentyp aus, geben Sie die URL für die Ressource ein, und geben Sie einen eindeutigen Anzeigenamen für die Verbindung ein. Klicken Sie auf Weiter. Klicken Sie auf „Verbinden“.

Für andere Ressourcentypen verfügen wir derzeit nicht über eine Azure RBAC-bezogene Lösung. Zur Problemumgehung können Sie einen SAS-URI anfordern, um ihn [an Ihre Ressource anzufügen](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-azure-built-in-roles"></a>Empfohlene in Azure integrierte Rollen

Es gibt mehrere integrierte Azure-Rollen, die die Berechtigungen bereitstellen können, die für die Verwendung von Storage-Explorer erforderlich sind. Einige dieser Rollen sind:
- [Besitzer:](../../role-based-access-control/built-in-roles.md#owner) Sie können alles verwalten, einschließlich des Zugriffs auf Ressourcen. **Hinweis** : Über diese Rolle erhalten Sie Schlüsselzugriff.
- [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor): Sie können alles verwalten, ausgenommen den Zugriff auf Ressourcen. **Hinweis** : Über diese Rolle erhalten Sie Schlüsselzugriff.
- [Leser:](../../role-based-access-control/built-in-roles.md#reader) Lesen und Auflisten von Ressourcen.
- [Speicherkontomitwirkender:](../../role-based-access-control/built-in-roles.md#storage-account-contributor) Vollständige Verwaltung von Speicherkonten. **Hinweis** : Über diese Rolle erhalten Sie Schlüsselzugriff.
- [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Vollzugriff auf Azure Storage-Blobcontainer und -Daten.
- [Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Lesen, Schreiben und Löschen von Azure Storage-Containern und -Blobs.
- [Leser von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Lesen und Auflisten von Azure Storage-Containern und -Blobs.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Error: Selbstsigniertes Zertifikat in der Zertifikatkette (und vergleichbare Fehler)

Zertifikatfehler treten in der Regel in einer der folgenden Situationen auf:

- Die App wird über einen _transparenten Proxy_ verbunden. Dies bedeutet, dass ein Server (z. B. Ihr Firmenserver) HTTPS-Datenverkehr abfängt, ihn entschlüsselt und ihn dann mit einem selbstsignierten Zertifikat verschlüsselt.
- Sie führen eine Anwendung aus, die ein selbstsigniertes TLS/SSL-Zertifikat in die empfangenen HTTPS-Nachrichten einfügt. Beispiele für Anwendungen, die Zertifikate einfügen, sind u. a. Antivirenprogramme und Prüfsoftware für den Netzwerkverkehr.

Wenn Storage-Explorer ein selbstsigniertes oder nicht vertrauenswürdiges Zertifikat sieht, kann er nicht mehr erkennen, ob die empfangene HTTPS-Nachricht geändert wurde. Wenn Sie über eine Kopie des selbstsignierten Zertifikats verfügen, können Sie Storage-Explorer anweisen, es als vertrauenswürdig zu behandeln. Führen Sie dazu die folgenden Schritte aus:

1. Rufen Sie eine X.509-Kopie (CER) des Zertifikats mit Base64-Verschlüsselung ab.
2. Wechseln Sie zu **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren** , suchen Sie über die Dateiauswahl die CER-Datei, wählen Sie sie aus, und öffnen Sie sie.

Dieses Problem kann auch auftreten, wenn es mehrere Zertifikate gibt (Stamm- und Zwischenzertifikat). Um diesen Fehler zu beheben, müssen beide Zertifikate hinzugefügt werden.

Wenn Sie nicht sicher sind, woher das Zertifikat kommt, können Sie zur Lösung die folgenden Schritte befolgen:

1. Installieren Sie OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Eine Light-Version sollte ausreichend sein.
    * Mac und Linux: Sollte im Betriebssystem enthalten sein.
2. Führen Sie OpenSSL aus.
    * Windows: Öffnen Sie das Installationsverzeichnis, wählen Sie **/bin/** aus, und doppelklicken Sie dann auf **openssl.exe** .
    * Mac und Linux: Führen Sie `openssl` über ein Terminal aus.
3. Führen Sie `s_client -showcerts -connect microsoft.com:443` aus.
4. Suchen Sie nach selbstsignierten Zertifikaten. Wenn Sie sich nicht sicher sind, welche Zertifikate selbst signiert sind, notieren Sie sich, wo immer der Betreff `("s:")` und der Zertifikataussteller `("i:")` identisch sind.
5. Wenn Sie selbstsignierte Zertifikate gefunden haben, kopieren Sie für jedes Zertifikat den gesamten Inhalt von (und einschließlich) `-----BEGIN CERTIFICATE-----` bis `-----END CERTIFICATE-----`, und fügen Sie ihn in eine neue CER-Datei ein.
6. Öffnen Sie Storage-Explorer, und navigieren Sie zu **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren** . Verwenden Sie dann die Dateiauswahl, um die von Ihnen erstellten CER-Dateien zu suchen, auszuwählen und zu öffnen.

Wenn Sie durch Befolgen dieser Schritte keine selbstsignierten Zertifikate finden können, wenden Sie sich über das Feedbacktool an uns. Sie können Storage-Explorer auch über die Befehlszeile öffnen, indem Sie das `--ignore-certificate-errors`-Flag verwenden. Wenn Storage-Explorer mit diesem Flag geöffnet wird, werden Zertifikatfehler ignoriert.

## <a name="sign-in-issues"></a>Probleme bei der Anmeldung

### <a name="blank-sign-in-dialog-box"></a>Leeres Anmeldedialogfeld

Leere Anmeldedialogfelder treten am häufigsten auf, wenn Active Directory-Verbunddienste (AD FS) Storage-Explorer auffordern, eine Umleitung durchzuführen, die von Electron nicht unterstützt wird. Als Problemumgehung können Sie versuchen, den Gerätecodefluss für die Anmeldung zu verwenden. Gehen Sie dazu folgendermaßen vor:

1. Wählen Sie auf der linken vertikalen Symbolleiste die Option **Einstellungen** aus. Wechseln Sie im Bereich „Einstellungen“ zu **Anwendung** > **Anmelden** . Aktivieren Sie die Option **Anmeldung per Gerätecodeflow verwenden** .
2. Öffnen Sie das Dialogfeld **Verbinden** (über das Steckersymbol in der vertikalen Leiste auf der linken Seite oder durch Auswählen der Option **Konto hinzufügen** im Kontobereich).
3. Wählen Sie die Umgebung aus, an der Sie sich anmelden möchten.
4. Wählen Sie **Anmelden** aus.
5. Befolgen Sie die Anweisungen im nächsten Bereich.

Wenn Sie sich nicht bei dem gewünschten Konto anmelden können, da Ihr Standardbrowser bereits bei einem anderen Konto angemeldet ist, führen Sie einen der folgenden Schritte aus:

- Kopieren Sie den Link und den Code manuell in eine private Sitzung Ihres Browsers.
- Kopieren Sie den Link und den Code manuell in einen anderen Browser.

### <a name="reauthentication-loop-or-upn-change"></a>Schleife für eine erneute Authentifizierung oder Änderung des UPN

Wenn Sie sich in einer Schleife für eine erneute Authentifizierung befinden oder den UPN von einem Ihrer Konten geändert haben, befolgen Sie folgende Schritte:

1. Entfernen Sie alle Konten, und schließen Sie dann Storage-Explorer.
2. Löschen Sie den Ordner „.IdentityService“ von Ihrem Computer. Der Ordner befindet sich unter Windows unter `C:\users\<username>\AppData\Local`. Bei Mac und Linux finden Sie den Ordner im Stammverzeichnis Ihres Benutzerverzeichnisses.
3. Wenn Sie Mac oder Linux verwenden, müssen Sie auch den Eintrag „Microsoft.Developer.IdentityService“ aus dem Keystore des Betriebssystems löschen. Unter Mac ist der Keystore die Anwendung *Gnome Keychain* . Unter Linux wird die Anwendung in der Regel als _Schlüsselbund_ bezeichnet, der Name kann jedoch abhängig von Ihrer Distribution abweichen.

### <a name="conditional-access"></a>Bedingter Zugriff

Aufgrund einer Einschränkung in der von Storage-Explorer verwendeten Azure AD-Bibliothek wird der bedingte Zugriff nicht unterstützt, wenn Storage-Explorer unter Windows 10, Linux oder MacOS verwendet wird.

## <a name="mac-keychain-errors"></a>Mac-Keychainfehler

Die macOS-Keychain kann manchmal in einem Zustand wechseln, der Probleme in Verbindung mit der Storage-Explorer-Authentifizierungsbibliothek verursacht. Führen Sie die folgenden Schritte durch, um diesen Zustand der Keychain aufzuheben:

1. Schließen Sie Storage Explorer.
2. Öffnen Sie die Keychain (drücken Sie BEFEHL+LEERTASTE, geben Sie **keychain** ein, und drücken Sie dann die EINGABETASTE).
3. Wählen Sie den Keychain-Wert „login“ (Anmelden) aus.
4. Wählen Sie das Schlosssymbol aus, um die Keychain zu sperren. (Das Schlosssymbol wird nach Abschluss des Vorgangs als verriegelt angezeigt. Es kann einige Sekunden dauern, je nachdem, welche Apps geöffnet sind).

    ![Schlosssymbol](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Öffnen Sie den Storage-Explorer.
6. Sie werden mit einer Nachricht wie „Servicehub möchte auf den Schlüsselbund zugreifen“ aufgefordert. Geben Sie das Kennwort Ihres Mac-Administratorkontos ein, und wählen Sie **Immer zulassen** (oder **Zulassen** , falls **Immer zulassen** nicht verfügbar ist) aus.
7. Versuchen Sie, sich anzumelden.

### <a name="general-sign-in-troubleshooting-steps"></a>Allgemeine Schritte zur Behandlung von Anmeldeproblemen

* Wenn Sie unter macOS arbeiten und im Anmeldefenster nie das Dialogfeld **Warten auf Authentifizierung** angezeigt wird, können Sie es mit [diesen Schritten](#mac-keychain-errors) versuchen.
* Starten Sie Storage-Explorer neu.
* Wenn das Fenster „Authentifizierung“ leer ist, warten Sie mindestens eine Minute, bevor Sie das Dialogfeld für die Authentifizierung schließen.
* Stellen Sie sicher, dass Ihre Proxy- und Zertifikateinstellungen für Ihren Computer und Storage-Explorer ordnungsgemäß konfiguriert sind.
* Wenn Sie Windows ausführen und auf demselben Computer Zugriff auf Visual Studio 2019 und die Anmeldeinformationen haben, versuchen Sie, sich bei Visual Studio 2019 anzumelden. Nach einer erfolgreichen Anmeldung bei Visual Studio 2019 können Sie den Storage-Explorer öffnen und Ihr Konto im Kontobereich sehen.

Wenn keine dieser Methoden funktioniert, [eröffnen Sie in GitHub ein Problem](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Fehlende Abonnements und fehlerhafte Mandanten

Wenn Sie nach erfolgreicher Anmeldung Ihre Abonnements nicht abrufen können, probieren Sie die folgenden Methoden zur Problembehandlung:

* Überprüfen Sie, ob Ihr Konto Zugriff auf die erwarteten Abonnements hat. Sie können Ihren Zugriff überprüfen, indem Sie sich am Portal für die Azure-Umgebung anmelden, die Sie verwenden möchten.
* Stellen Sie sicher, dass die Anmeldung mit der richtigen Azure-Umgebung (Azure, Azure China 21Vianet, Azure Deutschland, Azure US Government oder Benutzerdefinierte Umgebung) erfolgt ist.
* Wenn Sie sich hinter einem Proxyserver befinden, sollten Sie sicherstellen, dass Sie den Storage-Explorer-Proxy richtig konfiguriert haben.
* Entfernen Sie das Konto, und fügen Sie es wieder hinzu.
* Wenn ein Link namens „Weitere Informationen“ vorhanden ist, prüfen Sie, welche Fehlermeldungen für die fehlerhaften Mandanten gemeldet werden. Wenn Sie nicht sicher sind, wie Sie auf die Fehlermeldungen reagieren sollen, können Sie [ein Problem in GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues) öffnen.

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Zugeordnetes Konto oder verbundene Speicherressource kann nicht entfernt werden

Wenn ein zugeordnetes Konto oder eine verbundene Speicherressource über die Benutzeroberfläche nicht entfernt werden kann, können Sie alle zugeordneten Ressourcen manuell löschen, indem Sie die folgenden Ordner löschen:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Schließen Sie Storage-Explorer, bevor Sie diese Ordner löschen.

> [!NOTE]
> Falls Sie irgendwann SSL-Zertifikate importiert haben, sichern Sie den Inhalt des Verzeichnisses `certs`. Anschließend können Sie mithilfe der Sicherung die SSL-Zertifikate wieder importieren.

## <a name="proxy-issues"></a>Proxyprobleme

Vergewissern Sie sich zunächst, dass Sie die folgenden Informationen richtig eingegeben haben:

* Proxy-URL und Portnummer
* Benutzername und Kennwort, sofern für den Proxy erforderlich

> [!NOTE]
> Storage-Explorer unterstützt keine Dateien für die automatische Proxykonfiguration, die zum Konfigurieren von Proxyeinstellungen verwendet werden können.

### <a name="common-solutions"></a>Gängige Lösungen

Wenn weiterhin Probleme auftreten, probieren Sie die folgenden Methoden zur Problembehandlung:

* Wenn Sie ohne Verwendung des Proxys eine Verbindung mit dem Internet herstellen können, überprüfen Sie, ob der Storage-Explorer ohne aktivierte Proxyeinstellungen ausgeführt wird. Wenn dies der Fall ist, liegt möglicherweise ein Problem mit den Proxyeinstellungen vor. Identifizieren Sie die Probleme zusammen mit dem Administrator.
* Überprüfen Sie, ob andere Anwendungen, die den Proxyserver verwenden, wie erwartet ausgeführt werden.
* Prüfen Sie, ob Sie sich für die Azure-Umgebung, die Sie verwenden möchten, am Portal anmelden können.
* Vergewissern Sie sich, dass Antworten von den Dienstendpunkten empfangen werden können. Geben Sie eine der Endpunkt-URLs im Browser ein. Wenn Sie eine Verbindung herstellen können, sollten Sie eine XML-Antwort wie „InvalidQueryParameterValue“ oder eine ähnliche erhalten.
* Wenn eine andere Person auch den Storage-Explorer mit Ihrem Proxyserver verwendet, überprüfen Sie, ob diese Person eine Verbindung herstellen kann. Wenn dies der Fall ist, müssen Sie sich möglicherweise an den Administrator für den Proxyserver wenden.

### <a name="tools-for-diagnosing-issues"></a>Tools zum Diagnostizieren von Problemen

Wenn Sie über Netzwerktools verfügen, z.B. Fiddler für Windows, können Sie die Probleme wie folgt diagnostizieren:

* Wenn Sie über den Proxy arbeiten müssen, müssen Sie das Netzwerktool möglicherweise so konfigurieren, dass eine Verbindung über den Proxy hergestellt wird.
* Überprüfen Sie die vom Netzwerktool verwendete Portnummer.
* Geben Sie die URL des lokalen Hosts und die Portnummer des Netzwerktools als Proxyeinstellungen im Storage-Explorer ein. Bei korrekter Eingabe beginnt das Netzwerktool mit der Protokollierung der vom Storage-Explorer an Verwaltungs- und Dienstendpunkte gesendeten Netzwerkanforderungen. Wenn Sie in einem Browser beispielsweise `https://cawablobgrs.blob.core.windows.net/` für den Blogendpunkt eingeben, erhalten Sie eine Antwort ähnlich der folgenden:

  ![Codebeispiel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Diese Antwort deutet darauf hin, dass die Ressource vorhanden ist, auch wenn Sie nicht darauf zugreifen können.

### <a name="contact-proxy-server-admin"></a>Kontaktaufnahme mit dem Administrator für den Proxyserver

Wenn Ihre Proxyeinstellungen richtig sind, müssen Sie sich möglicherweise an den Administrator für den Proxyserver wenden, um Folgendes zu erreichen:

* Sicherstellen, dass der Proxy nicht den Datenverkehr zu Azure Verwaltungs- oder Ressourcenendpunkten blockiert.
* das von Ihrem Proxyserver verwendete Authentifizierungsprotokoll überprüfen. NTLM-Proxys werden derzeit vom Storage-Explorer nicht unterstützt.

## <a name="unable-to-retrieve-children-error-message"></a>Fehlermeldung: Untergeordnete Elemente können nicht abgerufen werden

Wenn Sie die Verbindung mit Azure über einen Proxy hergestellt haben, überprüfen Sie, ob die Proxyeinstellungen richtig sind. Wenn Ihnen vom Besitzer des Abonnements oder Kontos Zugriff auf eine Ressource erteilt wurde, sollten Sie sich vergewissern, dass Sie über die Berechtigung zum Lesen oder Auflisten für diese Ressource verfügen.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Verbindungszeichenfolge weist keine vollständigen Konfigurationseinstellungen auf

Wenn Sie diese Fehlermeldung erhalten, verfügen Sie möglicherweise nicht über die erforderlichen Berechtigungen zum Abrufen der Schlüssel für Ihr Speicherkonto. Um zu überprüfen, ob dies der Fall ist, navigieren Sie zum Portal, und suchen Sie Ihr Speicherkonto. Klicken Sie hierzu einfach mit der rechten Maustaste auf den Knoten für Ihr Speicherkonto, und wählen Sie dann **Im Portal öffnen** aus. Wechseln Sie dann zum Blatt **Zugriffsschlüssel** . Wenn Sie nicht über die Berechtigung zum Anzeigen von Schlüsseln verfügen, wird die Meldung „Sie haben keinen Zugriff“ angezeigt. Um dieses Problem zu umgehen, können Sie den Kontoschlüssel von einer anderen Person erhalten und mit Name und Schlüssel anfügen, oder Sie können eine andere Person um die SAS für das Speicherkonto bitten und mit dieser das Speicherkonto anfügen.

Wenn die Kontoschlüssel angezeigt werden, ist es ratsam, ein Problem in GitHub zu melden, damit wir Ihnen beim Beheben des Problems helfen können.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Fehler beim Hinzufügen einer neuen Verbindung aufgetreten: TypeError: Eigenschaft 'version' von undefiniert kann nicht gelesen werden

Wenn Sie diese Fehlermeldung erhalten, wenn Sie versuchen, eine benutzerdefinierte Verbindung hinzuzufügen, können die Verbindungsdaten, die in der lokalen Verwaltung für Anmeldeinformationen gespeichert sind, beschädigt sein. Versuchen Sie, Ihre beschädigten lokalen Verbindungen zu löschen und sie dann erneut hinzuzufügen, um dieses Problem zu umgehen:

1. Starten Sie den Storage-Explorer. Wechseln Sie im Menü zu **Hilfe** > **Entwicklertools umschalten** .
2. Wechseln Sie im geöffneten Fenster auf der Registerkarte **Anwendung** zu **Lokaler Speicher** (linke Seite) > **file://** .
3. Abhängig von der Art der Verbindung, bei der ein Problem auftritt, suchen Sie nach dem Schlüssel, und kopieren Sie dann den Wert in einen Text-Editor. Der Wert ist ein Array Ihrer benutzerdefinierten Verbindungsnamen, wie die folgenden:
    * Speicherkonten
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blobcontainer
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Dateifreigaben
        * `StorageExplorer_CustomConnections_Files_v1`
    * Warteschlangen
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabellen
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Nachdem Sie Ihre aktuellen Verbindungsnamen gespeichert haben, legen Sie den Wert in den Entwicklertools auf `[]` fest.

Wenn Sie die nicht beschädigten Verbindungen beibehalten möchten, können Sie die folgenden Schritte ausführen, um die beschädigten Verbindungen zu finden. Wenn es Ihnen nichts ausmacht, alle bestehenden Verbindungen zu verlieren, können Sie diese Schritte überspringen und den plattformspezifischen Anweisungen folgen, um Ihre Verbindungsdaten zu löschen.

1. Fügen Sie in einem Text-Editor jeden Verbindungsnamen erneut den Entwicklertools hinzu, und überprüfen Sie dann, ob die Verbindung weiterhin funktioniert.
2. Wenn eine Verbindung ordnungsgemäß funktioniert, ist sie nicht beschädigt und Sie können sie dort sicher belassen. Wenn eine Verbindung nicht funktioniert, entfernen Sie den Wert aus den Entwicklertools und zeichnen Sie ihn auf, damit Sie ihn später wieder hinzufügen können.
3. Wiederholen Sie den Vorgang, bis Sie alle Ihre Verbindungen überprüft haben.

Nachdem Sie alle Ihre Verbindungen überprüft haben, müssen Sie für alle Verbindungsnamen, die nicht zurückgesetzt werden, ihre beschädigten Daten löschen (falls vorhanden) und sie mit den Standardschritten in Storage-Explorer zurücksetzen:

# <a name="windows"></a>[Windows](#tab/Windows)

1. Suchen Sie im Menü **Start** nach der **Anmeldeinformationsverwaltung** und öffnen Sie sie.
2. Wechseln Sie zu **Windows-Anmeldeinformationen** .
3. Suchen Sie unter **Generische Anmeldeinformationen** nach Einträgen, die den Schlüssel `<connection_type_key>/<corrupted_connection_name>` aufweisen (z. B. `StorageExplorer_CustomConnections_Accounts_v1/account1`).
4. Löschen Sie diese Einträge, und fügen Sie die Verbindungen erneut hinzu.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Öffnen Sie Spotlight (BEFEHL+LEERTASTE), und suchen Sie nach **Schlüsselbundverwaltung (Keychain Access)** .
2. Suchen Sie nach Einträgen, die den Schlüssel `<connection_type_key>/<corrupted_connection_name>` aufweisen (z. B. `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Löschen Sie diese Einträge, und fügen Sie die Verbindungen erneut hinzu.

# <a name="linux"></a>[Linux](#tab/Linux)

Die lokale Anmeldeinformationsverwaltung variiert je nach Linux-Distribution. Wenn Ihre Linux-Distribution kein integriertes GUI-Tool für die lokale Anmeldeinformationsverwaltung enthält, können Sie ein Drittanbietertool zur Verwaltung Ihrer lokalen Anmeldeinformationen installieren. Sie können z. B. [Seahorse](https://wiki.gnome.org/Apps/Seahorse/) verwenden, ein Open-Source-GUI-Tool zur Verwaltung lokaler Anmeldeinformationen für Linux.

1. Öffnen Sie Ihr lokales Tool zur Verwaltung von Anmeldeinformationen, und suchen Sie nach Ihren gespeicherten Anmeldeinformationen.
2. Suchen Sie nach Einträgen, die den Schlüssel `<connection_type_key>/<corrupted_connection_name>` aufweisen (z. B. `StorageExplorer_CustomConnections_Accounts_v1/account1`).
3. Löschen Sie diese Einträge, und fügen Sie die Verbindungen erneut hinzu.
---

Wenn der Fehler nach Ausführung dieser Schritte weiterhin auftritt oder wenn Sie mitteilen möchten, was Ihrer Meinung nach die Verbindungen beschädigt hat, [öffnen Sie ein Problem](https://github.com/microsoft/AzureStorageExplorer/issues) auf unserer GitHub-Seite.

## <a name="issues-with-sas-url"></a>Probleme mit der SAS-URL

Wenn Sie eine Verbindung mit einem Dienst über eine SAS-URL herstellen und ein Fehler auftritt:

* Stellen Sie sicher, dass die URL die erforderlichen Berechtigungen zum Lesen oder Auflisten von Ressourcen umfasst.
* Vergewissern Sie sich, dass die URL nicht abgelaufen ist.
* Wenn die SAS-URL auf einer Zugriffsrichtlinie basiert, stellen Sie sicher, dass die Zugriffsrichtlinie nicht aufgehoben wurde.

Wenn Sie versehentlich eine ungültige SAS-URL angefügt haben und diese jetzt nicht trennen können, führen Sie diese Schritte aus:

1. Drücken Sie in Storage-Explorer F12, um das Fenster mit den Entwicklertools zu öffnen.
2. Wählen Sie auf der Registerkarte **Anwendung** die Option **Lokaler Speicher** > **file://** in der Struktur auf der linken Seite aus.
3. Suchen Sie den Schlüssel, der dem Diensttyp des problematischen SAS-URI zugeordnet ist. Wenn z.B. der fehlerhafte SAS-URI für einen Blobcontainer gilt, suchen Sie nach dem Schlüssel mit dem Namen `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Der Wert des Schlüssels sollte ein JSON-Array sein. Suchen Sie das Objekt, das dem fehlerhaften URI zugeordnet ist, und löschen Sie es anschließend.
5. Drücken Sie STRG+R, um den Storage-Explorer neu zu laden.

## <a name="linux-dependencies"></a>Linux-Abhängigkeiten

### <a name="snap"></a>Snap

Storage-Explorer 1.10.0 und höher ist als Snap im Snap Store verfügbar. Das Storage-Explorer-Snap installiert alle seine Abhängigkeiten automatisch, und es wird aktualisiert, wenn eine neue Version des Snaps verfügbar ist. Die Installation des Storage-Explorer-Snaps ist die empfohlene Installationsmethode.

Storage-Explorer erfordert den Einsatz eines Kennwort-Managers, mit dem Sie möglicherweise manuell eine Verbindung herstellen müssen, damit Storage-Explorer ordnungsgemäß funktioniert. Sie können Storage-Explorer mit dem Kennwort-Manager Ihres Systems verbinden, indem Sie den folgenden Befehl ausführen:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>.tar.gz-Datei

Sie können die Anwendung auch als „.tar.gz“-Datei herunterladen, aber Sie müssen die Abhängigkeiten manuell installieren.

Storage-Explorer, wie im „.tar. gz“-Download bereitgestellt, wird nur für die folgenden Versionen von Ubuntu unterstützt. Storage-Explorer funktioniert möglicherweise mit anderen Linux-Distributionen, diese werden aber nicht offiziell unterstützt.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Für Storage-Explorer muss .NET Core auf dem System installiert sein. Wir empfehlen .NET Core 2.1, aber Storage-Explorer funktioniert auch mit Version 2.2.

> [!NOTE]
> Storage-Explorer bis Version 1.7.0 erfordert .NET Core 2.0. Wenn Sie bereits eine neuere Version von .NET Core installiert haben, müssen Sie [Storage-Explorer mit Patches versehen](#patching-storage-explorer-for-newer-versions-of-net-core). Wenn Sie Storage-Explorer 1.8.0 oder höher ausführen, benötigen Sie mindestens .NET Core 2.1.

# <a name="ubuntu-2004"></a>[Ubuntu 20.04 ](#tab/2004)

1. Laden Sie die Storage-Explorer- Datei „.tar.gz“ herunter.
2. Installieren Sie die [.NET Core-Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Laden Sie die Storage-Explorer- Datei „.tar.gz“ herunter.
2. Installieren Sie die [.NET Core-Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Laden Sie die Storage-Explorer- Datei „.tar.gz“ herunter.
2. Installieren Sie die [.NET Core-Runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Viele Bibliotheken, die von Storage-Explorer benötigt werden, werden mit den Standardinstallationen von Ubuntu von Canonical vorinstalliert. In benutzerdefinierten Umgebungen fehlen möglicherweise einige dieser Bibliotheken. Wenn beim Starten von Storage-Explorer Probleme auftreten, sollten Sie sicherstellen, dass die folgenden Pakete auf dem System installiert sind:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Versehen von Storage-Explorer mit Patches für neuere Versionen von .NET Core

Bei Storage-Explorer 1.7.0 oder früher müssen Sie möglicherweise die von Storage-Explorer verwendete .NET Core-Version mit Patches versehen.

1. Laden Sie Version 1.5.43 von StreamJsonRpc [über NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) herunter. Suchen Sie rechts auf der Seite nach dem Link zum Herunterladen des Pakets („Download package“).
2. Ändern Sie nach dem Herunterladen des Pakets die Dateierweiterung von `.nupkg` in `.zip`.
3. Entzippen Sie das Paket.
4. Öffnen Sie den Ordner `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopieren Sie `StreamJsonRpc.dll` an die folgenden Speicherorte im Storage-Explorer-Ordner:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>„In Explorer öffnen“ über das Azure-Portal funktioniert nicht

Wenn die Schaltfläche **In Explorer öffnen** im Azure-Portal nicht funktioniert, sollten Sie sicherstellen, dass Sie einen kompatiblen Browser verwenden. Für die folgenden Browser wurde die Kompatibilität getestet:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Nächste Schritte

Wenn keine der Lösungen funktioniert, [öffnen Sie in GitHub ein Problem](https://github.com/Microsoft/AzureStorageExplorer/issues). Sie können dazu auch die Schaltfläche **Problem auf GitHub melden** in der linken unteren Ecke auswählen.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)