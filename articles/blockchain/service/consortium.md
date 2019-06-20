---
title: Azure Blockchain-Dienstkonsortium
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027618"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain-Dienstkonsortium

Mit dem Azure Blockchain-Dienst können Sie private Konsortium-Blockchainnetzwerke erstellen, bei denen jedes Blockchainnetzwerk auf bestimmte Teilnehmer im Netzwerk beschränkt werden kann. Nur Teilnehmer des privaten Konsortium-Blockchainnetzwerks können die Blockchain-Komponenten einsehen und mit ihren interagieren. Konsortium-Netzwerke im Azure Blockchain-Dienst können zwei Arten von Mitgliederteilnehmerrollen enthalten:

* **Administrator** – Privilegierte Teilnehmer, die Verwaltungsmaßnahmen im Konsortium ergreifen und an Blockchaintransaktionen teilnehmen können.

* **Benutzer** – Teilnehmer, die keine Verwaltungsmaßnahmen im Konsortium ergreifen dürfen, aber an Blockchaintransaktionen teilnehmen können.

Konsortiumnetzwerke können eine Mischung aus Teilnehmerrollen sein und können eine beliebige Anzahl von jedem Rollentyp haben. Es muss mindestens einen Administrator geben.

Das folgende Diagramm zeigt ein Konsortiumnetzwerk mit mehreren Teilnehmern:

![Diagramm eines privaten Konsortiumnetzwerks](./media/consortium/network-diagram.png)

Mit der Konsortiumsverwaltung im Azure Blockchain Service können Sie Teilnehmer im Konsortiumsnetzwerk verwalten. Die Verwaltung des Konsortiums basiert auf dem Konsensmodell des Netzwerks. In der aktuellen Vorschauversion bietet der Azure Blockchain-Dienst ein zentralisiertes Konsensmodell für die Konsortiumsverwaltung. Jeder privilegierte Teilnehmer mit einer Verwaltungsrolle kann Maßnahmen zur Verwaltung des Konsortiums ergreifen, wie z.B. das Hinzufügen oder Entfernen von Teilnehmern aus einem Netzwerk.

## <a name="roles"></a>Rollen

Teilnehmer an einem Konsortium können Einzelpersonen oder Organisationen sein und können einer Benutzerrolle oder einer Administratorrolle zugeordnet werden. Die folgende Tabelle zeigt die allgemeinen Unterschiede zwischen den zwei Rollen:

| Aktion | Benutzerrolle | Administratorrolle
|--------|:----:|:------------:|
| Neues Mitglied erstellen | Ja | Ja |
| Neues Mitglied einladen | Nein | Ja |
| Mitgliederteilnehmerrollen festlegen oder ändern | Nein | Ja |
| Anzeigename von Mitgliedern ändern | Nur für eigenes Mitglied | Nur für eigenes Mitglied |
| Entfernen von Mitgliedern | Nur für eigenes Mitglied | Ja |
| An Blockchaintransaktionen teilnehmen | Ja | Ja |

### <a name="user-role"></a>Benutzerrolle

Benutzer sind Konsortiumsmitglieder ohne Administratorrechte. Sie können nicht an der Verwaltung von Mitgliedern teilnehmen, die mit dem Konsortium verbunden sind. Benutzer können ihren Anzeigenamen ändern und sich aus einem Konsortium entfernen.

### <a name="administrator"></a>Administrator

Ein Administrator verwalten kann Mitglieder innerhalb des Konsortiums verwalten. Ein Administrator kann Mitglieder einladen, Mitglieder entfernen oder Mitgliederrollen innerhalb des Konsortiums aktualisieren.
In einem Konsortium muss es immer mindestens einen Administrator geben. Der letzte Administrator muss einen anderen Teilnehmer als Administratorrolle angeben, bevor er ein Konsortium verlässt.

## <a name="managing-members"></a>Verwalten von Mitgliedern

Nur Administratoren können andere Teilnehmer in das Konsortium einladen. Administratoren laden Teilnehmer mit ihrer Azure-Abonnement-ID ein.

Nach der Einladung können die Teilnehmer dem Blockchainkonsortium beitreten, indem sie ein neues Mitglied im Azure Blockchain-Dienst einsetzen. Um das eingeladene Konsortium anzuzeigen und ihm beizutreten, müssen Sie die gleiche Azure-Abonnement-ID angeben, die der Netzwerkadministrator in der Einladung verwendet hat.

Administratoren können jeden Teilnehmer aus dem Konsortium entfernen, auch andere Administratoren. Mitglieder können nur sich selbst aus einem Konsortium entfernen.

## <a name="consortium-management-smart-contract"></a>Smart Contract für Konsortiumsverwaltung

Die Konsortiumsverwaltung im Azure Blockchain-Dienst erfolgt per Smart Contracts für die Konsortiumsverwaltung. Die Smart Contracts werden automatisch auf Ihre Knoten bereitgestellt, wenn Sie ein neues Blockchainmitglied bereitstellen.

Die Adresse des Smart Contract für die Stammkonsortiumsverwaltung kann im Azure-Portal eingesehen werden. Die **RootContract-Adresse** befindet sich im Übersichtsbereich des Blockchainmitglieds.

![RootContract-Adresse](./media/consortium/rootcontract-address.png)

Sie können mit dem Smart Contract der Konsortiumsverwaltung über das [PowerShell-Modul](manage-consortium-powershell.md) der Konsortiumsverwaltung, das Azure-Portal oder direkt über den Smart Contract über das vom Azure Blockchain-Dienst generierte Ethereum-Konto interagieren.

## <a name="ethereum-account"></a>Ethereum-Konto

Wenn ein Mitglied erstellt wird, wird ein Ethereum-Kontoschlüssel erstellt. Der Azure Blockchain-Dienst verwendet den Schlüssel, um Transaktionen im Zusammenhang mit der Konsortiumsverwaltung zu erstellen. Der Ethereum Kontoschlüssel wird vom Azure Blockchain-Dienst automatisch verwaltet.

Das Mitgliedskonto kann im Azure-Portal eingesehen werden. Das Mitgliedskonto befindet sich im Übersichtsbereich des Blockchainmitglieds.

![Mitgliedskonto](./media/consortium/member-account.png)

Sie können Ihr Ethereum-Konto zurücksetzen, indem Sie auf Ihr Mitgliedskonto klicken und ein neues Passwort eingeben. Sowohl die Ethereum-Kontoadresse als auch das Kennwort werden zurückgesetzt.  

## <a name="next-steps"></a>Nächste Schritte

[Verwalten der Mitglieder im Azure Blockchain-Dienst mithilfe von PowerShell](manage-consortium-powershell.md)
