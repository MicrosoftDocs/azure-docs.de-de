---
title: Schützen von PaaS-Datenbanken in Azure | Microsoft-Dokumentation
description: 'Hier finden Sie Informationen zu bewährten Methoden im Zusammenhang mit der Sicherheit für Azure SQL-Datenbank und Azure Synapse Analytics, die zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen beitragen. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 278812754c636d434bf579c0408832f1e99d3445
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408072"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Bewährte Methoden zum Schützen von PaaS-Datenbanken in Azure

In diesem Artikel werden einige bewährte Methoden im Zusammenhang mit der Sicherheit für [Azure SQL-Datenbank](../../azure-sql/database/sql-database-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) erläutert, die zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen (Platform-as-a-Service) beitragen. Diese empfohlenen Vorgehensweisen sind aus unseren Erfahrungen mit Azure und den Erfahrungen von Kunden wie Ihnen abgeleitet.

Azure SQL-Datenbank und Azure Synapse Analytics stellen einen Dienst für relationale Datenbanken für Ihre internetbasierten Anwendungen bereit. Im Anschluss finden Sie Informationen zu Diensten, mit deren Hilfe Anwendungen und Daten bei Verwendung von Azure SQL-Datenbank und Azure Synapse Analytics in einer PaaS-Bereitstellung geschützt werden:

- Azure Active Directory-Authentifizierung (anstelle von SQL Server-Authentifizierung)
- Azure SQL-Firewall
- TDE (Transparent Data Encryption)

## <a name="use-a-centralized-identity-repository"></a>Verwenden eines zentralisierten Identitätsrepositorys

Azure SQL-Datenbank kann so konfiguriert werden, dass einer von zwei Authentifizierungstypen verwendet wird:

- Die **SQL-Authentifizierung** verwendet einen Benutzernamen und ein Kennwort. Bei der Erstellung des Servers für die Datenbank haben Sie eine Serveradministratoranmeldung mit Benutzername und Kennwort angegeben. Mit diesen Anmeldeinformationen können Sie sich bei jeder Datenbank auf diesem Server als Datenbankbesitzer authentifizieren.

- Die **Azure Active Directory-Authentifizierung** verwendet von Azure Active Directory verwaltete Identitäten und wird für verwaltete und integrierte Domänen unterstützt. Zur Verwendung der Azure Active Directory-Authentifizierung müssen Sie einen weiteren Serveradministrator mit der Bezeichnung „Azure AD-Administrator“ erstellen, der zum Verwalten von Azure Active Directory-Benutzern und -Gruppen berechtigt ist. Dieser Administrator kann außerdem die gleichen Aufgaben wie ein normaler Serveradministrator ausführen.

Die [Azure Active Directory-Authentifizierung](../../active-directory/develop/authentication-vs-authorization.md) ist ein Mechanismus zum Herstellen einer Verbindung mit Azure SQL-Datenbank und Azure Synapse Analytics unter Verwendung von Identitäten in Azure Active Directory (AD). Azure AD stellt eine Alternative zur SQL Server-Authentifizierung bereit, damit Sie die Verbreitung von Benutzeridentitäten über Datenbankserver hinweg beenden können. Die Azure AD-Authentifizierung ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten. Die zentrale ID-Verwaltung ermöglicht eine einheitliche Verwaltung von Datenbankbenutzern und vereinfacht die Berechtigungsverwaltung.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Vorteile der Verwendung der Azure AD- anstelle der SQL-Authentifizierung

- Über eine zentrale Stelle wird eine Kennwortrotation ermöglicht.
- Datenbankberechtigungen können mithilfe externer Azure AD-Gruppen verwaltet werden.
- Das Aktivieren der integrierten Windows-Authentifizierung und anderer von Azure AD unterstützte Authentifizierungsformen machen das Speichern von Kennwörtern überflüssig.
- Eigenständige Datenbankbenutzer werden zum Authentifizieren von Identitäten auf Datenbankebene verwendet.
- Unterstützt tokenbasierte Authentifizierung für Anwendungen, die eine Verbindung mit SQL-Datenbank herstellen.
- Unterstützt Active Directory-Verbunddienste (AD FS) sowie native Benutzer-/Kennwortauthentifizierung für ein lokales Azure AD ohne Domänensynchronisierung.
- Unterstützt Verbindungen von SQL Server Management Studio, bei denen universelle Active Directory-Authentifizierungsverfahren verwendet werden, zu denen auch die [Multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) gehört. MFA bietet eine sichere Authentifizierung über eine Reihe einfacher Überprüfungsoptionen – Telefonanruf, SMS, Smartcards mit PIN oder Benachrichtigung in einer mobilen App. Weitere Informationen finden Sie unter [Verwenden der mehrstufigen Azure Active Directory-Authentifizierung](../../azure-sql/database/authentication-mfa-ssms-overview.md).

Weitere Informationen zur Azure AD-Authentifizierung erhalten Sie unter:

- [Verwenden der Azure Active Directory-Authentifizierung](../../azure-sql/database/authentication-aad-overview.md)
- [Authentifizieren bei Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Tokenbasierte Authentifizierungsunterstützung für Azure SQL-Datenbank mithilfe der Azure AD-Authentifizierung](../../azure-sql/database/authentication-aad-overview.md)

> [!NOTE]
> Um sicherzustellen, dass Azure Active Directory für Ihre Umgebung geeignet ist, lesen Sie [Funktionen und Einschränkungen von Azure AD](../../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

## <a name="restrict-access-based-on-ip-address"></a>Beschränken des Zugriffs auf Grundlage der IP-Adresse

Sie können Firewallregeln erstellen, die die Bereiche der zulässigen IP-Adressen festlegen. Diese Regeln können auf Server- und auf Datenbankebene ausgerichtet werden. Wir empfehlen, nach Möglichkeit Firewallregeln auf Datenbankebene zu verwenden, um die Sicherheit und die Portabilität der Datenbank zu verbessern. Firewallregeln auf Serverebene eignen sich am besten für Administratoren und bei vielen Datenbanken mit identischen Zugriffsanforderungen, wenn Sie die Datenbanken nicht einzeln konfigurieren möchten.

Die Standardeinschränkungen der Quell-IP-Adresse von SQL-Datenbank erlauben den Zugriff von allen Azure-Adressen, einschließlich anderer Abonnements und Mandanten. Sie können dies einschränken und nur Ihren IP-Adressen den Zugriff auf die Instanz erlauben. Auch mit den Einschränkungen Ihrer SQL-Firewall und der IP-Adresse ist dennoch eine strenge Authentifizierung erforderlich. Weitere Informationen finden Sie in den Empfehlungen weiter oben in diesem Artikel.

Weitere Informationen zu Azure SQL-Firewall- und IP-Einschränkungen finden Sie unter:

- [Autorisieren des Datenbankzugriffs für Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics](../../azure-sql/database/logins-create-manage.md)
- [Azure SQL-Datenbank- und Azure Synapse-IP-Firewallregeln](../../azure-sql/database/firewall-configure.md)

## <a name="encrypt-data-at-rest"></a>Verschlüsselung ruhender Daten

[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) ist standardmäßig aktiviert. TDE verschlüsselt auf transparente Weise Daten und Protokolldateien für SQL Server, Azure SQL-Datenbank und Azure Synapse Analytics. TDE schützt vor der Gefährdung eines direkten Zugriffs auf die Dateien oder ihrer Sicherungen. Dadurch können Sie ruhende Daten verschlüsseln, ohne vorhandene Anwendungen zu ändern. TDE sollte immer aktiviert sein. Beachten Sie jedoch, dass dies einen Angreifer, der den normalen Zugriffspfad verwendet, nicht aufhalten wird. TDE ermöglicht die Einhaltung von Gesetzen, Bestimmungen und Richtlinien, die in vielen Branchen etabliert sind.

Azure SQL verwaltet die schlüsselbezogenen Probleme für TDE. Wie bei TDE auch muss bei lokaler Verwendung und beim Verschieben von Datenbanken besonders sorgfältig vorgegangen werden. In komplexeren Szenarios können die Schlüssel explizit über die erweiterte Schlüsselverwaltung in Azure Key Vault verwaltet werden. Weitere Informationen finden Sie unter [Aktivieren von TDE in SQL Server mithilfe von EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Dadurch wird auch Bring Your Own Key (BYOK) über die BYOK-Funktion von Azure Key Vault ermöglicht.

Azure SQL ermöglicht auch die Verschlüsselung von Spalten über [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Hierdurch wird nur autorisierten Anwendungen der Zugriff auf vertrauliche Spalten gewährt. Das Verwenden dieser Verschlüsselungsart begrenzt SQL-Abfragen für verschlüsselte Spalten auf gleichheitsbasierte Werte.

Die Verschlüsselung auf Anwendungsebene muss auch für selektive Daten verwendet werden. Bedenken in Bezug auf die Datenhoheit können durch das Verschlüsseln von Daten mit einem Schlüssel verringert werden, der im richtigen Land/der richtigen Region aufbewahrt wird. Dadurch wird verhindert, dass durch eine versehentliche Datenübertragung Probleme verursacht werden, da die Daten ohne den Schlüssel nicht entschlüsselt werden können. Voraussetzung dafür ist jedoch, dass ein starker Algorithmus (z.B. AES-256) verwendet wird.

Sie können zusätzliche Vorsichtsmaßnahmen zum Schützen der Datenbank treffen, z.B. das Entwerfen eines sicheren Systems, das Verschlüsseln vertraulicher Datenbestände und das Erstellen einer Firewall für die Datenbankserver.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden einige bewährte Methoden im Zusammenhang mit der Sicherheit für SQL-Datenbank und Azure Synapse Analytics vorgestellt, die zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen beitragen. Weitere Informationen zum Schutz Ihrer PaaS-Bereitstellungen finden Sie unter:

- [Schützen von PaaS-Bereitstellungen](paas-deployments.md)
- [Securing PaaS web and mobile applications using Azure App Services](paas-applications-using-app-services.md) (Schützen webbasierter und mobiler PaaS-Anwendungen mit Azure App Services)