In Dataverse (previously known as the Common Data Service, used in Microsoft Dynamics 365 CRM), there are indeed two common approaches for passing parameters to a new record form via the URL. Let me explain both approaches in more detail.

### 1. Passing Field + Value in URL Parameters (Query String)
This is a straightforward method where you append field names and their corresponding values directly as URL parameters in the query string. These parameters are automatically mapped to fields on the form when the form loads. 

#### How It Works:
- In the URL, you append the schema name of the field and the value you want to set for that field.
- The system automatically binds the value to the respective field.

#### Example:
If you have a form for the `Contact` entity and you want to pre-populate the `firstname` and `lastname` fields:

```plaintext
https://yourorg.crm.dynamics.com/main.aspx?etn=contact&pagetype=entityrecord&extraqs=firstname=John&lastname=Doe
```

- **etn**: The entity type name (e.g., `contact`).
- **pagetype**: The page type (e.g., `entityrecord`).
- **extraqs**: Stands for “extra query string” and allows you to pass field-value pairs. Here `firstname` is set to "John" and `lastname` is set to "Doe."

This method is simple and does not require any custom JavaScript. However, it is limited to basic data types and field-value pairs.

### 2. Passing a Custom Object and Parsing It with JavaScript
For more complex scenarios where you need to pass multiple values, arrays, or handle conditional logic, you can pass a custom object or custom parameters in the URL and then use JavaScript to parse and handle them.

#### How It Works:
- You append a custom object or encoded string to the URL (usually using query string or fragment identifiers).
- On the form load, you write a JavaScript function that parses the URL to retrieve the parameters.
- Once parsed, you can apply custom logic, set field values, or trigger other actions based on the parameters.

#### Example:
Let’s say you want to pass multiple fields and their values in a more structured or encoded way:

```plaintext
https://yourorg.crm.dynamics.com/main.aspx?etn=contact&pagetype=entityrecord&extraqs=myCustomData=%7B%22firstname%22%3A%22John%22%2C%22lastname%22%3A%22Doe%22%2C%22address%22%3A%221234%20Main%20St%22%7D
```

- `myCustomData` contains a JSON string with the fields `firstname`, `lastname`, and `address`.
- On the form’s `OnLoad` event, you use JavaScript to decode the JSON string and then populate the form fields.

#### JavaScript Example:

```javascript
function onLoad(executionContext) {
    var formContext = executionContext.getFormContext();
    
    // Get the full URL of the page
    var url = window.location.href;
    
    // Use a helper function to parse URL parameters
    var params = getUrlParams(url);
    
    if (params.myCustomData) {
        // Parse the JSON string
        var customData = JSON.parse(decodeURIComponent(params.myCustomData));
        
        // Set field values based on the custom data
        if (customData.firstname) {
            formContext.getAttribute("firstname").setValue(customData.firstname);
        }
        if (customData.lastname) {
            formContext.getAttribute("lastname").setValue(customData.lastname);
        }
        if (customData.address) {
            formContext.getAttribute("address1_line1").setValue(customData.address);
        }
    }
}

// Helper function to parse URL query parameters
function getUrlParams(url) {
    var params = {};
    var parser = document.createElement('a');
    parser.href = url;
    var query = parser.search.substring(1);
    var vars = query.split('&');
    for (var i = 0; i < vars.length; i++) {
        var pair = vars[i].split('=');
        params[decodeURIComponent(pair[0])] = decodeURIComponent(pair[1]);
    }
    return params;
}
```

### Key Differences:
- **Simple URL Parameters**: Easier to use for basic cases. Suitable for simple field-value mapping.
- **Custom Object & JavaScript**: More flexible and powerful. Useful when you need to handle complex data, conditional logic, or custom validation.

### Use Cases:
- **Approach 1** is great for simple cases like pre-filling form fields based on URL clicks from emails or links.
- **Approach 2** is useful when dealing with more complex scenarios, such as passing multiple parameters or encoded data between forms, or when you need to do additional logic or validation.

Let me know if you need further examples or a deep dive into any specific part!
