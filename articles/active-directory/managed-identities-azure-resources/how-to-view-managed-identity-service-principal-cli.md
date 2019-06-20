---
title: Anzeigen des Dienstprinzipals einer verwalteten Identität über die Azure CLI
description: Schrittanleitungen zur Anzeige des Dienstprinzipals einer verwalteten Identität über die Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f379c78113a4edc1efc288617a8a1c205d03552a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60442283"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Anzeigen des Dienstprinzipals einer verwalteten Identität über die Azure CLI

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erhalten Sie Informationen zur Anzeige des Dienstprinzipals einer verwalteten Identität über die Azure CLI.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter.
- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/) registrieren.
- Aktivieren Sie eine [systemseitig zugewiesene Identität auf einem virtuellen Computer](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) oder in einer [Anwendung](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Um die CLI-Skriptbeispiele auszuführen, haben Sie drei Möglichkeiten:
    - Verwenden Sie [Azure Cloud Shell](../../cloud-shell/overview.md) aus dem Azure-Portal (siehe nächster Abschnitt).
    - Verwenden Sie die eingebettete Azure Cloud Shell, indem Sie die Schaltfläche „Ausprobieren“ in der oberen rechten Ecke jedes Codeblocks verwenden.
    - [Installieren Sie die aktuelle Version der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), wenn Sie lieber eine lokale CLI-Konsole verwenden möchten, und melden Sie sich mit `az login` bei Azure an.
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Anzeigen des Dienstprinzipals

Mit dem folgenden Befehl zeigen Sie den Dienstprinzipal einer VM oder einer Anwendung mit aktivierter verwalteter Identität an. Ersetzen Sie `<VM or application name>` durch Ihre eigenen Werte. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten von Azure AD-Dienstprinzipalen mit der Azure CLI finden Sie unter [az ad sp](/cli/azure/ad/sp).


