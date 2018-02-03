This template sets up an Azure Function along with some settings for it using an ARM template.
It also showcases how to call the Rest API to make updates to a setting.

```
az group create -n contoso-func -l usgovvirginia
az group deployment create -n $(date +%Y%m%d%H%M%S) -g contoso-func --template-file template.json

# If you make code changes, sync your Functions
az webapp deployment source sync -g contoso-func -n contoso-site
```

TODO:
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

Reference:
https://docs.microsoft.com/azure/azure-functions/functions-infrastructure-as-code
https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings#Request_Body