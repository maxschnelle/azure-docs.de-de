---
title: Entfernen von Rollenzuweisungen für eine Gruppe in Azure Active Directory | Microsoft-Dokumentation
description: 'Vorschau: Benutzerdefinierte Azure AD-Rollen zur Delegierung der Identitätsverwaltung. Verwalten von Azure-Rollen im Azure-Portal, mit PowerShell oder über die Graph-API.'
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f441930d9d99f35c2e53bb040b0db0a427659
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92373741"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Entfernen von Rollenzuweisungen für eine Gruppe in Azure Active Directory

In diesem Artikel wird beschrieben, wie ein IT-Administrator Azure AD-Rollen entfernen kann, die Gruppen zugewiesen sind. Im Azure-Portal können Sie jetzt sowohl direkte als auch indirekte Rollenzuweisungen für einen Benutzer entfernen. Ist einem Benutzer durch eine Gruppenmitgliedschaft eine Rolle zugewiesen, entfernen Sie den Benutzer aus der Gruppe, um die Rollenzuweisung zu entfernen.

## <a name="using-azure-admin-center"></a>Mithilfe von Azure Admin Center

1. Melden Sie sich bei [Azure AD Admin Center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit Berechtigungen vom Typ „Administrator für privilegierte Rollen“ oder „Globaler Administrator“ in der Azure AD-Organisation an.

1. Wählen Sie **Rollen und Administratoren** > *_Rollenname_* aus.

1. Wählen Sie die Gruppe aus, für die Sie die Rollenzuweisung entfernen möchten, und wählen Sie „Zuweisung entfernen“ aus.

   ![Entfernen einer Rollenzuweisung für eine ausgewählte Gruppe](./media/groups-remove-assignment/remove-assignment.png)

1. Wählen Sie **Ja** aus, wenn Sie zur Bestätigung der Aktion aufgefordert werden.

## <a name="using-powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Erstellen einer Gruppe, die der Rolle zugewiesen werden kann

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Abrufen der Rollendefinition, der Sie die Gruppe zuweisen möchten

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Erstellen einer Rollenzuweisung

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Entfernen der Rollenzuweisung

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Verwenden der Microsoft Graph-API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Erstellen einer Gruppe, die einer Azure AD-Rolle zugewiesen werden kann

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Abrufen der Rollendefinition

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Erstellen der Rollenzuweisung

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Löschen von Rollenzuweisungen

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Problembehandlung bei Rollen, die Cloudgruppen zugewiesen sind](groups-faq-troubleshooting.md)
