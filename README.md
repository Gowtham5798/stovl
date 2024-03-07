# Creation of infra in GCP

Terraform code in this folder will do the following

1) Creation of Cloud run instance.
2) Creation of Service account and IAM role used for the service account.
3) Creation of Internal load balancer.
4) Creation of Self-signed certificate and attach to the load balancer
5) Creation of Cloud DNS
6) Creation of A DNS Record for the private IP of internal load balancer.

## Installation

Use the below commands to create about above mentioned resources

```bash
terraform init
```
```bash
terraform plan -var-file=vars/dev.tfvars
```
```bash
terraform apply -var-file=vars/dev.tfvars
```

## Validation

Use below commands in the VM which has no external IP to validate and see the results.

```bash
# This command will skip the validation of certificate since self-signed certificate being used here.
curl -k -X POST -H "Content-Type: application/json" -d '{"key1":"value1", "key2":"value2"}' https://provider.skipify.internal.net/echo
```
In order to validate the with certificate pls follow below steps:

```bash
# This command will show the cacert used here
openssl s_client -showcerts -connect provider.skipify.internal.net:443 < /dev/null
```

Copy the cacert from the above command and paste in the vi editor to a new file.

```bash
# This command will show the cacert used here
vi cert.pem
```
Pass the cert.pem in the curl command to validate the certificate

```bash
# This command allow us to validate with certificate since we are passing the certificate along with command so need to use -k or -insecure to skip the certificate validation
curl --cacert cert.pem -H "Content-Type: application/json" -d '{"key": "Value"}' https://provider.skipify.internal.net/echo
```

```bash
# This command show more details on certificate being used
openssl x509 -in cert.pem -noout -text
```

## Result

On successfull execution validation step below is the expected result.

{"Trace":{"svc_name":"provider.skipify"}, "payload":{"key":"value"}}
