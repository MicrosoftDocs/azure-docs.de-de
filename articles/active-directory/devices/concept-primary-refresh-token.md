---
title: Primäres Aktualisierungstoken (Primary Refresh Token, PRT) und Azure AD (Azure Active Directory)
description: Welche Rolle hat das primäre Aktualisierungstoken (Primary Refresh Token, PRT), und wie wird es in Azure Active Directory verwaltet?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f2b059bb6ae63d7f427ce970b2538da922e2dec
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837262"
---
# <a name="what-is-a-primary-refresh-token"></a>Was ist ein primäres Aktualisierungstoken (Primary Refresh Token, PRT)?

Ein primäres Aktualisierungstoken (Primary Refresh Token, PRT) ist ein Schlüsselartefakt der Azure AD-Authentifizierung auf Geräten mit Windows 10, Windows Server 2016 und höheren Versionen, iOS und Android. Es handelt sich um ein JSON Web Token (JWT), das speziell für Microsoft-Erstanbieter-Tokenbroker ausgestellt wird, um einmaliges Anmelden (Single Sign-On, SSO) für die Anwendungen zu ermöglichen, die auf diesen Geräten verwendet werden. Dieser Artikel enthält Details dazu, wie ein PRT auf Windows 10-Geräten ausgestellt, verwendet und geschützt wird.

In diesem Artikel wird davon ausgegangen, dass Sie bereits mit den verschiedenen verfügbaren Gerätestatus in Azure AD und der Funktionsweise des einmaligen Anmeldens in Windows 10 vertraut sind. Weitere Informationen zu Geräten in Azure AD finden Sie im Artikel [Worum handelt es sich bei der Geräteverwaltung in Azure Active Directory?](overview.md).

## <a name="key-terminology-and-components"></a>Wichtige Terminologie und Komponenten

Die folgenden Windows-Komponenten spielen beim Anfordern und Verwenden eines PRT eine wichtige Rolle:

* **Cloud-Authentifizierungsanbieter** (CloudAP): CloudAP ist der moderne Authentifizierungsanbieter für die Windows-Anmeldung, mit dem Benutzer überprüft werden, die sich an einem Windows 10-Gerät anmelden. CloudAP verfügt über ein Plug-In-Framework, das von Identitätsanbietern als Grundlage für die Ermöglichung der Windows-Authentifizierung mit den Anmeldeinformationen des jeweiligen Identitätsanbieters genutzt werden kann.
* **Web Account Manager** (WAM): WAM ist der Standardtokenbroker auf Windows 10-Geräten. WAM verfügt auch über ein Plug-In-Framework, das Identitätsanbieter als Grundlage verwenden können, um einmaliges Anmelden (SSO) für ihre Anwendungen über den jeweiligen Identitätsanbieter zu ermöglichen.
* **Azure AD-CloudAP-Plug-In**: Ein Azure AD-spezifisches Plug-In, das auf dem CloudAP-Framework basiert und mit dem die Anmeldeinformationen von Benutzern für Azure AD während der Windows-Anmeldung überprüft werden.
* **Azure AD-WAM-Plug-In**: Ein Azure AD-spezifisches Plug-In, das auf dem WAM-Framework basiert und mit dem SSO für Anwendungen ermöglicht wird, für die Azure AD für die Authentifizierung genutzt wird.
* **Dsreg**: Eine Azure AD-spezifische Komponente unter Windows 10, mit der der Prozess der Geräteregistrierung für alle Gerätezustände verarbeitet wird.
* **Trusted Platform Module** (TPM): Ein TPM ist eine in ein Gerät integrierte Hardwarekomponente, mit der hardwarebasierte Sicherheitsfunktionen für Benutzer- und Gerätegeheimnisse bereitgestellt werden. Weitere Informationen finden Sie im Artikel [Trusted Platform Module – Technologieübersicht](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Was enthält das PRT?

Ein PRT enthält Ansprüche, die normalerweise Teil von Azure AD-Aktualisierungstoken sind. Darüber hinaus sind im PRT auch einige gerätespezifische Ansprüche enthalten. Dies sind:

* **Geräte-ID**: Ein PRT wird für einen Benutzer auf einem bestimmten Gerät ausgestellt. Mit dem Geräte-ID-Anspruch `deviceID` wird das Gerät ermittelt, auf dem das PRT für den Benutzer ausgestellt wurde. Dieser Anspruch wird später dann für Token ausgestellt, die über das PRT empfangen werden. Der Geräte-ID-Anspruch wird verwendet, um die Autorisierung für den bedingten Zugriff basierend auf dem Gerätezustand oder der Gerätekonformität zu ermitteln.
* **Sitzungsschlüssel**: Der Sitzungsschlüssel ist ein verschlüsselter symmetrischer Schlüssel, der vom Azure AD-Authentifizierungsdienst generiert und als Teil des PRT ausgestellt wird. Der Sitzungsschlüssel fungiert als Eigentumsnachweis, wenn ein PRT verwendet wird, um Token für andere Anwendungen zu beschaffen.

### <a name="can-i-see-whats-in-a-prt"></a>Kann ich sehen, was in einem PRT enthalten ist?

Ein PRT ist ein nicht transparentes Blob, das von Azure AD gesendet wird und dessen Inhalt für Clientkomponenten nicht einsehbar ist. Sie können nicht sehen, was sich in einem PRT befindet.

## <a name="how-is-a-prt-issued"></a>Wie wird ein PRT ausgestellt?

Die Geräteregistrierung ist eine Voraussetzung für die gerätebasierte Authentifizierung in Azure AD. Ein PRT wird für Benutzer nur auf registrierten Geräten ausgestellt. Ausführlichere Informationen zur Geräteregistrierung finden Sie im Artikel [Windows Hello for Business und Geräteregistrierung](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Während der Geräteregistrierung werden mit der dsreg-Komponente zwei Sätze mit kryptografischen Schlüsselpaaren generiert:

* Geräteschlüssel (dkpub/dkpriv)
* Transportschlüssel (tkpub/tkpriv)

Die privaten Schlüssel sind an das TPM des Geräts gebunden, wenn das Gerät über ein gültiges und funktionierendes TPM verfügt, während die öffentlichen Schlüssel während des Prozesses für die Geräteregistrierung an Azure AD gesendet werden. Diese Schlüssel werden verwendet, um den Gerätezustand bei PRT-Anforderungen zu überprüfen.

Das PRT wird während der Benutzerauthentifizierung auf einem Windows 10-Gerät in zwei Szenarien ausgestellt:

* **In Azure AD eingebunden** oder **Hybrid in Azure AD eingebunden**: Ein PRT wird während der Windows-Anmeldung ausgestellt, wenn sich ein Benutzer mit seinen Anmeldeinformationen der Organisation anmeldet. Ein PRT wird mit allen von Windows 10 unterstützten Anmeldeinformationen ausgestellt, z. B. Kennwort und Windows Hello for Business. In diesem Szenario ist das Azure AD-CloudAP-Plug-In die primäre Autorität für das PRT.
* **In Azure AD registriertes Gerät**: Ein PRT wird ausgestellt, wenn ein Benutzer seinem Windows 10-Gerät ein sekundäres Geschäftskonto hinzufügt. Benutzer haben zwei Möglichkeiten, um Windows 10 ein Konto hinzuzufügen:  
   * Hinzufügen eines Kontos über die Eingabeaufforderung **Dieses Konto überall auf Ihrem Gerät verwenden** nach dem Anmelden an einer App (z. B. Outlook)
   * Hinzufügen eines Kontos über **Einstellungen** > **Konten** > **Auf Arbeits- oder Schulkonto zugreifen** > **Verbinden**

In Szenarien für in Azure AD registrierte Geräte ist das Azure AD-WAM-Plug-In die primäre Autorität für das PRT, da die Windows-Anmeldung mit diesem Azure AD-Konto nicht durchgeführt wird.

> [!NOTE]
> Identitätsanbieter von Drittanbietern müssen das WS-Trust-Protokoll unterstützen, damit die PRT-Ausstellung auf Windows 10-Geräten ermöglicht wird. Ohne WS-Trust kann PRT nicht für Benutzer auf in Azure AD Hybrid oder in Azure AD eingebunden Geräten ausgestellt werden. Für AD FS sind nur „usernamemixed“-Endpunkte erforderlich. Die Endpunkte „adfs/services/trust/2005/windowstransport“ und „adfs/services/trust/13/windowstransport“ dürfen nur als Endpunkte mit Intranetzugriff aktiviert und **NICHT** als Endpunkte mit Extranetzugriff über den Webanwendungsproxy verfügbar gemacht werden.

## <a name="what-is-the-lifetime-of-a-prt"></a>Welche Lebensdauer hat ein PRT?

Nach der Ausstellung ist ein PRT 14 Tage lang gültig und wird fortlaufend verlängert, solange das Gerät vom Benutzer aktiv genutzt wird.  

## <a name="how-is-a-prt-used"></a>Wie wird ein PRT verwendet?

Ein PRT wird in Windows von zwei wichtigen Komponenten verwendet:

* **Azure AD-CloudAP-Plug-In**: Während der Windows-Anmeldung fordert das Azure AD-CloudAP-Plug-In ein PRT von Azure AD an, indem die vom Benutzer bereitgestellten Anmeldeinformationen verwendet werden. Darüber hinaus wird das PRT hierbei zwischengespeichert, um die Nutzung der zwischengespeicherten Anmeldung zu ermöglichen, wenn der Benutzer keinen Zugriff auf eine Internetverbindung hat.
* **Azure AD-WAM-Plug-In**: Wenn Benutzer versuchen, auf Anwendungen zuzugreifen, wird das PRT vom Azure AD-WAM-Plug-In genutzt, um SSO unter Windows 10 zu ermöglichen. Das Azure AD-WAM-Plug-In nutzt das PRT, um Aktualisierungs- und Zugriffstoken für Anwendungen anzufordern, für die WAM für Tokenanforderungen verwendet wird. Außerdem wird hiermit SSO in Browsern ermöglicht, indem das PRT in Browseranforderungen eingefügt wird. SSO für Browser unter Windows 10 wird für Microsoft Edge (nativ) und Chrome (über die [Windows 10-Konten](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) oder die [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en)-Erweiterungen) unterstützt.

## <a name="how-is-a-prt-renewed"></a>Wie wird ein PRT verlängert?

Es gibt zwei Verfahren, um ein PRT zu verlängern:

* **Alle vier Stunden per Azure AD-CloudAP-Plug-In**: Das CloudAP-Plug-In verlängert das PRT während der Windows-Anmeldung alle vier Stunden. Wenn der Benutzer während dieses Zeitraums nicht über eine Internetverbindung verfügt, verlängert das CloudAP-Plug-In das PRT, nachdem das Gerät mit dem Internet verbunden wurde.
* **Azure AD-WAM-Plug-In bei Anforderungen von App-Token**: Mit dem WAM-Plug-In wird SSO auf Windows 10-Geräten ermöglicht, indem automatische Tokenanforderungen für Anwendungen aktiviert werden. Das WAM-Plug-In kann das PRT während dieser Tokenanforderungen auf zwei Arten verlängern:
   * Eine App fordert WAM automatisch für ein Zugriffstoken an, aber für die App ist kein Aktualisierungstoken vorhanden. In diesem Fall nutzt WAM das PRT, um ein Token für die App anzufordern, und erhält als Antwort ein neues PRT.
   * Eine App fordert WAM für ein Zugriffstoken an, aber das PRT ist ungültig, oder für Azure AD ist eine zusätzliche Autorisierung erforderlich (z. B. Azure AD Multi-Factor Authentication). In diesem Szenario initiiert WAM eine interaktive Anmeldung, bei der der Benutzer sich erneut authentifizieren oder eine zusätzliche Überprüfung durchführen muss. Nach der erfolgreichen Authentifizierung wird ein neues PRT ausgestellt.

In einer ADFS-Umgebung ist keine direkte Sichtlinie zum Domänencontroller erforderlich, um das PRT zu verlängern. Für die PRT-Verlängerung müssen lediglich die Endpunkte „/adfs/services/trust/2005/usernamemixed“ und „/adfs/services/trust/13/usernamemixed“ mithilfe des WS-Trust-Protokolls für den Proxy aktiviert werden.

Windows-Transportendpunkte werden nur für die Kennwortauthentifizierung benötigt, wenn ein Kennwort geändert wird, nicht aber für die PRT-Verlängerung.

### <a name="key-considerations"></a>Wichtige Aspekte

* Ein PRT wird nur während einer nativen App-Authentifizierung ausgestellt und verlängert. Ein PRT wird während einer Browsersitzung nicht verlängert oder ausgestellt.
* Auf Geräten vom Typ „In Azure AD eingebunden“ und „Hybrid in Azure AD eingebunden“ ist das CloudAP-Plug-In die primäre Autorität für ein PRT. Wenn ein PRT während einer WAM-basierten Tokenanforderung verlängert wird, wird es zurück an das CloudAP-Plug-In gesendet. Dort wird die Gültigkeit des PRT für Azure AD überprüft, bevor es akzeptiert wird.

## <a name="how-is-the-prt-protected"></a>Wie wird das PRT geschützt?

Ein PRT wird geschützt, indem es an das Gerät gebunden wird, an dem sich der Benutzer angemeldet hat. Für Azure AD und Windows 10 wird der PRT-Schutz anhand der folgenden Methoden ermöglicht:

* **Während der ersten Anmeldung**: Während der ersten Anmeldung wird ein PRT ausgestellt, indem Anforderungen mit dem Geräteschlüssel signiert werden, der während der Geräteregistrierung kryptografisch generiert wurde. Auf einem Gerät mit einem gültigen und funktionierenden TPM wird der Geräteschlüssel per TPM geschützt, um schädliche Zugriffe zu verhindern. Es wird kein PRT ausgestellt, wenn die entsprechende Geräteschlüsselsignatur nicht überprüft werden kann.
* **Bei Tokenanforderungen und -verlängerungen**: Wenn ein PRT ausgestellt wird, wird von Azure AD auch ein verschlüsselter Sitzungsschlüssel für das Gerät ausgestellt. Er wird mit dem öffentlichen Transportschlüssel (tkpub) generiert und im Rahmen der Geräteregistrierung an Azure AD gesendet. Dieser Sitzungsschlüssel kann nur mit dem privaten Transportschlüssel (tkpriv) entschlüsselt werden, der per TPM geschützt ist. Der Sitzungsschlüssel ist der Schlüssel für den Eigentumsnachweis (Proof-of-Possession, POP) für alle Anforderungen, die an Azure AD gesendet werden.  Der Sitzungsschlüssel ist außerdem durch das TPM geschützt, und keine anderen Betriebssystemkomponenten können darauf zugreifen. Tokenanforderungen oder Anforderungen zur PRT-Verlängerung werden mit diesem Sitzungsschlüssel über das TPM auf sichere Weise signiert und können daher nicht manipuliert werden. Von Azure AD werden alle Anforderungen von diesem Gerät ungültig gemacht, die nicht mit dem entsprechenden Sitzungsschlüssel signiert sind.

Indem diese Schlüssel mit dem TPM geschützt werden, können böswillige Akteure weder die Schlüssel stehlen, noch das PRT an einem anderen Ort wiedergeben. Auf das TPM kann auch dann nicht zugegriffen werden, wenn sich das Gerät physisch im Besitz eines Angreifers befindet.  Durch die Nutzung eines TPM wird der Schutz von Geräten vom Typ „In Azure AD eingebunden“, „Hybrid in Azure AD eingebunden“ und „Bei Azure AD registriert“ vor einem Diebstahl von Anmeldeinformationen stark verbessert. In Bezug auf Leistung und Zuverlässigkeit ist TPM 2.0 die empfohlene Version für alle Szenarien der Azure AD-Geräteregistrierung unter Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Wie sind App-Token und Browsercookies geschützt?

**App-Token**: Wenn eine App Token per WAM anfordert, stellt Azure AD ein Aktualisierungstoken und ein Zugriffstoken aus. Von WAM wird aber nur das Zugriffstoken an die App zurückgegeben und das Aktualisierungstoken im Cache geschützt, indem es mit dem DPAPI-Schlüssel (Data Protection Application Programming Interface) des Benutzers verschlüsselt wird. Von WAM wird das Aktualisierungstoken auf sichere Weise verwendet, indem Anforderungen mit dem Sitzungsschlüssel signiert werden, um weitere Zugriffstoken auszustellen. Der DPAPI-Schlüssel wird mit einem Azure AD-basierten symmetrischen Schlüssel direkt in Azure AD geschützt. Wenn das Gerät das Benutzerprofil mit dem DPAPI-Schlüssel entschlüsseln muss, stellt Azure AD den mit dem Sitzungsschlüssel verschlüsselten DPAPI-Schlüssel bereit. Für den Sitzungsschlüssel fordert das CloudAP-Plug-In die Entschlüsselung durch das TPM an. Mit dieser Funktionalität wird sichergestellt, dass das Schützen von Aktualisierungstoken einheitlich erfolgt, und es wird vermieden, dass Anwendungen ihre eigenen Schutzmechanismen implementieren.  

**Browsercookies**: In Windows 10 unterstützt Azure AD SSO für Browser in Internet Explorer und Microsoft Edge auf native Weise, und in Google Chrome über die Kontoerweiterung für Windows 10. Die Sicherheit dient nicht nur zum Schützen der Cookies, sondern auch der Endpunkte, an die die Cookies gesendet werden. Browsercookies werden genauso wie ein PRT geschützt, indem der Sitzungsschlüssel genutzt wird, um die Cookies zu signieren und zu schützen.

Wenn ein Benutzer eine Browserinteraktion initiiert, ruft der Browser (bzw. die Erweiterung) einen nativen COM-Clienthost auf. Der native Clienthost stellt sicher, dass die Seite aus einer der zulässigen Domänen stammt. Der Browser kann auch andere Parameter an den nativen Clienthost senden, z. B. eine Nonce, aber der native Clienthost garantiert die Überprüfung des Hostnamens. Der native Clienthost fordert ein PRT-Cookie vom CloudAP-Plug-In an. Das Plug-In erstellt und signiert es mit dem per TPM geschützten Sitzungsschlüssel. Da das PRT-Cookie mit dem Sitzungsschlüssel signiert ist, kann es nicht manipuliert werden. Dieses PRT-Cookie ist in den Anforderungsheader für Azure AD integriert, um das Ursprungsgerät zu überprüfen. Bei Verwendung des Chrome-Browsers ist der Aufruf nur mit der Erweiterung möglich, die im Manifest des nativen Clienthosts explizit definiert ist, damit keine anderen Erweiterungen diese Anforderungen senden können. Nachdem das PRT-Cookie von Azure AD überprüft wurde, wird ein Sitzungscookie für den Browser ausgestellt. Dieses Sitzungscookie enthält auch den gleichen Sitzungsschlüssel, der mit einem PRT ausgestellt wird. Während der nachfolgenden Anforderungen wird der Sitzungsschlüssel auf effektive Weise überprüft, und das Cookie wird an das Gerät gebunden. So werden Wiedergaben von anderen Orten verhindert.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Wann erhält ein PRT einen MFA-Anspruch?

Ein PRT kann in bestimmten Szenarien einen MFA-Anspruch (Multi-Factor Authentication) erhalten. Wenn ein MFA-basiertes PRT zum Anfordern von Token für Anwendungen verwendet wird, wird der MFA-Anspruch auf diese App-Token übertragen. Diese Funktionalität ermöglicht Benutzern eine reibungslose Vorgehensweise, weil die MFA-Überprüfung für alle Apps, für die dies erforderlich ist, verhindert wird. Ein PRT kann einen MFA-Anspruch auf folgende Arten erhalten:

* **Anmeldung mit Windows Hello for Business**: Bei Windows Hello for Business werden anstelle von Kennwörtern kryptografische Schlüssel verwendet, um für eine sichere zweistufige Authentifizierung zu sorgen. Windows Hello for Business gilt spezifisch für einen Benutzer auf einem Gerät und erfordert selbst einen MFA-Vorgang für die Bereitstellung. Wenn sich ein Benutzer mit Windows Hello for Business anmeldet, erhält das PRT des Benutzers einen MFA-Anspruch. Dieses Szenario gilt auch für Benutzer, die sich mit Smartcards anmelden, wenn bei der Authentifizierung per Smartcard ein MFA-Anspruch von AD FS erstellt wird.
   * Da Windows Hello for Business als mehrstufige Authentifizierung gilt, wird der MFA-Anspruch aktualisiert, wenn das PRT selbst aktualisiert wird. Die MFA-Dauer wird also fortlaufend verlängert, wenn sich Benutzer mit Windows Hello for Business anmelden.
* **MFA während der interaktiven WAM-Anmeldung**: Für eine Tokenanforderung per WAM gilt Folgendes: Wenn ein Benutzer einen MFA-Vorgang durchführen muss, um auf die App zuzugreifen, wird das PRT, das während dieser Interaktion verlängert wird, mit einem MFA-Anspruch versehen.
   * In diesem Fall wird der MFA-Anspruch nicht fortlaufend aktualisiert, und die MFA-Dauer basiert auf der Lebensdauer, die für das Verzeichnis festgelegt ist.
   * Wenn ein vorheriges vorhandenes primäres Aktualisierungstoken (Primary Refresh Token, PRT) und Aktualisierungstoken (Refresh Token, RT) für den Zugriff auf eine App verwendet werden, werden das PRT und RT als erster Authentifizierungsnachweis angesehen. Ein neues Authentifizierungstoken wird mit einem zweiten Nachweis und gedruckten MFA-Anspruch benötigt. Hierdurch werden auch ein neues PRT und RT ausgestellt.
* **MFA während der Geräteregistrierung**: Wenn ein Administrator seine Geräteeinstellungen in Azure AD so konfiguriert hat, dass [zum Registrieren von Geräten ein MFA-Vorgang erforderlich ist](device-management-azure-portal.md#configure-device-settings), muss der Benutzer diesen MFA-Vorgang durchführen, um die Registrierung abzuschließen. Während dieses Prozesses erhält das PRT, das für den Benutzer ausgestellt wird, den MFA-Anspruch, der während der Registrierung beschafft wird. Diese Funktion gilt nur für den Benutzer, der den Beitrittsvorgang durchgeführt hat, und nicht für andere Benutzer, die sich an diesem Gerät anmelden.
   * Ähnlich wie bei der interaktiven WAM-Anmeldung wird der MFA-Anspruch nicht fortlaufend aktualisiert, sodass die MFA-Dauer auf der Lebensdauer basiert, die für das Verzeichnis festgelegt ist.

Unter Windows 10 wird für jeden Satz von Anmeldeinformationen eine partitionierte Liste mit PRTs geführt. Es ist also ein PRT für jede Windows Hello for Business-Instanz, jedes Kennwort bzw. jede Smartcard vorhanden. Mit dieser Partitionierung wird sichergestellt, dass MFA-Ansprüche anhand der verwendeten Anmeldeinformationen isoliert und bei Tokenanforderungen nicht verwechselt werden.

## <a name="how-is-a-prt-invalidated"></a>Wann wird ein PRT ungültig gemacht?

Ein PRT wird in den folgenden Szenarien ungültig gemacht:

* **Ungültiger Benutzer**: Wenn ein Benutzer in Azure AD gelöscht oder deaktiviert wird, wird das zugehörige PRT ungültig gemacht und kann nicht verwendet werden, um Token für Anwendungen zu erhalten. Wenn sich ein gelöschter oder deaktivierter Benutzer vorher an einem Gerät angemeldet hat, wird die Anmeldung für ihn über die zwischengespeicherte Anmeldung durchgeführt, bis CloudAP über den ungültigen Zustand informiert wird. Nachdem von CloudAP ermittelt wurde, dass der Benutzer ungültig ist, werden nachfolgende Anmeldungen blockiert. Für einen ungültigen Benutzer wird die Anmeldung auf neuen Geräten, für die die Anmeldeinformationen nicht zwischengespeichert werden, automatisch gesperrt.
* **Ungültiges Gerät**: Wenn ein Gerät in Azure AD gelöscht oder deaktiviert wird, wird das auf diesem Gerät erhaltene PRT ungültig gemacht und kann nicht verwendet werden, um Token für andere Anwendungen abzurufen. Wenn ein Benutzer bereits auf einem ungültigen Gerät angemeldet ist, ist dies weiterhin möglich. Alle Token auf dem Gerät werden aber ungültig gemacht, und der Benutzer kann für Ressourcen über dieses Gerät kein SSO nutzen.
* **Kennwortänderung**: Nachdem ein Benutzer sein Kennwort geändert hat, wird das PRT, das mit dem vorherigen Kennwort beschafft wurde, von Azure AD ungültig gemacht. Eine Kennwortänderung führt dazu, dass der Benutzer ein neues PRT erhält. Token können hierbei auf zwei Arten ungültig gemacht werden:
   * Wenn sich der Benutzer bei Windows mit seinem neuen Kennwort anmeldet, verwirft CloudAP das alte PRT und sendet eine Anforderung an Azure AD, dass ein neues PRT mit dem neuen Kennwort ausgestellt werden soll. Wenn der Benutzer nicht über eine Internetverbindung verfügt, kann das neue Kennwort nicht überprüft werden. Es kann sein, dass der Benutzer von Windows aufgefordert wird, das alte Kennwort einzugeben.
   * Wenn sich ein Benutzer mit seinem alten Kennwort angemeldet oder das Kennwort nach dem Anmelden an Windows geändert hat, wird das alte PRT für alle WAM-basierten Tokenanforderungen verwendet. In diesem Szenario wird der Benutzer aufgefordert, während der WAM-Tokenanforderung eine erneute Authentifizierung durchzuführen, und ein neues PRT wird ausgestellt.
* **TPM-Probleme**: Es kann vorkommen, dass das TPM eines Geräts nicht reibungslos funktioniert oder ausfällt, sodass auf die mit dem TPM geschützten Schlüssel nicht mehr zugegriffen werden kann. In diesem Fall kann das Gerät kein PRT abrufen oder Token über ein vorhandenes PRT anfordern, da der Nachweis über den Besitz von kryptografischen Schlüsseln nicht erbracht werden kann. Aus diesem Grund werden alle vorhandenen PRTs von Azure AD ungültig gemacht. Wenn Windows 10 einen Fehler erkennt, wird ein Wiederherstellungsverfahren initiiert, um das Gerät mit neuen kryptografischen Schlüsseln wieder zu registrieren. Bei der Azure AD-Hybrideinbindung wird die Wiederherstellung – wie auch bei der ersten Registrierung – automatisch ohne Benutzereingabe durchgeführt. Für in Azure AD eingebundene oder registrierte Geräte muss die Wiederherstellung von einem Benutzer durchgeführt werden, der auf dem Gerät über Administratorrechte verfügt. In diesem Szenario wird das Wiederherstellungsverfahren über eine Windows-Eingabeaufforderung initiiert, in der für den Benutzer die Schritte zur erfolgreichen Wiederherstellung des Geräts angegeben werden.

## <a name="detailed-flows"></a>Ausführliche Verfahren

Im folgenden Diagramm sind die zugrunde liegenden Details für das Ausstellen, Verlängern und Verwenden eines PRT zum Anfordern eines Zugriffstokens für eine Anwendung dargestellt. Darüber hinaus wird mit diesen Schritten auch beschrieben, wie die oben erwähnten Sicherheitsmechanismen bei diesen Interaktionen angewendet werden.

### <a name="prt-issuance-during-first-sign-in"></a>PRT-Ausstellung während der ersten Anmeldung

![PRT-Ausstellung während der ersten Anmeldung: Ausführliches Verfahren](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Für in Azure AD eingebundene Geräte wird dieser Austausch synchron durchgeführt, damit ein PRT ausgestellt wird, bevor sich der Benutzer bei Windows anmelden kann. Für in Azure AD eingebundene Hybridgeräte ist die lokale Active Directory-Instanz die primäre Autorität. Der Benutzer wartet also nur darauf, dass ein TGT für die Anmeldung abgerufen werden kann, während die PRT-Ausstellung asynchron erfolgt. Dieses Szenario gilt nicht für in Azure AD registrierte Geräte, da für die Anmeldung keine Azure AD-Anmeldeinformationen verwendet werden.

| Schritt | BESCHREIBUNG |
| :---: | --- |
| Ein | Benutzer geben ihr Kennwort auf der Benutzeroberfläche für die Anmeldung ein. Die Benutzeroberfläche für die Anmeldung (LogonUI) übergibt die Anmeldeinformationen in einem Authentifizierungspuffer an die lokale Sicherheitsautorität, von der sie wiederum intern an CloudAP übergeben werden. CloudAP leitet diese Anforderung an das CloudAP-Plug-In weiter. |
| B | Das CloudAP-Plug-In initiiert eine Bereichsermittlungsanforderung, um den Identitätsanbieter für den Benutzer zu identifizieren. Wenn für den Mandanten des Benutzers ein Verbundanbieter eingerichtet wurde, gibt Azure AD den Metadatenaustausch-Endpunkt (MEX-Endpunkt) des Verbundanbieters zurück. Wenn nicht, gibt Azure AD den Hinweis zurück, dass der Benutzer verwaltet wird und die Authentifizierung mit Azure AD durchführen kann. |
| C | Wenn der Benutzer verwaltet wird, erhält CloudAP die Nonce von Azure AD. Falls der Benutzer Teil eines Verbunds ist, fordert das CloudAP-Plug-In ein SAML-Token mit den Anmeldeinformationen des Benutzers vom Verbundanbieter an. Nach Erhalt des SAML-Tokens fordert es eine Nonce von Azure AD an. |
| D | Das CloudAP-Plug-In erstellt die Authentifizierungsanforderung mit den Anmeldeinformationen des Benutzers, einer Nonce und einem Brokerbereich, signiert die Anforderung mit dem Geräteschlüssel (dkpriv) und sendet sie an Azure AD. In einer Verbundumgebung nutzt das CloudAP-Plug-In nicht die Anmeldeinformationen des Benutzers, sondern das vom Verbundanbieter zurückgegebene SAML-Token. |
| E | Azure AD überprüft die Anmeldeinformationen des Benutzers, die Nonce und die Gerätesignatur, stellt sicher, dass das Gerät im Mandanten gültig ist, und stellt das verschlüsselte PRT aus. Zusammen mit dem PRT stellt Azure AD auch einen symmetrischen Schlüssel aus, der als Sitzungsschlüssel bezeichnet wird. Er wird von Azure AD mit dem Transportschlüssel (tkpub) verschlüsselt. Darüber hinaus wird der Sitzungsschlüssel auch in das PRT eingebettet. Dieser Sitzungsschlüssel fungiert als Eigentumsnachweis (Proof-of-Possession, PoP) für nachfolgende Anforderungen mit dem PRT. |
| F | Das CloudAP-Plug-In übergibt das verschlüsselte PRT und den Sitzungsschlüssel an CloudAP. CloudAP fordert vom TPM die Entschlüsselung des Sitzungsschlüssels mit dem Transportschlüssel (tkpriv) und die erneute Verschlüsselung mit dem eigenen Schlüssel des TPM an. CloudAP speichert den verschlüsselten Sitzungsschlüssel zusammen mit dem PRT in seinem Cache. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT-Verlängerung bei nachfolgenden Anmeldungen

![PRT-Verlängerung bei nachfolgenden Anmeldungen](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Schritt | BESCHREIBUNG |
| :---: | --- |
| Ein | Benutzer geben ihr Kennwort auf der Benutzeroberfläche für die Anmeldung ein. Die Benutzeroberfläche für die Anmeldung (LogonUI) übergibt die Anmeldeinformationen in einem Authentifizierungspuffer an die lokale Sicherheitsautorität, von der sie wiederum intern an CloudAP übergeben werden. CloudAP leitet diese Anforderung an das CloudAP-Plug-In weiter. |
| B | Wenn sich der Benutzer bereits vorher am Gerät angemeldet hat, initiiert Windows die zwischengespeicherte Anmeldung und überprüft die Anmeldeinformationen, um die Anmeldung für den Benutzer durchzuführen. Alle vier Stunden initiiert das CloudAP-Plug-In asynchron die PRT-Verlängerung. |
| C | Das CloudAP-Plug-In initiiert eine Bereichsermittlungsanforderung, um den Identitätsanbieter für den Benutzer zu identifizieren. Wenn für den Mandanten des Benutzers ein Verbundanbieter eingerichtet wurde, gibt Azure AD den Metadatenaustausch-Endpunkt (MEX-Endpunkt) des Verbundanbieters zurück. Wenn nicht, gibt Azure AD den Hinweis zurück, dass der Benutzer verwaltet wird und die Authentifizierung mit Azure AD durchführen kann. |
| D | Falls der Benutzer Teil eines Verbunds ist, fordert das CloudAP-Plug-In ein SAML-Token mit den Anmeldeinformationen des Benutzers vom Verbundanbieter an. Nach Erhalt des SAML-Tokens fordert es eine Nonce von Azure AD an. Wenn der Benutzer verwaltet wird, erhält CloudAP die Nonce direkt von Azure AD. |
| E | Das CloudAP-Plug-In erstellt die Authentifizierungsanforderung mit den Anmeldeinformationen des Benutzers, einer Nonce und dem vorhandenen PRT, signiert die Anforderung mit dem Sitzungsschlüssel und sendet sie an Azure AD. In einer Verbundumgebung nutzt das CloudAP-Plug-In nicht die Anmeldeinformationen des Benutzers, sondern das vom Verbundanbieter zurückgegebene SAML-Token. |
| F | Azure AD überprüft die Signatur des Sitzungsschlüssels, indem sie mit dem Sitzungsschlüssel verglichen wird, der in das PRT eingebettet ist. Anschließend wird die Nonce überprüft und sichergestellt, dass das Gerät im Mandanten gültig ist, und es wird ein neues PRT ausgestellt. Wie bereits beschrieben, verfügt das PRT auch hier wieder über den Sitzungsschlüssel, der mit dem Transportschlüssel (tkpub) verschlüsselt ist. |
| G | Das CloudAP-Plug-In übergibt das verschlüsselte PRT und den Sitzungsschlüssel an CloudAP. CloudAP fordert vom TPM die Entschlüsselung des Sitzungsschlüssels mit dem Transportschlüssel (tkpriv) und die erneute Verschlüsselung mit dem eigenen Schlüssel des TPM an. CloudAP speichert den verschlüsselten Sitzungsschlüssel zusammen mit dem PRT in seinem Cache. |

> [!NOTE]
> Ein PRT kann extern verlängert werden, ohne dass eine VPN-Verbindung erforderlich ist, wenn „usernamemixed“-Endpunkte extern aktiviert werden.

### <a name="prt-usage-during-app-token-requests"></a>PRT-Nutzung bei App-Tokenanforderungen

![PRT-Nutzung bei App-Tokenanforderungen](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Schritt | BESCHREIBUNG |
| :---: | --- |
| Ein | Eine Anwendung (z. B. Outlook, OneNote usw.) initiiert das Senden einer Tokenanforderung an WAM. Von WAM erhält das Azure AD-WAM-Plug-In wiederum die Aufforderung, die Tokenanforderung zu verarbeiten. |
| B | Wenn für die Anwendung bereits ein Aktualisierungstoken verfügbar ist, wird es vom Azure AD-WAM-Plug-In verwendet, um ein Zugriffstoken anzufordern. Um die Gerätebindung nachzuweisen, wird die Anforderung vom WAM-Plug-In mit dem Sitzungsschlüssel signiert. Azure AD überprüft den Sitzungsschlüssel und stellt ein Zugriffstoken und ein neues Aktualisierungstoken mit Verschlüsselung per Sitzungsschlüssel für die App aus. Das WAM-Plug-In fordert das CloudAP-Plug-In auf, die Token zu entschlüsseln. Das CloudAP-Plug-In fordert wiederum das TPM auf, die Entschlüsselung mit dem Sitzungsschlüssel durchzuführen, sodass das WAM-Plug-In beide Token erhält. Als Nächstes stellt das WAM-Plug-In nur das Zugriffstoken für die Anwendung bereit, während es das Aktualisierungstoken per DPAPI wieder verschlüsselt und im eigenen Cache speichert.  |
| C |  Wenn für die Anwendung kein Aktualisierungstoken verfügbar ist, verwendet das Azure AD-WAM-Plug-In das PRT, um ein Zugriffstoken anzufordern. Als Eigentumsnachweis signiert das WAM-Plug-In die Anforderung, die das PRT enthält, mit dem Sitzungsschlüssel. Azure AD überprüft die Sitzungsschlüsselsignatur, indem sie mit dem Sitzungsschlüssel verglichen wird, der in das PRT eingebettet ist. Anschließend wird überprüft, ob das Gerät gültig ist, und für die Anwendung werden ein Zugriffstoken und ein Aktualisierungstoken ausgestellt. Außerdem kann Azure AD ein neues PRT ausstellen (basierend auf dem Aktualisierungszyklus), wobei alle Token mit dem Sitzungsschlüssel verschlüsselt werden. |
| D | Das WAM-Plug-In fordert das CloudAP-Plug-In auf, die Token zu entschlüsseln. Das CloudAP-Plug-In fordert wiederum das TPM auf, die Entschlüsselung mit dem Sitzungsschlüssel durchzuführen, sodass das WAM-Plug-In beide Token erhält. Als Nächstes stellt das WAM-Plug-In nur das Zugriffstoken für die Anwendung bereit, während es das Aktualisierungstoken per DPAPI wieder verschlüsselt und im eigenen Cache speichert. Das WAM-Plug-In nutzt das Aktualisierungstoken ab jetzt für diese Anwendung. Darüber hinaus gibt das WAM-Plug-In das neue PRT an das CloudAP-Plug-In zurück, von dem das PRT mit Azure AD überprüft wird, bevor der eigene Cache aktualisiert wird. Das CloudAP-Plug-In nutzt ab jetzt das neue PRT. |
| E | Das neu ausgestellte Zugriffstoken wird für WAM bereitgestellt, von wo es wiederum an die aufrufende Anwendung zurückgegeben wird.|

### <a name="browser-sso-using-prt"></a>Browser-SSO mit PRT

![Browser-SSO mit PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Schritt | BESCHREIBUNG |
| :---: | --- |
| Ein | Der Benutzer meldet sich mit seinen Anmeldeinformationen an Windows an, um ein PRT zu erhalten. Nachdem der Benutzer den Browser geöffnet hat, werden die URLs vom Browser (bzw. der Erweiterung) aus der Registrierung geladen. |
| B | Wenn ein Benutzer eine Azure AD-Anmelde-URL öffnet, überprüft der Browser bzw. die Erweiterung die URL mit den URLs aus der Registrierung. Wenn sie übereinstimmen, ruft der Browser den nativen Clienthost für den Tokenabruf auf. |
| C | Der native Clienthost überprüft, ob die URLs zu den Microsoft-Identitätsanbietern gehören (Microsoft-Konto oder Azure AD), extrahiert eine von der URL gesendete Nonce und sendet einen Aufruf zum Beschaffen eines PRT-Cookies an das CloudAP-Plug-In. |
| D | Das CloudAP-Plug-In erstellt das PRT-Cookie, signiert es mit dem an TPM gebundenen Sitzungsschlüssel und sendet es zurück an den nativen Clienthost. Da das Cookie mit dem Sitzungsschlüssel signiert ist, kann es nicht manipuliert werden. |
| E | Der native Clienthost gibt dieses PRT-Cookie an den Browser zurück, der es in den Anforderungsheader „x-ms-RefreshTokenCredential“ einfügt und Token von Azure AD anfordert. |
| F | Azure AD überprüft die Sitzungsschlüsselsignatur im PRT-Cookie, überprüft die Nonce, stellt sicher, dass das Gerät im Mandanten gültig ist, und stellt ein ID-Token für die Webseite und ein verschlüsseltes Sitzungscookie für den Browser aus. |

> [!NOTE]
> Der in den obigen Schritten beschriebene Browser-SSO-Flow gilt nicht für Sitzungen in privaten Modi wie InPrivate in Microsoft Edge oder Incognito in Google Chrome (bei Verwendung der Erweiterung für Microsoft-Konten).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Behandlung von PRT-Problemen finden Sie im Artikel [Beheben von Problemen mit Geräten unter Windows 10 und Windows Server 2016 mit Hybrideinbindung in Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).
