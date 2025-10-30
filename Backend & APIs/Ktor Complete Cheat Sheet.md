# Ktor Complete Cheat Sheet

## Table of Contents
- [Ktor Overview](#ktor-overview)
- [Project Setup](#project-setup)
- [Routing & Handlers](#routing--handlers)
- [Plugins & Features](#plugins--features)
- [HTTP Client](#http-client)
- [Authentication](#authentication)
- [Serialization](#serialization)
- [WebSockets](#websockets)
- [Database Integration](#database-integration)
- [Testing](#testing)
- [Deployment](#deployment)
- [Configuration](#configuration)
- [Best Practices](#best-practices)

## Ktor Overview

### What is Ktor?
- **Asynchronous framework** for building connected applications
- **Client and Server** in one framework
- **Coroutine-native** design
- **Modular** with plugins (features)
- **Kotlin Multiplatform** support

### Key Concepts
```kotlin
// Application: The main Ktor application
// Routing: URL path handling
// Plugins: Modular functionality (authentication, serialization, etc.)
// Pipeline: Request/response processing chain
```

## Project Setup

### Gradle Dependencies (Kotlin DSL)
```kotlin
// build.gradle.kts
plugins {
    kotlin("jvm") version "1.9.0"
    application
    kotlin("plugin.serialization") version "1.9.0"
}

dependencies {
    // Ktor server
    implementation("io.ktor:ktor-server-core:2.3.5")
    implementation("io.ktor:ktor-server-netty:2.3.5")
    implementation("io.ktor:ktor-server-content-negotiation:2.3.5")
    implementation("io.ktor:ktor-serialization-kotlinx-json:2.3.5")
    
    // Ktor client
    implementation("io.ktor:ktor-client-core:2.3.5")
    implementation("io.ktor:ktor-client-cio:2.3.5")
    implementation("io.ktor:ktor-client-content-negotiation:2.3.5")
    
    // Logging
    implementation("ch.qos.logback:logback-classic:1.4.11")
    
    // Testing
    testImplementation("io.ktor:ktor-server-tests:2.3.5")
    testImplementation("org.jetbrains.kotlin:kotlin-test-junit:1.9.0")
}

application {
    mainClass.set("ApplicationKt")
}
```

### Basic Server Setup
```kotlin
import io.ktor.server.application.*
import io.ktor.server.engine.*
import io.ktor.server.netty.*
import io.ktor.server.response.*
import io.ktor.server.routing.*

fun main() {
    embeddedServer(Netty, port = 8080, host = "0.0.0.0") {
        routing {
            get("/") {
                call.respondText("Hello, Ktor!")
            }
        }
    }.start(wait = true)
}

// Alternative: Using Application module
fun Application.module() {
    routing {
        get("/") {
            call.respondText("Hello, Ktor!")
        }
    }
}
```

## Routing & Handlers

### Basic Routing
```kotlin
import io.ktor.server.routing.*
import io.ktor.server.response.*
import io.ktor.server.application.*

fun Application.configureRouting() {
    routing {
        // GET request
        get("/") {
            call.respondText("Hello World!")
        }
        
        // Path parameters
        get("/user/{id}") {
            val id = call.parameters["id"]
            call.respondText("User ID: $id")
        }
        
        // Multiple path parameters
        get("/user/{userId}/post/{postId}") {
            val userId = call.parameters["userId"]
            val postId = call.parameters["postId"]
            call.respondText("User: $userId, Post: $postId")
        }
        
        // Query parameters
        get("/search") {
            val query = call.request.queryParameters["q"]
            val page = call.request.queryParameters["page"]?.toIntOrNull() ?: 1
            call.respondText("Search: $query, Page: $page")
        }
        
        // POST request with body
        post("/user") {
            val body = call.receive<String>()
            call.respondText("Received: $body")
        }
    }
}
```

### Advanced Routing
```kotlin
routing {
    // Route groups
    route("/api") {
        route("/v1") {
            get("/users") {
                call.respondText("API v1 Users")
            }
        }
        
        route("/v2") {
            get("/users") {
                call.respondText("API v2 Users")
            }
        }
    }
    
    // Nested routes
    route("/admin") {
        get {
            call.respondText("Admin dashboard")
        }
        
        route("/users") {
            get {
                call.respondText("Admin users list")
            }
            
            get("/{id}") {
                val id = call.parameters["id"]
                call.respondText("Admin user $id")
            }
        }
    }
    
    // HTTP methods
    get { /* ... */ }
    post { /* ... */ }
    put { /* ... */ }
    delete { /* ... */ }
    patch { /* ... */ }
    head { /* ... */ }
    options { /* ... */ }
}
```

### Route Parameters & Validation
```kotlin
import io.ktor.server.plugins.*
import java.util.UUID

routing {
    // Optional parameters
    get("/optional/{param?}") {
        val param = call.parameters["param"] ?: "default"
        call.respondText("Param: $param")
    }
    
    // Regex validation in routes
    get("/item/{id:\\d+}") {
        val id = call.parameters["id"]!!.toInt()
        call.respondText("Item ID: $id")
    }
    
    // Multiple segments
    get("/files/{path...}") {
        val path = call.parameters.getAll("path")?.joinToString("/")
        call.respondText("File path: $path")
    }
    
    // Custom parameter types
    get("/uuid/{id}") {
        val id = try {
            UUID.fromString(call.parameters["id"])
        } catch (e: IllegalArgumentException) {
            throw BadRequestException("Invalid UUID format")
        }
        call.respondText("UUID: $id")
    }
}
```

## Plugins & Features

### Common Plugins Setup
```kotlin
import io.ktor.serialization.kotlinx.json.*
import io.ktor.server.plugins.contentnegotiation.*
import io.ktor.server.plugins.cors.routing.*
import io.ktor.server.plugins.callloging.*
import io.ktor.server.request.*
import org.slf4j.event.Level

fun Application.configurePlugins() {
    // Content Negotiation (JSON serialization)
    install(ContentNegotiation) {
        json()
    }
    
    // CORS
    install(CORS) {
        anyHost()
        allowMethod(HttpMethod.Options)
        allowMethod(HttpMethod.Put)
        allowMethod(HttpMethod.Delete)
        allowMethod(HttpMethod.Patch)
        allowHeader(HttpHeaders.Authorization)
        allowHeader("My-Custom-Header")
        allowCredentials = true
    }
    
    // Call Logging
    install(CallLogging) {
        level = Level.INFO
        filter { call -> call.request.path().startsWith("/") }
        format { call ->
            val status = call.response.status()
            val httpMethod = call.request.httpMethod.value
            val userAgent = call.request.headers["User-Agent"]
            "Status: $status, HTTP method: $httpMethod, User agent: $userAgent"
        }
    }
    
    // Compression
    install(Compression) {
        gzip {
            priority = 1.0
        }
        deflate {
            priority = 10.0
            minimumSize(1024) // condition
        }
    }
    
    // Default Headers
    install(DefaultHeaders) {
        header("X-Engine", "Ktor")
    }
}
```

### Custom Plugins
```kotlin
import io.ktor.server.application.*
import io.ktor.util.*
import io.ktor.server.routing.*
import io.ktor.server.response.*

// Simple custom plugin
class CustomHeaderPlugin(config: Configuration) {
    val headerName = config.headerName
    val headerValue = config.headerValue
    
    class Configuration {
        var headerName = "X-Custom"
        var headerValue = "CustomValue"
    }
    
    companion object Feature : ApplicationPlugin<Application, CustomHeaderPlugin.Configuration, CustomHeaderPlugin> {
        override val key = AttributeKey<CustomHeaderPlugin>("CustomHeader")
        
        override fun install(pipeline: Application, configure: Configuration.() -> Unit): CustomHeaderPlugin {
            val configuration = CustomHeaderPlugin.Configuration().apply(configure)
            val plugin = CustomHeaderPlugin(configuration)
            
            pipeline.intercept(ApplicationCallPipeline.Plugins) {
                call.response.headers.append(configuration.headerName, configuration.headerValue)
                proceed()
            }
            
            return plugin
        }
    }
}

// Usage
fun Application.module() {
    install(CustomHeaderPlugin) {
        headerName = "X-My-App"
        headerValue = "Ktor-Rocks"
    }
}
```

## HTTP Client

### Client Setup & Configuration
```kotlin
import io.ktor.client.*
import io.ktor.client.engine.cio.*
import io.ktor.client.plugins.contentnegotiation.*
import io.ktor.client.plugins.logging.*
import io.ktor.serialization.kotlinx.json.*
import kotlinx.serialization.json.Json

val client = HttpClient(CIO) {
    // Content Negotiation
    install(ContentNegotiation) {
        json(Json {
            prettyPrint = true
            isLenient = true
            ignoreUnknownKeys = true
        })
    }
    
    // Logging
    install(Logging) {
        level = LogLevel.ALL
        logger = object : Logger {
            override fun log(message: String) {
                println("HTTP Client: $message")
            }
        }
    }
    
    // Default Request
    install(DefaultRequest) {
        header("User-Agent", "Ktor Client")
        header(HttpHeaders.ContentType, ContentType.Application.Json)
    }
    
    // Timeouts
    engine {
        requestTimeout = 10000
        socketTimeout = 10000
        connectTimeout = 10000
    }
}
```

### Making HTTP Requests
```kotlin
import io.ktor.client.request.*
import io.ktor.client.statement.*
import io.ktor.http.*
import kotlinx.serialization.Serializable

@Serializable
data class User(val id: Int, val name: String, val email: String)

@Serializable
data class CreateUserRequest(val name: String, val email: String)

suspend fun makeRequests() {
    // GET request
    val response: HttpResponse = client.get("https://jsonplaceholder.typicode.com/users/1")
    val body: String = response.bodyAsText()
    
    // GET with deserialization
    val user: User = client.get("https://jsonplaceholder.typicode.com/users/1")
    
    // POST with JSON body
    val newUser = client.post("https://jsonplaceholder.typicode.com/users") {
        contentType(ContentType.Application.Json)
        setBody(CreateUserRequest("John Doe", "john@example.com"))
    }
    
    // PUT request
    val updatedUser = client.put("https://jsonplaceholder.typicode.com/users/1") {
        contentType(ContentType.Application.Json)
        setBody(User(1, "John Updated", "updated@example.com"))
    }
    
    // DELETE request
    client.delete("https://jsonplaceholder.typicode.com/users/1")
    
    // With custom headers
    val responseWithAuth = client.get("https://api.example.com/data") {
        headers {
            append("Authorization", "Bearer token123")
            append("X-Custom-Header", "value")
        }
    }
    
    // Query parameters
    val searchResults = client.get("https://api.example.com/search") {
        url {
            parameters.append("q", "kotlin")
            parameters.append("page", "1")
            parameters.append("limit", "10")
        }
    }
    
    // Form data
    val formResponse = client.submitForm(
        url = "https://api.example.com/login",
        formParameters = parameters {
            append("username", "user")
            append("password", "pass")
        }
    )
}
```

### Advanced Client Features
```kotlin
// Response validation
suspend fun getUserWithValidation(id: Int): User {
    return client.get("https://api.example.com/users/$id") {
        expectSuccess = true  // Throws exception for non-2xx responses
    }.body()
}

// Streaming
suspend fun downloadFile() {
    client.get("https://example.com/large-file.zip") {
        onDownload { bytesSentTotal, contentLength ->
            val progress = (bytesSentTotal * 100 / contentLength).toInt()
            println("Download progress: $progress%")
        }
    }
}

// Multipart form data
suspend fun uploadFile() {
    client.post("https://api.example.com/upload") {
        setBody(MultiPartFormDataContent(
            formData {
                append("file", File("image.jpg").readBytes(), Headers.build {
                    append(HttpHeaders.ContentType, "image/jpeg")
                    append(HttpHeaders.ContentDisposition, "filename=image.jpg")
                })
                append("description", "My image")
            }
        ))
    }
}
```

## Authentication

### Authentication Setup
```kotlin
import io.ktor.server.auth.*
import io.ktor.util.*
import io.ktor.server.sessions.*
import com.auth0.jwt.*
import com.auth0.jwt.algorithms.Algorithm

fun Application.configureAuthentication() {
    // Basic Authentication
    install(Authentication) {
        basic("auth-basic") {
            realm = "Ktor Server"
            validate { credentials ->
                if (credentials.name == "user" && credentials.password == "password") {
                    UserIdPrincipal(credentials.name)
                } else {
                    null
                }
            }
        }
        
        // JWT Authentication
        jwt("auth-jwt") {
            realm = "Ktor Server"
            verifier(
                JWT.require(Algorithm.HMAC256("secret"))
                    .withAudience("ktor-server")
                    .withIssuer("ktor-app")
                    .build()
            )
            validate { credential ->
                if (credential.payload.getClaim("username").asString() != "") {
                    JWTPrincipal(credential.payload)
                } else {
                    null
                }
            }
        }
        
        // Session Authentication
        session<UserSession>("auth-session") {
            validate { session ->
                if (session.userId.isNotBlank()) {
                    session
                } else {
                    null
                }
            }
            challenge {
                call.respondRedirect("/login")
            }
        }
    }
    
    // Session configuration
    install(Sessions) {
        cookie<UserSession>("user_session") {
            cookie.path = "/"
            cookie.maxAgeInSeconds = 3600
            transform(SessionTransportTransformerMessageAuthentication(hashKey))
        }
    }
}

data class UserSession(val userId: String, val count: Int = 0)
```

### Protected Routes
```kotlin
import io.ktor.server.auth.*

routing {
    // Basic auth protected route
    authenticate("auth-basic") {
        get("/protected-basic") {
            val principal = call.principal<UserIdPrincipal>()
            call.respondText("Hello, ${principal?.name}")
        }
    }
    
    // JWT protected route
    authenticate("auth-jwt") {
        get("/protected-jwt") {
            val principal = call.principal<JWTPrincipal>()
            val username = principal?.payload?.getClaim("username")?.asString()
            val expiresAt = principal?.expiresAt
            call.respondText("Hello, $username! Token expires at: $expiresAt")
        }
    }
    
    // Session protected route
    authenticate("auth-session") {
        get("/protected-session") {
            val session = call.sessions.get<UserSession>()
            call.respondText("Session user: ${session?.userId}")
        }
    }
    
    // Login endpoint
    post("/login") {
        val username = call.parameters["username"]
        val password = call.parameters["password"]
        
        if (authenticateUser(username, password)) {
            call.sessions.set(UserSession(username!!))
            call.respond(mapOf("status" to "success"))
        } else {
            call.respond(mapOf("status" to "failure"))
        }
    }
    
    // Logout endpoint
    get("/logout") {
        call.sessions.clear<UserSession>()
        call.respondRedirect("/")
    }
}
```

## Serialization

### JSON Serialization
```kotlin
import io.ktor.serialization.kotlinx.json.*
import kotlinx.serialization.*
import kotlinx.serialization.json.*

@Serializable
data class User(val id: Int, val name: String, val email: String)

@Serializable
data class ApiResponse<T>(val success: Boolean, val data: T?, val error: String? = null)

fun Application.configureSerialization() {
    install(ContentNegotiation) {
        json(Json {
            prettyPrint = true
            isLenient = true
            ignoreUnknownKeys = true
            coerceInputValues = true
        })
    }
}

// Using serialization in routes
routing {
    get("/user/{id}") {
        val id = call.parameters["id"]?.toIntOrNull()
        if (id == null) {
            call.respond(HttpStatusCode.BadRequest, ApiResponse(false, null, "Invalid ID"))
            return@get
        }
        
        val user = getUserById(id)
        if (user != null) {
            call.respond(HttpStatusCode.OK, ApiResponse(true, user))
        } else {
            call.respond(HttpStatusCode.NotFound, ApiResponse(false, null, "User not found"))
        }
    }
    
    post("/user") {
        try {
            val userRequest = call.receive<User>()
            val createdUser = createUser(userRequest)
            call.respond(HttpStatusCode.Created, ApiResponse(true, createdUser))
        } catch (e: Exception) {
            call.respond(HttpStatusCode.BadRequest, ApiResponse(false, null, "Invalid user data"))
        }
    }
}
```

### Custom Serialization
```kotlin
import io.ktor.serialization.*
import io.ktor.util.reflect.*
import io.ktor.server.plugins.contentnegotiation.*
import kotlinx.serialization.SerializationStrategy
import kotlinx.serialization.DeserializationStrategy

// Custom content converter
class CustomConverter : ContentConverter {
    override suspend fun serialize(
        contentType: ContentType,
        charset: Charset,
        typeInfo: TypeInfo,
        value: Any
    ): OutgoingContent? {
        // Custom serialization logic
        return null
    }
    
    override suspend fun deserialize(charset: Charset, typeInfo: TypeInfo, content: ByteReadChannel): Any? {
        // Custom deserialization logic
        return null
    }
}
```

## WebSockets

### WebSocket Setup
```kotlin
import io.ktor.server.websocket.*
import io.ktor.websocket.*
import java.time.Duration
import kotlinx.coroutines.channels.ClosedReceiveChannelException

fun Application.configureWebSockets() {
    install(WebSockets) {
        pingPeriod = Duration.ofSeconds(15)
        timeout = Duration.ofSeconds(15)
        maxFrameSize = Long.MAX_VALUE
        masking = false
    }
}

// WebSocket routes
routing {
    webSocket("/chat") {
        val sessionId = call.parameters["sessionId"]
        println("WebSocket connected: $sessionId")
        
        try {
            for (frame in incoming) {
                when (frame) {
                    is Frame.Text -> {
                        val receivedText = frame.readText()
                        println("Received: $receivedText")
                        
                        // Echo back
                        outgoing.send(Frame.Text("You said: $receivedText"))
                        
                        // Broadcast to other connections
                        // (would need connection tracking)
                    }
                    is Frame.Binary -> {
                        // Handle binary data
                    }
                    else -> {}
                }
            }
        } catch (e: ClosedReceiveChannelException) {
            println("WebSocket closed normally")
        } catch (e: Throwable) {
            println("WebSocket error: ${e.message}")
        } finally {
            println("WebSocket disconnected: $sessionId")
        }
    }
    
    webSocket("/realtime") {
        // Send periodic updates
        for (i in 1..10) {
            outgoing.send(Frame.Text("Update $i"))
            kotlinx.coroutines.delay(1000)
        }
    }
}
```

### Advanced WebSocket Features
```kotlin
class Connection(val session: DefaultWebSocketSession) {
    suspend fun send(message: String) {
        session.send(Frame.Text(message))
    }
}

val connections = mutableSetOf<Connection>()

routing {
    webSocket("/broadcast") {
        val connection = Connection(this)
        connections += connection
        
        try {
            send("Welcome! There are ${connections.size} users online")
            
            for (frame in incoming) {
                if (frame is Frame.Text) {
                    val text = frame.readText()
                    // Broadcast to all connections
                    connections.forEach {
                        it.send("User: $text")
                    }
                }
            }
        } finally {
            connections -= connection
            connections.forEach {
                it.send("A user left. ${connections.size} users online")
            }
        }
    }
}
```

## Database Integration

### Exposed ORM Setup
```kotlin
import org.jetbrains.exposed.sql.*
import org.jetbrains.exposed.sql.transactions.transaction
import com.zaxxer.hikari.HikariConfig
import com.zaxxer.hikari.HikariDataSource

object Users : Table() {
    val id = integer("id").autoIncrement()
    val name = varchar("name", 50)
    val email = varchar("email", 100)
    val createdAt = datetime("created_at")
    
    override val primaryKey = PrimaryKey(id)
}

fun Application.configureDatabase() {
    val config = HikariConfig().apply {
        jdbcUrl = "jdbc:h2:mem:test;DB_CLOSE_DELAY=-1"
        driverClassName = "org.h2.Driver"
        maximumPoolSize = 10
        isAutoCommit = false
        transactionIsolation = "TRANSACTION_REPEATABLE_READ"
        validate()
    }
    
    val dataSource = HikariDataSource(config)
    Database.connect(dataSource)
    
    // Create tables
    transaction {
        SchemaUtils.create(Users)
    }
}
```

### Database Operations
```kotlin
import org.jetbrains.exposed.sql.SqlExpressionBuilder.eq

@Serializable
data class UserDTO(val id: Int, val name: String, val email: String)

// Repository pattern
class UserRepository {
    fun getAllUsers(): List<UserDTO> = transaction {
        Users.selectAll().map { 
            UserDTO(
                it[Users.id],
                it[Users.name],
                it[Users.email]
            )
        }
    }
    
    fun getUserById(id: Int): UserDTO? = transaction {
        Users.select { Users.id eq id }
            .singleOrNull()
            ?.let {
                UserDTO(
                    it[Users.id],
                    it[Users.name],
                    it[Users.email]
                )
            }
    }
    
    fun createUser(name: String, email: String): Int = transaction {
        Users.insert {
            it[Users.name] = name
            it[Users.email] = email
            it[createdAt] = java.time.LocalDateTime.now()
        } get Users.id
    }
    
    fun updateUser(id: Int, name: String, email: String): Boolean = transaction {
        Users.update({ Users.id eq id }) {
            it[Users.name] = name
            it[Users.email] = email
        } > 0
    }
    
    fun deleteUser(id: Int): Boolean = transaction {
        Users.deleteWhere { Users.id eq id } > 0
    }
}
```

## Testing

### Server Testing
```kotlin
import io.ktor.server.testing.*
import kotlin.test.*

class ApplicationTest {
    @Test
    fun testRoot() = testApplication {
        application {
            module()
        }
        
        client.get("/").apply {
            assertEquals(HttpStatusCode.OK, status)
            assertEquals("Hello World!", bodyAsText())
        }
    }
    
    @Test
    fun testUserApi() = testApplication {
        application {
            module()
        }
        
        // Test GET user
        client.get("/user/1").apply {
            assertEquals(HttpStatusCode.OK, status)
        }
        
        // Test POST user
        client.post("/user") {
            contentType(ContentType.Application.Json)
            setBody("""{"name":"John","email":"john@test.com"}""")
        }.apply {
            assertEquals(HttpStatusCode.Created, status)
        }
    }
    
    @Test
    fun testWebSocket() = testApplication {
        application {
            module()
        }
        
        client.webSocket("/chat") {
            send(Frame.Text("Hello"))
            val response = incoming.receive() as Frame.Text
            assertEquals("You said: Hello", response.readText())
        }
    }
}
```

### Client Testing
```kotlin
import io.ktor.client.engine.mock.*
import io.ktor.http.*
import kotlin.test.*

class ClientTest {
    @Test
    fun testClient() = testApplication {
        val client = HttpClient(MockEngine) {
            engine {
                addHandler { request ->
                    when (request.url.fullPath) {
                        "/users/1" -> respond(
                            content = """{"id":1,"name":"John","email":"john@test.com"}""",
                            status = HttpStatusCode.OK,
                            headers = headersOf(HttpHeaders.ContentType, ContentType.Application.Json.toString())
                        )
                        else -> respond(
                            content = "Not Found",
                            status = HttpStatusCode.NotFound
                        )
                    }
                }
            }
        }
        
        val user: User = client.get("http://test.com/users/1")
        assertEquals(1, user.id)
        assertEquals("John", user.name)
    }
}
```

## Deployment

### Application Configuration
```kotlin
// application.conf
ktor {
    deployment {
        port = 8080
        port = ${?PORT}
        host = "0.0.0.0"
        host = ${?HOST}
        
        # For watch/reload
        watch = [ classes, resources ]
        
        # SSL configuration
        sslPort = 8443
        sslKeyStore = keystore.jks
        sslKeyStorePassword = password
        sslKeyAlias = mykey
    }
    
    application {
        modules = [ com.example.ApplicationKt.module ]
    }
    
    # Environment-specific configuration
    development {
        logLevel = trace
    }
    
    production {
        logLevel = info
    }
}
```

### Docker Configuration
```dockerfile
# Dockerfile
FROM openjdk:17-jdk-slim

WORKDIR /app
COPY build/libs/my-ktor-app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - HOST=0.0.0.0
      - PORT=8080
      - DATABASE_URL=jdbc:postgresql://db:5432/mydb
    depends_on:
      - db
  
  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=mydb
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

## Configuration

### Environment Configuration
```kotlin
import io.ktor.server.config.*

fun Application.module() {
    val config = environment.config
    
    val databaseUrl = config.property("storage.database.url").getString()
    val apiKey = config.propertyOrNull("api.keys.secret")?.getString() ?: "default"
    val featureFlag = config.property("features.newUI").getString().toBoolean()
    
    // Nested configuration
    val redisConfig = config.config("redis")
    val redisHost = redisConfig.property("host").getString()
    val redisPort = redisConfig.property("port").getString().toInt()
    
    println("Database URL: $databaseUrl")
    println("Redis: $redisHost:$redisPort")
}
```

### Custom Configuration
```hocon
# application.conf
ktor {
    application {
        modules = [ com.example.ApplicationKt.module ]
    }
    
    storage {
        database {
            url = "jdbc:postgresql://localhost:5432/mydb"
            poolSize = 10
        }
        redis {
            host = "localhost"
            port = 6379
        }
    }
    
    api {
        keys {
            secret = ${?API_SECRET_KEY}
            public = ${?API_PUBLIC_KEY}
        }
    }
    
    features {
        newUI = true
        experimental = false
    }
}
```

## Best Practices

### Error Handling
```kotlin
import io.ktor.server.plugins.statuspages.*
import io.ktor.http.*

fun Application.configureErrorHandling() {
    install(StatusPages) {
        exception<Throwable> { call, cause ->
            call.respond(
                HttpStatusCode.InternalServerError,
                ApiResponse(false, null, "Internal server error")
            )
            throw cause
        }
        
        exception<AuthenticationException> { call, cause ->
            call.respond(HttpStatusCode.Unauthorized)
        }
        
        exception<AuthorizationException> { call, cause ->
            call.respond(HttpStatusCode.Forbidden)
        }
        
        status(HttpStatusCode.NotFound) { call, status ->
            call.respond(
                status,
                ApiResponse(false, null, "Resource not found")
            )
        }
    }
}

// Custom exceptions
class AuthenticationException : RuntimeException("Authentication failed")
class AuthorizationException : RuntimeException("Access denied")
```

### Logging & Monitoring
```kotlin
import io.ktor.server.request.*
import org.slf4j.LoggerFactory

val logger = LoggerFactory.getLogger("Application")

fun Application.configureMonitoring() {
    install(CallLogging) {
        level = Level.INFO
        filter { call -> call.request.path().startsWith("/") }
        
        mdc("method") { call -> call.request.httpMethod.value }
        mdc("path") { call -> call.request.path() }
        mdc("status") { call -> call.response.status()?.toString() }
        
        format { call ->
            val status = call.response.status()
            val method = call.request.httpMethod.value
            val path = call.request.path()
            val userAgent = call.request.headers["User-Agent"]
            "Status: $status, Method: $method, Path: $path, User-Agent: $userAgent"
        }
    }
}

// Structured logging
fun logRequest(call: ApplicationCall, message: String, additionalInfo: Map<String, Any> = emptyMap()) {
    logger.info(
        "Request: {} {} - {} - {}",
        call.request.httpMethod.value,
        call.request.path(),
        message,
        additionalInfo
    )
}
```

### Performance Optimization
```kotlin
fun Application.configurePerformance() {
    // Compression for large responses
    install(Compression) {
        gzip {
            priority = 1.0
        }
        deflate {
            priority = 10.0
            minimumSize(1024)
        }
    }
    
    // Caching headers
    install(CachingHeaders) {
        options { call, outgoingContent ->
            when (outgoingContent.contentType?.withoutParameters()) {
                ContentType.Text.CSS -> CachingOptions(CacheControl.MaxAge(maxAgeSeconds = 24 * 60 * 60))
                ContentType.Application.JavaScript -> CachingOptions(CacheControl.MaxAge(maxAgeSeconds = 24 * 60 * 60))
                else -> null
            }
        }
    }
    
    // Rate limiting (custom implementation)
    intercept(ApplicationCallPipeline.Plugins) {
        // Implement rate limiting logic
        val clientIp = call.request.origin.remoteHost
        if (isRateLimited(clientIp)) {
            call.respond(HttpStatusCode.TooManyRequests, "Rate limit exceeded")
            finish()
        }
        proceed()
    }
}
```

---

## Quick Reference Card

### Most Used Plugins
```kotlin
// Server plugins
ContentNegotiation    // JSON serialization
CORS                  // Cross-origin requests
Authentication        // Auth mechanisms
Sessions              // Session management
WebSockets            // Real-time communication
CallLogging           // Request logging
Compression           // Response compression
StatusPages           // Error handling
```

### Common Response Types
```kotlin
call.respondText("Hello")                          // Plain text
call.respond(HttpStatusCode.OK)                    // Status only
call.respond(mapOf("status" to "success"))         // JSON object
call.respond(MyDataClass(...))                     // Serialized object
call.respondBytes(byteArray, ContentType.Image.PNG) // Binary data
call.respondRedirect("/new-location")              // Redirect
```

### Essential Client Features
```kotlin
// Request configuration
setBody(data)                    // Request body
header("Key", "Value")           // Custom headers
parameter("key", "value")        // Query parameters
basicAuth("user", "pass")        // Authentication
timeout { ... }                  // Timeout configuration

// Response handling
body<T>()                        // Deserialized response
bodyAsText()                     // Raw text
status                           // HTTP status
headers                          // Response headers
```

### Pro Tips
1. Use **structured logging** for better observability
2. Implement **proper error handling** with StatusPages
3. Use **environment-specific configurations**
4. **Test thoroughly** with testApplication
5. Use **connection pooling** for databases
6. Implement **rate limiting** for public APIs
7. Use **compression** for large responses
8. Follow **RESTful conventions** for APIs

---

*Remember: Ktor's strength lies in its coroutine-native design and modular architecture. Leverage suspending functions throughout your application and choose only the plugins you need for optimal performance!*