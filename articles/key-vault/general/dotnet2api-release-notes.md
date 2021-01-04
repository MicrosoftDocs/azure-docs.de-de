---
title: Key Vault .NET 2.x-API – Versionshinweise | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Apps, die für ältere Azure Key Vault-Versionen geschrieben werden, so aktualisieren, dass sie mit der 2.0-Version der Azure Key Vault-Bibliothek für C# und .NET verwendet werden können.
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013954"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 – Versionshinweise und Migrationshandbuch
Die folgenden Informationen unterstützen Sie beim Migrieren zur Version 2.0 der Azure Key Vault-Bibliothek für C# und .NET.  Für ältere Versionen geschriebene Apps müssen aktualisiert werden, um die neueste Version zu unterstützen.  Diese Änderungen sind zur uneingeschränkten Unterstützung neuer und verbesserter Features (beispielsweise **Key Vault-Zertifikate**) erforderlich.

## <a name="key-vault-certificates"></a>Key Vault-Zertifikate

Key Vault-Zertifikate verwalten X.509-Zertifikate und unterstützen folgendes Verhalten:  

* Erstellen von Zertifikaten über einen Key Vault-Erstellungsprozess oder Importieren vorhandener Zertifikate. Dies schließt sowohl selbstsignierte als auch von einer Zertifizierungsstelle (Certificate Authority, CA) generierte Zertifikate ein.
* Sicheres Speichern und Verwalten von X.509-Zertifikatspeicher ohne Interaktion mit privaten Schlüsseln.  
* Definieren von Richtlinien für die Zertifikatlebenszyklusverwaltung durch Key Vault.  
* Angeben von Kontaktinformationen für Lebenszyklusereignisse wie Ablaufwarnungen und Verlängerungsbenachrichtigungen.  
* Automatisches Verlängern von Zertifikaten mit ausgewählten Ausstellern (Key Vault-Partneranbieter oder -Partnerzertifizierungsstellen für X509-Zertifikate).* Unterstützen von Zertifikaten alternativer Anbieter (keine Partner) und Zertifizierungsstellen (ohne Unterstützung der automatischen Verlängerung).  

## <a name="net-support"></a>Unterstützung von .NET

* **.NET 4.0** wird von Version 2.0 der .NET-Bibliothek für Azure Key Vault nicht unterstützt.
* **.NET Framework 4.5.2** wird von Version 2.0 der .NET-Bibliothek für Azure Key Vault unterstützt.
* **.NET Standard 1.4** wird von Version 2.0 der .NET-Bibliothek für Azure Key Vault unterstützt.

## <a name="namespaces"></a>Namespaces

* Der Namespace für **Modelle** wurde von **Microsoft.Azure.KeyVault** in **Microsoft.Azure.KeyVault.Models** geändert.
* Der Namespace **Microsoft.Azure.KeyVault.Internal** wurde gelöscht.
* Folgende Namespaces für Azure SDK-Abhängigkeiten haben sich geändert: 

    - **Hyak.Common** is jetzt **Microsoft.Rest**.
    - **Hyak.Common.Internals** is jetzt **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Typänderungen

* *Secret* wurde in *SecretBundle* geändert.
* *Dictionary* wurde in *IDictionary* geändert.
* *List\<T>, string []* wurde in *IList\<T>* geändert.
* *NextList* wurde in *NextPageLink* geändert.

## <a name="return-types"></a>Rückgabetypen

* **KeyList** und **SecretList** geben jetzt *IPage\<T>* anstelle von *ListKeysResponseMessage* zurück.
* Das generierte **BackupKeyAsync**-Element gibt jetzt *BackupKeyResult* mit *Value* (Backupblob) zurück. Zuvor war die Methode umschlossen und hat nur den Wert zurückgegeben.

## <a name="exceptions"></a>Ausnahmen

* *KeyVaultClientException* wird in *KeyVaultErrorException* geändert.
* Der Dienstfehler hat sich von *exception.Error* in *exception.Body.Error.Message* geändert.
* Aus der Fehlermeldung für **[JsonExtensionData]** wurden zusätzliche Informationen entfernt.

## <a name="constructors"></a>Konstruktoren

* Der Konstruktor akzeptiert statt eines *HttpClient*-Elements als Konstruktorargument nur *HttpClientHandler* oder *DelegatingHandler[]* .

## <a name="downloaded-packages"></a>Heruntergeladene Pakete

Wenn ein Client eine Key Vault-Abhängigkeit verarbeitet, werden folgende Pakete heruntergeladen:

### <a name="previous-package-list"></a>Vorherige Paketliste

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Aktuelle Paketliste

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Klassenänderungen

* Die Klasse **UnixEpoch** wurde entfernt.
* Die Klasse **Base64UrlConverter** wurde in **Base64UrlJsonConverter** umbenannt.

## <a name="other-changes"></a>Weitere Änderungen

* Dieser API-Version wurde Unterstützung für die Konfiguration der KV-Vorgangswiederholungsrichtlinie für vorübergehende Fehler hinzugefügt.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Bei den Vorgängen, die ein *vault*-Element zurückgegeben haben, war der Rückgabetyp eine Klasse mit einer **Vault**-Eigenschaft. Der Rückgabetyp lautet jetzt *Vault*.
* *PermissionsToKeys* und *PermissionsToSecrets* heißen jetzt *Permissions.Keys* und *Permissions.Secrets*.
* Bestimmte Rückgabetypänderungen gelten auch für die Steuerungsebene.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Für Kryptografievorgänge wurde das Paket in **Microsoft.Azure.KeyVault.Extensions** und **Microsoft.Azure.KeyVault.Cryptography** unterteilt.

