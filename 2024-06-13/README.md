## Java Class Transcript Summary (2024-06-13)

### Concepts Taught and Explanations

1. **Live Coding for API Module**:
   - **Expense Tracker Application**: Continued development of the expense tracker application, focusing on implementing the remaining controller methods and starting the Thymeleaf templates.
   - **Controller Layer**: Creation of `ExpenseController` methods for listing, adding, updating, and deleting expenses. Emphasis on using the `Principal` interface to identify the currently logged-in user.
   - **Thymeleaf Templates**: Detailed explanation and live coding of the login page using Thymeleaf and Bootstrap for styling.

2. **Transactional Management**:
   - **Transactional Annotation**: Emphasis on using `@Transactional` in the service layer for methods that modify data, ensuring data integrity and rollback mechanisms.

3. **Spring Security**:
   - **Security Configuration**: Review of the `SecurityConfig` class to handle security settings, including user authentication, authorization, and session management.
   - **UserDetailsService**: Explanation of the importance of implementing `UserDetailsService` for custom user authentication.
   - **Security Filters**: Configuration of security filters for handling login, logout, and access control to specific endpoints.

4. **Dependency Injection**:
   - **Autowired Annotation**: Detailed discussion on how `@Autowired` works and the importance of annotating classes with `@Service`, `@Repository`, and `@Bean` to ensure they are recognized as beans in the Spring container.

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

5. **Question**: How does Spring recognize a class as a bean?
   - **Answer**: Spring recognizes a class as a bean if it is annotated with `@Service`, `@Repository`, or `@Bean`. These annotations ensure that instances of the class are added to the Spring container and can be injected as dependencies using `@Autowired`.

6. **Question**: Why do we use the `Principal` interface in the controller?
   - **Answer**: The `Principal` interface is used to identify the currently logged-in user, allowing the application to perform actions based on the user's identity and permissions.

### Live Coding Examples

1. **ExpenseController**:
   ```java
   @Controller
   @RequestMapping("/expenses")
   public class ExpenseController {
       @Autowired
       private ExpenseService expenseService;
       @Autowired
       private UserService userService;

       @GetMapping
       public String listExpenses(Model model, Principal principal) {
           User user = userService.findByUsername(principal.getName());
           List<Expense> expenses = expenseService.findAllExpensesByUser(user.getId());
           model.addAttribute("expenses", expenses);
           return "expenses";
       }

       @GetMapping("/add")
       public String showAddExpenseForm(Model model) {
           model.addAttribute("expense", new Expense());
           return "addExpense";
       }

       @PostMapping("/add")
       public String addExpense(@ModelAttribute Expense expense, Principal principal) {
           User user = userService.findByUsername(principal.getName());
           expense.setUser(user);
           expenseService.saveExpense(expense);
           return "redirect:/expenses";
       }

       @PostMapping("/updateAmount")
       public String updateExpenseAmount(@RequestParam double amount, @RequestParam Long id) {
           expenseService.updateExpenseAmount(amount, id);
           return "redirect:/expenses";
       }

       @PostMapping("/deleteByCategory")
       public String deleteExpenseByCategory(@RequestParam String category) {
           expenseService.deleteExpenseByCategory(category);
           return "redirect:/expenses";
       }

       @PostMapping("/updateDescription")
       public String updateExpenseDescription(@RequestParam String description, @RequestParam Long id) {
           expenseService.updateExpenseDescription(description, id);
           return "redirect:/expenses";
       }

       @PostMapping("/updateCategory")
       public String updateExpenseCategory(@RequestParam String category, @RequestParam Long id) {
           expenseService.updateExpenseCategory(category, id);
           return "redirect:/expenses";
       }

       @PostMapping("/deleteByDateRange")
       public String deleteExpenseByDateRange(@RequestParam LocalDate startDate, @RequestParam LocalDate endDate, Principal principal) {
           User user = userService.findByUsername(principal.getName());
           expenseService.deleteExpenseByDateRangeAndUser(startDate, endDate, user.getId());
           return "redirect:/expenses";
       }

       @PostMapping("/updateDate")
       public String updateExpenseDate(@RequestParam LocalDate date, @RequestParam Long id) {
           expenseService.updateExpenseDate(date, id);
           return "redirect:/expenses";
       }

       @PostMapping("/deleteByUser")
       public String deleteExpenseByUser(@RequestParam Long userId) {
           expenseService.deleteExpenseByUser(userId);
           return "redirect:/expenses";
       }

       @PostMapping("/updateMultiple")
       public String updateMultipleExpenses(@RequestParam double amount, @RequestParam List<Long> ids) {
           expenseService.updateMultipleExpenses(amount, ids);
           return "redirect:/expenses";
       }

       @PostMapping("/deleteById")
       public String deleteExpenseById(@RequestParam Long id) {
           expenseService.deleteExpenseById(id);
           return "redirect:/expenses";
       }
   }
   ```

2. **Login Page (Thymeleaf Template)**:
   ```html
   <!DOCTYPE html>
   <html xmlns:th="http://www.thymeleaf.org" lang="en">
   <head>
       <meta charset="UTF-8">
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/5.3.0/css/bootstrap.min.css">
       <title>Login</title>
   </head>
   <body>
       <div class="container">
           <div class="row justify-content-center">
               <div class="col-md-6">
                   <h2 class="text-center mt-5">Login</h2>
                   <form th:action="@{/login}" method="post">
                       <div class="mb-3">
                           <label for="username" class="form-label">Username</label>
                           <input type="text" class="form-control" id="username" name="username" placeholder="Please enter your username" required>
                       </div>
                       <div class="mb-3">
                           <label for="password" class="form-label">Password</label>
                           <input type="password" class="form-control" id="password" name="password" placeholder="Please enter your password" required>
                       </div>
                       <div th:if="${param.error}" class="alert alert-danger" role="alert">
                           Invalid credentials
                       </div>
                       <button type="submit" class="btn btn-primary w-100">Login</button>
                       <div class="text-center mt-3">
                           <a th:href="@{/signup}">Don't have an account? Sign up</a>
                       </div>
                   </form>
               </div>
           </div>
       </div>
   </body>
   </html>
   ```

### Announcements

1. **Break Time**: A 15-minute break was announced during the session.
2. **Lunch Break**: A lunch break was announced, and students were instructed to return after the break.
3. **Task Assignment**: Students were assigned a task to implement the principle interface in the online banking system, focusing on updating the `AccountController` to use the `Principal` interface.
4. **Resource Sharing**: The instructor shared a GitHub repository containing the transcript and code examples for reference.

### Conclusion

The session covered essential concepts of Spring Boot, JPA, and Hibernate, focusing on building a robust API module. Students were given practical tasks to reinforce their learning and were encouraged to ask questions and participate actively. The session concluded with a reminder to review the shared resources and complete the assigned tasks.
