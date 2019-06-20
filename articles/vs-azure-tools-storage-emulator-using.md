---
title: Konfigurieren und Verwenden des Speicheremulators mit Visual Studio | Microsoft Docs
description: Konfigurieren und Verwenden des Speicheremulators mit Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: 39e2071a62d6a1f6ee050f862856815048e50430
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128295"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurieren und Verwenden des Speicheremulators mit Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Übersicht
Die Azure SDK-Entwicklungsumgebung umfasst den Speicheremulator, ein Hilfsprogramm, das die in Azure verfügbaren Blob-, Warteschlangen- und Tabellenspeicherdienste auf dem lokalen Entwicklungscomputer simuliert. Wenn Sie einen Clouddienst erstellen, für den die Azure-Speicherdienste verwendet werden, oder wenn Sie eine beliebige externe Anwendung schreiben, mit der die Speicherdienste aufgerufen werden, können Sie den Code lokal mit dem Speicheremulator testen. Die Azure Tools für Microsoft Visual Studio integrieren die Verwaltung des Speicheremulators in Visual Studio. Mit den Azure Tools wird die Speicheremulator-Datenbank bei der ersten Verwendung initialisiert, der Speicheremulatordienst wird beim Ausführen oder Debuggen von Code von Visual Studio aus gestartet, und es wird schreibgeschützter Zugriff auf die Speicheremulatordaten über den Azure Storage-Explorer bereitgestellt.

Ausführliche Informationen zum Speicheremulator, einschließlich der Systemanforderungen und der benutzerdefinierten Konfigurationsanweisungen, finden Sie unter [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](storage/common/storage-use-emulator.md).

> [!NOTE]
> Es bestehen einige Unterschiede hinsichtlich der Funktionalität zwischen der Speicheremulatorsimulation und den Azure-Speicherdiensten. Informationen zu den spezifischen Unterschieden finden Sie in der Azure SDK-Dokumentation unter [Unterschiede zwischen dem Speicheremulator und den Azure-Speicherdiensten](storage/common/storage-use-emulator.md).
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurieren einer Verbindungszeichenfolge für den Speicheremulator
Wenn Sie von Code in einer Rolle aus auf den Speicheremulator zugreifen möchten, möchten Sie wahrscheinlich eine Verbindungszeichenfolge konfigurieren, die auf den Speicheremulator verweist und die später so geändert werden kann, dass sie auf ein Azure-Speicherkonto verweist. Eine Verbindungszeichenfolge ist eine Konfigurationseinstellung, die von der Rolle zur Laufzeit gelesen werden kann, um eine Verbindung mit einem Speicherkonto herzustellen. Weitere Informationen zum Erstellen von Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Sie können einen Verweis auf das Speicheremulatorkonto aus dem Code zurückgeben, indem Sie die **DevelopmentStorageAccount**-Eigenschaft verwenden. Diese Methode funktioniert ordnungsgemäß, wenn Sie auf den Speicheremulator vom Code aus zugreifen möchten. Wenn die Anwendung jedoch in Azure veröffentlicht werden soll, müssen Sie vor der Veröffentlichung eine Verbindungszeichenfolge für den Zugriff auf das Azure-Speicherkonto erstellen und den Code so ändern, dass die Verbindungszeichenfolge verwendet wird. Wenn Sie häufig zwischen dem Speicheremulatorkonto und einem Azure-Speicherkonto wechseln, wird der Prozess durch eine Verbindungszeichenfolge vereinfacht.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Initialisieren und Ausführen des Speicheremulators
Sie können angeben, dass beim Ausführen oder Debuggen des Diensts in Visual Studio automatisch der Speicheremulator gestartet werden soll. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das **Azure**-Projekt, und wählen Sie **Eigenschaften** aus. Wählen Sie auf der Registerkarte **Entwicklung** in der Liste **Azure-Speicheremulator starten** die Option **True** aus (falls sie noch nicht auf diesen Wert festgelegt ist).

Beim ersten Ausführen oder Debuggen des Diensts von Visual Studio aus wird vom Speicheremulator ein Initialisierungsprozess gestartet. Bei diesem Prozess werden lokale Ports für den Speicheremulator reserviert, und die Speicheremulator-Datenbank wird erstellt. Nach Abschluss dieses Prozesses ist keine erneute Ausführung erforderlich, sofern nicht die Speicheremulator-Datenbank gelöscht wird.

> [!NOTE]
> Ab der Azure Tools-Version von Juni 2012 wird der Speicheremulator standardmäßig in SQL Express LocalDB ausgeführt. In früheren Versionen von Azure Tools wird der Speicheremulator für eine Standardinstanz von SQL Express 2005 oder 2008 ausgeführt, die vor der Installation des Azure SDK installiert werden muss. Sie können den Speicheremulator auch für eine benannte Instanz von SQL Express oder eine benannte Instanz bzw. Standardinstanz von Microsoft SQL Server ausführen. Wenn Sie den Speicheremulator für die Ausführung mit einer anderen Instanz als der Standardinstanz konfigurieren müssen, finden Sie Informationen hierzu unter [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](storage/common/storage-use-emulator.md).
> 
> 

Der Speicheremulator verfügt über eine Benutzeroberfläche, auf der Sie den Status der lokalen Speicherdienste anzeigen und diese starten, beenden und zurücksetzen können. Nach dem Start des Speicheremulatordiensts können Sie die Benutzeroberfläche anzeigen oder den Dienst starten bzw. beenden, indem Sie auf der Windows-Taskleiste mit der rechten Maustaste auf das Infobereichsymbol für den Microsoft Azure-Emulator klicken.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Anzeigen von Speicheremulatordaten im Server-Explorer
Der Azure Storage-Knoten im Server-Explorer ermöglicht Ihnen die Anzeige von Daten und das Ändern von Einstellungen für Blob- und Tabellendaten in Ihren Speicherkonten, einschließlich des Speicheremulators. Weitere Informationen finden Sie unter [Verwalten von Azure Blob Storage-Ressourcen mit dem Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs).

