## Java Class Transcript Summary (2024-06-18)

### Concepts Taught and Explanations

1. **Live Coding for API Module**:
   - **Expense Tracker Application**: Continued development of the expense tracker application, focusing on implementing email verification for user sign-up.
   - **Model Layer**: Creation of a `VerificationToken` entity to handle email verification tokens.
   - **Repository Layer**: Creation of a `VerificationTokenRepository` to manage verification tokens.
   - **Service Layer**: Updates to `UserService` to handle email verification, including methods to create and verify tokens, and send verification emails.
   - **Controller Layer**: Updates to `UserController` to handle the verification endpoint.

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

### Live Coding Examples

1. **VerificationToken Entity**:
   ```java
   @Entity
   @Table(name = "verification_tokens")
   public class VerificationToken {
       @Id
       @GeneratedValue(strategy = GenerationType.SEQUENCE)
       private Long id;

       @Column(nullable = false, unique = true)
       private String token;

       @OneToOne(targetEntity = User.class, fetch = FetchType.EAGER)
       @JoinColumn(nullable = false, name = "user_id")
       private User user;

       @Column(nullable = false)
       private LocalDateTime expiryDate;

       // Getters and Setters
   }
   ```

2. **UserService**:
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
       public void saveUser(User user) {
           user.setPassword(passwordEncoder.encode(user.getPassword()));
           userRepository.save(user);
           sendVerificationEmail(user);
       }

       public void sendVerificationEmail(User user) {
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
           return User.withUsername(user.getUsername())
                      .password(user.getPassword())
                      .authorities(Collections.emptyList())
                      .build();
       }
   }
   ```

3. **SecurityConfig**:
   ```java
   @Configuration
   @EnableWebSecurity
   public class SecurityConfig {
       @Bean
       public UserDetailsService userDetailsService() {
           return new UserService();
       }

       @Bean
       public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
           http
               .authorizeHttpRequests(auth -> auth
                   .requestMatchers("/signup", "/login", "/verify").permitAll()
                   .anyRequest().authenticated()
               )
               .formLogin(form -> form
                   .loginPage("/login")
                   .defaultSuccessUrl("/home", true)
                   .permitAll()
               )
               .logout(logout -> logout
                   .logoutUrl("/logout")
                   .logoutSuccessUrl("/login?logout")
                   .permitAll()
               );
           return http.build();
       }

       @Bean
       public JavaMailSender javaMailSender() {
           JavaMailSenderImpl mailSender = new JavaMailSenderImpl();
           mailSender.setHost("smtp.gmail.com");
           mailSender.setPort(587);
           mailSender.setUsername("your-email@gmail.com");
           mailSender.setPassword("your-app-password");

           Properties props = mailSender.getJavaMailProperties();
           props.put("mail.transport.protocol", "smtp");
           props.put("mail.smtp.auth", "true");
           props.put("mail.smtp.starttls.enable", "true");
           props.put("mail.debug", "true");

           return mailSender;
       }
   }
   ```

4. **UserController**:
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

       @GetMapping("/verify")
       public String verifyUser(@RequestParam("token") String token) {
           userService.verifyUser(token);
           return "redirect:/login?verified=true";
       }
   }
   ```

### Announcements

1. **Break Time**: A 15-minute break was announced during the session.
2. **Lunch Break**: A lunch break was announced, and students were instructed to return after the break.
3. **Task Assignment**: Students were assigned a task to implement email verification in the online banking system, focusing on updating the `AccountController` to handle email verification.
4. **Resource Sharing**: The instructor shared a GitHub repository containing the transcript and code examples for reference.

### Conclusion

The session covered essential concepts of Spring Boot, JPA, and Hibernate, focusing on building a robust API module with email verification. Students were given practical tasks to reinforce their learning and were encouraged to ask questions and participate actively. The session concluded with a reminder to review the shared resources and complete the assigned tasks.
