## Java Class Transcript Summary (2024-06-19)

### Concepts Taught and Explanations

1. **Live Coding for API Module**:
   - **Expense Tracker Application**: Continued development of the expense tracker application, focusing on handling email verification failures and ensuring only verified users can log in.
   - **Service Layer**: Updates to `UserService` to handle email verification failures, including catching `MailException` and returning a boolean value to indicate success or failure.
   - **Controller Layer**: Updates to `UserController` to handle feedback to users when email verification fails.
   - **Security Configuration**: Ensuring that only users with verified emails can log in by updating the `SecurityConfig` and `UserService`.

2. **Transactional Management**:
   - **Transactional Annotation**: Emphasis on using `@Transactional` in the service layer for methods that modify data, ensuring data integrity and rollback mechanisms.

3. **Spring Security**:
   - **Security Configuration**: Updates to `SecurityConfig` to allow the verification endpoint to be accessed without authentication.
   - **UserDetailsService**: Explanation of the importance of implementing `UserDetailsService` for custom user authentication.
   - **Security Filters**: Configuration of security filters for handling login, logout, and access control to specific endpoints.

4. **Dependency Injection**:
   - **Autowired Annotation**: Detailed discussion on how `@Autowired` works and the importance of annotating classes with `@Service`, `@Repository`, and `@Bean` to ensure they are recognized as beans in the Spring container.

5. **Email Verification**:
   - **JavaMailSender**: Use of `JavaMailSender` to send verification emails to users.
   - **UUID**: Use of `UUID` to generate unique verification tokens.
   - **App Password**: Configuration of app passwords in Gmail to allow the application to send emails.

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

7. **Question**: How do we handle email verification in the application?
   - **Answer**: We handle email verification by creating a `VerificationToken` entity, sending a verification email with a unique token, and verifying the token when the user clicks the verification link.

8. **Question**: How do we ensure that only verified users can log in?
   - **Answer**: We ensure that only verified users can log in by updating the `UserService` to check the `verified` attribute of the user and throwing an exception if the user is not verified. This is handled in the `loadUserByUsername` method.

### Live Coding Examples

1. **UserService**:
   ```java
   @Service
   public class UserService implements UserDetailsService {
       @Autowired
       private UserRepository userRepository;
       @Autowired
       private PasswordEncoder passwordEncoder;
       @Autowired
       private JavaMailSender mailSender;
       @Autowired
       private VerificationTokenRepository tokenRepository;

       @Transactional
       public boolean saveUser(User user) {
           user.setPassword(passwordEncoder.encode(user.getPassword()));
           userRepository.save(user);
           try {
               sendVerificationEmail(user);
               return true;
           } catch (MailException e) {
               System.out.println("Failed to send verification email: " + e.getMessage());
               return false;
           }
       }

       public void sendVerificationEmail(User user) throws MailException {
           String token = UUID.randomUUID().toString();
           createVerificationToken(user, token);

           String recipientAddress = user.getEmail();
           String subject = "Email Verification";
           String confirmationUrl = "http://localhost:8085/verify?token=" + token;
           String message = "Please click the link below to verify your email address:\n" + confirmationUrl;

           SimpleMailMessage email = new SimpleMailMessage();
           email.setTo(recipientAddress);
           email.setSubject(subject);
           email.setText(message);
           mailSender.send(email);
       }

       public void createVerificationToken(User user, String token) {
           VerificationToken verificationToken = new VerificationToken();
           verificationToken.setToken(token);
           verificationToken.setUser(user);
           verificationToken.setExpiryDate(LocalDateTime.now().plusDays(1));
           tokenRepository.save(verificationToken);
       }

       public VerificationToken getVerificationToken(String token) {
           return tokenRepository.findByToken(token);
       }

       public void verifyUser(String token) {
           VerificationToken verificationToken = getVerificationToken(token);
           if (verificationToken != null && verificationToken.getExpiryDate().isAfter(LocalDateTime.now())) {
               User user = verificationToken.getUser();
               user.setVerified(true);
               userRepository.save(user);
               tokenRepository.delete(verificationToken);
           }
       }

       @Override
       public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
           User user = userRepository.findByUsername(username);
           if (user == null) {
               throw new UsernameNotFoundException("User not found");
           }
           if (!user.isVerified()) {
               throw new UsernameNotFoundException("User email is not verified");
           }
           return User.withUsername(user.getUsername())
                      .password(user.getPassword())
                      .authorities(Collections.emptyList())
                      .build();
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
           model.addAttribute("error", "Invalid credentials or email is not verified");
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
       public String signUp(@ModelAttribute User user, Model model) {
           boolean userSaved = userService.saveUser(user);
           if (!userSaved) {
               model.addAttribute("errorMessage", "Failed to send verification email. Please check your email address.");
               return "signup";
           }
           return "redirect:/login";
       }

       @GetMapping("/verify")
       public String verifyUser(@RequestParam("token") String token) {
           userService.verifyUser(token);
           return "redirect:/login?verified=true";
       }
   }
   ```

3. **Login Page (Thymeleaf Template)**:
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
                           Invalid credentials or email is not verified
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
3. **Task Assignment**: Students were assigned a task to implement email verification in the seasonal fruits exercise and the online banking system, focusing on updating the `AccountController` to handle email verification.
4. **Resource Sharing**: The instructor shared a GitHub repository containing the transcript and code examples for reference.

### Conclusion

The session covered essential concepts of Spring Boot, JPA, and Hibernate, focusing on building a robust API module with email verification. Students were given practical tasks to reinforce their learning and were encouraged to ask questions and participate actively. The session concluded with a reminder to review the shared resources and complete the assigned tasks.
