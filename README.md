# terraform-aws-codepipeline-slack-notifications

[![Github Actions](https://github.com/kjagiello/terraform-aws-codepipeline-slack-notifications/workflows/CI/badge.svg)](https://github.com/kjagiello/terraform-aws-codepipeline-slack-notifications/actions?workflow=CI)

A terraform module to set up Slack notifications for your AWS CodePipelines. Available through the [Terraform registry](https://registry.terraform.io/modules/kjagiello/codepipeline-slack-notifications/aws).

![image](https://user-images.githubusercontent.com/74944/71839994-b660bf00-30bc-11ea-8e5e-4d8850da6900.png)

## Usage

```hcl
resource "aws_codepipeline" "example" {
  // ...
}

module "codepipeline_notifications" {
  source  = "kjagiello/codepipeline-slack-notifications/aws"
  version = "1.0.0"

  name          = "codepipeline-notifications"
  namespace     = "kjagiello"
  stage         = "sandbox"
  slack_url     = "https://hooks.slack.com/services/(...)"
  slack_channel = "#notifications"
  codepipelines = [
    aws_codepipeline.example,
  ]
}
```

Beware that during the initial apply, it might fail with following error:

> Error: error creating codestar notification rule: ConfigurationException: AWS
> CodeStar Notifications could not create the AWS CloudWatch Events managed
> rule in your AWS account. If this is your first time creating a notification
> rule, the service-linked role for AWS CodeStar Notifications might not yet
> exist. Creation of this role might take up to 15 minutes. Until it exists,
> notification rule creation will fail. Wait 15 minutes, and then try again. If
> this is is not the first time you are creating a notification rule, there
> might be a problem with a network connection, or one or more AWS services
> might be experiencing issues. Verify your network connection and check to see
> if there are any issues with AWS services in your AWS Region before trying
> again.

This is due to this module using [AWS CodeStar](https://aws.amazon.com/codestar/)
for subscribing to the CodePipeline state changes. The first use of a CodeStar
resource automatically creates the required service-linked role, which
typically is nearly instantaneous. Just reapply your Terraform plan and you
should be good to go.

# Module documentation

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 0.12 |
| <a name="requirement_archive"></a> [archive](#requirement\_archive) | >= 1.3 |
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | >= 2.70, < 5.0 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_archive"></a> [archive](#provider\_archive) | 2.2.0 |
| <a name="provider_aws"></a> [aws](#provider\_aws) | 4.11.0 |

## Resources

| Name | Type |
|------|------|
| [archive_file.notifier_package](https://registry.terraform.io/providers/hashicorp/archive/latest/docs/data-sources/file) | data source |
| [aws_iam_policy_document.pipeline_notification](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.pipeline_notification_role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.pipeline_updates_policy](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_attributes"></a> [attributes](#input\_attributes) | List of attributes to add to label | `list(any)` | `[]` | no |
| <a name="input_codepipelines"></a> [codepipelines](#input\_codepipelines) | CodePipeline resources that should trigger Slack notifications | `list(any)` | n/a | yes |
| <a name="input_event_type_ids"></a> [event\_type\_ids](#input\_event\_type\_ids) | The list of event type to trigger a notification on | `list(any)` | <pre>[<br>  "codepipeline-pipeline-pipeline-execution-failed",<br>  "codepipeline-pipeline-pipeline-execution-canceled",<br>  "codepipeline-pipeline-pipeline-execution-started",<br>  "codepipeline-pipeline-pipeline-execution-resumed",<br>  "codepipeline-pipeline-pipeline-execution-succeeded",<br>  "codepipeline-pipeline-pipeline-execution-superseded"<br>]</pre> | no |
| <a name="input_name"></a> [name](#input\_name) | Name (unique identifier for app or service) | `string` | n/a | yes |
| <a name="input_namespace"></a> [namespace](#input\_namespace) | Namespace (e.g. `skynet`) | `string` | n/a | yes |
| <a name="input_slack_channel"></a> [slack\_channel](#input\_slack\_channel) | A slack channel to send the deployment notifications to | `string` | n/a | yes |
| <a name="input_slack_emoji"></a> [slack\_emoji](#input\_slack\_emoji) | The emoji avatar of the user that sends the notifications | `string` | `":rocket:"` | no |
| <a name="input_slack_url"></a> [slack\_url](#input\_slack\_url) | Slack webhook URL for deploy notifications | `string` | n/a | yes |
| <a name="input_slack_username"></a> [slack\_username](#input\_slack\_username) | The name of the user that sends the notifications | `string` | `"Deploy Bot"` | no |
| <a name="input_stage"></a> [stage](#input\_stage) | Stage (e.g. `prod`, `dev`, `staging`) | `string` | n/a | yes |
| <a name="input_tags"></a> [tags](#input\_tags) | Additional tags (e.g. `map('BusinessUnit','XYZ')`) | `map(string)` | `{}` | no |

## Outputs

No outputs.
<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
