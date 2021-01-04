---
title: Bereitstellen des lokalen Azure AD-Kennwortschutzes
description: Erfahren Sie, wie Sie den Azure AD-Kennwortschutz in einer lokalen Active Directory Domain Services-Umgebung planen und bereitstellen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: deb1f74902fe28d53a5180e4f341547f339a83ac
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741983"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planen und Bereitstellen des lokalen Azure AD-Kennwortschutzes

Benutzer erstellen häufig Kennwörter, in denen gängige Wörter mit regionalem Bezug verwendet werden, z. B. eine Schule, eine Sportmannschaft oder eine prominente Person. Diese Kennwörter sind leicht zu erraten und halten wörterbuchbasierten Angriffen oft nicht stand. Zum Erzwingen von sicheren Kennwörtern in Ihrer Organisation bietet der Azure Active Directory-Kennwortschutz (Azure AD) eine Liste mit global und benutzerdefiniert gesperrten Kennwörtern. Die Anforderung einer Kennwortänderung ist nicht erfolgreich, wenn sich eine Übereinstimmung mit dieser Liste mit gesperrten Kennwörtern ergibt.

Um Ihre lokale Active Directory Domain Services-Umgebung (AD DS) zu schützen, können Sie den Azure AD-Kennwortschutz installieren und konfigurieren, sodass Ihr lokaler DC verwendet wird. In diesem Artikel erfahren Sie, wie Sie den Azure AD-Kennwortschutz-Proxydienst und den Azure AD-Kennwortschutz-DC-Agent in Ihrer lokalen Umgebung installieren und registrieren.

Weitere Informationen zur Funktionsweise des Azure AD-Kennwortschutzes in einer lokalen Umgebung finden Sie unter [Erzwingen des Azure AD-Kennwortschutzes für Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Bereitstellungsstrategie

Die folgende Abbildung zeigt, wie die grundlegenden Komponenten des Azure AD-Kennwortschutzes in einer lokalen Active Directory-Umgebung zusammenarbeiten:

![Zusammenspiel der Komponenten des Azure AD-Kennwortschutzes](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Es empfiehlt sich, sich vor der Bereitstellung mit der Funktionsweise der Software vertraut zu machen. Weitere Informationen finden Sie unter [Konzeptionelle Übersicht über den Azure AD-Kennwortschutz](concept-password-ban-bad-on-premises.md).

Es wird empfohlen, Bereitstellungen im *Überwachungsmodus* zu starten. Der Überwachungsmodus ist die anfängliche Standardeinstellung, bei der Kennwörter weiterhin festgelegt werden können. Kennwörter, die gesperrt würden, werden im Ereignisprotokoll aufgezeichnet. Nachdem Sie die Proxyserver und DC-Agents im Überwachungsmodus bereitgestellt haben, sollten Sie die Auswirkungen überwachen, die die Kennwortrichtlinie auf Benutzer hat, wenn sie durchgesetzt wird.

Während der Überwachungsphase stellen viele Organisationen fest, dass die folgenden Situationen eintreten:

* Sie müssen bestehende Betriebsprozesse verbessern, um sicherere Kennwörter zu verwenden.
* Benutzer verwenden häufig unsichere Kennwörter.
* Sie müssen Benutzer über bevorstehende Änderungen der Sicherheitsmaßnahmen und deren mögliche Auswirkungen auf sie informieren und aufklären, wie sicherere Kennwörter ausgewählt werden können.

Es ist auch möglich, dass eine stärkere Kennwortüberprüfung die vorhandene Active Directory-Domänencontroller-Bereitstellungsautomatisierung beeinträchtigt. Es wird empfohlen, während der Auswertung des Überwachungszeitraums mindestens eine DC-Heraufstufung und eine DC-Herabstufung durchzuführen, um solche Probleme erkennen zu können. Weitere Informationen finden Sie in den folgenden Artikeln:

* [„Ntdsutil.exe“ kann kein unsicheres DSRM-Kennwort festlegen](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Fehler beim Höherstufen des Domänencontrollerreplikats aufgrund eines unsicheren DSRM-Kennworts](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Fehler beim Herabstufen des Domänencontrollers aufgrund eines unsicheren lokalen Administratorkennworts](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Nachdem das Feature für einen angemessenen Zeitraum im Überwachungsmodus ausgeführt wurde, können Sie die Konfiguration von *Überwachen* auf *Erzwingen* umstellen, um sicherere Kennwörter zu verlangen. Eine zusätzliche Überwachung während dieser Zeit wird empfohlen.

Beachten Sie unbedingt, dass der Azure AD-Kennwortschutz Kennwörter nur bei der Änderung oder Festlegung überprüfen kann. Kennwörter, die vor der Bereitstellung des Azure AD-Kennwortschutzes in Active Directory akzeptiert und gespeichert wurden, werden niemals überprüft und funktionieren unverändert. Im Lauf der Zeit werden alle Benutzer und Konten auf die Verwendung von Kennwörtern umgestellt, die vom Azure AD-Kennwortschutz überprüft wurden, da die bestehenden Kennwörter normal ablaufen. Konten, die mit „Kennwort läuft nie ab“ konfiguriert wurden, sind davon ausgenommen.

### <a name="multiple-forest-considerations"></a>Überlegungen zur Gesamtstruktur

Für die Bereitstellung des Azure AD-Kennwortschutzes in mehreren Gesamtstrukturen gibt es keine zusätzlichen Anforderungen.

Jede Gesamtstruktur ist unabhängig konfiguriert, wie im folgenden Abschnitt zum [Bereitstellen eines lokalen Azure AD-Kennwortschutzes](#download-required-software) beschrieben. Jeder Azure AD-Kennwortschutzproxy kann nur Domänencontroller aus der Gesamtstruktur unterstützen, mit der er verknüpft ist.

Unabhängig von der in Active Directory konfigurierten Vertrauensstellung besitzt die Azure AD-Kennwortschutzsoftware in einer bestimmten Gesamtstruktur keine Informationen zu einer Azure AD-Kennwortschutzsoftware, die in einer anderen Gesamtstruktur bereitgestellt ist.

### <a name="read-only-domain-controller-considerations"></a>Überlegungen zu Domänencontrollern ohne Schreibzugriff

Ereignisse durch Änderungen oder Festlegungen von Kennwörtern werden auf schreibgeschützten Domänencontrollern (RODCs) nicht verarbeitet und gespeichert. Sie werden stattdessen an nicht schreibgeschützte Domänencontroller weitergeleitet. Sie müssen die DC-Agent-Software für den Azure AD-Kennwortschutz nicht auf den RODCs installieren.

Darüber hinaus wird das Ausführen des Proxydiensts für den Azure AD-Kennwortschutz auf einem schreibgeschützten Domänencontroller nicht unterstützt.

### <a name="high-availability-considerations"></a>Überlegungen zu Hochverfügbarkeit

Der wichtigste Aspekt beim Kennwortschutz ist die Verfügbarkeit der Proxyserver für den Azure AD-Kennwortschutz, wenn Domänencontroller in einer Gesamtstruktur versuchen, neue Richtlinien oder andere Daten aus Azure herunterzuladen. Jeder DC-Agent für den Azure AD-Kennwortschutz verwendet bei der Entscheidung, welcher Proxyserver aufgerufen werden soll, einen einfachen Roundrobinalgorithmus. Der Agent überspringt Proxyserver, die nicht antworten.

Für die meisten vollständig verbundenen Active Directory-Bereitstellungen, die eine fehlerfreie Replikation des Verzeichnisses und SYSVOL-Ordnerstatus aufweisen, reichen zwei Azure AD-Kennwortschutz-Proxyserver aus, um die Verfügbarkeit sicherzustellen. Diese Konfiguration führt zum rechtzeitigen Download neuer Richtlinien und anderer Daten. Wenn gewünscht, können Sie zusätzliche Azure AD-Kennwortschutz-Proxyserver bereitstellen.

Die üblichen Probleme, die mit Hochverfügbarkeit einhergehen, werden durch den Entwurf der DC-Agent-Software für den Azure AD-Kennwortschutz verringert. Der DC-Agent für den Azure AD-Kennwortschutz verwaltet einen lokalen Cache, in dem sich die zuletzt heruntergeladene Kennwortrichtlinie befindet. Selbst wenn alle registrierten Proxyserver nicht mehr verfügbar sind, setzen die Azure AD-Kennwortschutz-DC-Agents ihre zwischengespeicherten Kennwortrichtlinien durch.

Eine angemessene Aktualisierungshäufigkeit für Kennwortrichtlinien in einer umfangreichen Bereitstellung liegt für gewöhnlich in der Größenordnung von Tagen, nicht Stunden oder weniger. Kurze Ausfälle der Proxyserver haben daher keinen wesentlichen Einfluss auf den Azure AD-Kennwortschutz.

## <a name="deployment-requirements"></a>Anforderungen für die Bereitstellung

Weitere Informationen zur Lizenzierung finden Sie unter [Azure AD-Kennwortschutz – Lizenzanforderungen](concept-password-ban-bad.md#license-requirements).

Es gelten die folgenden zentralen Anforderungen:

* Alle Computer (auch Domänencontroller), auf denen Azure AD-Kennwortschutzkomponenten installiert sind, müssen über eine Installation der Universal C-Runtime verfügen.
    * Sie können die Runtime abrufen, indem Sie sicherstellen, dass Sie über alle Updates von Windows Update verfügen. Sie können sie auch in einem betriebssystemspezifischen Updatepaket abrufen. Weitere Informationen finden Sie unter [Update für die Universal C-Runtime in Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Sie benötigen ein Konto mit Active Directory-Domänenadministratorrechten in der Stammdomäne der Gesamtstruktur, um die Windows Server Active Directory-Gesamtstruktur bei Azure AD zu registrieren.
* Den Schlüsselverteilungsdienst muss auf allen Domänencontrollern in der Domäne aktiviert sein, auf denen Windows Server 2012 ausgeführt wird. Standardmäßig wird dieser Dienst über das Starten eines manuellen Triggers aktiviert.
* Netzwerkkonnektivität muss zwischen mindestens einem Domänencontroller in jeder Domäne und mindestens einem Server bestehen, der den Proxydienst für den Azure AD-Kennwortschutz hostet. Diese Konnektivität muss es dem Domänencontroller gestatten, auf den RPC-Endpunktzuordnungsport 135 und den RPC-Serverport für den Proxydienst zuzugreifen.
    * Der RPC-Serverport ist standardmäßig ein dynamischer RPC-Port. Er kann jedoch so konfiguriert werden, dass er einen [statischen Port verwendet](#static).
* Alle Computer, auf denen der Proxydienst für den AD-Kennwortschutz installiert werden soll, müssen Netzwerkzugriff auf die folgenden Endpunkte besitzen:

    |**Endpunkt**|**Zweck**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Authentifizierungsanforderungen|
    |`https://enterpriseregistration.windows.net`|Funktion für Azure AD-Kennwortschutz|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD-Kennwortschutz-DC-Agent

Die folgenden Anforderungen gelten für den Azure AD-Kennwortschutz-DC-Agent:

* Alle Computer, auf denen die DC Agent-Software für den Azure AD-Kennwortschutz installiert werden soll, müssen Windows Server 2012 oder höher ausführen.
    * Die Active Directory-Domäne oder -Gesamtstruktur muss sich nicht ebenfalls in einer Windows Server 2012-Domänenfunktionsebene oder der Funktionsebene der Gesamtstruktur befinden. Wie unter [Entwurfsprinzipien](concept-password-ban-bad-on-premises.md#design-principles) ausgeführt, gibt es für das Ausführen des DC-Agents oder der Proxysoftware keine Mindestanforderungen an die Domänenfunktionsebene (DFL) oder die Funktionsebene der Gesamtstruktur (FFL).
* Auf allen Computern, auf denen der Azure AD-Kennwortschutz-DC-Agent ausgeführt wird, muss .NET 4.5 installiert sein.
* Alle Active Directory-Domänen, die den DC-Agent-Dienst für den Azure AD-Kennwortschutz ausführen, müssen DFSR (Distributed File System Replication) für die SYSVOL-Replikation verwenden.
   * Sollte DFSR von Ihrer Domäne noch nicht verwendet werden, müssen Sie vor der Installation des Azure AD-Kennwortschutzes eine Migration durchführen. Weitere Informationen finden Sie im [Migrationshandbuch für die SYSVOL-Replikation: Replikation von FRS zu DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Die DC-Agent-Software für den Azure AD-Kennwortschutz wird derzeit auf Domänencontrollern in Domänen installiert, von denen noch FRS (die Vorgängertechnologie zu DFSR) für die SYSVOL-Replikation verwendet wird. Die Software funktioniert in dieser Umgebung allerdings NICHT ordnungsgemäß.
    >
    > Dies macht sich unter anderem durch nicht erfolgreich replizierte Einzeldateien sowie durch scheinbar erfolgreiche SYSVOL-Wiederherstellungsprozeduren bemerkbar, bei denen jedoch nicht alle Dateien repliziert werden.
    >
    > Migrieren Sie daher die Domäne baldmöglichst für die Verwendung von DFSR, um von den DFSR-Vorteilen zu profitieren und die Blockierung der Bereitstellung des Azure AD-Kennwortschutzes aufzuheben. Zukünftige Versionen der Software werden automatisch deaktiviert, wenn sie in einer Domäne ausgeführt werden, die noch FRS verwendet.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD-Kennwortschutz-Proxydienst

Die folgenden Anforderungen gelten für den Azure AD-Kennwortschutz-Proxydienst:

* Alle Computer, auf denen der Azure AD-Kennwortschutz-Proxydienst installiert werden soll, müssen Windows Server 2012 R2 oder höher ausführen.

    > [!NOTE]
    > Die Bereitstellung des Azure AD-Kennwortschutz-Proxydiensts ist eine obligatorische Voraussetzung für das Bereitstellen des Azure AD-Kennwortschutzes, auch wenn der Domänencontroller möglicherweise über eine direkte ausgehende Internetverbindung verfügt.

* Auf allen Computern, auf denen der Azure AD-Kennwortschutz-Proxydienst installiert werden soll, muss .NET 4.7 installiert sein.
    * .NET 4.7 sollte bereits auf einem vollständig aktualisierten Windows-Server installiert sein. Laden Sie bei Bedarf das Installationsprogramm unter [.NET Framework 4.7-Offlineinstallationsprogramm für Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows) herunter, und führen Sie es aus.
* Alle Computer, auf denen der Proxydienst für den Azure AD-Kennwortschutz gehostet wird, müssen so konfiguriert werden, dass Domänencontrollern die Anmeldung beim Proxydienst ermöglicht wird. Dies wird über die Zuweisung der Berechtigung „Auf diesen Computer vom Netzwerk aus zugreifen“ gesteuert.
* Alle Computer, die den Proxydienst für den Azure AD-Kennwortschutz hosten, müssen so konfiguriert sein, dass sie ausgehenden HTTP-Datenverkehr mit TLS 1.2 zulassen.
* Ein Konto vom Typ *Globaler Administrator* oder *Sicherheitsadministrator* zum Registrieren des Azure AD-Kennwortschutz-Proxydiensts und der Gesamtstruktur bei Azure AD.
* Außerdem müssen Sie Netzwerkzugriff für die Ports und URLs aktivieren, die im Artikel [Setupprozeduren für die Anwendungsproxyumgebung](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) angegeben sind.

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Voraussetzungen für Microsoft Azure AD Connect Agent Updater

Der Microsoft Azure AD Connect Agent Updater-Dienst wird zusammen mit dem Azure AD-Kennwortschutz-Proxydienst installiert. Damit der Microsoft Azure AD Connect Agent Updater-Dienst funktionieren kann, ist eine zusätzliche Konfiguration erforderlich:

* Wenn in Ihrer Umgebung ein HTTP-Proxyserver verwendet wird, befolgen Sie die im Artikel [Verwenden von vorhandenen lokalen Proxyservern](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) angegebenen Richtlinien.
* Der Microsoft Azure AD Connect Agent Updater-Dienst erfordert auch die TLS 1.2-Schritte, die in den [TLS-Anforderungen](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements) angegeben werden.

> [!WARNING]
> Für den Azure AD-Kennwortschutz-Proxy und den Azure AD-Anwendungsproxy werden unterschiedliche Versionen des Microsoft Azure AD Connect Agent Updater-Diensts installiert. Daher beziehen sich die Anweisungen auf Inhalte für den Anwendungsproxy. Diese verschiedenen Versionen sind nicht kompatibel, wenn sie parallel installiert sind. Eine solche parallele Installation verhindert, dass der Agent Updater-Dienst Azure für Softwareupdates kontaktiert. Sie sollten also nie den Azure AD-Kennwortschutzproxy und den Azure AD-Anwendungsproxy auf demselben Computer installieren.

## <a name="download-required-software"></a>Herunterladen der erforderlichen Software

Es gibt zwei erforderliche Installationsprogramme für lokale Bereitstellungen des Azure AD-Kennwortschutzes:

* Azure AD-Kennwortschutz-DC-Agent (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD-Kennwortschutz-Proxy (*AzureADPasswordProtectionProxySetup.exe*)

Laden Sie beide Installationsprogramme vom [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071) herunter.

## <a name="install-and-configure-the-proxy-service"></a>Installieren und Konfigurieren des Proxydiensts

Der Azure AD-Kennwortschutz-Proxydienst befindet sich in der Regel auf einem Mitgliedsserver in Ihrer lokalen AD DS-Umgebung. Nach der Installation kommuniziert der Azure AD-Kennwortschutz-Proxydienst mit Azure AD, um eine Kopie der Listen der global und vom Kunden gesperrten Kennwörter für Ihren Azure AD-Mandanten zu verwalten.

Im nächsten Abschnitt installieren Sie die Azure AD-Kennwortschutz-DC-Agents auf Domänencontrollern in Ihrer lokalen AD DS-Umgebung. Diese DC-Agents kommunizieren mit dem Proxydienst, um die neuesten Listen mit gesperrten Kennwörtern zur Verwendung bei der Verarbeitung von Änderungsereignissen im Zusammenhang mit Kennwörtern in der Domäne zu erhalten.

Wählen Sie mindestens einen Server für das Hosten des Azure AD-Kennwortschutz-Proxydiensts aus. Die folgenden Überlegungen gelten für diese Server:

* Jeder dieser Dienste kann nur Kennwortrichtlinien für eine einzelne Gesamtstruktur bereitstellen. Der Hostcomputer muss einer Domäne in dieser Gesamtstruktur angehören.
* Die Installation des Proxydiensts in Stammdomänen oder untergeordneten Domänen bzw. in einer Kombination dieser Domänen wird unterstützt.
* Sie benötigen eine Netzwerkverbindung zwischen mindestens einem Domänencontroller in jeder Domäne der Gesamtstruktur und einem Kennwortschutz-Proxyserver.
* Sie haben die Möglichkeit, den Azure AD-Kennwortschutz-Proxydienst zu Testzwecken auf einem Domänencontroller auszuführen. Für den Domänencontroller ist dann aber eine Internetverbindung erforderlich. Diese Konnektivität kann ein Sicherheitsproblem darstellen. Es wird empfohlen, diese Konfiguration nur zu Testzwecken zu verwenden.
* Es wird empfohlen, aus Redundanzgründen mindestens zwei Azure AD-Kennwortschutz-Proxyserver pro Gesamtstruktur zu verwenden, wie im vorherigen Abschnitt mit [Überlegungen zur Hochverfügbarkeit](#high-availability-considerations) beschrieben.
* Das Ausführen des Proxydiensts für den Azure AD-Kennwortschutz auf einem schreibgeschützten Domänencontroller wird nicht unterstützt.

Befolgen Sie zum Installieren des Azure AD-Kennwortschutz-Proxydiensts die folgenden Schritte:

1. Führen Sie zum Installieren des Azure AD-Kennwortschutz-Proxydiensts das Softwareinstallationsprogramm `AzureADPasswordProtectionProxySetup.exe` aus.

    Die Softwareinstallation erfordert keinen Neustart und kann mithilfe von MSI-Standardverfahren automatisiert werden, wie im folgenden Beispiel gezeigt:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Der Windows-Firewall-Dienst muss vor der Installation des Pakets `AzureADPasswordProtectionProxySetup.exe` ausgeführt werden, um einen Installationsfehler zu vermeiden.
    >
    > Wenn die Windows-Firewall dafür konfiguriert ist, nicht ausgeführt zu werden, besteht die Problemumgehung im vorübergehenden Aktivieren und Ausführen des Firewalldiensts während des Installationsvorgangs. Die Proxysoftware weist nach der Installation keine Abhängigkeit von der Windows-Firewall auf.
    >
    > Wenn Sie eine Firewall eines Drittanbieters verwenden, muss auch diese zum Erfüllen der Anforderungen für die Bereitstellung konfiguriert sein. Dazu gehören Zugriff auf Port 135 für eingehenden Datenverkehr und auf den Proxy-RPC-Serverport. Weitere Informationen finden Sie im vorherigen Abschnitt mit den [Anforderungen für die Bereitstellung](#deployment-requirements).

1. Die Software für den Azure AD-Kennwortschutz-Proxy enthält ein neues PowerShell-Modul namens `AzureADPasswordProtection`. Die folgenden Schritte führen verschiedene Cmdlets aus diesem PowerShell-Modul aus.

    Um dieses Modul zu verwenden, öffnen Sie ein PowerShell-Fenster als Administrator, und importieren Sie das neue Modul wie folgt:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Um zu überprüfen, ob der Azure AD-Kennwortschutz-Proxydienst ausgeführt wird, verwenden Sie den folgenden PowerShell-Befehl:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Das Ergebnis sollte als **Status** den Wert *Wird ausgeführt* anzeigen.

1. Der Proxydienst wird auf dem Computer ausgeführt, verfügt aber nicht über die Anmeldeinformationen für die Kommunikation mit Azure AD. Registrieren Sie den Azure AD-Kennwortschutz-Proxyserver mithilfe des Cmdlets `Register-AzureADPasswordProtectionProxy` bei Azure AD.

    Dieses Cmdlet erfordert die Anmeldeinformationen des *globalen Administrators* oder des *Sicherheitsadministrators* für Ihren Azure-Mandanten. Dieses Cmdlet muss außerdem mit einem Konto mit lokalen Administratorrechten ausgeführt werden.

    Nachdem dieser Befehl einmal für einen Azure AD-Kennwortschutz-Proxydienst erfolgreich war, sind weitere Aufrufe erfolgreich, aber nicht erforderlich.

    Das Cmdlet `Register-AzureADPasswordProtectionProxy` unterstützt die folgenden drei Authentifizierungsmodi. Die ersten beiden Modi unterstützen Azure AD Multi-Factor Authentication, der dritte hingegen nicht.

    > [!TIP]
    > Es kann zu einer spürbaren Verzögerung bis zum Abschluss kommen, wenn dieses Cmdlet zum ersten Mal für einen bestimmten Azure-Mandanten ausgeführt wird. Wenn kein Fehler gemeldet wird, müssen Sie sich keine Gedanken über diese Verzögerung machen.

     * Interaktiver Authentifizierungsmodus:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Dieser Modus funktioniert nicht auf Server Core-Betriebssystemen. Verwenden Sie stattdessen einen der folgenden Authentifizierungsmechanismen. Dieser Modus kann fehlschlagen, wenn verstärkte Sicherheitskonfiguration für Internet Explorer aktiviert ist. Die Problemumgehung besteht darin, diese Konfiguration zu deaktivieren, den Proxy zu registrieren und die Konfiguration dann erneut zu aktivieren.

     * Wählen Sie den Gerätecode-Authentifizierungsmodus aus:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Verwenden Sie, wenn Sie dazu aufgefordert werden, den Link zum Öffnen eines Webbrowsers, und geben Sie den Authentifizierungscode ein.

     * Automatischer (kennwortbasierter) Authentifizierungsmodus:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Diese Modus ist nicht erfolgreich, wenn Azure AD Multi-Factor Authentication für Ihr Konto erforderlich ist. Verwenden Sie in diesem Fall einen der beiden vorherigen Authentifizierungsmodi oder ein anderes Konto, für das keine mehrstufige Authentifizierung erforderlich ist.
        >
        > Eine mehrstufige Authentifizierung kann auch erforderlich sein, wenn der (im Hintergrund durch den Azure AD-Kennwortschutz verwendete) Azure-Geräteregistrierungsdienst so konfiguriert wurde, dass global eine mehrstufige Authentifizierung erzwungen wird. Zur Umgehung dieser Anforderung können Sie ein anderes Konto verwenden, das die mehrstufige Authentifizierung mit einem der beiden vorherigen Authentifizierungsmodi unterstützt, oder die MFA-Anforderung des Azure-Geräteregistrierungsdiensts vorübergehend lockern.
        >
        > Wenn Sie diese Änderung vornehmen möchten, suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie den Dienst und dann **Geräte > Geräteeinstellungen** aus. Legen Sie **Mehrstufige Authentifizierung zum Hinzufügen von Geräten erforderlich** auf *Nein* fest. Denken Sie daran, diese Einstellung nach Abschluss der Registrierung wieder auf *Ja* zurückzusetzen.
        >
        > MFA-Anforderungen sollten ausschließlich zu Testzwecken umgangen werden.

    Sie müssen zurzeit den Parameter *-ForestCredential* nicht angeben, der für zukünftige Funktionalität reserviert ist.

    Die Registrierung des Azure AD-Kennwortschutz-Proxydiensts ist nur ein Mal während der Lebensdauer des Diensts erforderlich. Danach führt der Azure AD-Kennwortschutz-Proxydienst automatisch alle weiteren notwendigen Wartungen durch.

1. Registrieren Sie nun die lokale Active Directory-Gesamtstruktur mit den erforderlichen Anmeldeinformationen, um unter Verwendung des PowerShell-Cmdlets `Register-AzureADPasswordProtectionForest` mit Azure zu kommunizieren.

    > [!NOTE]
    > Wenn mehrere Azure AD-Kennwortschutz-Proxyserver in Ihrer Umgebung installiert sind, spielt es keine Rolle, welcher Proxyserver zum Registrieren der Gesamtstruktur verwendet wird.

    Das Cmdlet erfordert die Anmeldeinformationen des *globalen Administrators* oder des *Sicherheitsadministrators* für Ihren Azure-Mandanten. Es benötigt außerdem lokale Active Directory-Unternehmensadministratorrechte. Sie müssen dieses Cmdlet auch mit einem Konto mit lokalen Administratorrechten ausführen. Das Azure-Konto, das zum Registrieren der Gesamtstruktur verwendet wird, unterscheidet sich möglicherweise vom lokalen Active Directory-Konto.
    
    Dieser Schritt wird pro Gesamtstruktur einmal ausgeführt.

    Das Cmdlet `Register-AzureADPasswordProtectionForest` unterstützt die folgenden drei Authentifizierungsmodi. Die ersten beiden Modi unterstützen Azure AD Multi-Factor Authentication, der dritte hingegen nicht.

    > [!TIP]
    > Es kann zu einer spürbaren Verzögerung bis zum Abschluss kommen, wenn dieses Cmdlet zum ersten Mal für einen bestimmten Azure-Mandanten ausgeführt wird. Wenn kein Fehler gemeldet wird, müssen Sie sich keine Gedanken über diese Verzögerung machen.

     * Interaktiver Authentifizierungsmodus:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Dieser Modus funktioniert nicht auf Server Core-Betriebssystemen. Verwenden Sie stattdessen einen der folgenden zwei Authentifizierungsmechanismen. Dieser Modus kann fehlschlagen, wenn verstärkte Sicherheitskonfiguration für Internet Explorer aktiviert ist. Die Problemumgehung besteht darin, diese Konfiguration zu deaktivieren, die Gesamtstruktur zu registrieren und die Konfiguration dann wieder zu aktivieren.  

     * Wählen Sie den Gerätecode-Authentifizierungsmodus aus:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Verwenden Sie, wenn Sie dazu aufgefordert werden, den Link zum Öffnen eines Webbrowsers, und geben Sie den Authentifizierungscode ein.

     * Automatischer (kennwortbasierter) Authentifizierungsmodus:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Diese Modus ist nicht erfolgreich, wenn Azure AD Multi-Factor Authentication für Ihr Konto erforderlich ist. Verwenden Sie in diesem Fall einen der beiden vorherigen Authentifizierungsmodi oder ein anderes Konto, für das keine mehrstufige Authentifizierung erforderlich ist.
        >
        > Eine mehrstufige Authentifizierung kann auch erforderlich sein, wenn der (im Hintergrund durch den Azure AD-Kennwortschutz verwendete) Azure-Geräteregistrierungsdienst so konfiguriert wurde, dass global eine mehrstufige Authentifizierung erzwungen wird. Zur Umgehung dieser Anforderung können Sie ein anderes Konto verwenden, das die mehrstufige Authentifizierung mit einem der beiden vorherigen Authentifizierungsmodi unterstützt, oder die MFA-Anforderung des Azure-Geräteregistrierungsdiensts vorübergehend lockern.
        >
        > Wenn Sie diese Änderung vornehmen möchten, suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie den Dienst und dann **Geräte > Geräteeinstellungen** aus. Legen Sie **Mehrstufige Authentifizierung zum Hinzufügen von Geräten erforderlich** auf *Nein* fest. Denken Sie daran, diese Einstellung nach Abschluss der Registrierung wieder auf *Ja* zurückzusetzen.
        >
        > MFA-Anforderungen sollten ausschließlich zu Testzwecken umgangen werden.

       Diese Beispiele funktionieren nur, wenn der aktuell angemeldete Benutzer auch ein Active Directory-Domänenadministrator für die Stammdomäne ist. Wenn dies nicht der Fall ist, können die alternativen Anmeldeinformationen für die Domäne auch über den Parameter *-ForestCredential* angegeben werden.

    Die Registrierung der Active Directory-Gesamtstruktur ist nur ein Mal während der Lebensdauer der Gesamtstruktur erforderlich. Danach führt der Azure AD-Kennwortschutz-DC-Agent in der Gesamtstruktur automatisch alle weiteren notwendigen Wartungen durch. Nachdem `Register-AzureADPasswordProtectionForest` erfolgreich für eine Gesamtstruktur ausgeführt wurde, sind weitere Aufrufe des Cmdlets erfolgreich, aber nicht erforderlich.
    
    Damit `Register-AzureADPasswordProtectionForest` erfolgreich ist, muss mindestens ein Domänencontroller, auf dem Windows Server 2012 oder höher ausgeführt wird, in der Domäne des Azure AD-Kennwortschutz-Proxyservers verfügbar sein. Die DC-Agent-Software des Azure AD-Kennwortschutzes muss vor diesem Schritt nicht auf einem Domänencontroller installiert sein.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Konfigurieren des Proxydiensts für die Kommunikation über einen HTTP-Proxy

Wenn Ihre Umgebung die Verwendung eines bestimmten HTTP-Proxys für die Kommunikation mit Azure erfordert, führen Sie die folgenden Schritte aus, um den Azure AD-Kennwortschutzdienst zu konfigurieren.

Erstellen Sie die Datei *AzureADPasswordProtectionProxy.exe.config* im Ordner `%ProgramFiles%\Azure AD Password Protection Proxy\Service`. Beziehen Sie die folgenden Inhalte ein:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Wenn Ihr HTTP-Proxy Authentifizierung erfordert, fügen Sie das *useDefaultCredentials*-Tag hinzu:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

In beiden Fällen ersetzen Sie `http://yourhttpproxy.com:8080` durch die Adresse und den Port Ihres jeweiligen HTTP-Proxyservers.

Wenn Ihr HTTP-Proxy für die Verwendung einer Autorisierungsrichtlinie konfiguriert ist, müssen Sie den Zugriff auf das Active Directory-Computerkonto des Computers gewähren, der den Proxydienst für den Kennwortschutz hostet.

Es wird empfohlen, den Azure AD-Kennwortschutz-Proxydienst zu beenden und neu zu starten, nachdem Sie die Datei *AzureADPasswordProtectionProxy.exe.config* erstellt oder aktualisiert haben.

Der Proxydienst unterstützt nicht die Verwendung von spezifischen Anmeldeinformationen für das Herstellen einer Verbindung mit einem HTTP-Proxy.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Konfigurieren des Proxydiensts für das Lauschen an einem bestimmten Port

Die DC-Agent-Software für den Azure AD-Kennwortschutz verwendet RPC über TCP für die Kommunikation mit dem Proxydienst. Standardmäßig lauscht der Azure AD-Kennwortschutz-Proxydienst auf jedem verfügbaren dynamischen RPC-Endpunkt. Sie können den Dienst so konfigurieren, dass er an einem bestimmten TCP-Port lauscht, wenn dies aufgrund von Netzwerktopologie- oder Firewallanforderungen in Ihrer Umgebung erforderlich ist.

<a id="static" /></a>Verwenden Sie das Cmdlet `Set-AzureADPasswordProtectionProxyConfiguration` wie folgt, um den Dienst für die Ausführung unter einem statischen Port zu konfigurieren.

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Sie müssen den Azure AD-Kennwortschutz-Proxydienst beenden und neu starten, damit die Änderungen wirksam werden.

Um den Dienst so zu konfigurieren, dass er unter einem dynamischen Port ausgeführt wird, verwenden Sie die gleiche Prozedur, legen *StaticPort* dabei aber wieder auf Null fest:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Sie müssen den Azure AD-Kennwortschutz-Proxydienst beenden und neu starten, damit die Änderungen wirksam werden.

Der Azure AD-Kennwortschutz-Proxydienst erfordert einen manuellen Neustart nach jeder Änderung an der Portkonfiguration. Sie müssen jedoch den DC-Agent-Dienst für den Azure AD-Kennwortschutz auf Domänencontrollern nicht neu starten, nachdem Sie diese Konfigurationsänderungen vorgenommen haben.

Um die aktuelle Konfiguration des Diensts abzufragen, verwenden Sie das Cmdlet `Get-AzureADPasswordProtectionProxyConfiguration`, wie im folgenden Beispiel gezeigt:

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

Die folgende Beispielausgabe zeigt, dass der Azure AD-Kennwortschutz-Proxydienst einen dynamischen Port verwendet:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Installieren des DC-Agent-Diensts

Führen Sie zum Installieren des DC-Agent-Diensts für den Azure AD-Kennwortschutz das Paket `AzureADPasswordProtectionDCAgentSetup.msi` aus.

Die Softwareinstallation kann mit MSI-Standardverfahren automatisiert werden, wie im folgenden Beispiel gezeigt:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

Sie können das `/norestart`-Flag auslassen, wenn Sie es vorziehen, dass der Computer vom Installationsprogramm automatisch neu gestartet wird.

Die Installation oder Deinstallation der Software erfordert einen Neustart. Dies liegt daran, dass Kennwortfilter-DLLs nur durch einen Neustart geladen oder entladen werden.

Die Installation des lokalen Azure AD-Kennwortschutzes ist abgeschlossen, nachdem die DC-Agent-Software auf einem Domänencontroller installiert und dieser Computer neu gestartet wurde. Eine andere Konfiguration ist weder erforderlich noch möglich. Bei Ereignissen durch Kennwortänderungen für lokale Domänencontroller werden die konfigurierten Listen mit gesperrten Kennwörtern von Azure AD verwendet.

Informationen zum Aktivieren des lokalen Azure AD-Kennwortschutzes über das Azure-Portal und zum Konfigurieren von benutzerdefinierten gesperrten Kennwörtern finden Sie unter [Aktivieren des lokalen Azure AD-Kennwortschutzes](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Sie können den Azure AD-Kennwortschutz-DC-Agent auf einem Computer installieren, der noch kein Domänencontroller ist. In diesem Fall wird der Dienst gestartet und ausgeführt, bleibt aber inaktiv, bis der Computer zum Domänencontroller hochgestuft wird.

## <a name="upgrading-the-proxy-service"></a>Aktualisieren des Proxydiensts

Der Azure AD-Kennwortschutz-Proxydienst unterstützt automatische Upgrades. Für das automatische Upgrade wird der Microsoft Azure AD Connect Agent Updater-Dienst verwendet, der zusammen mit dem Proxydienst installiert wird. Das automatische Upgrade ist standardmäßig aktiviert und kann mit dem Cmdlet `Set-AzureADPasswordProtectionProxyConfiguration` aktiviert oder deaktiviert werden.

Die aktuelle Einstellung kann mit dem Cmdlet `Get-AzureADPasswordProtectionProxyConfiguration` abgefragt werden. Es wird empfohlen, die Einstellung für das automatische Upgrade immer aktiviert zu lassen.

Mit dem Cmdlet `Get-AzureADPasswordProtectionProxy` kann die Softwareversion aller derzeit installierten Azure AD-Kennwortschutz-Proxyserver in einer Gesamtstruktur abgefragt werden.

### <a name="manual-upgrade-process"></a>Manueller Aktualisierungsvorgang

Ein manuelles Upgrade erfolgt durch Ausführen der neuesten Version des Softwareinstallationsprogramms `AzureADPasswordProtectionProxySetup.exe`. Die aktuelle Version der Software ist im [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071) verfügbar.

Es ist nicht erforderlich, die aktuelle Version des Azure AD-Kennwortschutz-Proxydiensts zu deinstallieren. Das Installationsprogramm führt ein direktes Upgrade durch. Beim Upgrade des Proxydiensts sollte kein Neustart erforderlich sein. Das Softwareupgrade kann mit MSI-Standardverfahren wie `AzureADPasswordProtectionProxySetup.exe /quiet` automatisiert werden.

## <a name="upgrading-the-dc-agent"></a>Upgrade des DC-Agents

Wenn eine neuere Version der DC-Agent-Software für den Azure AD-Kennwortschutz verfügbar ist, erfolgt das Upgrade durch Ausführen der neuesten Version des Softwarepakets `AzureADPasswordProtectionDCAgentSetup.msi`. Die aktuelle Version der Software ist im [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071) verfügbar.

Es ist nicht erforderlich, die aktuelle Version der DC-Agent-Software zu deinstallieren. Das Installationsprogramm führt ein direktes Upgrade durch. Beim Upgrade der DC-Agent-Software ist immer ein Neustart erforderlich. Diese Anforderung wird durch das Kernverhalten von Windows verursacht.

Das Softwareupgrade kann mit MSI-Standardverfahren wie `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` automatisiert werden.

Sie können das `/norestart`-Flag auslassen, wenn Sie es vorziehen, dass der Computer vom Installationsprogramm automatisch neu gestartet wird.

Mit dem Cmdlet `Get-AzureADPasswordProtectionDCAgent` kann die Softwareversion aller derzeit installierten Azure AD-Kennwortschutz-DC-Agents in einer Gesamtstruktur abgefragt werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun die für den Azure AD-Kennwortschutz erforderlichen Dienste auf Ihren lokalen Servern installiert haben, [aktivieren Sie den Azure AD-Kennwortschutz über das Azure-Portal](howto-password-ban-bad-on-premises-operations.md), um Ihre Bereitstellung abzuschließen.