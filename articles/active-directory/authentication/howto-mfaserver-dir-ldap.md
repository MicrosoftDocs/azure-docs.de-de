---
title: LDAP-Authentifizierung und Azure MFA Server – Azure Active Directory
description: Bereitstellen von LDAP-Authentifizierung und Azure Multi-Factor Authentication-Server.
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
ms.openlocfilehash: 8961cccbd57294e477f0d33202fe91cd292b7814
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742220"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-Authentifizierung und Azure Multi-Factor Authentication-Server

Standardmäßig ist der Azure Multi-Factor Authentication-Server zum Importieren oder Synchronisieren von Benutzern aus Active Directory konfiguriert. Er kann jedoch auch zum Binden an andere LDAP-Verzeichnisse konfiguriert sein, z. B. an ein ADAM-Verzeichnis oder einen bestimmten Active Directory-Domänencontroller. Wenn die Verbindung mit einem Verzeichnis über LDAP besteht, kann der Azure Multi-Factor Authentication-Server als LDAP-Proxy zur Ausführung von Authentifizierungen fungieren. Dies ermöglicht auch die Verwendung einer LDAP-Bindung als RADIUS-Ziel für die Vorauthentifizierung von Benutzern bei der IIS-Authentifizierung oder für die primäre Authentifizierung im Azure MFA-Benutzerportal.

Zum Verwenden der Multi-Factor Authentication als LDAP-Proxy fügen Sie den Azure Multi-Factor Authentication-Server zwischen dem LDAP-Client (beispielsweise VPN-Appliance, Anwendung) und dem LDAP-Verzeichnisserver ein. Der Azure Multi-Factor Authentication-Server muss für die Kommunikation sowohl mit den Clientservern als auch dem LDAP-Verzeichnis konfiguriert werden. In dieser Konfiguration akzeptiert der Azure Multi-Factor Authentication-Server LDAP-Anforderungen von Clientservern und -anwendungen und leitet sie zur Überprüfung der primären Anmeldeinformationen an den Ziel-LDAP-Verzeichnisserver weiter. Wenn das LDAP-Verzeichnis die primären Anmeldeinformationen validiert, führt Azure Multi-Factor Authentication eine zweite Identitätsüberprüfung durch und sendet eine Antwort zurück an den LDAP-Client. Die gesamte Authentifizierung ist nur erfolgreich, wenn sowohl die LDAP-Serverauthentifizierung als auch die Überprüfung in zwei Schritten erfolgreich ist.

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die für die Anmeldung der Benutzer mehrstufige Authentifizierung (MFA) anfordern möchten, sollten cloudbasierte Multi-Factor Authentication von Azure AD verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

## <a name="configure-ldap-authentication"></a>Konfigurieren der LDAP-Authentifizierung

Zum Konfigurieren der LDAP-Authentifizierung installieren Sie den Azure Multi-Factor Authentication-Server auf einem Windows-Server. Gehen Sie dazu wie folgt vor:

### <a name="add-an-ldap-client"></a>Hinzufügen eines LDAP-Clients

1. Wählen Sie im Azure Multi-Factor Authentication-Server im linken Menü das Symbol „LDAP-Authentifizierung“.
2. Aktivieren Sie das Kontrollkästchen **LDAP-Authentifizierung aktivieren**.

   ![LDAP-Authentifizierung in MFA Server](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Ändern Sie auf der Registerkarte „Clients“ den TCP- und SSL (TLS)-Port, wenn der Azure Multi-Factor Authentication-LDAP-Dienst an andere Ports als Standardports gebunden werden soll, um auf LDAP-Anforderungen zu lauschen.
4. Wenn Sie die LDAPS-Kommunikation vom Client zum Azure Multi-Factor Authentication-Server verwenden möchten, muss ein TLS/SSL-Zertifikat auf demselben Server wie der MFA-Server installiert sein. Klicken Sie neben dem Feld „SSL (TLS)-Zertifikat“ auf **Durchsuchen**, und wählen Sie ein Zertifikat aus, das für die sichere Verbindung verwendet werden soll.
5. Klicken Sie auf **Hinzufügen**.
6. Geben Sie im Dialogfeld „LDAP-Client hinzufügen“ die IP-Adresse der Appliance, des Servers oder der Anwendung, die auf dem Server authentifiziert wird, und einen Anwendungsnamen (optional) ein. Der Anwendungsname wird in Azure Multi-Factor Authentication-Berichten und möglicherweise auch in Authentifizierungsnachrichten in SMS oder der mobilen App angezeigt.
7. Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication-Benutzerabgleich erfordern**, wenn alle Benutzer in den Server importiert wurden oder werden und die zweistufige Überprüfung verwendet werden soll. Wenn eine hohe Anzahl von Benutzern noch nicht in den Server importiert wurde und/oder von der zweistufigen Überprüfung ausgenommen werden soll, lassen Sie das Kontrollkästchen deaktiviert. Weitere Informationen zu dieser Funktion finden Sie in der Hilfedatei zum MFA-Server.

Wiederholen Sie diese Schritte, um weitere LDAP-Clients hinzuzufügen.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurieren der LDAP-Verzeichnisverbindung

Wenn die Multi-Factor Authentication zum Empfangen von LDAP-Authentifizierungen konfiguriert ist, müssen diese Authentifizierungen per Proxy an das LDAP-Verzeichnis weitergegeben werden. Daher zeigt die Registerkarte "Ziel" nur eine einzelne, grau unterlegte Option zum Verwenden eines LDAP-Ziels an.

> [!NOTE]
> Die Verzeichnisintegration funktioniert mit anderen Verzeichnissen als Active Directory Domain Services möglicherweise nicht.

1. Zum Konfigurieren der LDAP-Verzeichnisverbindung klicken Sie auf das Symbol **Verzeichnisintegration**.
2. Aktivieren Sie auf der Registerkarte „Einstellungen“ das Optionsfeld **Bestimmte LDAP-Konfiguration verwenden**.
3. Wählen Sie **Bearbeiten…** .
4. Füllen Sie im Dialogfeld "LDAP-Konfiguration bearbeiten" die Felder mit den Informationen aus, die für die Verbindung mit dem LDAP-Verzeichnis erforderlich sind. Beschreibungen der Felder sind in der Hilfedatei zum Azure Multi-Factor Authentication-Server enthalten.

    ![LDAP-Konfiguration für Verzeichnisintegration](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Testen Sie die LDAP-Verbindung durch Klicken auf die Schaltfläche **Testen**.
6. Wenn der Test der LDAP-Verbindung erfolgreich war, klicken Sie auf die Schaltfläche **OK**.
7. Klicken Sie auf die Registerkarte **Filter**. Der Server ist für das Laden von Containern, Sicherheitsgruppen und Benutzern aus Active Directory vorkonfiguriert. Wenn Sie eine Bindung an ein anderes LDAP-Verzeichnis benötigen, müssen Sie wahrscheinlich die angezeigten Filter bearbeiten. Klicken Sie auf den Link **Hilfe**, um weitere Informationen zu Filtern zu erhalten.
8. Klicken Sie auf die Registerkarte **Attribute** . Der Server ist so vorkonfiguriert, dass Attribute aus Active Directory zugeordnet werden.
9. Bei einer Bindung an ein anderes LDAP-Verzeichnis oder bei Änderung der vorkonfigurierten Attributzuordnungen klicken Sie auf **Bearbeiten...** .
10. Ändern Sie im Dialogfeld "Attribute bearbeiten" die LDAP-Attributzuordnungen für Ihr Verzeichnis. Attributnamen können eingegeben oder ausgewählt werden, indem Sie auf die Schaltfläche **...** neben jedem Feld klicken. Klicken Sie auf den Link **Hilfe**, um weitere Informationen zu Attributen zu erhalten.
11. Klicken Sie auf die Schaltfläche **OK** .
12. Klicken Sie auf das Symbol **Unternehmenseinstellungen**, und wählen Sie die Registerkarte **Benutzernamenauflösung** aus.
13. Wenn Sie von einem in die Domäne eingebundenen Server eine Verbindung mit Active Directory herstellen, sollten Sie das Optionsfeld **Windows-Sicherheits-IDs (SIDs) für den Abgleich von Benutzernamen verwenden** aktiviert lassen. Wählen Sie andernfalls das Optionsfeld **Eindeutiges LDAP-Bezeichnerattribut für den Abgleich von Benutzernamen verwenden** aus.

Wenn das Optionsfeld **Eindeutiges LDAP-Bezeichnerattribut für den Abgleich von Benutzernamen verwenden** ausgewählt ist, versucht der Azure Multi-Factor Authentication-Server, jeden Benutzernamen in einem eindeutigen Bezeichner im LDAP-Verzeichnis aufzulösen. Eine LDAP-Suche wird für die Benutzernamenattribute durchgeführt, die auf der Registerkarte „Verzeichnisintegration“ > „Attribute“ definiert werden. Wenn ein Benutzer authentifiziert wird,wird der Benutzername in den eindeutigen Bezeichner im LDAP-Verzeichnis aufgelöst. Anhand des eindeutigen Bezeichners wird der Benutzer in der Azure Multi-Factor Authentication-Datendatei zugeordnet. Dies ermöglicht Vergleiche ohne Berücksichtigung der Groß-/Kleinschreibung sowie lange und kurze Formate von Benutzernamen.

Nachdem Sie diese Schritte ausgeführt haben, lauscht der MFA-Server an den konfigurierten Ports auf LDAP-Zugriffsanforderungen von den konfigurierten Clients und fungiert als Proxy für diese Anfragen zur Authentifizierung an das LDAP-Verzeichnis.

## <a name="configure-ldap-client"></a>Konfigurieren des LDAP-Clients

Verwenden Sie die folgenden Richtlinien, um den LDAP-Client zu konfigurieren:

* Konfigurieren Sie Ihre Appliance, den Server oder die Anwendung so, dass die Authentifizierung über LDAP an den Azure Multi-Factor Authentication-Server stattfindet, als wäre es Ihr LDAP-Verzeichnis. Verwenden Sie die gleichen Einstellungen, die Sie normalerweise für die direkte Verbindung mit Ihrem LDAP-Verzeichnis verwenden würden, mit Ausnahme des Servernamens oder der IP-Adresse, die dem Azure Multi-Factor Authentication-Server entsprechen.
* Legen Sie die Konfiguration für das LDAP-Timeout auf 30 bis 60 Sekunden fest, damit genug Zeit ist, die Anmeldeinformationen des Benutzers mit dem LDAP-Verzeichnis zu überprüfen, die Überprüfung in zwei Schritten durchzuführen, deren Antwort zu empfangen und auf die LDAP-Zugriffsanforderung zu reagieren.
* Bei Verwendung der LDAPS-Kommunikation muss die Appliance oder der Server, die bzw. der LDAP-Abfragen ausgibt, dem auf dem Azure Multi-Factor Authentication-Server installierten TLS/SSL-Zertifikat vertrauen.
