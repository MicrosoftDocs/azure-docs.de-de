---
title: Löschen von Marketplace-Angeboten | Azure Marketplace
description: Löschen von Angeboten in Azure Marketplace und AppSource über das Cloud-Partnerportal
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pabutler
ms.openlocfilehash: 542bc611ac8820385c3f749aa151afc1dcdf6db5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942135"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Löschen von Azure Marketplace- und AppSource-Angeboten oder -SKUs

Aus verschiedenen Gründen können Sie sich dafür entscheiden, Ihr Angebot vom Microsoft Marketplace zurückzuziehen. Das kann auf zwei Arten geschehen:

- Durch das *Entfernen eines Angebots* wird sichergestellt, dass Ihr Angebot nicht mehr durch neue Kunden erworben oder bereitgestellt werden kann. Dies wirkt sich jedoch nicht auf bestehende Kunden aus, denen Sie weiterhin Support entsprechend Ihrem Lizenzvertrag und gemäß geltenden Gesetzen bereitstellen müssen. 
- Durch *Beenden des Angebots* wird der Dienst und/oder der Lizenzvertrag zwischen Ihnen und Ihren bestehenden Kunden beendet. 

Eine Anleitung und Richtlinien zur Entfernung und Beendigung von Angeboten finden Sie im [Microsoft Marketplace-Herausgebervertrag](https://go.microsoft.com/fwlink/?LinkID=699560) und in den [Teilnahmerichtlinien](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (Abschnitt [Aussetzen und Entfernen von Angeboten](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

In diesem Artikel werden die unterschiedlichen unterstützten Szenarien zum Löschen und die dazu ausgeführten Schritte behandelt.  

> [!NOTE]
> Ein nicht veröffentlichtes Angebot können Sie löschen, indem Sie einfach die Schaltfläche **Löschen** in der Symbolleiste der Registerkarte **Editor** auswählen.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Löschen einer veröffentlichten SKU aus dem Azure Marketplace

Mit den folgenden Schritten können Sie eine veröffentlichte SKU im Azure Marketplace löschen:

1.  Melden Sie sich beim [Cloudpartnerportal](https://cloudpartner.azure.com/) an.
2.  Wählen Sie Ihr Angebot auf der Seite **Alle Angebote** aus.  Ihr Angebot sollte auf der Registerkarte **Editor** angezeigt werden.
3.  Wählen Sie auf der linken Symbolleiste die Registerkarte **SKUs** aus. 
4.  Wählen Sie die SKU aus, die Sie löschen möchten, und klicken Sie auf die Schaltfläche **Löschen**.
5.  [Veröffentlichen Sie das Angebot erneut](./cpp-publish-offer.md) im Azure Marketplace.

Nachdem das geänderte Angebot auf dem Azure Marketplace veröffentlicht wurde, wird die ausgewählte SKU nicht mehr im Azure Marketplace und Azure-Portal gelistet.


## <a name="roll-back-to-a-previous-sku-version"></a>Ausführen eines Rollbacks zu einer vorherigen SKU-Version

Sie können die aktuelle Version einer veröffentlichten SKU mit den hier beschriebenen Schritten im Azure Marketplace löschen. Nachdem der Vorgang abgeschlossen ist, wird die SKU auf die vorherige Version zurückgesetzt.

1. Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an.
2. Wählen Sie Ihr Angebot auf der Seite **Alle Angebote** aus.  Ihr Angebot sollte auf der Registerkarte **Editor** angezeigt werden.
3. Wählen Sie auf der linken Symbolleiste die Registerkarte **SKUs** aus. 
4. Löschen Sie die neueste Version des zugehörigen Lösungsobjekts aus der Liste der Disk-Versionen.  Je nach Angebotstyp kann dieses Feld **Disk-Versionen**, **Paketversionen** oder ein ähnliches Objekt sein. 
5. [Veröffentlichen Sie das Angebot erneut](./cpp-publish-offer.md) im Azure Marketplace.

Nachdem das geänderte Angebot auf dem Azure Marketplace veröffentlicht wurde, wird die aktuelle Version der gelisteten SKU nicht mehr gelistet. im Azure Marketplace und im Azure-Portal.  Die SKU wird auf die vorherige Version zurückgesetzt.


## <a name="delete-a-live-offer"></a>Löschen eines Liveangebots

Es gibt verschiedene verfahrenstechnische, geschäftliche und rechtliche Aspekte bei der Entfernung eines Liveangebots. Führen Sie die folgenden Schritte aus, um vom Supportteam Hilfe beim Entfernen eines Liveangebots aus dem Azure Marketplace zu erhalten:

1.  Erstellen Sie auf der Seite [Vorfall erstellen](https://go.microsoft.com/fwlink/?linkid=844975) oder durch Klicken auf **Support** in der rechten oberen Ecke des [Cloud-Partnerportals](https://cloudpartner.azure.com/) ein Supportticket.

2.  Wählen Sie den spezifischen Angebotstyp in der Liste **Problemtyp** und dann **Veröffentlichtes Angebot entfernen** in der Liste **Kategorie** aus.

3.  Senden Sie die Anforderung.

Das Supportteam führt Sie durch den Vorgang zum Löschen des Angebots.

> [!NOTE]
> Löschen eines Angebots oder einer SKU wirkt sich nicht auf den aktuelle Käufe dieses Angebots bzw. der SKU aus. Diese Käufe funktionieren weiterhin wie zuvor. Gelöschte Angebote oder SKUs sind jedoch für zukünftige Käufe nicht mehr verfügbar.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie mit den grundlegenden Funktionen zur Verwaltung von Angeboten vertraut sind, können Sie eine Instanz eines [Microsoft Marketplace-Angebots](../cpp-marketplace-offers.md) erstellen.
