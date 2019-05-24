---
title: Konfigurieren eines benutzerdefinierten Domänennamens für Ihre Azure API Management-Instanz | Microsoft-Dokumentation
description: In diesem Thema erfahren Sie, wie Sie einen benutzerdefinierten Domänennamen für Ihre Azure API Management-Instanz konfigurieren.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: a8bfa7c5baa316b4019480bfc146b6cc61eff979
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141302"
---
# <a name="configure-a-custom-domain-name"></a>Konfigurieren eines benutzerdefinierten Domänennamens 

Wenn Sie eine APIM-Instanz (API Management) erstellen, wird sie von Azure einer Unterdomäne von azure-api.net (beispielsweise `apim-service-name.azure-api.net`) zugewiesen. Sie können Ihre APIM-Endpunkte jedoch unter Ihrem eigenen Domänennamen (beispielsweise **contoso.com**) verfügbar machen. In diesem Tutorial erfahren Sie, wie Sie Endpunkten, die durch eine Azure API Management-Instanz verfügbar gemacht werden, einen vorhandenen benutzerdefinierten DNS-Namen zuordnen.

> [!WARNING]
> Kunden, die das Anheften von Zertifikaten nutzen möchten, um die Sicherheit ihrer Anwendungen zu erhöhen, müssen einen benutzerdefinierten Domänennamen und ein von ihnen verwaltetes Zertifikat (nicht das Standardzertifikat) verwenden. Kunden, die stattdessen das Standardzertifikat anheften, übernehmen eine harte Abhängigkeit für die Eigenschaften des nicht von ihnen verwalteten Zertifikats. Dies ist kein empfohlenes Verfahren.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der in diesem Artikel beschriebenen Schritte benötigen Sie Folgendes:

+ Ein aktives Azure-Abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Eine APIM-Instanz. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).
+ Einen benutzerdefinierten Domänennamen, der sich in Ihrem Besitz befindet. Der benutzerdefinierte Domänenname, den Sie verwenden möchten, muss separat bezogen und auf einem DNS-Server gehostet werden. Dieses Thema enthält keine Anleitung zum Hosten eines benutzerdefinierten Domänennamens.
+ Sie benötigen ein gültiges Zertifikat mit einem öffentlichen und privaten Schlüssel (.pfx). Der Antragstellername oder der alternative Antragstellername (Subject Alternative Name, SAN) muss dem Domänennamen entsprechen, damit APIM sicher URLs über SSL verfügbar machen kann.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Festlegen eines benutzerdefinierten Domänennamens über das Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer APIM-Instanz.
1. Klicken Sie auf **Benutzerdefinierte Domänen und SSL**.
    
    Es stehen mehrere Endpunkte zur Verfügung, denen Sie einen benutzerdefinierten Domänennamen zuweisen können. Derzeit sind folgende Endpunkte verfügbar: 
   + **Proxy** (Standardwert: `<apim-service-name>.azure-api.net`) 
   + **Portal** (Standardwert: `<apim-service-name>.portal.azure-api.net`)     
   + **Verwaltung** (Standardwert: `<apim-service-name>.management.azure-api.net`) 
   + **SCM** (Standardwert: `<apim-service-name>.scm.azure-api.net`).

     >[!NOTE]
     > Sie können alle oder nur bestimmte Endpunkte aktualisieren. Kunden aktualisieren üblicherweise **Proxy** (URL zum Aufrufen der über API Management verfügbar gemachten API) und **Portal** (URL des Entwicklerportals). Die Endpunkte **Verwaltung** und **SCM** werden intern von APIM-Kunden verwendet, weshalb ihnen seltener ein benutzerdefinierter Domänenname zugewiesen wird.

1. Wählen Sie den Endpunkt aus, den Sie aktualisieren möchten. 
1. Klicken Sie im Fenster auf der rechten Seite auf **Benutzerdefiniert**.

   + Geben Sie unter **Benutzerdefinierter Domänenname** den gewünschten Namen an. Beispiel: `api.contoso.com`. Platzhalterdomänennamen wie etwa „*.domain.com“ werden ebenfalls unterstützt.
   + Wählen Sie unter **Zertifikat** ein Zertifikat aus „Key Vault“ aus. Wenn das Zertifikat mit einem Kennwort geschützt ist, können Sie auch eine gültige PFX-Datei hochladen und deren **Kennwort** angeben.

     > [!TIP]
     > Sollten Sie das benutzerdefinierte SSL-Domänenzertifikat mit Azure Key Vault verwalten, stellen Sie sicher, dass das Zertifikat [als ein *Zertifikat*](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate) und nicht als ein *Geheimnis* in Key Vault eingefügt wird. Wenn für das Zertifikat „autorotate“ (automatisches Rotieren) festgelegt wurde, übernimmt API Management automatisch die neueste Version.

1. Klicken Sie auf „Übernehmen“.

    >[!NOTE]
    >Die Zuweisung des Zertifikats kann je nach Größe der Bereitstellung 15 Minuten und länger dauern. Bei der Entwickler-SKU tritt eine Ausfallzeit auf, bei der den SKUs Basic und höher SKUs jedoch nicht.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Nächste Schritte

[Upgrade and scale an API Management instance](upgrade-and-scale.md) (Upgraden und Skalieren einer API Management-Instanz)
