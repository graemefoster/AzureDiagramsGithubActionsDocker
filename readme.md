# AzureDiagrams

## Generate a Draw.IO diagram from your Azure Resources

### Github Action

You need to an AAD token to use AzureResourceMap with at least Reader scope over the resource-groups. 
Here's an example that:
- will get a token
- use the Custom Github Action to generate an image
- publish the diagram as an artefact.


```yaml
      - name: "Get access token"
        id: token
        run: |
          token=$(az account get-access-token --query accessToken -o tsv --query "accessToken")
          echo "::add-mask::$token" 
          echo "token=$token" >> $GITHUB_OUTPUT

      - name: Generate Diagram
        uses: graemefoster/azurediagramsgithubactionsdocker@v0.1.13
        with:
          subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
          resourceGroup: "*-grf-*"
          outputFileName: "azurediagram.drawio"
          accessToken: ${{ steps.token.outputs.token }}

      - uses: actions/upload-artifact@v3
        with:
          name: diagram
          path: ./azurediagram.drawio.png
```
## Yaml properties

| Property          | Required  | Description                                                                  |
|:------------------|:----------|:-----------------------------------------------------------------------------|
| subscriptionId    | Yes       | Subscription Id to run against                                               |
| resourceGroup     | Yes       | Wildcard enabled resource group name (supports multiple)                     |
| accessToken       | Yes       | Azure Resource Manager JWT (see above example)                               |
| outputFileName    | Yes       | Filename to output drawio file to. Png will have .png suffix                 |
| condensed         | No        | True collapses private endpoints into subnets (can simplify large diagrams)  |
| showRuntime       | No        | True to show runtime flows defined on the control plane                      |
| showInferred      | No        | True to infer connections between resources by introspecting appSettings     |
| showIdentity      | No        | True to show User Assigned Managed Identity connections                      |
| showDiagnostics   | No        | True to show diagnostics flows                                               |


## Output Formats
The initial version supports Draw.IO diagrams. 
