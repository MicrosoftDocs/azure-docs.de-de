---
title: Erstellen und Zuordnen von Dienstendpunktrichtlinien – Azure-Portal
titlesuffix: Azure Virtual Network
description: In diesem Artikel erfahren Sie, wie Dienstendpunktrichtlinien über das Azure-Portal eingerichtet und zugeordnet werden.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: kumud
ms.openlocfilehash: b1d2d04e74828323166810d93c52a60671bf71e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710922"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Erstellen, Ändern oder Löschen einer Dienstendpunktrichtlinie über das Azure-Portal

Mithilfe von Dienstendpunktrichtlinien können Sie über Dienstendpunkte den Datenverkehr eines virtuellen Netzwerks auf bestimmte Azure-Ressourcen filtern. Wenn Sie nicht mit Dienstendpunktrichtlinien vertraut sind, lesen Sie [Dienstendpunktrichtlinien für virtuelle Netzwerke (Vorschau)](virtual-network-service-endpoint-policies-overview.md), um mehr zu erfahren.

 In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Dienstendpunktrichtlinie
> * Erstellen einer Definition einer Dienstendpunktrichtlinie
> * Erstellen eines virtuellen Netzwerks mit einem Subnetz
> * Zuordnen einer Dienstendpunktrichtlinie zu einem Subnetz

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure 

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-service-endpoint-policy"></a>Erstellen einer Dienstendpunktrichtlinie

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Geben Sie im Suchbereich „Dienstendpunktrichtlinie“ ein, und klicken Sie auf **Dienstendpunktrichtlinie (Vorschau)** und dann auf **Erstellen**.
3. Geben Sie unter **Allgemeine Informationen** folgende Informationen ein, bzw. wählen Sie sie aus: 

   - Abonnement: Wählen Sie Ihr Abonnement für die Richtlinie aus.    
   - Ressourcengruppe: Klicken Sie auf **Neu erstellen**, und geben Sie *myResourceGroup* ein.     
   - Name: myEndpointPolicy
   - Standort: USA, Westen-Mitte     
 
   ![Grundlagen zur Erstellung von Dienstendpunktrichtlinien](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Geben Sie unter **Richtliniendefinitionen** folgende Informationen ein, bzw. wählen Sie sie aus:

   - Klicken Sie auf **+ Eine Ressource hinzufügen**, und geben Sie die folgenden Informationen ein, oder wählen Sie sie aus. Übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Hinzufügen**.  
   - Bereich: Wählen Sie **Einzelnes Konto** oder **Alle Konten im Abonnement** oder **Alle Konten in der Ressourcengruppe** aus.    
   - Abonnement: Wählen Sie Ihr Abonnement für das Speicherkonto aus. Richtlinien- und Speicherkonten können sich in verschiedenen Abonnements befinden.   
   - Ressourcengruppe: Wählen Sie Ihre Ressourcengruppe aus. Erforderlich, wenn der Bereich auf „Alle Konten in der Ressourcengruppe“ oder auf „Einzelnes Konto“ festgelegt ist.  
   - Ressource: mystorageaccountportal    
   - Klicken Sie auf **+ Eine Ressource hinzufügen**, um mit dem Hinzufügen weiterer Ressourcen fortzufahren.
   
   ![Erstellen von Definitionen einer Dienstendpunktrichtlinie](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Optional: Geben Sie unter **Tags** folgende Informationen ein, bzw. wählen Sie sie aus:
   
   - Schlüssel: Wählen Sie Ihren Schlüssel für die Richtlinie aus. Beispiel: Abtlg     
   - Wert: Geben Sie den Wert für den Schlüssel ein. Beispiel: Finanzen

6. Klicken Sie auf **Überprüfen + erstellen**. Überprüfen Sie die Informationen, und klicken Sie auf **Erstellen**. Um weitere Änderungen vorzunehmen, klicken Sie auf **Zurück**. 

   ![Durchführen von abschließenden Überprüfungen einer Dienstendpunktrichtlinie](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Anzeigen von Endpunktrichtlinien 

1. Beginnen Sie im Portal im Feld *Alle Dienste* mit der Eingabe von *Dienstendpunktrichtlinien*. Wählen Sie **Dienstendpunktrichtlinien (Vorschau)** aus.
2. Wählen Sie unter **Abonnements** Ihr Abonnement und Ihre Ressourcengruppe aus, wie in der folgenden Abbildung dargestellt wird.

   ![Anzeigen einer Richtlinie](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Wählen Sie die Richtlinie aus, und klicken Sie auf **Richtliniendefinitionen**, um weitere Richtliniendefinitionen anzuzeigen oder hinzuzufügen.

   ![Anzeigen von Richtliniendefinitionen](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Wählen Sie **Zugeordnete Subnetze** aus, um die der Richtlinie zugeordneten Subnetze anzuzeigen. Um die Richtlinie einem Subnetz zuzuordnen, klicken Sie auf „Zum virtuellen Netzwerk in der gleichen Region navigieren“.

   ![Anzeigen der zugeordneten Subnetze](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Zuordnen einer Richtlinie zu einem Subnetz

>[!WARNING] 
> Stellen Sie sicher, dass alle Ressourcen für den ausgewählten Dienst, auf die über das Subnetz zugegriffen wird, zur Richtlinie hinzugefügt wurden, bevor Sie die Richtlinie zuordnen. Sobald die Richtlinie zugeordnet wurde, ist für Endpunktregionen für den Dienst nur der Zugriff auf die in der Richtlinie aufgeführten Ressourcen zulässig. 

Bevor Sie eine Richtlinie einem Subnetz zuordnen können, müssen Sie ein virtuelles Netzwerk und ein Subnetz erstellen. Danach können Sie die Richtlinie dem Subnetz zuordnen:

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.
3. Geben Sie unter **Virtuelles Netzwerk erstellen** die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Erstellen**:
   - Name: myVirtualNetwork      
   - Adressraum: 10.0.0.0/16      
   - Abonnement: Wählen Sie Ihr Abonnement aus. Die Richtlinie muss sich im selben Abonnement wie das VNET befinden.     
   - Ressourcengruppe: Wählen Sie **Vorhandene verwenden** und dann *myResourceGroup* aus.     
   - Standort: USA, Westen-Mitte     
   - Name des Subnetzes: Privat     
   - Adressbereich: 10.0.0.0/24
     
4. Beginnen Sie oben im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** mit der Eingabe von *myVirtualNetwork*. Wenn **myVirtualNetwork** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
5. Klicken Sie unter **EINSTELLUNGEN** auf **Subnetze**, und wählen Sie dann **Privat** aus.
6. Klicken Sie wie in der folgenden Abbildung gezeigt auf **Dienstendpunkte**, auf **Microsoft.Storage**, auf **Dienstendpunktrichtlinien**, auf **myEndpointPolicy** und dann auf **Speichern**:

   ![Zuordnen einer Richtlinie](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>Von diesem Subnetz aus kann basierend auf Netzwerksicherheitsgruppen (NSGs) auf Dienstressourcen in anderen Regionen zugegriffen werden. Um den Zugriff nur auf Endpunktregionen zu beschränken, beschränken Sie NSGs ausschließlich auf Datenverkehr von Diensten in Endpunktregionen ein. Weitere Informationen zum Erstellen von NSGs mit Diensttags pro Region finden Sie unter [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json).

Im folgenden Beispiel ist der Zugriff der NSG ausschließlich auf Azure Storage-Ressourcen in „USA, Westen-Mitte“ und „USA, Westen 2“ mit einer Regel namens „Alle ablehnen“ als Regel niedrigerer Priorität beschränkt.

![NSG „Alle ablehnen“](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie eine Dienstendpunktrichtlinie erstellt und einem Subnetz zugeordnet. Um mehr über Dienstendpunktrichtlinien zu erfahren, lesen Sie [Dienstendpunktrichtlinien für virtuelle Netzwerke (Vorschau)](virtual-network-service-endpoint-policies-overview.md).

