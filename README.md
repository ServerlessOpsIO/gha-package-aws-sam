# Package AWS SAM artifacts

Package AWS SAM artifacts.

This GitHub Action allows you to package an AWS SAM application for later deployment.

This action purposely does not allow for overriding SAM CLI's template file resolution logic. THIS IS INTENTIONAL. To handle non-standrad template names, multiple templates, or templates in non-stndard locations run `sam buld` before this action. That will result in the creation of a `template.yaml` under `.aws-sam` which SAM CLI will find and use. This will ensure that `sam package` will correctly bundle function dependencies when creationg funtion artifacts.

_*NOTE: This workflow is opinionated and meets the needs of its author. It is provided publicly as a reference for others to use and modify as needed.*_

The `gha-package-aws-sam` action performs the following tasks:
1. Determines S3 bucket and prefix
2. Runs `sam package` to upload functions to S3 and generate a packaged SAM template tha references the objects in S3

## Usage
See below for inputs, outputs, and examples.

### Inputs

- `packaged_template_file` (optional): path to which packaged template should be written
- `aws_account_region` (optional): AWS region to use for SAM packaging.
- `sam_s3_bucket` (optional): S3 bucket for SAM deployment.
- `sam_s3_prefix` (optional): S3 prefix for SAM deployment.

### Outputs

- `packaged-template`: Name of the packaged template file that was created.
- `sam-artifact-bucket`: The S3 bucket where the SAM artifact was uploaded.
- `sam-artifact-bucket-prefix`: The S3 prefix where the SAM artifact was uploaded.

### Examples
To use this action see the examples below:

#### Standard usage
```yaml
name: CI
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    # Required for AWS credentials
    permissions:
      id-token: write
      contents: read

    steps:
      - name: Setup job workspace
        id: setup-workspace
        uses: ServerlessOpsIO/gha-setup-workspace@v1


      # Do job work here


      - name: Assume AWS Credentials
        uses: ServerlessOpsIO/gha-assume-aws-credentials@v1
        with:
          build_aws_account_id: ${{ secrets.BUILD_AWS_ACCOUNT_ID }}
          deploy_aws_account_id: ${{ secrets.DEPLOY_AWS_ACCOUNT_ID }}
          aws_account_region: 'us-east-1'
          gha_build_role_name: ${{ secrets.GHA_BUILD_ROLE_NAME }}
          gha_deploy_role_name: ${{ secrets.GHA_DEPLOY_ROLE_NAME }}

      - name: Store Artifacts
        uses: ServerlessOpsIO/gha-package-aws-sam@v1

```

#### S3 Bucket

The default value for `sam_s3_bucket` comes from [ServerlessOpsIO/aws-gha-integration](https://github.com/ServerlessOpsIO/aws-gha-integration). Both the `sam_s3_bucket` and `sam_s3_prefix` should not need to be configured but are avavailbel for unique circumstances.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any changes.

## Contact

For any questions or support, please open an issue in this repository.
