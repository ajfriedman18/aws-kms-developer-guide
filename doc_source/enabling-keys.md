# Enabling and disabling keys<a name="enabling-keys"></a>

You can disable and reenable the [AWS KMS keys](concepts.md#kms_keys) that you manage\. You cannot enable or disable AWS managed keys\.

When you create a KMS key, it is enabled by default\. If you disable a KMS key, it cannot be used to encrypt or decrypt data until you re\-enable it\. AWS managed keys are permanently enabled for use by [services that use AWS KMS](service-integration.md)\. You cannot disable them\.

You can also delete KMS keys\. For more information, see [Deleting AWS KMS keys](deleting-keys.md)\.

**Note**  
AWS KMS does not rotate the key material of customer managed keys while they are disabled\. For more information, see [How automatic key rotation works](rotate-keys.md#rotate-keys-how-it-works)\.

**Topics**
+ [Enabling and disabling KMS keys \(console\)](#enabling-keys-console)
+ [Enabling and disabling KMS keys \(AWS KMS API\)](#enabling-keys-api)

## Enabling and disabling KMS keys \(console\)<a name="enabling-keys-console"></a>

You can use the AWS KMS console to enable and disable [customer managed keys](concepts.md#customer-cmk)\.

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. In the navigation pane, choose **Customer managed keys**\.

1. Select the check box for the KMS keys that you want to enable or disable\.

1. To enable a KMS key, choose **Key actions**, **Enable**\. To disable a KMS key, choose **Key actions**, **Disable**\.

## Enabling and disabling KMS keys \(AWS KMS API\)<a name="enabling-keys-api"></a>

The [EnableKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_EnableKey.html) operation enables a disabled AWS KMS key\. These examples use the [AWS Command Line Interface \(AWS CLI\)](https://aws.amazon.com/cli/), but you can use any supported programming language\. The `key-id` parameter is required\.

This operation does not return any output\. To see the key status, use the [DescribeKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html) operation\.

```
$ aws kms enable-key --key-id 1234abcd-12ab-34cd-56ef-1234567890ab
```

The [DisableKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_DisableKey.html) operation disables an enabled KMS key\. The `key-id` parameter is required\.

```
$ aws kms disable-key --key-id 1234abcd-12ab-34cd-56ef-1234567890ab
```

This operation does not return any output\. To see the key status, use the [DescribeKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html) operation, and see the Enabled field\.

```
$ aws kms describe-key --key-id 1234abcd-12ab-34cd-56ef-1234567890ab
{
    "KeyMetadata": {
        "Origin": "AWS_KMS",
        "KeyId": "1234abcd-12ab-34cd-56ef-1234567890ab",
        "Description": "",
        "KeyManager": "CUSTOMER",
        "MultiRegion": false,
        "Enabled": false,
        "KeyState": "Disabled",
        "KeyUsage": "ENCRYPT_DECRYPT",        
        "CreationDate": 1502910355.475,
        "Arn": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab",
        "AWSAccountId": "111122223333"
        "KeySpec": "SYMMETRIC_DEFAULT",
        "CustomerMasterKeySpec": "SYMMETRIC_DEFAULT",
        "EncryptionAlgorithms": [
            "SYMMETRIC_DEFAULT"
        ]
    }
}
```