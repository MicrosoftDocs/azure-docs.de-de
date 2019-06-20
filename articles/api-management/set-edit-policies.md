---
title: Festlegen oder Bearbeiten von Azure API Management-Richtlinien | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie Azure API Management-Richtlinien festlegen oder bearbeiten.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3d1847b6001ef8e32f00a4e1cd9728d5ca0662f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097195"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Festlegen oder Bearbeiten von Azure API Management-Richtlinien

Die Richtliniendefinition ist ein XML-Dokument, das eine Sequenz eingehender und ausgehender Anweisungen beschreibt. Das XML-Dokument kann direkt im Definitionsfenster bearbeitet werden. Sie können auch eine vordefinierte Richtlinie aus der Liste auswählen, die rechts vom Richtlinienfenster angezeigt wird. Die für den aktuellen Bereich anwendbaren Anweisungen sind aktiviert und hervorgehoben. Wenn Sie auf eine aktivierte Anweisung klicken, wird der entsprechende XML-Ausschnitt an der Cursorposition in der Definitionsansicht eingefügt. 

Ausführliche Informationen zu Richtlinien finden Sie unter [Richtlinien in API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Festlegen oder Bearbeiten einer Richtlinie

Führen Sie zum Festlegen oder Bearbeiten einer Richtlinie die folgenden Schritte aus:

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Navigieren Sie zu Ihrer APIM-Instanz.
3. Klicken Sie auf die Registerkarte **APIs**.

    ![Richtlinie bearbeiten](./media/set-edit-policies/code-editor.png)

4. Wählen Sie eine der APIs aus, die Sie zuvor importiert haben.
5. Klicken Sie auf die Registerkarte **Entwurf**.
6. Wählen Sie einen Vorgang aus, auf den die Richtlinie angewendet werden soll. Wenn die Richtlinie auf alle Vorgänge angewendet werden soll, klicken Sie auf **Alle Vorgänge**.
7. Wählen Sie das Symbol **</>** (Codeeditor) im Abschnitt **Eingehende Verarbeitung** oder **Ausgehende Verarbeitung**.
8. Fügen Sie den gewünschten Richtliniencode in einen der entsprechenden Blöcke ein.
         
        <policies>
             <inbound>
                 <base />
             </inbound>
             <backend>
                 <base />
             </backend>
             <outbound>
                 <base />
             </outbound>
             <on-error>
                 <base />
             </on-error>
         </policies>
 
## <a name="configure-scope"></a>Konfigurieren des Suchbereichs

Richtlinien können entweder global oder im Geltungsbereich eines Produkts, einer API oder eines Vorgangs konfiguriert werden. Um eine Richtlinie zu konfigurieren, müssen Sie zunächst ihren gewünschten Geltungsbereich auswählen.

Richtlinienbereiche werden in der folgenden Reihenfolge ausgewertet:

1. Globaler Bereich
2. Produktbereich
3. API-Bereich
4. Vorgangsbereich

Die Anweisungen in diesen Richtlinien werden entsprechend der Platzierung des `base`-Elements ausgewertet, sofern es vorhanden ist. Eine globale Richtlinie besitzt keine übergeordnete Richtlinie, und die Verwendung des `<base>`-Elements in der Richtlinie hat keinerlei Auswirkung.

Um die Richtlinien im aktuellen Bereich im Richtlinien-Editor anzuzeigen, klicken Sie auf **Recalculate effective policy for selected scope**.

### <a name="global-scope"></a>Globaler Bereich

Der globale Bereich ist für **Alle APIs** in Ihrer APIM-Instanz konfiguriert.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrer APIM-Instanz.
2. Klicken Sie auf **Alle APIs**.

    ![Globaler Bereich](./media/api-management-howto-policies/global-scope.png)

3. Klicken Sie auf das Dreieckssymbol.
4. Klicken Sie auf **Code-Editor**.
5. Sie können Richtlinien hinzufügen oder bearbeiten.
6. Klicken Sie auf **Speichern**. 

    Die Änderungen werden sofort an das API Management-Gateway weitergegeben.

### <a name="product-scope"></a>Produktbereich

Der Produktbereich wird für das ausgewählte Produkt konfiguriert.

1. Klicken Sie auf **Produkte**.

    ![Produktbereich](./media/api-management-howto-policies/product-scope.png)

2. Wählen Sie das Produkt aus, auf das Richtlinien angewendet werden sollen.
3. Klicken Sie auf **Richtlinien**.
4. Sie können Richtlinien hinzufügen oder bearbeiten.
5. Klicken Sie auf **Speichern**. 

### <a name="api-scope"></a>API-Bereich

Der API-Bereich wird für **Alle Vorgänge** der ausgewählten API konfiguriert.

1. Wählen Sie die **API** aus, auf die Richtlinien angewendet werden sollen.

    ![API-Bereich](./media/api-management-howto-policies/api-scope.png)

2. Auswählen von **Alle Vorgänge**
3. Klicken Sie auf das Dreieckssymbol.
4. Klicken Sie auf **Code-Editor**.
5. Sie können Richtlinien hinzufügen oder bearbeiten.
6. Klicken Sie auf **Speichern**. 

### <a name="operation-scope"></a>Vorgangsbereich 

Der Vorgangsbereich wird für den ausgewählten Vorgang konfiguriert.

1. Wählen Sie eine **API** aus.
2. Wählen Sie den Vorgang aus, auf den Richtlinien angewendet werden sollen.

    ![Vorgangsbereich](./media/api-management-howto-policies/operation-scope.png)

3. Klicken Sie auf das Dreieckssymbol.
4. Klicken Sie auf **Code-Editor**.
5. Sie können Richtlinien hinzufügen oder bearbeiten.
6. Klicken Sie auf **Speichern**. 

## <a name="next-steps"></a>Nächste Schritte

Informationen finden Sie in den folgenden verwandten Themen:

+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](api-management-policy-reference.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management policy samples](policy-samples.md) (API Management-Richtlinienbeispiele)