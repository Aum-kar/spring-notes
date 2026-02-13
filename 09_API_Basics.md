# Accepting Requests

Accept data from different parts of request

```java
@RestController
class RootController {

    @GetMapping(path = "/getparamaters", produces = "text/plain")
    public String rootGetMapping(
            @RequestParam("username") String username,
            @RequestParam("password") String password
    ) {
        /*
        GET http://localhost:8080/getparamaters?username=Omkar&password=1234
         */
        return "Username is: " + username + "\nPassword: " + password;
    }

    @GetMapping("/modelAttribute")
    public String nodeModelAttribute(@ModelAttribute User user) {
        /*
        GET http://localhost:8080/modelAttribute?username=omkar&password=12345
         */
        return user.toString();
    }

    @PostMapping("/getJSONData")
    public String nodeJSONData(@RequestBody User user) {
        /*
        REQUEST
        POST http://localhost:8080/getJSONData
        Content-Type: application/json

        JSON payload:
        {
        	"username": "Omkar",
	        "password": "1234"
        }
         */
        return user.toString();
    }

    @GetMapping("/findBookByName/{bookname}")
    public String getBook(@PathVariable String bookname) {
        /*
        GET http://localhost:8080/findBookByName/omkar
         */
        return bookname;
    }

}
```

What need to be learned:
What’s missing from API basics:

@PutMapping

@DeleteMapping

ResponseEntity

Custom HTTP status codes

Validation (@Valid)

Exception handling

Headers (@RequestHeader)

Consumes attribute

Content negotiation deeper understanding

Once you finish those → Controller layer = complete.
