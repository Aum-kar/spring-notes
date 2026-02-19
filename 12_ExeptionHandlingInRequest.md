# Exception Handling in Requests

## Validating Request

```txt
REQUEST:    Client Request
\/
CONTROLLER: Controller listens request
\/
VALIDATE:   Validate requests using @Valid while consuming in controller class
\/
SERVICE:    Business logic in Services
\/
ENTITY:     Validate, manipulate data using Entity class methods in service class
\/
REPOSITORY: Save data to database
\/
DATABASE
```

### Example

Suppose an example of saving product to database
It accepts requests in addProduct() controller
Validates incoming request using spring `@Valid` annotations defined in `Product Entity`

```java
@PostMapping
public ResponseEntity<ProductResponse> addProduct(@Valid @RequestBody ProductRequest productRequest) {
    ProductResponse productResponse = productService.saveProducts(productRequest);
    return ResponseEntity.status(201).body(productResponse);
}
```

- In above example, we have added @Valid tag before `productRequest`.
- `productRequest` is a java class that maps and validates the request for `addProduct` controller.
- In incoming request, we don't want 
  - proudct names to  be empty,
  - product price not less than 0, and
  - product stock quantity should be positive.
- So, we validate these details in `ProductRequest` using `jakarta.validation.constraints` package.

```java
import jakarta.validation.constraints.*;

public class ProductRequest {

    @NotBlank
    private String productName;

    @DecimalMin(value = "0.0", message = "Product price should be more than 0")
    private BigDecimal price;

    @Positive
    private Long stockQuantity;
}
```

## Reference Packages

```java
import jakarta.validation.constraints.**;
```
