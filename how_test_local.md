To test a modified Terraform provider locally, you need to compile it and then configure Terraform to use this local build instead of the registry version.

Here are the steps:

### 1. Compile the Provider

First, you need to compile the provider code to create the executable binary. Use the following command, which takes into account the path to `go` that you provided:

```bash
/opt/homebrew/bin/go build -o terraform-provider-restful
```

This command will generate a binary file called `terraform-provider-restful` in the current directory.

### 2. Create a Development Directory for the Provider

Terraform looks for provider binaries in specific directories. For development, the easiest way is to create a directory structure and place the provider binary in it.

Create the following directory structure in your home directory:

```bash
mkdir -p ~/.terraform-plugins
```

Now, move the binary you compiled to this new directory:

```bash
mv terraform-provider-restful ~/.terraform-plugins
```

### 3. Configure Terraform to Use the Local Provider

Create a configuration file for Terraform in your home directory to instruct it to use the local provider. Create the file `~/.terraformrc` with the following content:

```
provider_installation {
  dev_overrides {
    "lfventura/restful" = "/Users/user/terraform-plugins"
  }
}
```

### 4. Test the Provider and View the Logs

Now you can go to any of the example directories in the project (like `examples/resources/restful_resource`) and run the Terraform commands.

To see the provider logs, you need to set the `TF_LOG_PROVIDER` environment variable.

Run the following commands:

```bash
export TF_LOG_PROVIDER=INFO
terraform init
terraform plan
```

The init might fail, but proceed with plan.

When running `terraform plan`, you should see a log message with the type of the `output` variable. Look for a line similar to this:

```
[INFO] output type: basetypes.DynamicValue
```

Please execute these steps and tell me what the type of the `output` variable is. After that, I will be able to fix the code.
