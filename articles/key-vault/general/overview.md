---
title: Azure Key Vault – Übersicht
description: Azure Key Vault ist ein sicherer, auf Hardwaresicherheitsmodulen basierender Geheimnisspeicher für die Verwaltung von Geheimnissen, Schlüsseln und Zertifikaten.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5025c4ad81433b6eb18f5beded860625791ffce5
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445097"
---
# <a name="about-azure-key-vault"></a>Informationen zu Azure Key Vault

Mit Azure Key Vault lassen sich folgende Probleme lösen:

- **Geheimnisverwaltung**: Azure Key Vault ermöglicht die sichere Speicherung und präzise Steuerung des Zugriffs auf Token, Kennwörter, Zertifikate, API-Schlüssel und andere Geheimnisse.
- **Schlüsselverwaltung**: Azure Key Vault kann auch als Schlüsselverwaltungslösung verwendet werden. Azure Key Vault vereinfacht das Erstellen und Verwalten der zur Verschlüsselung Ihrer Daten verwendeten Verschlüsselungsschlüssel. 
- **Zertifikatverwaltung**: Darüber hinaus können Sie mit dem Azure Key Vault-Dienst komfortabel öffentliche und private SSL-/TLS-Zertifikate (Secure Sockets Layer/Transport Layer Security) für die Verwendung mit Azure und Ihren internen verbundenen Ressourcen bereitstellen und verwalten.

Azure Key Vault verfügt über zwei Dienstebenen: Die Dienstebene „Standard“ verschlüsselt mit einem Softwareschlüssel und die Dienstebene „Premium“ enthält durch HSM geschützte Schlüssel. Einen Vergleich zwischen den Ebenen „Standard“ und „Premium“ finden Sie auf der Seite [Azure Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="why-use-azure-key-vault"></a>Gründe für die Verwendung von Azure Key Vault

### <a name="centralize-application-secrets"></a>Zentralisieren von Anwendungsgeheimnissen

Durch die zentralisierte Speicherung von Anwendungsgeheimnissen in Azure Key Vault können Sie deren Verteilung steuern. Mit Key Vault lässt sich das Risiko einer unbeabsichtigten Weitergabe von Geheimnissen erheblich senken. Dank Key Vault müssen Anwendungsentwickler Sicherheitsinformationen nicht mehr in der Anwendung speichern. Wenn Sicherheitsinformationen nicht mehr in Anwendungen gespeichert werden müssen, entfällt die Notwendigkeit, diese Informationen in den Code einzubinden. Ein Beispiel: Angenommen, eine Anwendung muss eine Verbindung mit einer Datenbank herstellen. Anstatt die Verbindungszeichenfolge im App-Code zu speichern, können Sie diese sicher in Key Vault speichern.

Ihre Anwendungen können mithilfe von URIs sicher auf benötigte Informationen zugreifen. Diese URIs bieten den Anwendungen die Möglichkeit, bestimmte Versionen eines geheimen Schlüssels abzurufen. Sie müssen keinerlei benutzerdefinierten Code schreiben, um die geheimen Informationen zu schützen, die in Key Vault gespeichert sind.

### <a name="securely-store-secrets-and-keys"></a>Sicheres Speichern von Geheimnissen und Schlüsseln

Ein Aufrufer (Benutzer oder Anwendung) kann erst nach ordnungsgemäßer Authentifizierung und Autorisierung auf einen Schlüsseltresor zugreifen. Bei der Authentifizierung wird die Identität des Aufrufers ermittelt. Bei der Autorisierung wird dagegen bestimmt, welche Vorgänge der Aufrufer ausführen darf.

Die Authentifizierung erfolgt über Azure Active Directory. Für die Autorisierung kann die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) oder eine Key Vault-Zugriffsrichtlinie verwendet werden. Azure RBAC kommt bei der Verwaltung der Tresore zum Einsatz. Eine Key Vault-Zugriffsrichtlinie wird für den Zugriff auf gespeicherte Daten in einem Tresor verwendet.

Azure Key Vaults kann entweder durch Software geschützt oder mit der Azure Key Vault-Ebene „Premium“ durch Hardwaresicherheitsmodule (HSMs) per Hardware geschützt sein. Durch Software geschützte Schlüssel, Geheimnisse und Zertifikate werden von Azure mithilfe von branchenüblichen Algorithmen und Schlüssellängen geschützt.  In Szenarien mit erhöhten Sicherheitsanforderungen können Sie Schlüssel in Hardwaresicherheitsmodule (HSMs) importieren oder darin generieren. Diese Schlüssel bleiben immer innerhalb der HSM-Grenzen. Azure Key Vault verwendet nCipher-HSMs, die nach den Federal Information Processing Standards (FIPS) 140-2 Level 2 überprüft wurden. Sie können nCipher-Tools verwenden, um einen Schlüssel aus Ihrem HSM zu Azure Key Vault zu verschieben.

Darüber hinaus ist Azure Key Vault so konzipiert, dass Ihre Daten von Microsoft weder angezeigt noch extrahiert werden können.

### <a name="monitor-access-and-use"></a>Überwachen von Zugriff und Verwendung

Nachdem Sie eine Reihe von Key Vault-Instanzen erstellt haben, können Sie überwachen, wie und wann auf Ihre Schlüssel und Geheimnisse zugegriffen wird. Durch das Aktivieren der Protokollierung für Ihre Tresore können Sie Aktivitäten überwachen. Azure Key Vault kann für Folgendes konfiguriert werden:

- Archivieren in einem Speicherkonto
- Streamen an einen Event Hub
- Senden der Protokolle an Azure Monitor-Protokolle

Sie haben die Kontrolle über Ihre Protokolle: Sie können den Zugriff auf Protokolle einschränken, um sie zu schützen, und Sie können nicht mehr benötigte Protokolle löschen.

### <a name="simplified-administration-of-application-secrets"></a>Einfachere Verwaltung von Anwendungsgeheimnissen

Beim Speichern wertvoller Daten sind mehrere Punkte zu berücksichtigen: Sicherheitsinformationen müssen geschützt, hochverfügbar und mit einem für sie festgelegten Lebenszyklus versehen sein. Azure Key Vault vereinfacht das Erfüllen dieser Anforderungen durch:

- Beseitigung des Bedarfs für interne Kenntnisse von Hardware-Sicherheitsmodellen
- Kurzfristiges Hochskalieren zur Verarbeitung von Auslastungsspitzen Ihrer Organisation
- Replikation des Inhalts Ihrer Key Vault-Instanz innerhalb einer Region und in einer sekundären Region. Die Datenreplikation gewährleistet die Hochverfügbarkeit der Informationen, und ein Failover kann ganz ohne Eingriff des Administrators ausgelöst werden.
- Bereitstellung standardmäßiger Azure-Verwaltungsoptionen über das Portal, die Azure-Befehlszeilenschnittstelle und PowerShell
- Automatisierung bestimmter Aufgaben im Zusammenhang mit Zertifikaten, die Sie von öffentlichen Zertifizierungsstellen erwerben (z.B. Registrierung und Verlängerung)

Darüber hinaus können Azure Key Vault-Instanzen auch zur Isolierung von Anwendungsgeheimnissen verwendet werden. Anwendungen können nur auf den Tresor zugreifen, für den sie zugriffsberechtigt sind, und die ausführbaren Vorgänge können auf bestimmte Vorgänge beschränkt werden. Sie können eine Azure Key Vault-Instanz pro Anwendung erstellen und die in einer Key Vault-Instanz gespeicherten Geheimnisse auf eine bestimmte Anwendung und ein bestimmtes Entwicklerteam beschränken.

### <a name="integrate-with-other-azure-services"></a>Integrieren in andere Azure-Dienste

Als sicherer Speicher in Azure wurde Key Vault u. a. zur Vereinfachung folgender Szenarien verwendet:
-  [Azure-Datenträgerverschlüsselung](../../security/fundamentals/encryption-overview.md)
-  Die Funktionen [Immer verschlüsselt]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) und [Transparent Data Encryption]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) in SQL Server und Azure SQL-Datenbank
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

Key Vault selbst kann in Speicherkonten, Event Hubs und Protokollanalysen integriert werden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Schlüssel, Geheimnisse und Zertifikate](about-keys-secrets-certificates.md).
- [Schnellstart: Erstellen einer Azure Key Vault-Instanz mithilfe der Befehlszeilenschnittstelle](../secrets/quick-create-cli.md)
- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
