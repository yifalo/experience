---
title: 'Java Stream流多字段排序'
categories:
  - technology
  - software
  - backend
  - java
---

### 有以下需求：优惠券分为可用和不可用，要求可用的排前面，不可用的排后面。同样可用的优惠券则按照金额由大到小排序。

**CouponRecordAvailableDto 类**

```java
@Data
public class CouponRecordAvailableDto {

    private Boolean isAvailable;

    private Long amount;
}
```

---实现方式---
```java
available.stream()
        .sorted(
            Comparator.comparing(CouponRecordAvailableDto::getIsAvailable)
            .thenComparing(CouponRecordAvailableDto::getAmount).reversed()
        )
        .collect(Collectors.toList())
```

