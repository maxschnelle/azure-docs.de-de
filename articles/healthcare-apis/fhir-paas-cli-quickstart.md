---
title: 'Schnellstart: Bereitstellen von Azure API for FHIR mithilfe der Azure CLI'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure API for FHIR in Azure mithilfe der Azure CLI bereitstellen.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: b09a72538dd7a6886811b9a23c915316627da093
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339440"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Schnellstart: Bereitstellen von Azure API for FHIR mithilfe der Azure CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure API for FHIR in Azure mithilfe der Azure CLI bereitstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>Hinzufügen der HealthcareAPIs-Erweiterung

```azurecli-interactive
az extension add --name healthcareapis
```

Rufen Sie eine Liste der Befehle für HealthcareAPIs ab:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe

Wählen Sie einen Namen für die Ressourcengruppe aus, in der Azure API for FHIR enthalten sein soll, und erstellen Sie sie:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Bereitstellen von Azure API for FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Abrufen der FHIR-API-Funktionsbestätigung

Rufen Sie wie folgt eine Funktionsbestätigung aus der FHIR-API ab:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht mehr benötigen, löschen Sie die Ressourcengruppe wie folgt:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Azure API for FHIR in Ihrem Abonnement bereitgestellt. Wenn Sie zusätzliche Einstellungen in Azure API for FHIR festlegen möchten, fahren Sie mit der Schrittanleitung für zusätzliche Einstellungen fort. Wenn Sie Azure API for FHIR verwenden möchten, informieren Sie sich ausführlicher über das Registrieren von Anwendungen.

>[!div class="nextstepaction"]
>[Zusätzliche Einstellungen in Azure API for FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Registrieren der Azure Active Directory-Apps für Azure API for FHIR](fhir-app-registration.md)
