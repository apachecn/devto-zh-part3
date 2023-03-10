# 使用 PowerShell 设置您的 Azure 搜索

> 原文：<https://dev.to/manuelsidler/setup-your-azure-search-with-powershell-11gb>

微软通过 [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/module/az.resourcegraph/?view=azps-1.5.0#search) 和 [REST API](https://docs.microsoft.com/en-us/rest/api/searchservice/) 提供 cmdlets 来管理你的 Azure 搜索资源。

在这种情况下，我们需要一个 Azure 搜索服务，它将:

*   从 Azure Blob 容器中提取存储元数据
*   将元数据存储在索引中
*   提供一个自定义键来查询我们的索引

为了满足这些要求，我们将使用 Azure PowerShell cmdlets 和 REST API 端点的组合，通过以下组件设置 Azure 搜索:

*   Azure 搜索服务
*   搜索查询关键字
*   连接到 Azure Blob 存储的搜索索引

为了实现这一目标，我们将采取以下步骤:

1.  定义公共变量
2.  登录 Azure
3.  创建搜索服务
4.  创建搜索查询关键字
5.  获取搜索主要管理关键字
6.  创建索引
7.  创建数据源
8.  创建索引程序

先决条件

*   Azure 订阅
*   资源组
*   具有 Blob 容器的存储帐户
*   已安装 [Azure PowerShell 模块](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-1.5.0)

### 变量

首先，我们需要定义一些变量，我们将在接下来的步骤中使用这些变量。

```
$tenantId  =  "<tenantId>"  # eg. 12133905-3bce-414e-983f-79013911deee  $subscriptionId  =  "<subscriptionId>"  # eg. 510f6c5b-b136-45e7-b239-dccb600331ea  $resourceGroup  =  "<resourceGroup>"  # eg. my-project  $location  =  "<location>"  # eg. WestEurope  $storageAccountName  =  "<storageaccount>"  # eg. myprojectstorage  $searchName  =  "<searchName>"  # eg. my-project-search 
```

Enter fullscreen mode Exit fullscreen mode

### 登录

要访问我们的 Azure 资源，我们需要连接到我们的 Azure 帐户:

```
Connect-AzAccount  -Tenant  $tenantId  -Subscription  $subscriptionId 
```

Enter fullscreen mode Exit fullscreen mode

此 cmdlet 将打开一个弹出窗口，您可以在其中输入 Azure 帐户的凭据。

### 创建搜索服务

假设登录成功，我们现在可以创建搜索服务:

```
$sku  =  "Free"  # Free|Basic|S1|S2|S3|S3-HD  New-AzSearchService  -Name  $searchName  -ResourceGroupName  $resourceGroup  -Sku  $sku  -Location  $location 
```

Enter fullscreen mode Exit fullscreen mode

### 创建搜索查询关键字

在我们的场景中，我们还需要一个额外的搜索查询关键字:

```
$queryKey  =  "MyQueryKey"  New-AzSearchQueryKey  -Name  $queryKey  -ServiceName  $searchName  -ResourceGroupName  $resourceGroup 
```

Enter fullscreen mode Exit fullscreen mode

### 获取搜索主管理关键字

不幸的是，Azure PowerShell 不提供 cmdlets 来管理你的搜索索引、索引器和数据源。为了创建这些资源，我们将使用 Azure REST API。但是首先我们需要获取搜索主管理键，我们将把它用作 API 键:

```
$adminKeyPair  =  Get-AzSearchAdminKeyPair  -ResourceGroupName  $resourceGroup  -ServiceName  $searchName  $primaryAdminKey  =  $adminKeyPair.Primary 
```

Enter fullscreen mode Exit fullscreen mode

### 创建索引

我想将索引定义作为 JSON 存储在文件夹中，而不是使用变量。一旦我们存储了索引定义，我们就可以在实际调用 [Create Index](https://docs.microsoft.com/en-us/rest/api/searchservice/create-index) 端点时遍历它们。我们将尽可能保持这个索引的简单。字段:

*   ID(主键)
*   标题
*   标签(可过滤的集合)
*   元数据存储路径(二进制大对象的路径)

/indexes/myindex.json

```
{  "name":  "my-index",  "fields":  [  {"name":  "ID",  "type":  "Edm.String",  "key":  true,  "filterable":  false,  "sortable":  false,  "facetable":  false,  "searchable":  false},  {"name":  "TITLE",  "type":  "Edm.String",  "filterable":  false,  "sortable":  false,  "facetable":  false,  "searchable":  false},  {"name":  "TAGS",  "type":  "Collection(Edm.String)",  "filterable":  true,  "sortable":  false,  "facetable":  false,  "searchable":  false},  {"name":  "metadata_storage_path",  "type":  "Edm.String",  "filterable":  false,  "sortable":  false,  "facetable":  false,  "searchable":  false}  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

根据我们的定义，我们现在可以创建我们的索引:

```
Get-ChildItem  "indexes"  -Filter  *.json  |  ForEach-Object  {  $indexDefinition  =  Get-Content  $_.FullName  -Raw  $headers  =  @{  'api-key'  =  $primaryAdminKey  'Content-Type'  =  'application/json'  'Accept'  =  'application/json'  }  $url  =  "https://$searchName.search.windows.net/indexes?api-version=2017-11-11"  Invoke-RestMethod  -Uri  $url  -Headers  $headers  -Method  Post  -Body  $indexDefinition  |  ConvertTo-Json  } 
```

Enter fullscreen mode Exit fullscreen mode

### 创建数据源

我们采用相同的方法创建数据源。但是在这种情况下，我们需要从我们的存储帐户设置连接字符串。由于我们是优秀的开发人员，我们不会将字符串存储在数据源 JSON 定义中；-)

/data sources/my data source . JSON

```
{  "name"  :  "my-datasource",  "type"  :  "azureblob",  "credentials"  :  {  "connectionString"  :  "[STORAGECONNECTION]"  },  "container"  :  {  "name"  :  "myblobcontainer"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

要替换 STORAGECONNECTION 占位符，我们首先需要获取连接字符串:

```
$storageKey  =  (Get-AzStorageAccountKey  -Name  $storageAccountName  -ResourceGroupName  $resourceGroup)[0].Value  $storageConnectionString  =  "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageKey;EndpointSuffix=core.windows.net" 
```

Enter fullscreen mode Exit fullscreen mode

有了可用的存储连接字符串，我们现在可以遍历数据源文件夹，并使用[创建数据源](https://docs.microsoft.com/en-us/rest/api/searchservice/create-data-source)端点来创建数据源:

```
Get-ChildItem  "datasources"  -Filter  *.json  |  ForEach-Object  {  $datasourceDefinition  =  (Get-Content  $_.FullName  -Raw).replace("[STORAGECONNECTION]",  $storageConnectionString)  $headers  =  @{  'api-key'  =  $primaryAdminKey  'Content-Type'  =  'application/json'  'Accept'  =  'application/json'  }  $url  =  "https://$searchName.search.windows.net/datasources?api-version=2017-11-11"  Invoke-RestMethod  -Uri  $url  -Headers  $headers  -Method  Post  -Body  $dataSourceDefinition  |  ConvertTo-Json  } 
```

Enter fullscreen mode Exit fullscreen mode

### 创建索引器

最后但同样重要的是，我们必须创建一个索引器。我们再次使用 JSON 文件来定义索引器。这里重要的是，在我们的场景中，我们只想索引存储元数据。您也可以选择提取所有元数据或文件内容。

/indexers/myindexer.json

```
{  "name"  :  "my-indexer",  "dataSourceName"  :  "my-datasource",  "targetIndexName"  :  "my-index",  "schedule"  :  {  "interval"  :  "PT1H"  },  "parameters"  :  {  "configuration"  :  {  "dataToExtract"  :  "storageMetadata"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

为了创建索引器，我们将使用[创建索引器](https://docs.microsoft.com/en-us/rest/api/searchservice/create-indexer)端点:

```
Get-ChildItem  "indexers"  -Filter  *.json  |  ForEach-Object  {  $indexerDefinition  =  Get-Content  $_.FullName  -Raw  $headers  =  @{  'api-key'  =  $primaryAdminKey  'Content-Type'  =  'application/json'  'Accept'  =  'application/json'  }  $url  =  "https://$searchName.search.windows.net/indexers?api-version=2017-11-11"  Invoke-RestMethod  -Uri  $url  -Headers  $headers  -Method  Post  -Body  $indexerDefinition  |  ConvertTo-Json  } 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

我们现在有一个 Azure 搜索服务，它:

*   提供一个自定义键来查询我们的索引
*   从 Azure Blob 容器中提取存储元数据
*   将元数据存储在索引中

[![search query](img/06ee1e7704dcc815b0d24de663bee6b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ylEWMzhD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i6s21618cmamwaeupd4b.png)