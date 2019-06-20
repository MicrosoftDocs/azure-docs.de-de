---
title: Anmelden bei Azure Notebooks
description: Konfigurieren Sie Ihr Benutzerkonto für Azure Notebooks mithilfe eines Microsoft-Kontos oder eines Geschäfts-, Schul- oder Unikontos.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: kraigb
ms.openlocfilehash: 311f4d612c9deab23e7537b0c53ff3932c312cd4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60632056"
---
# <a name="your-user-account-for-azure-notebooks"></a>Ihr Benutzerkonto für Azure Notebooks

Sie können Azure Notebooks mit oder ohne Anmeldung mit einem Benutzerkonto verwenden:

- Ohne Anmeldung können Sie Notebooks erstellen und ausführen, Notebooks oder Datendateien bleiben aber nicht als Teil von Projekten erhalten. Benutzer, die einen Link zu einem Azure Notebook erhalten, können beispielsweise das Notebook nutzen, ohne sich anmelden zu müssen.
- Wenn Sie angemeldet sind, bewahrt Azure Notebooks alle Ihre Projekte unter Ihrem Konto auf. Angemeldete Benutzer verfügen darüber hinaus über eine Benutzer-ID, die es ihnen erlaubt, ihre Projekte und Notebooks mit anderen Personen zu teilen.
  - Wenn das für Azure Notebooks verwendete Konto außerdem einem Azure-Abonnement zugeordnet ist, können Sie zusätzliche Vorteile genießen, z. B. das Ausführen von Notebooks auf leistungsstärkeren Servern, das Erstellen privater Notebooks und das Erteilen von Berechtigungen für Notebooks für einzelne Benutzer.

Für das Anmelden bei Azure Notebooks ist entweder ein Microsoft-Konto oder ein Geschäfts-, Schul- oder Unikonto erforderlich. Sie werden zur Eingabe Ihres Kontos aufgefordert, wenn Sie den Befehl **Anmelden** in der rechten oberen Ecke der Notebookseite auswählen:

![Befehl „Anmelden“ für Azure Notebooks](media/accounts/sign-in-command.png)

Die gesamte Arbeit, die Sie in Azure Notebooks verrichten, ist mit dem Konto verknüpft, das Sie für die Anmeldung benutzen. Jedem Konto muss außerdem eine eindeutige Benutzer-ID in Ihrem [Benutzerprofil](azure-notebooks-user-profile.md) zugeordnet sein. Daher können Sie sich bei Azure Notebooks mit verschiedenen Konten anmelden, wenn Sie getrennte Sätze von Projekten und separate Identitäten aufrecht erhalten müssen. Beispielsweise könnte jedes Mitglied eines Data Science-Teams neben einem gemeinsam verwendeten Gruppenkonto, das zur Darstellung der Arbeit für Personen außerhalb des Unternehmens verwendet wird, über individuelle Konten verfügen. Analog dazu können Dozenten ein Konto für ihre Lehrtätigkeit unterhalten, das sich von einem für externes Consulting oder Open Source-Aktivitäten verwendeten Konto unterscheidet.

## <a name="microsoft-accounts"></a>Microsoft-Konten

Microsoft-Konten werden zur Anmeldung bei einer beliebigen Anzahl von Microsoft-Produkten und -diensten verwendet, wie etwa Windows, Azure, outlook.com, OneDrive und XBox Live. Wenn Sie einen dieser Dienste verwenden, verfügen Sie wahrscheinlich bereits über ein Microsoft-Konto, das Sie für Azure Notebooks verwenden können.

Wenn Sie sich nicht sicher sind, wählen Sie an der Kontoaufforderung den Befehl **Konto erstellen** aus. Sie können ein neues Microsoft-Konto mithilfe einer beliebigen E-Mail-Adresse eines beliebigen Anbieters erstellen.

![Befehl zum Erstellen eines neuen Microsoft-Kontos](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Wenn Sie versuchen, ein neues Konto mit einer E-Mail-Adresse zu erstellen, der bereits ein Konto zugeordnet ist, wird möglicherweise die Meldung „Sie können sich hier nicht mit einer Geschäfts-, Schul- oder Uni-E-Mail-Adresse anmelden. Verwenden Sie eine persönliche E-Mail-Adresse wie Gmail oder Yahoo!, oder besorgen Sie sich eine neue Outlook-E-Mail.“ angezeigt. Versuchen Sie in diesem Fall, sich mit der Geschäfts-E-Mail-Adresse anzumelden, ohne ein neues Konto zu erstellen.

Für Konten von Kindern ist der Zugriff auf Azure Notebooks standardmäßig gesperrt. Bei einem Anmeldeversuch mit dem Konto eines Kindes wird der unten dargestellte Fehler angezeigt:

![Fehler bei der Anmeldung mit einem Konto eines Kindes: Etwas ist schief gelaufen, und Deine Eltern haben den Zugriff blockiert.](media/accounts/child-account-error.png)

Um den Zugriff zu ermöglichen, muss ein Elternteil die folgenden Schritte ausführen:

1. Suchen Sie `https://account.live.com/mk` auf, und melden Sie sich mit dem Konto eines Elternteils an.
1. Wählen Sie im Abschnitt für das fragliche Kind **Verwalten der Berechtigungen für Drittanbieter-Apps, auf die Ihr Kind zugreifen kann** aus.
1. Wählen Sie auf der nächsten Seite **Zugriff aktivieren** aus.
1. Wenn das Konto des Kindes das nächste Mal zur Anmeldung bei Azure Notebooks verwendet wird, wählen Sie **Ja** in der dann angezeigten Berechtigungsaufforderung aus.

> [!Warning]
> Durch das Aktivieren des Zugriffs auf Apps von Drittanbietern für Azure Notebooks wird auch der Zugriff auf alle anderen Apps von Drittanbietern aktiviert. Eltern sollten beim Aktivieren des Zugriffs umsichtig vorgehen, und es kann ratsam sein, die Aktivitäten des Kindes genauer zu überwachen.

## <a name="work-or-school-accounts"></a>Geschäfts-, Schul- oder Unikonten

Ein Geschäfts-, Schul- oder Unikonto wird vom Administrator einer Organisation erstellt, um einem Mitglied der Organisation den Zugriff auf Microsoft-Clouddienste wie Office 365 oder die Anmeldung bei Windows auf einem Computer zu ermöglichen, der Mitglied einer Domäne ist. Für ein Geschäfts-, Schul- oder Unikonto wird normalerweise die E-Mail-Adresse einer Organisation verwendet, wie z. B. any-user@contoso.com.

Für die Anmeldung bei Azure Notebooks mit einem Geschäfts-, Schul- oder Unikonto ist möglicherweise die Zustimmung des Administrators erforderlich, da Azure Notebooks Informationen wie die E-Mail-Adresse des Kontos und die Browserinformationen des Benutzers sammelt (aber nicht offenlegt). (Browserdaten werden zur Optimierung von Funktionen auf der Grundlage von Benutzerpräferenzen verwendet.)

Der Administrator eines Organisationskontos muss die Zustimmung im Benutzerauftrag erteilen, wenn den Benutzern die individuelle Zustimmung nicht möglich ist. In diesem Fall wird Benutzern die Meldung „Sie können nicht auf diese Anwendung zugreifen“ angezeigt:

![Meldung „Sie können nicht auf diese Anwendung zugreifen“ bei Verwendung eines Geschäfts-, Schul- oder Unikontos](media/accounts/consent-permissions-denied.png)

Um Ihre Zustimmung als Administrator zu erteilen, verwenden Sie die [Administrator-Zustimmungsseite](https://notebooks.azure.com/account/adminConsent), die Sie durch den Vorgang führt.

## <a name="next-steps"></a>Nächste Schritte  

> [!div class="nextstepaction"]
> [Bearbeiten von Profil und Benutzer-ID](azure-notebooks-user-profile.md)
