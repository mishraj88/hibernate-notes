Here is a clean, ready-to-save **Revision Cheat Sheet** for JPA/Hibernate Relationships, Embeddables, JoinColumn, JoinTable & mappedBy – perfect for quick revision before interviews or exams.

```markdown
# JPA / Hibernate Revision Cheat Sheet  
(Spring Boot + Jakarta Persistence)

## 1. @Embeddable & @Embedded – Rule of Thumb
| Concept              | Meaning                                                                 | Has own table? | Has @Id? |
|----------------------|--------------------------------------------------------------------------|----------------|----------|
| `@Embeddable`        | Reusable group of columns (Address, JobInfo, ContactInfo, etc.)         | No             | No       |
| `@Embedded`          | Used in @Entity to include the embeddable                                | –              | –        |

**Use when** → You want to reuse a group of fields + relationships in multiple entities  
**Never create** a separate table for Address/JobInfo → just columns!

**Example**
```java
@Embeddable
class Address { String city; String zip; }

@Entity
class Employee {
    @Id Long id;
    @Embedded Address address;   // → city, zip columns in Employee table
}
```

## 2. @JoinColumn vs @JoinTable vs mappedBy

| Annotation     | Used On             | Creates Foreign Key In      | Who OWNS the relationship?          |
|----------------|---------------------|-----------------------------|-------------------------------------|
| `@JoinColumn`  | `@ManyToOne`        | **My table**                | This side (owning side)             |
| `@JoinTable`   | `@ManyToMany`       | **New separate table**      | Neither side (join table owns)      |
| `mappedBy`     | `@OneToMany` / `@OneToOne` | Other table           | The **other side** owns it          |

**Golden Rule**  
Only **one side** can own the relationship (have the foreign key).

## 3. mappedBy – What to write?

`mappedBy = "name_of_the_field_in_the_owning_side"`

| Case                                | mappedBy value example                          |
|-------------------------------------|--------------------------------------------------|
| Normal                              | `mappedBy = "department"`                        |
| Inside @Embeddable                  | `mappedBy = "jobInfo.manager"`  (dot notation)   |
| Self-reference                      | `mappedBy = "manager"`                           |

## 4. Unidirectional vs Bidirectional

| Type            | Example                                 | Recommended?       | When to use bidirectional?                     |
|-----------------|-----------------------------------------|--------------------|------------------------------------------------|
| Unidirectional  | Employee → Department (only)            | Yes (90% cases)    | Almost always sufficient                      |
| Bidirectional   | Employee ↔ Department                   | Only if needed     | Parent really needs list of children          |

**Rule**: Make bidirectional **only if** the parent entity needs to access children easily.

## 5. Self-Referencing Relationship (Very Important)

```java
@Entity
public class Employee {
    @Id private Long id;
    private String name;

    @ManyToOne
    @JoinColumn(name = "manager_id")
    private Employee manager;                // My boss

    @OneToMany(mappedBy = "manager")
    private List<Employee> team = new ArrayList<>(); // My team
}
```

Table → `employee (id, name, manager_id)`

## 6. @ManyToOne inside @Embeddable → Totally Allowed!

```java
@Embeddable
class JobInfo {
    String role;
    @ManyToOne Employee programManager;   // Yes! Relationship inside embeddable
}

@Entity
class Employee {
    @Id Long id;
    @Embedded JobInfo jobInfo;
}
```

→ Foreign key `jobInfo_programManager_id` goes into Employee table  
→ In reverse mapping: `mappedBy = "jobInfo.programManager"`

## 7. Quick Decision Table

| You Want                                   | Use This Combination                                      |
|--------------------------------------------|------------------------------------------------------------|
| Foreign key in my table                    | `@ManyToOne` + `@JoinColumn`                               |
| No FK in my table                          | `@OneToMany(mappedBy = "...")`                             |
| Many-to-Many                               | `@ManyToMany` + `@JoinTable`                               |
| Reusable fields + relationships            | `@Embeddable` + `@Embedded`                                |
| Parent needs children                      | Make bidirectional                                         |
| Only child needs parent                    | Unidirectional (`@ManyToOne` only) – preferred            |
| Employee → Manager (same entity)           | Self-referencing `@ManyToOne`                              |
| Relationship inside embeddable (bi-dir)    | `mappedBy = "embedField.relationField"` (dot notation)     |

## 8. One-Liner Rules to Remember

1. Only **one side** owns the foreign key.
2. Owning side → has `@JoinColumn`
3. Non-owning side → has `mappedBy`
4. `@Embeddable` never has `@Id` or own table
5. Use embeddable for reuse & clean code
6. Bidirectional = convenience, not mandatory
7. Self-reference → `@ManyToOne` pointing to same entity
8. mappedBy value = exact field name (use dot if inside embeddable)

**Save this file as** → `jpa-revision-cheat-sheet.md`  
You’re now ready for any JPA interview question! 🚀
```

Copy-paste this into a file and keep it forever — it covers 95% of real-world JPA mapping questions!  
Let me know if you want the PDF or Notion version too!
