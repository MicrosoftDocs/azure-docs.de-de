---
title: IP-Adressen von Azure Integration Runtime
description: Erfahren Sie, welche IP-Adressen Sie für eingehenden Datenverkehr zulassen müssen, damit Sie Firewalls zum Sichern des Netzwerkzugriffs auf Datenspeicher ordnungsgemäß konfigurieren können.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 7b663c8d6e5849d39bb8366c82f45e0fd66d77dd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371395"
---
# <a name="azure-integration-runtime-ip-addresses"></a>IP-Adressen von Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die von Azure Integration Runtime verwendeten IP-Adressen richten sich nach der Region, in der sich Ihre Azure Integration Runtime-Instanz befindet. *Alle* Azure Integration Runtime-Instanzen in ein und derselben Region verwenden dieselben IP-Adressbereiche.

> [!IMPORTANT]  
> Datenflüsse und Azure Integration Runtime-Instanzen, die ein verwaltetes virtuelles Netzwerk aktivieren, unterstützen die Verwendung von festgelegten IP-Adressbereichen nicht.
>
> Sie können diese IP-Adressbereiche für Datenverschiebungen, Pipeline und externe Aktivitäten verwenden. Diese IP-Adressbereiche können auch verwendet werden, um in Datenspeichern, Netzwerksicherheitsgruppen (NSG) oder Firewalls für eingehenden Datenverkehr von Azure Integration Runtime einzurichten. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>IP-Adressen von Azure Integration Runtime: Bestimmte Regionen

Lassen Sie Datenverkehr von den IP-Adressen zu, die für Azure Integration Runtime in der jeweiligen Azure-Region aufgeführt sind, in der sich Ihre Ressourcen befinden. Sie können eine IP-Adressbereichsliste der Diensttags über den [Downloadlink für den IP-Adressbereich der Diensttags](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) abrufen. Wenn die Azure-Region z. B. **AustraliaEast** ist, können Sie eine IP-Adressbereichsliste von **DataFactory.AustraliaEast** erhalten.


## <a name="known-issue-with-azure-storage"></a>Bekanntes Problem mit Azure Storage

* Beim Herstellen einer Verbindung mit einem Azure Storage-Konto haben IP-Netzwerkregeln keine Auswirkungen auf Anforderungen aus der Azure Integration Runtime-Instanz, die sich in derselben Region befindet wie das Storage-Konto. Weitere Informationen finden Sie in [diesem Artikel](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range). 

  Stattdessen wird empfohlen, [beim Herstellen einer Verbindung mit Azure Storage vertrauenswürdige Dienste](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993) zu verwenden. 

## <a name="next-steps"></a>Nächste Schritte

* [Sicherheitsüberlegungen für Datenverschiebungen in Azure Data Factory](data-movement-security-considerations.md)