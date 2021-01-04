---
title: Benutzerdefinierte Unterdomänen
titleSuffix: Azure Cognitive Services
description: Benutzerdefinierte Unterdomänennamen für jede Cognitive Service-Ressource werden über das Azure-Portal, Azure Cloud Shell oder die Azure CLI erstellt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: erhopf
ms.openlocfilehash: 4746aad2d7cd62cb309a1823f8c50487e6f7e87c
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032965"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Benutzerdefinierte Unterdomänennamen für Cognitive Services

Azure Cognitive Services verwenden benutzerdefinierte Unterdomänennamen für jede Ressource, die über das [Azure-Portal](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) oder die [Azure CLI](/cli/azure/install-azure-cli) erstellt wurde. Anders als bei regionalen Endpunkten, die für alle Kunden in einer bestimmten Azure-Region üblich waren, sind benutzerdefinierte Unterdomänennamen für die Ressource eindeutig. Benutzerdefinierte Unterdomänennamen sind erforderlich, um Funktionen wie Azure Active Directory (Azure AD) für die Authentifizierung zu aktivieren.

## <a name="how-does-this-impact-existing-resources"></a>Wie wirkt sich dies auf vorhandene Ressourcen aus?

Für Cognitive Services-Ressourcen, die vor dem 1. Juli 2019 erstellt wurden, werden die regionalen Endpunkte für den zugeordneten Dienst verwendet. Diese Endpunkte können mit den vorhandenen und neuen Ressourcen verwendet werden.

Führen Sie zum Migrieren einer vorhandenen Ressource die folgenden Schritte aus, um benutzerdefinierte Unterdomänennamen zu verwenden, sodass Sie Funktionen wie Azure AD aktivieren können:

1. Melden Sie sich beim Azure-Portal an, und suchen Sie die Cognitive Services-Ressource, der Sie einen benutzerdefinierten Unterdomänennamen hinzufügen möchten.
2. Suchen Sie auf dem Blatt **Übersicht** nach **Generate Custom Domain Name** (Benutzerdefinierten Domänennamen generieren), und klicken Sie darauf.
3. Dadurch wird ein Panel mit Anweisungen zum Erstellen einer eindeutigen benutzerdefinierten Unterdomäne für Ihre Ressource geöffnet.
   > [!WARNING]
   > Nachdem Sie einen benutzerdefinierten Unterdomänennamen erstellt haben, kann dieser **nicht** mehr geändert werden.

## <a name="do-i-need-to-update-my-existing-resources"></a>Muss ich meine vorhandenen Ressourcen aktualisieren?

Nein. Der regionale Endpunkt funktioniert weiterhin für neue und vorhandene Cognitive Services-Komponenten, und der benutzerdefinierte Unterdomänenname ist optional. Auch wenn ein benutzerdefinierter Unterdomänenname hinzugefügt wird, funktioniert der regionale Endpunkt weiterhin mit der Ressource.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Was geschieht, wenn ein SDK die Region für eine Ressource anfordert?

> [!WARNING]
> Die Speech-Dienste nutzen **ausschließlich** benutzerdefinierte Subdomänen mit [privaten Endpunkten](Speech-Service/speech-services-private-link.md). Verwenden Sie in allen anderen Fällen die **regionalen Endpunkte** bei Verwendung der Speech-Dienste und der dazugehörigen SDKs.

Regionale Endpunkte und benutzerdefinierte Unterdomänennamen werden unterstützt und können austauschbar verwendet werden. Dennoch ist der vollständige Endpunkt erforderlich.

Informationen zu Regionen sind auf dem Blatt **Übersicht** für Ihre Ressource im [Azure-Portal](https://portal.azure.com) verfügbar. Eine vollständige Liste der regionalen Endpunkte finden Sie unter [Gibt es eine Liste der regionalen Endpunkte?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Sind benutzerdefinierte Unterdomänennamen regional?

Ja. Wenn Sie einen benutzerdefinierten Unterdomänennamen verwenden, werden die regionalen Aspekte Ihrer Cognitive Services-Ressource nicht geändert.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Welche Anforderungen gelten für einen benutzerdefinierten Unterdomänennamen?

Ein benutzerdefinierter Unterdomänenname ist für Ihre Ressource eindeutig. Der Name kann nur alphanumerische Zeichen und das Zeichen `-` enthalten. Er muss zwischen 2 und 64 Zeichen lang sein und darf nicht mit dem Zeichen `-` enden.

## <a name="can-i-change-a-custom-domain-name"></a>Kann ich einen benutzerdefinierten Domänennamen ändern?

Nein. Nachdem Sie einen benutzerdefinierten Unterdomänennamen erstellt und einer Ressource zugeordnet haben, kann dieser nicht mehr geändert werden.

## <a name="can-i-reuse-a-custom-domain-name"></a>Kann ich einen benutzerdefinierten Domänennamen wieder verwenden?

Jeder benutzerdefinierte Unterdomänenname ist eindeutig. Wenn Sie also einen einer Cognitive Services-Ressource zugeordneten benutzerdefinierten Unterdomänennamen wieder verwenden möchten, müssen Sie die vorhandene Ressource löschen. Nachdem die Ressource gelöscht wurde, können Sie den benutzerdefinierten Unterdomänennamen wieder verwenden.

## <a name="is-there-a-list-of-regional-endpoints"></a>Gibt es eine Liste regionaler Endpunkte?

Ja. Dies ist eine Liste der regionalen Endpunkte, die Sie mit Azure Cognitive Services-Ressourcen verwenden können.

> [!NOTE]
> Der Translator-Dienst und die Bing-Suche-APIs verwenden globale Endpunkte.

| Endpunkttyp | Region | Endpunkt |
|---------------|--------|----------|
| Öffentlich | Global (Translator und Bing) | `https://api.cognitive.microsoft.com` |
| | Australien (Osten) | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brasilien Süd | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Kanada, Mitte | `https://canadacentral.api.cognitive.microsoft.com` |
| | USA (Mitte) | `https://centralus.api.cognitive.microsoft.com` |
| | Asien, Osten | `https://eastasia.api.cognitive.microsoft.com` |
| | East US | `https://eastus.api.cognitive.microsoft.com` |
| | USA (Ost) 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Frankreich, Mitte | `https://francecentral.api.cognitive.microsoft.com` |
| | Indien, Mitte | `https://centralindia.api.cognitive.microsoft.com` |
| | Japan, Osten | `https://japaneast.api.cognitive.microsoft.com` |
| | Korea, Mitte | `https://koreacentral.api.cognitive.microsoft.com` |
| | USA Nord Mitte | `https://northcentralus.api.cognitive.microsoft.com` |
| | Nordeuropa | `https://northeurope.api.cognitive.microsoft.com` |
| | Südafrika, Norden | `https://southafricanorth.api.cognitive.microsoft.com` |
| | USA Süd Mitte | `https://southcentralus.api.cognitive.microsoft.com` |
| | Asien, Südosten | `https://southeastasia.api.cognitive.microsoft.com` |
| | UK, Süden | `https://uksouth.api.cognitive.microsoft.com` |
| | USA, Westen-Mitte | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa, Westen | `https://westeurope.api.cognitive.microsoft.com` |
| | USA (Westen) | `https://westus.api.cognitive.microsoft.com` |
| | USA, Westen 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Government, Virginia | `https://virginia.api.cognitive.microsoft.us` |
| China | China, Osten 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | China, Norden | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Weitere Informationen

* [Was ist Cognitive Services?](./what-are-cognitive-services.md)
* [Authentifizierung](authentication.md)