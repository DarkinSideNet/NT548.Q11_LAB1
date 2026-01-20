# NT548.Q11_LAB1

## CloudFormation (Nested Stacks)

Các template:
- Root stack: cloudformation/root.yaml
- Modules:
	- cloudformation/modules/vpc.yaml
	- cloudformation/modules/security_groups.yaml
	- cloudformation/modules/ec2.yaml

### Deploy (package + deploy)

Nested stacks cần upload template con lên S3, nên dùng `aws cloudformation package`.

1) Tạo (hoặc chọn) 1 S3 bucket để chứa templates:

```bash
aws --region us-east-1 s3 mb s3://<YOUR_BUCKET_NAME>
```

2) Package root template (tự upload templates con lên S3 và tạo file output đã rewrite TemplateURL):

```bash
aws --region us-east-1 cloudformation package \
	--template-file cloudformation/root.yaml \
	--s3-bucket <YOUR_BUCKET_NAME> \
	--output-template-file cloudformation/packaged-root.yaml
```

3) Deploy stack:

```bash
aws --region us-east-1 cloudformation deploy \
	--stack-name nt548-lab1 \
	--template-file cloudformation/packaged-root.yaml \
	--parameter-overrides \
		TemplatesS3Bucket=<YOUR_BUCKET_NAME> \
		AllowedSshCidr=42.115.61.52/32 \
		KeyName=Terreform-key \
		AmiId=ami-0c398cb65a93047f2 \
		InstanceType=t3.micro
	--capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND
```

Nếu bạn đã cấu hình region mặc định (`aws configure`), có thể bỏ `--region us-east-1`.

### Xem outputs

```bash
aws --region us-east-1 cloudformation describe-stacks \
	--stack-name nt548-lab1 \
	--query 'Stacks[0].Outputs'
```
