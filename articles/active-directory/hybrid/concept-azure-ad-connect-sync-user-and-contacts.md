---
title: 'Azure AD Connect-Synchronisierung: Grundlegendes zu Benutzern, Gruppen und Kontakten | Microsoft-Dokumentation'
description: Hier werden Benutzer, Gruppen und Kontakte in der Azure AD Connect-Synchronisierung erläutert.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e015f7937db6788aa4473a8a04434121299901e9
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861781"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect-Synchronisierung: Grundlegendes zu Benutzern, Gruppen und Kontakten
Es gibt verschiedene Gründe, weshalb Sie möglicherweise über mehrere Active Directory-Gesamtstrukturen verfügen, und es gibt eine Reihe unterschiedlicher Bereitstellungstopologien. Häufige Modelle umfassen eine Kontoressourcenbereitstellung und per GAL synchronisierte Gesamtstrukturen nach einer Unternehmensfusion oder -übernahme. Es gibt zwar reine Modelle, Hybridmodelle sind jedoch ebenfalls häufig vorhanden. Die Standardkonfiguration der Azure AD Connect-Synchronisierung geht von keinem bestimmten Modell aus. Es können jedoch auf Basis des im Installationshandbuch ausgewählten Benutzerabgleichs unterschiedliche Verhaltensweisen beobachtet werden.

In diesem Thema wird erläutert, wie sich die Standardkonfiguration in bestimmten Topologien verhält. Die Konfiguration wird durchlaufen, und der Synchronisierungsregel-Editor kann verwendet werden, um die Konfiguration anzuzeigen.

Die Konfiguration geht von einigen wenigen allgemeinen Regeln aus:
* Unabhängig davon, in welcher Reihenfolge die Active Directory-Quellen importiert werden, sollte das Endergebnis immer identisch sein.
* Ein aktives Konto trägt immer Anmeldeinformationen bei, einschließlich **userPrincipalName** und **sourceAnchor**.
* Ein deaktiviertes Konto trägt "userPrincipalName" und "sourceAnchor" bei (falls kein aktives Konto gefunden wurde), es sei denn, es handelt sich um ein verknüpftes Postfach.
* Ein Konto mit einem verknüpften Postfach wird niemals für "userPrincipalName" und "sourceAnchor" verwendet. Es wird angenommen, dass später ein aktives Konto gefunden wird.
* Ein Kontaktobjekt wird Azure AD möglicherweise als Kontakt oder Benutzer bereitgestellt. Sie werden dies erst dann genau wissen, wenn alle Active Directory-Quellgesamtstrukturen verarbeitet wurden.

## <a name="groups"></a>Gruppen
Wichtige Punkte, die beim Synchronisieren von Gruppen in Active Directory mit Azure AD beachtet werden sollten:

* Azure AD Connect schließt integrierte Sicherheitsgruppen von der Verzeichnissynchronisierung aus.

* Azure AD Connect unterstützt nicht die Synchronisierung von [Mitgliedschaften in primären Gruppen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11)) mit Azure AD.

* Azure AD Connect unterstützt nicht die Synchronisierung von [Mitgliedschaften in dynamischen Verteilergruppen](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups) mit Azure AD.

* So synchronisieren Sie eine Active Directory-Gruppe mit Azure AD als E-Mail-aktivierte Gruppe:

    * Wenn das *proxyAddress*-Attribut der Gruppe leer ist, muss das *mail*-Attribut einen Wert enthalten.

    * Wenn das *proxyAddress*-Attribut der Gruppe nicht leer ist, muss es mindestens einen SMTP-Proxyadresswert enthalten. Im Folgenden finden Sie einige Beispiele:
    
      * Eine Active Directory-Gruppe, deren proxyAddress-Attribut den Wert *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* hat, ist in Azure AD nicht E-Mail-aktiviert. Es enthält keine SMTP-Adresse.
      
      * Eine Active Directory-Gruppe, deren proxyAddress-Attribut die Werte *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe\@contoso.com"}* aufweist, ist in Azure AD E-Mail-aktiviert.
      
      * Eine Active Directory-Gruppe, deren proxyAddress-Attribut die Werte *{"X500:/0=contoso.com/ou=users/cn=testgroup","smtp:johndoe\@contoso.com"}* aufweist, ist in Azure AD ebenfalls E-Mail-aktiviert.

## <a name="contacts"></a>Kontakte
Nach einer Unternehmensfusion oder -übernahme kommt es häufig vor, dass Kontakte einen Benutzer in einer anderen Gesamtstruktur darstellen und eine GALSynch-Lösung zwei oder mehr Exchange-Gesamtstrukturen verbindet. Das Kontaktobjekt wird immer mithilfe des Attributs "mail" vom Connectorbereich mit dem Metaverse verknüpft. Wenn bereits ein Kontakt- oder Benutzerobjekt mit derselben E-Mail-Adresse vorhanden ist, werden die Objekte miteinander verknüpft. Dies wird in der Regel **In from AD – Contact Join** konfiguriert. Es gibt auch eine Regel namens **In from AD – Contact Common** mit einem Attributfluss zum Metaverseattribut **sourceObjectType** mit der Konstante **Contact**. Diese Regel hat eine sehr niedrige Rangfolge. Wenn daher ein Benutzerobjekt mit demselben Metaverseobjekt verknüpft ist, trägt die Regel **In from AD – User Common** den Wert „User“ zu diesem Attribut bei. Bei dieser Regel weist dieses Attribut den Wert "Contact" auf, wenn kein Benutzer verknüpft wurde, und den Wert "User", wenn mindestens ein Benutzer gefunden wurde.

Für die Bereitstellung eines Objekts für Azure AD erstellt die ausgehende Regel **Out to AAD – Contact Join** ein Kontaktobjekt, wenn das Metaverseattribut **sourceObjectType** auf **Contact** festgelegt ist. Wenn dieses Attribut auf **User** festgelegt ist, erstellt die Regel **Out to AAD – User Join** stattdessen ein Benutzerobjekt.
Es ist möglich, dass ein Objekt von "Contact" zu "User" aufsteigt, wenn weitere Active Directory-Quellen importiert und synchronisiert werden.

In einer GALSync-Topologie finden sich beispielsweise Kontaktobjekte für alle Benutzer in der zweiten Gesamtstruktur, wenn die erste Gesamtstruktur importiert wird. Dadurch werden neue Kontaktobjekte im Azure AD-Connector bereitgestellt. Wenn die zweite Gesamtstruktur später importiert und synchronisiert wird, werden die wirklichen Benutzer gefunden und mit den vorhandenen Metaverseobjekten verbunden. Anschließend wird das Kontaktobjekt in Azure AD gelöscht und stattdessen ein neuer Benutzer erstellt.

Wenn Sie über eine Topologie verfügen, in der Benutzer als Kontakte dargestellt werden, stellen Sie sicher, dass Sie im Installationshandbuch die Option zum Abgleich der Benutzer mit dem mail-Attribut auswählen. Wenn Sie eine andere Option auswählen, haben Sie eine Konfiguration, die von der Reihenfolge abhängig ist. Kontaktobjekte werden immer mit dem mail-Attribut verknüpft. Benutzerobjekte werden nur dann mit dem mail-Attribut verknüpft, wenn diese Option im Installationshandbuch ausgewählt wurde. Dies könnte zwei unterschiedliche Objekte im Metaverse mit demselben mail-Attribut zur Folge haben, wenn das Kontaktobjekt vor dem Benutzerobjekt importiert wurde. Während des Exports nach Azure AD wird ein Fehler ausgegeben. Dieses Verhalten ist beabsichtigt und weist auf ungültige Daten hin oder darauf, dass die Topologie während der Installation nicht richtig ermittelt wurde.

## <a name="disabled-accounts"></a>Deaktivierte Konten
Deaktivierte Konten werden auch mit Azure AD synchronisiert. Deaktivierte Konten werden häufig zum Darstellen von Ressourcen in Exchange verwendet, beispielsweise von Konferenzräumen. Die Ausnahme bilden Benutzer mit einem verknüpften Postfach. Diese stellen, wie dies bereits zuvor erwähnt wurde, niemals ein Konto für Azure AD bereit.

Folgende Annahme gilt: Wenn ein deaktiviertes Benutzerkonto gefunden wird, wird später kein anderes aktives Konto gefunden, und das Objekt wird Azure AD mit den gefundenen Werten für "userPrincipalName" und "sourceAnchor" bereitgestellt. Sollte ein anderes aktives Konto eine Verknüpfung mit demselben Metaverseobjekt herstellen, werden "userPrincipalName" und "sourceAnchor" dieses Kontos verwendet.

## <a name="changing-sourceanchor"></a>Ändern von "sourceAnchor"
Nachdem ein Objekt nach Azure AD exportiert wurde, kann der Wert für "sourceAnchor" nicht mehr geändert werden. Wenn das Objekt exportiert wurde, wird das Metaverseattribut **cloudSourceAnchor** mit dem **sourceAnchor**-Wert festgelegt, der von Azure AD akzeptiert wird. Wenn **sourceAnchor** geändert wird und nicht mit **cloudSourceAnchor** übereinstimmt, löst die Regel **Out to AAD – User Join** den Fehler aus, dass das sourceAnchor-Attribut geändert wurde: **sourceAnchor attribute has changed**. In diesem Fall müssen Konfiguration oder Daten korrigiert werden, sodass derselbe sourceAnchor-Wert wieder im Metaverse vorhanden ist, bevor das Objekt erneut synchronisiert werden kann.

## <a name="additional-resources"></a>Weitere Ressourcen
* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
