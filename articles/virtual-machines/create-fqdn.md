---
title: Erstellen eines FQDN für VMs im Azure-Portal
description: Erfahren Sie, wie Sie im Azure-Portal einen vollqualifizierten Domänennamen (FQDN) für eine VM erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351845"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Erstellen eines vollqualifizierten Domänennamens im Azure-Portal für eine Linux-VM

Beim Erstellen eines virtuellen Computers (Virtual Machine, VM) im [Azure-Portal](https://portal.azure.com) wird automatisch eine öffentliche IP als Ressource für den virtuellen Computer erstellt. Mit dieser IP-Adresse greifen Sie per Remotezugriff auf den virtuellen Computer zu. Obwohl das Portal standardmäßig keinen [vollqualifizierten Domänennamen](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (Fully Qualified Domain Name, FQDN) erstellt, können Sie nach der Erstellung des virtuellen Computers einen solchen hinzufügen. Dieser Artikel demonstriert die einzelnen Schritte, um einen DNS-Namen oder einen FQDN zu erstellen. 

## <a name="create-a-fqdn"></a>Erstellen eines FQDN
In diesem Artikel wird davon ausgegangen, dass Sie bereits einen virtuellen Computer erstellt haben. Bei Bedarf können Sie im Portal eine [Linux](./linux/quick-create-portal.md)- oder [Windows](./windows/quick-create-portal.md)-VM erstellen. Sobald Ihr virtueller Computer eingerichtet ist und ausgeführt wird, gehen Sie folgendermaßen vor:


1. Wählen Sie im Portal Ihren virtuellen Computer aus. Wählen Sie unter **DNS-Name** die Option **Konfigurieren** aus.
2. Geben Sie den DNS-Namen ein, und wählen Sie dann oben auf der Seite **Speichern** aus.
3. Um zum Übersichtsblatt der VM zurückzukehren, schließen Sie das Blatt **Konfiguration** , indem Sie in der rechten oberen Ecke das **X** auswählen. 
4. Überprüfen Sie, ob der *DNS-Name* jetzt richtig angezeigt wird.
   



## <a name="next-steps"></a>Nächste Schritte
Ihre VM verfügt nun über eine öffentliche IP-Adresse und einen DNS-Namen. Als Nächstes können Sie allgemeine Anwendungsframeworks oder Dienste wie z. B. NGINX, MongoDB und Docker bereitstellen.

Außerdem können Sie sich ausführlicher [über die Verwendung von Resource Manager informieren](../azure-resource-manager/management/overview.md), um Hinweise zum Erstellen Ihrer Azure-Bereitstellungen zu erhalten.

