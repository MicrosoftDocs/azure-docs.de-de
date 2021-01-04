---
title: Sicherheitsübersicht für Azure Data Share
description: Sicherheitsübersicht für Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578092"
---
# <a name="security-overview-for-azure-data-share"></a>Sicherheitsübersicht für Azure Data Share

Dieser Artikel bietet eine Sicherheitsübersicht über den Azure Data Share-Dienst.

## <a name="security-overview"></a>Sicherheitsübersicht

Azure Data Share nutzt die zugrunde liegende Sicherheit, die von Azure angeboten wird, um ruhende Daten und Daten während der Übertragung zu schützen. Daten werden im ruhenden Zustand verschlüsselt, wenn dies vom zugrunde liegenden Datenspeicher unterstützt wird. Daten werden auch während der Übertragung per TLS 1.2 verschlüsselt. Metadaten zu einer Datenfreigabe werden im ruhenden Zustand und während der Übertragung ebenfalls verschlüsselt. Von Azure Data Share werden keine Inhalte der freigegebenen Kundendaten gespeichert.

Azure Data Share nutzt die verwaltete Identität (zuvor als MSI bezeichnet), um auf Datenspeicher zuzugreifen, die für die gemeinsame Nutzung von Daten verwendet werden. Zwischen einem Datenanbieter und einem Datenconsumer findet kein Austausch von Anmeldeinformationen statt. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Weitere Informationen zu Rollen und Berechtigungen, die für die gemeinsame Nutzung von Daten erforderlich sind, finden Sie unter [Rollen und Anforderungen](concepts-roles-permissions.md).

Zugriffssteuerungen für Azure Data Share können auf der Data Share-Ressourcenebene festgelegt werden, um sicherzustellen, dass nur von berechtigten Personen darauf zugegriffen wird. 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Freigeben von Daten für Datenspeicher mit aktivierter Firewall
Um Daten für Speicherkonten mit aktivierter Firewall in ein- oder ausgehender Richtung freizugeben, müssen Sie in Ihrem Speicherkonto die Option **Vertrauenswürdige Microsoft-Dienste zulassen** aktivieren. Ausführliche Informationen finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services).


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Freigeben von Daten finden Sie im Tutorial zum [Freigeben Ihrer Daten](share-your-data.md).
