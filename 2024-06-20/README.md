### Java Class Transcript Summary (2024-06-20)

#### Concepts Taught:
1. **Password Reset Mechanism**:
   - Users can reset their passwords by answering security questions.
   - Systems may maintain a list of previous passwords to prevent reuse.

2. **Role-Based Access Control (RBAC)**:
   - Implementation of roles such as `USER` and `ADMIN`.
   - Use of annotations like `@ElementCollection` and `@CollectionTable` to manage roles in the database.
   - Fetch types (`EAGER` and `LAZY`) and their implications.

3. **Spring Boot Dependencies**:
   - Importance of dependencies like `spring-boot-starter-validation`, `spring-boot-starter-web`, `spring-boot-starter-security`, and `spring-boot-starter-mail`.
   - Explanation of Hibernate Validator for data validation.

4. **RestTemplate and RESTful APIs**:
   - Use of `RestTemplate` for making HTTP requests.
   - Explanation of REST principles: Statelessness, Client-Server Architecture, Cacheability, Uniform Interface, and Layered System.

#### Questions and Answers:
1. **Q: Can a user have multiple roles?**
   - A: Currently, a user can have only one role, but the system can be extended to support multiple roles in the future.

2. **Q: What is the purpose of the `@ElementCollection` annotation?**
   - A: It is used to define a collection of basic types (e.g., roles) associated with an entity (e.g., user).

3. **Q: How does the system handle account locking?**
   - A: The account is locked if the user's email is not verified.

4. **Q: What is the significance of the `RestTemplate` class?**
   - A: It is used to perform synchronous HTTP requests, adhering to REST principles.

#### Live Coding Examples:
1. **User Entity with Roles**:
   ```java
   @Entity
   public class User {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
       private String email;
       private String password;
       private boolean verified;

       @ElementCollection(fetch = FetchType.EAGER)
       @CollectionTable(name = "user_roles", joinColumns = @JoinColumn(name = "user_id"))
       @Column(name = "role")
       private Set<String> roles = new HashSet<>();

       // Getters and Setters
   }
   ```

2. **User Service for Role Assignment**:
   ```java
   @Service
   public class UserService {
       @Autowired
       private UserRepository userRepository;

       public void saveUser(User user) {
           Set<String> roles = new HashSet<>();
           roles.add("USER");
           user.setRoles(roles);
           userRepository.save(user);
       }
   }
   ```

3. **Security Configuration**:
   ```java
   @Configuration
   @EnableWebSecurity
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http.authorizeRequests()
               .antMatchers("/currency-converter").hasRole("ADMIN")
               .anyRequest().authenticated()
               .and()
               .formLogin().permitAll()
               .and()
               .logout().permitAll();
       }
   }
   ```

4. **RestTemplate Usage**:
   ```java
   @Service
   public class CurrencyConverterService {
       @Value("${rate.api.key}")
       private String apiKey;

       private final RestTemplate restTemplate = new RestTemplate();

       public CurrencyConversionResponse convertCurrency(String from, String to, double amount) {
           String url = "https://api.exchangerate-api.com/v4/latest/" + from + "?apikey=" + apiKey;
           ResponseEntity<CurrencyConversionResponse> response = restTemplate.getForEntity(url, CurrencyConversionResponse.class);
           return response.getBody();
       }
   }
   ```

#### Announcements:
1. **Final Project Presentation**:
   - Date: 25th June, 10 AM to 12 PM.
   - Platform: Zoom.
   - Attendance is mandatory, and it will be recorded.

2. **Career Fair**:
   - Date: 27th June.
   - Recommended for networking with potential employers.

3. **Transcript and Code Repository**:
   - Transcripts and code snippets will be available on the GitHub repository named "transcript".
   - The repository will include a summary of the day's lessons and live coding examples.

#### Additional Notes:
- **Camera Usage**: Encouraged for a better class environment.
- **Class Locations**: DCI campuses in Berlin, Hamburg, Dusseldorf, and a small office in Leipzig.
- **DCI History**: Initially focused on integrating refugees into the job market, now expanded to a broader audience.

This summary encapsulates the key points, questions, live coding examples, and announcements from the Java class held on 2024-06-20.
