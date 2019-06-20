---
title: Übersicht über die Bereitstellung – Avere vFXT für Azure
description: Übersicht über das Bereitstellen von Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 88305e5d877f5bc3cc7ebb116f69b0f49f162b79
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409911"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT für Azure – Übersicht über die Bereitstellung

Dieser Artikel bietet eine Übersicht über die Schritte, die erforderlich sind, um einen Avere vFXT für Azure-Cluster in Betrieb zu nehmen.

Vor und nach dem Erstellen des vFXT-Clusters aus Azure Marketplace sind mehrere Aufgaben erforderlich. Ein sicheres Gespür für den gesamten Prozess hilft Ihnen, den erforderlichen Aufwand zu minimieren. 

## <a name="deployment-steps"></a>Bereitstellungsschritte

Nach der [Planung Ihres Systems](avere-vfxt-deploy-plan.md) können Sie mit der Erstellung des Avere vFXT-Clusters beginnen. 

Eine Azure Resource Manager-Vorlage in Azure Marketplace erfasst die erforderlichen Informationen und stellt automatisch den gesamten Cluster bereit. 

Nachdem der vFXT-Cluster in Betrieb genommen wurde, möchten Sie sicherlich wissen, wie Sie Clients damit verbinden und, falls erforderlich, Ihre Daten in den neuen Blob-Speichercontainer verschieben können.  

Hier folgt eine Übersicht über alle Schritte.

1. Konfigurieren der Voraussetzungen 

   Bevor Sie einen virtuellen Computer erstellen, müssen Sie ein neues Abonnement für das Avere vFXT-Projekt erstellen, den Abonnementbesitz konfigurieren, Kontingente prüfen und bei Bedarf eine Erhöhung anfordern sowie Bedingungen für die Nutzung der Avere vFXT-Software akzeptieren. Ausführliche Anweisungen finden Sie unter [Vorbereiten der Avere vFXT-Erstellung](avere-vfxt-prereqs.md).

1. Erstellen des Avere vFXT-Clusters 

   Verwenden Sie Azure Marketplace, um den Avere vFXT-Cluster für Azure zu erstellen. Eine Vorlage erfasst die erforderlichen Informationen und führt Skripts aus, um das endgültige Produkt zu erstellen.

   Die Erstellung des Clusters umfasst die folgenden Schritte, die alle von der Marketplace-Vorlage ausgeführt werden: 

   * Erstellen einer neuen Netzwerkinfrastruktur und von Ressourcengruppen, wenn erforderlich
   * Erstellen eines *Clustercontrollers*  

     Der Clustercontroller ist eine einfache VM, die sich im gleichen virtuellen Netzwerk wie der Avere vFXT-Cluster befindet und über die für die Erstellung und Verwaltung des Clusters erforderliche benutzerdefinierte Software verfügt. Der Controller erstellt die vFXT-Knoten und bildet den Cluster, und er stellt auch eine Befehlszeilenschnittstelle zur Verfügung, um den Cluster während seiner Lebensdauer zu verwalten.

     Wenn Sie während der Bereitstellung ein neues VNET erstellen, hat Ihr Controller eine öffentliche IP-Adresse. Dies bedeutet, dass der Controller als Jump Host für das Herstellen der Verbindung mit dem Cluster von außerhalb des virtuellen VNET dienen kann.

   * Erstellen der Clusterknoten-VMs

   * Konfigurieren der Clusterknoten-VMs, um das Cluster zu bilden

   * Optional Erstellen eines neuen Blobcontainers und Konfigurieren des Containers als Back-End-Speicher für den Cluster

1. Konfigurieren des Clusters 

   Stellen Sie eine Verbindung zur Avere vFXT-Konfigurationsschnittstelle (Avere-Systemsteuerung) her, um die Einstellungen des Clusters anzupassen. Melden Sie sich für die Supportüberwachung an, und fügen Sie Ihr Speichersystem hinzu, wenn Sie ein lokales Rechenzentrum verwenden.

   * [Zugreifen auf den vFXT-Cluster](avere-vfxt-cluster-gui.md)
   * [Aktivieren des Supports](avere-vfxt-enable-support.md)
   * [Konfigurieren des Speichers](avere-vfxt-add-storage.md) (falls erforderlich)

1. Einbinden von Clients

   Befolgen Sie die Anweisungen unter [Einbinden des Avere vFXT-Clusters](avere-vfxt-mount-clients.md), um mehr über den Lastausgleich und darüber zu erfahren, wie Clientcomputer den Cluster einbinden sollten.

1. Hinzufügen von Daten (falls erforderlich)

   Da es sich bei Avere vFXT um einen skalierbaren Cache mit mehreren Clients handelt, ist der beste Weg, Daten in einen neuen Back-End-Speichercontainer zu verschieben, die Strategie der Verwendung mehrerer Clients und mehrerer Threads. Weitere Informationen finden Sie unter [Verschieben von Daten in den vFXT-Cluster](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit [Vorbereiten der Avere vFXT-Erstellung](avere-vfxt-prereqs.md) fort, um vorbereitende Aufgaben für die Bereitstellung von Avere vFXT für Azure abzuschließen. 