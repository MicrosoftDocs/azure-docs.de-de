---
title: IIS-Authentifizierung und Azure MFA Server – Azure Active Directory
description: Bereitstellen von IIS-Authentifizierung und Azure Multi-Factor Authentication-Server
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c522fb950e2489afce44553d233ac3eddf138730
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742169"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Konfigurieren des Azure Multi-Factor Authentication-Servers für IIS-Web-Apps

Verwenden Sie den Bereich „IIS-Authentifizierung“ des Azure Multi-Factor Authentication-Servers (MFA), um die IIS-Authentifizierung für die Integration mit Microsoft IIS-Webanwendungen zu aktivieren und zu konfigurieren. Vom Azure MFA-Server wird ein Plug-In installiert, mit dem an den IIS-Webserver gesendete Anforderungen gefiltert werden können, um Azure Multi-Factor Authentication hinzuzufügen. Das IIS-Plug-In bietet Unterstützung für die formularbasierte Authentifizierung und die integrierte Windows-HTTP-Authentifizierung. Vertrauenswürdige IP-Adressen können auch konfiguriert werden, um interne IP-Adressen der zweistufigen Authentifizierung auszunehmen.

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die für die Anmeldung der Benutzer mehrstufige Authentifizierung (MFA) anfordern möchten, sollten cloudbasierte Multi-Factor Authentication von Azure AD verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.
>
> Wenn Sie die cloudbasierte Azure-Multi-Factor Authentication verwenden, gibt es keine Alternative zum IIS-Plug-In, das von Azure Multi-Factor Authentication (MFA)-Server bereitgestellt wird. Verwenden Sie stattdessen den Webanwendungsproxy (WAP) mit Active Directory-Verbunddiensten (AD FS) oder den Azure Active Directory-Anwendungsproxy.

![IIS-Authentifizierung in MFA Server](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Verwenden formularbasierter IIS-Authentifizierung mit dem Azure Multi-Factor Authentication-Server

Um eine IIS-Webanwendung zu sichern, die die formularbasierte Authentifizierung verwendet, installieren Sie den Azure Multi-Factor Authentication-Server auf dem IIS-Webserver, und konfigurieren Sie den Server wie im folgenden Verfahren dargestellt:

1. Klicken Sie auf dem Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol „IIS-Authentifizierung“.
2. Klicken Sie auf die Registerkarte **Formularbasiert**.
3. Klicken Sie auf **Hinzufügen**.
4. Geben Sie zur automatischen Erkennung von Benutzername, Kennwort und Domänenvariablen im Dialogfeld „Formularbasierte Website automatisch konfigurieren“ die Anmelde-URL (beispielsweise `https://localhost/contoso/auth/login.aspx`) ein, und klicken Sie auf **OK**.
5. Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication-Benutzerabgleich erfordern**, wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.
6. Wenn die Seitenvariablen nicht automatisch erkannt werden können, klicken Sie im Dialogfeld „Formularbasierte Website automatisch konfigurieren“ auf **Manuell angeben**.
7. Geben Sie im Dialogfeld "Formularbasierte Website hinzufügen" im Feld "Sende-URL" die URL der Anmeldeseite und optional einen Anwendungsnamen ein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
8. Wählen Sie das richtige Anforderungsformat. Dieses ist für die meisten Webanwendungen auf **POST oder GET** festgelegt.
9. Geben Sie die Variablen für den Benutzernamen, das Kennwort und die Domäne ein (sofern auf der Anmeldeseite angezeigt). Navigieren Sie in einem Webbrowser zur Anmeldeseite, klicken Sie mit der rechten Maustaste auf die Seite, und wählen Sie **Quelltext anzeigen** aus, um die Namen der Eingabefelder zu ermitteln.
10. Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication-Benutzerabgleich erfordern**, wenn alle Benutzer in den Server importiert wurden bzw. werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.
11. Klicken Sie auf **Erweitert**, um die erweiterten Einstellungen anzuzeigen, z.B.:

    - Sie können eine Datei für eine benutzerdefinierte Verweigerungsseite auswählen.
    - Sie können erfolgreiche Authentifizierungen für die Website mithilfe von Cookies für einen bestimmten Zeitraum zwischenspeichern.
    - Geben Sie an, ob die Authentifizierung der primären Anmeldeinformationen für eine Windows-Domäne, ein LDAP-Verzeichnis oder einen RADIUS-Server durchgeführt werden soll.

12. Klicken Sie auf **OK**, um zum Dialogfeld „Formularbasierte Website hinzufügen“ zurückzukehren.
13. Klicken Sie auf **OK**.
14. Sobald die URL und die Seitenvariablen erkannt bzw. eingegeben wurden, werden die Websitedaten im Bereich „Formularbasiert“ angezeigt.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Verwenden der integrierten Windows-Authentifizierung mit dem Azure Multi-Factor Authentication-Server

Um eine IIS-Webanwendung zu sichern, für die die integrierte Windows-HTTP-Authentifizierung verwendet wird, installieren Sie den Azure MFA-Server auf dem IIS-Webserver und konfigurieren den Server dann mit den folgenden Schritten:

1. Klicken Sie auf dem Azure Multi-Factor Authentication-Server im linken Menü auf das Symbol „IIS-Authentifizierung“.
2. Klicken Sie auf die Registerkarte **HTTP**.
3. Klicken Sie auf **Hinzufügen**.
4. Geben Sie im Dialogfeld „Basis-URL hinzufügen“ die URL für die Website ein, auf der die HTTP-Authentifizierung erfolgt (beispielsweise `http://localhost/owa`), und geben Sie optional einen Anwendungsnamen an. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
5. Passen Sie die Leerlaufzeitüberschreitung und die maximale Sitzungsdauer an, wenn die Standardwerte nicht ausreichend sind.
6. Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication-Benutzerabgleich erfordern**, wenn alle Benutzer in den Server importiert wurden oder werden und die Multi-Factor Authentication verwenden. Wenn eine große Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der Multi-Factor Authentication ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert.
7. Aktivieren Sie bei Bedarf das Kontrollkästchen für das **Cookie zur** Zwischenspeicherung.
8. Klicken Sie auf **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Aktivieren des IIS-Plug-Ins für den Azure Multi-Factor Authentication-Server

Nachdem Sie die URLs und Einstellungen für die formularbasierte oder die HTTP-Authentifizierung konfiguriert haben, wählen Sie die Speicherorte aus, an denen die IIS-Plug-Ins von Azure Multi-Factor Authentication in IIS geladen und aktiviert werden sollen. Gehen Sie dazu wie folgt vor:

1. Klicken Sie bei Ausführung unter IIS 6 auf die Registerkarte **ISAPI**. Wählen Sie die Website aus, auf der die Webanwendung läuft (z.B. die Standardwebsite), um das ISAPI-Filter-Plug-In von Azure Multi-Factor Authentication für diese Website zu aktivieren.
2. Klicken Sie bei Ausführung unter IIS 7 oder höher auf die Registerkarte **Systemeigenes Modul**. Wählen Sie den Server, die Websites oder die Anwendungen aus, um das IIS-Plug-In auf den gewünschten Ebenen zu aktivieren.
3. Aktivieren Sie im oberen Bildschirmbereich das Kontrollkästchen **IIS-Authentifizierung aktivieren**. Die ausgewählte IIS-Anwendung wird jetzt mit Azure Multi-Factor Authentication gesichert. Stellen Sie sicher, dass die Benutzer in den Server importiert wurden.

## <a name="trusted-ips"></a>Vertrauenswürdige IP-Adressen

Über vertrauenswürdige IP-Adressen können Benutzer Azure Multi-Factor Authentication bei Websiteanforderungen umgehen, die von bestimmten IP-Adressen oder Subnetzen stammen. Beispielsweise können Benutzer, die sich vom Büro aus anmelden, von Azure Multi-Factor Authentication ausschließen. Dazu geben Sie das Bürosubnetz als Eintrag unter „Vertrauenswürdige IPs“ an. Verwenden Sie zum Konfigurieren von vertrauenswürdigen IP-Adressen das folgende Verfahren:

1. Klicken Sie im Abschnitt „IIS-Authentifizierung“ auf die Registerkarte **Vertrauenswürdige IPs**.
2. Klicken Sie auf **Hinzufügen**.
3. Im daraufhin angezeigten Dialogfeld „Vertrauenswürdige IP hinzufügen“ wählen Sie das Optionsfeld **Eine IP**, **IP-Bereich** oder **Subnetz** aus.
4. Geben Sie die IP-Adresse, den IP-Adressbereich oder das Subnetz ein, die zulässig sein sollen. Wählen Sie im Falle eines Subnetzes die entsprechende Netzmaske aus, und klicken Sie auf **OK**.
