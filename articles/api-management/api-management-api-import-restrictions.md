---
title: Einschränkungen und bekannte Probleme beim Import von APIs in Azure API Management | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu bekannten Problemen und Einschränkungen beim Import in Azure API Management bei Verwendung der Formate Open API, WSDL oder WADL.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 251caa840446e75ff13d9b4dcebcbae3a36473c8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60657508"
---
# <a name="api-import-restrictions-and-known-issues"></a>Einschränkungen und bekannte Probleme beim Import von APIs
## <a name="about-this-list"></a>Informationen zu dieser Liste
Beim Importieren einer API stoßen Sie unter Umständen auf Einschränkungen oder Probleme, die behoben werden müssen, damit der Import erfolgreich ausgeführt werden kann. Diese Probleme werden nachfolgend unter dem jeweiligen Importformat der API beschrieben.

## <a name="open-api"> </a>OpenAPI/Swagger

Wenn Sie beim Importieren Ihres OpenAPI-Dokuments Fehlermeldungen erhalten, stellen Sie sicher, dass Sie das Dokument zuvor überprüft haben. Dazu können Sie entweder im Azure-Portal den Designer (Entwurf – Front-End – OpenAPI-Spezifikations-Editor) oder ein Tool eines Drittanbieters (z. B. <a href="https://editor.swagger.io">Swagger Editor</a>) verwenden.

### <a name="open-api-general"> </a>Allgemein

* Erforderliche Parameter für Pfad und Abfrage müssen eindeutige Namen besitzen. (In OpenAPI muss ein Parametername nur an einem Ort (beispielsweise im Pfad, in der Abfrage oder im Header) eindeutig sein. In API Management können Vorgänge jedoch sowohl nach Pfad- als auch nach Abfrageparametern unterschieden werden (dies wird von OpenAPI nicht unterstützt). Daher müssen Parameternamen innerhalb der gesamten URL-Vorlage eindeutig sein.)
* Mit **$ref**-Zeigern kann nicht auf externe Dateien verwiesen werden.
* **x-ms-paths** und **x-servers** sind die einzigen unterstützten Erweiterungen.
* Benutzerdefinierte Erweiterungen werden beim Import ignoriert und nicht für den Export gespeichert oder aufbewahrt.
* **Rekursion**: API Management unterstützt keine Definitionen, die rekursiv definiert sind (z. B. Schemas, die auf sich selbst verweisen).
* Die Quelldatei-URL (sofern vorhanden) wird auf relative Server-URLs angewendet.

### <a name="open-api-v2"> </a>OpenAPI, Version 2

* Nur das JSON-Format wird unterstützt.

### <a name="open-api-v3"> </a>OpenAPI, Version 3

* Wenn viele **Server** angegeben sind, versucht API Management, die erste HTTPS-URL auszuwählen. Wenn keine HTTPS-URLs vorhanden sind, wird die erste HTTP-URL ausgewählt. Wenn keine HTTP-URLs vorhanden sind, bleibt die Server-URL leer.
* **Examples** wird nicht unterstützt, aber **example** wird unterstützt.
* **Multipart/form-data** wird nicht unterstützt.

> [!IMPORTANT]
> Dieses [Dokument](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) enthält wichtige Informationen und Tipps zum OpenAPI-Import.

## <a name="wsdl"></a>WSDL
WSDL-Dateien werden zur Generierung von SOAP-Pass-Through-APIs verwendet oder dienen als Back-End einer SOAP-to-REST-API.
* **SOAP-Bindungen**: Es werden nur SOAP-Bindungen unterstützt, die im Format von „document“- oder „literal“-Codierung vorliegen. Das „rpc“-Format und SOAP-Codierung werden nicht unterstützt.
* **WSDL:Import**: Dieses Attribut wird nicht unterstützt. Kunden sollten die zu importierenden Elemente in einem Dokument zusammenführen.
* **Mehrteilige Nachrichten**: Diese Art von Nachrichten wird nicht unterstützt.
* **WCF wsHttpBinding**: Für SOAP-Dienste, die mit Windows Communication Foundation erstellt wurden, muss basicHttpBinding verwendet werden – wsHttpBinding wird nicht unterstützt.
* **MTOM**: Dienste, die MTOM verwenden, funktionieren <em>möglicherweise</em>. Eine offizielle Unterstützung wird derzeit nicht angeboten.
* **Rekursion**: Typen, die rekursiv definiert sind (beispielsweise auf ein eigenes Array verweisen), werden von APIM nicht unterstützt.
* **Mehrere Namespaces**: In einem Schema können zwar mehrere Namespaces verwendet werden, aber nur der Zielnamespace kann zum Definieren von Nachrichtenteilen verwendet werden. Namespaces, die zum Definieren anderer Ein- oder Ausgabeelememente verwendet werden und bei denen es sich nicht um den Zielnamespace handelt, werden nicht beibehalten. Ein solches WSDL-Dokument kann zwar importiert werden, beim Exportieren haben jedoch alle Nachrichtenteile den WSDL-Zielnamespace.

## <a name="wadl"></a>WADL
Derzeit sind keine Probleme beim Import im Format WADL bekannt.
