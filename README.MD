# Azure Functions Resource Manager Template
This template sets up an Azure Function using the code in this same repo. It pull everything in this repo (including README.MD and template.json) but Azure Functions, given the appSettings in the template, will only look at root level folders that contain a `function.json` file.

> **Note:** You can export all the code in an existing Azure Function setup using the **Download app content** button. This template was tested using **Site Content** and leaving **unchecked Include app settings in the download**.

## Naming Convention
This template only has 1 parameter: `namingPrefix`. This parameter is used to autogenerate the names of all resources. For example, for `namingPrefix` of `contoso_`, the site will be named `contoso-site` and the storage account `contososa`.

## Assumptions
All resources are created in the same location as the resource group they are contained i

## Instructions
1. Create the resource group

    ```bash
    az group create -n contoso -l usgovvirginia
    ```

1. Deploy the template to the newly created group

    ```bash
    az group deployment create -g contoso -n $(date +%Y%m%d_%H%M%S) --template-file template.json
    ```

1. If you use your own GitHub repo and make changes to it after deploying the template, you can trigger a sync using the Azure CLI:

    ```bash
    az webapp deployment source sync -g contoso-func -n contoso-site
    ```

## Outstanding Items
* Add hostNameSslStates.thumbprint
* Document isManualIntegration(see [link](https://social.msdn.microsoft.com/Forums/en-US/5b07e49e-c373-405c-87f7-3aa5963fd13f/error-message-when-deploying-arm-template-with-public-github-repo-to-azure?forum=windowsazurewebsitespreview))
* Document Project appSetting
  ```json
  {
    "name": "Project",
    "value": "src"
  },
  ```
* Decide on Blob connection string (`EndpointSuffix` vs `BlobEndpoint`)
  ```json
    "value": "[concat('DefaultEndpointsProtocol=https;BlobEndpoint=', reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob,';AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')),'2016-01-01').keys[0].value)]"
    "value": "[concat('DefaultEndpointsProtocol=https;EndpointSuffix=', replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, concat('https://', variables('storageAccountName'), '.blob.'),''),'/',''),';AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')),'2016-01-01').keys[0].value)]"
  ```
* Add notes on how to use REST API (update app settings in particular)
* Add the other Website SKUs
* Add note that consumption-based functions aren't available in Gov yet.

## References
https://docs.microsoft.com/azure/azure-functions/functions-infrastructure-as-code
https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings#Request_Body