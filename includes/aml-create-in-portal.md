---
title: include file
description: include file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/21/2019
ms.openlocfilehash: 72f23b10047928f32886d9054f4dd1abdc569bd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66396930"
---
1. Melden Sie sich mit den Anmeldeinformationen, die Sie für das Azure-Abonnement verwenden, beim [Azure-Portal](https://portal.azure.com/) an. 

   ![Azure-Portal](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. Wählen Sie links oben im Portal **Ressource erstellen** aus.

   ![Erstellen einer Ressource im Azure-Portal](./media/aml-create-in-portal/portal-create-a-resource-05-2019.png)

1. Geben Sie in der Suchleiste **Machine Learning** ein. Wählen Sie das Suchergebnis **Machine Learning-Dienstarbeitsbereich** aus.

   ![Suchen nach einem Arbeitsbereich](./media/aml-create-in-portal/allservices-search-05-2019.png)

1. Wählen Sie im Bereich **ML-Dienstarbeitsbereich** die Option **Erstellen** aus, um zu beginnen.

    ![Schaltfläche „Erstellen“](./media/aml-create-in-portal/portal-create-button-05-2019.png)

1. Konfigurieren Sie Ihren Arbeitsbereich im Bereich **ML-Dienstarbeitsbereich**.

   Feld|BESCHREIBUNG
   ---|---
   Arbeitsbereichname |Geben Sie einen eindeutigen Namen ein, der Ihren Arbeitsbereich identifiziert. In diesem Beispiel verwenden wir **docs-ws**. Namen müssen in der Ressourcengruppe eindeutig sein. Verwenden Sie einen Namen, der leicht zu merken ist und sich von den von anderen Benutzern erstellten Arbeitsbereichen unterscheidet.  
   Abonnement |Wählen Sie das gewünschte Azure-Abonnement aus.
   Ressourcengruppe | Verwenden Sie eine vorhandene Ressourcengruppe in Ihrem Abonnement, oder geben Sie einen Namen ein, um eine neue Ressourcengruppe zu erstellen. Eine Ressourcengruppe enthält verwandte Ressourcen für eine Azure-Lösung. In diesem Beispiel verwenden wir **docs-aml**. 
   Location | Wählen Sie den Standort aus, der Ihren Benutzern und den Datenressourcen am nächsten ist. Dort wird der Arbeitsbereich erstellt.

   ![Arbeitsbereich erstellen](./media/aml-create-in-portal/workspace-create-main-tab-05-2019.png)

1. Wählen Sie **Überprüfen und erstellen** aus, um den Erstellungsprozess zu starten.

    ![Erstellen](./media/aml-create-in-portal/workspace-create-main-review-button-05-2019.png)

1. Überprüfen Sie Ihre Arbeitsbereichskonfiguration. Ist sie korrekt, wählen Sie **Erstellen** aus. Es kann einige Augenblicke dauern, bis der Arbeitsbereich erstellt wurde.

    ![Erstellen](./media/aml-create-in-portal/workspace-create-review-tab-05-2019.png)

1. Um den Status der Bereitstellung zu überprüfen, wählen Sie das Benachrichtigungssymbol (**Glocke**) in der Symbolleiste aus.

1. Wenn der Vorgang abgeschlossen ist, wird eine Erfolgsmeldung zur Bereitstellung angezeigt. Diese finden Sie auch im Abschnitt „Benachrichtigungen“. Um den neuen Arbeitsbereich anzuzeigen, wählen Sie **Zu Ressource wechseln** aus.

   ![Status der Arbeitsbereichserstellung](./media/aml-create-in-portal/notifications-05-2019.png)
