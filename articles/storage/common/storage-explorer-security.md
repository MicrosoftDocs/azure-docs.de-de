---
title: Sicherheitsleitfaden für Azure Storage-Explorer | Microsoft-Dokumentation
description: Sicherheitsrichtlinien für Azure Storage-Explorer
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783758"
---
# <a name="azure-storage-explorer-security-guide"></a>Sicherheitsleitfaden für Azure Storage-Explorer

Microsoft Azure Storage-Explorer ermöglicht Ihnen unter Windows, macOS und Linux das problemlose und sichere Arbeiten mit Azure Storage-Daten. Wenn Sie die hier aufgeführten Richtlinien befolgen, können Sie sicherstellen, dass Ihre Daten geschützt bleiben.

## <a name="general"></a>Allgemein

- **Verwenden Sie immer die aktuelle Version von Storage-Explorer.** Releases von Storage-Explorer können Sicherheitsupdates enthalten. Wenn Sie die App stets auf dem neuesten Stand halten, können Sie die allgemeine Sicherheit sicherstellen.
- **Stellen Sie nur Verbindungen mit vertrauenswürdigen Ressourcen her.** Daten, die Sie von nicht vertrauenswürdigen Quellen herunterladen, können schädlich sein, und das Hochladen von Daten in eine nicht vertrauenswürdige Quelle kann zu Datenverlust oder Datendiebstahl führen.
- **Verwenden Sie nach Möglichkeit HTTPS.** Storage-Explorer verwendet standardmäßig HTTPS. In einigen Szenarien können Sie HTTP verwenden, aber HTTP sollte nur als letzte Option verwendet werden.
- **Stellen Sie sicher, dass nur die jeweils erforderlichen Berechtigungen den Personen zugewiesen werden, die sie tatsächlich benötigen.** Vermeiden Sie es, allzu großzügig zu sein, wenn Sie jemandem Zugriff auf Ihre Ressourcen gewähren.
- **Gehen Sie beim Ausführen kritischer Vorgänge mit Bedacht vor.** Bestimmte Vorgänge wie das Löschen und Überschreiben von Daten können nicht rückgängig gemacht werden und zu Datenverlusten führen. Stellen Sie vor dem Ausführen dieser Vorgänge sicher, dass Sie mit den richtigen Ressourcen arbeiten.

## <a name="choosing-the-right-authentication-method"></a>Wählen der richtigen Authentifizierungsmethode

Storage-Explorer bietet verschiedene Möglichkeiten für den Zugriff auf Ihre Azure Storage-Ressourcen. Nachfolgend finden Sie unsere Empfehlungen, für welche Methode Sie sich auch entscheiden.

### <a name="azure-ad-authentication"></a>Azure AD-Authentifizierung

Die einfachste und sicherste Möglichkeit, auf Ihre Azure Storage-Ressourcen zuzugreifen, besteht darin, sich mit Ihrem Azure-Konto anzumelden. Bei der Anmeldung wird die Azure AD-Authentifizierung verwendet, die Folgendes ermöglicht:

- Sie können bestimmten Benutzern und Gruppen Zugriff gewähren.
- Sie können den Zugriff für bestimmte Benutzer und Gruppen jederzeit widerrufen.
- Sie können Zugriffsbedingungen erzwingen, z. B. die Anforderung einer mehrstufigen Authentifizierung.

Es wird empfohlen, nach Möglichkeit die Azure AD-Authentifizierung zu verwenden.

In diesem Abschnitt werden die zwei Azure AD-basierten Technologien beschrieben, die zum Schützen Ihrer Speicherressourcen verwendet werden können.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)

Die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) ermöglicht Ihnen eine differenzierte Zugriffssteuerung für Ihre Azure-Ressourcen. Azure-Rollen und -Berechtigungen können im Azure-Portal verwaltet werden.

Storage-Explorer unterstützt den Azure RBAC-Zugriff auf Speicherkonten, Blobs und Warteschlangen. Wenn Sie Zugriff auf Dateifreigaben oder Tabellen benötigen, müssen Sie Azure-Rollen zuweisen, die die Berechtigung zum Auflisten von Speicherkontoschlüsseln erteilen.

#### <a name="access-control-lists-acls"></a>Zugriffssteuerungslisten (ACLs)

Mit [Zugriffssteuerungslisten (Access Control Lists, ACLs)](../blobs/data-lake-storage-access-control.md) können Sie den Zugriff auf Datei- und Ordnerebene in ADLS Gen2-Blobcontainern steuern. Sie können Ihre ACLs mit Storage-Explorer verwalten.

### <a name="shared-access-signatures-sas"></a>Shared Access Signatures (SAS)

Wenn Sie die Azure AD-Authentifizierung nicht verwenden können, empfehlen wir die Verwendung von Shared Access Signatures. Shared Access Signatures bieten Ihnen die folgenden Möglichkeiten:

- Sie können anonymen eingeschränkten Zugriff auf sichere Ressourcen gewähren.
- Sie können eine SAS sofort widerrufen, wenn diese über eine SAS-Richtlinie generiert wurde.

Folgendes ist bei Shared Access Signatures jedoch nicht möglich:

- Einschränken, wer eine SAS verwenden kann. Eine gültige SAS kann von jedem Benutzer verwendet werden, der darüber verfügt.
- Widerrufen einer SAS, wenn diese nicht über eine SAS-Richtlinie generiert wurde.

Bei Verwendung von SAS in Storage-Explorer empfehlen wir, die folgenden Richtlinien zu befolgen:

- **Beschränken Sie die Verteilung von SAS-Token und -URIs.** Verteilen Sie SAS-Token und -URIs nur an vertrauenswürdige Personen. Durch Beschränken der SAS-Verteilung können Sie die Wahrscheinlichkeit verringern, dass eine SAS missbraucht wird.
- **Verwenden Sie nur SAS-Token und -URIs von Entitäten, denen Sie vertrauen.**
- **Verwenden Sie nach Möglichkeit SAS-Richtlinien beim Erstellen von SAS-Token und -URIs.** Eine auf einer SAS-Richtlinie basierende SAS ist sicherer als eine reine SAS, weil die SAS durch Löschen der SAS-Richtlinie widerrufen werden kann.
- **Generieren Sie Token mit minimalem Ressourcenzugriff und minimalen Berechtigungen.** Durch minimale Berechtigungen können Sie den potenziellen Schaden begrenzen, der durch einen SAS-Missbrauch entstehen könnte.
- **Generieren Sie Token, die nur so lange wie nötig gültig sind.** Eine kurze Lebensdauer ist besonders wichtig bei reinen SAS, weil es keine Möglichkeit gibt, sie nach der Generierung zu widerrufen.

> [!IMPORTANT]
> Wenn Sie SAS-Token und -URIs zu Problembehandlungszwecken freigeben, z. B. in Serviceanfragen oder Fehlerberichten, machen Sie immer zumindest den Signaturteil der SAS unkenntlich.

### <a name="storage-account-keys"></a>Speicherkontoschlüssel

Speicherkontoschlüssel gewähren uneingeschränkten Zugriff auf die Dienste und Ressourcen innerhalb eines Speicherkontos. Aus diesem Grund empfehlen wir, die Verwendung von Schlüsseln für den Zugriff auf Ressourcen in Storage-Explorer einzuschränken. Verwenden Sie stattdessen Azure RBAC-Features oder SAS, um Zugriff zu gewähren.

Einige Azure-Rollen erteilen die Berechtigung zum Abrufen von Speicherkontoschlüsseln. Personen mit diesen Rollen können durch Azure RBAC erteilte oder verweigerte Berechtigungen effektiv umgehen. Wir empfehlen, diese Berechtigung nur zu erteilen, wenn dies unbedingt erforderlich ist.

Storage-Explorer versucht, Speicherkontoschlüssel zu verwenden (sofern verfügbar), um Anforderungen zu authentifizieren. Sie können diese Funktion in den Einstellungen deaktivieren ( **Dienste > Speicherkonten > Verwendung von Schlüsseln deaktivieren** ). Einige Funktionen (wie das Arbeiten mit klassischen Speicherkonten) unterstützen Azure RBAC nicht. Diese Funktionen erfordern weiterhin Schlüssel und sind von dieser Einstellung nicht betroffen.

Wenn Sie Schlüssel für den Zugriff auf Ihre Speicherressourcen verwenden müssen, empfehlen wir, die folgenden Richtlinien zu befolgen:

- **Geben Sie Ihre Kontoschlüssel nicht für andere Personen frei.**
- **Behandeln Sie Ihre Speicherkontoschlüssel wie Kennwörter.** Wenn Sie Ihre Schlüssel zugänglich machen müssen, verwenden Sie sichere Speicherlösungen wie z. B. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Wenn Sie glauben, dass ein Speicherkontoschlüssel versehentlich freigegeben oder verteilt wurde, können Sie im Azure-Portal neue Schlüssel für Ihr Speicherkonto generieren.

### <a name="public-access-to-blob-containers"></a>Öffentlicher Zugriff auf Blobcontainer

Storage-Explorer ermöglicht Ihnen das Ändern der Zugriffsebene Ihrer Azure Blob Storage-Container. Nicht private Blobcontainer ermöglichen allen Benutzern anonymen Lesezugriff auf die in diesen Containern gespeicherten Daten.

Wenn Sie den öffentlichen Zugriff für einen Blobcontainer aktivieren, empfehlen wir, die folgenden Richtlinien zu befolgen:

- **Aktivieren Sie den öffentlichen Zugriff nicht für Blobcontainer, die möglicherweise vertrauliche Daten enthalten.** Stellen Sie unbedingt sicher, dass sich keine privaten Daten in Ihrem Blobcontainer befinden.
- **Laden Sie keine potenziell vertraulichen Daten in einen Blobcontainer mit Blob- oder Containerzugriff hoch.** 

## <a name="next-steps"></a>Nächste Schritte

- [Sicherheitsempfehlungen](../blobs/security-recommendations.md)