---
title: Authentifizierung – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über mögliche Maßnahmen zur Risikominderung im Bereich der Authentifizierung im Threat Modeling Tool. Lesen Sie die Informationen zur Risikominderung, und sehen Sie sich die Codebeispiele an.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: ee4dd70faab9ed44b1aa6ca8ca0ec517c7746f66
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832529"
---
# <a name="security-frame-authentication--mitigations"></a>Sicherheitsrahmen: Authentifizierung | Gegenmaßnahmen

| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **Web Application**    | <ul><li>[Verwenden Sie ggf. einen Standardauthentifizierungsmechanismus zur Authentifizierung bei Webanwendungen.](#standard-authn-web-app)</li><li>[Anwendungen müssen Szenarien mit nicht erfolgreicher Authentifizierung sicher behandeln.](#handle-failed-authn)</li><li>[Aktivieren Sie Step-up-Authentifizierung oder adaptive Authentifizierung.](#step-up-adaptive-authn)</li><li>[Stellen Sie sicher, dass Administratoroberflächen angemessen gesperrt sind.](#admin-interface-lockdown)</li><li>[Implementieren Sie sichere Funktionen für vergessene Kennwörter.](#forgot-pword-fxn)</li><li>[Stellen Sie sicher, dass die Kennwort- und die Kontorichtlinie implementiert werden.](#pword-account-policy)</li><li>[Implementieren Sie Kontrollen, um die Enumeration von Benutzernamen zu verhindern.](#controls-username-enum)</li></ul> |
| **Datenbank** | <ul><li>[Verwenden Sie beim Herstellen einer SQL Server-Verbindung nach Möglichkeit die Windows-Authentifizierung.](#win-authn-sql)</li><li>[Verwenden Sie beim Herstellen einer SQL-Datenbank-Verbindung nach Möglichkeit die Azure Active Directory-Authentifizierung.](#aad-authn-sql)</li><li>[Stellen Sie bei Verwendung des SQL-Authentifizierungsmodus sicher, dass die Konto- und die Kennwortrichtlinie für SQL Server erzwungen werden.](#authn-account-pword)</li><li>[Verwenden Sie für eigenständige Datenbanken keine SQL-Authentifizierung.](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Verwenden Sie gerätespezifische Authentifizierungsanmeldeinformationen mit SAS-Token.](#authn-sas-tokens)</li></ul> |
| **Azure-Vertrauensstellungsgrenze** | <ul><li>[Aktivieren Sie Azure AD Multi-Factor Authentication für Azure-Administratoren.](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric-Vertrauensstellungsgrenze** | <ul><li>[Beschränken Sie den anonymen Zugriff auf den Service Fabric-Cluster.](#anon-access-cluster)</li><li>[Stellen Sie sicher, dass sich das Client-zu-Knoten-Zertifikat von Service Fabric vom Knoten-zu-Knoten-Zertifikat unterscheidet.](#fabric-cn-nn)</li><li>[Verwenden Sie AAD, um Clients bei Service Fabric-Clustern zu authentifizieren.](#aad-client-fabric)</li><li>[Stellen Sie sicher, dass Service Fabric-Zertifikate von einer genehmigten Zertifizierungsstelle (Certificate Authority, CA) bezogen werden.](#fabric-cert-ca)</li></ul> |
| **Identity Server** | <ul><li>[Verwenden Sie von Identity Server unterstützte Standardauthentifizierungsszenarien.](#standard-authn-id)</li><li>[Überschreiben Sie den standardmäßigen Identity Server-Tokencache mit einer skalierbaren Alternative.](#override-token)</li></ul> |
| **Computer-Vertrauensstellungsgrenze** | <ul><li>[Stellen Sie sicher, dass die Binärdateien der bereitgestellten Anwendung digital signiert sind.](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Aktivieren Sie die Authentifizierung beim Herstellen einer Verbindung mit MSMQ-Warteschlangen in WCF.](#msmq-queues)</li><li>[Legen Sie „message clientCredentialType“ nicht auf „None“ fest.](#message-none)</li><li>[Legen Sie „transport clientCredentialType“ nicht auf „None“ fest.](#transport-none)</li></ul> |
| **Web-API** | <ul><li>[Stellen Sie sicher, dass Web-APIs durch Standardauthentifizierungsverfahren geschützt sind.](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Verwenden Sie von Azure Active Directory unterstützte Standardauthentifizierungsszenarien.](#authn-aad)</li><li>[Überschreiben Sie den standardmäßigen ADAL-Tokencache mit einer skalierbaren Alternative.](#adal-scalable)</li><li>[Verwenden Sie TokenReplayCache, um die Wiedergabe von ADAL-Authentifizierungstoken zu verhindern.](#tokenreplaycache-adal)</li><li>[Verwenden Sie ADAL-Bibliotheken, um Tokenanforderungen von OAuth2-Clients an AAD (oder lokales AD) zu verwalten.](#adal-oauth2)</li></ul> |
| **Zwischengeschaltetes IoT-Gateway** | <ul><li>[Authentifizieren Sie Geräte, die eine Verbindung mit dem zwischengeschalteten Gateway herstellen.](#authn-devices-field)</li></ul> |
| **IoT-Cloudgateway** | <ul><li>[Stellen Sie sicher, dass Geräte, die eine Verbindung mit dem Cloudgateway herstellen, authentifiziert werden.](#authn-devices-cloud)</li><li>[Verwenden Sie gerätespezifische Anmeldeinformationen für die Authentifizierung.](#authn-cred)</li></ul> |
| **Azure Storage (in englischer Sprache)** | <ul><li>[Stellen Sie sicher, dass nur die erforderlichen Container und Blobs anonymen Lesezugriff erhalten.](#req-containers-anon)</li><li>[Gewähren Sie mithilfe von SAS oder SAP eingeschränkten Zugriff auf Objekte im Azure-Speicher.](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Verwenden Sie ggf. einen Standardauthentifizierungsmechanismus zur Authentifizierung bei Webanwendungen.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| Details | <p>Bei der Authentifizierung wird die Identität einer Entität überprüft – in der Regel anhand von Anmeldeinformationen wie Benutzername und Kennwort. Hierzu stehen mehrere Authentifizierungsprotokolle zur Verfügung. Einige davon sind im Anschluss aufgeführt:</p><ul><li>Clientzertifikate</li><li>Windows-basiert</li><li>Formularbasiert</li><li>Verbund – ADFS</li><li>Verbund – Azure AD</li><li>Verbund – Identity Server</li></ul><p>Verwenden Sie ggf. einen Standardauthentifizierungsmechanismus, um den Quellprozess zu identifizieren.</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Anwendungen müssen Szenarien mit nicht erfolgreicher Authentifizierung sicher behandeln.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| Details | <p>Anwendungen mit expliziter Benutzerauthentifizierung müssen Szenarien mit nicht erfolgreicher Authentifizierung sicher behandeln. Der Authentifizierungsmechanismus muss Folgendes bewirken:</p><ul><li>Er muss den Zugriff auf privilegierte Ressourcen verweigern, wenn die Authentifizierung nicht erfolgreich war.</li><li>Er muss eine generische Fehlermeldung anzeigen, wenn die Authentifizierung nicht erfolgreich war und der Zugriff verweigert wird.</li></ul><p>Überprüfen Sie, ob Folgendes erfüllt ist:</p><ul><li>Privilegierte Ressourcen sind nach nicht erfolgreicher Anmeldung geschützt.</li><li>Bei nicht erfolgreicher Authentifizierung wird eine generische Fehlermeldung angezeigt, und es werden Zugriffsverweigerungsereignisse generiert.</li><li>Konten werden nach einer übermäßigen Anzahl nicht erfolgreicher Versuche deaktiviert.</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Aktivieren Sie Step-up-Authentifizierung oder adaptive Authentifizierung.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| Details | <p>Vergewissern Sie sich, dass die Anwendung über eine zusätzliche Autorisierung verfügt (etwa Step-up-Authentifizierung oder adaptive Authentifizierung – über die mehrstufige Authentifizierung, beispielsweise mittels OTP-Versand per SMS oder E-Mail, oder durch eine Aufforderung zur erneuten Authentifizierung), damit dem Benutzer eine Aufforderung angezeigt wird, bevor er Zugriff auf sensible Daten erhält. Diese Regel gilt auch für wichtige Änderungen an einem Konto oder an einer Aktion.</p><p>Das bedeutet auch, dass die Anpassung der Authentifizierung so implementiert werden muss, dass die Anwendung eine ordnungsgemäße kontextabhängige Autorisierung erzwingt, um nicht autorisierte Manipulationen (beispielsweise der Parameter) zu verhindern.</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Stellen Sie sicher, dass Administratoroberflächen angemessen gesperrt sind.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| Details | Die erste Möglichkeit besteht darin, den Zugriff auf die Administratoroberfläche nur über einen bestimmten Quell-IP-Adressbereich zu gewähren. Sollte das nicht möglich sein, empfiehlt es sich, für die Anmeldung bei der Administratoroberfläche eine Step-up-Authentifizierung oder eine adaptive Authentifizierung zu erzwingen. |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Implementieren Sie sichere Funktionen für vergessene Kennwörter.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| Details | <p>Vergewissern Sie sich zunächst, dass bei vergessenen Kennwörtern und bei anderen Wiederherstellungspfaden nicht das Kennwort, sondern ein Link mit einem Aktivierungstoken gesendet wird, das nur für einen bestimmten Zeitraum gültig ist. Bei Bedarf kann auch noch eine Softtoken-basierte Authentifizierung (beispielsweise per SMS-Token oder nativer mobiler Anwendung) erforderlich gemacht werden, bevor der Link übermittelt wird. Zweitens: Achten Sie darauf, dass das Benutzerkonto nicht während des Bezugs eines neuen Kennworts gesperrt wird.</p><p>Dies kann zu einem Denial-of-Service-Angriff führen, wenn ein Angreifer beschließt, die Benutzer mit einem automatisierten Angriff absichtlich auszusperren. Drittens: Bei der Initiierung der Anforderung eines neuen Kennworts muss eine generische Meldung angezeigt werden, um die Enumeration von Benutzernamen zu verhindern. Viertens: Unterbinden Sie immer die Verwendung alter Kennwörter, und implementieren Sie eine Richtlinie für sichere Kennwörter.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Stellen Sie sicher, dass die Kennwort- und die Kontorichtlinie implementiert werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| Details | <p>Implementieren Sie eine Kennwort- und eine Kontorichtlinie, die die Regeln und bewährten Methoden der Organisation erfüllen.</p><p>Zur Abwehr Brute-Force- und wörterbuchbasierter Angriffe muss eine Richtlinie für sichere Kennwörter implementiert werden, um sicherzustellen, dass Benutzer komplexe Kennwörter erstellen – beispielsweise mit einer Mindestlänge von 12 Zeichen sowie mit einer Kombination aus alphanumerischen Zeichen und Sonderzeichen.</p><p>Kontosperrungsrichtlinien können wie folgt implementiert werden:</p><ul><li>**Gemäßigte Sperrung:** Eine gute Option, um Ihre Benutzer vor Brute-Force-Angriffen zu schützen. Falls der Benutzer beispielsweise dreimal ein falsches Kennwort eingibt, kann die Anwendung das Konto eine Minute lang sperren, um die Brute-Force-Ermittlung des Kennworts zu verlangsamen und somit für den Angreifer unattraktiver zu machen. Wenn Sie in diesem Beispiel eine harte Sperrung implementieren, bewirkt dies einen Denial-of-Service-Angriff, da Konten dauerhaft gesperrt werden. Alternativ kann die Anwendung ein Einmalkennwort (One Time Password, OTP) generieren und out-of-band (etwa per E-Mail oder SMS) an den Benutzer senden. Eine weitere Möglichkeit ist die Implementierung eines CAPTCHAs nach Erreichen einer bestimmten Anzahl nicht erfolgreicher Versuche.</li><li>**Rigorose Sperrung:** Diese Art von Sperrung empfiehlt sich, wenn ein Benutzerangriff auf Ihre Anwendung festgestellt wird und das Konto des Angreifers dauerhaft gesperrt werden soll, bis ein Sicherheitsteam Gelegenheit hatte, den Vorfall zu untersuchen. Danach können Sie dem Benutzer wieder Zugriff auf sein Konto gewähren oder rechtliche Schritte gegen ihn einleiten. Bei diesem Ansatz kann der Angreifer nicht weiter in Ihre Anwendung und Infrastruktur vordringen.</li></ul><p>Vergewissern Sie sich zur Abwehr von Angriffen auf Standardkonten und vorhersehbare Konten, dass alle Schlüssel und Kennwörter ersetzbar sind und nach der Installation generiert oder ersetzt werden.</p><p>Falls die Anwendung Kennwörter automatisch generieren muss, stellen Sie sicher, dass es sich um Zufallskennwörter mit hoher Entropie handelt.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementieren Sie Kontrollen, um die Enumeration von Benutzernamen zu verhindern.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Alle Fehlermeldungen müssen generalisiert werden, um die Enumeration von Benutzernamen zu verhindern. Manchmal lassen sich Informationslecks in Funktionen nicht vermeiden (beispielsweise bei einer Registrierungsseite). Hier müssen ratenbegrenzende Methoden wie CAPTCHA verwendet werden, um automatisierte Angriffe zu verhindern. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Verwenden Sie beim Herstellen einer SQL Server-Verbindung nach Möglichkeit die Windows-Authentifizierung.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Lokal |
| **Attribute**              | SQL-Version: Alle |
| **Referenzen**              | [Auswählen eines Authentifizierungsmodus](/sql/relational-databases/security/choose-an-authentication-mode) |
| **Schritte** | Die Windows-Authentifizierung verwendet das Kerberos-Sicherheitsprotokoll, stellt Maßnahmen zur Durchsetzung von Kennwortrichtlinien, z. B. zur Überprüfung der Komplexität sicherer Kennwörter, bereit und bietet Unterstützung für Kontosperrungen und Ablauf von Kennwörtern.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Verwenden Sie beim Herstellen einer SQL-Datenbank-Verbindung nach Möglichkeit die Azure Active Directory-Authentifizierung.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | SQL Azure |
| **Attribute**              | SQL-Version: V12 |
| **Referenzen**              | [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](../../azure-sql/database/authentication-aad-overview.md) |
| **Schritte** | **Mindestversion:** Azure SQL-Datenbank V12 ist erforderlich, um Azure SQL-Datenbank die Verwendung der AAD-Authentifizierung für das Microsoft-Verzeichnis zu ermöglichen. |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Stellen Sie bei Verwendung des SQL-Authentifizierungsmodus sicher, dass die Konto- und die Kennwortrichtlinie für SQL Server erzwungen werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Kennwortrichtlinie](/previous-versions/sql/sql-server-2012/ms161959(v=sql.110)) |
| **Schritte** | Bei Verwendung der SQL Server-Authentifizierung werden in SQL Server Anmeldungen erstellt, die nicht auf Windows-Benutzerkonten basieren. Der Benutzername und das Kennwort werden mithilfe von SQL Server erstellt und in SQL Server gespeichert. SQL Server kann die Kennwortrichtlinienmechanismen von Windows verwenden. Es kann die in Windows verwendeten Komplexitäts- und Ablaufrichtlinien auf in SQL Server verwendete Kennwörter anwenden. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Verwenden Sie für eigenständige Datenbanken keine SQL-Authentifizierung.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Lokal, SQL Azure |
| **Attribute**              | SQL-Version: MSSQL2012, SQL-Version: V12 |
| **Referenzen**              | [Bewährte Methoden für die Sicherheit eigenständiger Datenbanken](/sql/relational-databases/databases/security-best-practices-with-contained-databases) |
| **Schritte** | Ohne erzwungene Kennwortrichtlinie erhöht sich das Risiko, dass in einer eigenständigen Datenbank unsichere Anmeldeinformationen eingerichtet werden. Nutzen Sie die Windows-Authentifizierung. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Verwenden Sie gerätespezifische Authentifizierungsanmeldeinformationen mit SAS-Token.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Event Hub |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Event Hubs-Authentifizierung und -Sicherheitsmodell (Übersicht)](../../event-hubs/authenticate-shared-access-signature.md) |
| **Schritte** | <p>Das Event Hubs-Sicherheitsmodell basiert auf einer Kombination aus SAS-Token (Shared Access Signature) und Ereignisherausgebern. Der Herausgebername stellt die Geräte-ID dar, die das Token erhält. Dadurch lassen sich die generierten Token leichter den entsprechenden Geräten zuordnen.</p><p>Alle Nachrichten werden dienstseitig mit dem Ursprung markiert, was die Erkennung nutzlastbasierter Spoofingversuche ermöglicht. Generieren Sie beim Authentifizieren von Geräten ein gerätespezifisches SAS-Token, das einem eindeutigen Herausgeber zugeordnet ist.</p>|

## <a name="enable-azure-ad-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Aktivieren Sie Azure AD Multi-Factor Authentication für Azure-Administratoren.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure-Vertrauensstellungsgrenze |
| **SDL-Phase**               | Bereitstellung |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Was ist Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md) |
| **Schritte** | <p>Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist eine Authentifizierungsmethode, die die Verwendung mehrerer Überprüfungsmethoden erfordert und eine wichtige zweite Sicherheitsebene für Benutzeranmeldungen und Transaktionen bietet. Dies funktioniert durch das Anfordern von zwei oder mehr der folgenden Verifizierungsmethoden:</p><ul><li>Etwas, das Sie wissen (in der Regel ein Kennwort)</li><li>Etwas, das Sie besitzen (ein vertrauenswürdiges Gerät, das sich nicht leicht duplizieren lässt, z. B. ein Telefon)</li><li>Etwas, das Sie sind (biometrisch)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Beschränken Sie den anonymen Zugriff auf den Service Fabric-Cluster.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Service Fabric-Vertrauensstellungsgrenze |
| **SDL-Phase**               | Bereitstellung |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Umgebung: Azure  |
| **Referenzen**              | [Szenarien für die Clustersicherheit in Service Fabric](../../service-fabric/service-fabric-cluster-security.md) |
| **Schritte** | <p>Cluster sollten immer gesichert werden, um zu verhindern, dass nicht autorisierte Benutzer eine Verbindung mit dem Cluster herstellen können, insbesondere dann, wenn im Cluster Produktionsworkloads ausgeführt werden.</p><p>Achten Sie beim Erstellen eines Service Fabric-Clusters darauf, dass der Sicherheitsmodus auf „Sicher“ festgelegt ist, und konfigurieren Sie das erforderliche X.509-Serverzertifikat. Bei Erstellung eines unsicheren Clusters kann jeder anonyme Benutzer eine Verbindung herstellen, wenn der Cluster Verwaltungsendpunkte im öffentlichen Internet verfügbar macht.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Stellen Sie sicher, dass sich das Client-zu-Knoten-Zertifikat von Service Fabric vom Knoten-zu-Knoten-Zertifikat unterscheidet.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Service Fabric-Vertrauensstellungsgrenze |
| **SDL-Phase**               | Bereitstellung |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Umgebung: Azure, Umgebung: eigenständig |
| **Referenzen**              | [Szenarien für die Clustersicherheit in Service Fabric](../../service-fabric/service-fabric-cluster-security.md#client-to-node-certificate-security), [Herstellen einer Verbindung mit einem sicheren Cluster](../../service-fabric/service-fabric-connect-to-secure-cluster.md) |
| **Schritte** | <p>Client-zu-Knoten-Zertifikatsicherheit wird beim Erstellen des Clusters über das Azure-Portal, über Azure Resource Manager-Vorlagen oder über eine eigenständige JSON-Vorlage durch Angeben eines Administratorclientzertifikats und/oder eines Benutzerclientzertifikats konfiguriert.</p><p>Die angegebenen Administrator- und Benutzerclientzertifikate müssen sich von den primären und sekundären Zertifikaten unterscheiden, die Sie für Knoten-zu-Knoten-Sicherheit angeben.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Verwenden Sie AAD, um Clients bei Service Fabric-Clustern zu authentifizieren.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Service Fabric-Vertrauensstellungsgrenze |
| **SDL-Phase**               | Bereitstellung |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Umgebung: Azure |
| **Referenzen**              | [Sicherheitsempfehlungen](../../service-fabric/service-fabric-cluster-security.md#security-recommendations) |
| **Schritte** | Unter Azure ausgeführte Cluster können den Zugriff auf die Verwaltungsendpunkte nicht nur mit Clientzertifikaten, sondern auch mit Azure Active Directory (AAD) schützen. Für Azure-Cluster wird die Verwendung der AAD-Sicherheit empfohlen, um Clients und Zertifikate für die Knoten-zu-Knoten-Sicherheit zu authentifizieren.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Stellen Sie sicher, dass Service Fabric-Zertifikate von einer genehmigten Zertifizierungsstelle (Certificate Authority, CA) bezogen werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Service Fabric-Vertrauensstellungsgrenze |
| **SDL-Phase**               | Bereitstellung |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Umgebung: Azure |
| **Referenzen**              | [X.509-Zertifikate und Service Fabric](../../service-fabric/service-fabric-cluster-security.md#x509-certificates-and-service-fabric) |
| **Schritte** | <p>Service Fabric verwendet X.509-Serverzertifikate für die Authentifizierung von Knoten und Clients.</p><p>Wichtige Punkte bei der Verwendung von Zertifikaten in Service Fabric:</p><ul><li>Zertifikate, die in Clustern mit Produktionsworkloads verwendet werden, müssen entweder mit einem korrekt konfigurierten Windows Server-Zertifikatsdienst erstellt oder über eine genehmigte Zertifizierungsstelle (Certificate Authority, CA) bezogen werden. Die Zertifizierungsstelle kann eine genehmigte externe Zertifizierungsstelle oder eine ordnungsgemäß verwaltete interne Public Key-Infrastruktur (PKI) sein.</li><li>Verwenden Sie in der Produktion niemals temporäre Zertifikate oder Testzertifikate, die mit Tools wie „MakeCert.exe“ erstellt wurden.</li><li>Verwenden Sie selbstsignierte Zertifikate nur für Testcluster, aber nicht in der Produktion.</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Verwenden Sie von Identity Server unterstützte Standardauthentifizierungsszenarien.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Identity Server |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [IdentityServer3 - The Big Picture](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) (Übersicht über IdentityServer3) |
| **Schritte** | <p>Typische, von Identity Server unterstützte Interaktionen:</p><ul><li>Browser kommunizieren mit Webanwendungen.</li><li>Webanwendungen kommunizieren mit Web-APIs (manchmal selbstständig, manchmal im Auftrag eines Benutzers).</li><li>Browserbasierte Anwendungen kommunizieren mit Web-APIs.</li><li>Native Anwendungen kommunizieren mit Web-APIs.</li><li>Serverbasierte Anwendungen kommunizieren mit Web-APIs.</li><li>Web-APIs kommunizieren mit Web-APIs (manchmal selbstständig, manchmal im Auftrag eines Benutzers).</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Überschreiben Sie den standardmäßigen Identity Server-Tokencache mit einer skalierbaren Alternative.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Identity Server |
| **SDL-Phase**               | Bereitstellung |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Identity Server Deployment - Caching](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) (Identity Server-Bereitstellung – Caching) |
| **Schritte** | <p>Identity Server verfügt über einen einfachen integrierten In-Memory-Cache. Dieser eignet sich zwar gut für kleinere native Apps, lässt sich aber aus folgenden Gründen nicht für Mid-Tier- und Back-End-Anwendungen skalieren:</p><ul><li>Auf diese Anwendungen greifen viele Benutzer gleichzeitig zu. Die Speicherung aller Zugriffstoken im gleichen Speicher führt zu Isolationsproblemen sowie zu Herausforderungen bei umfangreichen Systemen: Bei einer großen Anzahl von Benutzern (jeder davon mit so vielen Token wie Ressourcen, auf die die App in seinem Auftrag zugreift) ergeben sich unter Umständen riesige Mengen und äußerst aufwendige Suchvorgänge.</li><li>Diese Anwendungen werden in der Regel in verteilten Topologien bereitgestellt, in denen mehrere Knoten Zugriff auf den gleichen Cache benötigen.</li><li>Zwischengespeicherte Token müssen bei Prozesswiederverwendungen und Deaktivierungen erhalten bleiben.</li><li>Aus den oben genannten Gründen empfiehlt es sich bei der Implementierung von Web-Apps, den standardmäßigen Identity Server-Tokencache mit einer skalierbaren Alternative (beispielsweise Azure Cache for Redis) zu überschreiben.</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Stellen Sie sicher, dass die Binärdateien der bereitgestellten Anwendung digital signiert sind.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Computer-Vertrauensstellungsgrenze |
| **SDL-Phase**               | Bereitstellung |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Stellen Sie sicher, dass die Binärdateien der bereitgestellten Anwendung digital signiert sind, damit die Integrität der Binärdateien überprüft werden kann.|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Aktivieren Sie die Authentifizierung beim Herstellen einer Verbindung mit MSMQ-Warteschlangen in WCF.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein, .NET Framework 3 |
| **Attribute**              | – |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)) |
| **Schritte** | Falls das Programm beim Herstellen einer Verbindung mit MSMQ-Warteschlangen keine Authentifizierung aktiviert, kann ein Angreifer anonym Nachrichten an die Warteschlange senden, die dann verarbeitet werden. Wenn bei der Verbindungsherstellung mit einer Warteschlange, die eine Nachricht an ein anderes Programm übermittelt, keine Authentifizierung verwendet wird, kann ein Angreifer eine anonyme (schädliche) Nachricht senden.|

### <a name="example"></a>Beispiel
Das Element `<netMsmqBinding/>` der folgenden WCF-Konfigurationsdatei weist WCF an, die Authentifizierung zu deaktivieren, wenn zur Übermittlung einer Nachricht eine Verbindung mit einer MSMQ-Warteschlange hergestellt wird.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Konfigurieren Sie MSMQ so, dass bei allen ein- und ausgehenden Nachrichten immer die Windows-Domänenauthentifizierung oder die Zertifikatauthentifizierung erforderlich ist.

### <a name="example"></a>Beispiel
Das Element `<netMsmqBinding/>` der folgenden WCF-Konfigurationsdatei weist WCF an, die Zertifikatauthentifizierung zu aktivieren, wenn eine Verbindung mit einer MSMQ-Warteschlange hergestellt wird. Der Client wird mit X.509-Zertifikaten authentifiziert. Das Clientzertifikat muss im Zertifikatspeicher des Servers vorhanden sein.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>Legen Sie „message clientCredentialType“ nicht auf „None“ fest.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | .NET Framework 3 |
| **Attribute**              | Art der Clientanmeldeinformationen: keine |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Schritte** | Ohne Authentifizierung kann jeder auf diesen Dienst zugreifen. Ein Dienst ohne Clientauthentifizierung gewährt allen Benutzern Zugriff. Konfigurieren Sie die Anwendung so, dass eine Authentifizierung anhand von Clientanmeldeinformationen erfolgt. Legen Sie hierzu „message clientCredentialType“ auf „Windows“ oder „Certificate“ fest. |

### <a name="example"></a>Beispiel
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>Legen Sie „transport clientCredentialType“ nicht auf „None“ fest.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Generisch, .NET Framework 3 |
| **Attribute**              | Art der Clientanmeldeinformationen: keine |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Schritte** | Ohne Authentifizierung kann jeder auf diesen Dienst zugreifen. Ein Dienst ohne Clientauthentifizierung gewährt allen Benutzern Zugriff auf seine Funktionen. Konfigurieren Sie die Anwendung so, dass eine Authentifizierung anhand von Clientanmeldeinformationen erfolgt. Legen Sie hierzu „transport clientCredentialType“ auf „Windows“ oder „Certificate“ fest. |

### <a name="example"></a>Beispiel
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Stellen Sie sicher, dass Web-APIs durch Standardauthentifizierungsverfahren geschützt sind.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Authentication and Authorization in ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) (Authentifizierung und Autorisierung in der ASP.NET-Web-API), [External Authentication Services with ASP.NET Web API (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) (Externe Authentifizierungsdienste mit ASP.NET-Web-API (C#) |
| **Schritte** | <p>Bei der Authentifizierung wird die Identität einer Entität überprüft – in der Regel anhand von Anmeldeinformationen wie Benutzername und Kennwort. Hierzu stehen mehrere Authentifizierungsprotokolle zur Verfügung. Einige davon sind im Anschluss aufgeführt:</p><ul><li>Clientzertifikate</li><li>Windows-basiert</li><li>Formularbasiert</li><li>Verbund – ADFS</li><li>Verbund – Azure AD</li><li>Verbund – Identity Server</li></ul><p>Unter den Links im Abschnitt „Referenzen“ finden Sie detaillierte Informationen zur Implementierung der einzelnen Authentifizierungsschemas zum Schutz einer Web-API.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Verwenden Sie von Azure Active Directory unterstützte Standardauthentifizierungsszenarien.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure AD |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Authentifizierungsszenarien für Azure AD](../../active-directory/develop/authentication-vs-authorization.md), [Azure Active Directory-Codebeispiele](../../active-directory/azuread-dev/sample-v1-code.md), [Entwicklerhandbuch zu Azure Active Directory](../../active-directory/develop/index.yml) |
| **Schritte** | <p>Azure Active Directory (Azure AD) vereinfacht die Authentifizierung für Entwickler durch Bereitstellung von IaaS (Identity as a Service) sowie durch Unterstützung branchenüblicher Protokolle wie OAuth 2.0 und OpenID Connect. Azure AD unterstützt fünf primäre Anwendungsszenarien:</p><ul><li>Webbrowser zu Webanwendung: Ein Benutzer muss sich bei einer durch Azure AD geschützten Webanwendung anmelden.</li><li>Single-Page-Anwendung (SPA): Ein Benutzer muss sich bei einer durch Azure AD geschützten Single-Page-Webanwendung anmelden.</li><li>Native Anwendung zu Web-API: Eine native Anwendung auf einem Smartphone, Tablet oder PC muss einen Benutzer authentifizieren, um Ressourcen von einer Web-API abzurufen, die durch Azure AD geschützt wird.</li><li>Webanwendung zu Web-API: Eine Webanwendung muss Ressourcen von einer durch Azure AD geschützten Web-API abrufen.</li><li>Daemon- oder Serveranwendung zu Web-API: Eine Daemon- oder Serveranwendung ohne Webbenutzeroberfläche muss Ressourcen von einer Web-API abrufen, die durch Azure AD geschützt wird.</li></ul><p>Detaillierte Informationen zur Implementierung finden Sie unter den Links im Abschnitt „Referenzen“.</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Überschreiben Sie den standardmäßigen ADAL-Tokencache mit einer skalierbaren Alternative.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure AD |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Modern Authentication with Azure Active Directory for Web Applications](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) (Moderne Authentifizierung mit Azure Active Directory für Webanwendungen), [Using Redis as ADAL token cache](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/) (Verwenden von Redis als ADAL-Tokencache)  |
| **Schritte** | <p>Von Active Directory Authentication Library (ADAL) wird als Standardcache ein In-Memory-Cache verwendet, der auf einem statischen, prozessweit verfügbaren Speicher basiert. Dieser eignet sich zwar für kleinere native Apps, lässt sich aber aus folgenden Gründen nicht für Mid-Tier- und Back-End-Anwendungen skalieren:</p><ul><li>Auf diese Anwendungen greifen viele Benutzer gleichzeitig zu. Die Speicherung aller Zugriffstoken im gleichen Speicher führt zu Isolationsproblemen sowie zu Herausforderungen bei umfangreichen Systemen: Bei einer großen Anzahl von Benutzern (jeder davon mit so vielen Token wie Ressourcen, auf die die App in seinem Auftrag zugreift) ergeben sich unter Umständen riesige Mengen und äußerst aufwendige Suchvorgänge.</li><li>Diese Anwendungen werden in der Regel in verteilten Topologien bereitgestellt, in denen mehrere Knoten Zugriff auf den gleichen Cache benötigen.</li><li>Zwischengespeicherte Token müssen bei Prozesswiederverwendungen und Deaktivierungen erhalten bleiben.</li></ul><p>Aus den oben genannten Gründen empfiehlt es sich bei der Implementierung von Web-Apps, den standardmäßigen ADAL-Tokencache mit einer skalierbaren Alternative (beispielsweise Azure Cache for Redis) zu überschreiben.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Verwenden Sie TokenReplayCache, um die Wiedergabe von ADAL-Authentifizierungstoken zu verhindern.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure AD |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Modern Authentication with Azure Active Directory for Web Applications](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) (Moderne Authentifizierung mit Azure Active Directory für Webanwendungen) |
| **Schritte** | <p>Mithilfe der TokenReplayCache-Eigenschaft können Entwickler einen Tokenwiedergabecache definieren. In diesem Speicher können Token gespeichert werden, um die mehrmalige Verwendung eines Tokens zu verhindern.</p><p>Hierbei handelt es sich um eine Maßnahme gegen so genannte Tokenwiedergabeangriffe: Ein Angreifer, der ein bei der Anmeldung gesendetes Token abfängt, kann versuchen, das Token erneut an die App zu senden (es also gewissermaßen wiederzugeben), um eine neue Sitzung zu initiieren. Ein Beispiel: Nach erfolgreicher Benutzerauthentifizierung erfolgt im OIDC-Codegewährungsflow eine Anforderung an den Endpunkt „/signin-oidc“ der vertrauenden Seite mit den Parametern „id_token“, „code“ und „state“.</p><p>Die vertrauende Seite überprüft diese Anforderung und initiiert eine neue Sitzung. Wenn nun ein Angreifer diese Anforderung abfängt und wiedergibt, kann er erfolgreich eine Sitzung initiieren und den Benutzer spoofen. Die Nonce in OpenID Connect kann die Umstände, unter denen der Angriff gelingt, nur einschränken, aber nicht vollständig beseitigen. Zum Schutz ihrer Anwendungen können Entwickler eine ITokenReplayCache-Implementierung bereitstellen und TokenReplayCache eine Instanz zuweisen.</p>|

### <a name="example"></a>Beispiel
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Beispiel
Hier sehen Sie eine Beispielimplementierung der ITokenReplayCache-Schnittstelle. (Passen Sie dieses Beispiel an, und implementieren Sie Ihr projektspezifisches Cacheframework.)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
Auf den implementierten Cache muss in den OIDC-Optionen über die Eigenschaft „TokenValidationParameters“ verwiesen werden:
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Melden Sie sich bei Ihrer lokalen, durch OIDC geschützten Anwendung an, und erfassen Sie die an den Endpunkt `"/signin-oidc"` gerichtete Anforderung in Fiddler, um die Wirksamkeit dieser Konfiguration zu testen. Wenn kein Schutz vorhanden ist und diese Anforderung in Fiddler wiedergegeben wird, wird ein neues Sitzungscookie festgelegt. Wird die Anforderung wiedergegeben, nachdem der TokenReplayCache-Schutz hinzugefügt wurde, löst die Anwendung eine Ausnahme aus: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Verwenden Sie ADAL-Bibliotheken, um Tokenanforderungen von OAuth2-Clients an AAD (oder lokales AD) zu verwalten.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure AD |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [ADAL](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) |
| **Schritte** | <p>Die Azure AD-Authentifizierungsbibliothek (ADAL) ermöglicht es Entwicklern von Clientanwendungen, auf einfache Weise eine Benutzerauthentifizierung mit Active Directory (Cloud oder lokal) bereitzustellen und anschließend Zugriffstoken zur Absicherung von API-Aufrufen abzurufen.</p><p>ADAL verfügt über zahlreiche Features zur Vereinfachung der Authentifizierung für Entwickler. Dazu gehören beispielsweise die Unterstützung von asynchronen Methoden, ein konfigurierbarer Tokencache zum Speichern von Zugriffstoken und Aktualisierungstoken, eine automatische Tokenaktualisierung bei Ablauf eines Zugriffstokens, sofern ein Aktualisierungstoken verfügbar ist, und vieles mehr.</p><p>Da durch ADAL die meisten komplexen Aspekte abgedeckt werden, können sich Entwickler auf die Geschäftslogik in ihren Anwendungen konzentrieren und auf einfache Weise Ressourcen sichern, ohne Sicherheitsexperten sein zu müssen. Für .NET, JavaScript (Client und Node.js), Python, iOS, Android und Java stehen jeweils separate Bibliotheken zur Verfügung.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Authentifizieren Sie Geräte, die eine Verbindung mit dem zwischengeschalteten Gateway herstellen.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Zwischengeschaltetes IoT-Gateway |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Stellen Sie sicher, dass jedes Gerät durch das zwischengeschaltete Gateway authentifiziert wird, bevor Daten des Geräts akzeptiert werden und die Upstreamkommunikation mit dem Cloudgateway zugelassen wird. Stellen Sie außerdem sicher, dass bei der Verbindungsherstellung gerätespezifische Anmeldeinformationen verwendet werden, damit einzelne Geräte eindeutig identifiziert werden können.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Stellen Sie sicher, dass Geräte, die eine Verbindung mit dem Cloudgateway herstellen, authentifiziert werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Cloudgateway |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein, C#, Node.js  |
| **Attribute**              | N/V, Wahl des Gateways – Azure IoT Hub |
| **Referenzen**              | N/V, [Erste Schritte mit Azure IoT Hub (.NET)](../../iot-hub/quickstart-send-telemetry-dotnet.md), [Erste Schritte mit Azure IoT Hub (Node)](../../iot-hub/quickstart-send-telemetry-node.md), [Securing IoT with SAS and certificates](../../iot-hub/iot-hub-devguide-security.md) (Schützen von IoT mit SAS und Zertifikaten), [Git-Repository](https://github.com/Azure/azure-iot-sdks/) |
| **Schritte** | <ul><li>**Allgemein:** Authentifizieren Sie das Gerät mittels TLS (Transport Layer Security) oder IPsec. Die Infrastruktur sollte die Verwendung eines vorinstallierten Schlüssels (Pre-Shared Key, PSK) auf den Geräten unterstützen, bei denen keine vollständige asymmetrische Verschlüsselung möglich ist. Nutzen Sie Azure AD, OAuth.</li><li>**C#:** Beim Erstellen einer DeviceClient-Instanz erstellt die Create-Methode standardmäßig eine DeviceClient-Instanz, die das AMQP-Protokoll für die Kommunikation mit dem IoT Hub verwendet. Wenn Sie das HTTPS-Protokoll verwenden möchten, verwenden Sie die Überschreibung der Create-Methode, mit der Sie das Protokoll angeben können. Bei Verwendung des HTTPS-Protokolls müssen Sie Ihrem Projekt auch das NuGet-Paket `Microsoft.AspNet.WebApi.Client` hinzufügen, um den Namespace `System.Net.Http.Formatting` einzuschließen.</li></ul>|

### <a name="example"></a>Beispiel
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Beispiel
**Node.JS: Authentifizierung**
#### <a name="symmetric-key"></a>Symmetrischer Schlüssel
* Erstellen eines IoT-Hubs unter Azure
* Erstellen Sie einen Eintrag in der Geräteidentitätsregistrierung.
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Erstellen Sie ein simuliertes Gerät.
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS-Token
* Wird bei Verwendung eines symmetrischen Schlüssels intern generiert, kann aber auch explizit generiert und verwendet werden.
* Definieren Sie ein Protokoll: `var Http = require('azure-iot-device-http').Http;`
* Erstellen Sie ein SAS-Token:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Stellen Sie eine Verbindung her, und verwenden Sie dabei das SAS-Token:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>Zertifikate
* Generieren Sie ein selbst signiertes X.509-Zertifikat. Verwenden Sie hierzu ein Tool wie OpenSSL, um CERT- und KEY-Dateien zu erstellen und das Zertifikat und den Schlüssel zu speichern.
* Stellen Sie ein Gerät bereit, das eine sichere Verbindung mit Zertifikaten akzeptiert.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Stellen Sie unter Verwendung eines Zertifikats eine Verbindung mit einem Gerät her.
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    };
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Verwenden Sie gerätespezifische Anmeldeinformationen für die Authentifizierung.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Cloudgateway  |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Wahl des Gateways: Azure IoT Hub |
| **Referenzen**              | [Azure IoT Hub Security Tokens](../../iot-hub/iot-hub-devguide-security.md) (Azure IoT Hub – Sicherheitstoken) |
| **Schritte** | Verwenden Sie anstelle von SAS-Richtlinien auf IoT Hub-Ebene gerätespezifische Authentifizierungsanmeldeinformationen mit SAS-Token, die auf dem Geräteschlüssel oder Clientzertifikat basieren. Dies verhindert die Wiederverwendung der Authentifizierungstoken eines Geräts oder zwischengeschalteten Gateways durch ein anderes Gerät oder zwischengeschaltetes Gateway. |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Stellen Sie sicher, dass nur die erforderlichen Container und Blobs anonymen Lesezugriff erhalten.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | StorageType: Blob |
| **Referenzen**              | [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../../storage/blobs/anonymous-read-access-configure.md), [ Verwenden von Shared Access Signatures (SAS)](../../storage/common/storage-sas-overview.md) |
| **Schritte** | <p>Standardmäßig kann nur der Besitzer eines Speicherkontos auf einen Container und alle darin enthaltenen Blobs zugreifen. Wenn anonyme Benutzer Leseberechtigungen für einen Container und die enthaltenen Blobs erhalten sollen, kann der öffentliche Zugriff durch Festlegen der Containerberechtigungen gestattet werden. Anonyme Benutzer können Blobs innerhalb eines öffentlich zugänglichen Containers lesen, ohne dass die Anforderung authentifiziert werden muss.</p><p>Container stellen die folgenden Optionen zum Verwalten des Containerzugriffs bereit:</p><ul><li>Vollständiger öffentlicher Lesezugriff: Container- und Blobdaten können über anonyme Anforderungen gelesen werden. Clients können Blobs innerhalb des Containers über eine anonyme Anforderung aufzählen, können aber keine Container innerhalb des Speicherkontos aufzählen.</li><li>Öffentlicher Lesezugriff nur für Blobs: Blob-Daten innerhalb dieses Containers können über anonyme Anforderungen gelesen werden, Containerdaten sind aber nicht verfügbar. Clients können keine Blobs innerhalb des Containers über anonyme Anforderungen aufzählen.</li><li>Kein öffentlicher Lesezugriff: Container- und Blobdaten können nur vom Kontobesitzer gelesen werden.</li></ul><p>Anonymer Zugriff ist am besten für Szenarien, in denen bestimmte Blobs immer für den anonymen Lesezugriff zur Verfügung stehen sollen. Für eine präzisere Steuerung kann eine SAS (Shared Access Signature) erstellt werden, die es ermöglicht, eingeschränkten Zugriff mithilfe verschiedener Berechtigungen und über einen bestimmten Zeitraum zu delegieren. Achten Sie darauf, dass Container und Blobs, die möglicherweise sensible Daten enthalten, nicht versehentlich anonymen Zugriff erhalten.</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Gewähren Sie mithilfe von SAS oder SAP eingeschränkten Zugriff auf Objekte im Azure-Speicher.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage |
| **SDL-Phase**               | Entwickeln |
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | – |
| **Referenzen**              | [Verwenden von Shared Access Signatures (SAS)](../../storage/common/storage-sas-overview.md), [Shared Access Signatures, Teil 2: Erstellen und Verwenden einer SAS mit Blob Storage](../../storage/common/storage-sas-overview.md), [Delegieren des Zugriffs auf Objekte in Ihrem Konto mithilfe von SAS und gespeicherter Zugriffsrichtlinien](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Schritte** | <p>Shared Access Signatures (SAS) eignen sich sehr gut, um anderen Clients eingeschränkten Zugriff auf Objekte in Ihrem Speicherkonto zu gewähren, ohne den Kontozugriffsschlüssel weiterzugeben. Die SAS ist ein URI, dessen Abfrageparameter alle erforderlichen Informationen für den authentifizierten Zugriff auf eine Speicherressource enthalten. Für den Zugriff auf Speicherressourcen mit der SAS braucht der Client diese nur an den entsprechenden Konstruktor bzw. die entsprechende Methode zu übergeben.</p><p>Sie können eine SAS verwenden, um einem Client Zugriff auf Ressourcen in Ihrem Speicherkonto zu bieten, dem Sie Ihren Kontoschlüssel nicht anvertrauen möchten. Ihre Speicherkontoschlüssel bestehen aus Primär- und Sekundärschlüssel. Beide bieten Administratorzugriff auf Ihr Konto und alle enthaltenen Ressourcen. Wenn Sie Ihre Kontoschlüssel weitergeben, besteht die Gefahr von böswilliger oder fahrlässiger Nutzung. Shared Access Signatures bieten eine sichere alternative, um anderen Clients Lese-, Schreib- und Löschzugriff auf Daten in Ihrem Speicherkonto gemäß der von Ihnen definierten Berechtigungen zu bieten, ohne den Kontoschlüssel weitergeben zu müssen.</p><p>Wenn Sie über einen logischen Satz von Parametern verfügen, die jedes Mal ähnlich sind, empfiehlt sich die Verwendung einer gespeicherten Zugriffsrichtlinie (Stored Access Policy, SAP). Da Sie bei Verwendung einer SAS, die von einer gespeicherten Zugriffsrichtlinie abgeleitet ist, die SAS sofort widerrufen können, sollten Sie nach Möglichkeit immer die gespeicherte Zugriffsrichtlinie verwenden.</p>|