---
title: Verwenden von Azure AD Multi-Factor Authentication mit NPS – Azure Active Directory
description: Hier erfahren Sie, wie Sie Azure AD Multi-Factor Authentication-Funktionen bei Ihrer vorhandenen NPS-Authentifizierungsinfrastruktur (Network Policy Server, Netzwerkrichtlinienserver) verwenden.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8340712e10721374bb2f0a35ac2e2e9a6abf181c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743036"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>Integrieren Ihrer vorhandenen NPS-Infrastruktur in Azure AD Multi-Factor Authentication

Die NPS-Erweiterung (Network Policy Server, Netzwerkrichtlinienserver) für Azure AD Multi-Factor Authentication (MFA) fügt Ihrer Authentifizierungsinfrastruktur cloudbasierte MFA-Funktionen hinzu und nutzt dabei Ihre vorhandenen Server. Mit der NPS-Erweiterung können Sie Ihrem bestehenden Authentifizierungsvorgang eine Überprüfung per Telefonanruf, SMS oder Telefon-App hinzufügen, ohne neue Server installieren, konfigurieren und verwalten zu müssen.

Die NPS-Erweiterung fungiert als Adapter zwischen RADIUS und cloudbasierter Azure AD Multi-Factor Authentication, um eine zweite Authentifizierungsstufe für Verbund- oder synchronisierte Benutzer bereitzustellen.

## <a name="how-the-nps-extension-works"></a>Funktionsweise der NPS-Erweiterung

Wenn Sie die NPS-Erweiterung für Azure AD Multi-Factor Authentication verwenden, umfasst der Authentifizierungsflow die folgenden Komponenten:

1. Der **NAS/VPN-Server** empfängt Anforderungen von VPN-Clients und wandelt sie in RADIUS-Anforderungen an NPS-Server um.
2. Der **NPS-Server** stellt eine Verbindung mit Active Directory Domain Services (AD DS) her, um die primäre Authentifizierung für die RADIUS-Anforderungen durchzuführen, und übergibt die Anforderung bei Erfolg an eventuell installierte Erweiterungen.  
3. Die **NPS-Erweiterung** löst eine Anforderung der sekundären Authentifizierung bei Azure AD Multi-Factor Authentication aus. Sobald die Erweiterung die Antwort empfängt und die MFA-Abfrage erfolgreich ist, wird die Authentifizierungsanforderung abgeschlossen, indem dem NPS-Server Sicherheitstoken bereitgestellt werden, die einen von Azure STS ausgegebenen MFA-Anspruch enthalten.
4. **Azure AD MFA** kommuniziert mit Azure Active Directory (Azure AD), um die Informationen zum Benutzer abzurufen, und führt die sekundäre Authentifizierung mithilfe einer Überprüfungsmethode durch, die für den Benutzer konfiguriert wurde.

Das folgende Diagramm veranschaulicht den allgemeinen Ablauf dieser Authentifizierungsanforderung:

![Diagramm des Authentifizierungsflows für die Benutzerauthentifizierung über einen VPN-Server beim NPS-Server und bei der NPS-Erweiterung für Azure AD Multi-Factor Authentication](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>Verhalten des RADIUS-Protokolls und die NPS-Erweiterung

Da RADIUS ein UDP-Protokoll ist, nimmt der Absender Paketverluste an und wartet auf eine Antwort. Nach einer bestimmten Zeit kann ein Timeout für die Verbindung auftreten. In diesem Fall wird das Paket erneut gesendet, da der Absender annimmt, dass das Paket das Ziel nicht erreicht hat. Im Authentifizierungsszenario im vorliegenden Artikel senden VPN-Server die Anforderung und warten auf eine Antwort. Wenn ein Timeout für die Verbindung auftritt, sendet der VPN-Server die Anforderung erneut.

![Diagramm des RADIUS-UDP-Paketflows und der Anforderungen nach dem Timeout der Antwort des NPS-Servers](./media/howto-mfa-nps-extension/radius-flow.png)

Möglicherweise antwortet der NPS-Server nicht vor dem Auftreten des Timeouts für die Verbindung auf die ursprüngliche Anforderung des VPN-Servers, weil die MFA-Anforderung noch verarbeitet wird. Möglicherweise hat der Benutzer auf die MFA-Aufforderung nicht erfolgreich reagiert, und die NPS-Erweiterung für Azure AD Multi-Factor Authentication wartet darauf, dass dieses Ereignis abgeschlossen wird. In dieser Situation identifiziert der NPS-Server weitere Anforderungen des VPN-Servers als doppelte Anforderung. Der NPS-Server verwirft diese doppelten Anforderungen des VPN-Servers.

![Diagramm: NPS-Server verwirft doppelte Anforderungen des RADIUS-Servers](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

In den NPS-Serverprotokollen können Sie sehen, dass diese zusätzlichen Anforderungen verworfen werden. Dieses Verhalten ist beabsichtigt, um zu verhindern, dass Endbenutzer mehrere Anforderungen für einen einzigen Authentifizierungsversuch erhalten. Verworfene Anforderungen im Ereignisprotokoll des NPS-Servers bedeuten nicht, dass ein Problem mit dem NPS-Server oder der NPS-Erweiterung für Azure AD Multi-Factor Authentication vorliegt.

Um die Anzahl verworfener Anforderungen zu minimieren, empfiehlt es sich, VPN-Server mit einem Timeout von mindestens 60 Sekunden zu konfigurieren. Wenn es erforderlich ist oder Sie die Anzahl verworfener Anforderungen in den Ereignisprotokollen reduzieren möchten, können Sie den Timeoutwert für VPN-Server auf 90 oder 120 Sekunden erhöhen.

Aufgrund dieses Verhaltens des UDP-Protokolls könnte es passieren, dass der NPS-Server eine doppelte Anforderung erhält und eine weitere MFA-Aufforderung sendet, auch wenn der Benutzer bereits auf die ursprüngliche Anforderung reagiert hat. Um dies zu verhindern, fährt die NPS-Erweiterung für Azure AD Multi-Factor Authentication nach dem Senden einer Erfolgsmeldung an den VPN-Server bis zu 10 Sekunden lang damit fort, doppelte Anforderungen zu filtern und zu verwerfen.

![Diagramm des NPS-Servers, der nach einer erfolgreichen Antwort bis zu 10 Sekunden lang damit fortfährt, doppelte Anforderungen des VPN-Servers zu verwerfen](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Auch hier sehen Sie möglicherweise verworfene Anforderungen in den Ereignisprotokollen des NPS-Servers, selbst wenn die Aufforderung von Azure AD Multi-Factor Authentication erfolgreich war. Dieses Verhalten ist erwartet und weist nicht auf ein Problem mit dem NPS-Server oder der NPS-Erweiterung für Azure AD Multi-Factor Authentication hin.

## <a name="plan-your-deployment"></a>Planen der Bereitstellung

Die NPS-Erweiterung kümmert sich automatisch um die Redundanz, sodass keine spezielle Konfiguration erforderlich ist.

Sie können beliebig viele NPS-Server mit aktivierter Azure AD Multi-Factor Authentication erstellen. Wenn Sie mehrere Server installieren, sollten Sie für jeden ein anderes Clientzertifikat verwenden. Indem Sie für jeden Server ein eigenes Zertifikat erstellen, können Sie jedes Zertifikat einzeln aktualisieren und müssen sich keine Gedanken über Ausfallzeiten für Ihre einzelnen Server machen.

Weil VPN-Server Authentifizierungsanforderungen weiterleiten, müssen sie über die neuen NPS-Server mit aktivierter Azure AD Multi-Factor Authentication informiert werden.

## <a name="prerequisites"></a>Voraussetzungen

Die NPS-Erweiterung soll mit der vorhandenen Infrastruktur zusammenarbeiten. Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie beginnen.

### <a name="licenses"></a>Lizenzen

Die NPS-Erweiterung für Azure AD Multi-Factor Authentication steht Kunden mit [Lizenzen für Azure AD Multi-Factor Authentication](./concept-mfa-howitworks.md) zur Verfügung. Verbrauchsbasierte Lizenzen für Azure AD Multi-Factor Authentication, z. B. Lizenzen pro Benutzer oder pro Authentifizierung, sind mit der NPS-Erweiterung nicht kompatibel.

### <a name="software"></a>Software

Windows Server 2012 oder höher.

### <a name="libraries"></a>Bibliotheken

Sie müssen die folgende Bibliothek manuell installieren:

- [Visual C++ Redistributable für Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

Die folgenden Bibliotheken werden automatisch mit der Erweiterung installiert.

- [Visual C++ Redistributable Packages für Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory-Modul für Windows PowerShell, Version 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Das Microsoft Azure Active Directory-Modul für Windows PowerShell wird, sofern es nicht bereits vorhanden ist, über ein Konfigurationsskript installiert, das Sie im Rahmen des Setupvorgangs ausführen. Das Modul muss (falls es noch nicht installiert ist) nicht vorab installiert werden.

### <a name="azure-active-directory"></a>Azure Active Directory

Alle Benutzer der NPS-Erweiterung müssen mithilfe von Azure AD Connect mit Azure AD synchronisiert sein und für MFA registriert werden.

Wenn Sie die Erweiterung installieren, benötigen Sie die *Mandanten-ID* und die Administratoranmeldeinformationen für Ihren Azure AD-Mandanten. Führen Sie die folgenden Schritte aus, um die Mandanten-ID abzurufen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) als globaler Administrator des Azure-Mandanten an.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie es aus.
1. Auf der Seite **Übersicht** werden die *Mandanteninformationen* angezeigt. Wählen Sie neben der *Mandanten-ID* das Symbol **Kopieren** aus, wie im folgenden Beispielscreenshot gezeigt:

   ![Abrufen der Mandanten-ID aus dem Azure-Portal](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Netzwerkanforderungen

Der NPS-Server muss über die Ports 80 und 443 mit den folgenden URLs kommunizieren können:

* *https:\//adnotifications.windowsazure.com*
* *https:\//login.microsoftonline.com*
* *https:\//credentials.azure.com*

Darüber hinaus ist Konnektivität mit den folgenden URLs erforderlich, um die [Einrichtung des Adapters mit dem bereitgestellten PowerShell-Skript](#run-the-powershell-script) abzuschließen:

* *https:\//login.microsoftonline.com*
* *https:\//provisioningapi.microsoftonline.com*
* *https:\//aadcdn.msauth.net*

## <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

Vor der Installation der NPS-Erweiterung müssen Sie Ihre Umgebung für die Verarbeitung des Authentifizierungsdatenverkehrs vorbereiten.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Aktivieren der NPS-Rolle auf einem in die Domäne eingebundenen Server

Der NPS-Server stellt eine Verbindung mit Azure AD her und authentifiziert die MFA-Anforderungen. Wählen Sie einen Server für diese Rolle aus. Sie sollten einen Server auswählen, der keine Anforderungen von anderen Diensten verarbeitet, da die NPS-Erweiterung für alle Anforderungen Fehler auslöst, die keine RADIUS-Anforderungen sind. Der NPS-Server muss als primärer und sekundärer Authentifizierungsserver für Ihre Umgebung eingerichtet werden. Eine Proxyweiterleitung von RADIUS-Anforderungen an einen anderen Server ist nicht möglich.

1. Öffnen Sie auf Ihrem Server den **Server-Manager**. Wählen Sie aus dem Menü *Schnellstart* die Option **Assistent zum Hinzufügen von Rollen und Features** aus.
2. Wählen Sie als Installationstyp **Rollenbasierte oder featurebasierte Installation** aus.
3. Wählen Sie die Serverrolle **Netzwerkrichtlinien- und Zugriffsdienste** aus. Es wird möglicherweise ein Fenster angezeigt, in dem Sie über weitere erforderliche Features für die Ausführung dieser Rolle informiert werden.
4. Gehen Sie den Assistenten bis zur Seite *Bestätigung* durch. Klicken Sie auf **Installieren**, wenn Sie so weit sind.

Die Installation der NPS-Serverrolle kann einige Minuten dauern. Wenn die Installation abgeschlossen ist, fahren Sie mit den folgenden Abschnitten fort, um diesen Server für die Verarbeitung eingehender RADIUS-Anforderungen aus der VPN-Lösung zu konfigurieren.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurieren der VPN-Lösung für die Kommunikation mit dem NPS-Server

Die Schritte zum Konfigurieren der RADIUS-Authentifizierungsrichtlinie variieren in Abhängigkeit von der verwendeten VPN-Lösung. Konfigurieren Sie Ihre VPN-Richtlinie so, dass sie auf Ihren RADIUS-NPS-Server verweist.

### <a name="sync-domain-users-to-the-cloud"></a>Synchronisieren von Domänenbenutzern in der Cloud

Dieser Schritt wurde möglicherweise bereits auf Ihrem Mandanten durchgeführt. Es ist aber ratsam zu überprüfen, ob Azure AD Connect die Datenbanken kürzlich synchronisiert hat.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
2. Wählen Sie **Azure Active Directory** > **Azure AD Connect** aus.
3. Vergewissern Sie sich, dass der Synchronisierungsstatus **Aktiviert** lautet und dass die letzte Synchronisierung weniger als eine Stunde zurückliegt.

Wenn Sie einen neuen Synchronisierungslauf starten möchten, gehen Sie anhand der Anweisungen in [Azure AD Connect-Synchronisierung: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler) vor.

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Bestimmen Sie die Authentifizierungsmethoden, die Ihre Benutzer verwenden können

Zwei Faktoren haben Einfluss darauf, welche Authentifizierungsmethoden mit der Bereitstellung einer NPS-Erweiterung verfügbar sind:

* Der Kennwortverschlüsselungsalgorithmus wird zwischen dem RADIUS-Client (VPN, NetScaler-Server oder andere) und den NPS-Servern verwendet.
   - **PAP** unterstützt alle Authentifizierungsmethoden von Azure AD Multi-Factor Authentication in der Cloud: Telefonanruf, unidirektionale SMS, Benachrichtigung über eine mobile App, OATH-Hardwaretoken und Überprüfungscode in einer mobilen App.
   - **CHAPV2** und **EAP** unterstützt Telefonanruf und Benachrichtigung über eine mobile App.

    > [!NOTE]
    > Verwenden Sie bei der Bereitstellung der NPS-Erweiterung diese Faktoren, um auszuwerten, welche Methoden für Benutzer verfügbar sind. Wenn Ihr RADIUS-Client PAP unterstützt, der Client UX jedoch über kein Eingabefeld für einen Überprüfungscode verfügt, sind der Telefonanruf und die Benachrichtigung über eine mobile App die zwei unterstützten Optionen.
    >
    > Unabhängig vom verwendeten Authentifizierungsprotokoll (PAP, CHAP oder EAP) wird die Authentifizierung möglicherweise erfolgreich durchgeführt, wenn Ihre MFA-Methode textbasiert ist (SMS, Verifizierungscode in einer mobilen App oder OATH-Hardwaretoken) und erfordert, dass der Benutzer einen Code oder Text in das Eingabefeld der Benutzeroberfläche des VPN-Clients eingibt. *Allerdings* werden RADIUS-Attribute, die in der Richtlinie für den Netzwerkzugriff konfiguriert sind, *nicht* an den RADIUS-Client (also das Netzwerkzugriffsgerät, z. B. das VPN-Gateway) weitergeleitet. Folglich hat der VPN-Client möglicherweise mehr oder weniger Zugriff als gewünscht oder gar keinen Zugriff.

* Die Eingabemethoden, die von der Clientanwendung (VPN, NetScaler-Server oder andere) verarbeitet werden kann. Beispiel: Verfügt der VPN-Client über Mittel, die es dem Benutzer erlauben, einen Überprüfungscode aus einem Text oder einer mobilen App einzugeben?

Sie können [nicht unterstützte Authentifizierungsmethoden](howto-mfa-mfasettings.md#verification-methods) in Azure deaktivieren.

### <a name="register-users-for-mfa"></a>Registrieren von Benutzern für MFA

Bevor Sie die NPS-Erweiterung bereitstellen und verwenden können, müssen Benutzer, die Azure AD Multi-Factor Authentication durchführen müssen, für MFA registriert werden. Zum Testen der Erweiterung bei der Bereitstellung benötigen Sie außerdem mindestens ein Testkonto, das für Azure AD Multi-Factor Authentication vollständig registriert ist.

Wenn Sie ein Testkonto erstellen und konfigurieren müssen, gehen Sie folgendermaßen vor:

1. Melden Sie sich bei [https://aka.ms/mfasetup](https://aka.ms/mfasetup) mit einem Testkonto an.
2. Befolgen Sie die Anweisungen zum Einrichten einer Überprüfungsmethode.
3. Melden Sie sich im Azure-Portal als Administratorbenutzer an, und [erstellen Sie eine Richtlinie für bedingten Zugriff](howto-mfa-getstarted.md#create-conditional-access-policy), um für das Testkonto die mehrstufige Authentifizierung zu erzwingen.

> [!IMPORTANT]
>
> Stellen Sie sicher, dass Benutzer sich für Azure AD Multi-Factor Authentication erfolgreich registriert haben. Wenn sich Benutzer zuvor nur für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) registriert haben, wird für ihr Konto *StrongAuthenticationMethods* aktiviert. Azure AD Multi-Factor Authentication wird bei konfiguriertem *StrongAuthenticationMethods* selbst dann erzwungen, wenn sich der Benutzer nur für SSPR registriert hat.
>
> Eine kombinierte Sicherheitsregistrierung kann aktiviert werden, die SSPR und Azure AD Multi-Factor Authentication gleichzeitig konfiguriert. Weitere Informationen finden Sie unter [Aktivieren der kombinierten Registrierung von Sicherheitsinformationen in Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> Sie können auch [Benutzer zur erneuten Registrierung von Authentifizierungsmethoden](howto-mfa-userdevicesettings.md#manage-user-authentication-options) zwingen, wenn sie zuvor nur SSPR aktiviert hatten.

## <a name="install-the-nps-extension"></a>Installieren der NPS-Erweiterung

> [!IMPORTANT]
> Installieren Sie die NPS-Erweiterung auf einem anderen Server als dem VPN-Zugriffspunkt.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Herunterladen und Installieren der NPS-Erweiterung für Azure AD MFA

Führen Sie die folgenden Schritte aus, um die NPS-Erweiterung herunterzuladen und zu installieren:

1. [Laden Sie die NPS-Erweiterung aus dem Microsoft Download Center herunter](https://aka.ms/npsmfa).
1. Kopieren Sie die Binärdatei auf den Netzwerkrichtlinienserver, der konfiguriert werden soll.
1. Führen Sie *setup.exe* aus, und folgen Sie den Installationsanweisungen. Wenn Sie Fehler bemerken, stellen Sie sicher, dass die [Bibliotheken im Abschnitt „Voraussetzungen“](#libraries) erfolgreich installiert wurden.

#### <a name="upgrade-the-nps-extension"></a>Aktualisieren der NPS-Erweiterung

Wenn Sie zu einem späteren Zeitpunkt ein Upgrade für eine installierte NPS-Erweiterung ausführen möchten, gehen Sie folgendermaßen vor, um einen Neustart des zugrunde liegenden Servers zu vermeiden:

1. Deinstallieren Sie die vorhandene Version.
1. Führen Sie das neue Installationsprogramm aus.
1. Starten Sie den Dienst *Network Policy Server (IAS)* neu.

### <a name="run-the-powershell-script"></a>Ausführen des PowerShell-Skripts

Das Installationsprogramm erstellt unter `C:\Program Files\Microsoft\AzureMfa\Config` ein PowerShell-Skript (wobei `C:\` Ihr Installationslaufwerk ist). Dieses PowerShell-Skript führt bei jeder Ausführung folgende Aktionen aus:

* Erstellen eines selbstsignierten Zertifikats
* Zuordnen des öffentlichen Schlüssels des Zertifikats zum Dienstprinzipal in Azure AD
* Speichern des Zertifikats im Zertifikatspeicher des lokalen Computers
* Gewähren des Zugriffs auf den privaten Schlüssel des Zertifikats für den Netzwerkbenutzer
* Neustarten des NPS-Diensts

Sofern Sie nicht Ihre eigenen Zertifikate nutzen möchten (anstelle der vom PowerShell-Skript generierten selbstsignierten Zertifikate), führen Sie das PowerShell-Skript aus, um die Installation der NPS-Erweiterung abzuschließen. Wenn Sie die Erweiterung auf mehreren Servern installieren möchten, sollte jeder Server über ein eigenes Zertifikat verfügen.

Um Lastenausgleichsfunktionen oder Redundanz bereitzustellen, wiederholen Sie diese Schritte auf weiteren NPS-Servern:

1. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung als Administrator.
1. Wechseln Sie zu dem Verzeichnis, in dem das Installationsprogramm das PowerShell-Skript erstellt hat:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Führen Sie das PowerShell-Skript aus, das vom Installationsprogramm erstellt wurde.

   > [!IMPORTANT]
   > Für Kunden, die die Azure Government- oder Azure China 21ViaNet-Cloud verwenden, bearbeiten Sie zunächst die `Connect-MsolService`-Cmdlets im Skript *AzureMfaNpsExtnConfigSetup.ps1* so, dass die *AzureEnvironment*-Parameter für die betreffende Cloud enthalten sind. Geben Sie z. B. *-AzureEnvironment USGovernment* oder *-AzureEnvironment AzureChinaCloud* an.
   >
   > Weitere Informationen finden Sie in der [Referenz der Connect-MsolService-Parameter](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Wenn Sie dazu aufgefordert werden, melden Sie sich bei Azure AD als Administrator an.
1. PowerShell fordert Sie zur Angabe Ihrer Mandanten-ID auf. Verwenden Sie die *Mandanten-ID*-GUID, die Sie im Abschnitt „Voraussetzungen“ aus dem Azure-Portal kopiert haben.
1. Es wird eine Erfolgsmeldung angezeigt, wenn das Skript vollständig ausgeführt wurde.  

Wenn das vorherige Computerzertifikat abgelaufen ist und ein neues Zertifikat generiert wurde, sollten Sie alle abgelaufenen Zertifikate löschen. Bei abgelaufenen Zertifikaten können beim Start der NPS-Erweiterung Probleme auftreten.

> [!NOTE]
> Wenn Sie Ihre eigenen Zertifikate verwenden, statt diese mit dem PowerShell-Skript zu generieren, stellen Sie sicher, dass sie den NPS-Namenskonventionen entsprechen. Der Antragstellernamen muss **CN=\<TenantID\>,OU=Microsoft NPS Extension** lauten.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Zusätzliche Schritte für Microsoft Azure Government oder Azure China 21ViaNet

Für Kunden, die die Azure Government- oder Azure China 21Vianet-Cloud verwenden, sind auf jedem NPS-Server die folgenden zusätzlichen Konfigurationsschritte erforderlich.

> [!IMPORTANT]
> Konfigurieren Sie diese Registrierungseinstellungen nur, wenn Sie Azure Government- oder Azure China 21Vianet-Kunde sind.

1. Wenn Sie Azure Government- oder Azure China 21Vianet-Kunde sind, öffnen Sie den **Registrierungs-Editor** auf dem NPS-Server.
1. Navigieren Sie zu `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. Legen Sie für Azure Government Kunden die folgenden Schlüsselwerte fest:

    | Registrierungsschlüssel       | Wert |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Legen Sie für Azure China 21ViaNet-Kunden die folgenden Schlüsselwerte fest:

    | Registrierungsschlüssel       | Wert |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Wiederholen Sie die beiden vorherigen Schritte, um für jeden NPS-Server die Registrierungsschlüsselwerte festzulegen.
1. Starten Sie auf jedem NPS-Server den NPS-Dienst erneut.

    Nehmen Sie für minimale Auswirkung jeden NPS-Server einzeln aus der NLB-Rotation heraus, und warten Sie, bis alle Verbindungen beendet sind.

### <a name="certificate-rollover"></a>Zertifikatrollover

Ab Release *1.0.1.32* der NPS-Erweiterung wird das Lesen von mehreren Zertifikaten unterstützt. Diese Funktion vereinfacht das Aktualisieren von Zertifikaten vor deren Ablauf. Wenn in Ihrer Organisation eine frühere Version der NPS-Erweiterung ausgeführt wird, führen Sie ein Upgrade auf Version *1.0.1.32* oder höher durch.

Vom `AzureMfaNpsExtnConfigSetup.ps1`-Skript erstellte Zertifikate sind 2 Jahre gültig. Überwachen Sie die Zertifikate auf den Ablauf. Zertifikate für die NPS-Erweiterung werden im Zertifikatspeicher *Lokaler Computer* unter *Persönlich* gespeichert und für die Mandanten-ID *ausgestellt*, die für das Installationsskript bereitgestellt wurde.

Wenn sich das Ablaufdatum eines Zertifikats nähert, sollten Sie ein neues Zertifikat erstellen, um es zu ersetzen.  Dies können Sie durch erneutes Ausführen von `AzureMfaNpsExtnConfigSetup.ps1` erreichen. Behalten Sie dazu die Mandanten-ID bei, wenn Sie dazu aufgefordert werden. Dieser Vorgang sollte auf jedem NPS-Server in Ihrer Umgebung wiederholt werden.

## <a name="configure-your-nps-extension"></a>Konfigurieren der NPS-Erweiterung

Ihre Umgebung ist vorbereitet, und die NPS-Erweiterung wurde auf den erforderlichen Servern installiert. Nun können Sie die Erweiterung konfigurieren.

Dieser Abschnitt enthält Überlegungen zum Entwurf und Vorschläge für erfolgreiche Bereitstellungen der NPS-Erweiterung.

### <a name="configuration-limitations"></a>Einschränkungen der Konfiguration

- Die NPS-Erweiterung für Azure AD Multi-Factor Authentication enthält keine Tools zum Migrieren von Benutzern und Einstellungen vom MFA-Server in die Cloud. Aus diesem Grund wird die Verwendung der Erweiterung für neue Bereitstellungen statt vorhandener Bereitstellung empfohlen. Wenn Sie die Erweiterung für eine vorhandene Bereitstellung verwenden, müssen die Benutzer die Bestätigung erneut ausführen, um ihre MFA-Details in der Cloud anzugeben.  
- Die NPS-Erweiterung verwendet den Benutzerprinzipalnamen (User Principal Name, UPN) aus der lokalen AD DS-Umgebung, um den Benutzer in Azure AD Multi-Factor Authentication zu identifizieren und die sekundäre Authentifizierung durchzuführen. Die Erweiterung kann für die Verwendung eines anderen Bezeichners konfiguriert werden, z. B. einer alternativen Anmelde-ID oder eines benutzerdefinierten AD DS-Felds, bei dem es sich nicht um den UPN handelt. Weitere Informationen finden Sie im Artikel [Erweiterte Konfigurationsoptionen für die NPS-Erweiterung für Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Nicht alle Verschlüsselungsprotokolle unterstützen alle Überprüfungsmethoden.
   - **PAP** unterstützt Telefonanruf, unidirektionale Textnachricht, Benachrichtigung über eine mobile App und Überprüfungscode in der mobilen App
   - **CHAPV2** und **EAP** unterstützt Telefonanruf und Benachrichtigung über eine mobile App

### <a name="control-radius-clients-that-require-mfa"></a>Steuern von RADIUS-Clients, die MFA erfordern

Nachdem Sie MFA über die NPS-Erweiterung für einen RADIUS-Client aktiviert haben, muss bei allen Authentifizierungen für diesen Client MFA erfolgen. Wenn Sie MFA nur für einige RADIUS-Clients aktivieren möchten, für andere dagegen nicht, können Sie zwei NPS-Server konfigurieren und die Erweiterung nur auf einem installieren.

Verweisen Sie RADIUS-Clients, die MFA zum Senden von Anforderungen auffordern sollen, an den mit der Erweiterung konfigurierten NPS-Server und andere RADIUS-Clients an den NPS-Server, der nicht mit der Erweiterung konfiguriert ist.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Vorbereiten von Benutzern, die nicht für MFA registriert sind

Wenn es Benutzer gibt, die nicht für MFA registriert sind, können Sie bestimmen, was geschieht, wenn sie versuchen, sie zu authentifizieren. Um dieses Verhalten zu steuern, verwenden Sie die Einstellung *REQUIRE_USER_MATCH* im Registrierungspfad *HKLM\Software\Microsoft\AzureMFA*. Diese Einstellung hat eine einzelne Konfigurationsoption:

| Schlüssel | Wert | Standard |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Nicht festgelegt (gleichwertig mit TRUE) |

Diese Einstellung bestimmt, was passieren soll, wenn ein Benutzer nicht für MFA registriert ist. Wenn der Schlüssel nicht vorhanden, nicht festgelegt oder auf *TRUE* festgelegt ist und der Benutzer nicht registriert ist, betrachtet die Erweiterung die MFA-Abfrage als ungültig.

Wenn der Schlüssel auf *FALSE* festgelegt und der Benutzer nicht registriert ist, wird die Authentifizierung ohne MFA fortgesetzt. Wenn ein Benutzer für MFA registriert ist, muss er sich mit MFA authentifizieren, auch wenn *REQUIRE_USER_MATCH* auf *FALSE* festgelegt ist.

Sie können diesen Schlüssel erstellen und auf *FALSE* festlegen, während Ihre Benutzer das Onboarding durchlaufen und möglicherweise noch nicht alle für Azure AD Multi-Factor Authentication registriert wurden. Da das Festlegen des Schlüssels Benutzern, die nicht für MFA registriert sind, die Anmeldung erlaubt, müssen Sie diesen Schlüssel vor dem Wechsel in die Produktionsumgebung entfernen.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="nps-extension-health-check-script"></a>Skript für die Integritätsprüfung der NPS-Erweiterung

Zum Ausführen der Schritte der grundlegenden Integritätsprüfung bei der Problembehandlung der NPS-Erweiterung steht das folgende Skript zur Verfügung.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Wie überprüfe ich, ob das Clientzertifikat wie erwartet installiert ist?

Suchen Sie das vom Installationsprogramm erstellte selbstsignierte Zertifikat im Zertifikatspeicher, und prüfen Sie, ob der private Schlüssel dem Benutzer *NETZWERKDIENST* Berechtigungen erteilt. Das Zertifikat hat den Antragstellernamen **CN \<tenantid\>, OU = Microsoft NPS Extension**.

Selbstsignierte Zertifikate, die durch das Skript `AzureMfaNpsExtnConfigSetup.ps1` generiert werden, haben eine Gültigkeitsdauer von zwei Jahren. Wenn Sie überprüfen, ob das Zertifikat installiert ist, sollten Sie auch überprüfen, ob es nicht abgelaufen ist.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Wie kann ich überprüfen, ob mein Clientzertifikat meinem Mandanten in Azure AD zugeordnet ist?

Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie die folgenden Befehle aus:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Diese Befehle geben alle Zertifikate aus, die Ihren Mandanten Ihrer Instanz der NPS-Erweiterung in der PowerShell-Sitzung zuordnen. Suchen Sie nach dem Zertifikat, indem Sie Ihr Clientzertifikat als *Base64-codierte X.509-Datei (CER)* ohne den privaten Schlüssel exportieren und es mit der Liste aus PowerShell vergleichen.

Der folgende Befehl erstellt eine Datei namens *npscertificate* im *CER-Format* im Stammverzeichnis Ihres Laufwerks *C:* .

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Wenn Sie diesen Befehl ausgeführt haben, wechseln Sie zum Stammverzeichnis Ihres Laufwerks *C:* , suchen Sie die Datei, und doppelklicken Sie darauf. Öffnen Sie die Details, und scrollen Sie nach unten zu „Fingerabdruck“. Vergleichen Sie den Fingerabdruck des auf dem Server installierten Zertifikats mit diesem Fingerabdruck. Der Zertifikatfingerabdruck muss übereinstimmen.

Mithilfe der Zeitstempel *Valid-From* und *Valid-Until*, die in einem visuell lesbaren Format vorliegen, können Sie offensichtliche Fehler herausfiltern, wenn der Befehl mehrere Zertifikate zurückgibt.

### <a name="why-cannot-i-sign-in"></a>Warum kann ich mich nicht anmelden?

Überprüfen Sie, ob Ihr Kennwort nicht abgelaufen ist. Die NPS-Erweiterung unterstützt nicht das Ändern von Kennwörtern im Rahmen des Anmeldeworkflows. Wenden Sie sich an das IT-Team Ihrer Organisation, um weitere Unterstützung zu erhalten.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Warum misslingen meine Anforderungen mit ADAL-Tokenfehler?

Dieser Fehler kann verschiedene Gründe haben. Gehen Sie folgendermaßen vor, um das Problem zu beheben:

1. Starten Sie den NPS-Server neu.
2. Vergewissern Sie sich, dass das Clientzertifikat wie erwartet installiert wurde.
3. Überprüfen Sie, ob das Zertifikat Ihrem Mandanten in Azure AD zugeordnet ist.
4. Überprüfen Sie auf dem Server, auf dem die Erweiterung ausgeführt wird, ob auf `https://login.microsoftonline.com/` zugegriffen werden kann.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Warum misslingt die Authentifizierung mit einem Fehler in den HTTP-Protokollen, der besagt, dass der Benutzer nicht gefunden wurde?

Überprüfen Sie, ob AD Connect ausgeführt wird und der Benutzer sowohl in der lokalen AD DS-Umgebung als auch in Azure AD vorhanden ist.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Warum enthalten meine Protokolle HTTP-Verbindungs- und Authentifizierungsfehler?

Überprüfen Sie auf dem Server, auf dem die NPS-Erweiterung ausgeführt wird, ob auf https://adnotifications.windowsazure.com zugegriffen werden kann.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Warum funktioniert die Authentifizierung trotz eines gültigen Zertifikats nicht?

Wenn das vorherige Computerzertifikat abgelaufen ist und ein neues Zertifikat generiert wurde, löschen Sie alle abgelaufenen Zertifikate. Abgelaufene Zertifikate können beim Start der NPS-Erweiterung Probleme verursachen.

Um festzustellen, ob ein gültiges Zertifikat vorhanden ist, überprüfen Sie über MMC den *Zertifikatspeicher des lokalen Computerkontos*, und stellen Sie sicher, dass das Ablaufdatum des Zertifikats nicht überschritten wurde. Zum Generieren eines neuen gültigen Zertifikats führen Sie die Schritte im Abschnitt [Ausführen des PowerShell-Installationsskripts](#run-the-powershell-script) erneut aus.

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Warum werden mir verworfene Anforderungen in den NPS-Serverprotokollen angezeigt?

Möglicherweise sendet ein VPN-Server wiederholte Anforderungen an den NPS-Server, wenn der Timeoutwert zu niedrig festgelegt wurde. Der NPS-Server erkennt diese doppelten Anforderungen und verwirft sie. Dieses Verhalten ist beabsichtigt und weist nicht auf ein Problem mit dem NPS-Server oder der NPS-Erweiterung für Azure AD Multi-Factor Authentication hin.

Weitere Informationen dazu, warum verworfene Pakete in den NPS-Serverprotokollen angezeigt werden, finden Sie weiter oben in diesem Artikel unter [Verhalten des RADIUS-Protokolls und die NPS-Erweiterung](#radius-protocol-behavior-and-the-nps-extension).

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Verwalten von TLS/SSL-Protokollen und Verschlüsselungssammlungen

Es empfiehlt sich, ältere und weniger leistungsstarke Verschlüsselungssammlungen zu deaktivieren oder zu entfernen, sofern diese nicht von Ihrer Organisation benötigt werden. Informationen zur Durchführung dieser Aufgabe finden Sie im Artikel [Verwalten von SSL/TLS-Protokollen und Verschlüsselungssammlungen für AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

### <a name="additional-troubleshooting"></a>Weitere Informationen zur Problembehandlung

Zusätzliche Anleitungen zur Problembehandlung und mögliche Lösungen finden Sie im Artikel [Auflösen von Fehlermeldungen in der NPS-Erweiterung für Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht und Konfiguration des Netzwerkrichtlinienservers unter Windows Server](/windows-server/networking/technologies/nps/nps-top)

- Konfigurieren Sie alternative IDs für die Anmeldung, oder richten Sie unter [Erweiterte Konfigurationsoptionen für die NPS-Erweiterung für Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md) eine Ausnahmeliste für IP-Adressen ein, welche die zweistufige Überprüfung nicht ausführen müssen.

- Erfahren Sie, wie Sie [Remotedesktopgateway](howto-mfa-nps-extension-rdg.md) und [VPN-Server](howto-mfa-nps-extension-vpn.md) mithilfe der NPS-Erweiterung integrieren.

- [Auflösen von Fehlermeldungen in der NPS-Erweiterung für Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)