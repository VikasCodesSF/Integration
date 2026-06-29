# JSON 

## 1. What is JSON?
- **JSON** → JavaScript Object Notation.
- Most commonly used message format for applications, **irrespective of language** — any programming language can understand JSON, making it ideal for **interoperability**.

```
Salesforce   ---> Request (JSON)  ---> Java
Application  <--- Response (JSON) --- Application
```

- Lightweight, text-based **open standard** for data interchange.
- Originally specified by **Douglas Crockford**.
- Designed for **human-readable** data interchange.
- Extended from the JavaScript scripting language.
- File extension: `.json`
- Internet Media Type: `application/json`

### Uses of JSON
1. Serializing and transmitting structured data over a network connection.
2. Primarily used to transmit data between a server and web applications.
3. Web services and APIs use JSON to expose public data.
4. Usable with modern programming languages.

### Characteristics of JSON
1. Easy to read and write.
2. Lightweight, text-based interchange format.
3. Language-independent.

### JSON Syntax Rules (subset of JavaScript syntax)
1. Data is represented in **Key-Value pairs**.
2. Curly braces `{}` hold objects; each name followed by `:`; pairs separated by `,`.
3. Square brackets `[]` hold arrays; values separated by `,`.

```json
{"Key":"Value"}
```
Example: `{"Name":"Ramesh"}`

### Supported Data Structures
1. **Collection of Key-Value pairs** — supported across programming languages.
2. **Ordered list of values** — arrays, lists, etc.

---

## 2. JSON Data Types

### Number
- Stores numerical values (integer + decimal parts).
- **No need to enclose in double quotes.**
```json
{"Key":<Value>}
```
```json
{"CustomerCode":1001}
{"AnnualRevenue":2500000}
{"ServiceTax":2500.50}
{"ProductWeight":45.750}
```

### String
- Stores a collection of characters including special characters.
```json
{"Key":"Value"}
```
```json
{"CustomerName":"Ramesh Kumar"}
{"Location":"Mumbai"}
{"Rating":"Hot"}
{"Industry":"Banking"}
{"EmailID":"sureshkumar@gmail.com"}
```

### Boolean
- Stores TRUE/FALSE.
```json
{"Key":<Value>}
```
```json
{"ActiveStatus":true}
{"IsInStock":true}
{"IsBlocked":false}
{"CanDelete":false}
```

### Array
- Stores a collection of **homogeneous** elements.
- Enclosed in `[]`, elements separated by `,`.
- Each element indexed starting from **Zero**.
```json
{
    "Key":[<Value1>,<Value2>,<Value3>,....,<ValueN>]
}
```
**UseCase — Customer Codes array:**
```json
{
    "CustomerCodes":[1001,1002,1004,1007,1009,1211]
}
```
**UseCase — Country Names array:**
```json
{
    "CountryNames":['USA','India','Australia','Japan','Middle East','China','Germany']
}
```

### Object
- Stores a collection of **heterogeneous** elements.
- Enclosed in `{}` (braces — note: source text says "Square Braces" but the syntax/examples shown use curly braces, which is the correct JSON object syntax).
- "N" number of elements separated by comma.
1. Each object is a Key-Value pair.
2. Key must be a String type.
3. Value can be an Object / Array of Objects.

```json
{
    "KeyName": {
        "Key":"Value",
        "Key":"Value",
        ...
        "Key":"Value"
    }
}
```
**Example:**
```json
{
    "CustomerRecord":{
        "CustomerCode":1001,
        "CustomerName":"Srinivas",
        "Address":"Hyderabad",
        "Salary":2000000,
        "PhoneNumber":"9900998877",
        "IsActive":true
    }
}
```

---

## 3. JSON UseCases (Static Examples)

**Product Info (single object):**
```json
{
    "ProductInfo":{
        "ProductCode":450067,
        "ProductName":"Laptop",
        "Manufacturer":"DELL Inc.",
        "UnitPrice":45000,
        "IsInStock":true
    }
}
```

**Multiple Products Info (array of objects):**
```json
{
    "ProductsInfo": [
        {
            "ProductCode":23004500,
            "ProductName":"Mobile",
            "UnitPrice":15000,
            "Manufacturer":"Samsung Inc.",
            "IsInStock":true
        },
        {
            "ProductCode":23009900,
            "ProductName":"Laptop",
            "UnitPrice":45000,
            "Manufacturer":"Dell Inc.",
            "IsInStock":true
        }
    ]       
}
```

**Account Record** (fields: Name, Rating, Industry, AnnualRevenue, Phone, Fax, Type, Ownership, Active__C):
```json
{
    "accountRecord":{
        "Name":"Srinivas Kumar",
        "Rating":"Hot",
        "Industry":"Banking",
        "AnnualRevenue":2000000,
        "Phone":"9900998877",
        "Fax":"9900778855",
        "Type":"Customer - Direct",
        "Ownership":"Public",
        "Active__C":"Yes"
    }
}
```

**Lead Record** (fields: FirstName, LastName, Company, Title, Phone, Status, Rating, Industry, AnnualRevenue, City):
```json
{
    "leadRecord":{
        "FirstName":"Praveen",
        "LastName":"Kumar",
        "Company":"Infsys Inc.",
        "Title":"Project Manager",
        "Phone":"9900998877",
        "Status":"Open - Not Contacted",
        "Rating":"Hot",
        "Industry":"Manufacturing",
        "AnnualRevenue":3500000,
        "City":"Hyderabad"
    }
}
```
> Note: source text had `"City":Hyderabad` (missing quotes) — corrected above; city values must be quoted strings.

**Hiring Manager Record:**
```json
{
    "hrRecord":{
        "Name":"Pradeep Kumar",
        "Location__C":"Hyderabad",
        "Contact_Number__C":"9900556644",
        "Email_id__C":"pradeep@gmail.com"
    }
}
```

**3 Opportunity Records** (fields: Name, Amount, StageName, LeadSource, Probability):
```json
{
    "OpportunityRecords":[
        {"Name":"Opportunity1","Amount":25000,"StageName":"Prospecting","LeadSource":"Web","Probability":10},
        {"Name":"Opportunity2","Amount":15000,"StageName":"Closed - Won","LeadSource":"Phone","Probability":100},
        {"Name":"Opportunity3","Amount":75000,"StageName":"Negotiation & Review","LeadSource":"Web","Probability":40}
    ]
}
```

**Apex Class → JSON mapping (CustomerInfo):**
```apex
Public Class CustomerInfo
{
    Public integer customerCode, customerAge;
    Public string customerName, emailID, address;
    Public Decimal balanceAmount, loanAmount;
    Public Boolean isActive;
}
```
```json
{
    "customerInfo": {
        "customerCode":1004500,
        "customerAge":32,
        "customerName":"Raghu Prasad",
        "emailID":"raghu@gmail.com",
        "address":"#413, RK Apartments, Sri Nagar Colony, Hyderabad",
        "balanceAmount":29000,
        "loanAmount":200000,
        "isActive":true
    }
}
```

**Apex Class → JSON mapping (ProductDetails):**
```apex
Public Class ProductDetails
{
    Public integer productCode;
    Public string productName, manufacturerName, location;
    Public Decimal unitPrice;
    Public Date manufacturingDate, expiryDate;
    Public Boolean isInStock;
}
```
```json
{
    "ProductInfo":{
        "productCode":450060,
        "productName":"Mobile Device",
        "manufacturerName":"Samsung Inc.",
        "location":"India",
        "unitPrice":25000,
        "manufacturingDate":"05/01/2020",
        "expiryDate":"05/01/2025",
        "isInStock":true
    }
}
```

**Wrapper Class (CaseHelper):**
```apex
Public Class CaseHelper
{
    Public Case caseRecord;
    Public Boolean IsInProgess;
}
```
```json
{
    "caseHelper":{
        "IsInProgess":true,
        "caseRecord":{
            "caseNumber":00001006,
            "Status":"New",
            "Priority":"High",
            "Type":"Mechanical",
            "Reason":"Performance",
            "Origin":"Phone",
            "Subject":"My Mobile is not working properly",
            "Description":"Dear Customer support, my recently purchased Mobile is unable to access the Internet. Please have a look into the issue."
        }
    }
}
```

---

## 4. `JSONGenerator` Class (Serialization)
- Used to implement **Serialization** programmatically — converts Apex Object Types into String (JSON) format.

**Create the object:**
```apex
System.JSONGenerator <objectName> = System.JSON.CreateGenerator(true);
// Ex:
System.JSONGenerator jsonGen = System.JSON.CreateGenerator(true);
```

### Methods
| Method | Purpose |
|---|---|
| `WriteStartObject()` | Write starting `{` |
| `WriteEndObject()` | Write ending `}` |
| `WriteStartArray()` | Write starting `[` |
| `WriteEndArray()` | Write ending `]` |
| `WriteFieldName('FieldName')` | Create a new element (field name) |
| `WriteNumber(Number)` | Write numerical value |
| `WriteString(StringValue)` | Write string value |
| `WriteBoolean(true/false)` | Write boolean value |
| `WriteNull()` | Write null value |
| `WriteDate(DateValue)` | Write date value |
| `WriteStringField(Key, Value)` | Add a string element |
| `WriteNumberField(Key, NumericValue)` | Add a numeric element |
| `WriteBooleanField(Key, Value)` | Add a boolean element |
| `WriteDateField(Key, DateValue)` | Add a date element |
| `WriteDateTimeField(Key, DateTimeValue)` | Add a datetime element |

### UseCase: Generate Lead JSON
```apex
public class JSONGeneratorHelper 
{
    Public static void CreateJSONContent()
    {
        System.JSONGenerator leadGen = System.JSON.createGenerator(true);
        
        leadGen.writeStartObject();  
        
            leadGen.writeStringField('FirstName','Ram');
            leadGen.writeStringField('LastName','Kumar');
            leadGen.writeStringField('Company','IBM Inc.');
            leadGen.writeStringField('Title','Sales Manager');
            leadGen.writeStringField('Rating','Hot');
            leadGen.writeStringField('Industry','Manufacturing');
            leadGen.writeStringField('Phone','9900998877');
            leadGen.writeStringField('Fax','990077899');
            leadGen.writeNumberField('AnnualRevenue',2000000);
            leadGen.writeStringField('City','Hyderabad');
            leadGen.writeDateField('BirthDate',Date.newInstance(1980,05,21));
        
        leadGen.writeEndObject(); 
        
        system.debug('JSON Content is..: '+ leadGen.getAsString());
    }
}
```
**Execution:** `JSONGeneratorHelper.CreateJSONContent();`

### UseCase: Generate JSON for ALL Account Records (array + nulls handled)
```apex
public class JSONGeneratorHelper 
{
    Public Static void AccountRecordsJSONGenerator()
    {
        List<Account> lstAccounts = [Select id, name, rating, industry, annualrevenue, type, 
                                            Phone, fax, active__C 
                                      from Account
                                      order by name
                                      Limit 5];
        
        System.JSONGenerator accGen = system.JSON.createGenerator(true);
        
        accGen.writeStartObject();
        
          accGen.writeFieldName('accountRecords');
          accGen.writeStartArray();
        
            if(! lstAccounts.isEmpty())
            {
                for(Account acc : lstAccounts)
                {
                    accGen.writeStartObject();
                      accGen.writeIdField('ID', acc.id);
                      accGen.writeStringField('Name',acc.Name);                          
                   
                      if(acc.Rating == null || acc.Rating == '')
                            accGen.writeNullField('Rating');
                      else
                            accGen.writeStringField('Rating',acc.Rating);
                      
                      if(acc.Industry == null || acc.Industry == '')
                            accGen.writeNullField('Industry');
                      else
                            accGen.writeStringField('Industry',acc.Industry);
                      
                      if(acc.AnnualRevenue == null)
                            accGen.writeNullField('AnnualRevenue');
                      else
                            accGen.writeNumberField('AnnualRevenue',acc.AnnualRevenue);
                      
                      if(Acc.Type == null || acc.Type == '')
                            accGen.writeNullField('Type');
                      else
                           accGen.writeStringField('Type', acc.Type);
                      
                      if(acc.Phone == null || acc.Phone == '')
                            accGen.writeNullField('Phone');
                      else
                            accGen.writeStringField('Phone',acc.Phone);
                      
                      if(acc.Fax == null || acc.Fax == '')
                             accGen.writeNullField('Fax');
                      else
                        accGen.writeStringField('Fax',acc.Fax);
                      
                      if(acc.Active__c == null || acc.Active__c == '')
                            accGen.writeNullField('Active__C');
                      else
                        accGen.writeStringField('Active__C',acc.Active__c);
                      
                    accGen.writeEndObject();                        
                }
            }
        
          accGen.writeEndArray();
        accGen.writeEndObject();
        
        system.debug('JSON Content is...: '+ accGen.getAsString());
    }
}
```
**Execution:** `JSONGeneratorHelper.AccountRecordsJSONGenerator();`

---

## 5. `JSONParser` Class (Deserialization)
- Achieves **Deserialization** — converts a JSON String into our own Apex Object Types.
- When invoking/consuming third-party WebServices, the result usually comes back as JSON. We convert it via `JSONParser` so it can be:
  - Represented on a VF page,
  - Stored in the database, or
  - Used as input for another WebService call.
- Internally, the JSON string is split into smaller pieces called **Tokens**, iterated start-to-end, and parsed into our object types.

**Create the object:**
```apex
System.JSONParser <objectName> = System.JSON.CreateParser(<JSONString>);
```

### Methods
| Method | Purpose |
|---|---|
| `NextToken()` | Move cursor to the next token |
| `GetCurrentToken()` | Get the current token value |
| `GetText()` | Get current token value as String |
| `GetBooleanValue()` | Get Boolean value of current token |
| `GetDecimalValue()` | Get Decimal value of current token |
| `GetDateTimeValue()` | Get DateTime value of current token |

### `JSONToken` Enumeration (token type identification)
1. `FIELD_NAME`
2. `START_OBJECT`
3. `END_OBJECT`
4. `START_ARRAY`
5. `END_ARRAY`
6. `VALUE_NULL`
7. `VALUE_STRING`
8. `VALUE_TRUE`
9. `VALUE_FALSE`
10. `VALUE_NUMBER_INT`
11. `VALUE_NUMBER_FLOAT`

### UseCase: Parse a Product JSON string manually
```apex
public class JSONParserUtility 
{
    Public static void ParseJSONContent()
    {
        string productJSON = '{"productCode":40056,"productName":"Laptop","Manufacturer":"Lenovo Inc.","UnitPrice":42000,"Location":"Korea"}';
        
        System.JSONParser pParser = System.JSON.createParser(productJSON);
        
        While(pParser.nextToken() != null)
        {
            if(pParser.getCurrentToken() == JSONToken.FIELD_NAME && pParser.getText() == 'productCode' )
            {
                pParser.nextToken();
                system.debug('Product Code is...: '+ pPArser.getIntegerValue());                
            }
            if(pParser.getCurrentToken() == JSONToken.FIELD_NAME && pParser.getText() == 'productName' )
            {
                pParser.nextToken();
                system.debug('Product Name is...: '+ pParser.getText());
            }
            if(pParser.getCurrentToken() == JSONToken.FIELD_NAME && pParser.getText() == 'Manufacturer')
            {
                pParser.nextToken();
                system.debug('Manufacturer Name is....: '+ pPArser.getText());
            }
            if(pParser.getCurrentToken() == JSONToken.FIELD_NAME && pParser.getText() == 'UnitPrice')
            {
                pParser.nextToken();
                system.debug('Unit Price Value is...: '+ pPArser.getDecimalValue());
            }
            if(pParser.getCurrentToken() == JSONToken.FIELD_NAME && pPArser.getText() == 'Location')
            {
                pParser.nextToken();
                system.debug('Location Value is...: '+ pParser.getText());
            }
        }
    }
}
```
**Execution:** `JSONParserUtility.ParseJSONContent();`

### Assignment (Practice)
Parse the below JSON and print each value to the Debug Log:
```json
{
    "Name":"Sanjeev Reddy Nagar",
    "Description":null,
    "BranchType":"Sub Post Office",
    "DeliveryStatus":"Delivery",
    "Circle":"Andhra Pradesh",
    "District":"Hyderabad",
    "Division":"Hyderabad City",
    "Region":"Hyderabad City",
    "Block":"Ameerpet",
    "State":"Telangana",
    "Country":"India",
    "Pincode":"500038"
}
```

### UseCase: Deserialize JSON directly into an `Account` (shortcut method)
```apex
public class JSONParserUtility 
{
    Public static void ParseAccountJSON()
    {
        string accountJSON = '{"Name":"Raghav Sharma","Rating":"Hot","Industry":"Banking","Type":"Prospect","Ownership":"Public","AnnualRevenue":4200000,"Phone":"8899887766","Fax":"9900990099","customerpriority__C":"High","Active__C":"Yes","Website":"www.salesforce.com"}';
        
        Account acc = (Account) System.JSON.deserialize(accountJSON, Account.Class);
        
        insert acc;
        if(acc.id != null)
        {
            system.debug('Account Record ID is...: '+ acc.id);
        }
        
        /* 
        // Alternative manual approach (commented out in source) — same result via JSONParser + manual field-by-field token reading instead of System.JSON.deserialize().
        */
    }
}
```
**Execution:** `JSONParserUtility.ParseAccountJSON();`

> Key takeaway: `System.JSON.deserialize(jsonString, Type.Class)` is the **simpler shortcut** — avoids writing a manual token-by-token `JSONParser` loop, which is shown commented out in the source as the "General Process".

### UseCase: Deserialize Bulk Opportunity Records & Insert
```apex
public class JSONParserUtility 
{
    Public static void ParserBulkOpportunitiesJSON()
    {
        string opportunityJSON = '[{"Name":"JSONOppostunity1","Amount":30000,"StageName":"Prospecting","LeadSource":"Web","CloseDate":"2020-01-20"},{"Name":"JSONOppostunity2","Amount":40000,"StageName":"Prospecting","LeadSource":"Web","CloseDate":"2020-02-24"},{"Name":"JSONOppostunity3","Amount":50000,"StageName":"Prospecting","LeadSource":"Web","CloseDate":"2020-04-10"}]';  
        
        System.JSONParser opParser = System.JSON.createParser(opportunityJSON);
            
        List<Opportunity> lstOpportunities = (List<Opportunity>) System.JSON.deserialize(opportunityJSON, List<Opportunity>.Class);
        
        insert lstOpportunities;
    }
}
```
**Execution:** `JSONParserUtility.ParserBulkOpportunitiesJSON();`

### UseCase: Deserialize Postal Branch Details JSON into a Custom Wrapper List
```apex
public class PostalBranchDetails 
{
    Public string Name;
    Public string Description;
    Public string BranchType;
    Public string DeliveryStatus;
    Public string Circle;
    Public string District;
    Public string Division;
    Public string Region;
    Public string Block;
    Public string State;
    Public string Country;
    Public string Pincode;
}
```
```apex
public class JSONParserUtility 
{
    Public static void ParserPostalDetailsJSONResponse()
    {
        string branchDetailsJSON ='[{"Name":"Gandhi Bhawan (Hyderabad)","Description":null,"BranchType":"Sub Post Office","DeliveryStatus":"Non-Delivery","Circle":"Andhra Pradesh","District":"Hyderabad","Division":"Hyderabad City","Region":"Hyderabad City","Block":"Nampally","State":"Telangana","Country":"India","Pincode":"500001"}, ... ]';
        
        List<PostalBranchDetails> lstBranchResults = (List<PostalBranchDetails>) System.JSON.deserialize(branchDetailsJSON, List<PostalBranchDetails>.Class);
        system.debug('Branch Details....: '+ lstBRanchResults);
    }
}
```
**Execution:** `JSONParserUtility.ParserPostalDetailsJSONResponse();`

### UseCase: Display Deserialized Postal Branch Details on a VF Page
```apex
public class PostalBranchDetails 
{
    Public string Name{get;set;}
    Public string Description{get;set;}
    Public string BranchType{get;set;}
    Public string DeliveryStatus{get;set;}
    Public string Circle{get;set;}
    Public string District{get;set;}
    Public string Division{get;set;}
    Public string Region{get;set;}
    Public string Block{get;set;}
    Public string State{get;set;}
    Public string Country{get;set;}
    Public string Pincode{get;set;}
}
```
```html
<apex:page controller="ShowBranchDetailsController" >
<apex:sectionHeader title="Branch Details" subtitle="Postal Branch Details"/>
<apex:form >
    <apex:PageBlock title="Postal Branch Details: (  {!lstBranchResults.Size}  )"> 
        <apex:pageblockTable value="{!lstBranchResults}" var="br">
            <apex:column value="{!br.Name}"/>
            <apex:column value="{!br.Description}"/>
            <apex:column value="{!br.BranchType}"/>
            <apex:column value="{!br.DeliveryStatus}"/>
            <apex:column value="{!br.District}"/>
            <apex:column value="{!br.Division}"/>
            <apex:column value="{!br.Region}"/>
            <apex:column value="{!br.State}"/>
            <apex:column value="{!br.Country}"/>
            <apex:column value="{!br.pincode}"/>
        </apex:pageblockTable>
    </apex:PageBlock>
</apex:form>
</apex:page>
```
```apex
public class ShowBranchDetailsController 
{
    Public List<PostalBranchDetails> lstBranchResults{get;set;}
    
    Public ShowBranchDetailsController()
    {
        lstBranchResults = new List<PostalBranchDetails>();
        
        string branchDetailsJSON ='[{"Name":"Aluru", ... , "Pincode":"522124"}, ... ]'; // Full Guntur-district branch list (14 branches in source)
        
        lstBranchResults = (List<PostalBranchDetails>) System.JSON.deserialize(branchDetailsJSON, List<PostalBranchDetails>.Class);
    }
}
```
> The constructor directly deserializes the hardcoded JSON into the wrapper list as soon as the controller loads (no button click needed) — useful pattern when data is static/seeded rather than coming from a live callout.

---

## Quick Reference — Generator vs Parser
| Task | Class | Direction |
|---|---|---|
| Apex Object → JSON String | `JSONGenerator` | Serialization |
| JSON String → Apex Object | `JSONParser` / `System.JSON.deserialize()` | Deserialization |

**Shortcut tip:** For straightforward mapping (when JSON keys match sObject/Apex class fields exactly), prefer:
```apex
System.JSON.deserialize(jsonString, TargetType.Class)
```
over manually looping with `JSONParser` — it's far less code and less error-prone.