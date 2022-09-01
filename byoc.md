# BYOC (Bring Your Own Cloud)

---

## AWS (Amazon Web Services)

Follow these steps to set up a new client:

1. Ask for the client's AWS account ID and region.

2. Set up a new account for the client in BUTP.

3. Copy the RapidsDB AMIs from `us-west-2` to the client's region if it wasn't already done before. And add the new AMI IDs to the `virment/scripts/aws/share_amis.sh` and `virment/scripts/aws/unshare_amis.sh` scripts.

4. Share the RapidsDB AMIs with the client's AWS account by running `virment/scripts/aws/share_amis.sh` (see inline comment for usage).

5. The client needs to run our CloudFormation template to set up the required infrastructure and generate an access key and secret key pair. This can be done via the AWS website, or via an aws-cli command:

    ```shell
    aws cloudformation create-stack --stack-name RapidsDB --template-url https://rapidsdb-public.s3.us-west-2.amazonaws.com/cloud-formation.template.yml --capabilities CAPABILITY_IAM
    ```

6. The client needs to inspect the CloudFormation stack to get the access key and secret key pair. This can be done via the AWS website, or via an aws-cli command:

    ```shell
    aws cloudformation describe-stacks --stack-name RapidsDB
    ```

7. The client can now create a new `Region` config in BUTP with the access key and secret key pair, and start using it to create a new cluster.

## GCP (Google Cloud Platform)

Follow these steps to set up a new client:

1. Ask for the client's GCP project ID, region and zone.

2. Set up a new account for the client in BUTP.

3. The client needs to set up and configure [gcloud CLI](https://cloud.google.com/sdk/gcloud) for their project.

4. The client needs to run the script `virment/scripts/gcp/gcloud-cli-setup.sh` to set up the required infrastructure and generate a service account with a key.

5. Share the Cloud Compute images with the client's service account by running `virment/scripts/gcp/share_images.sh` (see inline comment for usage).

6. The client can now create a new `Region` config in BUTP with the access key being the service account key, and start using it to create a new cluster.
