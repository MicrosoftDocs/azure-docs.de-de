---
title: 'Erhöhen des Endpunktkontingents: LUIS'
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Service (LUIS) bietet die Möglichkeit, das Endpunkt-Anforderungskontingent über das Kontingent eines einzelnen Schlüssels hinaus zu erhöhen. Dies erfolgt, indem Sie mehrere Schlüssel für LUIS erstellen und diese der LUIS-Anwendung auf der Seite **Veröffentlichen** im Abschnitt **Resources and Keys** (Ressourcen und Schlüssel) hinzuzufügen.
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 6fc5bea71909d0e17b4ef0256ab0cad644dacbb3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95993821"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Verwenden von Microsoft Azure Traffic Manager zum Verwalten von Endpunktkontingenten über mehrere Schlüssel
Language Understanding Intelligent Service (LUIS) bietet die Möglichkeit, das Endpunkt-Anforderungskontingent über das Kontingent eines einzelnen Schlüssels hinaus zu erhöhen. Dies erfolgt, indem Sie mehrere Schlüssel für LUIS erstellen und diese der LUIS-Anwendung auf der Seite **Veröffentlichen** im Abschnitt **Resources and Keys** (Ressourcen und Schlüssel) hinzuzufügen.

Die Clientanwendung muss den Datenverkehr schlüsselübergreifend verwalten. Dies wird nicht von LUIS übernommen.

In diesem Artikel wird erläutert, wie Sie den Datenverkehr schlüsselübergreifend mit Azure [Traffic Manager][traffic-manager-marketing] verwalten. Sie müssen bereits über eine trainierte und veröffentlichte LUIS-App verfügen. Wenn Sie keine haben, befolgen Sie den [Schnellstart](luis-get-started-create-app.md) für vordefinierte Domänen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Herstellen einer Verbindung mit PowerShell im Azure-Portal
Öffnen Sie im [Azure][azure-portal]-Portal das PowerShell-Fenster. Das Symbol für das PowerShell-Fenster ist **>_** auf der oberen Navigationsleiste. Indem Sie PowerShell über das Portal verwenden, erhalten Sie die neueste PowerShell-Version und sind automatisch authentifiziert. PowerShell im Portal erfordert ein [Azure Storage](https://azure.microsoft.com/services/storage/)-Konto.

![Screenshot des Azure-Portals mit geöffnetem PowerShell-Fenster](./media/traffic-manager/azure-portal-powershell.png)

In den folgenden Abschnitten werden [PowerShell-Cmdlets für Traffic Manager](/powershell/module/az.trafficmanager/#traffic_manager) verwendet.

## <a name="create-azure-resource-group-with-powershell"></a>Erstellen einer Azure-Ressourcengruppe mit PowerShell
Erstellen Sie vor dem Erstellen der Azure-Ressourcen eine Ressourcengruppe, die alle Ressourcen enthalten soll. Geben Sie der Ressourcengruppe den Namen `luis-traffic-manager`, und verwenden Sie die Region `West US`. In der Region der Ressourcengruppe werden Metadaten zur Gruppe gespeichert. Ihre Ressourcen werden nicht verlangsamt, wenn sie sich in einer anderen Region befinden.

Erstellen Sie mit dem Cmdlet **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** eine Ressourcengruppe:

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Erstellen von LUIS-Schlüsseln zum Erhöhen des Endpunkt-Gesamtkontingents
1. Erstellen Sie im Azure-Portal zwei **Language Understanding Intelligent Service**-Schlüssel, einen in `West US` und einen in `East US`. Verwenden Sie die vorhandene Ressourcengruppe, die im vorherigen Abschnitt mit dem Namen `luis-traffic-manager` erstellt wurde.

    ![Screenshot des Azure-Portals mit zwei LUIS-Schlüsseln in der Ressourcengruppe luis-traffic-manager](./media/traffic-manager/luis-keys.png)

2. Weisen Sie der App auf der [LUIS][LUIS]-Website auf der Seite **Azure-Ressourcen** im Abschnitt **Verwalten** Schlüssel zu, und veröffentlichen Sie die App erneut, indem Sie im Menü rechts oben die Schaltfläche **Veröffentlichen** auswählen.

    In der Beispiel-URL in der Spalte **Endpunkt** wird eine GET-Anforderung mit dem Endpunktschlüssel als Abfrageparameter verwendet. Kopieren Sie die Endpunkt-URLs der beiden neuen Schlüssel. Sie werden im Rahmen der Traffic Manager-Konfiguration weiter unten in diesem Artikel verwendet.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Verwalten von schlüsselübergreifenden LUIS-Endpunktanforderungen mit Traffic Manager
Traffic Manager erstellt einen neuen DNS-Zugriffspunkt für Ihre Endpunkte. Dieser dient nicht als Gateway oder Proxy, sondern wird ausschließlich auf DNS-Ebene verwendet. In diesem Beispiel werden keine DNS-Einträge geändert. Es wird eine DNS-Bibliothek für die Kommunikation mit Traffic Manager verwendet, um den richtigen Endpunkt für diese spezifische Anforderung abzurufen. _Jede_ für LUIS vorgesehene Anforderung erfordert zuerst eine Traffic Manager-Anforderung, um zu ermitteln, welcher LUIS-Endpunkt verwendet werden soll.

### <a name="polling-uses-luis-endpoint"></a>Abrufen mit LUIS-Endpunkt
Traffic Manager fragt die Endpunkte in regelmäßigen Abständen ab, um sicherzustellen, dass der Endpunkt weiterhin verfügbar ist. Die abgefragte Traffic Manager-URL muss mit einer GET-Anforderung zugänglich sein und 200 zurückgeben. Dies trifft auf die Endpunkt-URL auf der Seite **Veröffentlichen** zu. Da jeder Endpunktschlüssel eine andere Route und andere Abfragezeichenfolgen-Parameter aufweist, ist für jeden Endpunktschlüssel ein anderer Abrufpfad erforderlich. Bei jedem Abfragevorgang durch Traffic Manager wird eine Anforderung aus dem Kontingent verrechnet. Der Abfragezeichenfolgen-Parameter **q** des LUIS-Endpunkts ist die an LUIS gesendete Äußerung. Anstatt eine Äußerung zu senden, wird dieser Parameter verwendet, um dem LUIS-Endpunkt Traffic Manager-Abfragen hinzuzufügen. Dies kann beim Konfigurieren von Traffic Manager als Debugtechnik genutzt werden.

Da jeder LUIS-Endpunkt einen eigenen Pfad benötigt, ist auch ein eigenes Traffic Manager-Profil erforderlich. Erstellen Sie für die profilübergreifende Verwaltung eine [_geschachtelte_ Traffic Manager](../../traffic-manager/traffic-manager-nested-profiles.md)-Architektur. Ein übergeordnetes Profil verweist auf die untergeordneten Profile und verwaltet den Datenverkehr zwischen ihnen.

Denken Sie daran, nach dem Konfigurieren von Traffic Manager den Pfad zu ändern, sodass er den Abfragezeichenfolgen-Parameter „logging=false“ verwendet und Ihr Protokoll nicht durch Abfragen gefüllt wird.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurieren von Traffic Manager mit geschachtelten Profilen
In den folgenden Abschnitten werden zwei untergeordnete Profile erstellt, eines für den LUIS-Schlüssel für „USA, Osten“ und eines für den LUIS-Schlüssel für „USA, Westen“. Dann wird ein übergeordnetes Profil erstellt, und die beiden untergeordneten Profile werden dem übergeordneten Profil hinzugefügt.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Erstellen des Traffic Manager-Profils für „USA, Osten“ mit PowerShell
Das Erstellen des Traffic Manager-Profils für „USA, Osten“ umfasst mehrere Schritte: Erstellen des Profils, Hinzufügen des Endpunkts und Festlegen des Endpunkts. Ein Traffic Manager-Profil kann viele Endpunkte aufweisen, jeder Endpunkt hat jedoch denselben Überprüfungspfad. Da sich die LUIS-Endpunkt-URLs für die Abonnements für „USA, Osten“ und „USA, Westen“ aufgrund der Region und des Endpunktschlüssels unterscheiden, muss jeder LUIS-Endpunkt ein einzelner Endpunkt im Profil sein.

1. Erstellen des Profils mit dem Cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Verwenden Sie das folgende Cmdlet, um das Profil zu erstellen. Achten Sie darauf, `appIdLuis` und `subscriptionKeyLuis` zu ändern. Der subscriptionKey gilt für den LUIS-Schlüssel für „USA, Osten“. Wenn der Pfad, einschließlich der LUIS-App-ID und des Endpunktschlüssels, nicht korrekt ist, gibt der Traffic Manager-Abruf den Status `degraded` zurück, da der Traffic Manager den LUIS-Endpunkt nicht erfolgreich anfordern kann. Stellen Sie sicher, dass `q` den Wert `traffic-manager-east` aufweist, damit Sie diesen Wert in den LUIS-Endpunktprotokollen sehen können.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    In dieser Tabelle wird jede Variable im Cmdlet erläutert:

    |Konfigurationsparameter|Variablenname oder -wert|Zweck|
    |--|--|--|
    |-Name|luis-profile-eastus|Traffic Manager-Name im Azure-Portal|
    |-ResourceGroupName|luis-traffic-manager|Im vorherigen Abschnitt erstellt|
    |-TrafficRoutingMethod|Leistung|Weitere Informationen finden Sie unter [Traffic Manager-Methoden für das Datenverkehrsrouting][routing-methods]. Beim Verwenden von Leistungsfunktionen muss die URL-Anforderung an Traffic Manager aus der Region des Benutzers stammen. Wenn die Anforderung über einen Chatbot oder eine andere Anwendung erfolgt, muss diese die Region im Aufruf von Traffic Manager imitieren. |
    |-RelativeDnsName|luis-dns-eastus|Dies ist die Unterdomäne für den Dienst: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Abrufintervall, 30 Sekunden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Der Port und das Protokoll für LUIS lauten HTTPS und 443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Ersetzen Sie `<appIdLuis>` und `<subscriptionKeyLuis>` durch Ihre eigenen Werte.|

    Bei einer erfolgreichen Anforderung erfolgt keine Antwort.

2. Hinzufügen des Endpunkts für „USA, Osten“ mit dem Cmdlet **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    In dieser Tabelle wird jede Variable im Cmdlet erläutert:

    |Konfigurationsparameter|Variablenname oder -wert|Zweck|
    |--|--|--|
    |-EndpointName|luis-east-endpoint|Unter dem Profil angezeigter Endpunktname|
    |-TrafficManagerProfile|$eastprofile|Verwenden Sie das in Schritt 1 erstellte Profilobjekt|
    |-Type|ExternalEndpoints|Weitere Informationen finden Sie unter [Traffic Manager-Endpunkt][traffic-manager-endpoints]. |
    |-Target|eastus.api.cognitive.microsoft.com|Dies ist die Domäne für den LUIS-Endpunkt.|
    |-EndpointLocation|"eastus"|Region des Endpunkts|
    |-EndpointStatus|Enabled|Endpunkt beim Erstellen aktivieren|

    Eine erfolgreiche Antwort sieht wie folgt aus:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Festlegen des Endpunkts für „USA, Osten“ mit dem Cmdlet **[Set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Eine erfolgreiche Antwort ist die gleiche Antwort wie in Schritt 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Erstellen des Traffic Manager-Profils für „USA, Westen“ mit PowerShell
Das Erstellen des Traffic Manager-Profils für „USA, Westen“ umfasst die gleichen Schritte: Erstellen des Profils, Hinzufügen des Endpunkts und Festlegen des Endpunkts.

1. Erstellen des Profils mit dem Cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Verwenden Sie das folgende Cmdlet, um das Profil zu erstellen. Achten Sie darauf, `appIdLuis` und `subscriptionKeyLuis` zu ändern. Der subscriptionKey gilt für den LUIS-Schlüssel für „USA, Osten“. Wenn der Pfad, einschließlich der LUIS-App-ID und des Endpunktschlüssels, nicht korrekt ist, gibt der Traffic Manager-Abruf den Status `degraded` zurück, da der Traffic Manager den LUIS-Endpunkt nicht erfolgreich anfordern kann. Stellen Sie sicher, dass `q` den Wert `traffic-manager-west` aufweist, damit Sie diesen Wert in den LUIS-Endpunktprotokollen sehen können.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    In dieser Tabelle wird jede Variable im Cmdlet erläutert:

    |Konfigurationsparameter|Variablenname oder -wert|Zweck|
    |--|--|--|
    |-Name|luis-profile-westus|Traffic Manager-Name im Azure-Portal|
    |-ResourceGroupName|luis-traffic-manager|Im vorherigen Abschnitt erstellt|
    |-TrafficRoutingMethod|Leistung|Weitere Informationen finden Sie unter [Traffic Manager-Methoden für das Datenverkehrsrouting][routing-methods]. Beim Verwenden von Leistungsfunktionen muss die URL-Anforderung an Traffic Manager aus der Region des Benutzers stammen. Wenn die Anforderung über einen Chatbot oder eine andere Anwendung erfolgt, muss diese die Region im Aufruf von Traffic Manager imitieren. |
    |-RelativeDnsName|luis-dns-westus|Dies ist die Unterdomäne für den Dienst: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Abrufintervall, 30 Sekunden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Der Port und das Protokoll für LUIS lauten HTTPS und 443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Ersetzen Sie `<appId>` und `<subscriptionKey>` durch Ihre eigenen Werte. Denken Sie daran, dass sich dieser Endpunktschlüssel vom Endpunktschlüssel für „USA, Osten“ unterscheidet.|

    Bei einer erfolgreichen Anforderung erfolgt keine Antwort.

2. Hinzufügen des Endpunkts für „USA, Westen“ mit dem Cmdlet **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    In dieser Tabelle wird jede Variable im Cmdlet erläutert:

    |Konfigurationsparameter|Variablenname oder -wert|Zweck|
    |--|--|--|
    |-EndpointName|luis-west-endpoint|Unter dem Profil angezeigter Endpunktname|
    |-TrafficManagerProfile|$westprofile|Verwenden Sie das in Schritt 1 erstellte Profilobjekt|
    |-Type|ExternalEndpoints|Weitere Informationen finden Sie unter [Traffic Manager-Endpunkt][traffic-manager-endpoints]. |
    |-Target|westus.api.cognitive.microsoft.com|Dies ist die Domäne für den LUIS-Endpunkt.|
    |-EndpointLocation|"westus"|Region des Endpunkts|
    |-EndpointStatus|Enabled|Endpunkt beim Erstellen aktivieren|

    Eine erfolgreiche Antwort sieht wie folgt aus:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Festlegen des Endpunkts für „USA, Westen“ mit dem Cmdlet **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Eine erfolgreiche Antwort ist die gleiche Antwort wie in Schritt 2.

### <a name="create-parent-traffic-manager-profile"></a>Erstellen des übergeordneten Traffic Manager-Profils
Erstellen Sie das übergeordnete Traffic Manager-Profil, und verknüpfen Sie zwei untergeordnete Traffic Manager-Profile mit dem übergeordneten Profil.

1. Erstellen des übergeordneten Profils mit dem Cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    In dieser Tabelle wird jede Variable im Cmdlet erläutert:

    |Konfigurationsparameter|Variablenname oder -wert|Zweck|
    |--|--|--|
    |-Name|luis-profile-parent|Traffic Manager-Name im Azure-Portal|
    |-ResourceGroupName|luis-traffic-manager|Im vorherigen Abschnitt erstellt|
    |-TrafficRoutingMethod|Leistung|Weitere Informationen finden Sie unter [Traffic Manager-Methoden für das Datenverkehrsrouting][routing-methods]. Beim Verwenden von Leistungsfunktionen muss die URL-Anforderung an Traffic Manager aus der Region des Benutzers stammen. Wenn die Anforderung über einen Chatbot oder eine andere Anwendung erfolgt, muss diese die Region im Aufruf von Traffic Manager imitieren. |
    |-RelativeDnsName|luis-dns-parent|Dies ist die Unterdomäne für den Dienst: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Abrufintervall, 30 Sekunden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Der Port und das Protokoll für LUIS lauten HTTPS und 443|
    |-MonitorPath|`/`|Dieser Pfad ist unerheblich, da stattdessen die Pfade der untergeordneten Endpunkte verwendet werden.|

    Bei einer erfolgreichen Anforderung erfolgt keine Antwort.

2. Hinzufügen eines untergeordneten Profils für „USA, Osten“ zum übergeordneten Profil mit **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** und dem Typ **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    In dieser Tabelle wird jede Variable im Cmdlet erläutert:

    |Konfigurationsparameter|Variablenname oder -wert|Zweck|
    |--|--|--|
    |-EndpointName|child-endpoint-useast|Profil für „USA, Osten“|
    |-TrafficManagerProfile|$parentprofile|Profil, dem dieser Endpunkt zugewiesen werden soll|
    |-Type|NestedEndpoints|Weitere Informationen finden Sie unter [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile.Id|ID des untergeordneten Profils|
    |-EndpointStatus|Enabled|Endpunktstatus nach dem Hinzufügen zum übergeordneten Element|
    |-EndpointLocation|"eastus"|[Name der Azure-Region](https://azure.microsoft.com/global-infrastructure/regions/) der Ressource|
    |-MinChildEndpoints|1|Minimale Anzahl von untergeordneten Endpunkten|

    Die erfolgreiche Antwort sieht wie folgt aus und enthält den neuen Endpunkt `child-endpoint-useast`:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Hinzufügen eines untergeordneten Profils für „USA, Westen“ zum übergeordneten Profil mit dem Cmdlet **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** und dem Typ **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    In dieser Tabelle wird jede Variable im Cmdlet erläutert:

    |Konfigurationsparameter|Variablenname oder -wert|Zweck|
    |--|--|--|
    |-EndpointName|child-endpoint-uswest|Profil für „USA, Westen“|
    |-TrafficManagerProfile|$parentprofile|Profil, dem dieser Endpunkt zugewiesen werden soll|
    |-Type|NestedEndpoints|Weitere Informationen finden Sie unter [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile.Id|ID des untergeordneten Profils|
    |-EndpointStatus|Enabled|Endpunktstatus nach dem Hinzufügen zum übergeordneten Element|
    |-EndpointLocation|"westus"|[Name der Azure-Region](https://azure.microsoft.com/global-infrastructure/regions/) der Ressource|
    |-MinChildEndpoints|1|Minimale Anzahl von untergeordneten Endpunkten|

    Die erfolgreiche Antwort sieht wie folgt aus und enthält den vorherigen Endpunkt `child-endpoint-useast` und den neuen Endpunkt `child-endpoint-uswest`:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Festlegen von Endpunkten mit dem Cmdlet **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Eine erfolgreiche Antwort ist die gleiche Antwort wie in Schritt 3.

### <a name="powershell-variables"></a>PowerShell-Variablen
In den vorherigen Abschnitten wurden drei PowerShell-Variablen erstellt: `$eastprofile`, `$westprofile`, `$parentprofile`. Diese Variablen werden gegen Ende der Traffic Manager-Konfiguration verwendet. Wenn Sie die Variablen nicht erstellen möchten oder vergessen, sie zu erstellen, oder wenn im PowerShell-Fenster ein Timeout auftritt, können Sie mit dem PowerShell-Cmdlet **[Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** das Profil erneut abrufen und es einer Variable zuweisen.

Ersetzen Sie die Elemente in spitzen Klammern `<>` durch die richtigen Werte für jedes der drei benötigten Profile.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Überprüfen der Funktion von Traffic Manager
Überprüfen Sie, ob die Traffic Manager-Profile funktionieren – sie müssen den Status `Online` aufweisen. Dieser Status basiert auf dem Abfragepfad des Endpunkts.

### <a name="view-new-profiles-in-the-azure-portal"></a>Anzeigen von neuen Profilen im Azure-Portal
Sie können überprüfen, ob alle drei Profile erstellt wurden, indem Sie sich die Ressourcen in der Ressourcengruppe `luis-traffic-manager` ansehen.

![Screenshot der Azure-Ressourcengruppe luis-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Überprüfen, ob der Profilstatus „Online“ lautet
Traffic Manager fragt den Pfad jedes Endpunkts ab, um sicherzustellen, dass er online ist. Wenn er online ist, lautet der Status der untergeordneten Profile `Online`. Dies wird in der **Übersicht** jedes Profils angezeigt.

![Screenshot der Übersicht eines Azure Traffic Manager-Profils mit dem Überwachungsstatus „Online“](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Überprüfen der Funktion von Traffic Manager-Abfragen
Eine weitere Möglichkeit, um zu überprüfen, ob die Traffic Manager-Abfragen funktionieren, bieten die LUIS-Endpunktprotokolle. Exportieren Sie in der Liste der Apps auf der [LUIS][LUIS]-Website das Endpunktprotokoll für die Anwendung. Da Traffic Manager die beiden Endpunkte häufig abfragt, enthalten die Protokolle Einträge, auch wenn sie nur wenige Minuten aktiv waren. Suchen Sie nach Einträgen, in denen die Abfrage mit `traffic-manager-` beginnt.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Überprüfen der Funktion der DNS-Antwort von Traffic Manager
Um zu überprüfen, ob die DNS-Antwort einen LUIS-Endpunkt zurückgibt, fordern Sie den DNS-Eintrag eines übergeordneten Traffic Manager-Profils mit einer DNS-Clientbibliothek an. Der DNS-Name für das übergeordnete Profil lautet `luis-dns-parent.trafficmanager.net`.

Der folgende Node.js-Code nimmt eine Anforderung des übergeordneten Profils vor und gibt einen LUIS-Endpunkt zurück:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Die erfolgreiche Antwort mit dem LUIS-Endpunkt lautet:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Verwenden des übergeordneten Traffic Manager-Profils
Damit Datenverkehr endpunktübergreifend verwaltet werden kann, müssen Sie einen Aufruf an das Traffic Manager-DNS vornehmen, um den LUIS-Endpunkt zu suchen. Dieser Aufruf erfolgt für jede LUIS-Endpunktanforderung und muss den geografischen Standort des Benutzers der LUIS-Clientanwendung simulieren. Fügen Sie den DNS-Antwortcode zwischen Ihrer LUIS-Clientanwendung und der Anforderung an LUIS nach der Endpunktvorhersage hinzu.

## <a name="resolving-a-degraded-state"></a>Auflösen des Status „Heruntergestuft“

Aktivieren Sie [Diagnoseprotokolle](../../traffic-manager/traffic-manager-diagnostic-logs.md) für Traffic Manager, um festzustellen, warum der Endpunktstatus heruntergestuft wurde.

## <a name="clean-up"></a>Bereinigung
Entfernen Sie die beiden LUIS-Endpunktschlüssel, die drei Traffic Manager-Profile und die Ressourcengruppe, die die fünf Ressourcen enthalten hat. Dies erfolgt im Azure-Portal. Löschen Sie zuerst die fünf Ressourcen aus der Ressourcenliste. Löschen Sie dann die Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

Überprüfen Sie [Middleware](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler&view=azure-bot-service-4.0)-Optionen im Bot-Framework v4, um zu verstehen, wie dieser Traffic Manager-Code einem BotFramework-Bot hinzugefügt werden kann.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md