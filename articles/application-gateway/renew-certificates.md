---
title: Verlängern des Azure Application Gateway-Zertifikats
description: Es wird beschrieben, wie Sie ein Zertifikat verlängern, das einem Anwendungsgateway-Listener zugeordnet ist.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
origin.date: 08/15/2018
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: 90200f7be6c71346441922365fc4439111dd8701
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133691"
---
# <a name="renew-application-gateway-certificates"></a>Verlängern von Application Gateway-Zertifikaten

Sie müssen Ihre Zertifikate nach einiger Zeit verlängern, falls sie für Ihr Anwendungsgateway die SSL-Verschlüsselung konfiguriert haben.

Sie können ein Zertifikat, das einem Listener zugeordnet ist, verlängern, indem Sie entweder das Azure-Portal, Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden:

## <a name="azure-portal"></a>Azure-Portal

Navigieren Sie zu Ihren Anwendungsgateway-Listenern, um ein Listenerzertifikat über das Portal zu verlängern. Klicken Sie auf den Listener, der über ein zu verlängerndes Zertifikat verfügt, und klicken Sie anschließend auf **Ausgewähltes Zertifikat erneuern oder bearbeiten**.

![Verlängern des Zertifikats](./media/renew-certificate/ssl-cert.png)

Laden Sie Ihr neues PFX-Zertifikat hoch, geben Sie ihm einen Namen, geben Sie das Kennwort ein, und klicken Sie dann auf **Speichern**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verwenden Sie das folgende Skript, um Ihr Zertifikat mit Azure PowerShell zu verlängern:

```azurepowershell
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az network application-gateway ssl-cert update `
  -n "<CertName>" `
  --gateway-name "<AppGatewayName>" `
  -g "ResourceGroupName>" `
  --cert-file <PathToCerFile> `
  --cert-password "<password>"
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren der SSL-Auslagerung mit Azure Application Gateway finden Sie unter [Konfigurieren der SSL-Auslagerung](application-gateway-ssl-portal.md).

<!-- Update_Description: code update -->