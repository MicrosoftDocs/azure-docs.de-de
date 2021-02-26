---
title: 'Azure Spring Cloud: Zugreifen auf eine App im virtuellen Netzwerk'
description: Zugreifen auf eine App in einer Azure Spring Cloud-Instanz im virtuellen Netzwerk.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576530"
---
# <a name="access-your-application-in-a-private-network"></a>Zugreifen auf eine Anwendung im privaten Netzwerk

In diesem Dokument wird erläutert, wie Sie in einem privaten Netzwerk auf einen Endpunkt für Ihre Anwendung zugreifen.  Um Zugriff zu erhalten, müssen Sie eine **private Azure DNS-Zone** in Ihrem Abonnement erstellen, um den privaten vollständig qualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) in seine IP-Adresse zu übersetzen bzw. aufzulösen.

Wenn **Endpunkt zuweisen** für Anwendungen in einer Azure Spring Cloud-Dienstinstanz in Ihrem virtuellen Netzwerk bereitgestellt wird, ist der Endpunkt ein privater FQDN. Die Domäne ist nur im privaten Netzwerk zugänglich. Apps und Dienste verwenden den Anwendungsendpunkt. Sie beinhalten den unter [Anzeigen von Apps und Bereitstellungen](spring-cloud-howto-staging-environment.md#view-apps-and-deployments) erläuterten **Testendpunkt**. **Protokollstreaming**, das unter [Streamen von Azure Spring Cloud-App-Protokollen in Echtzeit](spring-cloud-howto-log-streaming.md) beschrieben wird, funktioniert auch nur innerhalb des privaten Netzwerks.

## <a name="create-a-private-dns-zone"></a>Erstellen einer privaten DNS-Zone

Mit dem folgenden Verfahren wird eine private DNS-Zone für eine Anwendung im privaten Netzwerk erstellt.

1. Öffnen Sie das Azure-Portal. Suchen Sie im oberen Suchfeld nach **Private DNS-Zonen**, und wählen Sie im Ergebnis **Private DNS-Zonen** aus.

2. Wählen Sie auf der Seite **Private DNS-Zonen** die Option **+ Hinzufügen** aus.

3. Füllen Sie das Formular auf der Seite **Private DNS-Zone erstellen** aus. Geben Sie **<span>private.azuremicroservices.io</span>** als **Namen** der Zone ein.

4. Klicken Sie auf **Überprüfen + erstellen**.

5. Klicken Sie auf **Erstellen**.

Die Erstellung der Zone kann einige Minuten dauern.

## <a name="link-the-virtual-network"></a>Verknüpfen des virtuellen Networks

Um die private DNS-Zone mit dem virtuellen Netzwerk zu verknüpfen, müssen Sie eine virtuelle Netzwerkverknüpfung erstellen.

1. Wählen Sie die oben erstellte private DNS-Zonenressource aus: **<span>private.azuremicroservices.io</span>** . 

2. Wählen Sie im linken Bereich die Option **Virtual network links** (Virtuelle Netzwerkverknüpfungen) aus.

3. Wählen Sie **Hinzufügen**.

4. Geben Sie **azure-spring-cloud-dns-link** für den **Linknamen** ein.

5. Wählen Sie für **Virtuelles Netzwerk** das virtuelle Netzwerk aus, das Sie wie unter [Bereitstellen von Azure Spring Cloud in einem virtuellen Azure-Netzwerk (VNET-Injektion)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md) beschrieben erstellt haben.

    ![Hinzufügen einer virtuellen Netzwerkverknüpfung](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Klicken Sie auf **OK**.

## <a name="create-dns-record"></a>Erstellen eines DNS-Eintrags

Um die private DNS-Zone zum Übersetzen/Auflösen von DNS zu verwenden, müssen Sie einen „A“-Typdatensatz in der Zone erstellen.

1. Wählen Sie die virtuelle Netzwerkressource aus, die Sie wie unter [Bereitstellen von Azure Spring Cloud in einem virtuellen Azure-Netzwerk (VNET-Injektion)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md) beschrieben erstellt haben.

2. Geben Sie im Suchfeld **Verbundene Geräte** den Wert *kubernetes-internal* ein.

3. Suchen Sie im gefilterten Ergebnis das **Gerät**, das mit der Dienstruntime **Subnetz** der Dienstinstanz verbunden ist, und kopieren Sie dessen **IP-Adresse**. In diesem Beispiel lautet die IP-Adresse *10.1.0.7*.

    [ ![Erstellen eines DNS-Ressourceneintrags](media/spring-cloud-access-app-vnet/create-dns-record.png) ](media/spring-cloud-access-app-vnet/create-dns-record.png)

Alternativ können Sie die IP-Adresse mithilfe des folgenden Azure CLI-Befehls abrufen:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Wählen Sie die oben erstellte private DNS-Zonenressource aus: **<span>private.azuremicroservices.io</span>** .

5. Klicken Sie auf **+ Datensatzgruppe**.

6. Geben Sie in **Datensatzgruppe hinzufügen** diese Informationen ein, oder wählen Sie sie aus:

    |Einstellung     |Wert                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Name        |Eingeben von *\**                                                                 |
    |type        |**A** auswählen                                                               |
    |TTL         |*1* eingeben                                                                  |
    |TTL-Einheit    |**Stunden** auswählen                                                           |
    |IP-Adresse  |Geben Sie die in Schritt 3 kopierte IP-Adresse ein. Geben Sie im Beispiel *10.1.0.7* ein.    |

    Klicken Sie anschließend auf **OK**.

    ![Hinzufügen eines privaten DNS-Zoneneintrags](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Zuweisen eines privaten FQDN für Ihre Anwendung

Nachdem Sie das Verfahren in [Tutorial: Bereitstellen von Azure Spring Cloud in einem virtuellen Netzwerk](spring-cloud-tutorial-deploy-in-azure-virtual-network.md) ausgeführt haben, können Sie einen privaten FQDN für Ihre Anwendung zuweisen.

1. Wählen Sie die Azure Spring Cloud-Dienstinstanz aus, die in Ihrem virtuellen Netzwerk bereitgestellt wird, und öffnen Sie im Menü auf der linken Seite die Registerkarte **Apps**.

2. Wählen Sie die Anwendung aus, um die Seite **Übersicht** anzuzeigen.

3. Wählen Sie **Endpunkt zuweisen** aus, um Ihrer Anwendung einen privaten FQDN zuzuweisen. Dies kann einige Minuten dauern.

    ![Zuweisen eines privaten Endpunkts](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. Der zugewiesene private FQDN (mit der Bezeichnung **URL**) ist jetzt verfügbar. Es kann nur innerhalb des privaten Netzwerks, aber nicht über das Internet darauf zugegriffen werden.

## <a name="access-application-private-fqdn"></a>Zugreifen auf den privaten FQDN der Anwendung

Nach der Zuweisung können Sie auf den privaten FQDN Ihrer Anwendung im privaten Netzwerk zugreifen. Sie können z. B. einen Jumpbox-Computer im gleichen virtuellen Netzwerk oder einem mittels Peering verknüpften virtuellen Netzwerk erstellen, und auf diesem Jumpbox-Computer ist der private FQDN zugänglich.

![Zugreifen auf den privaten Endpunkt im VNET](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Nächste Schritte

- [Verfügbarmachen von Anwendungen im Internet mithilfe von Application Gateway und Azure Firewall](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Weitere Informationen

- [Problembehandlung für Azure Spring Cloud im VNET](spring-cloud-troubleshooting-vnet.md)
- [Kundenzuständigkeiten für die Ausführung von Azure Spring Cloud im VNET](spring-cloud-vnet-customer-responsibilities.md)