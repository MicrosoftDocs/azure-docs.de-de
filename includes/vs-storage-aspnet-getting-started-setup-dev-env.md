---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177992"
---
## <a name="set-up-the-development-environment"></a>Einrichten der Entwicklungsumgebung

Dieser Abschnitt führt Sie durch das Einrichten der Entwicklungsumgebung. Dies umfasst das Erstellen einer ASP.NET MVC-App, das Hinzufügen einer Verbindung mit verbundenen Diensten, das Hinzufügen eines Controllers und das Angeben der erforderlichen Namespacedirektiven.

### <a name="create-an-aspnet-mvc-app-project"></a>Erstellen einer ASP.NET MVC-App

1. Öffnen Sie Visual Studio.

1. Wählen Sie im Hauptmenü **Datei** > **Neu** > **Projekt** aus.

1. Wählen Sie im Dialogfeld **Neues Projekt** die Optionen **Web** > **ASP.NET-Webanwendung (.NET Framework)** aus. Geben Sie im Feld **Name** den Wert **StorageAspNet** an. Klicken Sie auf **OK**.

    ![Screenshot: Dialogfeld „Neues Projekt“](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Wählen Sie im Dialogfeld **Neue ASP.NET-Webanwendung** die Option **MVC** aus, und klicken Sie dann auf **OK**.

    ![Screenshot: Dialogfeld „Neue ASP.NET-Webanwendung“](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Herstellen einer Verbindung mit einem Azure-Speicherkonto über verbundene Dienste

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.

1. Wählen Sie im Kontextmenü **Hinzufügen** > **Verbundener Dienst** aus.

1. Wählen Sie im Dialogfeld **Verbundene Dienste** die Option **Cloudspeicher mit Azure Storage** aus.

    ![Screenshot: Dialogfeld „Verbundene Dienste“](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Wählen Sie im Dialogfeld **Azure Storage** das Azure-Speicherkonto aus, das für dieses Tutorial verwendet werden soll. Um ein neues Azure-Speicherkonto zu erstellen, wählen Sie **Neues Azure-Speicherkonto erstellen** aus, und füllen Sie das Formular aus. Wählen Sie **Hinzufügen** aus, nachdem Sie entweder ein vorhandenes Speicherkonto ausgewählt oder ein neues Speicherkonto erstellt haben. Visual Studio installiert das NuGet-Paket für Azure Storage und eine Speicherverbindungszeichenfolge in **Web.config**.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Abhängigkeiten**, wählen Sie **NuGet-Pakete verwalten** aus, und fügen Sie der neuesten Version von „Microsoft.Azure.ConfigurationManager“ einen NuGet-Paketverweis hinzu.

> [!TIP]
> Informationen zum Erstellen eines Speicherkontos mit dem [Azure-Portal](https://portal.azure.com) finden Sie unter [Informationen zu Azure-Speicherkonten](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Sie können ein Azure-Speicherkonto auch mit [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md) oder [Azure Cloud Shell](../articles/cloud-shell/overview.md) erstellen.
