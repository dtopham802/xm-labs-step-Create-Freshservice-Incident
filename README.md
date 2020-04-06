# xM Labs Create Freshservice Incident
This step is to help with the creation of Freshservice Incidents. 

---------

<kbd>
  <img src="https://github.com/xmatters/xMatters-Labs/raw/master/media/disclaimer.png">
</kbd>

---------

# Files

* [freshservice.jpeg](media/freshservice.jpeg) - Logo for Freshservice Step

# Freshservice
Freshservice is a cloud-based IT Help Desk and service management solution that enables organizations to simplify their IT operations. The solution offers features that include a ticketing system, self-service portal and knowledge-base. This article is specifically talking about the "Create Freshservice Incident" Step. We have an entire workflow after the Incident is created [here](https://github.com/dtopham802/xm-labs-Freshservice).

# Freshservice setup
All you need to do in Freshservice is have an API user you can use as an endpoint. 

# Flow Designer Steps

## Freshservice - Create Incident
This step is made to help with the creation of a Freshservice incident. This can be done in a couple of different ways.
    1) Automatically from an xMatters alert or a POST from another system
    2) By attatching this step to a repsonse option in an alert (i.e monitoting, etc) this will give an engineer a quick option to simply tap and xMatters will take care of the rest.

### Settings
Values for the settings tab. 

| Field | Value |
| ----- | ----- |
| Name | Freshservice - Create Incident |
| Description | Step to create an incident in Freshservice  |
| Icon | <kbd> <img width=70 src="/media/freshservice.jpg"></kbd> |
| Include Endpoint | Yes |
| Endpoint Type | Basic Auth |
| Endpoint Label | FreshService |



### Inputs


| Name  | Required? | Min | Max | Help Text | Default Value | Multiline |
| ----- | ----------| --- | --- | --------- | ------------- | --------- |
| Subject  | Yes | 0 | 2000 | Subject of the ticket | | No |
| Description | Yes | 0 | 20000 | HTML content of the ticket | | Yes |
| Urgency  | Yes | 0 | 2000 | Needs to be a number(1 = Low, 2 = Medium, 3 = High) | | No |
| Impact | Yes | 0 | 2000 | Needs to be a number(1 = Low, 2 = Medium, 3 = High) | | No |
| Source Type  | No | 0 | 2000 | Needs to be a number(1 = Email, 2 = Portal, 3 = Phone) | 1 | No |
| Status | Yes | 0 | 2000 | Needs to be a number(2 = Open, 3 = Pending, 4 = Resolved, 5 = Closed) | | No |
| Email  | Yes | 0 | 2000 | Email address of the requester (API User) | | No |
| Assignment Group | No | 0 | 2000 | Name of the Assignment Group | | No |
| Priority  | Yes | 0 | 2000 | Needs to be a number(1 = Low, 2 = Medium, 3 = High, 4 = Urgent) | | No |



### Outputs

| Name |
| ---- |
| id |

### Script

```javascript
console.log("****************GETTING GROUP ID FROM ASSIGNMENT GROUP INPUT")

var apiRequest = http.request({
    'endpoint': 'FreshService',
    'path': '/api/v2/groups',
    'method': 'GET'
});

var apiResponse = apiRequest.write();
if (apiResponse.statusCode >= 200 && apiResponse.statusCode < 300) {
    var response = JSON.parse(apiResponse.body);
}

    var groups = response.groups;
    var group_id;
        for (i = 0; i < groups.length; i++) {
        
        temp_name = groups[i].name
            if(temp_name == input['Assignment Group']) {
            group_id = groups[i].id;
            break;
            }
        }

console.log("****************REQUEST TO POST A TICKET")

var apiRequest1 = http.request({
    'endpoint': 'FreshService',
    'path': '/api/v2/tickets',
    'method': 'POST'
});

var payload = {
      "status":parseInt(input['Status']),
      "urgency": parseInt(input['Urgency']),
      "impact": parseInt(input['Impact']),
      "description": (input['Description']),
      "subject": (input['Subject']),
      "email": (input['Email']),
      "source": parseInt(input['Source Type']),
      "priority": parseInt(input['Priority']),
      "group_id": group_id
};

var apiResponse1 = apiRequest1.write(payload);
if (apiResponse1.statusCode >= 200 && apiResponse1.statusCode < 300) {
    var something = JSON.parse(apiResponse1.body);
}

output['id'] = something.ticket.id
console.log(output['id'])
```
### Endpoint
On the top right hand side of every canvas in Flow Designer in xMatters, there is a "Components Tab". Click on that, then "Endpoints". If you haven't created a FreshService enpoint yet, click "Add" then fill in the appropriate details like the screenshot. You will need to do this in every workflow you want to leverage the Freshservice Steps.

<kbd>
  <img src="media/Componants FD.png" width="300" height="150">
</kbd>

<kbd>
  <img src="media/Add FreshService Endpoint.png" width="300" height="150">
</kbd>

# Usage
You can use this step in any workflow. I would suggest trying it out from a monitoring workflow. In my example I create a new response option called "Create Freshservice Incident", drag and drop the "Freshservice - Create Incident" over and connect it to the response option. You then need to double click on the step and configure it how you want. In my case, I dragged over event properties over and typed some information in. **NOTE Make sure you point the step to your FreshService endpoint for authentication** 
