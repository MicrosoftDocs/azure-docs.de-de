---
title: Neuerungen bei Azure Key Vault
description: Aktuelle Updates für Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: dbbde397ab235068ea90280da721e3e3dc38866a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792360"
---
# <a name="whats-new-for-azure-key-vault"></a>Neuerungen bei Azure Key Vault

Dies sind die Neuerungen bei Azure Key Vault. Neue Funktionen und Verbesserungen werden außerdem im [Key Vault-Kanal von Azure Updates](https://azure.microsoft.com/updates/?category=security&query=Key%20vault) angekündigt.

## <a name="october-2020"></a>Oktober 2020

> [!WARNING]
> Diese Updates wirken sich potenziell auf Azure Key Vault-Implementierungen aus.

Zur Unterstützung der [standardmäßigen Aktivierung des vorläufigen Löschens](#soft-delete-on-by-default) wurden zwei Änderungen an den Azure Key Vault PowerShell-Cmdlets vorgenommen:

- Die Parameter „DisableSoftDelete“ und „EnableSoftDelete“ von [Update-AzKeyVault](/powershell/module/az.keyvault/update-azkeyvault) sind veraltet.
- Die Ausgabe des Cmdlets [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) verfügt nicht mehr über das Attribut `SecretValueText`.

## <a name="july-2020"></a>Juli 2020

> [!WARNING]
> Diese beiden Updates wirken sich potenziell auf Azure Key Vault-Implementierungen aus.

### <a name="soft-delete-on-by-default"></a>Standardmäßig aktiviertes vorläufiges Löschen

Ende 2020 **wird vorläufiges Löschen standardmäßig für alle Schlüsseltresore aktiviert** . Dies gilt sowohl für neue als auch für bereits vorhandene Schlüsseltresore. Ausführliche Informationen zu dieser Änderung, bei der es sich unter Umständen um einen Breaking Change handelt, sowie Schritte, mit denen Sie nach betroffenen Schlüsseltresoren suchen und sie vorab aktualisieren können, finden Sie im Artikel [Vorläufiges Löschen wird für alle Schlüsseltresore aktiviert](soft-delete-change.md).

### <a name="azure-tls-certificate-changes"></a>TLS-Zertifikatänderungen für Azure

Microsoft aktualisiert Azure-Dienste für die Verwendung von TLS-Zertifikaten aus einer anderen Gruppe von Stammzertifizierungsstellen (Certificate Authorities, CAs). Diese Änderung wird vorgenommen, da die aktuellen Zertifizierungsstellenzertifikate eine der grundlegenden Anforderungen des Zertifizierungsstellen-/Browserforums nicht erfüllen.  Ausführliche Informationen finden Sie unter [TLS-Zertifikatänderungen für Azure](../../security/fundamentals/tls-certificate-changes.md).

## <a name="june-2020"></a>Juni 2020

Azure Monitor für Key Vault ist jetzt als Vorschauversion erhältlich.  Azure Monitor ermöglicht eine umfassende Überwachung Ihrer Schlüsseltresore. Zu diesem Zweck wird eine einheitliche Darstellung der Anforderungen, Leistung, Fehler und Wartezeit im Zusammenhang mit Key Vault bereitgestellt. Weitere Informationen finden Sie unter [Azure Monitor für Key Vault (Vorschau).](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Mai 2020

Key Vault-BYOK („Bring-Your-Own-Key“) ist jetzt allgemein verfügbar. Mehr dazu finden Sie in der [Azure Key Vault-BYOK-Spezifikation](../keys/byok-specification.md), und erfahren Sie, wie Sie [HSM-geschützte Schlüssel in Key Vault (BYOK) importieren](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>März 2020

Private Endpunkte sind jetzt als Vorschauversion erhältlich. Mit dem Azure Private Link-Dienst können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure Key Vault sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen.  Weitere Informationen zum [Integrieren von Key Vault in Azure Private Link](private-link-service.md).

## <a name="2019"></a>2019

- Veröffentlichung der nächsten Generation von Azure Key Vault SDKs. Beispiele zur Verwendung finden Sie in den Schnellstartanleitungen zu Azure Key Vault-Geheimnissen für [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) und [Node.js](../secrets/quick-create-node.md)
- Neue Azure-Richtlinien zum Verwalten von Key Vault-Zertifikaten. Mehr dazu erfahren Sie unter [Integrierte Azure Policy-Definitionen für Key Vault](../policy-reference.md).
- Die Azure Key Vault-Erweiterung für virtuelle Computer ist jetzt allgemein verfügbar.  Informationen finden Sie unter [Key Vault-VM-Erweiterung für Linux](../../virtual-machines/extensions/key-vault-linux.md) und [Key Vault-VM-Erweiterung für Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Die ereignisgesteuerte Verwaltung von Geheimnissen für Azure Key Vault ist jetzt im Azure Event Grid verfügbar. Weitere Informationen finden Sie im [Event Grid-Schema für Ereignisse in Azure Key Vault](../../event-grid/event-schema-key-vault.md], und erfahren Sie mehr über das [Empfangen von und Antworten auf Schlüsseltresor-Benachrichtigungen mit Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

In diesem Jahr veröffentlichte neue Features und Integrationen:

- Integration in Azure Functions. Ein Beispielszenario, in dem [Azure Functions](../../azure-functions/index.yml) für Schlüsseltresorvorgänge genutzt wird, finden Sie unter [Automate the rotation of a secret](../secrets/tutorial-rotation.md) (Automatisieren der Rotation von Geheimnissen).
- [Integration in Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Hiermit unterstützt Azure Databricks jetzt zwei Typen von Geheimnisbereichen: Von Azure Key Vault unterstützte und von Databricks unterstützte. Weitere Informationen finden Sie unter [Erstellen eines von Azure Key Vault unterstützten Geheimnisbereichs](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [VNET-Dienstendpunkte für Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

In diesem Jahr veröffentlichte neue Features:

- Schlüssel für verwaltete Speicherkonten. Das hinzugefügte Feature „Speicherkontoschlüssel“ erleichtert die Integration in Azure Storage. Weitere Informationen finden Sie im Übersichtsthema [Azure Key Vault-Speicherkontoschlüssel](../secrets/overview-storage-keys.md).
- Vorläufiges Löschen. Das Feature „Vorläufiges Löschen“ verbessert den Datenschutz Ihrer Schlüsseltresore und der Schlüsseltresorobjekte. Weitere Informationen finden Sie im Übersichtsthema [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](./soft-delete-overview.md).

## <a name="2015"></a>2015

In diesem Jahr veröffentlichte neue Features:
- Zertifikatverwaltung. Als Funktion der GA-Version 2015-06-01 am 26. September 2016 hinzugefügt.

Die allgemein verfügbare Version 2015-06-01 wurde am 24. Juni 2015 angekündigt. Die folgenden Änderungen wurden in dieser Version vorgenommen:
- Löschen eines Schlüssels: Feld „use“ entfernt.
- Abrufen von Informationen über einen Schlüssel: Feld „use“ entfernt.
- Importieren eines Schlüssels in einen Tresor: Feld „use“ entfernt.
- Wiederherstellen eines Schlüssels: Feld „use“ entfernt.
- Für RSA-Algorithmen „RSA_OAEP“ in „RSA-OAEP“ geändert. Siehe [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-certificates.md).

Die zweite Vorschauversion (Version 2015-02-01-preview) wurde am 20. April 2015 angekündigt. Weitere Informationen finden Sie im Blogbeitrag zum [REST-API-Update](/archive/blogs/kv/rest-api-update). Die folgenden Aufgaben wurden aktualisiert:

- Auflisten der Schlüssel in einem Tresor: Unterstützung für Paginierung wurde zum Vorgang hinzugefügt.
- Auflisten der Versionen eines Schlüssels: Vorgang zum Auflisten der Versionen eines Schlüssels wurde hinzugefügt.
- Auflisten der Geheimnisse in einem Tresor: Unterstützung für Paginierung wurde hinzugefügt.
- Auflisten der Versionen eines Geheimnisses: Vorgang zum Auflisten der Versionen eines Geheimnisses wurde hinzugefügt.
- Alle Vorgänge: Attributen wurden Zeitstempel der Erstellung/Aktualisierung hinzugefügt.
- Erstellen eines Geheimnisses:Geheimnissen wurde ein Inhaltstyp hinzugefügt.
- Erstellen eines Schlüssels: Tags wurden als optionale Informationen hinzugefügt.
- Erstellen eines Geheimnisses: Tags wurden als optionale Informationen hinzugefügt.
- Aktualisieren eines Schlüssels: Tags wurden als optionale Informationen hinzugefügt.
- Aktualisieren eines Geheimnisses: Tags wurden als optionale Informationen hinzugefügt.
- Maximale Größe für Geheimnisse wurde von 10.000 in 25.000 Bytes geändert. Siehe [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-certificates.md).

## <a name="2014"></a>2014

Die erste Vorschauversion (Version 2014-12-08-preview) wurde am 8. Januar 2015 angekündigt.

## <a name="next-steps"></a>Nächste Schritte

Sollten Sie weitere Fragen haben, wenden Sie sich an den [Support](https://azure.microsoft.com/support/options/).