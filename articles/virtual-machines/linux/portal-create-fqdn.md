---
title: Erstellen eines FQDN für Linux-VMs im Azure-Portal | Microsoft-Dokumentation
description: Enthält Informationen zum Erstellen eines vollqualifizierten Domänennamens (FQDN) für einen Resource Manager-basierten virtuellen Computer im Azure-Portal.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54d1f77823b982ee8f49122c46b0a01cb27390f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129416"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Erstellen eines vollqualifizierten Domänennamens im Azure-Portal für eine Linux-VM

Beim Erstellen eines virtuellen Computers (Virtual Machine, VM) im [Azure-Portal](https://portal.azure.com) wird automatisch eine öffentliche IP als Ressource für den virtuellen Computer erstellt. Mit dieser IP-Adresse greifen Sie per Remotezugriff auf den virtuellen Computer zu. Obwohl das Portal standardmäßig keinen [vollqualifizierten Domänennamen](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (Fully Qualified Domain Name, FQDN) erstellt, können Sie nach der Erstellung des virtuellen Computers einen solchen hinzufügen. Dieser Artikel demonstriert die einzelnen Schritte, um einen DNS-Namen oder einen FQDN zu erstellen.

## <a name="create-a-fqdn"></a>Erstellen eines FQDN
In diesem Artikel wird davon ausgegangen, dass Sie bereits einen virtuellen Computer erstellt haben. Bei Bedarf können Sie [einen virtuellen Computer im Portal](quick-create-portal.md) oder [mit der Azure-CLI](quick-create-cli.md) erstellen. Gehen Sie folgendermaßen vor, sobald Ihr virtueller Computer ausgeführt wird:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Sie können jetzt mithilfe dieses DNS-Namens eine Remoteverbindung mit dem virtuellen Computer herstellen, z.B. mit `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Nächste Schritte
Ihr virtueller Computer verfügt nun über eine öffentliche IP und einen DNS-Namen. Als Nächstes können Sie allgemeine Anwendungsframeworks oder Dienste bereitstellen, z.B. nginx, MongoDB, Docker usw.

Außerdem können Sie sich ausführlicher [über die Verwendung von Resource Manager informieren](../../azure-resource-manager/resource-group-overview.md), um Hinweise zum Erstellen Ihrer Azure-Bereitstellungen zu erhalten.

