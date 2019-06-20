---
title: Sicherheits- und Identitätsdienste von Azure Deutschland | Microsoft-Dokumentation
description: Vergleich der Sicherheits- und Identitätsdienste für Azure Deutschland
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: ee98e074b8bf21abe198506899b99be4e2908f14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685929"
---
# <a name="azure-germany-security-and-identity-services"></a>Sicherheits- und Identitätsdienste von Azure Deutschland
## <a name="key-vault"></a>Key Vault
Einzelheiten zum Azure Key Vault-Dienst und seiner Verwendung finden Sie in der [globalen Dokumentation zu Key Vault](../key-vault/index.yml).

Key Vault ist in Azure Deutschland allgemein verfügbar. Wie auch bei der globalen Azure-Umgebung gibt es keine Erweiterung. Key Vault ist daher nur über PowerShell und die CLI verfügbar.

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory stellt Identitäts- und Zugriffsfunktionen für Informationssysteme in Microsoft Azure bereit. Durch die Verwendung von Verzeichnisdiensten, Sicherheitsgruppen und Gruppenrichtlinien können Sie die Zugriffs- und Sicherheitsrichtlinien von Computern steuern, die Azure Active Directory verwenden. Mithilfe von Konten und Sicherheitsgruppen können Sie in Verbindung mit der rollenbasierten Zugriffssteuerung (RBAC) den Zugriff auf die Informationssysteme steuern. 

Azure Active Directory ist in Azure Deutschland allgemein verfügbar.

### <a name="variations"></a>Abweichungen

* Azure Active Directory in Azure Deutschland ist vollständig von Azure Active Directory in der globalen Azure-Umgebung getrennt. 
* Kunden können sich nicht mit einem Microsoft-Konto bei Azure Deutschland anmelden.
* Das Anmeldesuffix für Azure Deutschland lautet *onmicrosoft.de* (nicht *onmicrosoft.com* wie bei der globalen Azure-Umgebung).
* Kunden benötigen ein gesondertes Abonnement für Azure Deutschland.
* Kunden von Azure Deutschland können nicht auf Ressourcen zugreifen, die ein Abonnement oder eine Identität in der globalen Azure-Umgebung erfordern.
* Kunden der globalen Azure-Umgebung können nicht auf Ressourcen zugreifen, die ein Abonnement oder eine Identität in Azure Deutschland erfordern.
* Zusätzliche Domänen können nur in einer der Cloudumgebungen hinzugefügt bzw. überprüft werden.
 
> [!NOTE]
> Die Option zum Zuweisen von Rechten an Benutzer durch andere Mandanten, bei denen sich *beide Mandanten in Azure Deutschland befinden*, ist noch nicht verfügbar.


## <a name="next-steps"></a>Nächste Schritte
Abonnieren Sie den [Azure Deutschland-Blog](https://blogs.msdn.microsoft.com/azuregermany/), um weitere Informationen und Updates zu erhalten.




