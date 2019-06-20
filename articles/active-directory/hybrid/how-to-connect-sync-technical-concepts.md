---
title: 'Azure AD Connect-Synchronisierung: Technische Konzepte | Microsoft-Dokumentation'
description: Erläutert die technischen Konzepte der Azure AD Connect-Synchronisierung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ec4a6100cfbb4419d7e30f4b97589113b88939
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347572"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect-Synchronisierung: Technische Konzepte
Dieser Artikel ist eine Zusammenfassung des Themas [Grundlegendes zur Architektur](how-to-connect-sync-technical-concepts.md).

Azure AD Connect-Synchronisierung basiert auf einer zuverlässigen Plattform für die Metaverzeichnissynchronisierung.
In den folgenden Abschnitte werden die Konzepte für die Metaverzeichnissynchronisierung erläutert.
Die auf MIIS, ILM und FIM basierenden Azure Active Directory-Synchronisierungsdienste bieten die nächste Plattform für die Verbindung mit Datenquellen, die Synchronisierung von Daten zwischen Datenquellen und die Bereitstellung von Identitäten sowie deren Aufhebung.

![Technische Konzepte](./media/how-to-connect-sync-technical-concepts/scenario.png)

Die folgenden Abschnitte bieten weitere Details zu den folgenden Aspekten des FIM-Synchronisierungsdiensts:

* Connector
* Attributfluss
* Connectorbereich
* Metaverse
* Bereitstellung

## <a name="connector"></a>Connector
Die zur Kommunikation mit einem verbundenen Verzeichnis verwendeten Codemodule werden „Connectors“ genannt (zuvor als Verwaltungs-Agents (Management Agents, MAs) bezeichnet).

Sie werden auf dem Computer installiert, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird. Mithilfe von Connectors können Konvertierungen ohne Agents mithilfe von Remotesystemprotokollen ausgeführt werden. Damit entfällt die Notwendigkeit, sich auf die Bereitstellung spezialisierter Agents zu verlassen. Dies hat ein vermindertes Risiko und geringere Bereitstellungszeiten zur Folge, insbesondere wenn es um die Arbeit mit kritischen Anwendungen und Systemen geht.

In der obigen Abbildung ist der Connector mit dem Connectorbereich gleichbedeutend, umfasst jedoch die gesamte Kommunikation mit dem externen System.

Der Connector ist für alle Funktionen verantwortlich, die den Import in das System und den Export aus dem System verarbeiten. So müssen Entwickler nicht wissen, welche systemeigenen Informationen notwendig sind, um bei der deklarativen Bereitstellung zum Anpassen von Datentransformationen Verbindungen zu den einzelnen Systemen herzustellen.

Es treten nur geplante Import- und Exportvorgänge auf, was eine weitere Isolierung gegenüber Änderungen ermöglicht, die im System auftreten, da Änderungen nicht automatisch auf die verbundene Datenquelle übertragen werden. Außerdem können Entwickler auch ihre eigenen Connectors erstellen, um sich mit praktisch jeder Datenquelle verbinden zu können.

## <a name="attribute-flow"></a>Attributfluss
Das Metaverse ist die konsolidierte Ansicht sämtlicher verknüpfter Identitäten aus benachbarten Connectorbereichen. In der obigen Abbildung wird der eingehende und ausgehende Attributfluss durch Linien mit Pfeilspitzen dargestellt. Beim Attributfluss handelt es sich um den Vorgang des Kopierens oder Umwandelns von Daten von einem System in ein anderes. Es werden ein- und ausgehende Attributflüsse unterschieden.

Ein Attributfluss tritt bidirektional zwischen Connectorbereich und Metaverse auf, wenn die Ausführung von Synchronisierungsvorgängen (vollständig oder Delta) geplant ist.

Ein Attributfluss tritt nur auf, wenn diese Synchronisierungen ausgeführt werden. Attributflüsse werden in Synchronisierungsregeln definiert. Diese können eingehend (ISR im obigen Bild) oder ausgehend (OSR im obigen Bild) sein.

## <a name="connected-system"></a>Verbundenes System
Verbundenes System (d. h. verbundenes Verzeichnis) bezieht sich auf das Remotesystem, mit dem die Azure AD Connect-Synchronisierung eine Verbindung hergestellt hat und aus dem sie Identitätsdaten liest bzw. in das sie diese Daten schreibt.

## <a name="connector-space"></a>Connectorbereich
Jede verbundene Datenquelle wird als gefilterte Teilmenge der Objekte und Attribute im Connectorbereich dargestellt.
So kann der Synchronisierungsdienst lokal ausgeführt werden, ohne das Remotesystem beim Synchronisieren der Objekte zu kontaktieren, und die Interaktion wird auf Importe und Exporte beschränkt.

Wenn die Datenquelle und der Connector in der Lage sind, eine Liste der Änderungen (einen Deltaimport) bereitzustellen, wird die Betriebseffizienz erheblich gesteigert, da nur seit dem letzten Abrufzyklus vorgenommene Änderungen ausgetauscht werden. Der Connectorbereich isoliert die verbundene Datenquelle von der automatischen Übertragung von Änderungen durch die Anforderung, dass Import- und Exportvorgänge gemäß dem Connectorplan erfolgen. Mit dieser zusätzlichen Versicherung können Sie unbesorgt testen, die Vorschau anzeigen oder das nächste Update bestätigen.

## <a name="metaverse"></a>Metaverse
Das Metaverse ist die konsolidierte Ansicht sämtlicher verknüpfter Identitäten aus benachbarten Connectorbereichen.

Da Identitäten miteinander verknüpft sind und die Autorität für verschiedene Attribute durch Importflusszuordnungen zugewiesen ist, beginnt das zentrale Metaverseobjekt, die Informationen aus mehreren Systemen zu aggregieren. Aus diesem Objektattributfluss sammeln Zuordnungen Informationen zu ausgehenden Systemen.

Objekte werden erstellt, wenn ein autoritatives System sie im Metaverse abbildet. Sobald alle Verbindungen entfernt sind, wird das Metaverseobjekt gelöscht.

Objekte im Metaverse können nicht direkt bearbeitet werden. Alle Daten im Objekt müssen über den Attributfluss beigesteuert werden. Das Metaverse behält persistente Connectors mit jedem Connectorbereich bei. Diese Connectors erfordern keine erneute Auswertung für jede Synchronisierungsausführung. Die Azure AD-Synchronisierung muss demnach nicht jedes Mal nach dem übereinstimmenden Remoteobjekt suchen. So erübrigen sich kostspielige Agents, um Änderungen an Attributen zu verhindern, die normalerweise für das Korrelieren der Objekte verantwortlich wären.

Beim Ermitteln neuer Datenquellen mit möglicherweise vorher vorhandenen Objekten, die verwaltet werden müssen, wendet die Azure AD Connect-Synchronisierung einen als Verknüpfungsregel bezeichneten Vorgang an, um potenzielle Kandidaten auszuwerten, zu denen ein Link hergestellt werden soll.
Nach der Herstellung des Links wird diese Auswertung nicht erneut vorgenommen, und der normale Attributfluss kann zwischen der remote verbundenen Datenquelle und dem Metaverse auftreten.

## <a name="provisioning"></a>Bereitstellung
Wenn eine autoritative Quelle ein neues Objekt im Metaverse abbildet, kann ein neues Connectorbereichsobjekt in einem anderen Connector erstellt werden, der eine per Downstream verbundene Datenquelle darstellt.

So wird inhärent eine Verknüpfung hergestellt, und der Attributfluss kann bidirektional fortgeführt werden.

Wenn eine Regel bestimmt, dass ein neues Connectorbereichsobjekt erstellt werden muss, wird dies als Bereitstellung bezeichnet. Da dieser Vorgang nur im Connectorbereich stattfindet, wird er erst in die verbundene Datenquelle übertragen, wenn ein Export vorgenommen wird.

## <a name="additional-resources"></a>Weitere Ressourcen
* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
