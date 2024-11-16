# practice-jpql
JPQL Practice Queries to practice with

# JPQL Queries

1. Select All
```java
@Query("SELECT p FROM Product p")
List<Product> findAllProducts();
```

2. Find By Id
```java
@Query("SELECT p FROM Product p WHERE p.id = :id")
Product findProductById(@Param("id") Long id);
```

3. Find All where amount is given
```java
@Query("SELECT p FROM Product p WHERE p.amount = :amount")
List<Product> findAllByAmount(@Param("amount") BigDecimal amount);
```

4. Find All where amount is greater than the given
```java
@Query("SELECT p FROM Product p WHERE p.amount > :amount")
List<Product> findAllByAmountGreaterThan(@Param("amount") BigDecimal amount);
```

5. Update Column
```java
@Modifying
@Transaction
@Query("UPDATE Product p SET p.price = :price WHERE p.id = :productId")
int updateProductPrice(@Param("productId") Long ProductId, @Param("Price") BigDecimal price)
```

6. Delete Row
```java
@Modifying
@Transactional
@Query("DELETE FROM Product p WHERE p.id = :productId")
int deleteProductById(@Param("productId") Long productId);
```

7. Insertion
- won't work directly because JPQL doesn't support INSERT statements.
- JPQL is designed for working with entities and not raw table operations.
- Instead, you need to use native queries for inserting data.
```java
@Modifying
@Transactional
@Query(value = "INSERT INTO product (name, price) VALUES (:name, :price)", nativeQuery = true)
int insertProductNative(@Param("name") String name, @Param("price") BigDecimal price);
```

8. Using Inner Join
```java
@Query("SELECT p FROM Product p INNER JOIN p.category c WHERE c.name = :categoryName")
List<Product> findProductsByCategoryName(@Param("categoryName") String categoryName);
```

9. Find category with search and also get with parent category
```java
@Query("SELECT c FROM Category c " +
       "WHERE (:search IS NULL OR LOWER(c.name) LIKE LOWER(CONCAT('%', :search, '%'))) " +
       "AND (:parentCategory IS NULL OR c.parentCategory = :parentCategory)")
Page<Category> findByNameContainingAndParentCategory(String search, Category parentCategory, Pageable pageable);
```

