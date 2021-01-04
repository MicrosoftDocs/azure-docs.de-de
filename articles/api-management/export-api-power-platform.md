---
title: Exportieren von APIs aus Azure API Management in Power Platform | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie APIs aus API Management in Power Platform exportieren.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 7c4d32dd63120a52fd7351977943574455e5cfad
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146653"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Exportieren von APIs aus Azure API Management in Power Platform 

Entwickler ohne Programmiererfahrung, die Microsoft [Power Platform](https://powerplatform.microsoft.com) nutzen, benötigen häufig Zugang zu den geschäftlichen Funktionen, die von professionellen Entwicklern entwickelt und in Azure bereitgestellt werden. [Azure API Management](https://aka.ms/apimrocks) ermöglicht professionellen Entwicklern die Veröffentlichung ihres Back-End-Diensts als APIs sowie das einfache Exportieren dieser APIs in Power Platform (Power Apps und Power Automate) als benutzerdefinierte Connectors, die Entwickler ohne Programmiererfahrung nutzen können. 

In diesem Artikel werden die Schritte zum Exportieren von APIs aus API Management in Power Platform erläutert. 

## <a name="prerequisites"></a>Voraussetzungen

+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Vergewissern Sie sich, dass Ihre API Management-Instanz die API enthält, die Sie in Power Platform exportieren möchten.
+ Stellen Sie sicher, dass Sie über eine Power Apps- oder Power Automate-[Umgebung](/powerapps/powerapps-overview#power-apps-for-admins) verfügen. 

## <a name="export-an-api"></a>Exportieren einer API

1. Navigieren Sie im Azure-Portal zu Ihrem API Management-Dienst, und wählen Sie im Menü **APIs** aus.
2. Klicken Sie auf die drei Punkte neben der API, die Sie exportieren möchten. 
3. Wählen Sie **Exportieren** aus.
4. Wählen Sie **Power Apps und Power Automate** aus.
5. Wählen Sie eine Umgebung aus, in die die API exportiert werden soll. 
6. Geben Sie einen Anzeigenamen an, der als Name des benutzerdefinierten Connectors verwendet wird.  
7. Optional: Wenn die API durch einen OAuth 2.0-Server geschützt wird, müssen Sie darüber hinaus weitere Details angeben, u. a. `Client ID`, `Client secret`, `Authorization URL`, `Token URL` und `Refresh URL`.  
8. Wählen Sie **Exportieren** aus. 

Navigieren Sie nach Abschluss des Exports zu Ihrer Power Apps- oder Power Automate-Umgebung. Die API wird als benutzerdefinierter Connector angezeigt.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über [Power Platform](https://powerplatform.microsoft.com/).
* [Machen Sie sich anhand der Tutorials ausführlicher mit allgemeinen Aufgaben in API Management vertraut.](./import-and-publish.md)
