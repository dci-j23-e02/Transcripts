## Java Class Transcript Summary (2024-06-11)

### Concepts Taught and Explanations

1. **Live Coding for API Module**:
   - **Expense Tracker Application**: Continued development of the expense tracker application, focusing on implementing the service and controller layers.
   - **Service Layer**: Creation of `UserService` and `ExpenseService` classes, including dependency injection, transactional management, and service methods.
   - **Controller Layer**: Creation of `UserController` and `ExpenseController` classes, including endpoint mappings for login, logout, and sign-up functionalities.

2. **Transactional Management**:
   - **Transactional Annotation**: Differences between `@Transactional` from Spring Framework and Jakarta. The Jakarta `@Transactional` is used in the service layer for better performance and standardization in Java EE environments, while the Spring `@Transactional` is more compatible with Spring features like Spring Data JPA.
   - **Best Practices**: Emphasis on using `@Transactional` in the service layer rather than the repository layer for better transaction management.

3. **Spring Security**:
   - **UserDetailsService**: Implementation of `UserDetailsService` to handle user authentication securely.
   - **Login and Logout**: Explanation of why login does not require a `@PostMapping` due to Spring Security handling the authentication process.

4. **Dependency Injection**:
   - **Autowired Annotation**: Use of `@Autowired` for injecting dependencies like `UserService`, `ExpenseService`, and `PasswordEncoder`.

5. **Form Handling with Thymeleaf**:
   - **Model Attribute**: Use of `Model` to pass an empty user object to the sign-up form, which will be populated with user input.

### Questions and Answers

1. **Question**: Why do we need to use `@Transactional` in the service layer?
   - **Answer**: Using `@Transactional` in the service layer ensures that multiple repository operations are managed within a single transaction, providing better transaction management and data integrity.

2. **Question**: Why don't we need a `@PostMapping` for login?
   - **Answer**: The authentication process is handled by Spring Security, which automatically processes the login form submission. Therefore, a `@PostMapping` is not required in the controller.

3. **Question**: What is the difference between `@Transactional` from Spring and Jakarta?
   - **Answer**: The Jakarta `@Transactional` is used in Java EE environments and is more suitable for the service layer, while the Spring `@Transactional` is more compatible with Spring features like Spring Data JPA and is typically used in the repository layer.

4. **Question**: Why do we use `UserDetailsService`?
   - **Answer**: `UserDetailsService` is a core component in Spring Security used to retrieve user-related data securely, primarily for authentication purposes.

### Live Coding Examples

1. **UserService**:
   ```java
   @Service
   public class UserService implements UserDetailsService {
       @Autowired
       private UserRepository userRepository;
       @Autowired
       private PasswordEncoder passwordEncoder;

       @Override
       public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
           User user = userRepository.findByUsername(username);
           if (user == null) {
               throw new UsernameNotFoundException("User not found");
           }
           return User.withUsername(user.getUsername())
                      .password(user.getPassword())
                      .authorities(Collections.emptyList())
                      .build();
       }

       @Transactional
       public void saveUser(User user) {
           user.setPassword(passwordEncoder.encode(user.getPassword()));
           userRepository.save(user);
       }

       public User findByUsername(String username) {
           return userRepository.findByUsername(username);
       }
   }
   ```

2. **UserController**:
   ```java
   @Controller
   public class UserController {
       @Autowired
       private UserService userService;

       @GetMapping("/login")
       public String loginForm(Model model) {
           model.addAttribute("error", "Invalid credentials");
           return "login";
       }

       @GetMapping("/logout")
       public String logout() {
           return "redirect:/login?logout";
       }

       @GetMapping("/signup")
       public String showSignUpForm(Model model) {
           model.addAttribute("user", new User());
           return "signup";
       }

       @PostMapping("/signup")
       public String signUp(@ModelAttribute User user) {
           userService.saveUser(user);
           return "redirect:/login";
       }
   }
   ```

3. **ExpenseService**:
   ```java
   @Service
   public class ExpenseService {
       @Autowired
       private ExpenseRepository expenseRepository;

       @Transactional
       public Expense saveExpense(Expense expense) {
           if (expense == null) {
               throw new IllegalArgumentException("Invalid expense object");
           }
           return expenseRepository.save(expense);
       }

       public List<Expense> findAllExpenses() {
           return expenseRepository.findAll();
       }
   }
   ```

### Announcements

1. **Break Time**: A 15-minute break was announced during the session.
2. **Lunch Break**: A lunch break was announced, and students were instructed to return after the break.
3. **Task Assignment**: Students were assigned a task to implement the remaining custom operations in the `ExpenseService` class.
4. **Resource Sharing**: The instructor shared a GitHub repository containing the transcript and code examples for reference.

### Conclusion

The session covered essential concepts of Spring Boot, JPA, and Hibernate, focusing on building a robust API module. Students were given practical tasks to reinforce their learning and were encouraged to ask questions and participate actively. The session concluded with a reminder to review the shared resources and complete the assigned tasks.
