# Review Organizational User Policies

## Task Review Organizational User Policies

## Conditions

Given organization user policies and access to information security personnel and knowledge workers.

> **Operator Note:** Understanding policy is crucial to assessing procedural compliance and risk exposure during an incident.

## Standards

* Team member reviews all written policies regarding users of information systems.  
* Policies should address at minimum:
    * Acceptable Use Policy (AUP)
    * Account request process
    * Account creation, deletion, and suspension procedures
    * Access control methodology
    * Initial and periodic user training
    * Elevated privilege request and review process
    * Account audit process

* Team member interviews security personnel for technical implementation insight.  
* Team member interviews random users for awareness and compliance validation.


## Endstate  
All user policies have been reviewed and shortfalls annotated and briefed to the system owner.  


## Notes  
* Acceptable Use Policy  
Will be tailored to the organization and include items such as social media, PII and email monitoring. Policy will fall in line with local provider or enclave.  

* Account Requests:  
Account forms can be found in a central digital location that is easily accessible for personnel to retrieve. The account request form needs to include information fields for name (first, last), rank/title, organization, reason for request, location/group. Digital copy of completed request forms should be kept in a central database with access restricted to those who need it (account creators, database administrators) Information Assurance (IA) training certification is required as part of account request packet. IA training can be found at https://ia.signal.army.mil. A passing score of 70% is required to gain an account and is an annual requirement to keep the account.  

* Account suspension:  
After a lack of use or non-use for 45 days or an AUP violation, user accounts will be placed in suspension (disabled) status. If suspension is in response to an AUP violation, the account will not be placed back into active status until after IA training has been redone and the certification is received by the account managers and noted on the account. If the account in is suspended status due to lack of use, the user will need to speak with the account managers to get it reactivated.  

* Account deletion:  
Reasons for account deletion include lack of use, repeat violations of the Acceptable Use Policy and job termination. Lack of use or non-use limit for user accounts is set at 90 days. For account deletion due to violations, users are granted two suspensions before resulting in deletion. All user violations should be noted on their account with the incident date, incident, response results, and date of completion of IA training from the new certification.  

* Access control methodology:   
Access to groups and elevated privileges are treated on an individual basis. Requesters will need to fill out a request form that includes information fields for name, rank/title, organization, requested groups/elevated privileges/access level, why they are requesting it and have supervisor approval to be given access to additional groups and privileges. Periodic review will be conducted to determine continued user access to the groups and privileges.  

* Initial and periodic review:  
IA and Information Assurance Security Officer (IASO) training will need to renewed on an annual basis. Any additional certifications will also be reviewed on an annual basis as part of the personnel performance review to ensure any continued education that is needed is being addressed and tracked.

* Elevated privilege request/review:  
Elevated privileges will be addressed on an individual basis. The request will use the same form as the group membership request and follow the same process. Elevated privileges will be periodically reviewed for determination of continued usage. Privilege violations will result in one warning before removal. To get privileges returned will require review with the user’s supervisor to determine if privileges will be returned due to required job duties.  

* Account audit process:  
Accounts will be audited on a monthly schedule and compared against various variables to determine account usage, status and IA/IASO certification expiration.  


## References  
[NIST 800-53 Controls](https://nvd.nist.gov/download/800-53/800-53-controls.xml)  
[DISA IAWIP Policy References](http://iase.disa.mil/iawip/Pages/policyref.aspx)  


## Operator Recommendations and Additional Tools

### Operator Checklist

- [ ] Obtain and review latest organizational user policies.
- [ ] Interview system administrators and IA personnel.
- [ ] Interview representative users for policy awareness.
- [ ] Document gaps and recommendations.
- [ ] Brief findings to system owner.

### Tools by Platform

| Platform | Tool | Purpose |
|----------|------|---------|
| Universal | SharePoint / Policy repository | Locate user policy documents |
| Windows | PowerShell / Active Directory Tools | Validate user accounts and statuses |

### Best Practices

- Validate account suspension/deletion process during review.
- Focus on elevated privileges and their review processes.
- Provide clear recommendations for unaddressed security gaps.

## References

[NIST 800-53 Controls](https://nvd.nist.gov/download/800-53/800-53-controls.xml)  
[DISA IAWIP Policy References](http://iase.disa.mil/iawip/Pages/policyref.aspx)

---

## Revision History

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-05-02 | 1.8 | Full original + enriched operator-focused analysis procedures and checklists | Leo |
📦 FINAL VERSION → 2.19 Create Firewall Rule
