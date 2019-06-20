---
title: Azure Storage-Explorer – Leitfaden zur Problembehandlung | Microsoft-Dokumentation
description: Übersicht über Debugverfahren für Azure Storage-Explorer
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 980dc850537b7419e4ee48391acd5ba971fb3fed
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306731"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage-Explorer – Leitfaden zur Problembehandlung

Bei Microsoft Azure Storage-Explorer handelt es sich um eine eigenständige App, über die Sie unter Windows, macOS und Linux komfortabel mit Azure Storage-Daten arbeiten können. Die App kann mit Storage-Konten verbunden werden, die in Azure, nationalen Clouds und Azure Stack gehostet werden.

In diesem Leitfaden sind Lösungen für häufig aufgetretene Probleme im Storage-Explorer zusammengefasst.

## <a name="role-based-access-control-permission-issues"></a>Probleme mit Berechtigungen für die rollenbasierte Zugriffssteuerung

Die [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) ermöglicht eine präzise Zugriffsverwaltung von Azure-Ressourcen, indem Berechtigungssätze in _Rollen_ kombiniert werden. Hier sind einige Vorschläge angegeben, die Sie befolgen können, um RBAC in Storage-Explorer einzurichten.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Was benötige ich, damit meine Ressourcen in Storage-Explorer angezeigt werden?

Falls Sie Probleme beim Zugreifen auf Speicherressourcen per RBAC haben, kann dies daran liegen, dass Ihnen nicht die richtigen Rollen zugewiesen wurden. In den folgenden Abschnitten werden die Berechtigungen beschrieben, die von Storage-Explorer derzeit zum Zugreifen auf Ihre Speicherressourcen benötigt werden.

Wenden Sie sich an Ihren Azure-Kontoadministrator, falls Sie nicht sicher sind, ob Sie über die richtigen Rollen oder Berechtigungen verfügen.

#### <a name="read-listget-storage-accounts"></a>Lesen: Auflisten/Abrufen von Speicherkonten

Sie benötigen die Berechtigung zum Auflisten von Speicherkonten. Sie können diese Berechtigung erhalten, indem Ihnen die Rolle „Leser“ zugewiesen wird.

#### <a name="list-storage-account-keys"></a>Auflisten von Speicherkontoschlüsseln

Für Storage-Explorer können auch Kontoschlüssel zum Authentifizieren von Anforderungen verwendet werden. Sie können Zugriff auf Schlüssel mit leistungsstärkeren Rollen erhalten, z. B. der Rolle „Mitwirkender“.

> [!NOTE]
> Mit Zugriffsschlüsseln werden für deren Besitzer uneingeschränkte Berechtigungen gewährt. Aus diesem Grund ist es normalerweise nicht zu empfehlen, dass sie an Kontobenutzer ausgegeben werden. Wenn Sie Zugriffsschlüssel widerrufen müssen, können Sie sie über das [Azure-Portal](https://portal.azure.com/) neu generieren.

#### <a name="data-roles"></a>Datenrollen

Ihnen muss mindestens eine Rolle zugewiesen sein, über die Zugriff zum Lesen der Daten von Ressourcen gewährt wird. Wenn Sie beispielsweise Blobs auflisten oder herunterladen müssen, benötigen Sie mindestens die Rolle „Storage-Blobdatenleser“.

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Warum benötige ich eine Rolle auf der Verwaltungsebene, damit meine Ressourcen in Storage-Explorer angezeigt werden?

Azure Storage verfügt über zwei Zugriffsebenen: _Verwaltung_ und _Daten_. Auf Abonnements und Speicherkonten wird über die Verwaltungsebene zugegriffen. Auf Container, Blobs und andere Datenressourcen wird über die Datenebene zugegriffen. Wenn Sie beispielsweise eine Liste mit Ihren Speicherkonten aus Azure abrufen möchten, senden Sie eine Anforderung an den Verwaltungsendpunkt. Wenn Sie eine Liste mit den Blobcontainern eines Kontos anzeigen möchten, können Sie eine Anforderung an den entsprechenden Dienstendpunkt senden.

RBAC-Rollen können Berechtigungen für den Zugriff auf die Verwaltungs- oder Datenebene enthalten. Mit der Rolle „Leser“ wird Ihnen beispielsweise Lesezugriff auf die Ressourcen der Verwaltungsebene gewährt.

Mit der Rolle „Leser“ werden genau genommen keine Berechtigungen für die Datenebene gewährt, und sie ist für den Zugriff darauf nicht erforderlich.

Storage-Explorer erleichtert Ihnen den Zugriff auf Ihre Ressourcen, indem die benötigten Informationen zum Herstellen einer Verbindung mit Ihren Azure-Ressourcen für Sie zusammengestellt werden. Zum Anzeigen Ihrer Blobcontainer sendet Storage-Explorer beispielsweise eine Anforderung zum Auflisten von Containern an den Blob-Dienstendpunkt. Um diesen Endpunkt zu erhalten, durchsucht Storage-Explorer die Liste mit den Abonnements und Speicherkonten, auf die Sie Zugriff haben. Zur Ermittlung Ihrer Abonnements und Speicherkonten benötigt Storage-Explorer aber auch Zugriff auf die Verwaltungsebene.

Wenn Sie nicht über eine Rolle verfügen, mit der Berechtigungen für die Verwaltungsebene gewährt werden, kann Storage-Explorer nicht die Informationen abrufen, die zum Herstellen der Verbindung mit der Datenebene benötigt werden.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Wie kann ich vorgehen, wenn ich die benötigten Berechtigungen für die Verwaltungsebene nicht von meinem Administrator erhalten kann?

Derzeit können wir noch keine RBAC-basierte Lösung anbieten. Zur Problemumgehung können Sie einen SAS-URI anfordern, um ihn [an Ihre Ressource anzufügen](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#attach-a-service-by-using-a-shared-access-signature-sas).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fehler selbstsigniertes Zertifikat in der Zertifikatkette (und vergleichbare Fehler)

Zertifikatfehler werden durch eine der beiden folgenden Situationen verursacht:

1. Die App ist über einen „transparenten Proxy“ verbunden, d.h., ein Server (z.B. Ihr Firmenserver) fängt HTTPS-Datenverkehr ab, entschlüsselt ihn und verschlüsselt ihn dann mit einem selbstsignierten Zertifikat.
2. Sie führen eine Anwendung aus, die ein selbstsigniertes SSL-Zertifikat in die empfangenen HTTPS-Nachrichten einfügt. Beispiele für Anwendungen, die Zertifikate einfügen, sind u. a. Antivirenprogramme und Prüfsoftware für den Netzwerkverkehr.

Wenn Storage-Explorer ein selbstsigniertes oder nicht vertrauenswürdiges Zertifikat sieht, kann er nicht mehr erkennen, ob die empfangene HTTPS-Nachricht geändert wurde. Wenn Sie über eine Kopie des selbstsignierten Zertifikats verfügen, können Sie Storage-Explorer anweisen, es als vertrauenswürdig zu behandeln. Führen Sie dazu die folgenden Schritte aus:

1. Abrufen einer X.509-Kopie (CER) des Zertifikats mit Base64-Verschlüsselung
2. Klicken Sie auf **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren**, suchen Sie über die Dateiauswahl die CER-Datei, wählen Sie sie aus, und öffnen Sie sie.

Dieses Problem kann auch das Ergebnis mehrerer Zertifikate (Stamm- und Zwischenzertifikat) sein. Beide Zertifikate müssen hinzugefügt werden, um den Fehler zu beheben.

Wenn Sie nicht sicher sind, woher das Zertifikat kommt, können Sie zur Lösung die folgenden Schritte ausführen:

1. Installieren Sie OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (eine Light-Version sollte ausreichend sein)
    * Mac und Linux: sollte im Betriebssystem enthalten sein
2. Führen Sie OpenSSL aus.
    * Windows: Öffnen Sie das Installationsverzeichnis, klicken Sie auf **/bin/** , und doppelklicken Sie dann auf **openssl.exe**.
    * Mac und Linux: Führen Sie **openssl** über ein Terminal aus.
3. Führen Sie `s_client -showcerts -connect microsoft.com:443` aus.
4. Suchen Sie nach selbstsignierten Zertifikaten. Gehen Sie wie folgt vor, wenn Sie nicht sicher sind, welche Zertifikate selbstsigniert sind: Suchen Sie nach Stellen, an denen der Antragssteller `("s:")` und der Zertifikataussteller `("i:")` identisch sind.
5. Wenn Sie selbstsignierte Zertifikate gefunden haben, kopieren Sie für jedes Zertifikat den gesamten Inhalt von einschließlich **-----BEGIN CERTIFICATE-----** bis **-----END CERTIFICATE-----** , und fügen Sie ihn in eine neue CER-Datei ein.
6. Öffnen Sie den Storage-Explorer, klicken Sie auf **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren**, suchen Sie über die Dateiauswahl die erstellten CER-Dateien, wählen Sie sie aus, und öffnen Sie sie.

Wenn Sie mit den oben beschriebenen Schritten keine selbstsignierten Zertifikate finden, wenden Sie sich über das Feedbacktool an uns, um weitere Unterstützung zu erhalten. Sie können Storage-Explorer auch über die Befehlszeile mit dem `--ignore-certificate-errors`-Flag starten. Wenn Azure Storage-Explorer mit diesem Flag gestartet wird, ignoriert er Zertifikatfehler.

## <a name="sign-in-issues"></a>Probleme bei der Anmeldung

### <a name="blank-sign-in-dialog"></a>Leeres Anmeldedialogfeld

Leere Anmeldedialogfelder werden meist dadurch verursacht, dass ADFS von Storage-Explorer die Durchführung einer Umleitung anfordert, die von Electron nicht unterstützt wird. Als Problemumgehung können Sie versuchen, den Gerätecodefluss für die Anmeldung zu verwenden. Dazu müssen Sie die folgenden Schritte ausführen:

1. Menü: „Vorschau“ > „Use Device Code Sign-In“ (Gerätecodeanmeldung verwenden).
2. Öffnen Sie das Dialogfeld „Verbinden“ (über das Steckersymbol in der vertikalen Leiste auf der linken Seite oder über die Option „Konto hinzufügen“ im Kontobereich).
3. Wählen Sie aus, an welcher Umgebung Sie sich anmelden möchten.
4. Klicken Sie auf die Schaltfläche „Anmelden“.
5. Befolgen Sie die Anweisungen im nächsten Bereich.

Falls Sie Probleme beim Anmelden am gewünschten Konto haben, weil über Ihren Standardbrowser bereits die Anmeldung an einem anderen Konto erfolgt ist, haben Sie folgende Möglichkeiten:

1. Kopieren Sie den Link und den Code manuell in eine private Sitzung Ihres Browsers.
2. Kopieren Sie den Link und den Code manuell in einen anderen Browser.

### <a name="reauthentication-loop-or-upn-change"></a>Schleife für eine erneute Authentifizierung oder Änderung des UPN

Wenn Sie sich in einer Schleife für eine erneute Authentifizierung befinden oder den UPN von einem Ihrer Konten geändert haben, versuchen Sie folgende Schritte:

1. Entfernen Sie alle Konten, und schließen Sie dann den Storage-Explorer.
2. Löschen Sie den Ordner „.IdentityService“ von Ihrem Computer. Der Ordner befindet sich unter Windows unter `C:\users\<username>\AppData\Local`. Bei Mac und Linux finden Sie den Ordner im Stammverzeichnis Ihres Benutzerverzeichnisses.
3. Wenn Sie Mac oder Linux verwenden, müssen Sie auch den Eintrag „Microsoft.Developer.IdentityService“ aus Ihrem Betriebssystemkeystore löschen. Unter Mac ist der Keystore die Anwendung „Gnome Keychain“. Bei Linux wird die Anwendung in der Regel als „Schlüsselbund“ bezeichnet, der Name kann jedoch abhängig von Ihrer Distribution abweichen.

### <a name="conditional-access"></a>Bedingter Zugriff

Bedingter Zugriff wird nicht unterstützt, wenn Storage-Explorer unter Windows 10, Linux oder macOS verwendet wird. Dies liegt an einer Einschränkung der AAD-Bibliothek, die Storage-Explorer verwendet.

## <a name="mac-keychain-errors"></a>Mac-Keychainfehler

Die macOS-Keychain kann sich manchmal in einem Zustand befinden, der Probleme in Verbindung mit der Storage Explorer-Authentifizierungsbibliothek verursacht. Versuchen Sie, diesen Zustand der Keychain mit den folgenden Schritten aufzuheben:

1. Schließen Sie Storage Explorer.
2. Öffnen Sie die Keychain (**BEFEHL+ LEER**, geben Sie die Keychain ein, drücken Sie EINGABE).
3. Wählen Sie den Keychain-Wert „login“ (Anmelden).
4. Klicken Sie auf das Vorhängeschlosssymbol, um die Keychain zu sperren (das Schloss wird nach Abschluss des Vorgangs in gesperrter Position dargestellt; dies kann ein paar Sekunden dauern, je nachdem, welche Apps Sie geöffnet haben).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Starten Sie Storage Explorer.
6. Ein Popupfenster mit einem Text wie dem Folgenden sollte angezeigt werden: „Servicehub möchte auf den Schlüsselbund zugreifen.“ Geben Sie in diesem Fall das Kennwort Ihres Mac-Administratorkontos ein, und klicken Sie auf **Immer zulassen** (oder **Zulassen**, falls **Immer zulassen** nicht verfügbar ist).
7. Versuchen Sie, sich anzumelden.

### <a name="general-sign-in-troubleshooting-steps"></a>Allgemeine Schritte zur Behandlung von Anmeldeproblemen

* Wenn Sie unter macOS arbeiten und im Anmeldefenster nie das Dialogfeld „Warten auf Authentifizierung...“ angezeigt wird, können Sie es mit [diesen Schritten](#mac-keychain-errors) versuchen.
* Neustart von Storage-Explorer
* Wenn das Fenster „Authentifizierung“ leer ist, warten Sie mindestens eine Minute, bevor Sie das Dialogfeld für die Authentifizierung schließen.
* Stellen Sie sicher, dass Ihre Proxy- und Zertifikateinstellungen für Ihren Computer und den Storage-Explorer ordnungsgemäß konfiguriert sind.
* Wenn Sie unter Windows arbeiten und auf demselben Computer Zugriff auf Visual Studio 2019 haben und sich anmelden können, können Sie versuchen, sich bei Visual Studio 2019 anzumelden. Nach einer erfolgreichen Anmeldung bei Visual Studio 2019 können Sie den Storage-Explorer öffnen und Ihr Konto im Kontobereich sehen.

Wenn keine dieser Methoden funktioniert, [eröffnen Sie auf GitHub ein Problem](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Fehlende Abonnements und fehlerhafte Mandanten

Wenn Sie nach erfolgreicher Anmeldung Ihre Abonnements nicht abrufen können, probieren Sie die folgenden Methoden zur Problembehebung:

* Überprüfen Sie, ob Ihr Konto Zugriff auf die erwarteten Abonnements hat. Sie können Ihren Zugriff überprüfen, indem Sie sich am Portal für die Azure-Umgebung anmelden, die Sie verwenden möchten.
* Stellen Sie sicher, dass die Anmeldung mit der richtigen Azure-Umgebung (Azure, Azure China 21Vianet, Azure Deutschland, Azure US Government oder Benutzerdefinierte Umgebung) erfolgt ist.
* Wenn Sie sich hinter einem Proxy befinden, sollten Sie sicherstellen, dass Sie den Storage-Explorer-Proxy richtig konfiguriert haben.
* Entfernen Sie das Konto, und fügen Sie es wieder hinzu.
* Wenn ein Link namens „Weitere Informationen“ vorhanden ist, prüfen Sie, welche Fehlermeldungen für die fehlerhaften Mandanten gemeldet werden. Wenn Sie sich nicht sicher sind, wie Sie mit den angezeigten Fehlermeldungen umgehen sollen, können Sie jederzeit [ein Thema auf GitHub erstellen](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Zugeordnetes Konto oder verbundene Speicherressource kann nicht entfernt werden

Wenn ein zugeordnetes Konto oder eine verbundene Speicherressource über die Benutzeroberfläche nicht entfernt werden kann, können Sie alle zugeordneten Ressourcen manuell löschen, indem Sie die folgenden Ordner löschen:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Schließen Sie Storage-Explorer, bevor Sie die oben genannten Ordner löschen.

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

* Wenn Sie ohne Verwendung des Proxys eine Verbindung mit dem Internet herstellen können, überprüfen Sie, ob der Storage-Explorer ohne aktivierte Proxyeinstellungen ausgeführt wird. Wenn dies der Fall ist, liegt möglicherweise ein Problem mit den Proxyeinstellungen vor. Identifizieren Sie die Probleme zusammen mit dem Administrator für den Proxyserver.
* Überprüfen Sie, ob andere Anwendungen, die den Proxyserver verwenden, wie erwartet ausgeführt werden.
* Prüfen Sie, ob Sie sich für die Azure-Umgebung, die Sie verwenden möchten, am Portal anmelden können.
* Vergewissern Sie sich, dass Antworten von den Dienstendpunkten empfangen werden können. Geben Sie eine der Endpunkt-URLs im Browser ein. Wenn Sie eine Verbindung herstellen können, sollten Sie eine XML-Antwort wie „InvalidQueryParameterValue“ oder eine ähnliche erhalten.
* Wenn eine andere Person auch den Storage-Explorer mit Ihrem Proxyserver verwendet, überprüfen Sie, ob diese Person eine Verbindung herstellen kann. Wenn dies der Fall ist, müssen Sie sich möglicherweise an den Administrator für den Proxyserver wenden.

### <a name="tools-for-diagnosing-issues"></a>Tools zum Diagnostizieren von Problemen

Wenn Sie über Netzwerktools verfügen, z.B. Fiddler für Windows, können Sie die Probleme wie folgt diagnostizieren:

* Wenn Sie über den Proxy arbeiten müssen, müssen Sie das Netzwerktool möglicherweise so konfigurieren, dass eine Verbindung über den Proxy hergestellt wird.
* Überprüfen Sie die vom Netzwerktool verwendete Portnummer.
* Geben Sie die URL des lokalen Hosts und die Portnummer des Netzwerktools als Proxyeinstellungen im Storage-Explorer ein. Bei korrekter Eingabe beginnt das Netzwerktool mit der Protokollierung der vom Storage-Explorer an Verwaltungs- und Dienstendpunkte gesendeten Netzwerkanforderungen. Wenn Sie in einem Browser beispielsweise https://cawablobgrs.blob.core.windows.net/ für den Blogendpunkt eingeben, erhalten Sie eine Antwort ähnlich der folgenden, die besagt, dass die Ressource vorhanden ist, Sie jedoch keinen Zugriff darauf haben.

![Codebeispiel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Kontaktaufnahme mit dem Administrator für den Proxyserver

Wenn Ihre Proxyeinstellungen richtig sind, müssen Sie sich möglicherweise an den Administrator für den Proxyserver wenden und

* sicherstellen, dass der Proxy nicht den Datenverkehr zu Azure Verwaltungs- oder Ressourcenendpunkten blockiert.
* das von Ihrem Proxyserver verwendete Authentifizierungsprotokoll überprüfen. NTLM-Proxys werden derzeit vom Storage-Explorer nicht unterstützt.

## <a name="unable-to-retrieve-children-error-message"></a>Fehlermeldung: Untergeordnete Elemente können nicht abgerufen werden

Wenn Sie die Verbindung mit Azure über einen Proxy hergestellt haben, überprüfen Sie, ob die Proxyeinstellungen richtig sind. Wenn Ihnen vom Besitzer des Abonnements oder Kontos Zugriff auf eine Ressource erteilt wurde, sollten Sie sich vergewissern, dass Sie über die Berechtigung zum Lesen oder Auflisten für diese Ressource verfügen.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Verbindungszeichenfolge weist keine vollständigen Konfigurationseinstellungen auf

Wenn Sie diese Fehlermeldung erhalten, verfügen Sie möglicherweise nicht über die erforderlichen Berechtigungen zum Abrufen der Schlüssel für Ihr Speicherkonto. Um zu überprüfen, ob dies der Fall ist, navigieren Sie zum Portal, und suchen Sie Ihr Speicherkonto. Klicken Sie hierzu einfach mit der rechten Maustaste auf den Knoten für Ihr Speicherkonto, und klicken Sie dann auf „Im Portal öffnen“. Wechseln Sie dann zum Blatt „Zugriffsschlüssel“. Wenn Sie nicht über Berechtigungen zum Anzeigen von Schlüsseln verfügen, wird eine Seite mit der Meldung „Sie haben keinen Zugriff“ angezeigt. Um dieses Problem zu umgehen, können Sie den Kontoschlüssel von einer anderen Person erhalten und mit Name und Schlüssel anfügen, oder Sie können eine andere Person um die SAS für das Speicherkonto bitten und mit dieser das Speicherkonto anfügen.

Wenn die Kontoschlüssel angezeigt werden, ist es ratsam, ein Problem bei GitHub zu melden, damit wir Ihnen beim Beheben des Problems helfen können.

## <a name="issues-with-sas-url"></a>Probleme mit der SAS-URL

Wenn Sie eine Verbindung mit einem Dienst über eine SAS-URL herstellen und dieser Fehler auftritt:

* Stellen Sie sicher, dass die URL die erforderlichen Berechtigungen zum Lesen oder Auflisten von Ressourcen umfasst.
* Vergewissern Sie sich, dass die URL nicht abgelaufen ist.
* Wenn die SAS-URL auf einer Zugriffsrichtlinie basiert, stellen Sie sicher, dass die Zugriffsrichtlinie nicht aufgehoben wurde.

Wenn Sie versehentlich eine ungültige SAS-URL angefügt haben und diese nicht trennen können, führen Sie diese Schritte aus:

1. Drücken Sie im Storage-Explorer F12, um das Fenster mit den Entwicklertools zu öffnen.
2. Klicken Sie auf der Registerkarte „Anwendung“ und dann in der Struktur auf der linken Seite auf „Lokaler Speicher“ > „file://“.
3. Suchen Sie den Schlüssel, der dem Diensttyp des problematischen SAS-URI zugeordnet ist. Wenn z.B. der fehlerhafte SAS-URI für einen Blobcontainer gilt, suchen Sie nach dem Schlüssel mit dem Namen `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Der Wert des Schlüssels sollte ein JSON-Array sein. Suchen Sie das Objekt, das dem fehlerhaften URI zugeordnet ist, und entfernen Sie es.
5. Drücken Sie STRG+R, um den Storage-Explorer neu zu laden.

## <a name="linux-dependencies"></a>Linux-Abhängigkeiten

Im Allgemeinen werden die folgenden Pakete benötigt, um Storage-Explorer unter Linux auszuführen:

* [.NET Core 2.0-Runtime](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) – Hinweis: Storage-Explorer bis Version 1.7.0 erfordert .NET Core 2.0. Wenn Sie bereits eine neuere Version von .NET Core installiert haben, müssen Sie Storage-Explorer ggf. mit Patches versehen (siehe unten). Wenn Sie Storage-Explorer 1.8.0 oder höher ausführen, können Sie .NET Core bis Version 2.2 verwenden. Die Funktion höherer Versionen als 2.2 wurde bisher nicht überprüft.
* `libgnome-keyring-common` und `libgnome-keyring-dev`
* `libgconf-2-4`

Je nach Ihrer Distribution müssen Sie unter Umständen andere bzw. weitere Pakete installieren.

Storage-Explorer wird unter Ubuntu 18.04, 16.04 und 14.04 offiziell unterstützt. Dies sind die Installationsschritte für einen „leeren“ Computer:

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Laden Sie Storage-Explorer herunter.
2. Installieren Sie die .NET Core-Runtime. Die letzte verifizierte Version ist: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-2.0.8) (Wenn Sie bereits eine neuere Version installiert haben, müssen Sie Storage-Explorer ggf. mit Patches versehen (siehe unten).)
3. Führen Sie `sudo apt-get install libgconf-2-4` aus.
4. Führen Sie `sudo apt install libgnome-keyring-common libgnome-keyring-dev` aus.

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Laden Sie Storage-Explorer herunter.
2. Installieren Sie die .NET Core-Runtime. Die letzte verifizierte Version ist: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.0.8) (Wenn Sie bereits eine neuere Version installiert haben, müssen Sie Storage-Explorer ggf. mit Patches versehen (siehe unten).)
3. Führen Sie `sudo apt install libgnome-keyring-dev` aus.

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Laden Sie Storage-Explorer herunter.
2. Installieren Sie die .NET Core-Runtime. Die letzte verifizierte Version ist: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-2.0.8) (Wenn Sie bereits eine neuere Version installiert haben, müssen Sie Storage-Explorer ggf. mit Patches versehen (siehe unten).)
3. Führen Sie `sudo apt install libgnome-keyring-dev` aus.

---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Versehen von Storage-Explorer mit Patches für neuere Versionen von .NET Core 
Wenn Sie eine höhere Version von .NET Core als 2.0 installiert haben und Version 1.7.0 oder früher von Storage-Explorer ausführen, müssen Sie Storage-Explorer wahrscheinlich über die folgenden Schritte mit Patches versehen:
1. Laden Sie Version 1.5.43 von StreamJsonRpc [über NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) herunter. Suchen Sie rechts auf der Seite nach dem Link zum Herunterladen des Pakets („Download package“).
2. Ändern Sie nach dem Herunterladen des Pakets die Dateierweiterung von `.nupkg` in `.zip`.
3. Entzippen Sie das Paket.
4. Besuchen Sie `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopieren Sie `StreamJsonRpc.dll` an die folgenden Speicherorte im Storage-Explorer-Ordner:
    1. `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
    2. `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>„In Explorer öffnen“ über das Azure-Portal funktioniert nicht

Wenn die Schaltfläche „In Explorer öffnen“ im Azure-Portal nicht funktioniert, sollten Sie sicherstellen, dass Sie einen kompatiblen Browser verwenden. Für die folgenden Browser wurde die Kompatibilität getestet.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Nächste Schritte

Wenn keine der Lösungen funktioniert, [eröffnen Sie auf GitHub ein Problem](https://github.com/Microsoft/AzureStorageExplorer/issues). Sie können auch über die Schaltfläche „Problem auf GitHub melden“ unten links schnell auf GitHub zugreifen.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)
