# DEVICE\_CERTIFICATE\_KEY\_QUALITY\_CHECK<a name="audit-chk-device-cert-key-quality"></a>

AWS IoT customers often rely on TLS mutual authentication using X\.509 certificates for authenticating to AWS IoT message broker\. These certificates and their certificate authority certificates must be registered in their AWS IoT account before they are used\. AWS IoT performs basic sanity checks on these certificates when they are registered\. These checks include:
+ They must be in a valid format\.
+ They must be signed by a registered certificate authority\.
+ They must still be within their validity period \(in other words, they haven't expired\)\.
+ Their cryptographic key sizes must meet a minimum required size \(for RSA keys, they must be 2048 bits or larger\)\.

This audit check provides the following additional tests of the quality of your cryptographic key:
+ CVE\-2008\-0166 – Check whether the key was generated using OpenSSL 0\.9\.8c\-1 up to versions before 0\.9\.8g\-9 on a Debian\-based operating system\. Those versions of OpenSSL use a random number generator that generates predictable numbers, making it easier for remote attackers to conduct brute force guessing attacks against cryptographic keys\.
+ CVE\-2017\-15361 – Check whether the key was generated by the Infineon RSA library 1\.02\.013 in Infineon Trusted Platform Module \(TPM\) firmware, such as versions before 0000000000000422 – 4\.34, before 000000000000062b – 6\.43, and before 0000000000008521 – 133\.33\. That library mishandles RSA key generation, making it easier for attackers to defeat some cryptographic protection mechanisms through targeted attacks\. Examples of affected technologies include BitLocker with TPM 1\.2, YubiKey 4 \(before 4\.3\.5\) PGP key generation, and the Cached User Data encryption feature in Chrome OS\.

AWS IoT Device Defender reports certificates as noncompliant if they fail these tests\.

Severity: **Critical**

## Details<a name="audit-chk-device-cert-key-quality-details"></a>

This check applies to device certificates that are ACTIVE or PENDING\_TRANSFER\.

The following reason codes are returned when this check finds a noncompliant certificate:
+ CERTIFICATE\_KEY\_VULNERABILITY\_CVE\-2017\-15361
+ CERTIFICATE\_KEY\_VULNERABILITY\_CVE\-2008\-0166

## Why it matters<a name="audit-chk-device-cert-key-quality-why-it-matters"></a>

When a device uses a vulnerable certificate, attackers can more easily compromise that device\.

## How to fix it<a name="audit-chk-device-cert-key-quality-how-to-fix"></a>

Update your device certificates to replace those with known vulnerabilities\.

If you are using the same certificate on multiple devices, you might want to:

1. Provision new, unique certificates and attach them to each device\. 

1. Verify that the new certificates are valid and the devices can use them to connect\.

1. Use [UpdateCertificate](https://docs.aws.amazon.com/iot/latest/apireference/API_UpdateCertificate.html) to mark the old certificate as REVOKED in AWS IoT\. You can also use mitigation actions to:
   + Apply the `UPDATE_DEVICE_CERTIFICATE` mitigation action on your audit findings to make this change\. 
   + Apply the `ADD_THINGS_TO_THING_GROUP` mitigation action to add the device to a group where you can take action on it\.
   + Apply the `PUBLISH_FINDINGS_TO_SNS` mitigation action if you want to implement a custom response in response to the Amazon SNS message\. 

   For more information, see [Mitigation actions](device-defender-mitigation-actions.md)\. 

1. Detach the old certificate from each of the devices\.