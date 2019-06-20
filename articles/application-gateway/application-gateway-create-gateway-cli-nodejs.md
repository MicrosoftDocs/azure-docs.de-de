---
title: Erstellen eines Azure Application Gateways – klassische Azure CLI
description: Erfahren Sie, wie Sie ein Anwendungsgateway mit der klassischen Azure CLI in Resource Manager erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095981"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Erstellen eines Anwendungsgateways mithilfe der Azure-CLI

Azure Application Gateway verwendet einen Load Balancer auf der Schicht 7 (Anwendungsschicht). Das Application Gateway ermöglicht ein Failover sowie schnelles Routing von HTTP-Anforderungen zwischen verschiedenen Servern in der Cloud und der lokalen Umgebung. Application Gateway umfasst derzeit die folgenden Anwendungsbereitstellungsfunktionen: HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität, SSL-Auslagerung (Secure Sockets Layer), benutzerdefinierte Integritätstests und Unterstützung für mehrere Websites.

## <a name="prerequisite-install-the-azure-cli"></a>Voraussetzung: Installieren der Azure CLI

Um die Schritte in diesem Artikel ausführen zu können, müssen Sie [die Azure CLI installieren](../xplat-cli-install.md) und sich bei [Azure anmelden](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Wenn Sie über kein Azure-Konto verfügen, müssen Sie eines erstellen. Sie können sich [hier](../active-directory/fundamentals/sign-up-organization.md)für eine kostenlose Testversion registrieren.

## <a name="scenario"></a>Szenario

In diesem Szenario erfahren Sie, wie Sie ein Anwendungsgateway über das Azure-Portal erstellen.

Dieses Szenario umfasst Folgendes:

* Sie erstellen ein Anwendungsgateway für mittlere Nutzungsdauer mit zwei Instanzen.
* Sie erstellen ein virtuelles Netzwerks mit dem Namen „ContosoVNET“ mit dem reservierten CIDR-Block 10.0.0.0/16.
* Sie erstellen ein Subnetz mit dem Namen Subnet01, für das 10.0.0.0/28 als CIDR-Block verwendet wird.

> [!NOTE]
> Zusätzliche Konfigurationsschritte für das Anwendungsgateway (u.a. benutzerdefinierte Integritätstests, Back-End-Pool-Adressen und zusätzlichen Regeln) werden nicht während der Erstbereitstellung, sondern nach der Konfiguration des Anwendungsgateways ausgeführt.

## <a name="before-you-begin"></a>Voraussetzungen

Für Azure Application Gateway ist ein eigenes Subnetz erforderlich. Stellen Sie beim Erstellen eines virtuellen Netzwerks sicher, dass der Adressbereich für mehrere Subnetze ausreicht. Sobald Sie ein Anwendungsgateway in einem Subnetz bereitstellen, können nur zusätzliche Anwendungsgateways zum Subnetz hinzugefügt werden.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Öffnen Sie die **Microsoft Azure-Eingabeaufforderung**, und melden Sie sich an.

```azurecli-interactive
az login
```

Wenn Sie das vorherige Beispiel eingeben, erhalten Sie einen Code. Navigieren Sie in einem Browser zu „https://aka.ms/devicelogin“, und setzen Sie den Anmeldeprozess fort.

![CMD mit Geräteanmeldung][1]

Geben Sie im Browser den Code ein, den Sie erhalten haben. Sie werden zu einer Anmeldeseite umgeleitet.

![Browser zur Eingabe des Codes][2]

Wenn Sie den Code eingegeben haben, sind Sie angemeldet. Schließen Sie dann den Browser, um mit dem Szenario fortzufahren.

![Erfolgreich angemeldet][3]

## <a name="switch-to-resource-manager-mode"></a>Wechseln in den Ressource Manager-Modus

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Vor dem Erstellen des Anwendungsgateways wird eine Ressourcengruppe erstellt, die das Anwendungsgateway enthält. Hier sehen Sie den Befehl:

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Sobald die Ressourcengruppe erstellt wurde, wird ein virtuelles Netzwerk für das Anwendungsgateway erstellt.  Im folgenden Beispiel lautet der Adressraum 10.0.0.0/16, wie in den obigen Szenariohinweisen angegeben.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Erstellen eines Subnetzes

Nach der Erstellung des virtuellen Netzwerks wird ein Subnetz für das Anwendungsgateway hinzugefügt.  Wenn Sie ein Anwendungsgateway mit einer Web-App verwenden, die im selben virtuellen Netzwerk wie das Anwendungsgateway gehostet wird, lassen Sie ausreichend Platz für ein weiteres Subnetz.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Sobald das virtuelle Netzwerk und das Subnetz erstellt wurden, sind die Voraussetzungen für das Anwendungsgateway erfüllt. Für den folgenden Schritt sind darüber hinaus ein zuvor exportiertes PFX-Zertifikat und das Kennwort für das Zertifikat erforderlich: Die für den Back-End verwendeten IP-Adressen sind die IP-Adressen für den Back-End-Server. Diese Werte können entweder private IPs im virtuellen Netzwerk, öffentliche IPs oder vollqualifizierte Domänennamen für Ihre Back-End-Server sein.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Um eine Liste mit Parametern zu erhalten, die während der Erstellung bereitgestellt werden kann, führen Sie den folgenden Befehl aus: **azure network application-gateway create --help**.

Mit diesem Beispiel wird ein einfaches Anwendungsgateway mit Standardeinstellungen für Listener, Back-End-Pool, Back-End-HTTP-Einstellungen und Regeln erstellt. Nach der erfolgreichen Bereitstellung können Sie diese Einstellungen an Ihre Anforderungen anpassen.
Wenn Sie Ihre Webanwendung bereits mit dem Back-End-Pool in den vorherigen Schritten definiert haben, beginnt nach dem Erstellen der Lastenausgleich.

## <a name="next-steps"></a>Nächste Schritte

Unter [Erstellen eines benutzerdefinierten Integritätstests](application-gateway-create-probe-portal.md)

Unter [Konfigurieren der SSL-Auslagerung](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
