# Hello BOSH

## Setup CFCR

There are 2 steps involved to setup CFCR (to manage Kubernetes via BOSH):

 - Setup BOSH, which can be done via BBL.
 - Setup CFCR BOSH.

### 1. Deploy BOSH

[BBL](https://github.com/cloudfoundry/bosh-bootloader/releases) simplifies the setup of BOSH in an IaaS (e.g. [AWS](https://bosh.io/docs/init-aws/)). Follow [this guide](https://docs.cloudfoundry.org/deploying/common/aws.html) after you have fulfilled these prerequisites:

 - Have an AWS account
 - Have a domain
 - Have a SSL cert and matching key

Eventually you will do these:

```bash
$ aws iam put-user-policy --user-name babylon.cf --policy-name "BabylonUserPolicy" --policy-document "$(cat etc/bbl-iam-policy.json)"
$ export BBL_AWS...
$ ...
$ bbl plan --iaas aws \
  --aws-access-key-id $BBL_AWS_ACCESS_KEY_ID --aws-secret-access-key $BBL_AWS_SECRET_ACCESS_KEY --aws-region $BBL_AWS_REGION \
  --lb-type cf --lb-cert etc/cert.pem --lb-key etc/key.pem --lb-domain cloud.yclian.com
$ bbl up
```

Once it's up, register the settings to your environment with:

```bash
$ eval "$(bbl print-env)"
```

Some other things you may do (I still don't know much yet):

```bash
$ bbl director-address
$ bosh login
$ bosh env
Using environment 'https://10.0.0.6:25555' as client 'admin'

Name      bosh-bbl-env-caspian-2018-09-11t05-25z
UUID      6aafa272-892f-4f71-98ef-1e49363b8832
Version   267.5.0 (00000000)
CPI       aws_cpi
Features  compiled_package_cache: disabled
          config_server: enabled
          dns: disabled
          snapshots: disabled
User      admin
$ bbl ssh --director
```

Not sure what this actually means:

```bash
$ bosh cloud-config
```

Initially, this shows you there's nobody home:

```bash
$ bosh deployments
Using environment 'https://10.0.0.6:25555' as client 'admin'

Name  Release(s)  Stemcell(s)  Team(s)

0 deployments
```

## Problems

### `bbl plan` isn't working on Windows

```
panic: asset: Asset(terraform): Asset terraform not found

goroutine 1 [running]:
github.com/cloudfoundry/bosh-bootloader/terraform/binary_dist.MustAsset(0x1cf871e, 0x9, 0xc042268300, 0x0, 0x0)
        /go/src/github.com/cloudfoundry/bosh-bootloader/terraform/binary_dist/tf_binary_dist_windows_amd64.go:113 +0x135
github.com/cloudfoundry/bosh-bootloader/terraform.installTfBinary(0xc0423fee10, 0x30, 0x1eb7b20, 0xc0423b2ea0, 0x0, 0x0)
        /go/src/github.com/cloudfoundry/bosh-bootloader/terraform/binary_path.go:53 +0x41
```

## References

 - [Comparing Bosh with Ansible and Chef](https://content.pivotal.io/blog/comparing-bosh-ansible-chef-part-1) ([Source](https://github.com/BrianMMcClain/bosh-vs-ansible-vs-chef/))
 - [Deploying to BOSH](https://mariash.github.io/learn-bosh/#deploy)
 - [Kubo](https://github.com/cloudfoundry-incubator/kubo-release/releases) (Was following this before `bbl`)

## [Terms](https://bosh.io/docs/terminology/)

 - [CPI -- Cloud Provider Interface](https://bosh.io/docs/cpi-api-v1/)
 - [Stemcell](https://bosh.io/docs/stemcell/)
