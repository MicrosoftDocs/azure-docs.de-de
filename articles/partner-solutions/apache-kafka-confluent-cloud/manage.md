---
title: 'Verwalten einer Confluent Cloud-Ressource: Azure-Partnerlösungen'
description: In diesem Artikel wird die Verwaltung einer Confluent Cloud-Ressource im Azure-Portal beschrieben. Hier erfahren Sie, wie Sie das einmalige Anmelden einrichten, eine Confluent-Organisation löschen und Support anfordern.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989134"
---
# <a name="manage-the-confluent-cloud-resource"></a>Verwalten der Confluent Cloud-Ressource

In diesem Artikel wird beschrieben, wie Sie Ihre Instanz von Apache Kafka für Confluent Cloud in Azure verwalten. Es wird gezeigt, wie Sie das einmalige Anmelden (Single Sign-on, SSO) einrichten, eine Confluent-Organisation löschen und eine Supportanfrage erstellen.

## <a name="single-sign-on"></a>Einmaliges Anmelden

Um das einmalige Anmelden für Ihr Unternehmen zu implementieren, kann Ihr Mandantenadministrator die Kataloganwendung importieren. Dieser Schritt ist optional. Informationen zum Importieren einer Anwendung finden Sie unter [Schnellstart: Hinzufügen einer Anwendung zu Ihrem Azure Active Directory (Azure AD)-Mandanten](../../active-directory/manage-apps/add-application-portal.md). Wenn der Mandantenadministrator die Anwendung importiert, müssen Benutzer nicht explizit zustimmen, um den Zugriff für das Confluent Cloud-Portal zuzulassen.

Gehen Sie folgendermaßen vor, um das einmalige Anmelden zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zur **Übersicht** für Ihre Instanz der Confluent Cloud-Ressource.
1. Wählen Sie den Link **Manage on Confluent Cloud** (In Confluent Cloud verwalten) aus.

   :::image type="content" source="media/sso-link.png" alt-text="Einmaliges Anmelden im Confluent-Portal":::

1. Wenn der Mandantenadministrator die Kataloganwendung für die Zustimmung zum einmaligen Anmelden nicht importiert hat, gewähren Sie Berechtigungen und erteilen eine Zustimmung. Dieser Schritt wird nur benötigt, wenn Sie zum ersten Mal auf den Link **Manage on Confluent Cloud** zugreifen.
1. Wählen Sie ein Azure AD-Konto für das einmalige Anmelden beim Confluent Cloud-Portal aus.
1. Nachdem die Zustimmung erteilt wurde, werden Sie zum Confluent Cloud-Portal weitergeleitet.

## <a name="set-up-cluster"></a>Einrichten eines Clusters

Weitere Informationen zum Einrichten des Clusters finden Sie in der Confluent-Dokumentation unter [Create a Cluster in Confluent Cloud](https://docs.confluent.io/cloud/current/clusters/create-cluster.html) (Erstellen eines Clusters in Confluent Cloud).

## <a name="delete-confluent-organization"></a>Löschen einer Confluent-Organisation

Wenn Sie Ihre Confluent Cloud-Ressource nicht mehr benötigen, löschen Sie die Ressource in Azure und in Confluent Cloud.

So löschen Sie die Ressourcen in Azure:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Alle Ressourcen** und **Nach Name filtern** für die Confluent Cloud-Ressource oder den **Ressourcentyp** _Confluent organization_ (Confluent-Organisation) aus. Alternativ können Sie im Azure-Portal nach dem Ressourcennamen suchen.
1. Wählen Sie in der **Übersicht** zur Ressource **Löschen** aus.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Ressource – Löschsymbol":::

1. Geben Sie den Namen der Ressource ein, und wählen Sie **Löschen** aus, um den Löschvorgang zu bestätigen.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Aufforderung, die Löschung der Ressource zu bestätigen":::

Informationen zum Löschen der Ressource in Confluent Cloud finden Sie in der Confluent-Dokumentation unter [Confluent Cloud Environments](https://docs.confluent.io/current/cloud/using/environments.html) (Confluent Cloud-Umgebungen) und unter [Confluent Cloud Basics](https://docs.confluent.io/current/cloud/using/cloud-basics.html) (Grundlagen zu Confluent Cloud).

Der Cluster und alle Daten im Cluster werden dauerhaft gelöscht. Wenn Ihr Vertrag eine Klausel für die Datenaufbewahrung enthält, werden Ihre Daten von Confluent für den Zeitraum beibehalten, der in der Confluent-Dokumentation unter [Terms of Service](https://www.confluent.io/confluent-cloud-tos) (Vertragsbedingungen) angegeben ist.

Die Nutzung wird Ihnen anteilig bis zum Zeitpunkt der Clusterlöschung in Rechnung gestellt. Nachdem der Cluster dauerhaft gelöscht wurde, erhalten Sie von Confluent eine Bestätigung per E-Mail.

## <a name="get-support"></a>Support

Um eine Supportanfrage an Confluent zu übermitteln, wenden Sie sich entweder an den [Confluent-Support](https://support.confluent.io) oder übermitteln eine Anfrage über das Portal, wie unten dargestellt.

> [!NOTE]
> Für erstmalige Benutzer: Setzen Sie Ihr Kennwort zurück, bevor Sie sich beim Confluent-Supportportal anmelden. Wenn Sie kein Confluent Cloud-Konto haben, senden Sie eine E-Mail an `cloud-support@confluent.io`, um weitere Unterstützung zu erhalten.

Im Portal können Sie eine Anforderung entweder über „Hilfe und Support“ oder direkt über Ihre Instanz von Apache Kafka on Confluent Cloud in Azure übermitteln.

So übermitteln Sie eine Anforderung über „Hilfe und Support“:

1. Wählen Sie **Hilfe und Support**.
1. Klicken Sie auf **Supportanfrage erstellen**.
1. Wählen Sie im Formular für **Problemtyp** die Option **Technisch** aus. Wählen Sie Ihr Abonnement aus. Wählen Sie in der Liste der Dienste **Confluent on Azure** aus.

    :::image type="content" source="media/support-request-help.png" alt-text="Erstellen einer Supportanfrage über „Hilfe und Support“":::

Führen Sie die folgenden Schritte aus, um eine Anforderung von Ihrer Ressource zu übermitteln:

1. Wählen Sie im Azure-Portal Ihre Confluent-Organisation aus.
1. Wählen Sie im Menü links im Bildschirm **Neue Supportanfrage** aus.
1. Um eine Supportanfrage zu erstellen, wählen Sie den Link zum **Confluent-Portal** aus.

    :::image type="content" source="media/support-request.png" alt-text="Erstellen einer Supportanfrage über eine Instanz":::

## <a name="next-steps"></a>Nächste Schritte

Hilfe bei der Problembehandlung finden Sie unter [Troubleshooting Apache Kafka for Confluent Cloud solutions](troubleshoot.md) (Problembehandlung in Apache Kafka für Confluent Cloud-Lösungen).
