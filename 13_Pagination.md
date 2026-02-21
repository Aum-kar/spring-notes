# Pagination in Spring Boot (Spring Data JPA)

## What is Pagination?

Pagination is a way to divide large data into smaller chunks (pages).

Instead of returning all records at once, we return:

* A limited number of records
* Page information (page number, total pages, etc.)

This improves performance and makes APIs scalable.

### Why We Need Pagination

* Prevent returning thousands of records at once
* Reduce database load
* Improve response time
* Support frontend features like page navigation

## Key Concepts

### 1. `Pageable`

`Pageable` represents pagination request information.

It contains:

* Page number
* Page size
* Sorting information

Spring automatically creates a `Pageable` object from query parameters like:

```
?page=0&size=5&sort=price,desc
```

We do not create it manually.

### 2. `Page<T>`

`Page<T>` represents paginated result.

It contains:

* `content` → list of records
* `totalElements` → total number of records
* `totalPages` → total number of pages
* `number` → current page number
* `size` → page size
* `first`, `last` → navigation flags

It is more than just a list.

### 3. `@PageableDefault`

`@PageableDefault` is used to define default pagination values when client does not provide page or size.

Example:

```java
@PageableDefault(page = 0, size = 10)
```

If client calls:

```
GET /products
```

Then:

* Page = 0
* Size = 10

will be used automatically.

---

## Simple Example

### Controller

```java
@GetMapping
public Page<ProductResponse> getProducts(
        @PageableDefault(page = 0, size = 10) Pageable pageable
) {
    return productService.getProducts(pageable);
}
```

### Service

```java
public Page<ProductResponse> getProducts(Pageable pageable) {

    Page<Product> page = productRepository.findAll(pageable);

    return page.map(this::mapToResponse);
}
```

### Repository

No change required. `JpaRepository` already supports pagination:

```java
public interface ProductRepository extends JpaRepository<Product, Long> {
}
```

## Example Requests

```txt
GET /products?page=0&size=5
GET /products?sort=price,desc
GET /products?sort=price,desc&sort=stockQuantity,asc
```

## Important Rules

* `page >= 0`
* `size > 0`
* Use default values when not provided
* Return `Page<DTO>`, not entity

## Summary

* `Pageable` → input (page request information)
* `Page<T>` → output (paginated result + metadata)
* `@PageableDefault` → sets default page and size
* Pagination improves performance and scalability
* Sorting is supported automatically through query parameters
