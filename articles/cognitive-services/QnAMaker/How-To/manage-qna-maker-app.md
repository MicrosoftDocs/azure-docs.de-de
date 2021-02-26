---
title: Verwalten der QnA Maker-App – QnA Maker
description: QnA Maker ermöglicht mehreren Personen, an einer Wissensdatenbank zusammenzuarbeiten. QnA Maker bietet die Möglichkeit, die Qualität Ihrer Wissensdatenbank durch aktives Lernen zu verbessern. Sie können Einträge überprüfen, annehmen oder ablehnen und hinzufügen, ohne vorhandene Fragen zu entfernen oder zu ändern.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 652489e8de68d61707fd419843f0dbb2ffd83754
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987945"
---
# <a name="manage-qna-maker-app"></a>Verwalten der QnA Maker-App

QnA Maker ermöglicht Ihnen die Zusammenarbeit mit verschiedenen Autoren und Inhalts-Editoren, indem es die Möglichkeit bietet, den Zugriff von Mitarbeitern je nach ihrer Rolle einzuschränken.
Erfahren Sie mehr über [QnA Maker-Konzepte zur Authentifizierung von Projektmitarbeitern](../Concepts/role-based-access-control.md).

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Hinzufügen der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)

Mit QnA Maker können mehrere Personen an allen Wissensdatenbanken in derselben QnA Maker-Ressource zusammenarbeiten. Diese Funktion wird im Rahmen der [rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md) bereitgestellt.

## <a name="access-at-the-qna-maker-resource-level"></a>Zugreifen auf der Ebene der QnA Maker-Ressource

In einem QnA Maker-Dienst kann keine bestimmte Wissensdatenbank freigegeben werden. Sollten Sie eine differenzierte Zugriffssteuerung benötigen, verteilen Sie Ihre Wissensdatenbanken ggf. auf verschiedene QnA Maker-Ressourcen, und fügen Sie ihnen jeweils Rollen hinzu.

## <a name="add-a-role-to-a-resource"></a>Hinzufügen einer Rolle zu einer Ressource

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Hinzufügen eines Benutzerkontos zur QnA Maker-Ressource

In den folgenden Schritten wird zwar die Rolle „Projektmitarbeiter“ verwendet, mit den gezeigten Schritten können jedoch auch andere [Rollen](../reference-role-based-access-control.md) hinzugefügt werden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrer QnA Maker-Ressource.

    ![QnA Maker-Ressourcenliste](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Öffnen Sie die Registerkarte **Zugriffssteuerung (IAM)** .

    ![IAM in QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Wählen Sie **Hinzufügen**.

    ![Hinzufügen von IAM in QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Wählen Sie eine Rolle aus der folgenden Liste aus:

    |Rolle|
    |--|
    |Besitzer|
    |Mitwirkender|
    |Cognitive Services QnA Maker-Leseberechtigter|
    |Cognitive Services QnA Maker-Editor|
    |Cognitive Services-Benutzer|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="Hinzufügen einer IAM-Rolle in QnA Maker.":::

1. Geben Sie die E-Mail-Adresse des Benutzers ein, und klicken Sie auf **Speichern**.

    ![Hinzufügen einer E-Mail-Adresse für IAM in QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Anzeigen von QnA Maker-Wissensdatenbanken

Wenn sich die Person, für die Sie Ihren QnA Maker-Dienst freigegeben haben, beim [QnA Maker-Portal](https://qnamaker.ai) anmeldet, werden ihr basierend auf ihrer Rolle alle Wissensdatenbanken in diesem Dienst angezeigt.

Wenn sie eine Wissensdatenbank auswählen, wird ihre aktuelle Rolle auf dieser QnA Maker-Ressource neben dem Namen der Wissensdatenbank angezeigt.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Screenshot der Wissensdatenbank im Bearbeitungsmodus mit Rollennamen in Klammern neben dem Namen der Wissensdatenbank in der linken oberen Ecke der Webseite.":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Wissensdatenbank](./manage-knowledge-bases.md)
