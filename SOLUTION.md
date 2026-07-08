# Lab Solution: IAM Policies and Roles

**Student Name:** Pragash KUMARAVEL 
**Date:** 08-07-26  
**Lab Completion Time:** 12h30 minutes

---

## Part 1: Understanding IAM Policy Structure

### Task 1: Policy Components Explanation

**Explain each component in your own words:**

**Version:**
```
It will be always 2012-10-17 as it is the current, standard version of the policy language.
```

**Statement:**
```
Statement key is followed by an array containing objects,each object inside the array is an individual rule.
```

**Sid:**
```
Sid will be the first stament and an optional stament ID.
```

**Effect:**
```
This stament results only allow or deny,
```

**Action:**
```
The specific (S3 bucket) sperations are granting or blocking.
```

**Resource:**
```
The specific AWS resources the action applies to, defined by their Amazon Resource Name (ARN)
```

---

## Part 2: Custom IAM Policies Created

### S3 Read-Only Policy

**Policy Name:** S3-ReadOnly-SpecificBucket

**Bucket Name Used:** pragash-kumar-bucket

**Policy JSON:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    
    
    
  ]
}
```

**Screenshot 1: S3 Custom Policy**
![S3 Policy](screenshots/01-s3-policy.png)

---

### EC2 Start/Stop Policy

**Policy Name:** S3-ReadOnly-SpecificBucket

**Policy ARN:** arn:aws:iam::033216807267

**Screenshot 2: EC2 Custom Policy**
![EC2 Policy](screenshots/02-ec2-policy.png)

---

### CloudWatch Logs Write Policy

**Policy Name:** CloudWatch-Logs-Write-Only

**Policy ARN:** arn:aws:iam::033216807267
______________________
**Screenshot 3: CloudWatch Logs Policy**
![CloudWatch Policy](screenshots/03-cloudwatch-policy.png)

---

## Part 3: Policy Attachments

### Policy Attached to User

**User Name:** alice

**Policy Attached:** S3-ReadOnly-SpecificBucket

**Attachment Method:** ☐ Console ☐ CLI

**CLI Command (if used):**
```bash
_____________________________________________________________
_____________________________________________________________
```

**Screenshot 4: Policy Attached**
![Policy Attachment](screenshots/04-policy-attached.png)

---

## Part 4: IAM Roles Created

### EC2 Service Role

**Role Name:** EC2-S3-ReadOnly-Role

**Role ARN:** arn:aws:iam::033216807267:role/EC2-S3-ReadOnly-Role

**Trusted Entity:** ___________________________

**Attached Policies:**
1. AmazonS3ReadOnlyAccess
2. ___________________________

**Trust Relationship JSON:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    
    
  ]
}
```

**Screenshot 5: EC2 Service Role**
![EC2 Role](screenshots/05-ec2-role.png)

---

### Lambda Execution Role

**Role Name:** Lambda-Basic-Execution-Role

**Role ARN:** ___________________________

**Attached Policies:**
1. AWSLambdaBasicExecutionRole
2. ___________________________

**Screenshot 6: Lambda Role**
![Lambda Role](screenshots/06-lambda-role.png)

---

### Cross-Account Access Role

**Role Name:** CrossAccount-ReadOnly-Role

**Role ARN:** arn:aws:iam::033216807267:role/CrossAccount-ReadOnly-Role

**External Account ID:** ___________________________

**External ID:** unique-external-id-123

**Attached Policies:**
1. S3-ReadOnly-SpecificBucket

**Screenshot 7: Cross-Account Role**
![Cross-Account Role](screenshots/07-cross-account-role.png)

---

## Part 5: Policy Testing

### Policy Simulator Results

**Policy Tested:** S3-ReadOnly-SpecificBucket

**Test Results:**

| Action | Expected Result | Actual Result | Pass/Fail |
|--------|----------------|---------------|-----------|
| s3:GetObject | Allowed | | ☐ Pass ☐ Fail |
| s3:PutObject | Denied | | ☐ Pass ☐ Fail |
| s3:DeleteObject | Denied | | ☐ Pass ☐ Fail |
| ec2:StartInstances | | | ☐ Pass ☐ Fail |
| ec2:TerminateInstances | | | ☐ Pass ☐ Fail |

**Screenshot 8: Policy Simulator**
![Policy Simulator](screenshots/08-policy-simulator.png)

---

### AWS CLI Testing

**Test 1: S3 List Bucket**
```bash
# Command:aws s3 ls s3://pragash-kumar-bucket/

# Output:
2026-07-08 14:58:33        195 ec2-trust-policy.json
2026-07-08 18:24:52         14 test.txt
PS C:\Users\praga\cloud-engineering\ce-lab-iam-policies-roles> 

# Result: x Success ☐ Access Denied
```

**Test 2: S3 Upload File**
```bash
# Command:aws s3 cp test.txt s3://pragash-kumar-bucket/ --profile alice-test

# Output:
upload failed: .\test.txt to s3://pragash-kumar-bucket/test.txt An error occurred (AccessDenied) when calling the PutObject operation: User: arn:aws:iam::033216807267:user/alice is not authorized to perform: s3:PutObject on resource: "arn:aws:s3:::pragash-kumar-bucket/test.txt" because no identity-based policy allows the s3:PutObject action

# Result: ☐ Success x Access Denied (Expected)
```

**Test 3: S3 Download File**
```bash
# Command:PS C:\Users\praga\cloud-engineering\ce-lab-iam-policies-roles> aws s3 cp s3://pragash-kumar-bucket/somefile.txt ./

# Output:
fatal error: An error occurred (404) when calling the HeadObject operation: Key "somefile.txt" does not exist
PS C:\Users\praga\cloud-engineering\ce-lab-iam-policies-roles>

# Result: ☐ Success x Access Denied
```

---

## Part 6: Least Privilege Implementation

### Custom Policy with Conditions

**Policy Name:**  pragash-S3-limit-bucket

**Condition Type Used:** x IP Address ☐ Time Window ☐ MFA ☐ Other: _______

**Policy JSON:**
```json
{{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:*"],
      "Resource": "arn:aws:s3:::pragash-kumar-bucket",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        }
      }
    }
  ]
}
```

**Rationale for this policy:**
```
This policy enforces the Principle of Least Privilege by restricting S3 bucket access to a specific IP address range (aws:SourceIp). This prevents unauthorized access from external networks, significantly reducing the security risk for this resource.
```

---

## Part 7: Troubleshooting

### Issue Encountered (if any)

**Issue Description:**
```
cmd was wrong, then i checked with AI then added arn
```

**Commands Used to Diagnose:**
```bash
aws iam simulate-principal-policy --policy-source-arn arn:aws:iam::033216807267:user/admin --action-names s3:GetObject --resource-arns arn:aws:s3:::pragash-kumar-bucket/testfile.txt

**Resolution:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

**Screenshot 9: Troubleshooting Output**
![Troubleshooting](screenshots/09-troubleshooting.png)

---

## Reflection Questions

### 1. Why are IAM roles preferred over access keys for EC2 instances?

**Your answer:**
```
IAM roles are preferred over access keys for EC2 instances for several key security and operational reasons
Improved Security_______________
Reduced Management Overhead_
Principle of Least Privilege_____________________________________Reduced Attack Surface
```

### 2. Explain the principle of least privilege and how you applied it in this lab.

**Your answer:**
```
The Principle of Least Privilege (PoLP) is a fundamental security concept that dictates that an entity—whether a user, a service, or an application—should be granted only the minimum level of access necessary to perform its intended function, and only for the duration required________________________________________
_____________________________________________________________
_____________________________________________________________
```

### 3. What is the difference between identity-based and resource-based policies?

**Your answer:**
```
The primary difference between identity-based and resource-based policies lies in where the policy is attached and what it controls
_____________________________________________________________
```

### 4. When would you use an explicit "Deny" in a policy?

**Your answer:**
```
In AWS, an explicit "Deny" is one of the most powerful tools in your security arsenal because it overrides any "Allow" permissions. Even if a user has broad administrative access, a single explicit "Deny" statement will block that action._____________________________________________
```

### 5. Describe a scenario where you'd use conditions in IAM policies.

**Your answer:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

---

## Summary of Resources Created

**IAM Policies:**
1.pragash-S3-limit-bucket  (ARN: arn:aws:iam::033216807267:policy/pragash-S3-limit-bucket)
2. ___________________________  (ARN: ___________________________)
3. ___________________________  (ARN: ___________________________)

**IAM Roles:**
1. EC2-S3-ReadOnly-Role  (ARN: arn:aws:iam::033216807267:role/EC2-S3-ReadOnly-Role)
2. ___________________________  (ARN: ___________________________)
3. ___________________________  (ARN: ___________________________)

**Users Modified:**
1. admin — This is the user you configured and used to perform the troubleshooting steps, including updating the trust policy and detaching/deleting policies.

---

## Cleanup Confirmation

- [ ] Detached all custom policies from users
- [ ] Deleted custom IAM policies
- [ ] Detached policies from roles
- [ ] Deleted test IAM roles
- [ ] Verified no resources remain

**Cleanup Commands:**
```bash
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

---

## Self-Assessment

**Rate your understanding (1-5):**

| Concept | Before Lab | After Lab | Improvement |
|---------|-----------|-----------|-------------|
| IAM Policy Structure | ___/5 | ___/5 | +___ |
| Custom Policy Creation | ___/5 | ___/5 | +___ |
| IAM Roles | ___/5 | ___/5 | +___ |
| Service Roles | ___/5 | ___/5 | +___ |
| Trust Relationships | ___/5 | ___/5 | +___ |
| Policy Testing | ___/5 | ___/5 | +___ |
| Least Privilege | ___/5 | ___/5 | +___ |
| Troubleshooting IAM | ___/5 | ___/5 | +___ |

---

## Instructor Verification

**Instructor Name:** ___________________________

**Date Reviewed:** ___________________________

**All policies validated:** ☐ Yes ☐ No

**Roles properly configured:** ☐ Yes ☐ No

**Comments:**
```
_____________________________________________________________
_____________________________________________________________
_____________________________________________________________
```

**Grade/Status:** ___________________________

---

**Lab Status:** x Complete ☐ Needs Revision

**Submission Date:** 08-07-26
