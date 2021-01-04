---
title: Erstellen einer Web-App in ASE v1
description: Hier erfahren Sie, wie Sie Web-Apps in einer App Service-Umgebung v1 erstellen. Dieses Dokument wird nur für Kunden bereitgestellt, die die ASE-Legacyumgebung v1 verwenden.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d0ac8e0cf187d1a6a329f2c7332c9ec37018d4cf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005230"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Erstellen einer Web-App in einer App Service-Umgebung v1

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v1 behandelt.  Für die App Service-Umgebung steht eine neuere Version zur Verfügung. Diese ist benutzerfreundlicher und basiert auf einer leistungsfähigeren Infrastruktur. Weitere Informationen zu dieser neuen Version finden Sie unter [Einführung in die App Service-Umgebung](intro.md).
> 

## <a name="overview"></a>Übersicht
Dieses Tutorial zeigt, wie Sie Web-Apps und App Service-Pläne in einer [App Service-Umgebung v1](app-service-app-service-environment-intro.md) (App Service Environment, ASE) erstellen. 

> [!NOTE]
> Wenn Sie wissen möchten, wie eine Web-App erstellt wird, dies jedoch nicht in einer App Service-Umgebung durchführen möchten, finden Sie weitere Informationen unter [Erstellen einer .NET-Web-App](../quickstart-dotnetcore.md) oder in einem der zugehörigen Tutorials für andere Sprachen und Frameworks.
> 
> 

## <a name="prerequisites"></a>Voraussetzungen
Bei diesem Tutorial wird vorausgesetzt, dass Sie eine App Service-Umgebung erstellt haben. Wenn das nicht der Fall ist, finden Sie weitere Informationen unter [Erstellen einer App Service-Umgebung](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Erstellen einer Web-App
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Ressource erstellen > Web + Mobil > Web-App**. 
   
    ![Screenshot, der zeigt, wo Sie eine Web-App im Azure-Portal erstellen.][1]
2. Wählen Sie Ihr Abonnement aus.  
   
    Achten Sie bei mehreren Abonnements darauf, dass Sie zum Erstellen einer App in Ihrer App Service-Umgebung dasselbe Abonnement verwenden müssen, das Sie beim Erstellen der App Service-Umgebung verwendet haben. 
3. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.
   
    *Ressourcengruppen* ermöglichen Ihnen, verwandte Azure-Ressourcen als Einheit zu verwalten, und sind hilfreich beim Einrichten von Regeln für die *rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)* für Ihre Apps. Weitere Informationen finden Sie unter [Übersicht über den Azure Resource Manager][ResourceGroups]. 
4. Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen.
   
    Bei *App Service-Plänen* handelt es sich um einen verwalteten Satz von Web-Apps.  Bei Auswählen der Preise gelten diese normalerweise nicht für die einzelnen Apps, sondern für den App Service-Plan. In einer ASE zahlen Sie für die zugeordneten Serverinstanzen, und nicht das, was in Ihrem ASP aufgelistet ist.  Um die Anzahl von Instanzen einer Web-App hochzuskalieren, skalieren Sie die Anzahl von Instanzen Ihres App Service-Plans hoch. Dies wirkt sich auf sämtliche Web-Apps innerhalb des betroffenen Plans aus.  Für einige Funktionen wie Websiteslots oder die VNET-Integration gelten innerhalb des Plans ebenfalls Mengenbeschränkungen.  Weitere Informationen hierzu finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../overview-hosting-plans.md).
   
    Sie können die App Service-Pläne in Ihrer ASE anhand des Standorts identifizieren, der unter dem Plannamen angegeben ist.  
   
    ![Screenshot, der zeigt, wo Sie die App Service-Pläne in Ihrer ASE anzeigen können.][5]
   
    Wenn Sie einen App Service-Plan verwenden möchten, der bereits in Ihrer App Service-Umgebung vorhanden ist, wählen Sie diesen aus. Wenn Sie einen neuen App Service-Plan erstellen möchten, finden Sie weitere Informationen im folgenden Abschnitt dieses Tutorials, [Erstellen eines App Service-Plans in einer App Service-Umgebung](#createplan).
5. Geben Sie den Namen für Ihre Web-App ein, und klicken Sie dann auf **Erstellen**. 
   
    Wenn Ihre ASE eine externe VIP-Adresse verwendet, lautet die URL einer App in dieser ASE: „[*Websitename*].[*Name Ihrer App Service-Umgebung*].p.azurewebsites.net“ anstelle von „[*Websitename*].azurewebsites.net“.
   
    Wenn Ihre ASE eine interne VIP-Adresse verwendet, lautet die URL einer App in dieser ASE: [*Websitename*].[*während der ASE-Erstellung angegebene Unterdomäne*]   
    Nach der Auswahl Ihres ASP während der Erstellung der ASE sehen Sie die aktualisierte Unterdomäne unter **Name**.

## <a name="create-an-app-service-plan"></a><a name="createplan"></a> Erstellen eines App Service-Plans
Wenn Sie einen App Service-Plan in einer App Service-Umgebung erstellen, stehen andere Worker zur Auswahl, da es in einer ASE keine gemeinsam verwendeten Worker gibt.  Es können ausschließlich die Worker verwendet werden, die der App Service-Umgebung vom Administrator zugewiesen wurden.  Um einen neuen Plan erstellen zu können, müssen dem ASE-Workerpool mehr Worker zugewiesen sein als die Gesamtzahl der Instanzen all Ihrer Pläne innerhalb dieses Workerpools.  Wenn im Workerpool Ihrer App Service-Umgebung nicht genügend Worker vorhanden sind, um Ihren Plan zu erstellen, müssen Sie sich an Ihren ASE-Administrator wenden und veranlassen, dass weitere Worker hinzugefügt werden.

Ein weiterer Unterschied bei App Service-Plänen, die in einer App Service-Umgebung gehostet werden, besteht darin, dass keine Preise ausgewählt werden können.  Mit einer App Service-Umgebung zahlen Sie für die vom System verwendeten Computeressourcen, und es fallen keine zusätzlichen Kosten für die Pläne innerhalb der Umgebung an.  Beim Erstellen eines App Service-Plans wählen Sie üblicherweise einen Tarif, auf dessen Basis die Abrechnung erfolgt.  Bei einer App Service-Umgebung handelt es sich im Wesentlichen um einen privaten Speicherort, an dem Sie Inhalte erstellen können.  Sie zahlen nicht für das Hosten Ihrer Inhalte, sondern für die Umgebung.

Die folgenden Schritte zeigen, wie Sie einen App Service-Plan während der Erstellung einer Web-App erstellen, wie im vorherigen Abschnitt des Tutorials erläutert.

1. Klicken Sie in der Oberfläche zur Planauswahl auf **Neu erstellen** , und geben Sie einen Namen für den Plan ein, wie Sie dies normalerweise auch außerhalb einer ASE durchführen.
2. Wählen Sie aus der Standortauswahl die ASE aus, die Sie verwenden möchten.
   
    Da eine App Service-Umgebung im Wesentlichen ein privater Bereitstellungsort ist, wird sie unter "Standort" angezeigt. 
   
    ![Screenshot, der die Standortauswahl zum Auswählen der gewünschten ASE zeigt.][2]
   
    Nach der Auswahl einer ASE in der Standortauswahl wird die Oberfläche zur Erstellung von App Service-Plänen aktualisiert.  Der Standort zeigt nun den Namen des ASE-Systems und die Region an, in der es sich befindet. Die Tarifplanauswahl wird durch eine Workerpoolauswahl ersetzt.  
   
    ![Screenshot, der die ASE-Systemdetails nach Auswahl der ASE in der Standortauswahl zeigt.][3]

### <a name="selecting-a-worker-pool"></a>Auswählen eines Workerpools
In Azure App Service und außerhalb einer App Service-Umgebung sind bei der Auswahl eines dedizierten Tarifplans üblicherweise drei Computegrößen verfügbar.  Auf ähnliche Weise können Sie für eine ASE bis zu drei Workerpools und die Computegröße angeben, die für den jeweiligen Workerpool verwendet wird.  Für Mandanten der ASE bedeutet dies, dass anstelle der Auswahl eines Tarifplans mit Computegröße für Ihren App Service-Plan ein sogenannter *Workerpool* ausgewählt wird.  

Auf der Benutzeroberfläche zur Auswahl des Workerpools wird unterhalb des Namens die Computegröße angezeigt, die für diesen Workerpool verwendet wird.  Die verfügbare Größe entspricht der Anzahl von Serverinstanzen, die zur Verwendung in diesem Pools verfügbar sind.  Möglicherweise verfügt der Pool insgesamt über mehr Instanzen als diese Anzahl, der hier angezeigte Wert bezieht sich jedoch lediglich auf die Anzahl von Instanzen, die gegenwärtig nicht verwendet werden.  Informationen zum Hinzufügen von Computeressourcen zu Ihrer App Service-Umgebung finden Sie unter [Konfigurieren der App Service-Umgebung](app-service-web-configure-an-app-service-environment.md).

![Screenshot, der den Bereich „Workerpool“ zeigt, in dem Sie Workerpools für die ASE auswählen können.][4]

In diesem Beispiel werden lediglich zwei verfügbare Workerpools angezeigt. Der Grund dafür ist, dass der Administrator der App Service-Umgebung nur diesen beiden Workerpools Hosts zugewiesen hat.  Der dritte Pool würde angezeigt, wenn ihm virtuelle Computer zugewiesen würden.  

## <a name="after-web-app-creation"></a>Nach der Erstellung der Web-App
Im Zusammenhang mit dem Ausführen von Web-Apps und dem Verwalten von App Service-Plänen in einer ASE müssen einige Aspekte berücksichtigt werden.  

Wie bereits erwähnt, ist der Besitzer der ASE für die Größe des Systems und damit ebenso dafür verantwortlich, dass eine ausreichend große Kapazität zum Hosten der gewünschten App Service-Pläne verfügbar ist. Wenn keine Worker verfügbar sind, kann der App Service-Plan nicht erstellt werden.  Dies gilt ebenfalls für das Skalieren Ihrer Web-App.  Wenn weitere Instanzen erforderlich sind, müssen Sie den Administrator der App Service-Umgebung bitten, weitere Worker hinzufügen.

Nach dem Erstellen der Web-App und des App Service-Plans sollten Sie eine zentrale Hochskalierung durchführen.  Um Fehlertoleranz für Ihre Apps zu gewährleisten, werden in einer ASE immer mindestens zwei Instanzen Ihres App Service-Plans benötigt.  Das Skalieren eines App Service-Plans in einer ASE entspricht dem normalen Skalieren über die Oberfläche des App Service-Plans.  Weitere Informationen zum Skalieren finden Sie unter [Skalieren einer Web-App in einer App Service-Umgebung](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: /powershell/azure/?view=azps-3.8.0