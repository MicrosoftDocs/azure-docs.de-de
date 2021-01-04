---
title: Communitytools – Verschieben klassischer Ressourcen zu Azure Resource Manager
description: In diesem Artikel werden die Tools katalogisiert, die von der Community zur Unterstützung der Migration von IaaS-Ressourcen vom klassischen zum Azure Resource Manager-Bereitstellungsmodell bereitgestellt wurden.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 75ca87836a429a81f28b133e329291143c03ea68
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94904647"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von Communitytools

> [!IMPORTANT]
> Derzeit nutzen etwa 90 % der IaaS-VMs [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Seit dem 28. Februar 2020 gelten klassische VMs als veraltet. Sie werden am 1. März 2023 vollständig eingestellt. [Erfahren Sie mehr]( https://aka.ms/classicvmretirement) zu dieser Einstellung und den [Auswirkungen auf Sie](classic-vm-deprecation.md#how-does-this-affect-me).

In diesem Artikel werden die Tools katalogisiert, die von der Community zur Unterstützung der Migration von IaaS-Ressourcen vom klassischen zum Azure Resource Manager-Bereitstellungsmodell bereitgestellt wurden.

> [!NOTE]
> Diese Tools werden vom Microsoft-Support nicht offiziell unterstützt. Daher sind sie auf GitHub als Open-Source-Versionen verfügbar, und wir sind jederzeit offen für Vorschläge in Bezug auf Fixes oder weitere Szenarien. Wenn Sie ein Problem melden möchten, verwenden Sie das Feature für GitHub-Probleme.
> 
> Die Migration mit diesen Tools führt bei Ihrem klassischen virtuellen Computer zu Ausfallzeiten. Informationen zur plattformgestützten Migration finden Sie hier: 
> 
>   * [Plattformgestützte Migration von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zum Azure Resource Manager-Stapel](migration-classic-resource-manager-overview.md)
>   * [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Hierbei handelt es sich um eine Sammlung von Hilfsprogrammen, die im Rahmen von Enterprise-Migrationen von der Azure-Dienstverwaltung zu Azure Resource Manager erstellt wird. Dieses Tool ermöglicht Ihnen das Replizieren Ihrer Infrastruktur in ein anderes Abonnement, mit dem Sie die Migration testen und etwaige Probleme beheben können, bevor Sie die Migration für Ihr Produktionsabonnement ausführen.

[Link zur Tooldokumentation](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz ist eine zusätzliche Option zum Migrieren eines vollständigen Satzes klassischen IaaS-Ressourcen zu Azure Resource Manager-IaaS-Ressourcen. Die Migration kann im gleichen Abonnement oder zwischen verschiedenen Abonnements und Abonnementtypen (Ausnahme: CSP-Abonnements) durchgeführt werden.

[Link zur Tooldokumentation](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von PowerShell](migration-classic-resource-manager-ps.md)
* [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe der Befehlszeilenschnittstelle](migration-classic-resource-manager-cli.md)
* [Überprüfen der häufigsten Fehler bei der Migration](migration-classic-resource-manager-errors.md)
* [Antworten auf die am häufigsten gestellten Fragen zum Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-faq.md)
