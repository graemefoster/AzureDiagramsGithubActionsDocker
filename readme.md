# AzureDiagrams

## Generate a Draw.IO diagram from your Azure Resources

### Github Action

You need to an AAD token to use AzureResourceMap with at least Reader scope over the resource-groups. 
Here's an example that:
- will get a token
- use the Custom Github Action to generate a diagram
- publish the diagram as an artefact.


```yaml
      - name: "Get access token"
        id: token
        run: |
          token=$(az account get-access-token --query accessToken -o tsv --query "accessToken")
          echo "::add-mask::$token" 
          echo "::set-output name=token::$token"

      - name: graemefoster-azurediagrams-action
        uses: graemefoster/azurediagrams-docker-action@v0.1.0
        with:
          subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          resourceGroup: "*-grf-*"
          outputFileName: "azurediagram.drawio"
          accessToken: ${{ steps.token.outputs.token }}

      - uses: actions/upload-artifact@v3
        with:
          name: diagram
          path: ./azurediagram.drawio
```
## Yaml properties

| Property          | Required  | Description                                                                  |
|:------------------|:----------|:-----------------------------------------------------------------------------|
| subscriptionId    | Yes       | Subscription Id to run against                                               |
| resourceGroup     | Yes       | Wildcard enabled resource group name (supports multiple)                     |
| accessToken       | Yes       | Optional JWT to avoid using CLI credential                                   |
| condensed         | Yes       | True collapses private endpoints into subnets (can simplify large diagrams)  |
| showRuntime       | Yes       | True to show runtime flows defined on the control plane                      |
| showInferred      | Yes       | True to infer connections between resources by introspecting appSettings     |
| showIdentity      | Yes       | True to show User Assigned Managed Identity connections                      |
| showDiagnostics   | Yes       | True to show diagnostics flows                                               |
| outputPng         | Yes       | True to export a png of the draw.io diagram                                  |


## Output Formats
The initial version supports Draw.IO diagrams. 
