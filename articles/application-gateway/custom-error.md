---
title: Erstellen von benutzerdefinierten Azure Application Gateway-Fehlerseiten
description: In diesem Artikel erfahren Sie, wie Sie benutzerdefinierte Application Gateway-Fehlerseiten erstellen. Sie können für eine benutzerdefinierte Fehlerseite Ihr eigenes Branding und Layout verwenden.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 5b34c559c8320961a2e96a663d88001400c572d3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397517"
---
# <a name="create-application-gateway-custom-error-pages"></a>Erstellen von benutzerdefinierten Application Gateway-Fehlerseiten

Mit Application Gateway können Sie benutzerdefinierte Fehlerseiten erstellen, anstatt Standardfehlerseiten anzuzeigen. Sie können für eine benutzerdefinierte Fehlerseite Ihr eigenes Branding und Layout verwenden.

Beispielsweise können Sie Ihre eigenen Wartungsseite definieren, wenn Ihre Webanwendung nicht erreichbar ist, oder eine Seite für nicht autorisierten Zugriff, wenn eine schädliche Anforderung an eine Webanwendung gesendet wird.

Benutzerdefinierte Fehlerseiten werden für die folgenden beiden Szenarien unterstützt:

- **Wartungsseite:** Diese benutzerdefinierte Fehlerseite wird anstelle der Seite „502 Ungültiges Gateway“ gesendet. Sie wird angezeigt, wenn Application Gateway kein Back-End zum Weiterleiten von Datenverkehr hat. Beispiele: Eine geplante Wartung steht an, oder ein unvorhergesehenes Problem beeinträchtigt den Zugriff auf den Back-End-Pool.
- **Seite für nicht autorisierten Zugriff:** Diese benutzerdefinierte Fehlerseite wird anstelle der Seite „403 Nicht autorisierter Zugriff“ gesendet. Sie wird angezeigt, wenn die Application Gateway-WAF schädlichen Datenverkehr erkennt und blockiert.

Wenn ein Fehler von den Back-End-Servern stammt, wird er unverändert zurück an den Aufrufer übergeben. Es wird keine benutzerdefinierte Fehlerseite angezeigt. Application Gateway kann eine benutzerdefinierte Fehlerseite anzeigen, wenn eine Anforderung das Back-End nicht erreichen kann.

Benutzerdefinierte Fehlerseiten können auf globaler Ebene und auf Listenerebene definiert werden:

- **Globale Ebene:** Die Fehlerseite gilt für den Datenverkehr für alle Webanwendungen, die auf dieser Application Gateway-Instanz bereitgestellt wurden.
- **Listenerebene:** Die Fehlerseite gilt für den Datenverkehr, der an diesem Listener empfangen wird.
- **Beide:** Die auf Listenerebene definierte benutzerdefinierte Fehlerseite hat höhere Priorität als die auf globaler Ebene festgelegte.

Zum Erstellen einer benutzerdefinierten Fehlerseite benötigen Sie Folgendes:

- Einen HTTP-Antwortstatuscode
- Den entsprechenden Standort für die Fehlerseite 
- Ein öffentlich zugängliches Azure Storage-Blob für den Standort
- Einen HTM- oder HTML-Erweiterungstyp 

Die Größe der Fehlerseite muss weniger als 1 MB betragen. Wenn auf der Fehlerseite Bilder verlinkt sind, muss es sich um öffentlich zugängliche absolute URLs oder um Base64-codierte Bilder auf der benutzerdefinierte Fehlerseite handeln. Relative Links mit Bildern am selben Blobspeicherort werden derzeit nicht unterstützt. 

Nachdem Sie eine Fehlerseite angeben, lädt Application Gateway sie vom Speicherort des Speicherblobs herunter und speichert sie im lokalen Application Gateway-Cache. Die Fehlerseite wird dann direkt aus Application Gateway bereitgestellt. Um eine vorhandene benutzerdefinierte Fehlerseite zu ändern, müssen Sie in der Application Gateway-Konfiguration auf einen anderen Blobspeicherort verweisen. Application Gateway überprüft nicht regelmäßig den Blobspeicherort zum Abrufen neuer Versionen.

## <a name="portal-configuration"></a>Portalkonfiguration

1. Navigieren Sie im Portal zu Application Gateway, und wählen Sie eine Application Gateway-Instanz aus.

    ![Screenshot, der die Seite „Übersicht“ für ein Anwendungsgateway zeigt.](media/custom-error/ag-overview.png)
2. Klicken Sie auf **Listener** , und navigieren Sie zu einem Listener, für den Sie eine Fehlerseite anzeigen möchten.

    ![Application Gateway-Listener](media/custom-error/ag-listener.png)
3. Konfigurieren Sie eine benutzerdefinierte Fehlerseite für einen 403-WAF-Fehler oder eine Seite „502 Wartung“ auf Listenerebene.

    > [!NOTE]
    > Das Erstellen von benutzerdefinierten Fehlerseiten auf globaler Ebene über das Azure-Portal wird derzeit nicht unterstützt.

4. Geben Sie eine öffentlich zugängliche Blob-URL für einen bestimmten Fehlerstatuscode an, und klicken Sie auf **Speichern**. Application Gateway ist jetzt mit der benutzerdefinierten Fehlerseite konfiguriert.

   ![Application Gateway-Fehlercodes](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell-Konfiguration

Sie können mithilfe von Azure PowerShell eine benutzerdefinierte Fehlerseite konfigurieren. Beispielsweise eine globale benutzerdefinierte Fehlerseite:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Oder eine Fehlerseite auf Listenerebene:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Weitere Informationen finden Sie unter [Add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) und [Add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Application Gateway-Diagnose finden Sie unter [Back-End-Integrität, Diagnoseprotokolle und Metriken für Application Gateway](application-gateway-diagnostics.md).