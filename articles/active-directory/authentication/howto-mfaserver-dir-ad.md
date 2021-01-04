---
title: Azure MFA-Server und Active Directory – Azure Active Directory
description: Erfahren Sie, wie Sie den Azure Multi-Factor Authentication-Server in Active Directory integrieren, damit Sie die Verzeichnisse synchronisieren können.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b3778ea68edf1fbbb41efb899749e6f35b39bae
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742288"
---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Verzeichnisintegration zwischen Azure MFA-Server und Active Directory

Verwenden Sie den Abschnitt „Verzeichnisintegration“ des Azure MFA-Servers für die Integration in Active Directory oder ein anderes LDAP-Verzeichnis. Sie können Attribute für das Verzeichnisschema konfigurieren und die automatische Synchronisierung von Benutzern einrichten.

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die bei Benutzeranmeldeereignissen eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verlangen möchten, sollten cloudbasierte Azure AD Multi-Factor Authentication verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

## <a name="settings"></a>Einstellungen

Standardmäßig ist der Azure MFA-Server (Multi-Factor Authentication) zum Importieren oder Synchronisieren von Benutzern aus Active Directory konfiguriert.  Auf der Registerkarte „Verzeichnisintegration“ können Sie das Standardverhalten außer Kraft setzen und die Bindung an ein anderes LDAP-Verzeichnis, ADAM-Verzeichnis oder einen speziellen Active Directory-Domänencontroller festlegen.  Außerdem ist die Verwendung der LDAP-Authentifizierung als Proxy für LDAP oder für die LDAP-Bindung als RADIUS-Ziel, die Vorauthentifizierung für die IIS-Authentifizierung oder die primäre Authentifizierung für das Benutzerportal möglich.  In der folgenden Tabelle sind die einzelnen Einstellungen beschrieben.

![Bearbeiten der LDAP-Konfiguration im MFA-Server](./media/howto-mfaserver-dir-ad/dirint.png)

> [!NOTE]
> Die Verzeichnisintegration funktioniert mit anderen Verzeichnissen als Active Directory Domain Services möglicherweise nicht.

| Funktion | BESCHREIBUNG |
| --- | --- |
| Active Directory verwenden |Wählen Sie die Option „Active Directory verwenden“ aus, um Active Directory zum Importieren und Synchronisieren zu nutzen.  Dies ist die Standardeinstellung. <br>Hinweis: Damit die Active Directory-Integration richtig funktioniert, binden Sie den Computer in eine Domäne ein und melden sich mit einem Domänenkonto an. |
| Vertrauenswürdige Domänen einschließen |Aktivieren Sie **Vertrauenswürdige Domänen einschließen**. Der Agent versucht dann, eine Verbindung mit Domänen herzustellen, die von der aktuellen Domäne als vertrauenswürdig angesehen werden, oder mit einer anderen Domäne in der Gesamtstruktur bzw. mit an einer Gesamtstruktur-Vertrauensstellung beteiligten Domänen.  Wenn Sie keine Benutzer aus den vertrauenswürdigen Domänen importieren bzw. keine Synchronisierung dafür durchführen, sollten Sie das Kontrollkästchen deaktivieren, um die Leistung zu verbessern.  Diese Option ist standardmäßig aktiviert. |
| Bestimmte LDAP-Konfiguration verwenden |Wählen Sie die Option „LDAP verwenden“ aus, um die LDAP-Einstellungen zu verwenden, die für das Importieren und Synchronisieren angegeben sind. Hinweis: Wenn „LDAP verwenden“ ausgewählt ist, ändern sich auf der Benutzeroberfläche die Verweise von „Active Directory“ in „LDAP“. |
| Schaltfläche „Bearbeiten“ |Über die Schaltfläche „Bearbeiten“ können die aktuellen LDAP-Konfigurationseinstellungen geändert werden. |
| Attributbereichsabfragen verwenden |Gibt an, ob Attributbereichsabfragen verwendet werden sollen.  Attributbereichsabfragen ermöglichen effiziente Verzeichnissuchen, indem Datensätze basierend auf den Einträgen in einem Attribut eines anderen Datensatzes qualifiziert werden.  Der Azure Multi-Factor Authentication-Server verwendet Attributbereichsabfragen zum effizienten Abfragen der Benutzer, die Mitglied einer Sicherheitsgruppe sind.   <br>Hinweis:  Es gibt Fälle, in denen Attributbereichsabfragen zwar unterstützt werden, aber nicht verwendet werden sollten.  Für Active Directory können beispielsweise Probleme mit Attributbereichsabfragen auftreten, wenn eine Sicherheitsgruppe Mitglieder aus mehr als einer Domäne enthält. Deaktivieren Sie das Kontrollkästchen in diesem Fall. |

In der folgenden Tabelle sind die LDAP-Konfigurationseinstellungen beschrieben.

| Funktion | BESCHREIBUNG |
| --- | --- |
| Server |Geben Sie den Hostnamen oder die IP-Adresse des Servers ein, auf dem das LDAP-Verzeichnis ausgeführt wird.  Sie können auch einen Sicherungsserver durch ein Semikolon getrennt angeben. <br>Hinweis: Wenn der Bindungstyp SSL (TLS) lautet, ist ein vollqualifizierter Hostname erforderlich. |
| Basis-DN |Geben Sie den Distinguished Name des Basisverzeichnisobjekts an, von dem alle Verzeichnisabfragen ausgehen.  Beispiel: dc=abc,dc=com. |
| Bindungstyp – Abfragen |Wählen Sie den entsprechenden Bindungstyp für Bindungen zum Durchsuchen des LDAP-Verzeichnisses aus.  Dieser wird für Importe, die Synchronisierung und die Auflösung des Benutzernamens verwendet. <br><br>  Anonym: Es wird eine anonyme Bindung durchgeführt.  „Bindungs-DN“ und „Bindungskennwort“ werden nicht verwendet.  Dies funktioniert nur, wenn das LDAP-Verzeichnis die anonyme Bindung zulässt und Berechtigungen das Abfragen der entsprechenden Datensätze und Attribute zulassen.  <br><br> Einfach: Bindungs-DN und Bindungskennwort werden für die Bindung an das LDAP-Verzeichnis im Nur-Text-Format übergeben.  Dies ist für Testzwecke bestimmt, um zu überprüfen, ob der Server erreicht werden kann und das Bindungskonto über die erforderlichen Zugriffsrechte verfügt. Verwenden Sie stattdessen SSL, nachdem das entsprechende Zertifikat installiert wurde.  <br><br> SSL: Bindungs-DN und Bindungskennwort werden per SSL verschlüsselt, um die Bindung an das LDAP-Verzeichnis herzustellen.  Installieren Sie lokal ein Zertifikat, das für das LDAP-Verzeichnis vertrauenswürdig ist.  <br><br> Windows: Bindungsbenutzername und Bindungskennwort werden verwendet, um eine sichere Verbindung mit einem Active Directory-Domänencontroller oder ADAM-Verzeichnis herzustellen.  Wenn „Bindungsbenutzername“ leer gelassen wird, wird für die Bindung das Konto des angemeldeten Benutzers verwendet. |
| Bindungstyp – Authentifizierungen |Wählen Sie den passenden Bindungstyp aus, der beim Durchführen der LDAP-Bindungsauthentifizierung verwendet werden soll.  Beschreibungen der Bindungstypen finden Sie unter „Bindungstyp – Abfragen“.  Hiermit kann beispielsweise die Bindung „Anonym“ für Abfragen verwendet werden, während die SSL-Bindung zum Schützen von LDAP-Bindungsauthentifizierungen genutzt wird. |
| Bindungs-DN oder Bindungsbenutzername |Geben Sie den Distinguished Name des Benutzerdatensatzes für das Konto ein, der für die Bindung an das LDAP-Verzeichnis verwendet werden soll.<br><br>Der Bindungs-DN wird nur verwendet, wenn der Bindungstyp „Einfach“ oder „SSL“ lautet.  <br><br>Geben Sie den Benutzernamen des Windows-Kontos ein, der für die Bindung an das LDAP-Verzeichnis verwendet werden soll, wenn der Bindungstyp „Windows“ lautet.  Wenn dieses Feld leer gelassen wird, wird das Konto des angemeldeten Benutzers für die Bindung verwendet. |
| Bindungskennwort |Geben Sie das Bindungskennwort für den Bindungs-DN oder den Benutzernamen ein, der für die Bindung an das LDAP-Verzeichnis verwendet wird.  Um das Kennwort für den AdSync-Dienst des Multi-Factor Authentication-Servers zu konfigurieren, müssen Sie die Synchronisierung aktivieren und sicherstellen, dass der Dienst auf dem lokalen Computer ausgeführt wird.  Das Kennwort wird in den unter Windows gespeicherten Benutzernamen und Kennwörtern unter dem Konto gespeichert, unter dem der AdSync-Dienst des Multi-Factor Authentication-Servers ausgeführt wird.  Das Kennwort wird ebenfalls unter dem Konto gespeichert, unter dem die Benutzeroberfläche ausgeführt wird, sowie unter dem Konto, unter dem der Multi-Factor Authentication-Server-Dienst ausgeführt wird.  <br><br>Da das Kennwort nur in den unter Windows gespeicherten Benutzernamen und Kennwörtern des lokalen Servers gespeichert wird, müssen Sie diesen Schritt auf jedem Multi-Factor Authentication-Server ausführen, der Zugriff auf das Kennwort benötigt. |
| Abfragegrößenlimit |Geben Sie die maximale Anzahl von Benutzern an, die bei einer Suche im Verzeichnis zurückgegeben werden kann.  Dieser Grenzwert sollte mit der Konfiguration im LDAP-Verzeichnis übereinstimmen.  Bei umfangreichen Suchläufen, bei denen die Auslagerung nicht unterstützt wird, versuchen Import- und Synchronisierungsvorgänge, Benutzer stapelweise abzurufen.  Wenn Sie hier als Grenzwert einen höheren Wert als den im LDAP-Verzeichnis konfigurierten Grenzwert angeben, fehlen möglicherweise einige Benutzer. |
| Schaltfläche "Test" |Klicken Sie auf **Test**, um die Bindung an den LDAP-Server zu testen.  <br><br>Es ist nicht erforderlich, die Option **LDAP verwenden** zu wählen, um die Bindung zu testen. Dies ermöglicht das Testen der Bindung vor dem Verwenden der LDAP-Konfiguration. |

## <a name="filters"></a>Filter

Mit Filtern können Sie Kriterien zum Qualifizieren von Datensätzen festlegen, wenn Sie eine Verzeichnissuche durchführen.  Durch Festlegen des Filters können Sie den Bereich für die Objekte eingrenzen, die Sie synchronisieren möchten.  

![Konfigurieren der Verzeichnisfilterung im MFA-Server](./media/howto-mfaserver-dir-ad/dirint2.png)

Azure Multi-Factor Authentication verfügt über die folgenden drei Filteroptionen:

* **Containerfilter:** Geben Sie die Filterkriterien an, die beim Durchführen einer Verzeichnissuche zum Qualifizieren von Containerdatensätzen verwendet werden sollen.  Für Active Directory und ADAM wird normalerweise (|(objectClass=organizationalUnit)(objectClass=container)) verwendet.  Verwenden Sie je nach Verzeichnisschema für andere LDAP-Verzeichnisse Filterkriterien, mit denen die einzelnen Arten von Containerobjekten qualifiziert werden.  <br>Hinweis:  Wenn das Feld leer gelassen wird, wird standardmäßig „((objectClass=organizationalUnit)(objectClass=container))“ verwendet.
* **Sicherheitsgruppenfilter:** Geben Sie die Filterkriterien an, die beim Durchführen einer Verzeichnissuche zum Qualifizieren von Sicherheitsgruppendatensätzen verwendet werden sollen.  Für Active Directory und ADAM wird normalerweise (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) verwendet.  Für andere LDAP-Verzeichnisse sollten je nach Verzeichnisschema Filterkriterien, mit denen die einzelnen Arten von Sicherheitsgruppenobjekten qualifiziert werden, verwendet werden.  <br>Hinweis:  Wenn das Feld leer gelassen wird, wird standardmäßig „(&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648))“ verwendet.
* **Benutzerfilter:** Geben Sie die Filterkriterien an, die beim Durchführen einer Verzeichnissuche zum Qualifizieren von Benutzerdatensätzen verwendet werden sollen.  Für Active Directory und ADAM wird normalerweise (&(objectClass=user)(objectCategory=person)) verwendet.  Verwenden Sie für andere LDAP-Verzeichnisse je nach Verzeichnisschema (objectClass=inetOrgPerson) oder etwas Ähnliches. <br>Hinweis:  Wenn das Feld leer gelassen wird, wird standardmäßig „(&(objectCategory=person)(objectClass=user))“ verwendet.

## <a name="attributes"></a>Attributes

Sie können Attribute je nach Bedarf für ein bestimmtes Verzeichnis anpassen.  Dadurch können Sie benutzerdefinierte Attribute hinzufügen und die Synchronisierung nur mit den benötigten Attributen optimieren. Verwenden Sie den Namen des Attributs, der im Verzeichnisschema für den Wert jedes Attributfelds definiert ist. Die folgende Tabelle enthält zusätzliche Informationen zu den einzelnen Funktionen.

Attribute können manuell eingegeben werden und müssen nicht mit einem Attribut in der Attributliste übereinstimmen.

![Anpassen der Verzeichnisintegrationsattribute im MFA-Server](./media/howto-mfaserver-dir-ad/dirint3.png)

| Funktion | BESCHREIBUNG |
| --- | --- |
| Eindeutiger Bezeichner |Geben Sie den Attributnamen des Attributs ein, das als eindeutiger Bezeichner von Container-, Sicherheitsgruppen- und Benutzerdatensätzen dient.  In Active Directory ist dies normalerweise „objectGUID“. In anderen LDAP-Implementierungen kann dies „entryUUID“ oder etwas Ähnliches sein.  Der Standardwert lautet „objectGUID“. |
| Typ des eindeutigen Bezeichners |Wählen Sie den Typ des eindeutigen Bezeichnerattributs aus.  In Active Directory hat das objectGUID-Attribut den Typ GUID. In anderen LDAP-Implementierungen kann der Typ „ASCII-Bytearray“ oder „Zeichenfolge“ lauten.  Der Standardwert lautet GUID. <br><br>Es ist wichtig, diesen Typ richtig festzulegen, weil auf Synchronisierungselemente anhand ihres eindeutigen Bezeichners verwiesen wird. Der Typ des eindeutigen Bezeichners wird verwendet, um das Objekt im Verzeichnis direkt zu ermitteln.  Falls dieser Typ auf „Zeichenfolge“ festgelegt wird, wenn der Wert im Verzeichnis als Bytearray mit ASCII-Zeichen gespeichert wird, funktioniert die Synchronisierung nicht richtig. |
| Distinguished Name |Geben Sie den Attributnamen des Attributs ein, das den Distinguished Name für die einzelnen Datensätze enthält.  In Active Directory ist dies normalerweise „distinguishedName“. In anderen LDAP-Implementierungen kann dies „entryDN“ oder etwas Ähnliches sein.  Der Standardwert lautet „distinguishedName“. <br><br>Wenn kein Attribut vorhanden ist, das nur den Distinguished Name enthält, kann das ads path-Attribut verwendet werden.  Der Teil „LDAP://\<server\>/“ des Pfads wird automatisch entfernt, sodass nur der Distinguished Name des Objekts übrig bleibt. |
| Containername |Geben Sie den Attributnamen des Attributs ein, das den Namen in einem Containerdatensatz enthält.  Der Wert dieses Attributs wird in der Containerhierarchie angezeigt, wenn aus Active Directory importiert wird oder Synchronisierungselemente hinzugefügt werden.  Die Standardeinstellung lautet „name“. <br><br>Verwenden Sie Semikolons, um mehrere Containernamenattribute voneinander zu trennen, wenn für unterschiedliche Container verschiedene Attribute für ihre Namen verwendet werden.  Das erste Containernamenattribut eines Containerobjekts wird zum Anzeigen des Namens verwendet. |
| Sicherheitsgruppenname |Geben Sie den Attributnamen des Attributs ein, das den Namen in einem Sicherheitsgruppendatensatz enthält.  Der Wert dieses Attributs wird in der Liste „Sicherheitsgruppe“ angezeigt, wenn aus Active Directory importiert wird oder Synchronisierungselemente hinzugefügt werden.  Die Standardeinstellung lautet „name“. |
| Username |Geben Sie den Attributnamen des Attributs ein, das den Benutzernamen in einem Benutzerdatensatz enthält.  Der Wert dieses Attributs wird als Benutzername für den Multi-Factor Authentication-Server verwendet.  Ein zweites Attribut kann als Backup für das erste Attribut angegeben werden.  Das zweite Attribut wird nur verwendet, wenn das erste Attribut keinen Wert für den Benutzer enthält.  Die Standardwerte lauten „userPrincipalName“ und „sAMAccountName“. |
| Vorname |Geben Sie den Attributnamen des Attributs ein, das den Vornamen in einem Benutzerdatensatz enthält.  Der Standardwert lautet „givenName“. |
| Nachname |Geben Sie den Attributnamen des Attributs ein, das den Nachnamen in einem Benutzerdatensatz enthält.  Der Standardwert lautet „sn“. |
| E-Mail-Adresse |Geben Sie den Attributnamen des Attributs ein, das die E-Mail-Adresse in einem Benutzerdatensatz enthält.  Die E-Mail-Adresse wird zum Senden von Willkommens- und Update-E-Mails an Benutzer verwendet.  Der Standardwert lautet „Mail“. |
| Benutzergruppe |Geben Sie den Attributnamen des Attributs ein, das die Benutzergruppe in einem Benutzerdatensatz enthält.  Die Benutzergruppe kann verwendet werden, um Benutzer im Agent und in Berichten im Verwaltungsportal des Multi-Factor Authentication-Servers zu filtern. |
| BESCHREIBUNG |Geben Sie den Attributnamen des Attributs ein, das die Beschreibung in einem Benutzerdatensatz enthält.  Die Beschreibung wird nur für die Suche verwendet.  Der Standardwert lautet „description“. |
| Sprache für Telefonanrufe |Geben Sie den Attributnamen des Attributs ein, das den Kurznamen der Sprache für Sprachanrufe enthält, die für den Benutzer für Sprachanrufe verwendet werden soll. |
| SMS-Sprache |Geben Sie den Attributnamen des Attributs ein, das den Kurznamen der Sprache enthält, die für den Benutzer für SMS-Nachrichten verwendet werden soll. |
| Sprache der mobilen App |Geben Sie den Attributnamen des Attributs ein, das den Kurznamen der Sprache enthält, die für den Benutzer für Textnachrichten der Phone-App verwendet werden soll. |
| OAUTH-Tokensprache |Geben Sie den Attributnamen des Attributs ein, das den Kurznamen der Sprache enthält, die für den Benutzer für OAUTH-Token-Textnachrichten verwendet werden soll. |
| Telefon (geschäftlich) |Geben Sie den Attributnamen des Attributs ein, das die geschäftliche Telefonnummer in einem Benutzerdatensatz enthält.  Der Standardwert lautet „telephoneNumber“. |
| Telefon (privat) |Geben Sie den Attributnamen des Attributs ein, das die private Telefonnummer in einem Benutzerdatensatz enthält.  Der Standardwert lautet „homePhone“. |
| Pager |Geben Sie den Attributnamen des Attributs ein, das die Pagernummer in einem Benutzerdatensatz enthält.  Der Standardwert lautet „pager“. |
| Mobiltelefon |Geben Sie den Attributnamen des Attributs ein, das die private Mobiltelefonnummer in einem Benutzerdatensatz enthält.  Der Standardwert lautet „mobile“. |
| Fax |Geben Sie den Attributnamen des Attributs ein, das die Faxnummer in einem Benutzerdatensatz enthält.  Der Standardwert lautet „facsimileTelephoneNumber“. |
| IP-Telefon |Geben Sie den Attributnamen des Attributs ein, das die IP-Telefonnummer in einem Benutzerdatensatz enthält.  Der Standardwert lautet „ipPhone“. |
| Benutzerdefiniert |Geben Sie den Attributnamen des Attributs ein, das eine benutzerdefinierte Telefonnummer in einem Benutzerdatensatz enthält.  Der Standardwert lautet „blank“. |
| Durchwahl |Geben Sie den Attributnamen des Attributs ein, das die private Durchwahl-Telefonnummer in einem Benutzerdatensatz enthält.  Der Wert des Felds „Durchwahl“ wird nur als Durchwahl für die Haupttelefonnummer verwendet.  Der Standardwert lautet „blank“. <br><br>Wenn das Extension-Attribut nicht angegeben wird, können Durchwahlnummern als Teil des phone-Attributs eingefügt werden. Stellen Sie der Durchwahl in diesem Fall ein „x“ voran, damit sie richtig analysiert wird.  Beispielsweise ergibt sich für 555-123-4567 x890 der Teil 555-123-4567 als Telefonnummer und 890 als Durchwahl. |
| Schaltfläche „Standardeinstellungen wiederherstellen“ |Klicken Sie auf **Standardeinstellungen wiederherstellen**, um alle Attribute auf ihren Standardwert zurückzusetzen.  Die Standardwerte sollten für das normale Active Directory- oder ADAM-Schema richtig funktionieren. |

Klicken Sie zum Bearbeiten von Attributen auf der Registerkarte „Attribute“ auf **Bearbeiten**.  Ein Fenster wird angezeigt, in dem Sie die Attribute bearbeiten. Wählen Sie **...** neben einem Attribut aus, um ein Fenster zu öffnen, in dem Sie die Anzeige der Attribute auswählen können.

![Bearbeiten der Verzeichnisattributzuordnung im MFA-Server](./media/howto-mfaserver-dir-ad/dirint4.png)

## <a name="synchronization"></a>Synchronization

Die Synchronisierung sorgt dafür, dass die Azure MFA-Benutzerdatenbank (Multi-Factor Authentication) mit den Benutzern in Active Directory oder einem anderen LDAP-Protokoll (Lightweight Directory Protocol) synchron bleibt. Der Prozess ähnelt dem manuellen Importieren von Benutzern aus Active Directory, aber es wird regelmäßig abgefragt, ob Änderungen bei den Active Directory-Benutzern und Sicherheitsgruppen verarbeitet werden müssen.  Außerdem werden Benutzer deaktiviert oder entfernt, die aus einem Container, einer Sicherheitsgruppe oder Active Directory entfernt wurden.

Der ADSync-Dienst der Multi-Factor Authentication ist ein Windows-Dienst, mit dem die regelmäßige Abfrage von Active Directory durchgeführt wird.  Dies darf nicht mit Azure AD Sync oder Azure AD verwechselt werden.  Der ADSync-Dienst der Multi-Factor Authentication wurde zwar auf einer ähnlichen Codebasis erstellt, gilt aber speziell für den Azure Multi-Factor Authentication-Server.  Er wird im beendeten Zustand (Stopped) installiert und vom Dienst des Multi-Factor Authentication-Servers gestartet, wenn er für die Ausführung konfiguriert ist.  Wenn Sie über eine Konfiguration des Multi-Factor Authentication-Servers mit mehreren Servern verfügen, kann der ADSync-Dienst der Multi-Factor Authentication nur auf einem Server ausgeführt werden.

Für den ADSync-Dienst der Multi-Factor Authentication wird die DirSync-LDAP-Servererweiterung genutzt, die von Microsoft zum effizienten Abfragen von Änderungen bereitgestellt wird.  Für diesen DirSync-Steuerungsaufrufer müssen das Recht „directory get changes“ und das erweiterte Steuerzugriffsrecht „DS-Replication-Get-Changes“ vorhanden sein.  Standardmäßig werden diese Rechte den Administrator- und LocalSystem-Konten auf Domänencontrollern zugewiesen.  Der AdSync-Dienst der Multi-Factor Authentication wird standardmäßig für die LocalSystem-Ausführung konfiguriert.  Daher ist es am einfachsten, den Dienst auf einem Domänencontroller auszuführen.  Wenn Sie den Dienst so konfigurieren, dass immer eine vollständige Synchronisierung durchgeführt wird, kann er als Konto mit geringeren Berechtigungen ausgeführt werden.  Dies ist weniger effizient, aber es werden auch weniger Kontoberechtigungen benötigt.

Wenn das LDAP-Verzeichnis DirSync unterstützt und dafür konfiguriert ist, funktioniert das Abfragen von Benutzer- und Sicherheitsgruppenänderungen genauso wie bei Active Directory.  Wenn das LDAP-Verzeichnis die DirSync-Steuerung nicht unterstützt, wird bei jedem Zyklus eine vollständige Synchronisierung durchgeführt.

![Synchronisierung von Verzeichnisobjekten im MFA-Server](./media/howto-mfaserver-dir-ad/dirint5.png)

Die folgende Tabelle enthält zusätzliche Informationen zu den einzelnen Einstellungen auf der Registerkarte „Synchronisierung“.

| Funktion | BESCHREIBUNG |
| --- | --- |
| Synchronisierung mit Active Directory aktivieren |Wenn dieses Kontrollkästchen aktiviert ist, fragt der Multi-Factor Authentication-Server-Dienst Active Directory regelmäßig auf Änderungen ab. <br><br>Hinweis: Es muss mindestens ein Synchronisierungselement hinzugefügt und der Schritt „Jetzt synchronisieren“ ausgeführt werden, damit der Multi-Factor Authentication-Server-Dienst mit dem Verarbeiten von Änderungen beginnt. |
| Synchronisierung alle |Geben Sie das Zeitintervall an, wie lange der Multi-Factor Authentication-Server-Dienst zwischen dem Abrufen und Verarbeiten von Änderungen wartet. <br><br> Hinweis: Das angegebene Intervall ist der Zeitraum zwischen dem Startzeitpunkt der jeweiligen Zyklen.  Wenn die Zeit zum Verarbeiten von Änderungen das Intervall überschreitet, startet der Dienst sofort wieder einen Abruf. |
| Nicht mehr in Active Directory vorhandene Benutzer entfernen |Wenn dieses Kontrollkästchen aktiviert ist, verarbeitet der Multi-Factor Authentication-Server-Dienst von Active Directory gelöschte veraltete Benutzer und entfernt den dazugehörigen Multi-Factor Authentication-Server-Benutzer. |
| Immer vollständige Synchronisierung ausführen |Wenn dieses Kontrollkästchen aktiviert ist, führt der Multi-Factor Authentication-Server-Dienst immer eine vollständige Synchronisierung durch.  Wenn dieses Kontrollkästchen deaktiviert ist, führt der Multi-Factor Authentication-Server-Dienst eine inkrementelle Synchronisierung durch, indem nur geänderte Benutzer abgefragt werden.  Diese Option ist standardmäßig deaktiviert. <br><br>Bei der Deaktivierung wird vom Azure MFA-Server nur dann eine inkrementelle Synchronisierung durchgeführt, wenn das Verzeichnis die DirSync-Steuerung unterstützt und die Kontobindung an das Verzeichnis über Berechtigungen zum Durchführen von inkrementellen DirSync-Abfragen verfügt.  Falls das Konto nicht die entsprechenden Berechtigungen besitzt oder mehrere Domänen an der Synchronisierung beteiligt sind, wird vom Azure MFA-Server eine vollständige Synchronisierung durchgeführt. |
| Administratorgenehmigung erfordern, wenn mehr als X Benutzer deaktiviert oder entfernt werden |Synchronisierungselemente können konfiguriert werden, um Benutzer zu deaktivieren oder zu entfernen, die nicht mehr Mitglied des Containers oder einer Sicherheitsgruppe des Elements sind.  Als Sicherheitsmaßnahme kann die Administratorgenehmigung erforderlich gemacht werden, wenn die Anzahl der zu deaktivierenden oder zu entfernenden Benutzer einen Schwellenwert überschreitet.  Wenn dieses Kontrollkästchen aktiviert ist, ist ab dem angegebenen Schwellenwert eine Genehmigung erforderlich.  Der Standardwert ist 5, und der zulässige Bereich liegt zwischen 1 und 999. <br><br> Die Genehmigung wird durchgeführt, indem zuerst eine E-Mail-Benachrichtigung an Administratoren gesendet wird. Die E-Mail-Benachrichtigung enthält eine Anleitung zum Überprüfen und Genehmigen der Deaktivierung und Entfernung von Benutzern.  Wenn die Benutzeroberfläche des Multi-Factor Authentication-Servers gestartet wird, wird eine Aufforderung zur Genehmigung angezeigt. |

Mit der Schaltfläche **Jetzt synchronisieren** können Sie für die angegebenen Synchronisierungselemente eine vollständige Synchronisierung durchführen.  Eine vollständige Synchronisierung ist jeweils erforderlich, wenn Synchronisierungselemente hinzugefügt, geändert, entfernt oder neu angeordnet werden.  Sie ist auch erforderlich, bevor der AdSync-Dienst der Multi-Factor Authentication betriebsbereit ist, da hiermit der Ausgangspunkt festgelegt wird, ab dem der Dienst die Abfrage auf inkrementelle Änderungen durchführt.  Wenn Änderungen an Synchronisierungselementen vorgenommen wurden, aber keine vollständige Synchronisierung durchgeführt wurde, wird die Aufforderung „Jetzt synchronisieren“ angezeigt.

Mit der Schaltfläche **Entfernen** kann der Administrator ein oder mehrere Synchronisierungselemente aus der Elementliste der Multi-Factor Authentication-Server-Synchronisierung löschen.

> [!WARNING]
> Nachdem ein Synchronisierungselement-Datensatz entfernt wurde, kann er nicht wiederhergestellt werden. Sie müssen den Synchronisierungselement-Datensatz erneut hinzufügen, wenn Sie ihn versehentlich gelöscht haben.

Das bzw. die Synchronisierungselemente wurden vom Multi-Factor Authentication-Server entfernt.  Der Multi-Factor Authentication-Server-Dienst führt die Verarbeitung der Synchronisierungselemente nicht mehr durch.

Mit den Schaltflächen „Nach oben“ und „Nach unten“ kann der Administrator die Reihenfolge der Synchronisierungselemente ändern.  Die Reihenfolge ist wichtig, da ein Benutzer gleichzeitig Mitglied von mehr als einem Synchronisierungselement sein kann (z. B. Container und Sicherheitsgruppe).  Die Einstellungen, die während der Synchronisierung auf den Benutzer angewendet wurden, stammen aus dem ersten Synchronisierungselement in der Liste, der der Benutzer zugeordnet ist.  Daher sollten die Synchronisierungselemente in der Reihenfolge ihrer Priorität angeordnet werden.

> [!TIP]
> Nach dem Entfernen von Synchronisierungselementen sollte eine vollständige Synchronisierung durchgeführt werden.  Nach dem Sortieren von Synchronisierungselementen sollte eine vollständige Synchronisierung durchgeführt werden.  Klicken Sie auf **Jetzt synchronisieren**, um eine vollständige Synchronisierung durchzuführen.

## <a name="multi-factor-authentication-servers"></a>Multi-Factor Authentication-Server

Zusätzliche Multi-Factor Authentication-Server können als RADIUS-Sicherungsproxy, LDAP-Proxy oder für die IIS-Authentifizierung eingerichtet werden. Die Konfiguration der Synchronisierung wird für alle Agents gemeinsam genutzt. Der Multi-Factor Authentication-Server-Dienst wird aber unter Umständen nur für einen der Agents ausgeführt. Auf dieser Registerkarte können Sie den Multi-Factor Authentication-Server auswählen, der für die Synchronisierung aktiviert werden soll.

![Dazugehörige Multi-Factor Authentication-Server](./media/howto-mfaserver-dir-ad/dirint6.png)
