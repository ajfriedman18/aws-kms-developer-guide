# What is AWS Key Management Service?<a name="overview"></a>

AWS Key Management Service \(AWS KMS\) is a managed service that makes it easy for you to create and control the encryption keys that are used to encrypt your data\. AWS KMS uses hardware security modules \(HSM\) to protect and validate your AWS KMS keys under the [FIPS 140\-2 Cryptographic Module Validation Program](https://csrc.nist.gov/projects/cryptographic-module-validation-program/Certificate/3139), except in the China \(Beijing\) and China \(Ningxia\) Regions\. 

AWS KMS integrates with most [AWS services](https://aws.amazon.com/kms/features/#AWS_Service_Integration) that encrypt your data\. AWS KMS also integrates with [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/) to log use of your KMS keys for auditing, regulatory, and compliance needs\.

You can create and manage your AWS KMS keys:
+ [Create](create-keys.md), [edit](editing-keys.md), and [view](viewing-keys.md) [symmetric and asymmetric KMS keys](symmetric-asymmetric.md)
+ Control access to your KMS keys by using [key policies](key-policies.md), [IAM policies](iam-policies.md), and [grants](grants.md)\. AWS KMS supports [attribute\-based access control](abac.md) \(ABAC\)\. You can also refine policies by using [condition keys](policy-conditions.md)\. 
+ [Create, delete, list, and update *aliases*](kms-alias.md), friendly names for your KMS keys\. You can also [use aliases to control access](alias-authorization.md) to your KMS keys\.
+ [Tag your KMS keys](tagging-keys.md) for identification, automation, and cost tracking\. You can also [use tags to control access](tag-authorization.md) to your KMS keys\.
+ [Enable and disable](enabling-keys.md) KMS keys\.
+ Enable and disable [automatic rotation](rotate-keys.md) of the cryptographic material in a KMS keys\.
+ [Delete KMS keys](deleting-keys.md) to complete the key lifecycle\.

You can use your KMS keys in [cryptographic operations](concepts.md#cryptographic-operations)\. For examples, see [Programming the AWS KMS API](programming-top.md)\.
+ Encrypt, decrypt, and re\-encrypt data with symmetric or asymmetric KMS keys\.
+ Sign and verify messages with asymmetric KMS keys\.
+ Generate exportable symmetric data keys and asymmetric data key pairs\.
+ Generate random numbers suitable for cryptographic applications\.

You can use the advanced features of AWS KMS\.
+ [Import cryptographic material](importing-keys.md) into a KMS key
+ Create KMS keys in your own [custom key store](custom-key-store-overview.md) backed by a AWS CloudHSM cluster
+ Connect directly to AWS KMS through a [private endpoint in your VPC](kms-vpc-endpoint.md)
+ Use [hybrid post\-quantum TLS](pqtls.md) to provide forward\-looking encryption in transit for the data that you send to AWS KMS\.

By using AWS KMS, you gain more control over access to data you encrypt\. You can use the key management and cryptographic features directly in your applications or through AWS services integrated with AWS KMS\. Whether you write applications for AWS or use AWS services, AWS KMS enables you to maintain control over who can use your AWS KMS keys and gain access to your encrypted data\.

AWS KMS integrates with AWS CloudTrail, a service that delivers log files to your designated Amazon S3 bucket\. By using CloudTrail you can monitor and investigate how and when your KMS keys have been used and who used them\.

**AWS KMS in AWS Regions**  
The AWS Regions in which AWS KMS is supported are listed in [AWS Key Management Service Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/kms.html)\. If an AWS KMS feature is not supported in an AWS Region that AWS KMS supports, the regional difference is described in the topic about the feature\. 
<a name="pricing"></a>
**AWS KMS pricing**  
As with other AWS products, using AWS KMS does not require contracts or minimum purchases\. For more information about AWS KMS pricing, see [AWS Key Management Service Pricing](https://aws.amazon.com/kms/pricing/)\.

**Service level agreement**  
AWS Key Management Service is backed by a [service level agreement](https://aws.amazon.com/kms/sla/) that defines our service availability policy\.

**Learn more**
+ To learn about the terms and concepts used in AWS KMS, see [AWS KMS Concepts](concepts.md)\.
+ For information about the AWS KMS API, see the [AWS Key Management Service API Reference](https://docs.aws.amazon.com/kms/latest/APIReference/)\. For examples in different programming languages, see [Programming the AWS KMS API](programming-top.md)\.
+ For detailed technical information about how AWS KMS uses cryptography and secures KMS keys, see [AWS Key Management Service Cryptographic Details](https://docs.aws.amazon.com/kms/latest/cryptographic-details/)\. The Cryptographic Details documentation does not describe how AWS KMS works in the China \(Beijing\) and China \(Ningxia\) Regions\.
+ For help with questions about AWS KMS, see the [AWS Key Management Service Discussion Forum](https://forums.aws.amazon.com/forum.jspa?forumID=182)\.

**AWS KMS in the AWS SDKs**
+ [AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/reference/kms/index.html)
+ [AWS SDK for \.NET](https://docs.aws.amazon.com/sdkfornet/v3/apidocs/items/KeyManagementService/NKeyManagementServiceModel.html)
+ [AWS SDK for C\+\+](https://sdk.amazonaws.com/cpp/api/LATEST/class_aws_1_1_k_m_s_1_1_k_m_s_client.html)
+ [AWS SDK for Go](https://docs.aws.amazon.com/sdk-for-go/api/service/kms/)
+ [AWS SDK for Java](https://docs.aws.amazon.com/sdk-for-java/latest/reference/com/amazonaws/services/kms/model/package-summary.html)
+ [AWS SDK for JavaScript](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/KMS.html)
+ [AWS SDK for PHP](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-kms-2014-11-01.html)
+ [AWS SDK for Python \(Boto3\)](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/kms.html)
+ [AWS SDK for Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/KMS/Client.html)