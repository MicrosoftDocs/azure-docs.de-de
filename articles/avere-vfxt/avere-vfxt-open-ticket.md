---
title: Anfordern von Support für Avere vFXT für Azure
description: Erläuterung zum Öffnen von Supporttickets für Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d621511cbb6983f8ad57ea8305823039475f40d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409634"
---
# <a name="get-help-with-your-system"></a>Abrufen von Hilfe zu Ihrem System

Wenn Sie Hilfe bei der Verwendung von Avere vFXT für Azure benötigen, folgen hier die verschiedenen Möglichkeiten zum Anfordern von Unterstützung:

* **Avere vFXT-Problem** – Verwenden Sie das Azure-Portal, um ein Supportticket wie [unten](#open-a-support-ticket-for-your-avere-vfxt) beschrieben für Avere vFXT zu öffnen.
* **Kontingent** – Wenn ein kontingentsbezogenes Problem vorliegt, f[ordern Sie eine Kontingenterhöhung an](#request-a-quota-increase).
* **Dokumentation und Beispiele** – Wenn Probleme mit dieser Dokumentation oder Beispielen vorliegen, scrollen Sie zum Ende der Seite mit dem Problem und verwenden Sie den Abschnitt **Feedback**, um nach vorhandenen Problemen zu suchen und bei Bedarf ein neues Problem zu melden.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Öffnen eines Supporttickets für Avere vFXT

Wenn bei der Bereitstellung oder Verwendung von Avere vFXT Probleme auftreten, fordern Sie Hilfe über das Azure-Portal an.  

Führen Sie diese Schritte aus, um sicherzustellen, dass Ihr Supportticket mit einer Ressource Ihres Clusters verknüpft ist. Durch die Markierung des Tickets können wir es an die richtige Supportressource weiterleiten. 

1. Wählen Sie unter [https://portal.azure.com](https://portal.azure.com) die Option **Ressourcengruppen** aus.

   ![Screenshot des linken Menüs des Azure-Portals mit eingekreister Option „Ressourcengruppen“](media/avere-vfxt-ticket-rg.png)

1. Navigieren Sie zu der Ressourcengruppe, die den vFXT-Cluster enthält, in dem das Problem aufgetreten ist, und klicken Sie auf einen der virtuellen Avere-Computer.

    ![Screenshot des Fensters „Übersicht“ der Azure-Portal-Ressourcengruppe mit einem eingekreisten bestimmten virtuellen Computer](media/avere-vfxt-ticket-vm.png)

1. Scrollen Sie auf der VM-Seite bis zum unteren Rand des linken Fensters, und klicken Sie auf **Neue Supportanfrage**.

    ![Screenshot des Azure-Portalseite für virtuelle Computer für den virtuellen Computer aus dem vorherigen Screenshot Unterer Rand des linken Menüs und „Neue Supportanfrage“ ist eingekreist](media/avere-vfxt-ticket-request.png)

1. Klicken Sie auf der ersten Seite der Supportanfrage auf **Alle Dienste** und suchen Sie unter **Speicher**, um **Avere vFXT** auszuwählen.

    ![Screenshot des neuen Bildschirms für Supportanfragen im Azure-Portal mit der Überschrift „Grundlagen“ und einem Kreis um die Position „Dienst“ Die Schaltfläche „Alle Dienste“ ist aktiviert und das Dropdownmenü zeigt den Wert „Avere vFXT“ an.](media/avere-vfxt-ticket-service.png)

1. Wählen Sie auf der zweiten Seite den Problemtyp und die Kategorie aus, die Ihrem Problem am ehesten entsprechen. Fügen Sie einen kurzen Titel und eine Beschreibung hinzu, die den Zeitpunkt enthält, zu dem das Problem aufgetreten ist. 

   ![Screenshot des neuen Bildschirms für Supportanfragen mit der Überschrift „Problem“, der viele Felder enthält, die ausgefüllt werden müssen](media/avere-vfxt-ticket-problem.png)

1. Geben Sie auf der dritten Seite Ihre Kontaktinformationen ein, und klicken Sie auf **Erstellen**. Eine Bestätigungs- und Ticketnummer wird an Ihre E-Mail-Adresse gesendet, und ein Supportmitarbeiter wird sich an Sie wenden.

## <a name="request-a-quota-increase"></a>Anfordern einer Kontingenterhöhung

Lesen Sie [Kontingent für den vFXT-Cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster), um zu erfahren, welche Komponenten für die Bereitstellung von Avere vFXT für Azure erforderlich sind. Sie können [eine Kontingenterhöhung](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) über das Azure-Portal anfordern.