# Intercept a HTTP request or response

```javascript
document.addEventListener('DOMContentLoaded', function() {
  var originalOpen = XMLHttpRequest.prototype.open;
  var originalSend = XMLHttpRequest.prototype.send;

  XMLHttpRequest.prototype.open = function(method, url) {
    console.log("Intercepted HTTP request: " + method + " " + url);
    originalOpen.apply(this, arguments);
  };

  XMLHttpRequest.prototype.send = function() {
    var self = this;

    // Hooking the onreadystatechange event to intercept the response
    this.onreadystatechange = function() {
      if (self.readyState === 4) {
        console.log("Intercepted HTTP response:", JSON.parse(self.responseText));
      }
    };

    originalSend.apply(this, arguments);
  };

  // Example: Making a sample XMLHttpRequest
  var xhr = new XMLHttpRequest();
  xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1", true);
  xhr.send();
});

//Output
"Intercepted HTTP request: GET https://jsonplaceholder.typicode.com/todos/1"
"Intercepted HTTP response:", {
  completed: false,
  id: 1,
  title: "delectus aut autem",
  userId: 1
}
```
