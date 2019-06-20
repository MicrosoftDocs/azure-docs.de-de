---
title: Zuweisen des Zugriffs einer verwalteten Identität auf eine Azure-Ressource im Azure-Portal
description: Hier finden Sie Schritt-für-Schritt-Anleitungen für das Zuweisen einer verwalteten Identität für eine Ressource und für das Zuweisen des Zugriffs auf eine andere Ressource über das Azure-Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 958b3d72a3a8df4a3b67f62e7db788d7142ca667
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112920"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Zuweisen des Zugriffs einer verwalteten Identität auf eine Ressource über das Azure-Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Nachdem Sie eine Azure-Ressource mit einer verwalteten Identität konfiguriert haben, können Sie der verwalteten Identität genau wie bei allen anderen Sicherheitsprinzipalen Zugriff auf eine andere Ressource erteilen. In diesem Artikel wird gezeigt, wie Sie der verwalteten Identität eines virtuellen Azure-Computers oder einer VM-Skalierungsgruppe über das Azure-Portal den Zugriff auf ein Azure-Speicherkonto gewähren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#how-does-it-work)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Verwenden von RBAC zum Zuweisen des Zugriffs einer verwalteten Identität auf eine andere Ressource

Nachdem Sie die verwaltete Identität für eine Azure-Ressource aktiviert haben, zum Beispiel für einen [virtuellen Azure-Computer](qs-configure-portal-windows-vm.md) oder [Azure-VMSS](qs-configure-portal-windows-vmss.md), gehen Sie wie folgt vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, unter dem Sie die verwaltete Identität konfiguriert haben.

2. Navigieren Sie zu der gewünschten Ressource, für die Sie die Zugriffssteuerung ändern möchten. In diesem Beispiel wird einem virtuellen Azure-Computer Zugriff auf ein Speicherkonto gewährt. Aus diesem Grund navigieren wir zu dem Speicherkonto.

3. Wählen Sie die Seite **Zugriffssteuerung (IAM)** der Ressource aus, und wählen Sie dann **+ Rollenzuweisung hinzufügen** aus. Geben Sie dann die **Rolle**, **Zugriff zuweisen zu** und das entsprechende **Abonnement** an. Unter dem Bereich mit den Suchkriterien sollte die Ressource nun angezeigt werden. Wählen Sie die Ressource und anschließend **Speichern** aus. 

   ![Screenshot von „Zugriffssteuerung (IAM)“](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
- Informationen zum Aktivieren einer verwalteten Identität auf einem virtuellen Azure-Computer finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](qs-configure-portal-windows-vm.md).
- Informationen zum Aktivieren einer verwalteten Identität in einer Azure-VM-Skalierungsgruppe finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe über das Azure-Portal](qs-configure-portal-windows-vmss.md).


