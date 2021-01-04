---
title: Anfordern von Support für Avere vFXT für Azure
description: Hier erfahren Sie, wie Sie Probleme behandeln können, die bei der Bereitstellung oder Verwendung von Avere vFXT for Azure auftreten können, indem Sie ein Supportticket über das Azure-Portal erstellen.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 8159d83624cdd474d91030d5376a3db447beffe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008428"
---
# <a name="get-help-with-your-system"></a>Abrufen von Hilfe zu Ihrem System

Wenn Sie Hilfe bei der Verwendung von Avere vFXT-Systems für Azure benötigen, folgen hier die verschiedenen Möglichkeiten zum Anfordern von Unterstützung:

* **Avere vFXT-Problem** – Verwenden Sie das Azure-Portal, um ein Supportticket wie [unten](#open-a-support-ticket-for-your-avere-vfxt) beschrieben für Avere vFXT zu öffnen.
* **Kontingent** – Wenn ein kontingentsbezogenes Problem vorliegt, f [ordern Sie eine Kontingenterhöhung an](#request-a-quota-increase).
* **Dokumentation und Beispiele** – Wenn Probleme mit dieser Dokumentation oder Beispielen vorliegen, scrollen Sie zum Ende der Seite mit dem Problem und verwenden Sie den Abschnitt **Feedback**, um nach vorhandenen Problemen zu suchen und bei Bedarf ein neues Problem zu melden.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Öffnen eines Supporttickets für Avere vFXT

Wenn bei der Bereitstellung oder Verwendung von Avere vFXT Probleme auftreten, fordern Sie Hilfe über das Azure-Portal an.

Führen Sie diese Schritte aus, um sicherzustellen, dass Ihr Supportticket mit einer Ressource Ihres Clusters verknüpft ist. Durch die Markierung des Tickets können wir es an die richtige Supportressource weiterleiten.

1. Wählen Sie unter [https://portal.azure.com](https://portal.azure.com) die Option **Ressourcengruppen** aus. Navigieren Sie zu der Ressourcengruppe, die den vFXT-Cluster enthält, in dem das Problem aufgetreten ist, und klicken Sie auf einen der virtuellen Avere-Clustercomputer.

    ![Screenshot des Fensters „Übersicht“ der Azure-Portal-Ressourcengruppe mit einem eingekreisten bestimmten virtuellen Computer](media/avere-vfxt-ticket-vm.png)

1. Scrollen Sie auf der VM-Seite bis zum unteren Rand des linken Fensters, und klicken Sie auf **Neue Supportanfrage**.

    ![Screenshot des Azure-Portalseite für virtuelle Computer für den virtuellen Computer aus dem vorherigen Screenshot Unterer Rand des linken Menüs und „Neue Supportanfrage“ ist eingekreist.](media/avere-vfxt-ticket-request.png)

1. Wählen Sie auf der ersten Seite der Supportanfrage den Problemtyp aus, und vergewissern Sie sich, dass das richtige Abonnement ausgewählt ist.

   Klicken Sie unter **Dienst** auf **Alle Dienste**, und suchen Sie unter **Speicher**, um **Avere vFXT** auszuwählen.

   Fügen Sie eine kurze Zusammenfassung hinzu, und wählen Sie den Problemtyp aus.

    ![Screenshot einer neuen Supportanfrage in Azure-Portal. Die Registerkarte „Grundlagen“ ist ausgewählt. Bildschirmelemente sind z. B. „Problemtyp“, „Abonnement“, „Dienst“, „Zusammenfassung“ und „Problemtyp“.](media/ticket-basics.png)

   Klicken Sie auf zum Fortfahren auf **Weiter**.

1. Die zweite Seite des Supportformulars enthält Vorschläge zum Beheben des Problems basierend auf Ihrer Zusammenfassungsbeschreibung. Klicken Sie im unteren Bereich auf die Schaltfläche **Weiter**, wenn Sie trotzdem ein Supportticket erstellen müssen.

   ![Screenshot des Bildschirms „Neue Supportanfrage“ mit ausgewählter Registerkarte „Lösungen“. Ein Textfeld in der Mitte weist den Titel „Empfohlene Lösung“ auf und erläutert mögliche Abhilfemaßnahmen.](media/ticket-solutions.png)

1. Geben Sie auf der dritten Seite Details an. Dies umfasst Informationen zu Ihrem Cluster, den Zeitpunkt, zu dem das Problem aufgetreten ist, den Schweregrad und Ihre Kontaktinformationen. Geben Sie die Informationen ein, und klicken Sie im unteren Bereich auf die Schaltfläche **Weiter**.

   ![Screenshot des Bildschirms „Neue Supportanfrage“ mit ausgewählter Registerkarte „Details“. Informationsfelder sind in die Kategorien „Problemdetails“, „Supportmethode“ und „Kontaktinformationen“ unterteilt.](media/ticket-details.png)

1. Überprüfen Sie die Informationen auf der letzten Seite, und klicken Sie auf **Erstellen**. Eine Bestätigungs- und Ticketnummer wird an Ihre E-Mail-Adresse gesendet, und ein Supportmitarbeiter wird sich an Sie wenden.

## <a name="request-a-quota-increase"></a>Anfordern einer Kontingenterhöhung

Lesen Sie [Kontingent für den vFXT-Cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster), um zu erfahren, welche Komponenten für die Bereitstellung von Avere vFXT für Azure erforderlich sind. Sie können [eine Kontingenterhöhung](../azure-portal/supportability/resource-manager-core-quotas-request.md) über das Azure-Portal anfordern.