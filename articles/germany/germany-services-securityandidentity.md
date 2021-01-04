---
title: Sicherheits- und Identitätsdienste von Azure Deutschland | Microsoft-Dokumentation
description: Dieser Artikel enthält einen Vergleich der Sicherheits- und Identitätsdienste für Azure Deutschland.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 1d66a1f9a1fd2aea17d28d52cbcaa102ec3cb6cc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018492"
---
# <a name="azure-germany-security-and-identity-services"></a>Sicherheits- und Identitätsdienste von Azure Deutschland

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="key-vault"></a>Key Vault
Einzelheiten zum Azure Key Vault-Dienst und seiner Verwendung finden Sie in der [globalen Dokumentation zu Key Vault](../key-vault/index.yml).

Key Vault ist in Azure Deutschland allgemein verfügbar. Wie auch bei der globalen Azure-Umgebung gibt es keine Erweiterung. Key Vault ist daher nur über PowerShell und die CLI verfügbar.

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory stellt Identitäts- und Zugriffsfunktionen für Informationssysteme in Microsoft Azure bereit. Durch die Verwendung von Verzeichnisdiensten, Sicherheitsgruppen und Gruppenrichtlinien können Sie die Zugriffs- und Sicherheitsrichtlinien von Computern steuern, die Azure Active Directory verwenden. Mithilfe von Konten und Sicherheitsgruppen können Sie in Verbindung mit der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) den Zugriff auf die Informationssysteme steuern. 

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
Abonnieren Sie den [Azure Deutschland-Blog](/archive/blogs/azuregermany/), um weitere Informationen und Updates zu erhalten.