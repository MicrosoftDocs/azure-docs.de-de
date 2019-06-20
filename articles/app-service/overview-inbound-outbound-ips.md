---
title: Ein-/ausgehende IP-Adressen – Azure App Service | Microsoft-Dokumentation
description: Beschreibt, wie ein- und ausgehende IP-Adressen in App Service verwendet werden und wie Sie Informationen zu diesen Adressen für Ihre App ermitteln.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: de9ae8e5c0cbf0997811db9624f6c6b92e03a5df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742934"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Ein- und ausgehende IP-Adressen in Azure App Service

Mit Ausnahme von [App Service-Umgebungen](environment/intro.md) ist [Azure App Service](overview.md) ein mehrinstanzenfähiger Dienst. Apps, die sich nicht in einer App Service-Umgebung (nicht in der [isolierten Ebene](https://azure.microsoft.com/pricing/details/app-service/)) befinden, nutzen die Netzwerkinfrastruktur gemeinsam mit anderen Apps. Aus diesem Grund können sich die ein- und ausgehenden IP-Adressen einer App unterscheiden und in bestimmten Situationen sogar ändern. 

[App Service-Umgebungen](environment/intro.md) nutzen dedizierte Netzwerkinfrastrukturen, sodass in einer App Service-Umgebung ausgeführte Apps sowohl für ein- als auch ausgehende Verbindungen statische, dedizierte IP-Adressen abrufen.

## <a name="when-inbound-ip-changes"></a>Situationen, in denen sich eingehende IP-Adressen ändern

Unabhängig von der Anzahl horizontal skalierter Instanzen besitzt jede App eine einzelne eingehende IP-Adresse. Die eingehende IP-Adresse kann sich ändern, wenn Sie eine der folgenden Aktionen ausführen:

- Sie löschen eine App und erstellen sie in einer anderen Ressourcengruppe neu.
- Sie löschen die letzte App in einer Kombination aus Ressourcengruppe _und_ Region und erstellen sie neu.
- Sie löschen eine vorhandene SSL-Bindung, beispielsweise während der Zertifikaterneuerung (siehe [Erneuern von Zertifikaten](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="find-the-inbound-ip"></a>Auffinden der eingehenden IP-Adresse

Führen Sie einfach den folgenden Befehl in einem lokalen Terminal aus:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Abrufen einer statischen eingehenden IP-Adresse

Mitunter benötigen Sie eine dedizierte, statische IP-Adresse für Ihre App. Um eine statische eingehende IP-Adresse abzurufen, müssen Sie eine [IP-basierte SSL-Bindung](app-service-web-tutorial-custom-ssl.md#secure-a-custom-domain) konfigurieren. Wenn Sie die SSL-Funktion eigentlich nicht zum Schutz Ihrer App benötigen, können Sie auch ein selbstsigniertes Zertifikat für diese Bindung hochladen. Bei einer IP-basierten SSL-Bindung ist das Zertifikat an die IP-Adresse selbst gebunden. Daher stellt App Service zu diesem Zweck eine statische IP-Adresse bereit. 

## <a name="when-outbound-ips-change"></a>Situationen, in denen sich ausgehende IP-Adressen ändern

Unabhängig von der Anzahl horizontal skalierter Instanzen besitzt jede App immer eine festgelegte Anzahl von ausgehenden IP-Adressen. Jede ausgehende Verbindung von der App Service-App (beispielsweise mit einer Back-End-Datenbank) verwendet eine der ausgehenden IP-Adressen als IP-Ursprungsadresse. Da Sie vorab nicht wissen, welche IP-Adresse eine bestimmte App-Instanz zum Herstellen der ausgehenden Verbindung verwendet, muss Ihr Back-End-Dienst seine Firewall für alle ausgehenden IP-Adressen Ihrer App öffnen.

Der Satz von ausgehenden IP-Adressen für Ihre App ändert sich, wenn Sie die App zwischen den niedrigeren Tarifen (**Basic**, **Standard** und **Premium**) und dem Tarif **Premium V2** skalieren.

Sie finden die Sammlung aller möglichen IP-Ausgangsadressen, die von Ihrer App verwendet werden können, indem Sie nach der Eigenschaft `possibleOutboundIPAddresses` suchen, oder im Feld **Zusätzliche ausgehende IP-Adressen** auf dem Blatt **Eigenschaften** im Azure-Portal. Siehe [Ermitteln der ausgehenden IP-Adressen](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Ermitteln der ausgehenden IP-Adressen

Um die momentan von Ihrer App verwendeten ausgehenden IP-Adressen zu ermitteln, klicken Sie im Azure-Portal im linken Navigationsbereich Ihrer App auf **Eigenschaften**. Sie sind im Feld **Ausgehende IP-Adressen** aufgelistet.

Die gleichen Informationen erhalten Sie, indem Sie den folgenden Befehl in der [Cloud Shell](../cloud-shell/quickstart.md) ausführen.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Um unabhängig vom Tarif _alle_ möglichen IP-Ausgangsadressen für Ihre App zu finden, klicken Sie im linken Navigationsbereich Ihrer App auf **Eigenschaften**. Sie sind im Feld **Zusätzliche ausgehende IP-Adressen** aufgelistet.

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
