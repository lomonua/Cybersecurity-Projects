# Cloud Security Lab — AWS S3 IAM & RBAC (Controlled Lab)

**Author:** Lawrence Omonua  
**Date:** 2026-01-19  
**Environment:** AWS (IAM, S3, CloudTrail) — isolated personal AWS account  
**Scope & Authorization:** This lab was performed in my own AWS account using intentionally
created resources.

---

## Executive Summary
This lab demonstrates secure access control for Amazon S3 using AWS Identity and Access Management (IAM)
and Attribute-Based Access Control (ABAC). I created an S3 bucket, implemented multiple IAM users and policies, 
and validated permissions using the AWS CLI. Administrative, read/write, read-only,
and deny-all policies and userrs were tested to confirm proper enforcement. AWS CloudTrail was used to verify and
audit access attempts.

---

## Lab Objectives
- Create and secure an S3 bucket
- Implement IAM users and policies using least-privilege principles
- Validate permissions through AWS CLI actions
- Observe and verify access events in CloudTrail

---

## Lab Workflow

### **Setup**
1. Created an S3 bucket in `us-east-2`.
2. Enabled AWS CloudTrail for API activity logging.
3. Uploading documents to the S3 Bucket and keeping one file (Prices.txt) so that it can be
   uploaded later to test the commands and access controls.

---

### **IAM Configuration**
1. Created IAM users for testing role-based access.
   - **AdminUser**
   - **ReadandWrite**
   - **ReadOnlyUser**
   - **DenyAllUser**
2. Attached AWS managed policies to the AdminUser, ReadOnlyUser and DenyAllUser users.
3. Created the inline policy for the ReadandWrite user and attached it to the user

---

### **Administrative Access Validation**
After configuring IAM policies, permissions were validated using the AWS CLI. Commands executed and their purposes:

#### **Verify The User (Read)**
```bash
aws sts get-caller-identity
```
#### **List Bucket Contents (Read)**
```bash
aws s3 ls s3://cybersecurity-iam-bucket/ --region us-east-2
```
#### **Upload a File (Write)**
```bash
aws s3 cp .\prices.txt s3://cybersecurity-iam-bucket/ --region us-east-2
```
#### **Download a File (Read)**
```bash
aws s3 cp s3://cybersecurity-iam-bucket/prices.txt ./prices.txt --region us-east-2
```
#### **Delete a File (Remove)**
```bash
aws s3 rm s3://cybersecurity-iam-bucket/prices.txt --region us-east-2
```
#### Observations per User
| User             | List | Upload | Download | Delete |
|------------------|------|--------|----------|--------|
| **AdminUser**    | ✅    | ✅      | ✅        | ✅      |
| **ReadandWrite** | ✅    | ✅      | ✅        | ❌      |
| **ReadOnlyUser** | ✅    | ❌      | ✅        | ❌      |
| **DenyAllUser**  | ❌    | ❌      | ❌        | ❌      |


#### **Results Summary**
AdminUser: Full access — can read, write, download, and delete objects.

ReadandWrite: Can list, upload, and download; delete denied.

ReadOnlyUser: Can list and download; upload and delete denied.

DenyAllUser: Cannot perform any actions.

## **CloudTrail Verification**
During this lab, AWS CloudTrail recorded five events, all of which were GetCallerIdentity API calls from AWS Security Token Service (STS). Each event corresponds to a different IAM user (ReadOnlyUser, ReadandWrite, AdminUser, and DenyAllUser), confirming that each set of credentials was successfully authenticated.

## **Risk Assessment**
Severity: High - improperly configured IAM roles can expose sensitive S3 data.
Likelihood: Medium - misconfigurations are common in cloud environments.
Impact: Unauthorized access, potential data exfiltration, and accidental deletion

## **Recommended Security Controls**
Enforce least privilege for all IAM roles, policies and users

Monitor S3 access via CloudTrail and alerts

Periodically audit IAM policies and user permissions

Rotate and limit access keys; avoid long-lived credentials

Consider MFA or session-based role assumption for sensitive operations

## **Conclusion**
This lab demonstrates role-based access control for AWS S3, validating that IAM policies and users enforce permissions as expected. The AdminUser, ReadandWrite, ReadOnlyUser, and DenyAllUser all performed according to their assigned privileges, and CloudTrail verified all access attempts. This lab highlights best practices for secure S3 access management in cloud environments.