---
title: Übersicht über verwaltetes Azure-HSM | Microsoft-Dokumentation
description: Verwaltetes Azure-HSM ist ein Clouddienst zum Schutz Ihrer kryptografischen Schlüssel für Cloudanwendungen.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 95feeebf009cae22cf3952df80596c12e75cbe4b
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357571"
---
# <a name="what-is-azure-key-vault-managed-hsm-preview"></a>Was ist verwaltetes HSM von Azure Key Vault (Vorschauversion)?

Verwaltetes HSM von Azure Key Vault ist ein vollständig verwalteter, hochverfügbarer und standardkonformer Einzelmandanten-Clouddienst, der es Ihnen ermöglicht, kryptografische Schlüssel für Ihre Cloudanwendungen über HSMs zu schützen, die mit **FIPS 140-2 Level 3** validiert sind.  

## <a name="why-use-managed-hsm"></a>Gründe für die Verwendung von verwaltetem HSM

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Vollständig verwaltetes, hochverfügbares Einzelmandanten-HSM als Dienst

- **Vollständig verwaltet:** Bereitstellung, Konfiguration, Patching und Wartung des HSM werden durch den Dienst erledigt. 
- **Hochverfügbar und zonenresilient** (wenn Verfügbarkeitszonen unterstützt werden): Jeder HSM-Cluster besteht aus mehreren HSM-Partitionen, die sich über mindestens zwei Verfügbarkeitszonen erstrecken. Im Falle eines Hardwarefehlers werden Mitgliedspartitionen für Ihren HSM-Cluster automatisch zu fehlerfreien Knoten migriert.
- **Ein Mandant:** Jede verwaltete HSM-Instanz ist für einen einzelnen Kunden dediziert und besteht aus einem Cluster mit mehreren HSM-Partitionen. Jeder HSM-Cluster verwendet eine separate, kundenspezifische Sicherheitsdomäne, um die HSM-Cluster der einzelnen Kunden kryptografisch zu isolieren.


### <a name="access-control-enhanced-data-protection--compliance"></a>Zugriffssteuerung, verbesserter Schutz von Daten und Konformität

- **Zentrale Schlüsselverwaltung:** Verwalten Sie kritische Schlüssel mit hohem Wert für Ihre gesamte Organisation an einem zentralen Ort. Dank präziser schlüsselspezifischer Berechtigungen können Sie den Zugriff auf die einzelnen Schlüssel gemäß dem Prinzip der möglichst geringen Zugriffsberechtigungen steuern.
- **Isolierte Zugriffssteuerung:** Mit dem Zugriffssteuerungsmodell der lokalen RBAC für verwaltete HSMs haben designierte HSM-Clusteradministratoren die volle Kontrolle über die HSMs und damit sogar Vorrang vor Verwaltungsgruppen-, Abonnement- und Ressourcengruppenadministratoren.
- **FIPS 140-2 Level 3-zertifizierte HSMs:** Schützen Sie Ihre Daten, und erfüllen Sie Complianceanforderungen mit FIPS 140-2 Level 3-zertifizierten HSMs (Federal Information Protection Standard). Verwaltete HSMs verwenden HSM-Adapter von Marvell LiquidSecurity.
- **Überwachen und Überprüfen:** Profitieren Sie von der vollständigen Azure Monitor-Integration. Erhalten Sie umfassende Protokolle aller Aktivitäten über Azure Monitor. Verwenden Sie Azure Log Analytics für Analysen und Warnungen.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integration in Azure- und Microsoft-Dienste (PaaS/SaaS) 

- Generieren Sie Schlüssel (oder importieren Sie sie unter Verwendung von [BYOK](hsm-protected-keys-byok.md)), und verwenden Sie sie zum Verschlüsseln ruhender Daten in Azure-Diensten wie [Azure Storage](../../storage/common/customer-managed-keys-overview.md), [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md) und [Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Verwendung der gleichen API und Verwaltungsschnittstellen wie Key Vault

- Bereits vorhandene Anwendungen, die einen Tresor (mehrere Mandanten) nutzen, können ganz einfach für die Verwendung verwalteter HSMs migriert werden.
- Nutzen Sie für alle Ihre Anwendungen die gleichen Muster für die Anwendungsentwicklung und -bereitstellung – unabhängig von der verwendeten Schlüsselverwaltungslösung (Tresore mit mehreren Mandanten oder verwaltete HSMs mit einem einzelnen Mandanten).

### <a name="import-keys-from-your-on-premise-hsms"></a>Importieren von Schlüsseln aus Ihren lokalen HSMs

- Generieren Sie durch HSM geschützte Schlüssel in Ihrem lokalen HSM, und importieren Sie sie sicher in verwaltete HSMs.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen finden Sie unter [Schnellstart: Bereitstellen und Aktivieren eines verwalteten HSM mithilfe der Azure-Befehlszeilenschnittstelle](quick-create-cli.md). Dort erfahren Sie, wie Sie ein verwaltetes HSM erstellen und aktivieren.
- Informieren Sie sich über [bewährte Methoden bei der Verwendung verwalteter HSMs von Azure Key Vault](best-practices.md).
