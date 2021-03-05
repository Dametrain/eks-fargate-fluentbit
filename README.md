# eks-fargate-fluentbit

This is a rip off of the blog post https://aws.amazon.com/blogs/containers/fluent-bit-for-amazon-eks-on-aws-fargate-is-here/

Instructions

There is a fixed namespace called aws-observability that you can use to send logs to, this has a cluster wide effect, so you can send from any namespace in the cluster. This is great since you don’t have to create a sidecar for Fargate logging, some have called it a hide-car pattern. *Note* Today integrating with Datadog and Splunk is supported via Kinesis Firehose as documented below, native integration is being discuss, you can follow https://github.com/aws/containers-roadmap/issues/1242. 

If you need to spin up a Fargate enabled EKS cluster, you can use the eks-fargate-existing-vpc.template. Make sure to change the region, VPC and Subnets to yours appropriately. 

Currently the only supported outputs are es, firehose, kinesis_firehose, cloudwatch, cloudwatch_logs, and kinesis. Fargate validates against the following supported output, so you will get an error if it is different.

Once you have your cluster configured you will need to create a Kinesis Firehose delivery stream, this is very straight forward and can be done for both Datadog and Splunk as a destination.

After creating the Kinesis Data Firehose configure the output using the instructions below:

1.	Send logs to Kinesis Data Firehose. You have two output options when using Kinesis Data Firehose:
•	kinesis_firehose – An output plugin written in C.
•	firehose – An output plugin written in Golang.
The following example shows you how to use the kinesis_firehose plugin to send logs to Kinesis Data Firehose.
iii.	Save the following contents to a file named aws-logging-firehose-configmap.yaml.
xvi.	Apply the manifest to your cluster.
kubectl apply -f aws-logging-firehose-configmap.yaml
xvii.	Download the Kinesis Data Firehose IAM policy to your computer. You can also view the policy on GitHub.
curl -o permissions.json https://raw.githubusercontent.com/aws-samples/amazon-eks-fluent-logging-examples/mainline/examples/fargate/kinesis-firehose/permissions.json
2.	Create an IAM policy.
aws iam create-policy --policy-name <eks-fargate-logging-policy> --policy-document file://permissions.json
3.	Attach the IAM policy to the pod execution role specified for your Fargate profile. Replace <111122223333> with your account ID.
4.	aws iam attach-role-policy \
5.	  --policy-arn arn:aws:iam::<111122223333>:policy/<eks-fargate-logging-policy> \
  --role-name <your-pod-execution-role>
  
  
Size considerations
We suggest that you plan for up to 50 MB of memory for your logs. If you expect your application to generate logs at very high throughput then you should plan for up to 100 MB.

