---
title: Erstellen und Zuordnen von Dienstendpunktrichtlinien – Azure-Portal
titlesuffix: Azure Virtual Network
description: In diesem Artikel erfahren Sie, wie Dienstendpunktrichtlinien über das Azure-Portal eingerichtet und zugeordnet werden.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004958"
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
2. Geben Sie im Suchbereich „Dienstendpunktrichtlinie“ ein, und wählen Sie **Dienstendpunktrichtlinie** und dann **Erstellen** aus.

![Dienstendpunktrichtlinie erstellen](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Geben Sie unter **Allgemeine Informationen** folgende Informationen ein, bzw. wählen Sie sie aus: 

   - Abonnement: Wählen Sie Ihr Abonnement für die Richtlinie aus.
   - Ressourcengruppe: Klicken Sie auf **Neue erstellen**, und geben Sie *myResourceGroup* ein.
   - Name: myEndpointPolicy
   - Standort: USA (Mitte)
 
   ![Grundlagen zur Erstellung von Dienstendpunktrichtlinien](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Wählen Sie unter **Ressourcen** die Option **+ Hinzufügen** aus, und geben Sie im Bereich **Ressource hinzufügen** die folgenden Informationen ein, oder wählen Sie sie dort aus:

   - Dienst        : Bei „Dienstendpunktrichtlinien“ steht nur **Microsoft.Storage** zur Verfügung.
   - Bereich: Wählen Sie eine der Optionen **Einzelnes Konto**, **Alle Konten im Abonnement** oder **Alle Konten in der Ressourcengruppe** aus.
   - Abonnement: Wählen Sie Ihr Abonnement für das Speicherkonto aus. Richtlinien- und Speicherkonten können sich in verschiedenen Abonnements befinden.
   - Ressourcengruppe: Wählen Sie Ihre Ressourcengruppe aus. Erforderlich, wenn der Bereich auf „Alle Konten in der Ressourcengruppe“ oder auf „Einzelnes Konto“ festgelegt ist.  
   - Ressource       : Wählen Sie unter dem ausgewählten Abonnement oder der ausgewählten Ressourcengruppe Ihre Azure Storage-Ressource aus.
   - Klicken Sie unten auf die Schaltfläche **Hinzufügen**, um das Hinzufügen der Ressource abzuschließen.

   ![Definition einer Dienstendpunktrichtlinie – Ressource](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Fügen Sie weitere Ressourcen hinzu, indem Sie die vorstehenden Schritte nach Bedarf wiederholen.

5. Optional: Geben Sie unter **Tags** folgende Informationen ein, bzw. wählen Sie sie aus:
   
   - Schlüssel: Wählen Sie Ihren Schlüssel für die Richtlinie aus. Beispiel: Abtlg     
   - Wert: Geben Sie den Wert für den Schlüssel ein. Beispiel: Finanzen

6. Klicken Sie auf **Überprüfen + erstellen**. Überprüfen Sie die Informationen, und klicken Sie auf **Erstellen**. Um weitere Änderungen vorzunehmen, klicken Sie auf **Zurück**. 

   ![Durchführen von abschließenden Überprüfungen einer Dienstendpunktrichtlinie](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Anzeigen von Endpunktrichtlinien 

1. Beginnen Sie im Portal im Feld *Alle Dienste* mit der Eingabe von *Dienstendpunktrichtlinien*. Wählen Sie **Dienstendpunktrichtlinien** aus.
2. Wählen Sie unter **Abonnements** Ihr Abonnement und Ihre Ressourcengruppe aus, wie in der folgenden Abbildung dargestellt wird.

   ![Anzeigen einer Richtlinie](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Wählen Sie die Richtlinie aus, und klicken Sie auf **Richtliniendefinitionen**, um weitere Richtliniendefinitionen anzuzeigen oder hinzuzufügen.

   ![Anzeigen von Richtliniendefinitionen](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Wählen Sie **Zugeordnete Subnetze** aus, um die der Richtlinie zugeordneten Subnetze anzuzeigen. Wenn noch kein Subnetz zugeordnet wurde, führen Sie die Anleitungen im nächsten Schritt aus.

   ![Zugeordnete Subnetze](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Zuordnen einer Richtlinie zu einem Subnetz

>[!WARNING] 
> Stellen Sie sicher, dass alle Ressourcen, auf die über das Subnetz zugegriffen wird, der Richtliniendefinition hinzugefügt wurden, bevor Sie die Richtlinie dem angegebenen Subnetz zuordnen. Sobald die Richtlinie zugeordnet wurde, wird nur der Zugriff auf die *in der Zulassungsliste aufgeführten* Ressourcen über Dienstendpunkte gestattet. 
>
> Stellen Sie außerdem sicher, dass in dem der Dienstendpunktrichtlinie zugeordneten Subnetz keine verwalteten Azure-Dienste vorhanden sind.

- Bevor Sie eine Richtlinie einem Subnetz zuordnen können, müssen Sie ein virtuelles Netzwerk und ein Subnetz erstellen. Hilfe dazu finden Sie im Artikel [Erstellen eines virtuellen Netzwerks](./quick-create-portal.md).

- Nachdem Sie das virtuelle Netzwerk und das Subnetz eingerichtet haben, müssen Sie VNET-Dienstendpunkte für Azure Storage konfigurieren. Wählen Sie auf dem Blatt „Virtual Network“ die Option **Dienstendpunkte**, anschließend im nächsten Bereich **Microsoft.Storage** und dann unter **Subnetze** das gewünschte VNET oder Subnetz aus.

- Jetzt können Sie entweder aus der Dropdownliste im vorstehenden Bereich die gewünschte Dienstendpunktrichtlinie auswählen, wenn Sie bereits Dienstendpunktrichtlinien erstellt haben, bevor Sie den Dienstendpunkt für das Subnetz konfigurieren (siehe die Abbildung unten)

    ![Subnetz beim Erstellen des Dienstendpunkts zuordnen](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- ODER – wenn Sie Dienstendpunktrichtlinien erst nach der Konfiguration von Dienstendpunkten zuordnen – auswählen, dass das Subnetz über das Blatt „Dienstendpunktrichtlinie“ zugeordnet werden soll, indem Sie zum Bereich **Zugeordnete Subnetze** navigieren (siehe die Abbildung unten).

    ![Subnetz über SEP zuordnen](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Der Zugriff auf Azure Storage-Ressourcen in allen Regionen wird gemäß der Dienstendpunktrichtlinie (Service Endpoint Policy, SEP) aus diesem Subnetz eingeschränkt.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie eine Dienstendpunktrichtlinie erstellt und einem Subnetz zugeordnet. Um mehr über Dienstendpunktrichtlinien zu erfahren, lesen Sie [Dienstendpunktrichtlinien für virtuelle Netzwerke (Vorschau)](virtual-network-service-endpoint-policies-overview.md).
