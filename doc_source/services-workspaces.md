# How WorkSpaces uses AWS KMS<a name="services-workspaces"></a>

You can use [WorkSpaces](https://aws.amazon.com/workspaces/) to provision a cloud\-based desktop \(a *WorkSpace*\) for each of your end users\. When you launch a new WorkSpace, you can choose to encrypt its volumes and decide which [AWS KMS key](concepts.md#kms_keys) to use for the encryption\. You can choose the [AWS managed key](concepts.md#aws-managed-cmk) for WorkSpaces \(**aws/workspaces**\) or a symmetric [customer managed key](concepts.md#customer-cmk)\.

**Important**  
WorkSpaces supports only symmetric KMS keys\. You cannot use an asymmetric KMS key to encrypt the volumes in an WorkSpaces\. For help determining whether a KMS key is symmetric or asymmetric, see [Identifying symmetric and asymmetric KMS keys](find-symm-asymm.md)\.

For more information about creating WorkSpaces with encrypted volumes, go to [Encrypt a WorkSpace](https://docs.aws.amazon.com/workspaces/latest/adminguide/wsp_encrypt_workspace.html) in the *Amazon WorkSpaces Administration Guide*\.

**Topics**
+ [Overview of WorkSpaces encryption using AWS KMS](#services-workspaces-overview)
+ [WorkSpaces encryption context](#services-workspaces-encryptioncontext)
+ [Giving WorkSpaces permission to use a KMS key on your behalf](#services-workspaces-permissions)

## Overview of WorkSpaces encryption using AWS KMS<a name="services-workspaces-overview"></a>

When you create WorkSpaces with encrypted volumes, WorkSpaces uses Amazon Elastic Block Store \(Amazon EBS\) to create and manage those volumes\. Both services use your AWS KMS key to work with the encrypted volumes\. For more information about EBS volume encryption, see the following documentation:
+ [How Amazon Elastic Block Store \(Amazon EBS\) uses AWS KMS](services-ebs.md) in this guide
+ [Amazon EBS Encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) in the *Amazon EC2 User Guide for Windows Instances*

When you launch WorkSpaces with encrypted volumes, the end\-to\-end process works like this:

1. <a name="WSP-you-specify-key"></a>You specify the KMS key to use for encryption as well as the WorkSpace's user and directory\. This action creates a [grant](grants.md) that allows WorkSpaces to use your KMS key only for this WorkSpace—that is, only for the WorkSpace associated with the specified user and directory\.

1. WorkSpaces creates an encrypted EBS volume for the WorkSpace and specifies the KMS key to use as well as the volume's user and directory \(the same information that you specified at [Step 1](#WSP-you-specify-key)\)\. This action creates a [grant](grants.md) that allows Amazon EBS to use your KMS key only for this WorkSpace and volume—that is, only for the WorkSpace associated with the specified user and directory, and only for the specified volume\.

1. <a name="WSP-EBS-requests-encrypted-volume-data-key"></a>Amazon EBS requests a volume data key that is encrypted under your KMS key and specifies the WorkSpace user's `Sid` and directory ID as well as the volume ID as encryption context\.

1. <a name="WSP-KMS-creates-data-key"></a>AWS KMS creates a new data key, encrypts it under your KMS key, and then sends the encrypted data key to Amazon EBS\.

1. <a name="WSP-uses-EBS-to-attach-encrypted-volume"></a>WorkSpaces uses Amazon EBS to attach the encrypted volume to your WorkSpace\. Amazon EBS sends the encrypted data key to AWS KMS with a [https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) request and specifies the WorkSpace user's `Sid`, its directory ID, and the the volume ID, which is used as the [encryption context](#services-workspaces-encryptioncontext)\.

1. AWS KMS uses your KMS key to decrypt the data key, and then sends the plaintext data key to Amazon EBS\.

1. Amazon EBS uses the plaintext data key to encrypt all data going to and from the encrypted volume\. Amazon EBS keeps the plaintext data key in memory for as long as the volume is attached to the WorkSpace\.

1. Amazon EBS stores the encrypted data key \(received at [Step 4](#WSP-KMS-creates-data-key)\) with the volume metadata for future use in case you reboot or rebuild the WorkSpace\.

1. When you use the AWS Management Console to remove a WorkSpace \(or use the [https://docs.aws.amazon.com/workspaces/latest/devguide/API_TerminateWorkspaces.html](https://docs.aws.amazon.com/workspaces/latest/devguide/API_TerminateWorkspaces.html) action in the WorkSpaces API\), WorkSpaces and Amazon EBS retire the grants that allowed them to use your KMS key for that WorkSpace\.

## WorkSpaces encryption context<a name="services-workspaces-encryptioncontext"></a>

WorkSpaces doesn't use your AWS KMS key directly for cryptographic operations \(such as [https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html](https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html), [https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html), [https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html), etc\.\), which means WorkSpaces doesn't send requests to AWS KMS that include an [encryption context](concepts.md#encrypt_context)\. However, when Amazon EBS requests an encrypted data key for the encrypted volumes of your WorkSpaces \([Step 3](#WSP-EBS-requests-encrypted-volume-data-key) in the [Overview of WorkSpaces encryption using AWS KMS](#services-workspaces-overview)\) and when it requests a plaintext copy of that data key \([Step 5](#WSP-uses-EBS-to-attach-encrypted-volume)\), it includes encryption context in the request\. The encryption context provides [additional authenticated data](https://docs.aws.amazon.com/crypto/latest/userguide/cryptography-concepts.html#term-aad) \(AAD\) that AWS KMS uses to ensure data integrity\. The encryption context is also written to your AWS CloudTrail log files, which can help you understand why a given AWS KMS key was used\. Amazon EBS uses the following for the encryption context:
+ The `sid` of the AWS Directory Service user that is associated with the WorkSpace
+ The directory ID of the AWS Directory Service directory that is associated with the WorkSpace
+ The volume ID of the encrypted volume

The following example shows a JSON representation of the encryption context that Amazon EBS uses:

```
{
  "aws:workspaces:sid-directoryid": "[S-1-5-21-277731876-1789304096-451871588-1107]@[d-1234abcd01]",
  "aws:ebs:id": "vol-1234abcd"
}
```

## Giving WorkSpaces permission to use a KMS key on your behalf<a name="services-workspaces-permissions"></a>

You can protect your workspace data under the AWS managed key for WorkSpaces \(**aws/workspaces**\) or a customer managed key If you use a customer managed key, you need to give WorkSpaces permission to use the KMS key on behalf of the WorkSpaces administrators in your account\. The AWS managed key for WorkSpaces has the required permissions by default\.

To prepare your customer managed key for use with WorkSpaces, use the following procedure\.

1. [Add the WorkSpaces administrators to the list of key users in the KMS key's key policy](#workspaces-permissions-key-users)

1. [Give the WorkSpaces administrators additional permissions with an IAM policy](#workspaces-permissions-iam-policy)

WorkSpaces administrators also need permission to use WorkSpaces\. For more information about these permissions, go to [Controlling Access to WorkSpaces Resources](https://docs.aws.amazon.com/workspaces/latest/adminguide/wsp_iam.html) in the *Amazon WorkSpaces Administration Guide*\.

### Part 1: Adding WorkSpaces administrators to a KMS key's key users<a name="workspaces-permissions-key-users"></a>

To give WorkSpaces administrators the permissions that they require, you can use the AWS Management Console or the AWS KMS API\.

#### To add WorkSpaces administrators as key users for a KMS key \(console\)<a name="workspaces-permissions-users-console"></a>

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. In the navigation pane, choose **Customer managed keys**\.

1. Choose the key ID or alias of your preferred customer managed key

1. Choose the **Key policy** tab\. Under **Key users**, choose **Add**\.

1. In the list of IAM users and roles, select the users and roles that correspond to your WorkSpaces administrators, and then choose **Attach**\.

#### To add WorkSpaces administrators as key users for a KMS key \(AWS KMS API\)<a name="workspaces-permissions-users-api"></a>

1. Use the [GetKeyPolicy](https://docs.aws.amazon.com/kms/latest/APIReference/API_GetKeyPolicy.html) operation to get the existing key policy, and then save the policy document to a file\.

1. Open the policy document in your preferred text editor\. Add the IAM users and roles that correspond to your WorkSpaces administrators to the policy statements that [give permission to key users](key-policies.md#key-policy-default-allow-users)\. Then save the file\.

1. Use the [PutKeyPolicy](https://docs.aws.amazon.com/kms/latest/APIReference/API_PutKeyPolicy.html) operation to apply the key policy to the KMS key\.

### Part 2: Giving WorkSpaces administrators extra permissions<a name="workspaces-permissions-iam-policy"></a>

If you are using a customer managed key to protect your WorkSpaces data, in addition to the permissions in the key users section of the [default key policy](key-policies.md#key-policy-default), WorkSpaces administrators need permission to create [grants](grants.md) on the KMS key\. Also, if they use the [AWS Management Console](https://console.aws.amazon.com/console/home) to create WorkSpaces with encrypted volumes, WorkSpaces administrators need permission to list aliases and list keys\. For information about creating and editing IAM user policies, see [Managed Policies and Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) in the *IAM User Guide*\.

To give these permissions to your WorkSpaces administrators, use an IAM policy\. Add an policy statement similar to the following example to the IAM policy for each WorkSpaces administrator\. Replace the example KMS key ARN \(`arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab`\) with a valid one\. If your WorkSpaces administrators use only the WorkSpaces API \(not the console\), you can omit the second policy statement with the `"kms:ListAliases"` and `"kms:ListKeys"` permissions\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "kms:CreateGrant",
      "Resource": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab"
    },
    {
      "Effect": "Allow",
      "Action": [
        "kms:ListAliases",
        "kms:ListKeys"
      ],
      "Resource": "*"
    }
  ]
}
```