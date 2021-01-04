---
title: 'Problembehandlung: Mit dem SDK erstellte Arbeitsbereiche können Synapse Studio nicht starten.'
description: Schritte zum Beheben des Fehlers, dass mit dem SDK erstellte Arbeitsbereiche Synapse Studio nicht starten können
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465397"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Problembehandlung für Azure Synapse Analytics-Arbeitsbereiche, die mit dem SDK erstellt wurden

Dieser Artikel enthält Schritte zur Problembehandlung für das Starten von Synapse Studio aus einem Synapse-Arbeitsbereich, der mit einem SDK (Software Development Kit) erstellt wurde.


## <a name="prerequisites"></a>Voraussetzungen

- Ein mit einem SDK erstellter Synapse-Arbeitsbereich

## <a name="workaround"></a>Problemumgehung

Führen Sie die folgenden Schritte aus, um Synapse Studio aus dem Arbeitsbereich zu starten, der mit einem SDK erstellt wurde: 
  1.    Erstellen Sie den Arbeitsbereich, indem Sie `az synapse workspace create` ausführen.
  2.    Extrahieren Sie die ID der verwalteten Identität, indem Sie `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` ausführen.
  3.    Weisen Sie der verwalteten Identität für das Speicherkonto die Rolle „Mitwirkender an Storage-Blobdaten“ zu, indem Sie ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.` ausführen.
  4.    Fügen Sie eine Firewallregel hinzu, indem Sie ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` ausführen.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist Azure Synapse?](../overview-what-is.md)
* [Erste Schritte](../get-started.md)
