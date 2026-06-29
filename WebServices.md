# WebServices

## 1. What are Web Services?
- Web Services = custom business logic that is accessible by **External Systems**.
- They provide communication between different disparate systems built on different programming languages and platforms.
- Provide **platform-independent** communication between applications.

```
Salesforce  <----- WebServices ----->  Java
Application                            Application
```

- Using WebServices, we can expose **Data (Records), Business Logic, and Functionalities** to third-party systems.
- Using Apex, we can implement **2 types** of WebServices:
  1. REST-based WebServices
  2. SOAP-based WebServices

---

## 2. REST-Based WebServices / Apex REST

- **REST** = Representational State Transfer.
- Access resources over the internet using the **HTTP protocol**.
- REST is an **architectural style** built purely on HTTP; it uses HTTP internally for app-to-app communication.
- In REST WebServices, every Object / Business Logic / Class / Functionality is implemented as a **"Resource"**.
- Each Resource is identified by a **Resource Name** (developer-defined).

### Example: Table → Resource Name mapping
| Table Name | Resource Name (prefix) |
|---|---|
| Account | 001 |
| Contact | 003 |
| Opportunity | 006 |
| Case | 500 |
| Solution | 501 |
| Lead | 00Q |
| Campaign | 701 |
| Order | 801 |
| User | 005 |
| Task | 00T |
| Event | 00U |
| Group | 00G |

- Every resource is accessible via a **URI** (Uniform Resource Identifier):
  ```
  https://sample.com/<resourceName>
  ```
  e.g.
  ```
  https://ap16.salesforce.com/001              --> Account Object Records
  https://ap16.salesforce.com/500              --> Case Object Records
  https://ap16.salesforce.com/apex/ShowAllAccountsPage  --> Visualforce Page
  ```

### HTTP Methods → Operation Mapping
| Method | Operation |
|---|---|
| GET | Retrieve records from a Resource/Object |
| POST | Insert records into a Resource/Object |
| PUT | Update records in a Resource |
| PATCH | Both INSERT + UPDATE on the resource |
| DELETE | Delete records from the object/resource |

> REST-based WebServices can return results as **XML / JSON / TEXT**.

### REST Annotations
1. `@RestResource()`
2. `@HttpGet()`
3. `@HttpPost()`
4. `@HttpPut()`
5. `@HttpPatch()`
6. `@HttpDelete()`

---

## 3. `@RestResource` Annotation
- Exposes an Apex class/business-logic class to third-party applications.
- The class itself holds the business logic in various methods.
- **Rule:** WebService class must be `Global`. Resource name is given via `@RestResource()` on top of the class.

**Syntax:**
```apex
@RestResource(URLMapping='/<ResourceName>/*')
Global Class <ClassName>
{
    // Business Logic
}
```

**Examples:**
```apex
@RestResource(URLMapping='/AccountsService/*')
Global Class AccountsManager
{
    // Business Logic
}

@RestResource(URLMapping='/CommonService/*')
Global Class CommonHelper
{
    // Business Logic
}
```

### Accessing the Resource
```
https://<ServerNumber>.Salesforce.com/services/apexrest/<ResourceName>
```
Examples:
```
https://ap16.salesforce.com/services/apexrest/CommonService
https://ap16.salesforce.com/services/apexrest/AccountsService
```

### Tools to Invoke / Test REST WebServices
1. Workbench Tool
2. Postman Tool
3. HP QTP Tool
4. Through Apex Programming
5. Third-party applications
6. Integration tools (Tibco, WebMethods, SeeBeyond, BizTalk, MuleSoft, etc.)

---

## 4. `@HttpGet` Method
- Gets the required resource info from Provider → Consumer.
- Applicable only at **method level**.
- WebService methods must be `Global` and `Static`.
- **Only one `@HttpGet` method allowed per class** (can't repeat same annotation in one class).

**Syntax:**
```apex
@HttpGet
Global static <ReturnType> <MethodName>()
{
    // Business Logic
}
```

**Example:**
```apex
@RestResource(URLMapping='/AccountRecordsService/*')
Global Class AccountsManager
{
    @HttpGet
    Global Static List<Account> GetAllAccounts()
    {
        // Business Logic
    }
}
```

### UseCase: Get all Account records
```apex
@RestResource(URLMapping='/AccountsService/*')
Global class AccountsManagerService 
{
    @HttpGet
    Global static List<Account> GetAllAccountRecords()
    {
        List<Account> lstAccounts = [Select id, name, rating, industry, annualrevenue,
                                        phone, fax, type, active__C
                                        from Account
                                        Order by name];
        return lstAccounts;
    }
}
```
**Testing (Workbench — https://Workbench.Developerforce.com):**
- Resource URL: `/services/apexrest/AccountsService`
- Method: GET

### UseCase: Get all New Cases
```apex
@RestResource(URLMapping='/CaseService/*')
Global class CaseManagerService 
{
    @HttpGet
    Global static List<Case> GetAllNewCases()
    {
        List<Case> lstCases = [Select id, caseNumber, status, type, reason, origin, 
                                    priority, subject
                                from Case
                                Where Status = 'New'
                                order by caseNumber];        
        return lstCases;
    }
}
```
**Testing:** Resource URL `/services/apexrest/CaseService`, Method: GET

### Assignments (HttpGet)
1. Create a WebService to expose all Hiring Manager Records to third-party apps.
2. Create a WebService to access all Lead Records whose AnnualRevenue > 2,00,000.
3. Create a WebService to access all Position Records whose Status is "In Progress".

---

## 5. `RestContext` Class
- Establishes a secured communication channel between Provider and Consumer, so the Consumer can pass required input parameters and get a response back.

### Inner classes:
1. **RestRequest** — used by Consumer to pass input parameters to the WebService; stored as **Key-Value pairs** (Key = Param Name, Value = Param Value).

   Example:
   | Key | Value |
   |---|---|
   | Id | '001464564564' |
   | Name | 'Test' |
   | Rating | 'Education' |
   | AnnualRevenue | 10000 |

   **Property `Params`** — holds all input params supplied by Consumer, as a Map.
   ```apex
   Map<String,String> <objectName> = RestContext.Request.Params;
   // Ex:
   Map<String, String> inputParams = RestContext.Request.Params;
   ```

2. **RestResponse** — used to collect the response sent back from the WebService class to the third-party app.

### UseCase: Get Lead by ID
```apex
@RestResource(URLMapping='/LeadRecordsService/*')
Global class LeadManagerService 
{
    @HttpGet
    Global static Lead GetLeadRecordByID()
    {
        Map<String,String> inputParams = RestContext.request.Params;
        Lead leadRecord = [Select id, firstname, lastname, email, company, phone, title,
                                Status, fax, rating, industry, annualrevenue
                            from Lead
                            Where id =: inputParams.get('Id')];
        return leadRecord; 
    }
}
```
**Testing:** `/services/apexrest/LeadRecordsService/?Id=00Q2v00001dln9s` — Method: GET

### UseCase: Get Cases by Priority
```apex
@RestResource(URLMapping='/CaseManagerService/*')
Global class CaseRecordsService 
{
    @HttpGet
    Global Static List<Case> GetCasesByPriority()
    {
        Map<String, String> inputParams = RestContext.request.Params;
        List<Case> lstCaseRecords = [Select id, caseNumber, Status, Priority, Type, Origin, 
                                            Reason, Subject
                                        from Case
                                        Where Priority =: inputParams.get('priority')];
        return lstCaseRecords;
    }
}
```
**Testing:** `/services/apexrest/CaseManagerService/?priority=Medium` — Method: GET

### Assignments (RestContext / GET with params)
1. Get required Account Records based on specified Industry Name.
2. Get all related Contact Records based on specified Account Name.
3. Get all Position Records based on specified Location Name.
4. Get all related Candidates for the specified Position Name.

---

## 6. `@HttpDelete` Method
- Removes one or more records from objects via the WebService.
- Fetch records to remove based on one or more conditions.

**Syntax:**
```apex
@HttpDelete
Global static <ReturnType> <MethodName>()
{
    // Business Logic to remove records
}
```

### UseCase: Delete Hiring Manager by Id
```apex
@RestResource(URLMapping='/HRService/*')
Global class HiringManagerService 
{
    @HttpDelete
    Global static string DeleteHRRecord()
    {
        Map<string, string> inputParams = RestContext.request.Params;        
        string hrRecordId = inputParams.Get('Id');
        
        Delete [Select id, Name from Hiring_Manager__c
                Where id =: hrRecordID];     
        
        return 'Record Deleted';
    }
}
```
**Testing:** `/services/apexrest/HRService/?Id=a032v00006NYCg0` — Method: DELETE

### UseCase: Delete related Contacts by Account Name
```apex
@RestResource(URLMapping='/ContactDeleteService/*')
Global class ContactsDeletionService 
{
    @HttpDelete
    Global static string RemoveRelatedContacts()
    {
        Map<String,String> inputParams = RestContext.request.Params;
        List<Contact> lstContacts = [Select id, firstname, lastname, Account.Name
                                        from Contact
                                        Where account.name =: inputParams.Get('Name')];
        Delete lstContacts;
        return 'Contacts Deleted';
    }
}
```
**Testing:** `/services/apexrest/ContactDeleteService/?Name=Edge+Communications` — Method: DELETE

> **Note:** Records deleted via `@HttpDelete` remain in the Recycle Bin for **15 days**.

---

## 7. `@HttpPost` Method
- Inserts one or more records into a Salesforce object via WebService.
- Field values passed as **`{}` JSON format** in the **Request Body**.

**Syntax:**
```apex
@HttpPost
Global Static <ReturnType> <MethodName>(<Parameters>)
{
    // Business Logic to insert records
}
```

### UseCase: Insert a single Account
```apex
@RestResource(URLMapping='/NewAccountService/*')
Global class NewAccountsManagerService 
{
    @HttpPost
    Global Static string InsertNewAccount(string accName, string accRating, string accIndustry,
                                            Decimal accRevenue, string accPhone, string accFax,
                                            string accWebSite, string accType, string accOwnership,
                                            string accActive, string accPriority, string accPan)
    {
        Account acc = new Account();
        acc.Name = accName;
        acc.Rating = accRating;
        acc.Industry = accIndustry;
        acc.AnnualRevenue = accRevenue;
        acc.Phone = accPhone;
        acc.Fax = accFax;
        acc.Type = accType;
        acc.Ownership = accOwnership;
        acc.Website = accWebSite;
        acc.Active__c = accActive;
        acc.CustomerPriority__c = accPriority;
        acc.PAN_Number__c = accPan;
        
        insert acc;
        
        if(acc.Id != null)
            return 'Account Inserted.';
        else
            return 'Something Went Wrong in Account Data.';
    }
}
```
**Testing:**
- Endpoint: `/services/apexrest/NewAccountService`
- Method: POST
- Request Body:
```json
{
    "accName":"Santhosh Verma",
    "accRating":"Hot",
    "accIndustry":"Technology",
    "accPhone":"9900998877",
    "accFax":"9977665566",
    "accType":"Prospect",
    "accOwnership":"Private",
    "accWebSite":"www.salesforce.com",
    "accPan":"ALPHJ8989E",
    "accPriority":"High",
    "accActive":"Yes",
    "accRevenue":4700000
}
```

### UseCase: Insert Multiple Hiring Manager Records (bulk)
```apex
@RestResource(URLMapping='/BulkHRRecordsService/*')
Global class BulkHiringManagerRecordsService 
{
    @HttpPost
    Global static string InsertBulkHRRecords()
    {
        string hrRecordsCollection = RestContext.request.requestBody.ToString();
        
        List<Hiring_Manager__C> lstHRRecords = (List<Hiring_Manager__C>) System.JSON.deserialize(hrRecordsCollection, List<Hiring_Manager__C>.Class);
        
        if(! lstHRRecords.isEmpty())
        {
            insert lstHRRecords;
            return 'SUCCESS';
        }
        else
            return 'FAILED';
    }
}
```
**Testing:**
- Endpoint: `/services/apexrest/BulkHRRecordsService`
- Method: POST
- Request Body:
```json
[
    {"Name":"Service 1 HR","Location__C":"Bangalore","Contact_Number__C":"9900998877","Email_id__C":"service1hr@gmail.com"},
    {"Name":"Service 2 HR","Location__C":"Hyderabad","Contact_Number__C":"9900990099","Email_id__C":"service2hr@gmail.com"},
    {"Name":"Service 3 HR","Location__C":"Chennai","Contact_Number__C":"7788998877","Email_id__C":"service3hr@gmail.com"},
    {"Name":"Service 4 HR","Location__C":"Bangalore","Contact_Number__C":"8899884466","Email_id__C":"service4hr@gmail.com"}
]
```

### Assignments (HttpPost)
1. Insert a Hiring Manager Record.
2. Insert a Lead Record with "Hot" Rating and "Finance" Industry.
3. Insert a New Case Record with "High Priority" and "Working" Status.
4. Insert 2 Account Records.
5. Insert 5 Opportunity Records.

---

## 8. `@HttpPut` Method
- Performs **Update** operations on object records — one or more.
- Must supply the record Id and the new field values to update.

**Syntax:**
```apex
@HttpPut
Global Static <ReturnType> <MethodName>(<Parameters>)
{
    // Business Logic to Update Records
}
```

### UseCase: Update Case Status/Priority by Case Number
```apex
@RestResource(URLMapping='/CaseRecordManagerService/*')
Global class CaseRecordsManagerService 
{
    @HttpPut
    Global static String UpdateCaseRecord(string cNumber, string cPriority, string cStatus)
    {
        if(cNumber != null && cNumber != '')
        {
            Case cRecord = [Select id, caseNumber, priority, Status, origin, reason
                                from Case
                                Where caseNumber =: cNumber];   
            
            if(cRecord.id != null)
            {
                cRecord.Status = cStatus;
                cRecord.Priority = cPriority;
                update cRecord;
                return 'Updated Successfully.';
            }
            else
            {
                return 'Case Record Not Found / Invalid Case Number.';
            }
        }
        else
            return 'Please provide the Case Number.';
    }
}
```
**Testing:**
- Endpoint: `/services/apexrest/CaseRecordManagerService`
- Method: PUT
- Request Body:
```json
{
    "cNumber":"00001003",
    "cPriority":"High",
    "cStatus":"Working"
}
```

### UseCase: Update Rating/AnnualRevenue by Industry (using Switch)
Mapping table:
| Industry Name | Rating | AnnualRevenue |
|---|---|---|
| Banking | Hot | 79,00,000 |
| Finance | Warm | 57,00,000 |
| Insurance | Hot | 84,00,000 |
| Education | Cold | 41,00,000 |
| Consulting | Warm | 50,00,000 |

```apex
@RestResource(URLMapping='/AccountsUpdateService/*')
Global class AccountRecordsManagerService 
{
    @HttpPut
    Global static string UpdateAccountRecords(string accIndustry)
    {
        if(accIndustry != null && accIndustry != '')
        {
            List<Account> lstAccounts = [Select id, name, industry, rating, annualrevenue
                                            from Account 
                                            Where industry =: accIndustry];
            
            if(! lstAccounts.isEmpty())
            {
                for(Account acc : lstAccounts)
                {
                    Switch ON acc.Industry
                    {
                        When 'Banking'   
                        {
                            acc.Rating = 'Hot';
                            acc.Annualrevenue = 7900000;
                        }
                        When 'Finance'
                        {
                            acc.Rating = 'Warm';
                            acc.Annualrevenue = 5700000;
                        }
                        When 'Insurance'
                        {
                            acc.Rating = 'Hot';
                            acc.Annualrevenue = 8400000;
                        }
                        When 'Education'
                        {
                            acc.Rating = 'Cold';
                            acc.Annualrevenue = 4100000;
                        }
                        When 'Consulting'
                        {
                            acc.Rating = 'Warm';
                            acc.Annualrevenue = 5000000;
                        }
                    }
                }
                Update lstAccounts;
                return 'Records Updated.';
            }
            else
                return 'No Matching Records Found.';
        }
        else
        {
            return 'Please provide the Industry Name.';
        }
    }
}
```
**Testing:**
- Endpoint: `/services/apexrest/AccountsUpdateService`
- Method: PUT
- Request Body: `{"accIndustry":"Education"}`

---

## 9. `@HttpPatch` Method
- Performs combined **Insert + Update** (upsert-style) operations.

**Syntax:**
```apex
@HttpPatch
Global Static <ReturnType> <MethodName>(<Parameters>)
{
    // Business Logic
}
```

### UseCase: Update HR Contact Number by City + Insert a New HR Record
```apex
@RestResource(URLMapping='/HiringManagerPatchService/*')
Global class HiringManagerUpsertService 
{
    @HttpPatch
    Global Static string UpsertHiringManagerRecords(string hrCityName)
    {
        if(hrCityName != null && hrCityName != '')
        {
            List<Hiring_Manager__C> lstHRRecords = [Select id, name, Location__C, contact_Number__C
                                                        from Hiring_Manager__C
                                                        Where Location__C =: hrCityName];
            
            if(! lstHRRecords.isEmpty())
            {
                List<Hiring_Manager__C> hrRecordsToUpsert = new List<Hiring_Manager__C>();
                
                // Update existing
                for(Hiring_Manager__C hr : lstHRRecords) 
                {
                    hr.Contact_Number__C = '9900008877';
                    hrRecordsToUpsert.Add(hr);
                }
                // Create new HR record
                Hiring_Manager__C hr = new Hiring_Manager__C();
                hr.Name = 'WebService Test HR';
                hr.Location__c = 'Chennai';
                hr.Contact_Number__c = '9999999999';
                hr.Email_ID__c = 'webservicetest@gmail.com';
                
                hrRecordsToUpsert.Add(hr);
                
                Upsert hrRecordsToUpsert;
                
                return 'Records Upserted';
            }
            else
            {
                return 'No Matching Records Found.';
            }
        }
        else
            return 'Please provide the City Name.';
    }
}
```
**Testing:**
- Endpoint: `/services/apexrest/HiringManagerPatchService`
- Method: PATCH
- Request Body: `{"hrCityName":"Hyderabad"}`

---

## 10. OAuth Authentication & Future Callouts (mentioned as upcoming topics)
- File ends pointing toward: **OAuth Authentication** and **Future Callouts** (covered in detail in the OAuth/Future Callouts notes).

---

## Quick Reference Table — REST Annotations
| Annotation | HTTP Verb | Use |
|---|---|---|
| `@HttpGet` | GET | Retrieve (1 per class) |
| `@HttpPost` | POST | Insert |
| `@HttpPut` | PUT | Update |
| `@HttpPatch` | PATCH | Insert + Update |
| `@HttpDelete` | DELETE | Delete (15-day Recycle Bin retention) |