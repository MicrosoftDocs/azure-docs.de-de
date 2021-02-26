---
title: Sichern von API Management-Back-Ends über eine Clientzertifikatauthentifizierung
titleSuffix: Azure API Management
description: Erfahren Sie, wie Sie Clientzertifikate verwalten und Back-End-Dienste über eine Clientzertifikatauthentifizierung in Azure API Management sichern.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492829"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung in Azure API Management

API Management ermöglicht es ihnen, den Zugriff auf den Back-End-Dienst einer API mithilfe von Clientzertifikaten abzusichern. Diese Anleitung zeigt das Verwalten von Zertifikaten in einer Azure API Management-Dienstinstanz mithilfe des Azure-Portals. Darüber hinaus wird auch erklärt, wie eine API zur Verwendung eines Zertifikats zum Zugriff auf einen Back-End-Dienst konfiguriert wird.

Sie können API Management-Zertifikate auch mit der [API Management-REST-API](/rest/api/apimanagement/2020-06-01-preview/certificate)verwalten.

## <a name="certificate-options"></a>Zertifikatoptionen

API Management bietet zwei Optionen zum Verwalten von Zertifikaten, mit denen der Zugriff auf Back-End-Dienste gesichert wird:

* Verweisen auf ein in [Azure Key Vault](../key-vault/general/overview.md) verwaltetes Zertifikat 
* Direktes Hinzufügen einer Zertifikatsdatei in API Management

Die Verwendung von Schlüsseltresorgeheimnissen wird empfohlen, weil dadurch die Sicherheit von API Management verbessert wird:

* In Schlüsseltresoren gespeicherte Geheimnisse können für mehrere Dienste wiederverwendet werden.
* Auf in Schlüsseltresoren gespeicherte Zertifikate können präzise [Zugriffsrichtlinien](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) angewendet werden.
* Im Schlüsseltresor aktualisierte Zertifikate werden in API Management automatisch rotiert. Nach der Aktualisierung im Schlüsseltresor wird ein Zertifikat innerhalb von 4 Stunden in API Management aktualisiert. Sie können das Zertifikat auch manuell im Azure-Portal oder über die Verwaltungs-REST-API aktualisieren.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen unter [Erstellen einer API Management-Dienstinstanz][Create an API Management service instance].
* Sie sollten Ihren Back-End-Dienst für die Clientzertifikatauthentifizierung konfiguriert haben. Informationen zum Konfigurieren der Zertifikatauthentifizierung im Azure App Service finden Sie in [diesem Artikel][to configure certificate authentication in Azure WebSites refer to this article]. 
* Sie benötigen Zugriff auf das Zertifikat und die Kennwörter für die Verwaltung in einem Azure Key Vault oder zum Hochladen in den API Management-Dienst. Das Zertifikat muss im **PFX** -Format vorliegen. Selbstsignierte Zertifikate sind zulässig.

### <a name="prerequisites-for-key-vault-integration"></a>Voraussetzungen für die Key Vault-Integration

1. Schritte zum Erstellen eines Schlüsseltresors finden Sie unter [Schnellstart: Erstellen eines Schlüsseltresors über das Azure-Portal](../key-vault/general/quick-create-portal.md).
1. Aktivieren Sie eine system- oder benutzerseitig zugewiesene [verwaltete Identität](api-management-howto-use-managed-service-identity.md) in der API Management-Instanz.
1. Weisen Sie der verwalteten Identität [Key Vault-Zugriffsrichtlinien](../key-vault/general/assign-access-policy-portal.md) mit Berechtigungen zum Abrufen und Auflisten von Zertifikaten im Tresor zu. So fügen Sie die Richtlinie hinzu
    1. Navigieren Sie im Portal zu Ihrem Schlüsseltresor.
    1. Wählen Sie **Einstellungen > Zugriffsrichtlinien > + Zugriffsrichtlinie hinzufügen** aus.
    1. Wählen Sie **Zertifikatberechtigungen** und dann **Abrufen** und **Auflisten** aus.
    1. Wählen Sie unter **Prinzipal auswählen** den Ressourcennamen der verwalteten Identität aus. Wenn Sie eine systemseitig zugewiesene Identität verwenden, ist der Prinzipal der Name der API Management-Instanz.
1. Erstellen Sie ein Zertifikat in den Schlüsseltresor, oder importieren Sie eines. Weitere Informationen finden Sie unter [Schnellstart: Festlegen und Abrufen eines Zertifikats aus Azure Key Vault über das Azure-Portal](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Hinzufügen eines Schlüsseltresorzertifikats

Weitere Informationen finden Sie unter [Voraussetzungen für die Key Vault-Integration](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Wenn Sie in API Management ein Zertifikat aus einem Schlüsseltresor verwenden, dürfen Sie auf keinen Fall das Zertifikat, den Schlüsseltresor oder die verwaltete Identität löschen, die für den Zugriff auf den Schlüsseltresor verwendet wird.

So fügen Sie ein Schlüsseltresorzertifikat zu API Management hinzu

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie unter **Sicherheit** die Option **Zertifikate** aus.
1. Wählen Sie **Zertifikate** >  **+ Hinzufügen** aus.
1. Geben Sie in **Id** den gewünschten Namen ein.
1. Wählen Sie in **Zertifikat** die Option **Schlüsseltresor** aus.
1. Geben Sie den Bezeichner eines Schlüsseltresorzertifikats ein, oder wählen Sie **Auswählen** aus, um ein Zertifikat aus einem Schlüsseltresor auszuwählen.
    > [!IMPORTANT]
    > Wenn Sie selbst einen Bezeichner für ein Schlüsseltresorzertifikat eingeben, stellen Sie sicher, dass dieser keine Versionsinformationen enthält. Andernfalls wird das Zertifikat nach einer Aktualisierung im Schlüsseltresor nicht automatisch in API Management rotiert.
1. Wählen Sie unter **Clientidentität** eine systemseitig zugewiesene oder eine vorhandene benutzerseitig zugewiesene verwaltete Identität aus. Erfahren Sie, wie Sie [verwaltete Identitäten in Ihrem API Management-Dienst hinzufügen oder bearbeiten](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Die Identität benötigt Berechtigungen zum Abrufen und Auflisten von Zertifikaten im Schlüsseltresor. Wenn Sie den Zugriff auf den Schlüsseltresor noch nicht konfiguriert haben, werden Sie von API Management dazu aufgefordert. Der Dienst kann die Identität dann automatisch mit den erforderlichen Berechtigungen konfigurieren.
1. Wählen Sie **Hinzufügen**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Hinzufügen eines Schlüsseltresorzertifikats":::

## <a name="upload-a-certificate"></a>Hochladen eines Zertifikats

So laden Sie ein Schlüsseltresorzertifikat in API Management hoch 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie unter **Sicherheit** die Option **Zertifikate** aus.
1. Wählen Sie **Zertifikate** >  **+ Hinzufügen** aus.
1. Geben Sie in **Id** den gewünschten Namen ein.
1. Wählen Sie in **Zertifikat** die Option **Benutzerdefinierte** aus.
1. Durchsuchen Sie das Dateisystem, um die PFX-Zertifikatdatei auszuwählen, und geben Sie das zugehörige Kennwort ein.
1. Wählen Sie **Hinzufügen**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Hochladen des Clientzertifikats":::

Nachdem das Zertifikat hochgeladen wurde, wird es im Fenster **Zertifikate** angezeigt. Wenn Sie viele Zertifikate haben, notieren Sie sich den Fingerabdruck des gewünschten Zertifikats zum [Konfigurieren einer API zum Verwenden eines Clientzertifikats für die Gatewayauthentifizierung](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Wenn Sie beispielsweise bei der Verwendung eines selbstsignierten Zertifikats die Überprüfung der Zertifikatkette deaktivieren möchten, führen Sie die weiter unten in diesem Artikel unter [Selbstsignierte Zertifikate](#self-signed-certificates) beschriebenen Schritte aus.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Konfigurieren einer API zum Verwenden eines Clientzertifikats für die Gatewayauthentifizierung

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie unter **APIs** die Option **APIs** aus.
1. Wählen Sie eine API aus der Liste aus. 
2. Wählen Sie auf der Registerkarte **Entwurf** das Editorsymbol im Abschnitt **Back-End** aus.
3. Wählen Sie in **Gatewayanmeldeinformationen** die Option **Clientzertifikat** aus, und wählen Sie Ihr Zertifikat aus der Dropdownliste aus.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Verwenden eines Clientzertifikats für die Gatewayauthentifizierung":::

> [!CAUTION]
> Die Änderungen werden unmittelbar übernommen, und Funktionsaufrufe dieser API verwenden das Zertifikat für die Authentifizierung beim Back-End-Server.

> [!TIP]
> Wenn für den Back-End-Dienst einer API ein Zertifikat zur Gatewayauthentifizierung angegeben ist, wird es Teil der Richtlinie dieser API und kann im Richtlinien-Editor angezeigt werden.

## <a name="self-signed-certificates"></a>Selbstsignierte Zertifikate

Wenn Sie selbstsignierte Zertifikate verwenden, müssen Sie die Überprüfung der Zertifikatkette deaktivieren, damit API Management mit dem Back-End-System kommuniziert. Andernfalls wird der Fehlercode 500 zurückgegeben. Verwenden Sie zum Konfigurieren das PowerShell-Cmdlet [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (für ein neues Back-End) oder [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (für ein vorhandenes Back-End), und legen Sie den Parameter `-SkipCertificateChainValidation` auf `True` fest.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Löschen eines Clientzertifikats

Wenn Sie ein Zertifikat löschen möchten, wählen Sie es aus und dann im Kontextmenü ( **...** ) **Löschen**.

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Ein Zertifikat löschen":::

> [!IMPORTANT]
> Falls andere Richtlinien auf das Zertifikat verweisen, wird ein Warnbildschirm angezeigt. Um das Zertifikat zu löschen, müssen Sie es zunächst aus allen Richtlinien entfernen, die zu seiner Verwendung konfiguriert sind.

## <a name="next-steps"></a>Nächste Schritte

* [Sichern von APIs über eine Clientzertifikatauthentifizierung in API Management](api-management-howto-mutual-certificates-for-clients.md)
* Informationen zu [Richtlinien in API Management](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

