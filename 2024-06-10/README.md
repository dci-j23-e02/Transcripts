## Java Class Transcript Summary (2024-06-10)

### Concepts Taught and Explanations

1. **Live Coding for API Module**:
   - **Expense Tracker Application**: The class focused on implementing an API module using an expense tracker application. The students were instructed to implement similar functionalities in an online banking system.
   - **Controller Layer**: The `AccountController` and `UserController` were discussed, including dependency injection, endpoint mappings, and handling user authentication using Spring Security.
   - **Model Layer**: The `User` and `Account` entities were explained, including annotations for JPA, relationships (one-to-many, many-to-one), and the importance of unique constraints.
   - **Repository Layer**: The creation of repositories for `User` and `Expense` entities, including custom query methods using JPQL.
   - **Service Layer**: The importance of transactional annotations in service methods to ensure data integrity and rollback mechanisms.

2. **Spring Security**:
   - **Authentication and Authorization**: Use of `@PreAuthorize` and `@Secured` annotations to restrict access to certain endpoints.
   - **Principle Annotation**: Ensuring that only authenticated users can perform certain actions like opening a new account.

3. **JPA and Hibernate**:
   - **Entity Relationships**: Detailed explanation of one-to-many and many-to-one relationships, including the use of `@JoinColumn` and `@MappedBy`.
   - **Lazy vs Eager Loading**: The impact of fetch types on performance and when to use each.

4. **Custom Queries in Repositories**:
   - **JPQL**: Writing custom queries for updating and deleting records based on various conditions.
   - **Transactional Management**: The necessity of marking custom query methods with `@Transactional` and `@Modifying` annotations.

### Questions and Answers

1. **Question**: Why do we need to use `@Transactional` in custom query methods?
   - **Answer**: While JPA provides default transaction management for CRUD operations, custom query methods need explicit transactional management to ensure data integrity and rollback mechanisms.

2. **Question**: Can we use only the user ID instead of the entire user object in queries?
   - **Answer**: Yes, using only the user ID can improve performance. However, providing the entire user object can be beneficial for maintaining transactional scopes and ensuring data integrity.

3. **Question**: What is the difference between `@Transactional` from Spring and Jakarta?
   - **Answer**: The `@Transactional` annotation from Spring is used for managing transactions in the service layer, while the one from Jakarta is used in the repository layer. Each has its specific use case and scope.

### Live Coding Examples

1. **AccountController**:
   ```java
   @Controller
   public class AccountController {
       @Autowired
       private BankingService bankingService;
       @Autowired
       private UserService userService;

       @GetMapping("/openAccount")
       public String showOpenAccountForm() {
           return "openAccount";
       }

       @PostMapping("/openAccount")
       @PreAuthorize("isAuthenticated()")
       public String openAccount(@RequestParam String accountType, @RequestParam double initialDeposit, Principal principal) {
           User user = userService.findByUsername(principal.getName());
           bankingService.openNewAccount(user, accountType, initialDeposit);
           return "redirect:/accountSuccess";
       }

       @GetMapping("/accountSuccess")
       public String showAccountSuccess() {
           return "accountSuccess";
       }
   }
   ```

2. **User Entity**:
   ```java
   @Entity
   @Table(name = "users")
   public class User {
       @Id
       @GeneratedValue(strategy = GenerationType.SEQUENCE)
       private Long id;

       @Column(nullable = false, unique = true)
       private String username;

       @Column(nullable = false)
       private String password;

       @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
       private Set<Account> accounts = new HashSet<>();

       // Getters and Setters
   }
   ```

3. **ExpenseRepository**:
   ```java
   public interface ExpenseRepository extends JpaRepository<Expense, Long> {
       @Modifying
       @Transactional
       @Query("update Expense e set e.amount = ?1 where e.id = ?2")
       void updateExpenseAmount(double amount, Long id);

       @Modifying
       @Transactional
       @Query("delete from Expense e where e.category = ?1")
       void deleteExpenseByCategory(String category);
   }
   ```

### Announcements

1. **Break Time**: A 15-minute break was announced during the session.
2. **Lunch Break**: A lunch break was announced, and students were instructed to return after the break.
3. **Task Assignment**: Students were assigned a task to implement a profile entity in the online banking system, including creating a one-to-one relationship with the user entity and updating the service and controller layers accordingly.
4. **Resource Sharing**: A resource was shared in the course resources channel to help students understand the necessity of transactional annotations in custom query methods.

### Conclusion

The session covered essential concepts of Spring Boot, JPA, and Hibernate, focusing on building a robust API module. Students were given practical tasks to reinforce their learning and were encouraged to ask questions and participate actively. The session concluded with a reminder to review the shared resources and complete the assigned tasks.
