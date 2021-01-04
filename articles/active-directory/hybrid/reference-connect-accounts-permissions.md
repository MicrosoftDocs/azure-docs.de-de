---
title: 'Azure AD Connect: Konten und Berechtigungen | Microsoft-Dokumentation'
description: Dieses Thema beschreibt die verwendeten und erstellten Konten sowie die erforderlichen Berechtigungen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c075e19422341ad7ccfd3ad951517876ab26a495
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858415"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Konten und Berechtigungen

## <a name="accounts-used-for-azure-ad-connect"></a>Für Azure AD Connect verwendete Konten

![Kontenübersicht](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect nutzt 3 Konten, um Informationen aus lokalen Verzeichnissen oder Windows Server Active Directory mit Azure Active Directory zu synchronisieren.  Diese Konten sind:

- **AD DS-Connector-Konto**: Wird zum Lesen/Schreiben von Informationen in Windows Server Active Directory verwendet

- **ADSync-Dienstkonto**: Wird zum Ausführen des Synchronisierungsdiensts und zum Zugreifen auf die SQL-Datenbank verwendet

- **Azure AD-Connector-Konto**: Wird zum Schreiben von Informationen in Azure AD verwendet

Zusätzlich zu diesen drei Konten, die zum Ausführen von Azure AD Connect ausgeführt werden, benötigen Sie außerdem die folgenden zusätzlichen Konten, um Azure AD Connect zu installieren.  Dies sind:

- **Lokales Administratorkonto**: Der Administrator, der Azure AD Connect installiert und über lokale Administratorberechtigungen für den Computer verfügt.

- **AD DS-Unternehmensadministratorkonto**: Wird optional verwendet, um das obige AD DS-Connector-Konto zu erstellen.

- **Azure AD Global Administrator-Konto**: Wird zum Erstellen des Azure AD-Connector-Kontos und zum Konfigurieren von Azure AD verwendet.

- **SQL SA-Konto (optional)** : Wird zum Erstellen der ADSync-Datenbank verwendet, wenn die Vollversion von SQL Server installiert ist.  Dieser SQL Server kann lokal oder remote zur Azure AD Connect-Installation vorliegen.  Dabei kann es sich um das gleiche Konto handeln wie bei Enterprise Administrator.  Der SQL-Administrator kann nun eine Out-of-Band-Datenbankbereitstellung ausführen, sodass die Datenbank anschließend vom Azure AD Connect-Administrator mit Datenbankbesitzerrechten installiert werden kann.  Weitere Informationen finden Sie unter [Installieren von Azure AD Connect mit Berechtigungen eines delegierten SQL-Administrators](how-to-connect-install-sql-delegation.md).


>[!IMPORTANT]
> Ab Build 1.4. ###.# wird es nicht mehr unterstützt, wenn Sie ein Unternehmens- oder ein Domänenadministratorkonto als AD DS-Connectorkonto verwenden.  Wenn Sie versuchen, ein Unternehmens- oder Domänenadministratorkonto einzugeben, und dabei **vorhandenes Konto verwenden** nutzen, wird Ihnen eine Fehlermeldung angezeigt.

> [!NOTE]
> Es wird unterstützt, um die in Azure AD Connect verwendeten Administratorkonten über eine administrative ESAE-Gesamtstruktur (auch „Red Forest“ genannt) verwalten zu können.
> Mithilfe dedizierter administrativer Gesamtstrukturen können Organisationen Administratorkonten, Arbeitsstationen und Gruppen in einer Umgebung hosten, die über bessere Sicherheitskontrollen verfügt als die Produktionsumgebung.
> Weitere Informationen zu dedizierten administrativen Gesamtstrukturen finden Sie unter [ESAE-basierter Ansatz für den Entwurf einer administrativen Gesamtstruktur](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> Die Rolle „Globaler Administrator“ ist nach der ersten Installation nicht erforderlich. Das einzige erforderliche Konto ist das Rollenkonto **Verzeichnissynchronisierungskonten**. Dies bedeutet nicht unbedingt, dass Sie das Konto mit der Rolle „Globaler Administrator“ einfach entfernen sollten. Sie sollten stattdessen besser die Rolle in eine Rolle mit weniger Berechtigungen ändern, da das vollständige Entfernen des Kontos zu Problemen führen kann, wenn Sie den Assistenten erneut ausführen müssen. Indem Sie die Berechtigungen der Rolle verringern, können Sie sie jederzeit erneut erhöhen, wenn Sie den Azure AD Connect-Assistenten erneut verwenden müssen. 

## <a name="installing-azure-ad-connect"></a>Installieren von Azure AD Connect
Der Azure AD Connect-Installations-Assistent bietet zwei verschiedene Methoden:

* Für die Expresseinstellungen benötigt der Assistent höhere Berechtigungen.  So können Sie Ihre Konfiguration problemlos einrichten, ohne Benutzer erstellen oder Berechtigungen konfigurieren zu müssen.
* In den benutzerdefinierten Einstellungen bietet der Assistent mehr Auswahlmöglichkeiten und Optionen. Es gibt jedoch Situationen, in denen Sie sicherstellen müssen, dass Sie selbst über die richtigen Berechtigungen verfügen.



## <a name="express-settings-installation"></a>Installation mit Express-Einstellungen
In den Expresseinstellungen benötigt der Installationsassistent folgende Anmeldeinformationen:

  - AD DS Enterprise Administrator-Anmeldeinformationen
  - Azure AD Global Administrator-Anmeldeinformationen

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS Enterprise Admin-Anmeldeinformationen
Das AD DS Enterprise Admin-Konto wird zum Konfigurieren Ihres lokales Active Directorys verwendet. Diese Anmeldeinformationen werden während der Installation verwendet, nach Abschluss der Installation jedoch nicht mehr. Enterprise-Administratoren und nicht Domänenadministratoren sollten sicherstellen, dass die Berechtigungen in Active Directory in allen Domänen festgelegt werden können.

Bei einem Upgrade von DirSync werden die Anmeldeinformationen des AD DS-Unternehmensadministrators verwendet, um das Kennwort für das von DirSync verwendete Konto zurückzusetzen. Sie müssen außerdem über die Anmeldeinformationen eines globalen Azure AD-Administrators verfügen.

### <a name="azure-ad-global-admin-credentials"></a>Anmeldeinformationen eines globalen Azure AD-Administrators
Diese Anmeldeinformationen werden während der Installation verwendet, nach Abschluss der Installation jedoch nicht mehr. Sie dienen zum Erstellen des Azure AD Connector-Kontos, das zum Synchronisieren der Änderungen mit Azure AD verwendet wird. Das Konto aktiviert auch die Synchronisierung als Feature in Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS Connector-Konto zum Erstellen von Berechtigungen für Expresseinstellungen
Das AD DS Connector-Konto wird zum Erstellen von Lese- und Schreibvorgängen in Windows Server AD erstellt und besitzt die folgenden Berechtigungen, wenn es mit den Expresseinstellungen erstellt wird:

| Berechtigung | Syntaxelemente |
| --- | --- |
| <li>Verzeichnisänderungen replizieren</li><li>Verzeichnisänderungen replizieren: Alle |Kennworthashsynchronisierung |
| Alle Eigenschaften lesen/schreiben: Benutzer |Importieren und Exchange-Hybridbereitstellung |
| Alle Eigenschaften lesen/schreiben: iNetOrgPerson |Importieren und Exchange-Hybridbereitstellung |
| Alle Eigenschaften lesen/schreiben: Gruppe |Importieren und Exchange-Hybridbereitstellung |
| Alle Eigenschaften lesen/schreiben: Kontakt |Importieren und Exchange-Hybridbereitstellung |
| Kennwort zurücksetzen |Vorbereitung für das Aktivieren des Rückschreibens von Kennwörtern |

### <a name="express-installation-wizard-summary"></a>Assistent für die Expressinstallation – Zusammenfassung

![Expressinstallation](./media/reference-connect-accounts-permissions/express.png)

Nachfolgend finden Sie eine Übersicht über die Seiten des Assistenten für die Expressinstallation, die gesammelten Anmeldeinformationen und deren Verwendungszweck.

| Seite des Assistenten | Erfasste Anmeldeinformationen | Erforderliche Berechtigungen | Verwendung |
| --- | --- | --- | --- |
| – |Benutzer, der den Installations-Assistenten ausführt |Administrator des lokalen Servers |<li>Erstellt das ADSync-Dienstkonto, das zum Ausführen des Synchronisierungsdiensts verwendet wird. |
| Stellen Sie eine Verbindung mit Azure AD her. |Azure AD-Verzeichnisanmeldeinformationen |Globale Administratorrolle in Azure AD |<li>Aktivieren der Synchronisierung im Azure AD-Verzeichnis</li>  <li>Erstellt das Azure AD Connector-Konto, das für fortlaufende Synchronisierungsvorgänge in Azure AD verwendet wird.</li> |
| Herstellen einer Verbindung mit AD DS |Lokale Active Directory-Anmeldeinformationen |Mitglied der  Gruppe „Unternehmensadministratoren“ in Active Directory |<li>Erstellt ein AD DS Connector-Konto in Active Directory und gewährt Zugriff darauf. Dieses erstellte Konto dient zum Lesen und Schreiben von Verzeichnisinformationen während der Synchronisierung.</li> |


## <a name="custom-installation-settings"></a>Einstellungen für die benutzerdefinierte Installation

Mit den benutzerdefinierten Installationseinstellungen bietet der Assistent mehr Auswahlmöglichkeiten und Optionen. 

### <a name="custom-installation-wizard-summary"></a>Assistent für die benutzerdefinierte Installation – Zusammenfassung

Nachfolgend finden Sie eine Übersicht über die Seiten des Assistenten für die benutzerdefinierte Installation, die gesammelten Anmeldeinformationen und deren Verwendungszweck.

![Screenshot: Seiten des Assistenten für die benutzerdefinierte Installation](./media/reference-connect-accounts-permissions/customize.png)

| Seite des Assistenten | Erfasste Anmeldeinformationen | Erforderliche Berechtigungen | Verwendung |
| --- | --- | --- | --- |
| – |Benutzer, der den Installations-Assistenten ausführt |<li>Administrator des lokalen Servers</li><li>Bei Verwendung einer vollständigen SQL Server-Instanz muss der Benutzer Systemadministrator (SA) in SQL sein.</li> |Erstellt standardmäßig das lokale Konto, das als Dienstkonto für das Synchronisierungsmodul verwendet wird. Das Konto wird nur erstellt, wenn der Administrator kein bestimmtes Konto angibt. |
| „Synchronisierungsdienste installieren“, Option „Dienstkonto“ |Anmeldeinformationen für Active Directory- oder lokale Konten |Benutzerberechtigungen werden vom Installations-Assistenten gewährt |Wenn der Administrator ein Konto angibt, wird dieses Konto als Dienstkonto für den Synchronisierungsdienst verwendet. |
| Stellen Sie eine Verbindung mit Azure AD her. |Azure AD-Verzeichnisanmeldeinformationen |Globale Administratorrolle in Azure AD |<li>Aktivieren der Synchronisierung im Azure AD-Verzeichnis</li>  <li>Erstellt das Azure AD Connector-Konto, das für fortlaufende Synchronisierungsvorgänge in Azure AD verwendet wird.</li> |
| Verzeichnisse verbinden |Lokale Active Directory-Anmeldeinformationen für jede Gesamtstruktur, die mit Azure AD verbunden wird |Die Berechtigungen hängen davon ab, welche Funktionen Sie aktivieren. Sie finden diese unter „Erstellen des AD DS-Kontos“. |Dieses Konto dient zum Lesen und Schreiben von Verzeichnisinformationen während der Synchronisierung. |
| AD FS-Server |Für jeden Server in der Liste sammelt der Assistent Anmeldeinformationen, wenn die Anmeldeinformationen des Benutzers, der den Assistenten ausführt, nicht für die Verbindung ausreichen. |Domänenadministrator |Installieren und Konfigurieren der AD FS-Server-Rolle. |
| Webanwendungsproxy-Server |Für jeden Server in der Liste sammelt der Assistent Anmeldeinformationen, wenn die Anmeldeinformationen des Benutzers, der den Assistenten ausführt, nicht für die Verbindung ausreichen. |Lokaler Administrator auf dem Zielcomputer |Installieren und Konfigurieren der WAP-Server-Rolle. |
| Anmeldeinformationen der Proxyvertrauensstellung |Anmeldeinformationen der Verbunddienstvertrauensstellung (die Anmeldeinformationen, die der Proxy zur Registrierung für ein Zertifikat für die Vertrauensstellung vom FS verwendet) |Domänenkonto, dessen Benutzer ein lokaler Administrator des AD FS-Servers ist |Erste Registrierung des vertrauenswürdigen FS-WAP-Zertifikats. |
| Seite „AD FS-Dienstkonto“, Option „Domänenbenutzerkonto verwenden“ |Anmeldeinformationen für das Active Directory-Benutzerkonto |Domänenbenutzer |Das Azure AD-Benutzerkonto, dessen Anmeldeinformationen bereitgestellt werden, wird als Anmeldekonto des AD FS-Diensts verwendet. |

### <a name="create-the-ad-ds-connector-account"></a>Erstellen des AD DS-Connector-Kontos

>[!IMPORTANT]
>Mit Build **1.1.880.0** (August 2018 veröffentlicht) wurde ein neues PowerShell-Modul namens „ADSyncConfig.psm1“ eingeführt, das eine Sammlung von Cmdlets enthält, die Ihnen bei der Konfiguration der richtigen Active Directory-Berechtigungen für Ihr Azure AD DS-Connector-Konto helfen sollen.
>
>Weitere Informationen hierzu finden Sie unter [Azure AD Connect: Konfigurieren der Azure AD DS-Connector-Kontoberechtigung](how-to-connect-configure-ad-ds-connector-account.md)

Das Konto, das Sie auf der Seite **Verzeichnisse verbinden** angeben, muss vor der Installation bereits in Active Directory vorhanden sein.  In Azure AD Connect Version 1.1.524.0 und höher kann der Azure AD Connect-Assistent das **AD DS Connector-Konto** erstellen, das zum Herstellen der Verbindung mit Active Directory verwendet wird.  

Es muss auch über die erforderlichen Berechtigungen verfügen. Der Installations-Assistent führt keine Überprüfung der Berechtigungen durch, mögliche Probleme werden erst während der Synchronisierung ermittelt.

Welche Berechtigungen Sie benötigen, hängt von den aktivierten optionalen Funktionen ab. Wenn Sie über mehrere Domänen verfügen, müssen die Berechtigungen für alle Domänen in der Gesamtstruktur erteilt werden. Wenn Sie keine dieser Features aktivieren, sind die **Domänenbenutzer** -Standardberechtigungen ausreichend.

| Funktion | Berechtigungen |
| --- | --- |
| ms-DS-ConsistencyGuid |Schreibberechtigungen für das Attribut „ms-DS-ConsistencyGuid“, das unter [Entwurfskonzepte – Verwendung von „ms-DS-ConsistencyGuid“ als „sourceAnchor“](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) dokumentiert ist. | 
| Kennworthashsynchronisierung |<li>Verzeichnisänderungen replizieren</li>  <li>Verzeichnisänderungen replizieren: Alle |
| Exchange-Hybridbereitstellung |Schreibberechtigungen für die Attribute, die in [Exchange-Hybridrückschreiben](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) für Benutzer, Gruppen und Kontakte dokumentiert sind |
| Öffentlicher Exchange-E-Mail-Ordner |Leseberechtigungen für die Attribute, die im [öffentlichen Exchange-E-Mail-Ordner](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) für öffentliche Ordner dokumentiert sind. | 
| Kennwortrückschreiben |Schreibberechtigungen für die Attribute, die in [Erste Schritte mit der Kennwortverwaltung](../authentication/tutorial-enable-sspr-writeback.md) für Benutzer dokumentiert sind |
| Geräterückschreiben |Berechtigungen, die mit einem PowerShell-Skript erteilt wurden, wie unter [Geräterückschreiben](how-to-connect-device-writeback.md)beschrieben |
| Gruppenrückschreiben |Ermöglicht das Rückschreiben von **Microsoft 365-Gruppen** in eine Gesamtstruktur, in der Exchange installiert ist.|

## <a name="upgrade"></a>Aktualisieren
Wenn Sie Azure AD Connect auf eine höhere Version aktualisieren, benötigen Sie folgende Berechtigungen:

>[!IMPORTANT]
>Seit Build 1.1.484 tritt in Azure AD Connect ein Regressionsfehler auf, aufgrund dessen zum Aktualisieren der SQL-Datenbank Systemadministratorberechtigungen erforderlich sind.  Dieser Fehler wurde in Build 1.1.647 behoben.  Für das Upgrade auf diesen Build benötigen Sie Systemadministratorberechtigungen.  dbo-Berechtigungen sind nicht ausreichend.  Wenn Sie versuchen, Azure AD Connect ohne Systemadministratorberechtigungen zu aktualisieren, tritt ein Fehler auf, und Azure AD Connect funktioniert anschließend nicht mehr ordnungsgemäß.  Microsoft ist sich dieses Problems bewusst und arbeitet an einer Lösung.


| Prinzipal | Erforderliche Berechtigungen | Syntaxelemente |
| --- | --- | --- |
| Benutzer, der den Installations-Assistenten ausführt |Administrator des lokalen Servers |Aktualisieren von Binärdateien. |
| Benutzer, der den Installations-Assistenten ausführt |Mitglied von ADSyncAdmins |Vornehmen von Änderungen an den Synchronisierungsregeln und anderen Konfigurationen. |
| Benutzer, der den Installations-Assistenten ausführt |Wenn Sie einen vollständigen SQL-Server verwenden: DBO (oder ähnliches) der Datenbank für das Synchronisierungsmodul |Vornehmen von Änderungen auf Datenbankebene, z. B. Aktualisieren von Tabellen mit neuen Spalten. |

## <a name="more-about-the-created-accounts"></a>Weitere Informationen zu den erstellten Konten
### <a name="ad-ds-connector-account"></a>AD DS Connector-Konto
Wenn Sie Express-Einstellungen verwenden, wird ein Konto für die Synchronisierung in Active Directory erstellt. Das erstellte Konto befindet sich in der Stammdomäne der Gesamtstruktur im Benutzercontainer, und sein Name ist mit dem Präfix **MSOL_** versehen. Das Konto wird mit einem langen, komplexen Kennwort erstellt, das nicht abläuft. Wenn Sie über eine Kennwortrichtlinie in der Domäne verfügen, stellen Sie sicher, dass lange und komplexe Kennwörter für dieses Konto zulässig sind.

![AD-Konto](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Wenn Sie benutzerdefinierte Einstellungen verwenden, sind Sie für das Erstellen des Kontos vor Beginn der Installation zuständig.  Weitere Informationen finden Sie unter „Erstellen des AD DS-Connector-Kontos“.

### <a name="adsync-service-account"></a>ADSync-Dienstkonto
Der Synchronisierungsdienst kann unter verschiedenen Konten ausgeführt werden. Er kann unter einem **virtuellen Dienstkonto** (VSA), einem **gruppenverwalteten Dienstkonto** (gMSA/sMSA) oder einem normalen Benutzerkonto ausgeführt werden. Die unterstützten Optionen wurden mit der Connect-Version von April 2017 geändert und treten in Kraft, wenn Sie eine Neuinstallation durchführen. Wenn Sie ein Upgrade von einer früheren Version von Azure AD Connect durchführen, sind diese zusätzlichen Optionen nicht verfügbar.

| Kontotyp | Installationsoption | BESCHREIBUNG |
| --- | --- | --- |
| [Virtuelles Dienstkonto](#virtual-service-account) | Express und benutzerdefiniert, April 2017 und höher | Dies ist die Option für alle Expressinstallationen mit Ausnahme von Installationen auf einem Domänencontroller. Für benutzerdefinierte Installationen ist es die Standardoption, sofern keine andere Option verwendet wird. |
| [Gruppenverwaltetes Dienstkonto](#group-managed-service-account) | Benutzerdefiniert, April 2017 und höher | Wenn Sie einen Remotecomputer mit SQL Server verwenden, empfehlen wir den Einsatz eines gruppenverwalteten Dienstkontos. |
| [Benutzerkonto](#user-account) | Express und benutzerdefiniert, April 2017 und höher | Ein Benutzerkonto mit dem Präfix „AAD_“ wird nur während der Installation erstellt, wenn diese unter Windows Server 2008 und auf einem Domänencontroller erfolgt. |
| [Benutzerkonto](#user-account) | Express und benutzerdefiniert, März 2017 und früher | Ein lokales Konto mit dem Präfix „AAD_“ wird während der Installation erstellt. Bei der benutzerdefinierten Installation kann ein anderes Konto angegeben werden. |

Wenn Sie Connect mit einem Build von März 2017 und früher verwenden, setzen Sie das Kennwort für das Dienstkonto nicht zurück, da Windows die Verschlüsselungsschlüssel aus Sicherheitsgründen zerstört. Sie können das Konto nicht in ein anderes Konto ändern, ohne Azure AD Connect neu zu installieren. Wenn Sie ein Upgrade auf einen Build von April 2017 oder höher durchführen, wird das Ändern des Kennworts für das Dienstkonto unterstützt. Sie können jedoch nicht das verwendete Konto ändern.

> [!Important]
> Sie können das Dienstkonto nur bei der erstmaligen Installation festlegen. Das Ändern des Dienstkontos nach Abschluss der Installation wird nicht unterstützt.

Hier sehen Sie eine Tabelle mit den Standard-, den empfohlenen und den unterstützten Optionen für das Synchronisierungsdienstkonto.

Legende:

- **Fettformatierung** kennzeichnet die Standardoption, die in den meisten Fällen der empfohlenen Option entspricht.
- *Kursivformatierung* kennzeichnet die empfohlene Option, sofern diese nicht mit der Standardoption übereinstimmt.
- 2008: Standardoption bei der Installation unter Windows Server 2008
- Nicht fett formatiert: unterstützte Option
- Lokales Konto: lokales Benutzerkonto auf dem Server
- Domänenkonto: Domänenbenutzerkonto
- sMSA: [eigenständig verwaltetes Dienstkonto](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA: [gruppenverwaltetes Dienstkonto](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Benutzerdefiniert | Remote-SQL</br>Benutzerdefiniert |
| --- | --- | --- | --- |
| **In die Domäne eingebundener Computer** | **VSA**</br>Lokales Konto (2008) | **VSA**</br>Lokales Konto (2008)</br>Lokales Konto</br>Domänenkonto</br>sMSA, gMSA | **gMSA**</br>Domänenkonto |
| **Domänencontroller** | **Domänenkonto** | *gMSA*</br>**Domänenkonto**</br>sMSA| *gMSA*</br>**Domänenkonto**|

#### <a name="virtual-service-account"></a>Virtuelles Dienstkonto
Ein virtuelles Dienstkonto ist ein besonderer Kontotyp, der nicht über ein Kennwort verfügt und von Windows verwaltet wird.

![Screenshot: Virtuelles Dienstkonto (Virtual Service Account, VSA)](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

Das VSA ist für den Einsatz in Szenarien vorgesehen, in denen das Synchronisierungsmodul und SQL sich auf demselben Server befinden. Wenn Sie einen SQL-Remotecomputer verwenden, empfehlen wir stattdessen den Einsatz eines gruppenverwalteten Dienstkontos.

Für dieses Feature ist Windows Server 2008 R2 oder höher erforderlich. Wenn Sie Azure AD Connect unter Windows Server 2008 installieren, wird bei der Installation automatisch ein [Benutzerkonto](#user-account) verwendet.

#### <a name="group-managed-service-account"></a>Gruppenverwaltetes Dienstkonto
Wenn Sie einen Remotecomputer mit SQL Server verwenden, empfehlen wir den Einsatz eines **gruppenverwalteten Dienstkontos**. Weitere Informationen zum Vorbereiten von Active Directory für das gruppenverwaltete Benutzerkonto finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).

Um diese Option zu verwenden, wählen Sie auf der Seite [Erforderliche Komponenten installieren](how-to-connect-install-custom.md#install-required-components) die Optionen **Vorhandenes Dienstkonto verwenden** und **Verwaltetes Dienstkonto**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Die Verwendung eines [eigenständig verwalteten Dienstkontos](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10)) wird ebenfalls unterstützt. Diese Konten können jedoch nur auf dem lokalen Computer verwendet werden, und es gibt keinen praktischen Vorteil gegenüber dem virtuellen Standarddienstkonto.

Für dieses Feature ist Windows Server 2012 oder höher erforderlich. Wenn Sie ein älteres Betriebssystem und Remote-SQL einsetzen, müssen Sie ein [Benutzerkonto](#user-account) verwenden.

#### <a name="user-account"></a>Benutzerkonto
Der Installations-Assistent erstellt ein lokales Dienstkonto (sofern das Konto nicht zur Verwendung in benutzerdefinierten Einstellungen angegeben wird). Das Konto ist mit dem Präfix **AAD_** versehen und wird zur Ausführung des eigentlichen Synchronisierungsdiensts verwendet. Wenn Sie Azure AD Connect auf einem Domänencontroller installieren, wird das Konto in der Domäne erstellt. Das Dienstkonto **AAD_** muss sich in folgenden Fällen in der Domäne befinden:
   - Sie verwenden einen Remoteserver, auf dem SQL Server ausgeführt wird.
   - Sie verwenden einen Proxy, der Authentifizierung erfordert.

![Synchronisierungsdienstkonto](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Das Konto wird mit einem langen, komplexen Kennwort erstellt, das nicht abläuft.

Dieses Konto wird verwendet, um die Kennwörter für die anderen Konten auf sichere Weise zu speichern. Die Kennwörter dieser Konten sind verschlüsselt in der Datenbank gespeichert. Die privaten Schlüssel für die Verschlüsselungsschlüssel sind mit der Verschlüsselung mit geheimem Schlüssel für kryptografische Dienste mithilfe der Windows-Datenschutz-API (DPAPI) geschützt.

Bei Verwendung einer SQL Server-Instanz mit vollem Funktionsumfang wird das Dienstkonto zum DBO der Datenbank, die für das Synchronisierungsmodul erstellt wurde. Der Dienst funktioniert mit anderen Berechtigungen nicht wie vorgesehen. Darüber hinaus wird eine SQL-Anmeldung erstellt.

Das Konto erhält auch Berechtigungen für Dateien, Registrierungsschlüssel und andere Objekte im Zusammenhang mit dem Synchronisierungsmodul.

### <a name="azure-ad-connector-account"></a>Azure AD Connector-Konto
Zur Verwendung durch den Synchronisierungsdienst wird ein Konto in Azure AD erstellt. Dieses Konto kann anhand des Anzeigenamens identifiziert werden.

![Screenshot: Azure AD-Konto](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Der Name des Servers, auf dem das Konto verwendet wird, kann im zweiten Teil des Benutzernamens identifiziert werden. In der Abbildung oben heißt der Server „DC1“. Wenn Sie über Stagingserver verfügen, erhält jeder Server ein eigenes Konto.

Das Konto wird mit einem langen, komplexen Kennwort erstellt, das nicht abläuft. Diesem wird eine besondere Rolle **Konten für die Verzeichnissynchronisierungsaufgaben** zugewiesen, die nur über Berechtigungen zur Ausführung von Verzeichnissynchronisierungsaufgaben verfügt. Diese besondere integrierte Rolle kann nicht außerhalb des Azure AD Connect-Assistenten gewährt werden. Im Azure-Portal wird dieses Konto mit der Rolle **Benutzer** angezeigt.

Es besteht eine Beschränkung auf 20 Synchronisierungsdienstkonten in Azure AD. Um die Liste der vorhandenen Azure AD-Dienstkonten in Azure AD abzurufen, führen Sie das folgende Azure AD PowerShell-Cmdlet aus: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Um nicht verwendete Azure AD-Dienstkonten zu entfernen, führen Sie das folgende Azure AD PowerShell-Cmdlet aus: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Bevor Sie die oben aufgeführten PowerShell-Befehle verwenden können, müssen Sie [Azure Active Directory-PowerShell für das Graph-Modul](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module) installieren und mit [Connect-AzureAD](/powershell/module/azuread/connect-azuread) eine Verbindung zu Ihrer Azure AD-Instanz herstellen.

Weitere Informationen zum Verwalten oder Zurücksetzen des Kennworts für das Azure AD Connector-Konto finden Sie unter [Verwalten der Azure AD Connect-Konten](how-to-connect-azureadaccount.md).

## <a name="related-documentation"></a>verwandten Dokumentation
Wenn Sie die Dokumentation zum [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](whatis-hybrid-identity.md) nicht gelesen haben, finden Sie in der folgenden Tabelle Links zu verwandten Themen:

|Thema |Link|  
| --- | --- |
|Azure AD Connect herunterladen | [Azure AD Connect herunterladen](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Installieren mit den Express-Einstellungen | [Expressinstallation von Azure AD Connect](how-to-connect-install-express.md)|
|Installieren mit benutzerdefinierten Einstellungen | [Benutzerdefinierte Installation von Azure AD Connect](./how-to-connect-install-custom.md)|
|Upgrade von DirSync | [Upgrade von Azure AD-Synchronisierungstools (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Nach der Installation | [Überprüfen der Installation und Zuweisen von Lizenzen](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
