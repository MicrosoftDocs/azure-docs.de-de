---
title: Erstellen eines Azure Blockchain-Diensts über das Azure-Portal
description: Verwenden Sie Azure Blockchain zum Erstellen eines Konsortiumsmitglieds.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 5cb236dc38ae41b202004b7e4806d8129378cfdb
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417495"
---
# <a name="quickstart-create-an-azure-blockchain-service-using-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Blockchain-Diensts über das Azure-Portal

Bei Azure Blockchain handelt es sich um eine Blockchainplattform, über die Sie Ihre Geschäftslogik in einem Smart Contract ausführen können. In dieser Schnellstartanleitung wird gezeigt, wie Sie zum Einstieg einen verwalteten Ledger über das Azure-Portal erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-managed-ledger"></a>Erstellen eines verwalteten Ledgers

Ein Azure Blockchain-Dienst wird mit einer definierten Gruppe von Compute- und Speicherressourcen erstellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.
1. Wählen Sie **Blockchain** > **Azure Blockchain Service** aus.
1. Vervollständigen Sie die Vorlage.

    ![Erstellen des Dienstes](./media/create-member/create-member.png)

    Einstellung | BESCHREIBUNG
    --------|------------
    Blockchainmitglied | Wählen Sie einen eindeutigen Namen, der Ihr Azure Blockchain-Mitglied identifiziert. Der Name des Blockchainmitglieds darf nur Kleinbuchstaben und Zahlen enthalten. Das erste Zeichen muss ein Buchstabe sein. Der Wert muss zwischen 2 und 20 Zeichen umfassen.
    Abonnement | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren Dienst verwenden möchten. Falls Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, über das die Ressource abgerechnet wird.
    Ressourcengruppe | Ein neuer Ressourcengruppenname oder ein bereits vorhandener Name aus Ihrem Abonnement
    Region | Der Speicherort muss für alle Mitglieder des Konsortiums identisch sein.
    Mitgliedskontokennwort | Das Mitgliedskontokennwort wird zum Verschlüsseln des privaten Schlüssels für das Ethereum-Konto verwendet, das für Ihr Mitglied erstellt wird. Sie verwenden das Mitgliedskonto und das Mitgliedskontokennwort für die Verwaltung des Konsortiums.
    Konsortiumsname | Geben Sie für ein neues Konsortium einen eindeutigen Namen ein. Wenn Sie einem Konsortium über eine Einladung beitreten, entspricht der Wert dem Konsortium, dem Sie beitreten.
    BESCHREIBUNG | Beschreibung des Konsortiums.
    Protocol |  Die Vorschauversion unterstützt das Quorum-Protokoll.
    Preise | Die Knotenkonfiguration für Ihren neuen Dienst. Wählen Sie **Standard** aus. Die Standardeinstellung umfasst zwei Validierungsknoten und einen Transaktionsknoten.
    Kennwort des Transaktionsknotens | Das Kennwort für den Standardtransaktionsknoten des Mitglieds. Verwenden Sie das Kennwort für die Standardauthentifizierung, wenn Sie eine Verbindung mit dem öffentlichen Endpunkt des Standardtransaktionsknotens des Blockchainmitglieds herstellen.

1. Wählen Sie **Erstellen** aus, um den Dienst bereitzustellen. Die Bereitstellung dauert ungefähr 10 Minuten.
1. Klicken Sie in der Symbolleiste auf **Benachrichtigungen**, um den Bereitstellungsprozess zu überwachen.
1. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie zu Ihrem Blockchainmitglied.

Klicken Sie auf **Übersicht**, um die grundlegenden Informationen über Ihren Dienst, einschließlich der RootContract-Adresse und des Mitgliedskontos, anzuzeigen.

![Blockchainmitglieder: Übersicht](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Das von Ihnen erstellte Mitglied können Sie für die nächste Schnellstartanleitung oder das nächste Tutorial verwenden. Wenn Ressourcen nicht mehr benötigt werden, können Sie diese löschen, indem Sie die `myResourceGroup`-Ressourcengruppe löschen, die Sie mit Azure Blockchain erstellt haben.

So löschen Sie die Ressourcengruppe:

1. Navigieren Sie im Azure-Portal im linken Navigationsbereich zu **Ressourcengruppe**, und wählen Sie die Ressourcengruppe aus, die gelöscht werden soll.
2. Wählen Sie die Option **Ressourcengruppe löschen**. Überprüfen Sie den Löschvorgang, indem Sie den Ressourcengruppennamen eingeben und auf **Löschen** klicken.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden von MetaMask zum Verbinden und Bereitstellen eines Smart Contracts](connect-metamask.md)