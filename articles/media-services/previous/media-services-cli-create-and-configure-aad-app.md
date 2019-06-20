---
title: Erstellen einer Azure AD-App mithilfe der Azure CLI und Konfigurieren ihres Zugriffs auf die Azure Media Services-API | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie mithilfe der Azure CLI eine Azure AD-App erstellt und ihr Zugriff auf die Azure Media Services-API konfiguriert wird.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e7ae5f83ff9dbb16733656a3bb4452ace750cf3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64690118"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Erstellen einer Azure AD-App mithilfe der Azure-Befehlszeilenschnittstelle und Konfigurieren ihres Zugriffs auf die Media Services-API 

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure CLI eine Azure AD-Anwendung (Azure Active Directory) und einen Dienstprinzipal erstellen, um auf Azure Media Services-Ressourcen zuzugreifen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 
- Media Services-Konto. Weitere Informationen finden Sie unter [Erstellen eines Azure Media Services-Kontos mithilfe des Azure-Portals](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Verwenden der Azure Cloud Shell

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Starten Sie die Cloud Shell im oberen Navigationsbereich des Portals.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Weitere Informationen finden Sie in der [Übersicht über Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Erstellen einer Azure AD-App mithilfe der Azure CLI und Konfigurieren ihres Zugriffs auf das Media-Konto
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Beispiel:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

In diesem Beispiel ist der **scope** der vollständige Ressourcenpfad für das Media Services-Konto. **scope** kann jedoch auf eine beliebige Ebene festgelegt sein.

So kann beispielsweise eine der folgenden Ebenen festgelegt sein:
 
* Die **Abonnement**-Ebene.
* Die **Ressourcengruppen**-Ebene.
* Die **Ressourcen**-Ebene (z.B. ein Media-Konto).

Weitere Informationen finden Sie unter [Erstellen eines Azure-Dienstprinzipals mit der Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Siehe auch [Verwalten der rollenbasierten Zugriffssteuerung mit der Azure-Befehlszeilenschnittstelle](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit dem [Hochladen von Dateien in Ihr Konto](media-services-portal-upload-files.md).
