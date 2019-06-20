---
title: Automatisieren der Veröffentlichung von Angeboten | Azure Marketplace
description: Erläutert, wie der Workflow für die Veröffentlichung eines virtuellen Computers programmgesteuert automatisiert werden kann.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0a927c72a82c6aa3c79988c599ea8b840821a2b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935901"
---
<a name="automate-offer-publishing"></a>Automatisieren der Angebotsveröffentlichung
=========================

Sie können den Workflow für die Veröffentlichung eines virtuellen Computers auch programmgesteuert automatisieren, indem Sie die APIs im Abschnitt [API-Referenz](./cloud-partner-portal-api-overview.md) verwenden. Es gibt zwei unterschiedliche Szenarien, die für die Planung der Automatisierung zu berücksichtigen sind: erstmalige Angebotsveröffentlichung und spätere Angebotsveröffentlichung.


<a name="offer-initial-publishing"></a>Erstmalige Angebotsveröffentlichung
-------------------------

Wenn Sie ein Angebot zum ersten Mal veröffentlichen, sind vor dem Hochladen in den Marketplace einige zusätzliche Schritte erforderlich.  Beispielsweise müssen Sie die Metadaten vorbereiten und einen Angebotsentwurf erstellen. Der Workflow für eine erstmalige Veröffentlichung ist in der folgenden Abbildung dargestellt.

![Interaktionen für eine erstmalige Angebotsveröffentlichung](media/cloud-partner-portal-automate-offer-publishing/first-time-offer-publishing.png)

Im folgenden Code sind diese Schritte veranschaulicht.

``` csharp
  CreateOfferAndPublish()
  {
      offer = CreateOfferObject()
      // Set all offer attributes
      offer.offerTypeId = "microsoft-azure-virtualmachines",
      // Create offer on server
     result = CloudPartnerPortal.Client.PutOffer(offer);
      if(result.status != success)
      {
        // Do failure actions here
      }
      // Publish created offer
      Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
      if(op.HTTPStatus != OK)
      {
        /*Queuing the publish failed, check error message and dofailure actions*/
        return failure;
      }
      // Return success as operation is successfully queued
      return success;
  }

  ValidateAndGoLive()    
  {
      // Confirm the version in preview slot is the version that needs to go live
      offer = CloudPartnerPortal.Client.GetOffer(offerName, “Preview”);
      if(!offer[skuName].containsVersion(VMDisk.Version))
      {
          UpdateOfferAndPublish()
      }
      // Go Live on offer and check result
      Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
      if(op.HTTPStatus != OK)
      {
          // Queuing the publish failed, check error message and do failure actions
          return failure;
      }  
     
      // Return success as operation is successfully queued
      return success;
  }
```


<a name="subsequent-offer-publishing"></a>Spätere Angebotsveröffentlichung
---------------------------

Sobald das Angebot eines virtuellen Computers (VM) in eine Continuous Integration-Pipeline integriert ist, können Sie den Veröffentlichungsworkflow automatisieren, sodass er jedes Mal ausgeführt wird, wenn eine neue virtuelle Festplatte (VHD) erstellt wurde.  Dieser Workflow ist in der folgenden Abbildung und im folgenden Beispielcode veranschaulicht.

![Interaktionen für spätere Angebotsveröffentlichungen](media/cloud-partner-portal-automate-offer-publishing/update-offer-and-publish.png)

``` csharp
    UpdateOfferAndPublish()
    {
        // The routine wakes up after a build finishes. 
        // It determines if the current offer is undergoing publishing and take appropriate action
        // Wake up and get draft offer using offer name
        offer = CloudPartnerPortal.Client.GetOffer(offerName);
        if(!offer[skuName].containsVersion(VMDisk.Version))
        {
                // Update the list of VMs with new virtual machine. 
                // This assumes that less than 8 disks are present in the SKU
                // If 8 disks are already present, the client must remove one of the previous disks before adding new disk
                offer[skuName].addDisk(VMDisk)
                // Update the offer on server with new VMDisk
                CloudPartnerPortal.Client.PutOffer(offer);
        }
        // Publish offer and check result. Publish is idempotent, so if a publish was already in progress with latest version, this would return success.
        Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
        if(op.HTTPStatus != OK)
        {
              // Queuing the publish failed, check error message and do failure actions
              return failure;
        }
        
        // Return success as operation is successfully queued
        return success;
    }

    CheckLastOperationStatus()
    {
        Operation op = CloudPartnerPortal.Client.GetLastOpertation(offer.offerName)
        if(op.status == completed)
        {
            return success;
        }
        if(op.status == failure)
           // Do failure actions here
        
        else 
           // Last operation is still running
           // Go to sleep.           
    }

    ValidateAndGoLive()
    {
        // Confirm the version in preview slot is the version that needs to go live
        offer = CloudPartnerPortal.Client.GetOffer(offerName, “Preview”);
        if(!offer[skuName].containsVersion(VMDisk.Version))
        {
            UpdateOfferAndPublish()
        }
        // Go Live on offer and check result
        Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
        if(op.HTTPStatus != OK)
        {
              // Queuing the publish failed, check error message and do failure actions
              return failure;
        }
        // Return success as operation is successfully queued
        return success;
    }
```
