---
title: Problemumgehung für Avere vFXT für Nicht-Besitzer – Azure
description: Problemumgehung, um Benutzern ohne die Berechtigung des Abonnementbesitzers die Bereitstellung von Avere vFXT für Azure zu ermöglichen
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: e72e6d969649de09389ee38b94e874fad98ee08f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409208"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autorisieren von Nicht-Besitzern zur Bereitstellung von Avere vFXT

Diese Anleitung ist ein Problemumgehung, die es einem Benutzer ohne Berechtigung des Abonnementbesitzers ermöglicht, ein Avere vFXT für Azure-System zu erstellen.

(Die empfohlene Methode zur Bereitstellung des Avere vFXT-Systems besteht darin, dass ein Benutzer mit Besitzerberechtigungen die Erstellungsschritte durchführt, wie in [Vorbereiten der Avere vFXT-Erstellung](avere-vfxt-prereqs.md) beschrieben.  

Die Problemumgehung umfasst die Erstellung einer zusätzlichen Zugriffsrolle, die ihren Benutzern ausreichende Berechtigungen für die Installation des Clusters gibt. Die Rolle muss von einem Abonnementbesitzer erstellt werden und ein Besitzer muss sie geeigneten Benutzern zuordnen. 

Ein Abonnementbesitzer muss auch die Nutzungsbedingungen für das Marketplace-Image für Avere vFXT [akzeptieren](avere-vfxt-prereqs.md). 

> [!IMPORTANT] 
> Sämtliche dieser Schritte müssen von einem Benutzer mit Besitzerberechtigungen für das Abonnement durchgeführt werden, das für den Cluster verwendet wird.

1. Kopieren Sie diese Zeilen, und speichern Sie sie in einer Datei (z. B. `averecreatecluster.json`). Verwenden Sie Ihre Abonnement-ID in der `AssignableScopes`-Anweisung.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",
   
           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Führen Sie den folgenden Befehl aus, um die Rolle zu erstellen:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Beispiel:
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Weisen Sie diese Rolle dem Benutzer zu, der den Cluster erstellen soll:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Nach diesem Vorgang hat jeder Benutzer, dem diese Rolle zugeordnet ist, die folgenden Berechtigungen für das Abonnement: 

* Erstellen und Konfigurieren der Netzwerkinfrastruktur
* Erstellen des Clustercontrollers
* Ausführen von Skripts zur Clustererstellung über den Clustercontroller zur Erstellung des Clusters
