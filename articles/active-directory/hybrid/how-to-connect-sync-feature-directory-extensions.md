---
title: 'Azure AD Connect Sync: Verzeichniserweiterungen | Microsoft Docs'
description: Dieses Thema beschreibt das Verzeichniserweiterungsfeature in Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74bc659c11c4f43ab3cf85cdc53f704cd07a1cde
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172366"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect Sync: Verzeichniserweiterungen
Sie können Verzeichniserweiterungen verwenden, um das Schema in Azure Active Directory (Azure AD) um Ihre eigenen Attribute aus dem lokalen Active Directory zu erweitern. Dank dieses Features können Sie Branchen-Apps erstellen, indem Sie Attribute nutzen, die Sie weiterhin lokal verwalten. Diese Attribute können über [Erweiterungen](/graph/extensibility-overview
) genutzt werden. Sie können die verfügbaren Attribute mithilfe von [Microsoft Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer) anzeigen. Sie können diese Funktion auch zum Erstellen dynamischer Gruppen in Azure AD verwenden.

Derzeit werden diese Attribute von keiner Microsoft 365-Workload genutzt.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Anpassen der mit Azure AD zu synchronisierenden Attribute

Sie konfigurieren im Installations-Assistenten im Pfad der benutzerdefinierten Einstellungen, welche zusätzlichen Attribute synchronisiert werden sollen.

> [!NOTE]
> In Versionen von Azure AD Connect vor 1.2.65.0 wird im Suchfeld für **Verfügbare Attribute** die Groß-/Kleinschreibung beachtet.

![Schemaerweiterungs-Assistent](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Bei der Installation werden folgenden Attribute als zulässige Kandidaten angezeigt:

* Benutzer- und Gruppenobjekttypen
* Einwertige Attribute: Zeichenfolge, Boolescher Wert, ganze Zahl, Binärwert
* Mehrwertige Attribute: Zeichenfolge, Binärwert


>[!NOTE]
> Azure AD Connect unterstützt zwar das Synchronisieren mehrwertiger Active Directory-Attribute für Azure AD als mehrwertige Verzeichniserweiterungen, doch gibt es derzeit keine Möglichkeit, die in Attribute für mehrwertige Verzeichniserweiterungen hochgeladenen Daten abzurufen/zu nutzen.

Die Attributliste wird aus dem Schemacache gelesen, der im Zuge der Installation von Azure AD Connect erstellt wird. Wenn Sie das Active Directory-Schema um zusätzliche Attribute erweitert haben, müssen Sie [das Schema aktualisieren](how-to-connect-installation-wizard.md#refresh-directory-schema), damit die neuen Attribute angezeigt werden.

Ein Objekt in Azure AD kann bis zu 100 Attribute für Verzeichniserweiterungen aufweisen. Die maximale Länge beträgt 250 Zeichen. Längere Attributwerte werden vom Synchronisierungsmodul gekürzt.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Konfigurationsänderungen in Azure AD, die vom Assistenten vorgenommen wurden

Während der Installation von Azure AD Connect wird eine Anwendung registriert, in der diese Attribute verfügbar sind. Diese Anwendung wird im Azure-Portal angezeigt. Ihr Name ist immer **Tenant Schema Extension App** .

![Schemaerweiterungs-App](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Zum Anzeigen dieser App müssen Sie **Alle Anwendungen** auswählen.

Die Attribute weisen das Präfix **extension \_{ApplicationId}\_** auf. Die Anwendungs-ID (ApplicationId) besitzt für alle Attribute in Ihrem Azure AD-Mandanten den gleichen Wert. Dieser Wert wird für alle anderen Szenarien in diesem Thema benötigt.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Anzeigen von Attributen mit der Microsoft Graph-API

Diese Attribute sind jetzt in [Microsoft Graph-Explorer](https://developer.microsoft.com/graph/graph-explorer#) über die Microsoft Graph-API verfügbar.

>[!NOTE]
> In der Microsoft Graph-API müssen Sie die zurückzugebenden Attribute anfordern. Wählen Sie die Attribute wie folgt explizit aus: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`.
>
> Weitere Informationen finden Sie unter [Microsoft Graph: Verwenden von Abfrageparametern](/graph/query-parameters#select-parameter).

>[!NOTE]
> Das Synchronisieren von Attributwerten aus AAD Connect mit Erweiterungsattributen, die nicht von AAD Connect erstellt wurden, wird nicht unterstützt. Dies kann zu einer Beeinträchtigung der Leistung und zu unerwarteten Ergebnissen führen. Nur Erweiterungsattribute, die wie oben gezeigt erstellt wurden, werden für die Synchronisierung unterstützt.

## <a name="use-the-attributes-in-dynamic-groups"></a>Verwenden der Attribute in dynamischen Gruppen

Eines der nützlicheren Szenarien ist die Verwendung dieser Attribute in dynamischen Sicherheits- oder Microsoft 365-Gruppen.

1. Erstellen Sie eine neue Gruppe in Azure AD. Vergeben Sie einen geeigneten Namen, und stellen Sie sicher, dass **Dynamischer Benutzer** für **Mitgliedschaftstyp** ausgewählt ist.

   ![Screenshot mit einer neuen Gruppe](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Wählen Sie **Dynamische Abfrage hinzufügen** aus. Wenn Sie sich die Eigenschaften ansehen, werden diese erweiterten Attribute nicht angezeigt. Sie müssen diese zunächst manuell hinzufügen. Klicken Sie auf **Benutzerdefinierte Erweiterungseigenschaften abrufen**, geben Sie die Anwendungs-ID ein, und klicken Sie auf **Eigenschaften aktualisieren**.

   ![Screenshot, auf dem Verzeichniserweiterungen hinzugefügt wurden](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Öffnen Sie die Dropdownliste mit Eigenschaften. Die von Ihnen hinzugefügten Attribute sind nun sichtbar.

   ![Screenshot mit neuen Attributen, die auf der Benutzeroberfläche angezeigt werden](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Vervollständigen Sie den Ausdruck entsprechend Ihren Anforderungen. In unserem Beispiel wird die Regel auf **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Sales and marketing")** festgelegt.

4. Geben Sie Azure AD nach dem Erstellen der Gruppe eine gewisse Zeit zum Hinzufügen der Mitglieder, und überprüfen Sie diese dann.

   ![Screenshot mit Mitgliedern in der dynamischen Gruppe](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md) .

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
