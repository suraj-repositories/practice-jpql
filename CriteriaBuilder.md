# CriteriaBuilder

1. first create the entity to work with
```java
@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(name = "product_name")
    private String productName;

    @ManyToOne
    @JoinColumn(name = "category_id")
    private Category category;

    @OneToMany(mappedBy = "product", fetch = FetchType.LAZY)
    private List<ProductImage> images;

    @OneToOne(mappedBy = "product", fetch = FetchType.LAZY)
    private ProductStock stock;

    // getters and setters
}

```

2. define an interface to create custom functions : 
```java
public interface ProductRepositoryCustom {
    List<Product> findProducts(Long categoryId, String search);
}
```

3. implement the custom method
```java
import org.springframework.stereotype.Repository;
import org.springframework.util.StringUtils;

import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import jakarta.persistence.criteria.*;
import java.util.List;

@Repository
public class ProductRepositoryCustomImpl implements ProductRepositoryCustom {

    @PersistenceContext
    private EntityManager entityManager;

    public List<Product> findProducts(Long categoryId, String search) {
        CriteriaBuilder cb = entityManager.getCriteriaBuilder();
        CriteriaQuery<Product> query = cb.createQuery(Product.class);
        Root<Product> product = query.from(Product.class);

        // Join with images and stocks
        product.fetch("images", JoinType.INNER);
        product.fetch("stock", JoinType.LEFT);

        // Build predicates
        Predicate predicate = cb.conjunction();

        if (categoryId != null) {
            predicate = cb.and(predicate, cb.equal(product.get("category").get("id"), categoryId));
        }

        if (StringUtils.hasText(search)) {
            predicate = cb.and(predicate, cb.like(product.get("productName"), "%" + search + "%"));
        }

        query.where(predicate);

        return entityManager.createQuery(query).getResultList();
    }
}
```

4. use in service
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<Product> getProducts(Long categoryId, String search) {
        return productRepository.findProducts(categoryId, search);
    }
}
```

### More useful functions of criteriaBuilder
