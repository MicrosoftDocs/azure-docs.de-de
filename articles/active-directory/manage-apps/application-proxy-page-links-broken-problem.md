---
title: Links auf der Seite funktionieren nicht für eine Anwendungsproxyanwendung | Microsoft-Dokumentation
description: Behandeln von Problemen mit fehlerhaften Links in Anwendungsproxyanwendungen, die Sie in Azure AD integriert haben
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8bb7326ed22217e56bdaf9a119529ba775b69a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783244"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Links auf der Seite funktionieren nicht für eine Anwendungsproxyanwendung

In diesem Artikel finden Sie Hilfe zur Problembehandlung bei nicht ordnungsgemäß funktionierenden Links in der Azure Active Directory-Anwendungsproxyanwendung.

## <a name="overview"></a>Übersicht 
Nach dem Veröffentlichen einer Anwendungsproxy-App funktionieren standardmäßig nur Links in der Anwendung, die auf Ziele verweisen, die in der veröffentlichten Stamm-URL enthalten sind. Links innerhalb der Anwendungen funktionieren nicht, da die interne URL für die Anwendung möglicherweise nicht alle Ziele der Links in der Anwendung enthält.

**Warum geschieht dies?** Wenn Sie auf einen Link in einer Anwendung klicken, versucht der Anwendungsproxy, die URL entweder als interne URL innerhalb derselben Anwendung oder als extern verfügbare URL aufzulösen. Wenn der Link auf eine interne URL verweist, die sich nicht innerhalb derselben Anwendung befindet, gehört sie keinem der Buckets an. Dies führt zu einem Fehler, dass das Ziel nicht gefunden wurde.

## <a name="ways-you-can-resolve-broken-links"></a>Methoden zum Beheben fehlerhafter Links

Es gibt drei Möglichkeiten, dieses Problem zu beheben. Die im Folgenden aufgeführten Optionen sind nach zunehmender Komplexität aufgeführt.

1.  Stellen Sie sicher, dass die interne URL eine Stamm-URL ist, die alle relevanten Links für die Anwendung enthält. Dadurch werden alle Links als innerhalb derselben Anwendung veröffentlichter Inhalt aufgelöst.

    Wenn Sie die interne URL ändern, die Zielseite für Benutzer jedoch nicht geändert werden soll, ändern Sie die URL der Startseite in die zuvor veröffentlichte interne URL. Wechseln Sie dazu zu „Azure Active Directory -&gt; App-Registrierungen -&gt; Anwendung auswählen -&gt; Eigenschaften“. Auf der Registerkarte „Eigenschaften“ befindet sich das Feld „URL der Startseite“, in dem Sie die gewünschte Startseite eingeben können.

2.  Wenn Ihre Anwendung vollqualifizierte Domänennamen (FQDNs) verwendet, verwenden Sie [Benutzerdefinierte Domänen](application-proxy-configure-custom-domain.md), um Ihre Anwendungen zu veröffentlichen. Durch dieses Feature kann dieselbe URL sowohl intern als auch extern verwendet werden.

    Mit dieser Option wird sichergestellt, dass die Links in der Anwendung extern über den Anwendungsproxy zugänglich sind, da die Links mit internen URLs in der Anwendung auch extern erkannt werden. Alle Links müssen dennoch zu einer veröffentlichten Anwendung gehören. Mit dieser Option müssen die Links jedoch nicht zur gleichen Anwendung gehören und können mehreren Anwendungen angehören.

3.  Wenn keine dieser Optionen möglich ist, stehen mehrere Optionen zum Aktivieren der Inlinelinkübersetzung zur Verfügung. Dazu gehören die Verwendung des Intune Managed Browser, der Erweiterung „Meine Apps“ oder der Einstellung zur Linkübersetzung in Ihrer Anwendung. Weitere Informationen zu diesen Optionen und ihrer Aktivierung finden Sie unter [Umleiten von hartcodierten Links für Apps, die mit Azure AD-Anwendungsproxy veröffentlicht wurden](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Nächste Schritte
[Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md)

