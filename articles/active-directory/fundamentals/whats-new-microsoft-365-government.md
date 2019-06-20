---
title: Neuerungen bei Azure Active Directory in Microsoft 365 Government – Azure Active Directory | Microsoft-Dokumentation
description: Lernen Sie einige Änderungen bei Azure Active Directory (Azure AD) in der Microsoft 365 Government-Cloudinstanz kennen, von denen Sie möglicherweise betroffen sind.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259348"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Neuerungen bei Azure Active Directory in Microsoft 365 Government

Bei Azure Active Directory (Azure AD) in der Microsoft 365 Government-Cloudinstanz wurden einige Änderungen vorgenommen, die für Kunden gelten, welche die folgenden Dienste nutzen:

- Microsoft Azure Government

- Microsoft 365 Government – GCC High

- Microsoft 365 Government – DoD

Dieser Artikel gilt nicht für Kunden von Microsoft 365 Government – GCC.

## <a name="changes-to-the-initial-domain-name"></a>Änderungen am anfänglichen Domänennamen

Bei der ersten Registrierung Ihrer Organisation für einen Microsoft 365 Government-Onlinedienst wurden Sie aufgefordert, den Domänennamen Ihrer Organisation, `<your-domain-name>.onmicrosoft.com`, auszuwählen. Wenn Sie bereits einen Domänennamen mit dem Suffix „.com“ haben, ändert sich nichts.

Wenn Sie sich jedoch für einen neuen Microsoft 365 Government-Dienst registrieren möchten, werden Sie aufgefordert, einen Domänennamen mit dem Suffix „`.us`“ auszuwählen. Daher lautet Ihr Domänenname `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Diese Änderung gilt nicht für Kunden, die von Clouddienstanbietern (CSPs) verwaltet werden.

## <a name="changes-to-portal-access"></a>Änderungen beim Portalzugriff

Die Portalendpunkte für Microsoft Azure Government, Microsoft 365 Government – GCC High und Microsoft 365 Government – DoD wurden aktualisiert, wie aus der [Tabelle mit der Endpunktzuordnung](#endpoint-mapping) hervorgeht.

Bisher konnten sich Kunden weltweit im Azure-Portal (portal.azure.com) und im Office 365-Portal (portal.office.com) anmelden. Infolge dieser Aktualisierung müssen sich die Kunden jetzt in den speziellen Portalen für Microsoft Azure Government, Microsoft 365 Government – GCC High und Microsoft 365 Government – DoD anmelden.

## <a name="endpoint-mapping"></a>Endpunktzuordnung

Die folgende Tabelle enthält die Endpunkte für alle Kunden:

| NAME | Endpunktdetails |
|------|------------------|
| Portale |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 Government – GCC High: https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Azure Active Directory Authority-Endpunkt | https://login.microsoftonline.us |
| Azure Active Directory Graph-API | https://graph.windows.net |
| Microsoft Graph-API für Microsoft 365 Government – GCC High | https://graph.microsoft.us |
| Microsoft Graph-API für Microsoft 365 Government – DoD | https://dod-graph.microsoft.us |
| Azure Government-Dienstendpunkte | Einzelheiten finden Sie im [Azure Government-Entwicklerhandbuch](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government – GCC High-Endpunkte | Einzelheiten finden Sie unter [Office 365 US Government – GCC High-Endpunkte](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government – DoD | Einzelheiten finden Sie unter [Office 365 US Government – DoD-Endpunkte](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen und Beispiele finden Sie in diesen Artikeln:

- [Was ist Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Blog: Azure Government AAD Authority Endpoint Update](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph-Endpunkte in US Government-Cloud](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government – GCC High und DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)