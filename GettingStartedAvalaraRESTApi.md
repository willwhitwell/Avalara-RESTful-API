# First Steps with Avalara APIs

Not well versed in Computer Programming yet? Not a problem, you can access the power of Avalara’s new AvaTax API in no time too. 

Hello, my name is Joe Savarese, and as a fresh Intern within the Engineering team here at Avalara, I was given the great opportunity to attend a Demo on Avalara’s new REST API (Being that I work on a different project this was all new to me). I nervously arrived on Bainbridge Ilsand and found a seat in a room full of experienced Developers and Quality Assurance specialists already setting up to check out the new API. Being an intern, I was there to learn and not really sure what to expect, but I couldn’t help but wonder what I had got myself into as I struggled to ignore the fact I hadn’t a clue on how to implement an API and I knew I had extremely limited knowledge of how they worked. But before my face could turn blue in a hyperventilating panic, my anxieties began to diminish as I read through instructions on how to get started and realized that even I, the new Intern, could begin utilizing the features of the API in no time with little to no prior experience. And I guess that’s kind of the point right? I had access to their integrated system of services, all of which were provided through making simple requests to a URL, so really, one wouldn’t need to know too much about what’s happening behind the scenes.

A little research into the [CRUD](http://www.restapitutorial.com/lessons/httpmethods.html) syntax of HTTP and help from the web service client [Postman](https://www.getpostman.com/)  I quickly had a collection of calls to the API capable of setting up an account with a company and implementing the features of Avalara’s AvaTax software. Also, as it turns out, this process was great in learning how to get started with AvaTax and what it has to offer a company, regardless of how technically savvy you are. As the API is fine-tuned to meet the needs of the users it should only get easier and more accessible.

Here I will walk you through what I did at least to get started using the API and what I was able to do. In this section I’ll discuss how I implemented these features:
* Access an AvaTax Account to make authorized requests to the API.
* Get some Account information that will be required to set up a Company.
* Add a new Company to my service Account Using POST /api/v2/companies.
* Make Changes to my Current Company Using the PUT /api/v2/companies/{id} endpoint.
* Provide Contact information for the Company.
* Set up a Company Location.
* Establish Nexus for the Company.
* Add Another Company, with the Larger Encompassing POST /api/v2/companies/initialize.
* Making use of the GET function to Access All the Company Information in a Single Call.
* Create a Company Transaction for which Taxes may be Determined.

## Establishing Account Credentials
So after installing the Postman application, I was able to see that to send any requests to the API I needed to first authenticate my account for proper authorization. To do so I needed the username and password credentials from my new AvaTax account. 

## Easy Access, First Request: GET http://<hostname>/api/v2/accounts
Using those credentials in Postman, clicking on authorization and selecting the Basic Auth option I filled in my username and password so Postman could generate an authorization header by encoding that information into a base64 representation.

After clicking the button to update request, filling in the header automatically, I was able to start making calls to the API. The Swagger Documentation contains a list of features of the API, each of which can be expanded to obtain the various requests within each category.

Notice that when you expand these options they are filled with variations of the basic CRUD methods I mentioned before. Postman allows you to generate requests for each of these specific methods by simply entering the URL and providing proper Headers and Body text as needed. Fortunately there is also an option to try it out directly from the documentation itself.

And as you will see in the following request/response blocks I was able to GET information on my Account by simply updating the Authorization Header, choosing the GET option and pasting the URL from the Swagger documentation into the Request URL section. The response tells me that my Account really does exist.

#### request
```
Headers:
GET http://<hostname>/api/v2/accounts
Authorization: Basic base64(username:password)
Accept: application/json
```

#### response
```json
{
  "@recordsetCount": 1,
  "value": [
    {
      "id": 111111111,
      "name": "TestAccountDemo",
      "endDate": "2099-01-01T00:00:00",
      "accountStatusId": "Active",
      "createdUserId": 222222,
      "modifiedUserId": 222111,
    }
  ]
}
```

Postman also has a nifty save feature that allows me to keep these requests in a collection in case I need to access the same request later!  I recommend using them so you can always go back to your rock-solid requests. 
 
## Now We're In Business: POST http://<hostname>/api/v2/companies
Next I wanted to add a Company to my Account which can be done in two different ways, but for now I'm just going to focus on the POST to the api/v2/companies endpoint and then initialize a company later using POST /companies/initialze. If you're like me you might be assuming a POST has the same format as the GET request we had success with earlier... and you'd soon find out how wrong you were. My first basic POST request scolded me with a 415 Unsuppported Media Type error, meaning I needed a Content-Type Header. Once I clicked Headers, and filled in the row below my authorization Header, inputing **Content-Type** in the **key** column on the left and **application/json** in the right column I POSTed once more. This time the response also told me I required a **Model** for the body section of my request. Still new to this, I really had no idea how or why I’d do that, but surprisingly during what I’d like to call a “trial-error-google-adjust” process I was able to fairly quickly address the error by clicking **Body** in Postman and entering in the beginnings of the model required by initializing an Object in JSON format. This is outlined in the following code blocks. 

#### request
```
POST http://<hostname>/api/v2/companies
Authorization: Basic base64(username:password)
Content-Type: application/json
```

#### response
```json
{
  "error": {
    "code": "RequestParsing",
    "message": "A required Model was not provided.",
    "target": "IncorrectData",
    "details": [
      {
        "ErrorCode": 38,
        "Summary": "A required Model was not provided.",
        "Details": "{0}",
        "FaultCode": "Client",
        "HelpLink": "http://www.avalara.com",
        "Name": "ModelRequiredException",
        "Severity": "Exception"
      }
    ]
  }
}
```

It’s requesting a model, which I found by entering empty brackets to the request code body and reading through the “missing field” errors in the response code seen below. 
#### request
```json
POST http://<hostname>/api/v2/companies
Authorization: Basic base64(username:password)
Content-Type: application/json
{
	"name": "DemoCompany",
}
```

#### response
```json
{
  "error": {
    "code": "IncorrectData",
    "message": "Field companyCode is required.",
    "target": "IncorrectData",
    "details": [
      {
        "ErrorCode": 14,
        "Summary": "Field companyCode is required.",
        "Details": "",
        "FaultCode": "Client",
        "HelpLink": "http://www.avalara.com",
        "Name": "StringValueRequiredError",
        "Severity": "Error"
      },
      {
        "ErrorCode": 14,
        "Summary": "Field taxpayerIdNumber is required.",
        "Details": "",
        "FaultCode": "Client",
        "HelpLink": "http://www.avalara.com",
        "Name": "StringValueRequiredError",
        "Severity": "Error"
      }
    ]
  }
}
```
 
At this point I was just hoping nobody saw my continuous stream of errors. When I finally figured out what goes in the body section of the request based on those error message summaries, both Postman and the API happily produced an error free response found in the next code block.  
 
#### request
```json
POST http://<hostname>/api/v2/companies
Authorization: Basic base64(username:password)
Content-Type: application/json
{
  "name": "DemoCompany",
  "companyCode": "123",
  "accountId": 1111111111,
  "taxpayerIdNumber": "44444"
}
```

#### response
```json
[
  {
    "id": 333333,
    "accountId": 1111111111,
    "companyCode": "123",
    "name": "DemoCompany",
    "isDefault": false,
    "isActive": false,
    "taxpayerIdNumber": "123456789",
    "hasProfile": false,
    "isReportingEntity": false,
    "defaultCountry": "US",
    "inProgress": false,
    "createdUserId": 288963,
    "modifiedUserId": 288963
  }
]

```
According to the response I can assume a Company named "DemoCompany" has been added to the account "TestAccountDemo". Although, I would like to be certain of this and to do so I used the GET Company request to see if the Company I attempted to POST would show up:

#### request
```
GET http://<hostname>/api/v2/companies
Authorization: Basic base64(username:password)
Accept: application/json
```

#### response
```json
{
  "@recordsetCount": 1,
  "value": [
    {
      "id": 3333333,
      "accountId": 1111111111,
      "companyCode": "123",
      "name": "DemoCompany",
      "isDefault": false,
      "isActive": false,
      "createdUserId": 288963,
      "modifiedUserId": 288963,
      "taxpayerIdNumber": "44444",
      "hasProfile": false,
      "isReportingEntity": false,
      "entityNo": 1,
      "inProgress": false
    }
  ]
}
```
 
And voila! - a new Company. Not quite time to celebrate yet though, notice many fields appear in the response that were not initialized in my request. Depending on the nature of your business, more information may be necessary to provide the company with the full functionality of the API.  Fortunately the entire Model including all the possible fields is available in the Swagger documentation! Though for my purposes, with the addition of a just a few more fields I'll have created a Company that I can now use to make Tax Calculations against!

## PUT 'Er There: PUT http://<hostname>/api/v2/companies/{companyId}
Notice in the GET api/v2/companies request my Company is Inactive and has no Tax Profile. These will have to be changed to true in order for the API to calculate tax on the transaction later. To rectify this problem we will modify these fields with a PUT request. 

#### request
```json
PUT http://<hostname>/api/v2/companies/{id}
Authorization: Basic base64(username:password)
Content-Type: application/json
	
	{
	"companyCode": 123,
	"accountId": 1111111111,
	"name": "DemoCompany",
	"taxPayerIdNumber": "123456789",
	"defaultCountry": "US",
    "isTest": true,
	"isActive": true,
	"hasProfile": true
	}
```

#### response
```json
{
  "id": 5551111,
  "accountId": 1111111111,
  "companyCode": "123",
  "name": "DemoCompany",
  "isDefault": false,
  "isActive": true,
  "taxpayerIdNumber": "123456789",
  "hasProfile": true,
  "isReportingEntity": false,
  "defaultCountry": "US",
  "isTest": true,
  "inProgress": false,
  "createdUserId": 111111,
  "modifiedUserId": 111222
}
```

Now that "isActive" is set to true, and "hasProfile" is set to true, I have Active Account and Tax Profile. Don't mind the "isTest" field unless, of course, your Using the API features to test. The "isReportingEntity" field being "false" simply means the Companies Transacion history could potentially be combined with its Parent Company if you have one. Right now, I don't, but if you do note that you will be required to provide the proper Credentials ("parentCompanyId": company{id}) in order to access all the API features successfully. This can be done in the initial POST /companies or any subsequent PUT /companies/{id} enpoint calls, where the Integer entered below is the API companyId of the Parent.

```json
{
    "parentCompanyId": 4441111
}
```

## Reaching Out: GET http://<hostname>/api/v2/companies/<companyid>/api/v2/contacts
Now that I have a Company to go with my Account, I figured I would see how to build on that Company by adding Contact information to it. After clicking the try it now button inside the POST /api/v2/companies/{companyId}/contacts API method it prompts me for a Company ID for the Company that owns this contact. Which is that “id” number which shows up in the response of the GET and POST requests against the /api/v2/companies endpoint. Using that Company “id” I was able to generate the correct request URL in the Post /api/v2/companies/contact endpoint. Since it’s a POST request I figured that I probably need to provide some sort of body code that contained a Contact-related model. Using the Swagger documentation model schema, I i into my request body, edited the fields I wanted, deleted those I didn’t need, and then clicked to POST request to the Contacts endpoint. Here’s what that might look like:

#### request
```
POST http://<hostname>/api/v2/companies/{companyId}/contacts
Authorization: Basic base64(username:password)
Content-Type: application/json
[{
    "id": 3333333,
    "companyId": 5551111,
    "contactCode": "Default",
    "firstName": "Jason",
    "lastName": "Bourne",
    "line1": "4th Ave & Jackson",
    "city": "Seattle",
    "region": "WA",
    "postalCode": "98101",
    "country": "US",
    "email": "fakeEmail@fake.com",
    "phone": "55555555",
}]

```

#### response
```json
[
  {
    "id": 333333,
    "companyId": 5551111,
    "contactCode": "Default",
    "firstName": "Jason",
    "lastName": "Bourne",
    "line1": "4th Ave & Jackson",
    "city": "Seattle",
    "region": "WA",
    "postalCode": "98101",
    "country": "US",
    "email": "fakeEmail@fake.com",
    "phone": "5555555",
    "createdUserId": 222222,
    "modifiedUserId": 222111
  }
]

```
## Where am I?: POST http://<hostname>/api/v2/companies/{companyId}/locations
After successfully getting a Contact added to my Company, I thought it may need a Location. I followed the same steps as adding a Contact, but just changed up the URL and the model.  Below is condensed version of that request/response code block.

#### request
```
POST http://<hostname>/api/v2/companies/{companyId}/locations
Authorization: Basic base64(username:password)
Content-Type: application/json
[{
	"id": 333333,
	"companyId": 5551111,
	"locationCode": "Default",
	"addressTypeId": "Location",
	"addressCategoryId": "MainOffice",
	"firstName": "Jason",
	"lastName": "Bourne",
	"line1": "4th Ave & Jackson",
    "city": "Seattle",
    "region": "WA",
    "postalCode": "98101",
    "country": "US",
    "email": "fakeEmail@fake.com",
    "phone": "5555555",
}]
```

#### response
```json
[
  {
    "id": 123123,
    "companyId": 5551111,
    "locationCode": "Default",
    "addressTypeId": "Location",
    "addressCategoryId": "MainOffice",
    "line1": "4th Ave & Jackson",
    "city": "Seattle",
    "region": "WA",
    "postalCode": "98101",
    "country": "US",
    "isDefault": false,
    "createdUserId": 222222,
    "modifiedUserId": 222111
  }
]
```

 
## Establishing Nexus: POST http://<hostname>/api/v2/companies/{companyId}/nexus
I decided to endeavor upon setting up a Nexus for my Company considering I will eventually want to do some tax calculations on Transactions. But simply knowing of Nexus in an Economic sense was not enough, to be sure I understood how it works within the Avalara API I found a great feature that allows me to actually use the GET api/v2/definition request and learn more about it. 

### Defining Nexus: GET http://<hostname>/api/v2/definitions/nexus/{country}/{region}

The following code and block illustrates the response from the GET definition feature from the endpoint /api/v2/definitions/US/WA:

 #### request
```
GET http://<hostname>/api/v2/definitions/nexus/US/WA
Authorization: Basic base64(username:password)
Accept: application/json
```

#### response
```json
{
      "id": 12345678,
      "companyId": 5551111,
      "state": "WA",
      "jurisTypeId": "STA",
      "jurisCode": "53",
      "jurisName": "WASHINGTON",
      "shortName": "WA",
      "signatureCode": "",
      "stateAssignedNo": "",
      "nexusTypeId": "Volunteer",
      "country": "US",
      "accountingMethodId": 0,
      "hasLocalNexus": false,
      "hasPermanentEstablishment": true,
      "createdUserId": 222222,
      "modifiedUserId": 222111
}

```
That lookup provides information necessary to establish nexus in Washington State. The code block below is another request looking up nexus in the broader context of countries of the api/v2/definitions/nexus endpoint. Which produces a huge list of all nexus jurisdictions with "country": "US", by pressing: *control-f* to open a search and typing (quotations and all): **"state": "US"** to go directly to the sought after information. 

 #### request
```
GET http://<hostname>/api/v2/definitions/nexus
Authorization: Basic base64(username:password)
Accept: application/json
```

#### response
```json
{
      "id": 1190189,
      "companyId": 1,
      "state": "US",
      "jurisTypeId": "CNT",
      "jurisCode": "US",
      "jurisName": "UNITED STATES",
      "shortName": "US",
      "signatureCode": "",
      "stateAssignedNo": "",
      "nexusTypeId": "Volunteer",
      "country": "US",
      "accountingMethodId": 0,
      "hasLocalNexus": true,
      "hasPermanentEstablishment": true,
      "createdUserId": 222222,
      "modifiedUserId": 222111
    },

```
Armed with this knowledge I can figure out how to establish Nexus in Washington State and the United States with the POST Nexus method, really the only modification necessary to the information provided from the GET definitions request is to add an "effectiveDate" and "endDate" to specify how long your company will have nexus within these regions. 

#### request
```
POST http://<hostname>/api/v2/companies/company{Id}/nexus
Authorization: Basic base64(username:password)
Content-Type: application/json
[
    {
        "companyId": 5551111,
        "state": "US",
        "jurisTypeId": "CNT",
        "jurisCode": "US",
        "jurisName": "UNITED STATES",
        "shortName": "US",
        "signatureCode": "",
        "stateAssignedNo": "",
        "nexusTypeId": "Volunteer",
        "country": "US",
        "accountingMethodId": 0,
        "hasLocalNexus": true,
        "hasPermanentEstablishment": true,
        "effectiveDate": "2015-01-01T21:15:41.78",
        "endDate": "2020-01-01T21:15:41.78"
    },
    {
        "companyId": 5551111,
        "state": "WA",
        "jurisTypeId": "STA",
        "jurisCode": "53",
        "jurisName": "WASHINGTON",
        "shortName": "WA",
        "signatureCode": "",
        "stateAssignedNo": "",
        "nexusTypeId": "Volunteer",
        "country": "US",
        "accountingMethodId": 0,
        "hasLocalNexus": true,
        "hasPermanentEstablishment": true,
        "effectiveDate": "2015-01-01T21:15:41.78",
        "endDate": "2020-01-01T21:15:41.78"
    }
]

```

#### response
```json
[
  {
          "id": 12345678,
          "companyId": 5551111,
          "state": "US",
          "jurisTypeId": "CNT",
          "jurisCode": "US",
          "jurisName": "UNITED STATES",
          "effectiveDate": "2015-01-01T21:15:41.78",
          "endDate": "2020-01-01T21:15:41.78",
          "shortName": "US",
          "signatureCode": "",
          "stateAssignedNo": "",
          "nexusTypeId": "Volunteer",
          "country": "US",
          "accountingMethodId": 0,
          "hasLocalNexus": true,
          "hasPermanentEstablishment": true,
          "createdUserId": 222222,
          "modifiedUserId": 222111
        },
        {
          "id": 12312398,
          "companyId": 5551111,
          "state": "WA",
          "jurisTypeId": "STA",
          "jurisCode": "53",
          "jurisName": "WASHINGTON",
          "effectiveDate": "2015-01-01T21:15:41.78",
          "endDate": "2020-01-01T21:15:41.78",
          "shortName": "WA",
          "signatureCode": "",
          "stateAssignedNo": "",
          "nexusTypeId": "Volunteer",
          "country": "US",
          "accountingMethodId": 0,
          "hasLocalNexus": true,
          "hasPermanentEstablishment": true,
          "createdUserId": 222222,
          "modifiedUserId": 222111
        }
]
```
There we have it, Nexus in all the jurisdictions to which the company is legally bound.

## Initialize? Why Didn't You Say So?: POST http://<hostname>/api/v2/api/v2/companies/initialize
If you were thinking that all those requests were a bit cumbersome just to Create a Company Entity then have I got a solution for you. POST /api/v2/companies is a method more effectively  used for POSTing one or more Companies once a Company or a Parent Company has already been initialized. So real quick, here's the request/response code for the awesome companies/initialize endpoint, POSTing a Company Entity, a Contact, a Location, and Nexus in one fell swooping request. 
### request
```
POST http://<hostname>.net:8020/api/v2/companies/initialize
Content-Type application/json
{
  "name": "DemoCompany2",
  "companyCode": "3467",
  "vatRegistrationId": "string",
  "taxpayerIdNumber": "string",
  "line1": "4th Ave & Jackson",
  "line2": "string",
  "line3": "string",
  "city": "Seattle",
  "region": "WA",
  "postalCode": "98101",
  "country": "US",
  "firstName": "Jason",
  "lastName": "Bourne",
  "title": "Blacklisted",
  "email": "befree81@gmail.com",
  "phoneNumber": "4257712146",
  "mobileNumber": "2067185766",
  "faxNumber": "4257712146",
  "isTest": true
}
```

### response
```json
{
  "id": 6662222,
  "accountId": 1111111111,
  "sstPid": "",
  "companyCode": "3467",
  "name": "DemoCompany2",
  "isDefault": false,
  "defaultLocationId": 0,
  "isActive": true,
  "taxpayerIdNumber": "string",
  "hasProfile": true,
  "isReportingEntity": true,
  "defaultCountry": "US",
  "baseCurrencyCode": "USD",
  "roundingLevelId": "Line",
  "warningsEnabled": false,
  "isTest": false,
  "taxDependencyLevelId": "Document",
  "inProgress": false,
  "businessIdentificationNo": "string",
  "createdUserId": 222222,
  "modifiedUserId": 222111,
  "contacts": [
    {
      "id": 739322,
      "companyId": 6662222,
      "contactCode": "Default",
      "firstName": "Jason",
      "middleName": "",
      "lastName": "Bourne",
      "title": "Blacklisted",
      "line1": "4th Ave & Jackson",
      "line2": "string",
      "line3": "string",
      "city": "Seattle",
      "region": "WA",
      "postalCode": "98101",
      "country": "US",
      "email": "befree81@gmail.com",
      "phone": "4257712146",
      "mobile": "2067185766",
      "fax": "4257712146",
      "createdUserId": 222222,
      "modifiedUserId": 222111
    }
  ],
  "locations": [
    {
      "id": 898898,
      "companyId": 6662222,
      "locationCode": "DEFAULT",
      "description": "",
      "addressTypeId": "Location",
      "addressCategoryId": "MainOffice",
      "line1": "4th Ave & Jackson",
      "line2": "string",
      "line3": "string",
      "city": "Seattle",
      "county": "",
      "region": "WA",
      "postalCode": "98101",
      "country": "US",
      "isDefault": true,
      "isRegistered": false,
      "dbaName": "DemoCompany2",
      "outletName": "Main Office",
      "createdUserId": 222222,
      "modifiedUserId": 222111
    }
  ],
  "nexus": [
    {
      "id": 33443344,
      "companyId": 6662222,
      "country": "US",
      "region": "WA",
      "jurisTypeId": "STA",
      "jurisCode": "53",
      "jurisName": "WASHINGTON",
      "shortName": "WA",
      "signatureCode": "",
      "stateAssignedNo": "",
      "nexusTypeId": "SalesOrSellersUseTax",
      "hasLocalNexus": false,
      "hasPermanentEstablishment": true,
      "createdUserId": 288963,
      "modifiedUserId": 288963
    },
    {
      "id": 33443345,
      "companyId": 662222,
      "country": "US",
      "region": "US",
      "jurisTypeId": "CNT",
      "jurisCode": "US",
      "jurisName": "UNITED STATES",
      "shortName": "US",
      "signatureCode": "",
      "stateAssignedNo": "",
      "nexusTypeId": "SalesOrSellersUseTax",
      "hasLocalNexus": true,
      "hasPermanentEstablishment": true,
      "createdUserId": 288963,
      "modifiedUserId": 288963
    }
  ]
}
```

## Get It All Back: GET http://<hostname>/api/v2/companies?$include=Contacts,Locations,Nexus
If I wanted take a look at everything I’ve done so far there’s a way to make a call to my company that includes the Get Account call, Get Company call, Get Locations call, and the Get Nexus call by filling in values for the $include parameter.
 
Here’s the request/response code block:
#### request
```
GET http://<hostname>/api/v2/companies?%24include=Contacts%2CLocations%2CNexus
Authorization: Basic base64(username:password)
Accept: application/json
```

#### response
```json
{
  "@recordsetCount": 1,
  "value": [
    {
      "id": 5551111,
      "accountId": 111111111,
      "companyCode": "123",
      "name": "DemoCompany",
      }...
      {
      "contacts": [
        {
          "id": 333333,
          "companyId": 5551111,
          "contactCode": "Default",
          "firstName": "Jason",
          "lastName": "Bourne"
        }...
      "locations": [
        {
          "id": 123123,
          "companyId": 5551111,
          "locationCode": "Default",
          "addressTypeId": "Location",
        }...
      ],
      "nexus": [
        {
          "id": 12345678,
          "companyId": 4701200,
          "state": "US",
          "jurisTypeId": "CNT",
          "jurisCode": "US",
          "jurisName": "UNITED STATES",
          }...
        {
          "id": 12345698,
          "companyId": 5551111,
          "state": "WA",
          "jurisTypeId": "STA",
          "jurisCode": "53",
          "jurisName": "WASHINGTON",
        }...
```
## Getting Taxed by the Man:  http://<hostname>/api/v2/transactions/create
Finally, the moment we've all been waiting for... using the API to create a transaction and calculate taxes. That is what we came to do, right? Since the account has a pseudo Company containing various bits of information by sending a request to the api/v2/transactions/create endpoint I get a transaction that looks something like this:  

#### request
```
POST http://<hostname>/api/v2/transactions/create
Authorization: Basic base64(username:password)
Content-Type: application/json
[{
    "companyCode": "123",
    "type": "SalesInvoice",
    "date": "2016-09-15T19:37:13.664Z",
    "code": "transactionCode",
    "customerCode": "CUST1",
    "commit": false,
    "addresses": {
      "ShipFrom": {
        "line1": "1100 2nd Ave",
        "city": "Seattle",
        "region": "WA",
        "country": "US",
        "postalCode": "98101"
      },
      "ShipTo": {
        "line1": "700 Pike St",
        "city": "Seattle",
        "region": "WA",
        "country": "US",
        "postalCode": "98101"
      }
    },
  
    "lines": [
      {
        "number": "string1",
        "quantity": 5,
        "amount": 500,
        "taxCode": "P000000"
      }
      ]
}]
```

#### response
```json
[
  {
    "id": 45645678,
    "code": "transactionCode",
    "date": "0000-00-00000:00:00",
    "taxDate": "0000-00-00000:00:00",
    "paymentDate": "0000-00-00000:00:00",
    "status": "Saved",
    "type": "SalesInvoice",
    "companyId": 5551111,
    "batchCode": "",
    "currencyCode": "USD",
    "customerUsageType": "",
    "customerVendorCode": "CUST1",
    "exemptNo": "",
    "reconciled": false,
    "purchaseOrderNo": "",
    "salespersonCode": "",
    "taxOverrideType": "None",
    "taxOverrideAmount": 0,
    "taxOverrideReason": "",
    "totalAmount": 500,
    "totalExempt": 0,
    "totalTax": 48,
    "totalTaxable": 500,
    "totalTaxCalculated": 48,
    "adjustmentReason": "NotAdjusted",
    "adjustmentDescription": "",
    "locked": false,
    "region": "WA",
    "country": "US",
    "version": 1,
    "softwareVersion": "16.8.0.71",
    "originAddressId": 36922678,
    "destinationAddressId": 36922679,
    "exchangeRateEffectiveDate": "0000-00-00000:00:00",
    "exchangeRate": 1,
    "isSellerImporterOfRecord": false,
    "modifiedUserId": 288963,
    "lines": [
      {
        "id": 30717155,
        "transactionId": 22409811,
        "lineNumber": "string1",
        "boundaryOverrideId": 0,
        "customerUsageType": "",
        "description": "buildingMaterials",
        "destinationAddressId": 37312414,
        "originAddressId": 37312411,
        "discountAmount": 0,
        "exemptAmount": 0,
        "exemptCertId": 0,
        "exemptNo": "",
        "isItemTaxable": true,
        "isSSTP": true,
        "itemCode": "Nails",
        "lineAmount": 500,
        "quantity": 5,
        "ref1": "",
        "ref2": "",
        "reportingDate": "0000-00-00000:00:00",
        "revAccount": "",
        "sourcing": "Destination",
        "tax": 48,
        "taxableAmount": 500,
        "taxCalculated": 48,
        "taxCode": "P000000",
        "taxCodeId": 155850,
        "taxDate": "0000-00-00000:00:00",
        "taxEngine": "",
        "taxOverrideType": "None",
        "taxOverrideAmount": 0,
        "taxOverrideReason": "",
        "taxIncluded": false,
        "details": [
          {
            "id": 5551111,
            "transactionLineId": 30717155,
            "transactionId": 22409811,
            "addressId": 37312414,
            "country": "US",
            "region": "WA",
            "stateFIPS": "53",
            "exemptAmount": 0,
            "exemptReasonId": 4,
            "inState": true,
            "jurisCode": "53",
            "jurisName": "WASHINGTON",
            "jurisdictionId": 61,
            "signatureCode": "BVPJ",
            "stateAssignedNo": "",
            "jurisType": "STA",
            "nonTaxableAmount": 0,
            "nonTaxableRuleId": 0,
            "nonTaxableType": "RateRule",
            "rate": 0.065,
            "rateRuleId": 1099760,
            "rateSourceId": 3,
            "serCode": "01726",
            "sourcing": "Destination",
            "tax": 32.5,
            "taxableAmount": 500,
            "taxType": "Sales",
            "taxName": "WA STATE TAX",
            "taxAuthorityTypeId": 45,
            "taxRegionId": 2109700,
            "taxCalculated": 32.5,
            "taxOverride": 0,
            "rateType": "General"
          },
          {
            "id": 5551111,
            "transactionLineId": 30717155,
            "transactionId": 22409811,
            "addressId": 37312414,
            "country": "US",
            "region": "WA",
            "stateFIPS": "53",
            "exemptAmount": 0,
            "exemptReasonId": 4,
            "inState": true,
            "jurisCode": "033",
            "jurisName": "KING",
            "jurisdictionId": 2986,
            "signatureCode": "BVVZ",
            "stateAssignedNo": "1700",
            "jurisType": "CTY",
            "nonTaxableAmount": 0,
            "nonTaxableRuleId": 0,
            "nonTaxableType": "RateRule",
            "rate": 0,
            "rateRuleId": 1098909,
            "rateSourceId": 3,
            "serCode": "01726",
            "sourcing": "Destination",
            "tax": 0,
            "taxableAmount": 500,
            "taxType": "Sales",
            "taxName": "WA COUNTY TAX",
            "taxAuthorityTypeId": 45,
            "taxRegionId": 2109700,
            "taxCalculated": 0,
            "taxOverride": 0,
            "rateType": "General"
          },
          {
            "id": 5551111,
            "transactionLineId": 30717155,
            "transactionId": 22409811,
            "addressId": 37312414,
            "country": "US",
            "region": "WA",
            "stateFIPS": "53",
            "exemptAmount": 0,
            "exemptReasonId": 4,
            "inState": true,
            "jurisCode": "63000",
            "jurisName": "SEATTLE",
            "jurisdictionId": 167796,
            "signatureCode": "BVXK",
            "stateAssignedNo": "1726",
            "jurisType": "CIT",
            "nonTaxableAmount": 0,
            "nonTaxableRuleId": 0,
            "nonTaxableType": "RateRule",
            "rate": 0.031,
            "rateRuleId": 1290133,
            "rateSourceId": 3,
            "serCode": "01726",
            "sourcing": "Destination",
            "tax": 15.5,
            "taxableAmount": 500,
            "taxType": "Sales",
            "taxName": "WA CITY TAX",
            "taxAuthorityTypeId": 45,
            "taxRegionId": 2109700,
            "taxCalculated": 15.5,
            "taxOverride": 0,
            "rateType": "General"
          }
        ]
      }
    ],
    "addresses": [
      {
        "id": 37312411,
        "transactionId": 22409811,
        "boundaryLevel": "Zip5",
        "line1": "1100 2nd Ave",
        "line2": "",
        "line3": "",
        "city": "Seattle",
        "region": "WA",
        "postalCode": "98101-2908",
        "country": "US",
        "taxRegionId": 2109700
      },
      {
        "id": 37312414,
        "transactionId": 22409811,
        "boundaryLevel": "Zip5",
        "line1": "700 Pike St",
        "line2": "",
        "line3": "",
        "city": "Seattle",
        "region": "WA",
        "postalCode": "98101-2311",
        "country": "US",
        "taxRegionId": 2109700
      }
    ],
    "summary": [
      {
        "country": "US",
        "region": "WA",
        "jurisType": "State",
        "jurisCode": "53",
        "jurisName": "WASHINGTON",
        "taxAuthorityType": 45,
        "stateAssignedNo": "",
        "taxType": "Sales",
        "taxName": "WA STATE TAX",
        "rateType": "General",
        "taxable": 500,
        "rate": 0.065,
        "tax": 32.5,
        "taxCalculated": 32.5,
        "nonTaxable": 0,
        "exemption": 0
      },
      {
        "country": "US",
        "region": "WA",
        "jurisType": "County",
        "jurisCode": "033",
        "jurisName": "KING",
        "taxAuthorityType": 45,
        "stateAssignedNo": "1700",
        "taxType": "Sales",
        "taxName": "WA COUNTY TAX",
        "rateType": "General",
        "taxable": 500,
        "rate": 0,
        "tax": 0,
        "taxCalculated": 0,
        "nonTaxable": 0,
        "exemption": 0
      },
      {
        "country": "US",
        "region": "WA",
        "jurisType": "City",
        "jurisCode": "63000",
        "jurisName": "SEATTLE",
        "taxAuthorityType": 45,
        "stateAssignedNo": "1726",
        "taxType": "Sales",
        "taxName": "WA CITY TAX",
        "rateType": "General",
        "taxable": 500,
        "rate": 0.031,
        "tax": 15.5,
        "taxCalculated": 15.5,
        "nonTaxable": 0,
        "exemption": 0
      }
    ]
  }
]
```
Now that this transaction has been created, you can see that the POST method finds the tax rules within the three Nexus locations and applies them to the Transaction. For example, in the above response: using `"jurisName": "WASHINGTON"` with the `"rate": 0.065,` applied to the Transaction value `"taxable": 500,` ultimately returns  `"taxCalculated": 32.5,` which comes out to **$32.50**. Since `"jurisName": "KING",` has `"rate": 0,` it moves onto `"jursName": "SEATTLE",` with `"rate": 0.031,` applies it to the `"taxable": 500,` which yields `"taxCalculated": 15.5,` or **$15.50**. Thus, returning `"totalTax": 48,` or **$48.00**.

## Finishing up  
Now that I started to see things fall into place from calling these various features of the API I was reassured that this is a manageable RESTful API service for anyone who wishes to utilize it. So just I got a chance to breathe a sigh of relief, as I ceased to crawl and began to walk with the enhancing power of Avalaras new REST API, Perhaps this rundown will help you do the same.