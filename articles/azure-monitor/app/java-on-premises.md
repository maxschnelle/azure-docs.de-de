---
title: Überwachen von lokal ausgeführten Java-Anwendungen – Azure Monitor Application Insights
description: Überwachen der Anwendungsleistung für lokal ausgeführte Java-Anwendungen, ohne die App zu instrumentieren. Verteilte Ablaufverfolgung und Anwendungszuordnung.
ms.topic: conceptual
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.date: 04/16/2020
ms.openlocfilehash: c2d35a6f379b0d7cf3c4c7d61e5e679553e5302f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87326884"
---
# <a name="java-codeless-application-monitoring-on-premises---azure-monitor-application-insights---public-preview"></a>Lokale Java-Anwendungsüberwachung ohne Code mit Azure Monitor Application Insights – öffentliche Vorschau

Bei der Java-Anwendungsüberwachung ohne Code geht es um Einfachheit. Es gibt keine Codeänderungen, und der Java-Agent kann durch nur wenige Konfigurationsänderungen aktiviert werden.

## <a name="overview"></a>Übersicht

Nach der Aktivierung sammelt der Java-Agent automatisch eine Vielzahl von Anforderungen, Abhängigkeiten, Protokollen und Metriken aus den am häufigsten genutzten Bibliotheken und Frameworks.

Sehen Sie sich die [detaillierten Anweisungen](./java-in-process-agent.md) für die verschiedenen Umgebungen an, darunter auch lokale.

 ## <a name="next-steps"></a>Nächste Schritte

* [Anweisungen zum Herunterladen des Java-Agents](./java-in-process-agent.md)
* [Konfigurieren der JVM-Argumente](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args)
* [Anpassen der Konfiguration](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options)
