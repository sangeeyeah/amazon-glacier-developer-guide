# Creating a Vault in Amazon Glacier Using the AWS SDK for Java<a name="creating-vaults-sdk-java"></a>

The low\-level API provides methods for all the vault operations, including creating and deleting vaults, getting a vault description, and getting a list of vaults created in a specific region\. The following are the steps to create a vault using the AWS SDK for Java\. 

1. Create an instance of the `AmazonGlacierClient` class \(the client\)\. 

   You need to specify an AWS region in which you want to create a vault\. All operations you perform using this client apply to that region\.

1. Provide request information by creating an instance of the `CreateVaultRequest` class\.

   Amazon Glacier requires you to provide a vault name and your account ID\. If you don't provide an account ID, then the account ID associated with the credentials you provide to sign the request is used\. For more information, see [Using the AWS SDK for Java with Amazon Glacier](using-aws-sdk-for-java.md)\. 

1. Execute the `createVault` method by providing the request object as a parameter\. 

   The response Amazon Glacier returns is available in the `CreateVaultResult` object\.

The following Java code snippet illustrates the preceding steps\. The snippet creates a vault in the `us-west-2` region\. The `Location` it prints is the relative URI of the vault that includes your account ID, the region, and the vault name\.

```
AmazonGlacierClient client = new AmazonGlacierClient(credentials);
client.setEndpoint("https://glacier.us-west-2.amazonaws.com");

CreateVaultRequest request = new CreateVaultRequest()
    .withVaultName("*** provide vault name ***");
CreateVaultResult result = client.createVault(request);

System.out.println("Created vault successfully: " + result.getLocation());
```

**Note**  
For information about the underlying REST API, see [Create Vault \(PUT vault\)](api-vault-put.md)\. 

## Example: Creating a Vault Using the AWS SDK for Java<a name="creating-vaults-sdk-java-example"></a>

The following Java code example creates a vault in the `us-west-2` region \(for more information on regions, see [Accessing Amazon Glacier](amazon-glacier-accessing.md)\)\. In addition, the code example retrieves the vault information, lists all vaults in the same region, and then deletes the vault created\. 

For step\-by\-step instructions on how to run the following example, see [Running Java Examples for Amazon Glacier Using Eclipse](using-aws-sdk-for-java.md#setting-up-and-testing-sdk-java)\. 

**Example**  

```
import java.io.IOException;
import java.util.List;

import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.services.glacier.AmazonGlacierClient;
import com.amazonaws.services.glacier.model.CreateVaultRequest;
import com.amazonaws.services.glacier.model.CreateVaultResult;
import com.amazonaws.services.glacier.model.DeleteVaultRequest;
import com.amazonaws.services.glacier.model.DescribeVaultOutput;
import com.amazonaws.services.glacier.model.DescribeVaultRequest;
import com.amazonaws.services.glacier.model.DescribeVaultResult;
import com.amazonaws.services.glacier.model.ListVaultsRequest;
import com.amazonaws.services.glacier.model.ListVaultsResult;


public class AmazonGlacierVaultOperations {

    public static AmazonGlacierClient client;

    public static void main(String[] args) throws IOException {

    	ProfileCredentialsProvider credentials = new ProfileCredentialsProvider();
    	
        client = new AmazonGlacierClient(credentials);
        client.setEndpoint("https://glacier.us-east-1.amazonaws.com/");
        
        String vaultName = "examplevaultfordelete";

        try {            
            createVault(client, vaultName);
            describeVault(client, vaultName); 
            listVaults(client);
            deleteVault(client, vaultName);      

        } catch (Exception e) {
            System.err.println("Vault operation failed." + e.getMessage());
        }
    }

    private static void createVault(AmazonGlacierClient client, String vaultName) {
        CreateVaultRequest createVaultRequest = new CreateVaultRequest()
            .withVaultName(vaultName);
        CreateVaultResult createVaultResult = client.createVault(createVaultRequest);

        System.out.println("Created vault successfully: " + createVaultResult.getLocation());
    }

    private static void describeVault(AmazonGlacierClient client, String vaultName) {
        DescribeVaultRequest describeVaultRequest = new DescribeVaultRequest()
            .withVaultName(vaultName);
        DescribeVaultResult describeVaultResult  = client.describeVault(describeVaultRequest);

        System.out.println("Describing the vault: " + vaultName);
        System.out.print(
                "CreationDate: " + describeVaultResult.getCreationDate() +
                "\nLastInventoryDate: " + describeVaultResult.getLastInventoryDate() +
                "\nNumberOfArchives: " + describeVaultResult.getNumberOfArchives() + 
                "\nSizeInBytes: " + describeVaultResult.getSizeInBytes() + 
                "\nVaultARN: " + describeVaultResult.getVaultARN() + 
                "\nVaultName: " + describeVaultResult.getVaultName());
    }

    private static void listVaults(AmazonGlacierClient client) {
        ListVaultsRequest listVaultsRequest = new ListVaultsRequest();
        ListVaultsResult listVaultsResult = client.listVaults(listVaultsRequest);

        List<DescribeVaultOutput> vaultList = listVaultsResult.getVaultList();
        System.out.println("\nDescribing all vaults (vault list):");
        for (DescribeVaultOutput vault : vaultList) {
            System.out.println(
                    "\nCreationDate: " + vault.getCreationDate() +
                    "\nLastInventoryDate: " + vault.getLastInventoryDate() +
                    "\nNumberOfArchives: " + vault.getNumberOfArchives() + 
                    "\nSizeInBytes: " + vault.getSizeInBytes() + 
                    "\nVaultARN: " + vault.getVaultARN() + 
                    "\nVaultName: " + vault.getVaultName()); 
        }
    }

    private static void deleteVault(AmazonGlacierClient client, String vaultName) {
        DeleteVaultRequest request = new DeleteVaultRequest()
            .withVaultName(vaultName);
        client.deleteVault(request);
        System.out.println("Deleted vault: " + vaultName);
    }

}
```