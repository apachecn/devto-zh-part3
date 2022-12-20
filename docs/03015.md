# ARM:在现有日志分析工作区上启用容器监控解决方案

> 原文：<https://dev.to/cmendibl3/arm-enable-container-monitoring-solution-on-an-existing-log-analytics-workspace-lgc>

最近，我不得不更新一堆[日志分析工作区](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwiFrof58qDiAhXqyIUKHWPuBaIQFjAAegQIARAB&url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Flearn%2Fquick-create-workspace&usg=AOvVaw3DvKwidPs8__aX0fQ0vjQf)资源来启用[容器监控解决方案](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/containers)，以便监控一些新的 [Azure Kubernetes 服务](https://azure.microsoft.com/en-us/services/kubernetes-service/)实例。所以我想出了这个 ARM 模板，想和大家分享一下:

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "LogAnalyticsWorkspaceName": {
            "type": "string",
            "metadata": {
                "description": "Log Analytics Workspace name"
            }
        }
    },
    "variables": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('LogAnalyticsWorkspaceName'))]",
        "containerSolutionName": "[concat(parameters('LogAnalyticsWorkspaceName'), '-containers')]"
    },
    "resources": [
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[variables('containerSolutionName')]",
            "location": "[resourceGroup().location]",
            "plan": {
                "name": "[variables('containerSolutionName')]",
                "product": "[concat('OMSGallery/', 'ContainerInsights')]",
                "promotionCode": "",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[variables('workspaceResourceId')]"
            }
        }
    ],
    "outputs": {}
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/786c80c222598ab275d043fb57c6fb39.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fcmendible%2Farmtemplates%2Fmaster%2Fcontainers%2Fenable-container-solution-existing-loganalytics-workspace%2Fazuredeploy.json)

希望有帮助！