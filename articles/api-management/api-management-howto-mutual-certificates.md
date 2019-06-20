---
title: Sichern von Back-End-Diensten über die Clientzertifikatauthentifizierung – Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Back-End-Dienste über eine Clientzertifikatauthentifizierung in Azure API Management sichern.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 13a2eb080c6822a8a6786be1952bc588fa8afd80
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141558"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management

API Management ermöglicht, den Zugriff auf den Back-End-Dienst einer API mithilfe von Clientzertifikaten abzusichern. Diese Anleitung zeigt das Verwalten von Zertifikaten in der Azure API Management-Dienstinstanz im Azure-Portal. Darüber hinaus wird auch erklärt, wie eine API zur Verwendung eines Zertifikats zum Zugriff auf einen Back-End-Dienst konfiguriert wird.

Weitere Informationen zur Verwaltung von Zertifikaten mit der API Management-REST-API finden Sie unter <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management-REST-API-Zertifikatsentität</a>.

## <a name="prerequisites"> </a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Diese Anleitung beschreibt, wie Sie Ihre Instanz des API Management-Diensts konfigurieren, um für den Zugriff auf den Back-End-Dienst einer API die Clientzertifikatauthentifizierung zu verwenden. Bevor Sie die Schritte in diesem Artikel ausführen, sollte Ihr Back-End-Dienst für die Clientzertifikatauthentifizierung konfiguriert sein ([Informationen zum Konfigurieren der Zertifikatauthentifizierung in Azure Websites finden Sie in diesem Artikel][to configure certificate authentication in Azure WebSites refer to this article]). Sie benötigen Zugriff auf das Zertifikat und das Kennwort zum Hochladen in den API Management-Dienst.

## <a name="step1"></a>Hochladen eines Clientzertifikats

![Hinzufügen von Clientzertifikaten](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Gehen Sie folgendermaßen vor, um ein neues Clientzertifikat hochzuladen. Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Tutorial [Erstellen einer API Management-Dienstinstanz][Create an API Management service instance].

1. Navigieren Sie im Azure-Portal zu Ihrer Azure API Management-Dienstinstanz.
2. Wählen Sie **Clientzertifikate** im Menü aus.
3. Klicken Sie auf die Schaltfläche **+ Hinzufügen**.  
    ![Hinzufügen von Clientzertifikaten](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Suchen Sie nach dem Zertifikat, und geben Sie dessen ID und Kennwort an.  
5. Klicken Sie auf **Create**.

> [!NOTE]
> Das Zertifikat muss im **.pfx** -Format vorliegen. Selbstsignierte Zertifikate sind zulässig.

Sobald das Zertifikat hochgeladen ist, wird es auf der Registerkarte **Clientzertifikate** angezeigt.  Wenn Sie viele Zertifikate haben, notieren Sie sich den Fingerabdruck des gewünschten Zertifikats zum [Konfigurieren einer API zum Verwenden eines Clientzertifikats für die Gatewayauthentifizierung][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Wenn Sie beispielsweise bei der Verwendung eines selbstsignierten Zertifikats die Überprüfung der Zertifikatkette deaktivieren möchten, führen Sie die Schritte in [diesem Punkt](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end) der häufig gestellten Fragen aus.

## <a name="step1a"></a>Löschen eines Clientzertifikats

Um ein Zertifikat zu löschen, klicken Sie auf das Kontextmenü **...** , und wählen Sie neben dem betreffenden Zertifikat **Löschen** aus.

![Löschen von Clientzertifikaten](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Falls das Zertifikat von einer API verwendet wird, wird ein Warnbildschirm angezeigt. Um das Zertifikat zu löschen, müssen Sie es zunächst aus allen APIs entfernen, die zu seiner Verwendung konfiguriert sind.

![Fehler beim Löschen von Clientzertifikaten](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"></a>Konfigurieren einer API zum Verwenden eines Clientzertifikats für die Gatewayauthentifizierung

1. Klicken Sie auf der linken Seite im Menü **API Management** auf **APIs**, und navigieren Sie zur API.  
    ![Aktivieren von Clientzertifikaten](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Klicken Sie in der Registerkarte **Entwurf** auf ein Stiftsymbol des Abschnitts **Back-End**. 
3. Ändern Sie die **Gateway-Anmeldeinformationen** in **Clientzertifikat**, und wählen Sie das Zertifikat aus der Dropdownliste aus.  
    ![Aktivieren von Clientzertifikaten](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klicken Sie auf **Speichern**. 

> [!WARNING]
> Die Änderungen werden unmittelbar übernommen, und Funktionsaufrufe dieser API verwenden das Zertifikat für die Authentifizierung beim Back-End-Server.


> [!TIP]
> Wenn für den Back-End-Dienst einer API ein Zertifikat zur Gateway-authentifizierung angegeben ist, wird es Teil der Richtlinie dieser API und kann im Richtlinien-Editor angezeigt werden.

## <a name="self-signed-certificates"></a>Selbstsignierte Zertifikate

Wenn Sie selbstsignierte Zertifikate verwenden, müssen Sie die Überprüfung der Zertifikatkette deaktivieren, damit API Management mit dem Back-End-System kommuniziert. Andernfalls wird der Fehlercode 500 zurückgegeben. Verwenden Sie zum Konfigurieren das PowerShell-Cmdlet [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (für ein neues Back-End) oder [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (für ein vorhandenes Back-End), und legen Sie den Parameter `-SkipCertificateChainValidation` auf `True` fest.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
