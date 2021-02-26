---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494010"
---
In diesem Abschnitt wird erläutert, wie eine Einschränkung beim Sicherheitskontext (Security Context Constraint, SCC) angewendet wird. Für das Vorschaurelease lockern diese die Sicherheitseinschränkungen. 

1. Laden Sie die benutzerdefinierte Sicherheitskontexteinschränkung (Security Context Constraint, SCC) herunter. Verwenden Sie einen der folgenden Werte: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Raw](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
       Mit dem folgenden Befehl wird „arc-data-scc.yaml“ heruntergeladen:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Create SCC (SCC erstellen).

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Wenden Sie die Sicherheitskontexteinschränkung auf das Dienstkonto an.

   > [!NOTE]
   > Verwenden Sie hier und im Befehl `azdata arc dc create` unten denselben Namespace. Beispiel: `arc`.

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

   > [!NOTE]
   > In Red Hat OpenShift ab Version 4.5 wurde die Anwendung der Sicherheitskontexteinschränkung auf das Dienstkonto geändert.
   > Verwenden Sie hier und im Befehl `azdata arc dc create` unten denselben Namespace. Beispiel: `arc`. 
   > 
   > Wenn Sie Red Hat OpenShift 4.5 oder höher verwenden, führen Sie Folgendes aus: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
