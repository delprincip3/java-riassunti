# Spring Security & JWT 

## 1. Spring Security: Fondamenti e Architettura

### 1.1 Architettura di Spring Security

Spring Security è basato su una serie di filtri servlet che intercettano le richieste HTTP prima che raggiungano i controller dell'applicazione. Questa catena di filtri gestisce diversi aspetti della sicurezza:

- **SecurityContextPersistenceFilter**: Mantiene il SecurityContext tra le richieste
- **UsernamePasswordAuthenticationFilter**: Gestisce l'autenticazione form-based
- **BasicAuthenticationFilter**: Gestisce l'autenticazione HTTP Basic
- **FilterSecurityInterceptor**: Applica le regole di autorizzazione

L'autenticazione in Spring Security è gestita attraverso l'`AuthenticationManager`, che delega la verifica delle credenziali a uno o più `AuthenticationProvider`. Il risultato è un oggetto `Authentication` contenente i dettagli dell'utente e i suoi ruoli.

### 1.2 UserDetailsService

L'interfaccia `UserDetailsService` è fondamentale per l'autenticazione:

```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

Questa interfaccia consente a Spring Security di caricare i dettagli dell'utente dal database o da un'altra fonte. L'implementazione tipica è simile a:

```java
@Service
public class UserDetailsServiceImpl implements UserDetailsService {
    @Autowired
    UserRepository userRepository;

    @Override
    @Transactional
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username)
            .orElseThrow(() -> new UsernameNotFoundException("User Not Found with username: " + username));

        return UserDetailsImpl.build(user);
    }
}
```

### 1.3 Configurazione Dettagliata

La configurazione di Spring Security avviene tipicamente in una classe che estende `WebSecurityConfigurerAdapter`:

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    UserDetailsServiceImpl userDetailsService;

    @Autowired
    private AuthEntryPointJwt unauthorizedHandler;

    @Bean
    public AuthTokenFilter authenticationJwtTokenFilter() {
        return new AuthTokenFilter();
    }

    @Override
    public void configure(AuthenticationManagerBuilder authenticationManagerBuilder) throws Exception {
        authenticationManagerBuilder.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().and().csrf().disable()
            .exceptionHandling().authenticationEntryPoint(unauthorizedHandler).and()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS).and()
            .authorizeRequests().antMatchers("/api/auth/**").permitAll()
            .antMatchers("/api/test/**").permitAll()
            .anyRequest().authenticated();

        http.addFilterBefore(authenticationJwtTokenFilter(), UsernamePasswordAuthenticationFilter.class);
    }
}
```

Questa configurazione:
- Disabilita CSRF (Cross-Site Request Forgery) poiché JWT è stateless
- Imposta la gestione delle sessioni come STATELESS
- Definisce quali endpoint sono pubblici e quali richiedono autenticazione
- Aggiunge il filtro JWT personalizzato

## 2. JWT: Approfondimento tecnico

### 2.1 Struttura dettagliata del token

Un token JWT è composto da tre parti codificate in Base64URL e separate da punti:

```
header.payload.signature
```

**Header**:
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**Payload (esempio)**:
```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022,
  "exp": 1516242622,
  "roles": ["ROLE_USER", "ROLE_ADMIN"]
}
```

**Signature**:
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

### 2.2 Claims standard

I JWT hanno un insieme di claim standard:

- `iss`: Emittente (issuer) del token
- `sub`: Soggetto (subject) del token
- `aud`: Destinatario (audience) del token
- `exp`: Timestamp di scadenza
- `nbf`: Timestamp "not before" (non valido prima di)
- `iat`: Timestamp di emissione
- `jti`: ID univoco del token

### 2.3 Implementazione dettagliata delle utility JWT

```java
public class JwtUtils {
    private static final Logger logger = LoggerFactory.getLogger(JwtUtils.class);

    @Value("${app.jwtSecret}")
    private String jwtSecret;

    @Value("${app.jwtExpirationMs}")
    private int jwtExpirationMs;

    public String generateJwtToken(Authentication authentication) {
        UserDetailsImpl userPrincipal = (UserDetailsImpl) authentication.getPrincipal();

        return Jwts.builder()
                .setSubject((userPrincipal.getUsername()))
                .setIssuedAt(new Date())
                .setExpiration(new Date((new Date()).getTime() + jwtExpirationMs))
                .signWith(SignatureAlgorithm.HS512, jwtSecret)
                .compact();
    }

    public String getUserNameFromJwtToken(String token) {
        return Jwts.parser().setSigningKey(jwtSecret).parseClaimsJws(token).getBody().getSubject();
    }

    public boolean validateJwtToken(String authToken) {
        try {
            Jwts.parser().setSigningKey(jwtSecret).parseClaimsJws(authToken);
            return true;
        } catch (SignatureException e) {
            logger.error("Invalid JWT signature: {}", e.getMessage());
        } catch (MalformedJwtException e) {
            logger.error("Invalid JWT token: {}", e.getMessage());
        } catch (ExpiredJwtException e) {
            logger.error("JWT token is expired: {}", e.getMessage());
        } catch (UnsupportedJwtException e) {
            logger.error("JWT token is unsupported: {}", e.getMessage());
        } catch (IllegalArgumentException e) {
            logger.error("JWT claims string is empty: {}", e.getMessage());
        }

        return false;
    }
}
```

## 3. Filtri di autenticazione

### 3.1 AuthTokenFilter

Il filtro JWT personalizzato è una componente cruciale:

```java
public class AuthTokenFilter extends OncePerRequestFilter {
    @Autowired
    private JwtUtils jwtUtils;

    @Autowired
    private UserDetailsServiceImpl userDetailsService;

    private static final Logger logger = LoggerFactory.getLogger(AuthTokenFilter.class);

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        try {
            String jwt = parseJwt(request);
            if (jwt != null && jwtUtils.validateJwtToken(jwt)) {
                String username = jwtUtils.getUserNameFromJwtToken(jwt);

                UserDetails userDetails = userDetailsService.loadUserByUsername(username);
                UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());
                authentication.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));

                SecurityContextHolder.getContext().setAuthentication(authentication);
            }
        } catch (Exception e) {
            logger.error("Cannot set user authentication: {}", e);
        }

        filterChain.doFilter(request, response);
    }

    private String parseJwt(HttpServletRequest request) {
        String headerAuth = request.getHeader("Authorization");

        if (StringUtils.hasText(headerAuth) && headerAuth.startsWith("Bearer ")) {
            return headerAuth.substring(7);
        }

        return null;
    }
}
```

Questo filtro:
1. Estrae il token JWT dall'header `Authorization`
2. Valida il token
3. Carica i dettagli dell'utente
4. Imposta l'autenticazione nel SecurityContext

### 3.2 AuthEntryPointJwt

Questa classe gestisce le risposte di errore quando l'autenticazione fallisce:

```java
@Component
public class AuthEntryPointJwt implements AuthenticationEntryPoint {

    private static final Logger logger = LoggerFactory.getLogger(AuthEntryPointJwt.class);

    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response,
                         AuthenticationException authException) throws IOException {
        logger.error("Unauthorized error: {}", authException.getMessage());
        response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Error: Unauthorized");
    }
}
```

## 4. Controller e endpoint

### 4.1 AuthController

```java
@CrossOrigin(origins = "*", maxAge = 3600)
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    @Autowired
    AuthenticationManager authenticationManager;

    @Autowired
    UserRepository userRepository;

    @Autowired
    RoleRepository roleRepository;

    @Autowired
    PasswordEncoder encoder;

    @Autowired
    JwtUtils jwtUtils;

    @PostMapping("/signin")
    public ResponseEntity<?> authenticateUser(@Valid @RequestBody LoginRequest loginRequest) {
        Authentication authentication = authenticationManager.authenticate(
                new UsernamePasswordAuthenticationToken(loginRequest.getUsername(), loginRequest.getPassword()));

        SecurityContextHolder.getContext().setAuthentication(authentication);
        String jwt = jwtUtils.generateJwtToken(authentication);
        
        UserDetailsImpl userDetails = (UserDetailsImpl) authentication.getPrincipal();    
        List<String> roles = userDetails.getAuthorities().stream()
                .map(item -> item.getAuthority())
                .collect(Collectors.toList());

        return ResponseEntity.ok(new JwtResponse(jwt, 
                                                 userDetails.getId(), 
                                                 userDetails.getUsername(), 
                                                 userDetails.getEmail(), 
                                                 roles));
    }

    @PostMapping("/signup")
    public ResponseEntity<?> registerUser(@Valid @RequestBody SignupRequest signUpRequest) {
        if (userRepository.existsByUsername(signUpRequest.getUsername())) {
            return ResponseEntity
                    .badRequest()
                    .body(new MessageResponse("Error: Username is already taken!"));
        }

        if (userRepository.existsByEmail(signUpRequest.getEmail())) {
            return ResponseEntity
                    .badRequest()
                    .body(new MessageResponse("Error: Email is already in use!"));
        }

        // Create new user's account
        User user = new User(signUpRequest.getUsername(), 
                             signUpRequest.getEmail(),
                             encoder.encode(signUpRequest.getPassword()));

        Set<String> strRoles = signUpRequest.getRole();
        Set<Role> roles = new HashSet<>();

        if (strRoles == null) {
            Role userRole = roleRepository.findByName(ERole.ROLE_USER)
                    .orElseThrow(() -> new RuntimeException("Error: Role is not found."));
            roles.add(userRole);
        } else {
            strRoles.forEach(role -> {
                switch (role) {
                case "admin":
                    Role adminRole = roleRepository.findByName(ERole.ROLE_ADMIN)
                            .orElseThrow(() -> new RuntimeException("Error: Role is not found."));
                    roles.add(adminRole);
                    break;
                case "mod":
                    Role modRole = roleRepository.findByName(ERole.ROLE_MODERATOR)
                            .orElseThrow(() -> new RuntimeException("Error: Role is not found."));
                    roles.add(modRole);
                    break;
                default:
                    Role userRole = roleRepository.findByName(ERole.ROLE_USER)
                            .orElseThrow(() -> new RuntimeException("Error: Role is not found."));
                    roles.add(userRole);
                }
            });
        }

        user.setRoles(roles);
        userRepository.save(user);

        return ResponseEntity.ok(new MessageResponse("User registered successfully!"));
    }
}
```

### 4.2 TestController

```java
@CrossOrigin(origins = "*", maxAge = 3600)
@RestController
@RequestMapping("/api/test")
public class TestController {
    @GetMapping("/all")
    public String allAccess() {
        return "Public Content.";
    }
    
    @GetMapping("/user")
    @PreAuthorize("hasRole('USER') or hasRole('MODERATOR') or hasRole('ADMIN')")
    public String userAccess() {
        return "User Content.";
    }

    @GetMapping("/mod")
    @PreAuthorize("hasRole('MODERATOR')")
    public String moderatorAccess() {
        return "Moderator Board.";
    }

    @GetMapping("/admin")
    @PreAuthorize("hasRole('ADMIN')")
    public String adminAccess() {
        return "Admin Board.";
    }
}
```

## 5. Sicurezza avanzata con JWT

### 5.1 Refresh Token

Per migliorare la sicurezza, si può implementare un meccanismo di refresh token:

```java
@Entity
@Table(name = "refreshtoken")
public class RefreshToken {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    @OneToOne
    @JoinColumn(name = "user_id", referencedColumnName = "id")
    private User user;

    @Column(nullable = false, unique = true)
    private String token;

    @Column(nullable = false)
    private Instant expiryDate;

    // getters and setters
}
```

Con un servizio dedicato:

```java
@Service
public class RefreshTokenService {
    @Value("${app.jwtRefreshExpirationMs}")
    private Long refreshTokenDurationMs;

    @Autowired
    private RefreshTokenRepository refreshTokenRepository;

    @Autowired
    private UserRepository userRepository;

    public Optional<RefreshToken> findByToken(String token) {
        return refreshTokenRepository.findByToken(token);
    }

    public RefreshToken createRefreshToken(Long userId) {
        RefreshToken refreshToken = new RefreshToken();

        refreshToken.setUser(userRepository.findById(userId).get());
        refreshToken.setExpiryDate(Instant.now().plusMillis(refreshTokenDurationMs));
        refreshToken.setToken(UUID.randomUUID().toString());

        refreshToken = refreshTokenRepository.save(refreshToken);
        return refreshToken;
    }

    public RefreshToken verifyExpiration(RefreshToken token) {
        if (token.getExpiryDate().compareTo(Instant.now()) < 0) {
            refreshTokenRepository.delete(token);
            throw new TokenRefreshException(token.getToken(), "Refresh token was expired. Please make a new signin request");
        }

        return token;
    }
}
```

### 5.2 Revoca di token

È possibile implementare un meccanismo per revocare i token:

```java
@DeleteMapping("/logout")
public ResponseEntity<?> logoutUser() {
    UserDetailsImpl userDetails = (UserDetailsImpl) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
    Long userId = userDetails.getId();
    refreshTokenService.deleteByUserId(userId);
    return ResponseEntity.ok(new MessageResponse("Log out successful!"));
}
```

### 5.3 Protezione contro attacchi comuni

Per rafforzare ulteriormente la sicurezza:

1. **Limitare il tempo di vita del token**: Token con una durata breve mitigano i rischi di furto del token
2. **Utilizzare HTTPS**: Proteggere la trasmissione del token tramite HTTPS
3. **Implementare whitelist di origini**: Utilizzare CORS per limitare quali domini possono inviare richieste
4. **Rate limiting**: Limitare il numero di richieste per prevenire attacchi di forza bruta

```java
// Esempio di configurazione CORS
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration configuration = new CorsConfiguration();
    configuration.setAllowedOrigins(Arrays.asList("https://example.com"));
    configuration.setAllowedMethods(Arrays.asList("GET", "POST", "PUT", "DELETE"));
    configuration.setAllowCredentials(true);
    configuration.setAllowedHeaders(Arrays.asList("Authorization", "Content-Type"));
    
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", configuration);
    return source;
}
```

## 6. Flusso completo di utilizzo

### 6.1 Registrazione utente
1. Il client invia una richiesta POST a `/api/auth/signup` con username, email e password
2. Il server verifica che username ed email non siano già in uso
3. La password viene codificata con BCrypt
4. I ruoli vengono assegnati (di default ROLE_USER)
5. L'utente viene salvato nel database
6. Il server risponde con un messaggio di successo

### 6.2 Login
1. Il client invia una richiesta POST a `/api/auth/signin` con username e password
2. Il server autentica le credenziali usando AuthenticationManager
3. Se le credenziali sono valide, viene generato un JWT
4. Il server risponde con il token JWT, ID utente, username, email e ruoli

### 6.3 Accesso a risorse protette
1. Il client invia una richiesta a un endpoint protetto (es. `/api/test/user`)
2. Nell'header "Authorization" della richiesta include "Bearer [token]"
3. Il filtro JWT estrae e valida il token
4. Se il token è valido, l'utente viene autenticato
5. Il server verifica i ruoli dell'utente e decide se autorizzare l'accesso
6. Se autorizzato, il server risponde con i dati richiesti

## 7. Integrazione con altre tecnologie

### 7.1 OAuth 2.0 e JWT

Spring Security fornisce supporto per OAuth 2.0, che può essere combinato con JWT:

```java
@Configuration
@EnableAuthorizationServer
public class AuthServerConfig extends AuthorizationServerConfigurerAdapter {
    @Autowired
    private TokenStore tokenStore;

    @Autowired
    private AuthenticationManager authenticationManager;

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
        endpoints
            .tokenStore(tokenStore)
            .authenticationManager(authenticationManager)
            .userDetailsService(userDetailsService);
    }

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients
            .inMemory()
            .withClient("client")
            .secret(passwordEncoder().encode("secret"))
            .authorizedGrantTypes("password", "refresh_token")
            .scopes("read", "write");
    }

    @Bean
    public TokenStore tokenStore() {
        return new JwtTokenStore(accessTokenConverter());
    }

    @Bean
    public JwtAccessTokenConverter accessTokenConverter() {
        JwtAccessTokenConverter converter = new JwtAccessTokenConverter();
        converter.setSigningKey("123");
        return converter;
    }
}
```

### 7.2 Microservizi e JWT

Nei sistemi basati su microservizi, JWT è particolarmente utile:

1. **Gateway API**: Un gateway può verificare l'autenticazione e poi passare il token ai servizi interni
2. **Propagazione del contesto utente**: Il token JWT contiene tutte le informazioni necessarie sull'utente
3. **Service-to-Service Authentication**: I servizi possono utilizzare JWT per autenticarsi reciprocamente

```java
// Esempio di configurazione di un Gateway API
@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
            .route("user-service", r -> r.path("/api/users/**")
                .filters(f -> f.filter(authFilter))
                .uri("lb://user-service"))
            .route("product-service", r -> r.path("/api/products/**")
                .filters(f -> f.filter(authFilter))
                .uri("lb://product-service"))
            .build();
    }
}
```

## 8. Best Practices per Spring Security e JWT

1. **Mantenere i token JWT piccoli**: Includere solo le informazioni necessarie
2. **Utilizzare HTTPS**: Sempre e ovunque
3. **Scadenze brevi**: Impostare scadenze brevi per i token (tipicamente 15-60 minuti)
4. **Utilizzare refresh token**: Implementare un meccanismo di refresh token
5. **Salvare i segreti in modo sicuro**: Utilizzare variabili d'ambiente o vault
6. **Non salvare dati sensibili nei token**: I payload JWT non sono criptati
7. **Implementare la revoca dei token**: Attraverso blacklist o database
8. **Utilizzare algoritmi di firma sicuri**: Preferire HS256 o RS256
9. **Validare tutti gli input**: Convalidare e sanitizzare tutte le richieste
10. **Controlli di autorizzazione specifici**: Utilizzare `@PreAuthorize` con espressioni dettagliate

```java
@PreAuthorize("hasRole('ADMIN') or (hasRole('USER') and #username == authentication.principal.username)")
public UserDto getUserByUsername(String username) {
    // Implementation
}
```

L'implementazione di questi principi e pattern garantisce un sistema di autenticazione e autorizzazione robusto e sicuro per le applicazioni Spring Boot.