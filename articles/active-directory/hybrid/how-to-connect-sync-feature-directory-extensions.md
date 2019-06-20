---
title: 'Azure AD Connect-Synchronisierung: Verzeichniserweiterungen | Microsoft-Dokumentation'
description: Dieses Thema beschreibt das Verzeichniserweiterungsfeature in Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff0fd4d01eab739b79685c1de67cb8fe28873961
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347998"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect-Synchronisierung: Verzeichniserweiterungen
Sie können Verzeichniserweiterungen verwenden, um das Schema in Azure Active Directory (Azure AD) um Ihre eigenen Attribute aus dem lokalen Active Directory zu erweitern. Dank dieses Features können Sie Branchen-Apps erstellen, indem Sie Attribute nutzen, die Sie weiterhin lokal verwalten. Diese Attribute können über [Azure AD Graph-API-Verzeichniserweiterungen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) oder über [Microsoft Graph](https://developer.microsoft.com/graph/) genutzt werden. Sie können die verfügbaren Attribute mithilfe von [Azure AD Graph-Explorer](https://graphexplorer.azurewebsites.net/) bzw. des [Microsoft Graph-Testers](https://developer.microsoft.com/graph/graph-explorer) anzeigen.

Derzeit können diese Attribute von keiner Office 365-Workload genutzt werden.

Sie konfigurieren im Installations-Assistenten im Pfad der benutzerdefinierten Einstellungen, welche zusätzlichen Attribute synchronisiert werden sollen.

>[!NOTE]
>Im Feld „Verfügbare Attribute“ muss die Groß-/Kleinschreibung beachtet werden.

![Schemaerweiterungs-Assistent](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Bei der Installation werden folgenden Attribute als zulässige Kandidaten angezeigt:

* Benutzer- und Gruppenobjekttypen
* Einwertige Attribute: Zeichenfolge, Boolescher Wert, ganze Zahl, Binärwert
* Mehrwertige Attribute: Zeichenfolge, Binärwert


>[!NOTE]
> Azure AD Connect unterstützt zwar das Synchronisieren mehrwertiger Active Directory-Attribute für Azure AD als mehrwertige Verzeichniserweiterungen, doch gibt es derzeit keine Möglichkeit, die in Attribute für mehrwertige Verzeichniserweiterungen hochgeladenen Daten abzurufen/zu nutzen.

Die Attributliste wird aus dem Schemacache gelesen, der im Zuge der Installation von Azure AD Connect erstellt wird. Wenn Sie das Active Directory-Schema um zusätzliche Attribute erweitert haben, müssen Sie [das Schema aktualisieren](how-to-connect-installation-wizard.md#refresh-directory-schema), damit die neuen Attribute angezeigt werden.

Ein Objekt in Azure AD kann bis zu 100 Attribute für Verzeichniserweiterungen aufweisen. Die maximale Länge beträgt 250 Zeichen. Längere Attributwerte werden vom Synchronisierungsmodul gekürzt.

Während der Installation von Azure AD Connect wird eine Anwendung registriert, in der diese Attribute verfügbar sind. Diese Anwendung wird im Azure-Portal angezeigt.

![Schemaerweiterungs-App](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Die Attribute weisen das Präfix „extension\_{AppClientId}\_“ auf. Die AppClientId besitzt für alle Attribute in Ihrem Azure AD-Mandanten den gleichen Wert.

Diese Attribute sind jetzt über die Azure AD Graph-API verfügbar. Sie können sie mithilfe von [Azure AD Graph-Explorer](https://graphexplorer.azurewebsites.net/) abfragen.

![Azure AD Graph-Explorer](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Sie können die Attribute auch mithilfe des [Microsoft Graph-Testers](https://developer.microsoft.com/graph/graph-explorer#) über die Microsoft Graph-API abfragen.

>[!NOTE]
> Sie müssen angeben, dass die Attribute zurückgegeben werden. Wählen Sie explizit Attribute wie das folgende aus: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Weitere Informationen finden Sie unter [Microsoft Graph: Verwenden von Abfrageparametern](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md) .

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
