# Fork Changes

This is a fork of [magodo/terraform-provider-restful](https://github.com/magodo/terraform-provider-restful) maintained by [@lfventura](https://github.com/lfventura).

## Upstream Pull Request

The changes in this fork have been submitted to the upstream repository:
- PR: https://github.com/magodo/terraform-provider-restful/pull/164

## Additional Features

### Resource-Level Configuration Overrides

Added support for overriding provider-level configurations at the resource level:

- **Optional `base_url` in Provider**: The `base_url` attribute in the provider is now optional. If not specified, it must be defined in each resource or operation.
- **New Attribute: `base_url` in Resources**: Allows overriding the provider's `base_url` per resource or operation.
- **Enhanced Security Overrides**: Improved support for overriding `security` configurations per resource, with better inheritance from the provider.

### Usage Example

```hcl
# Provider with optional base_url
provider "restful" {
  # base_url is now optional
  security = {
    http = {
      basic = {
        username = "user"
        password = "pass"
      }
    }
  }
}

# Resource with base_url override
resource "restful_resource" "api1" {
  base_url = "https://api1.example.com"
  path     = "/resource"
  body     = { name = "test" }
  security = {
    apikey = [{
      name  = "X-API-Key"
      value = "api1-key"
      in    = "header"
    }]
  }
}

# Operation with overrides
resource "restful_operation" "op" {
  base_url = "https://api2.example.com"
  path     = "/operation"
  method   = "POST"
  security = {
    oauth2 = {
      client_credentials = {
        token_url    = "https://api2.example.com/oauth/token"
        client_id    = "client"
        client_secret = "secret"
      }
    }
  }
}
```

### Sensitive Output Support

Added support for marking resource outputs as sensitive in Terraform:

- **New Attribute: `use_sensitive_output`** (Boolean, Optional)
  - When set to `true`, the response will be stored in `sensitive_output` instead of `output`
  - Defaults to `false`
  - Available in: `restful_resource`, `restful_operation`, `data.restful_resource`, `ephemeral.restful_resource`

- **New Output: `sensitive_output`** (Dynamic, Read-Only, Sensitive)
  - Contains the API response when `use_sensitive_output = true`
  - Automatically marked as sensitive by Terraform
  - Available in all resource types

### Usage Example

```hcl
resource "restful_resource" "secret" {
  path = "/api/secrets"
  use_sensitive_output = true
  body = {
    key   = "api_key"
    value = "secret_value"
  }
}

output "secret_data" {
  value     = restful_resource.secret.sensitive_output
  sensitive = true
}
```

## Publishing

This fork is published to the Terraform Registry as:
```hcl
terraform {
  required_providers {
    restful = {
      source = "lfventura/restful"
    }
  }
}
```

## Compatibility

This fork introduces breaking changes:
- `base_url` in the provider is now optional. Existing configurations that rely on provider-level `base_url` will continue to work, but new configurations must define `base_url` either in the provider or in each resource/operation.

Existing configurations without `base_url` in the provider will require adding `base_url` to resources or the provider.
