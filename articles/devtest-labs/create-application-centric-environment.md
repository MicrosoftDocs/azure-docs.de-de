---
title: 'Erstellen einer anwendungsorientierten Umgebung: Azure'
description: In diesem Artikel erfahren Sie, wie Sie eine anwendungsorientierte Umgebung mit CloudShell Colony und Microsoft Azure erstellen.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918016"
---
# <a name="create-an-application-centric-environment"></a>Erstellen einer anwendungsorientierten Umgebung

[CloudShell Colony von Quali](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) ist eine SaaS-Plattform zur Infrastrukturautomatisierung im großen Stil für komplexe anwendungsorientierte Umgebungen in Cloudtechnologien wie Azure und Kubernetes. CloudShell Colony ergänzt Azure DevTest Labs, um Entwicklerteams über den gesamten Wertstrom hinweg bis hin zur Produktion bei der Bereitstellung komplexer Anwendungen zu unterstützen.

In diesem Artikel erfahren Sie, wie Sie eine anwendungsorientierte Umgebung mit CloudShell Colony und Microsoft Azure erstellen.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>Einrichten der Umgebung mit CloudShell Colony und Microsoft Azure

1. Registrieren Sie sich für Ihre kostenlose Testversion von [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Für kostenlose Testversion registrieren":::    
1. Verknüpfen Sie Ihr Azure-Konto. (Eine entsprechende Anleitung finden Sie [hier](https://colonysupport.quali.com/hc/articles/360008222234).)

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Willkommen bei Colony":::     
1. Laden Sie Benutzer in Ihren Bereich ein.
1. Erstellen Sie Ihre erste Blaupause mithilfe einer YAML-Datei. (Eine entsprechende Anleitung finden Sie [hier](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint).)
    1. Verknüpfen Sie Ihr Blaupausenrepository in GitHub oder BitBucket mit Colony.
    1. Verwenden Sie eine Colony-Beispielblaupause als Grundlage, und ändern Sie sie nach Bedarf.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Belastungstests":::    
    1. Veröffentlichen Sie Ihre Blaupause, damit sie von anderen verwendet werden kann.
1. Starten Sie Ihre Anwendungsumgebung mithilfe von Colony in einer Sandbox.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Starten Ihrer Anwendungsumgebung in einer Sandbox mithilfe von Colony":::    

> [!NOTE]
> Sie können Ihre Blaupause auch in einen CI/CD-Workflow in Azure DevOps integrieren. (Eine entsprechende Anleitung finden Sie [hier](https://colonysupport.quali.com/hc/articles/360008464234).)

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Herstellen einer Verbindung mit Ihrer Azure DevOps-Pipeline":::    

## <a name="next-steps"></a>Nächste Schritte

[Anfordern einer Colony-Demo](https://info.quali.com/cloudshell-colony-demo-request)
