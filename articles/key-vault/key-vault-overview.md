---
title: Azure Key Vault – Übersicht | Microsoft-Dokumentation
description: Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 456f828f09a710009d30e1c0a669cde658318ae0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789370"
---
# <a name="what-is-azure-key-vault"></a>Was ist der Azure-Schlüsseltresor?

Mit Azure Key Vault lassen sich folgende Probleme lösen:

- **Geheimnisverwaltung**: Azure Key Vault ermöglicht die sichere Speicherung und präzise Steuerung des Zugriffs auf Token, Kennwörter, Zertifikate, API-Schlüssel und andere Geheimnisse.
- **Schlüsselverwaltung**: Azure Key Vault kann auch als Schlüsselverwaltungslösung verwendet werden. Azure Key Vault vereinfacht das Erstellen und Verwalten der zur Verschlüsselung Ihrer Daten verwendeten Verschlüsselungsschlüssel. 
- **Zertifikatverwaltung**: Darüber hinaus können Sie mit dem Azure Key Vault-Dienst komfortabel öffentliche und private SSL-/TLS-Zertifikate (Secure Sockets Layer/Transport Layer Security) für die Verwendung mit Azure und Ihren internen verbundenen Ressourcen bereitstellen und verwalten. 
- **Speichern von Geheimnissen mit Unterstützung durch Hardwaresicherheitsmodule**: Die Geheimnisse und Schlüssel können entweder per Software oder mit FIPS 140-2 Level 2 HSMs geschützt werden.

## <a name="why-use-azure-key-vault"></a>Gründe für die Verwendung von Azure Key Vault

### <a name="centralize-application-secrets"></a>Zentralisieren von Anwendungsgeheimnissen

Durch die zentralisierte Speicherung von Anwendungsgeheimnissen in Azure Key Vault können Sie deren Verteilung steuern. Mit Key Vault lässt sich das Risiko einer unbeabsichtigten Weitergabe von Geheimnissen erheblich senken. Dank Key Vault müssen Anwendungsentwickler Sicherheitsinformationen nicht mehr in der Anwendung speichern. Wenn Sicherheitsinformationen nicht mehr in Anwendungen gespeichert werden müssen, entfällt die Notwendigkeit, diese Informationen in den Code einzubinden. Ein Beispiel: Angenommen, eine Anwendung muss eine Verbindung mit einer Datenbank herstellen. Anstatt die Verbindungszeichenfolge im App-Code zu speichern, können Sie diese sicher in Key Vault speichern.

Ihre Anwendungen können mithilfe von URIs sicher auf benötigte Informationen zugreifen. Diese URIs bieten den Anwendungen die Möglichkeit, bestimmte Versionen eines geheimen Schlüssels abzurufen. Sie müssen keinerlei benutzerdefinierten Code schreiben, um die geheimen Informationen zu schützen, die in Key Vault gespeichert sind.

### <a name="securely-store-secrets-and-keys"></a>Sicheres Speichern von Geheimnissen und Schlüsseln

Geheimnisse und Schlüssel werden mit branchenüblichen Algorithmen, Schlüssellängen und Hardwaresicherheitsmodulen (HSMs) von Azure geschützt. Die verwendeten HSMs erfüllen die Anforderungen von FIPS 140-2, Level 2 (Federal Information Processing Standards).

Ein Aufrufer (Benutzer oder Anwendung) kann erst nach ordnungsgemäßer Authentifizierung und Autorisierung auf einen Schlüsseltresor zugreifen. Bei der Authentifizierung wird die Identität des Aufrufers ermittelt. Bei der Autorisierung wird dagegen bestimmt, welche Vorgänge der Aufrufer ausführen darf.

Die Authentifizierung erfolgt über Azure Active Directory. Für die Autorisierung kann die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) oder eine Key Vault-Zugriffsrichtlinie verwendet werden. RBAC kommt bei der Verwaltung der Tresore zum Einsatz. Eine Key Vault-Zugriffsrichtlinie wird für den Zugriff auf gespeicherte Daten in einem Tresor verwendet.

Azure Key Vault-Instanzen können durch Software oder Hardware (HSM) geschützt werden. In Szenarien mit erhöhten Sicherheitsanforderungen können Sie Schlüssel in Hardwaresicherheitsmodule (HSMs) importieren oder darin generieren. Diese Schlüssel bleiben immer innerhalb der HSM-Grenzen. Microsoft verwendet Hardwaresicherheitsmodule von nCipher. Sie können nCipher-Tools verwenden, um einen Schlüssel aus Ihrem HSM zu Azure Key Vault zu verschieben.

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
- Kurzfristige zentrale Hochskalierung zur Abdeckung von Auslastungsspitzen Ihrer Organisation
- Replikation des Inhalts Ihrer Key Vault-Instanz innerhalb einer Region und in einer sekundären Region. Die Datenreplikation gewährleistet die Hochverfügbarkeit der Informationen, und ein Failover kann ganz ohne Eingriff des Administrators ausgelöst werden.
- Bereitstellung standardmäßiger Azure-Verwaltungsoptionen über das Portal, die Azure-Befehlszeilenschnittstelle und PowerShell
- Automatisierung bestimmter Aufgaben im Zusammenhang mit Zertifikaten, die Sie von öffentlichen Zertifizierungsstellen erwerben (z.B. Registrierung und Verlängerung)

Darüber hinaus können Azure Key Vault-Instanzen auch zur Isolierung von Anwendungsgeheimnissen verwendet werden. Anwendungen können nur auf den Tresor zugreifen, für den sie zugriffsberechtigt sind, und die ausführbaren Vorgänge können auf bestimmte Vorgänge beschränkt werden. Sie können eine Azure Key Vault-Instanz pro Anwendung erstellen und die in einer Key Vault-Instanz gespeicherten Geheimnisse auf eine bestimmte Anwendung und ein bestimmtes Entwicklerteam beschränken.

### <a name="integrate-with-other-azure-services"></a>Integrieren in andere Azure-Dienste

Als sicherer Speicher in Azure wurde Key Vault u. a. zur Vereinfachung folgender Szenarien verwendet:
-  [Azure-Datenträgerverschlüsselung](../security/azure-security-disk-encryption.md)
-  Funktion [Immer verschlüsselt]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) in SQL-Server und Azure SQL-Datenbank
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site) 

Key Vault selbst kann in Speicherkonten, Event Hubs und Protokollanalysen integriert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen einer Azure Key Vault-Instanz mithilfe der Befehlszeilenschnittstelle](quick-create-cli.md)
- [Tutorial: Konfigurieren einer Azure-Webanwendung zum Lesen eines Geheimnisses aus Key Vault](tutorial-web-application-keyvault.md)
