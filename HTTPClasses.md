# HTTP Classes and UseCases 

## 1. `Http` Class
Used to initiate an HTTP request and get the response.

| Method | Purpose |
|---|---|
| `Send(httpRequest)` | Sends the HTTP Request to the specified endpoint, returns the HTTP Response |
| `ToString()` | Returns a string identifying the object's properties |

---

## 2. `HttpRequest` Class
Used to programmatically create HTTP requests — GET, POST, PUT, DELETE.
- Set the method type, request headers (for endpoint authentication), connection timeouts, and message body content.

### Methods
1. `SetMethod('GET')`
2. `SetEndPoint('https://gmail.com/accounts/')`
3. `SetBody('Request Body')`
4. `SetClientCertificateName(certificateName)`
5. `SetTimeOut(Time)`
6. `SetHeader(Key, Value)`
7. `SetCompressed(Boolean)`

### Example
```apex
HttpRequest postalRequest = new HttpRequest();

postalRequest.SetEndPoint('https://api.postalpincode.in/pincode/500038');
postalRequest.SetMethod('GET');
postalRequest.SetTimeOut(10000); // Allowed range: 1 – 1,20,000 milliseconds
```

---

## 3. `HttpResponse` Class
Used to handle the HTTP response returned by the `Http` class.
- Get the response body content.
- Set the response header (if needed).
- Set/Get the status code returned.

### Methods
1. `GetBody()`
2. `GetStatus()`
3. `GetStatusCode()`
4. `GetXMLStreamReader()`

### Example
```apex
Http postalHttp = new Http();

HttpRequest postalRequest = new HttpRequest();
postalRequest.SetEndPoint('https://api.postalpincode.in/pincode/500038');
postalRequest.SetMethod('GET');
postalRequest.SetTimeOut(10000);

HttpResponse postalResponse = postalHttp.Send(postalRequest);

string response = postalResponse.GetBody();    
system.debug('Response is....: '+ response);
```

---

## 4. Steps to Invoke Third-Party WebServices
1. **Step 1:** Gather details of the WebService to invoke:
   - EndPoint URL
   - Method Name
   - Required Input Parameters
   - Authorization Codes
2. **Step 2:** Add the EndPoint URL to **Remote Site Settings**:
   `Setup → Administer → Security Controls → Remote Site Settings`
   1. Click "New Remote Site".
   2. Specify Remote Site Name & Description.
   3. Specify the URL/Path.
   4. Click "Save".
3. **Step 3:** Connect to the third-party system/WebService using `Http`, `HttpRequest`, `HttpResponse` classes.
4. **Step 4:** Get the response, parse the content, and represent it inside the application.

---

## 5. UseCase: Search Postal Branch Details by Pin Code (PostalPinCode API)

**Step 1 — Inputs**
- EndPoint URL: `https://api.postalpincode.in/pincode/{PINCODE}`
- Method Type: GET
- Response Type: JSON

**Step 2** — Add URL to Remote Site Settings.

**Step 3** — Write code to call & parse the response.

### Wrapper Class
```apex
public class PostOfficeBranch 
{
    Public string Name {get;set;}
    Public string Description{get;set;}
    Public string BranchType{get;set;}
    Public string DeliveryStatus{get;set;}
    Public string Circle{get;set;}
    Public string District{get;set;}
    Public string Division{get;set;}
    Public string Region{get;set;}
    Public string State{get;set;}
    Public string Country{get;set;}
}
```

### Visualforce Page
```html
<apex:page controller="SearchIndianPostalBranchesController" setup="false" sidebar="false" showheader="true" tabStyle="Recruiter__c" >
<apex:sectionHeader title="Postal" subtitle="Postal Branches"/>
<apex:form >
    <apex:Pageblock title="Postal Branches Block">
        <apex:pageblockSection title="Search Branches" columns="2" collapsible="true"> 
            <apex:inputtext label="Enter Postal Pin Code: " html-placeholder="Enter Pin Code" value="{!postalPinCode}"/>
            <apex:commandButton value="Search Branches" action="{!SearchPostalBranches}" rerender="pgBlock"/>
        </apex:pageblockSection>
    </apex:Pageblock>
    
    <apex:Pageblock title="Postal Branches : ( {!lstBranches.Size} )" id="pgBlock">
        <apex:pageblockTable value="{!lstBranches}" var="br">
            <apex:column value="{!br.Name}" headerValue="Name"/>
            <apex:column value="{!br.BranchType}" headerValue="BranchType"/>
            <apex:column value="{!br.DeliveryStatus}" headerValue="DeliveryStatus"/>
            <apex:column value="{!br.Circle}" headerValue="Circle"/>
            <apex:column value="{!br.District}" headerValue="District"/>
            <apex:column value="{!br.Division}" headerValue="Division"/>
            <apex:column value="{!br.Region}" headerValue="Region"/>
            <apex:column value="{!br.State}" headerValue="State"/>
            <apex:column value="{!br.Country}" headerValue="Country"/>
        </apex:pageblockTable>
    </apex:Pageblock>
</apex:form>  
</apex:page>
```

### Controller Class
```apex
public class SearchIndianPostalBranchesController 
{
    Public string postalPinCode{get;set;}
    Public List<PostOfficeBranch> lstBranches{get;set;}
    
    Public string postalResponseJSON {get;set;}
    
    Public void SearchPostalBranches()
    {
        lstBranches = new List<PostOfficeBranch>();
        
        if(postalPinCode != null && postalPinCode != '')
        {
            string endPointURL = 'https://api.postalpincode.in/pincode/'+ postalPinCode;
            
            Http postalHttp = new Http();
            
            HttpRequest postalRequest = new HttpRequest();
                postalRequest.setEndpoint(endPointURL);
                postalRequest.setMethod('GET');
                postalRequest.setTimeout(20000);
            
            HttpResponse postalResponse = postalHttp.send(postalRequest);
            postalResponseJSON = postalResponse.getBody();
            
            // Parse the JSON Content and represent in tabular format
            System.JSONParser pParser = System.JSON.createParser(postalResponse.getBody());
            
            While(pParser.nextToken() != null)
            {
                if(pParser.getCurrentToken() == JsonToken.FIELD_NAME && pParser.getText() == 'Message')
                {
                    pParser.nextToken();
                    system.debug('JSON Response Message is...: '+ pParser.getText());
                }
                if(pParser.getCurrentToken() == JsonToken.FIELD_NAME && pParser.getText() == 'Status')
                {
                    system.debug('Response Status is...: '+ pParser.getText());
                }
                if(pParser.getCurrentToken() == JsonToken.FIELD_NAME && pParser.getText() == 'PostOffice')
                {
                    pParser.nextToken();
                    if(pParser.getCurrentToken() == JsonToken.START_ARRAY)
                    {
                        pPArser.nextToken();
                        While(pPArser.getCurrentToken() == JsonToken.START_OBJECT)
                        {
                            PostOfficeBranch branch = (PostOfficeBranch) pParser.readValueAs(PostOfficeBranch.Class);
                            lstBranches.Add(branch);
                            pParser.nextToken();
                        }
                    }
                }
            }
        }
    }
}
```
> Note: `readValueAs(Type.Class)` directly deserializes the current JSON object/array element into the given Apex type — a shortcut vs. manually parsing every field.

---

## 6. UseCase: Show Weather Details by City (OpenWeatherMap API)

**Step 1 — Inputs**
- EndPoint URL: `https://api.openweathermap.org/data/2.5/weather?q={city name}&appid={your api key}`
- Parameters: City Name, AppID (e.g., `21dc4546bf9ce1ec7978bf415a2caa5c`)
- Method Type: GET

**Step 2** — Add EndPoint URL to Remote Site Settings.

**Step 3** — Create **Custom Labels** to store EndPoint URL & App Key:
1. `WeatherMapAPIEndPoint` → `System.Label.WeatherMapAPIEndPoint`
2. `WeatherMapApplicationKey` → `System.Label.WeatherMapApplicationKey`

**Step 4** — Build the VF page + controller logic.

### Visualforce Page
```html
<apex:page controller="ShowWeatherDetailsByCityController" setup="false" sidebar="false" showheader="true" tabStyle="Position__c" >
<apex:sectionHeader title="Weather" subtitle="Weather Details"/>
<apex:form >
    <apex:pageblock title="Weather Details Block">
        <apex:PageBlockSection title="Search Weather Details" columns="2" collapsible="false">
            <apex:inputtext label="Enter City Name : " html-placeholder="Enter City Name" value="{!cityName}"/>
            <apex:commandButton value="Show Weather Details" action="{!GetWeatherDetails}" rerender="pgBlock"/>
        </apex:PageBlockSection>
    </apex:pageblock>
    
    <apex:Pageblock title="Weather Details for : {!cityName}" id="pgBlock">
        <apex:PageblockSection title="Weather Details Section" columns="2" collapsible="false">
            <apex:outputLabel ><b> Pressure Value : {!pressureValue} </b></apex:outputLabel>
            <apex:outputLabel ><b> Average Temperature : {! averageTemperature} </b></apex:outputLabel>
            <apex:outputLabel ><b> Humidity Value : {! humidityValue} </b></apex:outputLabel>
            <apex:outputLabel ><b> Minimum Temperature : {! mintemperature}</b></apex:outputLabel>
            <apex:outputLabel ><b> Feels Like Temperature : {!feelsLikeTemp } </b></apex:outputLabel>
            <apex:outputLabel ><b> Maximum Temperature : {! maxTemperature}</b></apex:outputLabel>
        </apex:PageblockSection>
    </apex:Pageblock>
</apex:form>
</apex:page>
```

### Controller Class
```apex
public class ShowWeatherDetailsByCityController 
{
    Public string cityName{get;set;}
    Public string weatherResponseJSON{get;set;}
    
    Public string humidityValue{get;set;}
    Public string pressureValue{get;set;}
    Public string feelsLikeTemp{get;set;}
    Public string mintemperature{get;set;}
    Public string maxTemperature{get;set;}
    Public string averageTemperature{get;set;}
    
    Public void GetWeatherDetails()
    {
        string endpointURL = System.Label.WeatherMapAPIEndPoint +  cityName + '&appid='+ System.Label.WeatherMapApplicationKey;
        
        Http wHttp = new Http();
        
        HttpRequest weatherRequest = new HttpRequest();
            weatherRequest.setEndpoint(endpointURL);
            weatherRequest.setMethod('GET');
            weatherRequest.setTimeout(60000);
       
        HttpResponse weatherResponse = wHttp.send(weatherRequest);        
        weatherResponseJSON = weatherResponse.getBody();
        
        // Parse the JSON Response and represent on the VF Page
        System.JSONParser wParser = System.JSON.createParser(weatherResponse.getBody());
        
        While(wParser.nextToken() != null)
        {
            if(wParser.getCurrentToken() == JsonToken.FIELD_NAME && wParser.getText() == 'main')
            {
                wParser.NextToken();
                
                if(wParser.GetCurrentToken() == JsonToken.START_OBJECT)                
                {
                    wParser.NextToken();
                    
                    if(wParser.getCurrentToken() == JsonToken.FIELD_NAME && wParser.getText() == 'temp')
                    {
                        wParser.nextToken();
                        averageTemperature = (wParser.getDecimalValue() - 273.15) + ' C';
                    }
                    wParser.NextToken();
                    
                    if(wParser.getCurrentToken() == JsonToken.FIELD_NAME && wParser.getText() == 'feels_like')
                    {
                        wParser.nextToken();
                        feelsLikeTemp = (wParser.getDecimalValue() - 273.15)+ ' C';
                    }
                   
                    wParser.NextToken();
                    if(wParser.getCurrentToken() == JsonToken.FIELD_NAME && wParser.getText() == 'temp_min')
                    {
                        wParser.nextToken();
                        mintemperature = (wParser.getDecimalValue() - 273.15)+ ' C';
                    }
                    
                    wParser.NextToken();
                    if(wParser.getCurrentToken() == JsonToken.FIELD_NAME && wParser.getText() == 'temp_max')
                    {
                        wParser.nextToken();
                        maxTemperature = (wParser.getDecimalValue() - 273.15)+ ' C';
                    }
                    
                    wParser.NextToken();
                    if(wParser.getCurrentToken() == JsonToken.FIELD_NAME && wParser.getText() == 'pressure')
                    {
                        wParser.nextToken();
                        pressureValue = wParser.getText();
                    }
                    wParser.nextToken();
                    if(wParser.getCurrentToken() == JsonToken.FIELD_NAME && wParser.getText() == 'humidity')
                    {
                        wParser.nextToken();
                        humidityValue = wParser.getText();
                    }
                }
            }
        }
    }    
}
```
> Note: Temperature values from OpenWeatherMap come in **Kelvin** — the code converts to Celsius via `value - 273.15`.

---

## Quick Reference
| Class | Key Methods |
|---|---|
| `Http` | `send()`, `toString()` |
| `HttpRequest` | `setMethod()`, `setEndpoint()`, `setBody()`, `setHeader()`, `setTimeout()` (1–120000 ms), `setClientCertificateName()`, `setCompressed()` |
| `HttpResponse` | `getBody()`, `getStatus()`, `getStatusCode()`, `getXMLStreamReader()` |

**Golden Rule before any callout:** Add the target endpoint to **Named Credentials** first, or the callout will fail.