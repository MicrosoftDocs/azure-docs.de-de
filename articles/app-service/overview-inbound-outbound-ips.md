---
title: Eingehende/ausgehende IP-Adressen
description: Hier wird beschrieben, wie ein- und ausgehende IP-Adressen in Azure App Service verwendet werden, wann sie sich ändern und wie Sie diese Adressen für Ihre App ermitteln.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e5b271cc5cd8cb52267b6ee44bc3965d0e4b0aab
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746146"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Ein- und ausgehende IP-Adressen in Azure App Service

Mit Ausnahme von [App Service-Umgebungen](environment/intro.md) ist [Azure App Service](overview.md) ein mehrinstanzenfähiger Dienst. Apps, die sich nicht in einer App Service-Umgebung (nicht in der [isolierten Ebene](https://azure.microsoft.com/pricing/details/app-service/)) befinden, nutzen die Netzwerkinfrastruktur gemeinsam mit anderen Apps. Aus diesem Grund können sich die ein- und ausgehenden IP-Adressen einer App unterscheiden und in bestimmten Situationen sogar ändern.

[App Service-Umgebungen](environment/intro.md) nutzen dedizierte Netzwerkinfrastrukturen, sodass in einer App Service-Umgebung ausgeführte Apps sowohl für ein- als auch ausgehende Verbindungen statische, dedizierte IP-Adressen abrufen.

## <a name="how-ip-addresses-work-in-app-service"></a>Funktionsweise von IP-Adressen in App Service

Eine App Service-App wird in einem App Service-Plan ausgeführt, und App Service-Pläne werden in einer der Bereitstellungseinheiten in der Azure-Infrastruktur (intern als Webspace bezeichnet) bereitgestellt. Jeder Bereitstellungseinheit werden bis zu fünf virtuelle IP-Adressen zugewiesen, darunter eine öffentliche IP-Adresse für eingehenden Datenverkehr und vier IP-Adressen für ausgehenden Datenverkehr. Alle App Service-Pläne in derselben Bereitstellungseinheit und App-Instanzen, die darin ausgeführt werden, verwenden dieselbe Gruppe von virtuellen IP-Adressen. Für eine App Service-Umgebung (ein App Service-Plan in der [Isolierten Ebene](https://azure.microsoft.com/pricing/details/app-service/)) ist der App Service-Plan die Bereitstellungseinheit selbst, sodass ihr die virtuellen IP-Adressen zugewiesen werden.

Da Sie einen App Service-Plan nicht zwischen Bereitstellungseinheiten verschieben dürfen, bleiben die Ihrer Anwendung zugewiesenen virtuellen IP-Adressen in der Regel gleich, aber es gibt Ausnahmen.

## <a name="when-inbound-ip-changes"></a>Situationen, in denen sich eingehende IP-Adressen ändern

Unabhängig von der Anzahl horizontal skalierter Instanzen besitzt jede App eine einzelne eingehende IP-Adresse. Die eingehende IP-Adresse kann sich ändern, wenn Sie eine der folgenden Aktionen ausführen:

- Sie löschen eine App und erstellen sie in einer anderen Ressourcengruppe neu (die Bereitstellungseinheit kann sich ändern).
- Sie löschen die letzte App in einer Kombination aus Ressourcengruppe _und_ Region und erstellen sie neu (die Bereitstellungseinheit kann sich ändern).
- Sie löschen eine vorhandene IP-basierte TLS-/SSL-Bindung, beispielsweise während der Zertifikaterneuerung (siehe [Erneuern von Zertifikaten](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Auffinden der eingehenden IP-Adresse

Führen Sie einfach den folgenden Befehl in einem lokalen Terminal aus:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Abrufen einer statischen eingehenden IP-Adresse

Mitunter benötigen Sie eine dedizierte, statische IP-Adresse für Ihre App. Um eine statische eingehende IP-Adresse abzurufen, müssen Sie eine [benutzerdefinierte Domäne sichern](configure-ssl-bindings.md#secure-a-custom-domain). Wenn Sie die TLS-Funktion eigentlich nicht zum Schutz Ihrer App benötigen, können Sie auch ein selbstsigniertes Zertifikat für diese Bindung hochladen. Bei einer IP-basierten TLS-Bindung ist das Zertifikat an die IP-Adresse selbst gebunden. Daher stellt App Service zu diesem Zweck eine statische IP-Adresse bereit. 

## <a name="when-outbound-ips-change"></a>Situationen, in denen sich ausgehende IP-Adressen ändern

Unabhängig von der Anzahl horizontal skalierter Instanzen besitzt jede App immer eine festgelegte Anzahl von ausgehenden IP-Adressen. Jede ausgehende Verbindung von der App Service-App (beispielsweise mit einer Back-End-Datenbank) verwendet eine der ausgehenden IP-Adressen als IP-Ursprungsadresse. Die zu verwendende IP-Adresse wird zur Laufzeit zufällig ausgewählt, sodass Ihr Back-End-Dienst seine Firewall für alle ausgehenden IP-Adressen Ihrer Anwendung öffnen muss.

Der Satz der ausgehenden IP-Adressen für Ihre Anwendung ändert sich, wenn Sie eine der folgenden Aktionen durchführen:

- Sie löschen eine App und erstellen sie in einer anderen Ressourcengruppe neu (die Bereitstellungseinheit kann sich ändern).
- Sie löschen die letzte App in einer Kombination aus Ressourcengruppe _und_ Region und erstellen sie neu (die Bereitstellungseinheit kann sich ändern).
- Skalieren Sie die App zwischen den niedrigeren Tarifen ( **Basic** , **Standard** und **Premium** ) und dem Tarif **Premium V2** (IP-Adressen können dem Satz hinzugefügt oder aus ihm entfernt werden).

Sie finden die Sammlung aller möglichen IP-Ausgangsadressen, die von Ihrer App verwendet werden können, indem Sie nach der Eigenschaft `possibleOutboundIpAddresses` suchen, oder im Feld **Zusätzliche ausgehende IP-Adressen** auf dem Blatt **Eigenschaften** im Azure-Portal. Siehe [Ermitteln der ausgehenden IP-Adressen](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Ermitteln der ausgehenden IP-Adressen

Um die momentan von Ihrer App verwendeten ausgehenden IP-Adressen zu ermitteln, klicken Sie im Azure-Portal im linken Navigationsbereich Ihrer App auf **Eigenschaften** . Sie sind im Feld **Ausgehende IP-Adressen** aufgelistet.

Die gleichen Informationen erhalten Sie, indem Sie den folgenden Befehl in der [Cloud Shell](../cloud-shell/quickstart.md) ausführen.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Um unabhängig vom Tarif _alle_ möglichen IP-Ausgangsadressen für Ihre App zu finden, klicken Sie im linken Navigationsbereich Ihrer App auf **Eigenschaften** . Sie sind im Feld **Zusätzliche ausgehende IP-Adressen** aufgelistet.

Die gleichen Informationen erhalten Sie, indem Sie den folgenden Befehl in der [Cloud Shell](../cloud-shell/quickstart.md) ausführen.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie den eingehenden Datenverkehr anhand von Quell-IP-Adressen einschränken.

> [!div class="nextstepaction"]
> [Einschränkungen für statische IP-Adressen](app-service-ip-restrictions.md)
