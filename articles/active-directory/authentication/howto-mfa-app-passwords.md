---
title: Konfigurieren von App-Kennwörtern für Azure AD Multi-Factor Authentication – Azure Active Directory
description: Hier erfahren Sie, wie Sie App-Kennwörter für ältere Anwendungen in Azure AD Multi-Factor Authentication konfigurieren und verwenden.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743104"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Aktivieren und Verwenden von Azure AD Multi-Factor Authentication bei älteren Anwendungen mithilfe von App-Kennwörtern

Einige ältere, nicht browserbasierte Apps – etwa Office 2010 oder ältere Versionen und Apple Mail vor iOS 11 – kennen keine Pausen oder Unterbrechungen im Authentifizierungsvorgang. Wenn Azure AD Multi-Factor Authentication für einen Benutzer aktiviert wurde und der Benutzer versucht, eine dieser älteren, nicht browserbasierten Apps zu verwenden, kann er sich nicht erfolgreich authentifizieren. Wenn Sie diese Anwendungen auf sichere Weise mit Azure AD Multi-Factor Authentication (das für die Benutzerkonten aktiviert wurde) verwenden möchten, können Sie App-Kennwörter nutzen. Diese App-Kennwörter ersetzen das herkömmliche Kennwort und ermöglichen der App, die mehrstufige Authentifizierung zu umgehen und ordnungsgemäß zu funktionieren.

Die moderne Authentifizierung für Microsoft Office 2013-Clients und neuere Clients wird unterstützt. Office 2013-Clients (einschließlich Outlook) unterstützen moderne Authentifizierungsprotokolle und können für die zweistufige Überprüfung aktiviert werden. Nach der Aktivierung sind für diese Clients keine App-Kennwörter erforderlich.

In diesem Artikel erfahren Sie, wie Sie App-Kennwörter für ältere Anwendungen aktivieren und verwenden, die keine Eingabeaufforderungen für die mehrstufige Authentifizierung unterstützen.

>[!NOTE]
> App-Kennwörter funktionieren nicht mit auf bedingtem Zugriff basierenden Multi-Factor Authentication-Richtlinien und moderner Authentifizierung.

## <a name="overview-and-considerations"></a>Übersicht und Überlegungen

Wenn ein Benutzerkonto für Azure AD Multi-Factor Authentication aktiviert wurde, wird die reguläre Anmeldeaufforderung durch eine Anforderung zur zusätzlichen Überprüfung unterbrochen. Einige ältere Anwendungen verstehen diese Unterbrechung beim Anmeldevorgang nicht, sodass die Authentifizierung fehlschlägt. Wenn die Sicherheit von Benutzerkonten weiterhin gewährleistet und die Aktivierung von Azure AD Multi-Factor Authentication beibehalten werden soll, können statt des normalen Benutzernamens und Kennworts des Benutzers App-Kennwörter verwendet werden. Wenn bei der Anmeldung ein App-Kennwort verwendet wird, gibt es keine zusätzliche Überprüfungsaufforderung, sodass die Authentifizierung erfolgreich ist.

App-Kennwörter werden automatisch generiert und nicht vom Benutzer angegeben. Automatisch generierte Kennwörter sind für einen Angreifer schwerer zu erraten und daher sicherer. Benutzer müssen sich die Kennwörter nicht merken oder sie jedes Mal eingeben, da App-Kennwörter nur einmal pro Anwendung eingegeben werden.

Beim Verwenden von App-Kennwörtern sind folgende Überlegungen zu beachten:

* Pro Benutzer können maximal 40 App-Kennwörter festgelegt werden.
* Bei Anwendungen, die Kennwörter zwischenspeichern und in lokalen Szenarien nutzen, können Fehler auftreten, da das App-Kennwort außerhalb des Geschäfts-, Schul- oder Unikontos nicht bekannt ist. Ein Beispiel dafür sind Exchange-E-Mails, die lokal vorhanden sind, bei denen sich die archivierten Nachrichten jedoch in der Cloud befinden. Das gleiche Kennwort funktioniert in diesem Fall nicht.
* Nachdem Azure AD Multi-Factor Authentication für ein Benutzerkonto aktiviert wurde, können App-Kennwörter für die meisten nicht auf Browsern basierenden Clients wie Outlook oder Microsoft Skype for Business verwendet werden. Bei Verwendung von App-Kennwörtern können jedoch keine administrativen Aufgaben durch nicht auf Browsern basierende Anwendungen (z. B. Windows PowerShell) durchgeführt werden. Die Aufgaben können selbst dann nicht durchgeführt werden, wenn der betreffende Benutzer über ein administratives Konto verfügt.
    * Erstellen Sie zum Ausführen von PowerShell-Skripts ein Dienstkonto mit einem sicheren Kennwort, und aktivieren Sie für das Konto nicht die zweistufige Überprüfung.
* Wenn Sie vermuten, dass ein Benutzerkonto gefährdet ist, und das Kontokennwort widerrufen bzw. zurücksetzen, sollten die App-Kennwörter ebenfalls aktualisiert werden. App-Kennwörter werden nicht automatisch widerrufen, wenn das Kennwort eines Benutzerkontos widerrufen bzw. zurückgesetzt wird. Der Benutzer sollte vorhandene App-Kennwörter löschen und neue erstellen.
   * Weitere Informationen finden Sie unter [Erstellen und Löschen von App-Kennwörtern auf der Seite „Zusätzliche Sicherheitsüberprüfung“](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

>[!WARNING]
> App-Kennwörter funktionieren nicht in Hybridumgebungen, in denen Clients sowohl mit lokalen AutoErmittlung-Endpunkten als auch mit solchen in der Cloud kommunizieren. Für die lokale Authentifizierung werden Domänenkennwörter benötigt. Für die Authentifizierung in der Cloud sind App-Kennwörter erforderlich.

### <a name="app-password-names"></a>Namen von App-Kennwörtern

Die Namen der App-Kennwörter sollten auf das Gerät hinweisen, auf dem sie verwendet werden. Für einen Laptop, der über Nicht-Browseranwendungen wie Outlook, Word und Excel verfügt, erstellen Sie ein App-Kennwort mit dem Namen **Laptop** für diese Anwendungen. Erstellen Sie ein weiteres App-Kennwort namens **Desktop** für die gleichen auf dem Desktopcomputer ausgeführten Anwendungen.

Es empfiehlt sich, ein App-Kennwort pro Gerät und nicht pro Anwendung zu erstellen.

## <a name="federated-or-single-sign-on-app-passwords"></a>App-Kennwörter im Verbund oder mit einmaligem Anmelden

Azure AD unterstützt den Verbund bzw. das einmalige Anmelden (SSO) mit lokalen Active Directory Domain Services (AD DS). Wenn Ihre Organisation einen Verbund mit Azure AD bildet und Sie Azure AD Multi-Factor Authentication verwenden, sind die folgenden Punkte zu App-Kennwörtern zu beachten:

>[!NOTE]
> Die folgenden Punkte gelten nur für Verbundkunden (SSO).

* App-Kennwörter werden von Azure AD überprüft, sodass der Verbund umgangen wird. Der Verbund wird nur beim Einrichten von App-Kennwörtern aktiv verwendet.
* Im Gegensatz zum passiven Ablauf erfolgt für Verbundbenutzer (SSO) keine Kontaktaufnahme mit dem Identitätsanbieter (Identity Provider, IdP). Die App-Kennwörter werden im Geschäfts-, Schul- oder Unikonto gespeichert. Wenn ein Benutzer das Unternehmen verlässt, werden die Benutzerinformationen mithilfe von **DirSync** in Echtzeit in das Geschäfts-, Schul- oder Unikonto übertragen. Die Synchronisierung nach dem Deaktivieren oder Löschen des Kontos kann bis zu drei Stunden dauern, wodurch sich auch das Deaktivieren bzw. Löschen des App-Kennworts in Azure AD verzögern kann.
* Lokale Einstellungen für die Clientzugriffssteuerung werden vom Feature für App-Kennwörter nicht berücksichtigt.
* Zusammen mit der App-Kennwortfunktion ist keine lokale Funktion zur Protokollierung oder Überwachung der Authentifizierung verfügbar.

In bestimmten erweiterten Architekturen ist für die mehrstufige Authentifizierung bei Clients eine Kombination aus Anmeldeinformationen erforderlich. Diese Anmeldeinformationen können den Benutzernamen und Kennwörter eines Geschäfts-, Schul- oder Unikontos und App-Kennwörter umfassen. Die jeweiligen Anforderungen hängen davon ab, wie die Authentifizierung durchgeführt wird. Bei Clients, die sich bei einer lokalen Infrastruktur authentifizieren, sind der Benutzername und das Kennwort eines Geschäfts-, Schul- oder Unikontos erforderlich. Für Clients, die sich bei Azure AD authentifizieren, wird ein App-Kennwort benötigt.

Angenommen, Sie verfügen über die folgende Architektur:

* Ihre lokale Instanz von Active Directory bildet einen Verbund mit Azure AD.
* Sie verwenden Exchange Online.
* Sie verwenden Skype for Business lokal.
* Sie verwenden Azure AD Multi-Factor Authentication.

In diesem Szenario verwenden Sie die folgenden Anmeldeinformationen:

* Verwenden Sie für die Anmeldung bei Skype for Business den Benutzernamen und das Kennwort für Ihr Geschäfts-, Schul- oder Unikonto.
* Verwenden Sie ein App-Kennwort für den Zugriff auf das Adressbuch über einen Outlook-Client, der die Verbindung mit Exchange Online herstellt.

## <a name="allow-users-to-create-app-passwords"></a>Zulassen der Erstellung von App-Kennwörtern durch Benutzer

Standardmäßig können Benutzer keine App-Kennwörter erstellen. Die App-Kennwortfunktion muss aktiviert werden, bevor Benutzer sie verwenden können. Führen Sie die folgenden Schritte aus, um Benutzern die Erstellung von App-Kennwörtern zu ermöglichen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie nach **Azure Active Directory**, wählen Sie diese Option aus, und wählen Sie dann **Benutzer** aus.
3. Wählen Sie in der Navigationsleiste im oberen Teil des Fensters **Benutzer** die Option *Multi-Factor Authentication* aus.
4. Klicken Sie unter „Multi-Factor Authentication“ auf **Diensteinstellungen**.
5. Wählen Sie auf der Seite **Diensteinstellungen** die Option **Benutzern das Erstellen von App-Kennwörtern zum Anmelden bei nicht browserbasierten Apps gestatten** aus.

    ![Screenshot des Azure-Portals mit den Diensteinstellungen für die mehrstufige Authentifizierung und der Erlaubnis für den Benutzer, App-Kennwörter zu erstellen](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> Wenn Sie das Erstellen von App-Kennwörtern durch Benutzer deaktivieren, sind vorhandene App-Kennwörter weiterhin funktionsfähig. Benutzer können diese vorhandenen App-Kennwörter jedoch weder verwalten noch löschen, nachdem Sie diese Option deaktiviert haben.
>
> Wenn Sie die Möglichkeit zum Erstellen von App-Kennwörtern deaktivieren, empfiehlt es sich auch, [eine Richtlinie für bedingten Zugriff zu erstellen, um die Verwendung der Legacy Authentifizierung zu deaktivieren](../conditional-access/block-legacy-authentication.md). Diese Vorgehensweise verhindert, dass vorhandene App-Kennwörter weiterhin funktionsfähig sind. Die Verwendung moderner Authentifizierungsmethoden wird erzwungen.

## <a name="create-an-app-password"></a>Erstellen eines App-Kennworts

Wenn Benutzer ihre anfängliche Registrierung für Azure AD Multi-Factor Authentication abgeschlossen haben, können sie am Ende des Registrierungsvorgangs App-Kennwörter erstellen.

Benutzer können auch App-Kennwörter nach der Registrierung erstellen. Weitere Informationen und detaillierte Schritte für Ihre Benutzer finden Sie unter [Welchen Zweck erfüllen App-Kennwörter bei Azure AD Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur schnellen Registrierung durch Benutzer für Azure AD Multi-Factor Authentication finden Sie unter [Kombinierte Registrierung von Sicherheitsinformationen – Übersicht](concept-registration-mfa-sspr-combined.md).
