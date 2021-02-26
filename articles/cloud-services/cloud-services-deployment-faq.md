---
title: Probleme mit der Bereitstellung in Microsoft Azure Cloud Services – Häufig gestellte Fragen | Microsoft-Dokumentation
description: Dieser Artikel behandelt häufig gestellte Fragen zur Bereitstellung in Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896476"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Bereitstellungsprobleme bei Azure Cloud Services (klassisch): Häufig gestellte Fragen (FAQs)

> [!IMPORTANT]
> [Azure Cloud Services (erweiterter Support)](../cloud-services-extended-support/overview.md) ist ein neues auf Azure Resource Manager basierendes Bereitstellungsmodell für Azure Cloud Services. Im Zuge dieser Änderung wurden Azure Cloud Services-Instanzen, die unter dem Azure Service Manager-basierten Bereitstellungsmodell ausgeführt werden, in „Cloud Services (klassisch)“ umbenannt. Für alle neuen Bereitstellungen wird [Azure Cloud Services (erweiterter Support)](../cloud-services-extended-support/overview.md) verwendet.
Dieser Artikel enthält häufig gestellte Fragen zu Problemen mit der Bereitstellung in [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informationen zur Größe finden sie auch unter [Größen für Clouddienste](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Warum tritt bei der Bereitstellung eines Clouddiensts in einem Stagingslot manchmal ein Fehler bei der Ressourcenzuteilung auf, wenn bereits eine Bereitstellung im Produktionsslot vorhanden ist?
Verfügt ein Clouddienst über eine Bereitstellung in einem der Slots, wird der gesamte Clouddienst mit einem bestimmten Cluster verknüpft. Wenn also bereits eine Bereitstellung im Produktionsslot vorhanden ist, kann eine neue Stagingbereitstellung nur demselben Cluster wie der Produktionsslot zugeordnet werden.

Fehler bei der Zuordnung treten auf, wenn der Cluster, in dem sich der Clouddienst befindet, nicht über ausreichend physische Computeressourcen verfügt, um die Bereitstellungsanfrage zu erfüllen.

Hilfe bei der Abmilderung solcher Zuordnungsfehler finden Sie unter [Zuordnungsfehler bei Cloud Service-Instanzen: Lösungen](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Warum führt das Hochskalieren oder das horizontale Skalieren einer Clouddienstbereitstellung manchmal zu Zuordnungsfehlern?
Wenn ein Clouddienst bereitgestellt ist, wird er normalerweise an einen bestimmten Cluster angeheftet. Das bedeutet, dass beim horizontalen oder Hochskalieren eines vorhandenen Clouddiensts eine Zuweisung neuer Instanzen in demselben Cluster erfolgen muss. Wenn der Cluster seine Kapazität nahezu erreicht hat, oder die gewünschte Größe/der gewünschte Typ des virtuellen Computers nicht verfügbar ist, schlägt die Anforderung möglicherweise fehl.

Hilfe bei der Abmilderung solcher Zuordnungsfehler finden Sie unter [Zuordnungsfehler bei Cloud Service-Instanzen: Lösungen](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Warum führt die Bereitstellung eines Clouddiensts in einer Affinitätsgruppe manchmal zu einem Fehler bei der Zuordnung?
Eine neue Bereitstellung für einen leeren Clouddienst kann von dem Fabric in jedem Cluster in dieser Region zugeordnet werden, es sei denn, der Clouddienst ist mit einer Affinitätsgruppe verknüpft. Bereitstellungen in derselben Affinitätsgruppe werden auch im selben Cluster versucht. Wenn der Cluster seine Kapazität nahezu erreicht hat, schlägt die Anforderung möglicherweise fehl.

Hilfe bei der Abmilderung solcher Zuordnungsfehler finden Sie unter [Zuordnungsfehler bei Cloud Service-Instanzen: Lösungen](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Warum führt das Ändern der Größe eines virtuellen Computers oder das Hinzufügen eines neuen virtuellen Computers zu einem vorhandenen Clouddienst manchmal zu einem Zuordnungsfehler?
Die Cluster in einem Datencenter können über verschiedene Konfigurationen der Computertypen verfügen (z. B. A-Serie, Av2-Serie, D-Serie, Dv2-Serie, G-Serie, H-Serie usw.). Allerdings verfügen möglicherweise nicht alle Cluster über alle Arten von virtuellen Computern. Wenn Sie z.B. versuchen, einen virtuellen Computer der D-Serie einem Clouddienst zuzuordnen, der bereits in einem Cluster der A-Serie bereitgestellt ist, tritt ein Zuordnungsfehler auf. Dies geschieht auch dann, wenn Sie versuchen, die SKU-Größe des virtuellen Computers zu ändern (z.B. durch Wechsel von einer A-Serie zu einer D-Serie).

Hilfe bei der Abmilderung solcher Zuordnungsfehler finden Sie unter [Zuordnungsfehler bei Cloud Service-Instanzen: Lösungen](cloud-services-allocation-failures.md#solutions).

Informationen zu den verfügbaren Größen in Ihrer Region finden Sie unter [Microsoft Azure: Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Warum schlägt die Bereitstellung eines Clouddiensts manchmal aufgrund von Grenzwerten/Kontingenten/Einschränkungen in meinem Abonnement oder Dienst fehl?
Die Bereitstellung eines Clouddiensts schlägt manchmal fehl, wenn die erforderlichen Ressourcen für die Zuweisung das maximale oder standardmäßige Kontingent überschreiten, das für Ihren Dienst in Ihrer Region/Datencenterebene zulässig ist. Weitere Informationen finden Sie unter [Grenzwerte für Clouddienste](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Sie können auch die aktuelle Nutzung bzw. das aktuelle Kontingent für Ihr Abonnement im Portal nachverfolgen: Azure-Portal > Abonnements > \<appropriate subscription> > „Verbrauch + Kontingent“.

Informationen zur Ressourcenauslastung und zum Verbrauch können auch über die Azure-Abrechnungs-APIs abgerufen werden. Weitere Informationen finden Sie unter [Azure-Nutzungs-API: Übersicht](../cost-management-billing/manage/consumption-api-overview.md).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Wie kann ich die Größe eines bereitgestellten virtuellen Clouddienstcomputers ändern, ohne ihn erneut bereitzustellen?
Die Größe eines virtuellen Clouddienstcomputers kann nicht ohne erneute Bereitstellung geändert werden. Die Größe des virtuellen Computers ist in CSDEF festgelegt. Diese Datei kann nur durch eine erneute Bereitstellung aktualisiert werden.

Weitere Informationen finden Sie unter [Aktualisieren eines Clouddiensts](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Warum kann ich bei Verwendung eines Azure Resource Manager-Speicherkontos Cloud Services nicht über Dienstverwaltungs-APIs oder PowerShell bereitstellen? 

Da es sich bei Cloud Services um eine klassische Ressource handelt, die nicht direkt mit dem Azure Resource Manager-Modell kompatibel ist, können Sie Cloud Services nicht mit den Azure Resource Manager-Speicherkonten verknüpfen. Hier sind einige Optionen angegeben: 

- Bereitstellen über die REST-API

    Bei der Bereitstellung über die Dienstverwaltungs-REST-API können Sie die Einschränkung umgehen, indem Sie eine SAS-URL zum Blobspeicher angeben. Diese Vorgehensweise kann sowohl für klassische Konten als auch für Azure Resource Manager-Speicherkonten verwendet werden. Weitere Informationen zur PackageUrl-Eigenschaft finden Sie [hier](/previous-versions/azure/reference/ee460813(v=azure.100)).

- Bereitstellen über das [Azure-Portal](https://portal.azure.com)

    Die Bereitstellung ist über das [Azure-Portal](https://portal.azure.com) möglich, da der Aufruf über einen Proxy/Shim erfolgt, wodurch die Kommunikation zwischen Azure Resource Manager-Ressourcen und klassischen Ressourcen ermöglicht wird. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Warum verlangt das Azure-Portal von mir die Angabe eines Speicherkontos für die Bereitstellung?

Im klassischen Portal wurde das Paket direkt in die Verwaltungs-API-Schicht hochgeladen, und die API-Schicht speicherte das Paket dann vorübergehend in einem internen Speicherkonto.  Dieser Vorgang verursacht Leistungs- und Skalierbarkeitsprobleme, da die API-Schicht nicht als Dateiuploaddienst konzipiert wurde.  Im Azure-Portal (Resource Manager-Bereitstellungsmodell) haben wir den Zwischenschritt des ersten Hochladens in die API-Schicht umgangen, da dies zu schnelleren und zuverlässigeren Bereitstellungen führt.

Was die Kosten anbelangt, so ist sie sehr gering, und Sie können dasselbe Speicherkonto für alle Bereitstellungen wiederverwenden. Sie können den [Speicherkostenrechner](https://azure.microsoft.com/pricing/calculator/#storage1) verwenden, um die Kosten zu ermitteln, die für das Hochladen des Dienstpakets (CSPKG), das Herunterladen des CSPKG und das anschließenden Löschen des CSPKG anfallen.
