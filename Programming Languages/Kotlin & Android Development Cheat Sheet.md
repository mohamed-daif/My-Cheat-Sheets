# Kotlin & Android Development Complete Cheat Sheet

## Table of Contents
- [Kotlin Basics](#kotlin-basics)
- [Kotlin Functions](#kotlin-functions)
- [Kotlin OOP & Classes](#kotlin-oop--classes)
- [Kotlin Collections](#kotlin-collections)
- [Coroutines & Async](#coroutines--async)
- [Android Components](#android-components)
- [Jetpack Compose](#jetpack-compose)
- [Android Architecture](#android-architecture)
- [Room Database](#room-database)
- [Networking & Retrofit](#networking--retrofit)
- [Dependency Injection](#dependency-injection)
- [Build Configuration](#build-configuration)
- [Testing](#testing)
- [Performance & Optimization](#performance--optimization)

## Kotlin Basics

### Variables & Data Types
```kotlin
// Variables
val name = "John"                    // Read-only (immutable)
var age = 25                         // Mutable
const val PI = 3.14                  // Compile-time constant

// Data types
val number: Int = 10
val bigNumber: Long = 100L
val decimal: Double = 3.14
val floatDecimal: Float = 3.14f
val character: Char = 'A'
val boolean: Boolean = true
val string: String = "Hello"

// Nullable types
var nullableString: String? = null
var length = nullableString?.length   // Safe call
var lengthOrDefault = nullableString?.length ?: 0  // Elvis operator
```

### Control Flow
```kotlin
// If-else as expression
val max = if (a > b) a else b

// When expression (switch replacement)
val description = when (x) {
    1 -> "One"
    2, 3 -> "Two or Three"
    in 4..10 -> "Between 4 and 10"
    else -> "Other"
}

// When without argument
when {
    x.isOdd() -> print("x is odd")
    x.isEven() -> print("x is even")
    else -> print("x is funny")
}

// For loops
for (i in 1..10) { }                // 1 to 10 inclusive
for (i in 1 until 10) { }           // 1 to 9
for (i in 10 downTo 1) { }          // 10 down to 1
for (i in 1..10 step 2) { }         // 1, 3, 5, 7, 9

// While loops
while (x > 0) { x-- }
do { x-- } while (x > 0)
```

## Kotlin Functions

### Function Definitions
```kotlin
// Basic function
fun greet(name: String): String {
    return "Hello, $name"
}

// Single-expression function
fun square(x: Int) = x * x

// Default arguments
fun connect(
    host: String, 
    port: Int = 8080,
    timeout: Int = 5000
) { }

// Named arguments
connect(host = "localhost", timeout = 10000)

// Extension functions
fun String.addExclamation() = "$this!"

// Infix functions
infix fun Int.times(str: String) = str.repeat(this)
val result = 2 times "Bye "  // "Bye Bye "

// Higher-order functions
fun calculate(x: Int, y: Int, operation: (Int, Int) -> Int): Int {
    return operation(x, y)
}
val result = calculate(10, 5) { a, b -> a + b }
```

### Scope Functions
```kotlin
val person = Person("John", 25)

// let - null check and transformation
person?.let { 
    println(it.name)
    it.age 
}

// apply - configure object
person.apply {
    name = "Jane"
    age = 30
}

// also - additional actions
person.also {
    println("Person created: ${it.name}")
}

// run - object configuration with return value
val result = person.run {
    name = "Bob"
    calculateAge()
}

// with - non-extension version
with(person) {
    println(name)
    println(age)
}
```

## Kotlin OOP & Classes

### Classes & Objects
```kotlin
// Data class (automatically generates equals, hashCode, toString, copy)
data class User(
    val name: String,
    val age: Int
)

// Regular class
class Car(val brand: String) {
    var speed: Int = 0
        private set  // Setter is private
    
    // Secondary constructor
    constructor(brand: String, speed: Int) : this(brand) {
        this.speed = speed
    }
    
    // Companion object (static methods)
    companion object {
        const val MAX_SPEED = 200
        fun createDefault() = Car("Unknown")
    }
}

// Sealed classes (restricted class hierarchies)
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error(val exception: Exception) : Result<Nothing>()
    object Loading : Result<Nothing>()
}

// Enum classes
enum class Color {
    RED, GREEN, BLUE
}

enum class Color(val rgb: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00),
    BLUE(0x0000FF)
}
```

### Inheritance & Interfaces
```kotlin
// Open class (can be inherited)
open class Animal(val name: String) {
    open fun makeSound() {
        println("Some sound")
    }
}

// Interface
interface Swimmable {
    fun swim() {
        println("Swimming")
    }
}

// Inheritance
class Dog(name: String) : Animal(name), Swimmable {
    override fun makeSound() {
        println("Woof!")
    }
    
    // Override interface method with default implementation
    override fun swim() {
        println("Dog swimming")
    }
}

// Abstract class
abstract class Shape {
    abstract fun area(): Double
}

class Circle(val radius: Double) : Shape() {
    override fun area(): Double = Math.PI * radius * radius
}
```

## Kotlin Collections

### Collection Operations
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val mutableNumbers = mutableListOf(1, 2, 3)

// Transformation
numbers.map { it * 2 }                    // [2, 4, 6, 8, 10]
numbers.filter { it % 2 == 0 }            // [2, 4]
numbers.flatMap { listOf(it, it + 1) }    // [1, 2, 2, 3, 3, 4, 4, 5, 5, 6]

// Aggregation
numbers.sum()                             // 15
numbers.average()                         // 3.0
numbers.count()                           // 5
numbers.reduce { acc, i -> acc + i }      // 15

// Searching
numbers.find { it > 3 }                   // 4
numbers.first { it > 2 }                  // 3
numbers.any { it > 4 }                    // true
numbers.all { it > 0 }                    // true

// Sorting
numbers.sorted()                          // [1, 2, 3, 4, 5]
numbers.sortedDescending()                // [5, 4, 3, 2, 1]
numbers.sortedBy { it % 2 }               // [2, 4, 1, 3, 5]

// Grouping
numbers.groupBy { if (it % 2 == 0) "even" else "odd" }
// {odd=[1, 3, 5], even=[2, 4]}
```

### Collection Types
```kotlin
// Lists
val list = listOf("a", "b", "c")
val mutableList = mutableListOf("a", "b", "c")

// Sets
val set = setOf("a", "b", "c")
val mutableSet = mutableSetOf("a", "b", "c")

// Maps
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
val mutableMap = mutableMapOf("a" to 1, "b" to 2)

// Sequences (lazy evaluation)
val sequence = sequenceOf(1, 2, 3)
val generatedSequence = generateSequence(1) { it + 1 }
    .take(10)
    .toList()
```

## Coroutines & Async

### Basic Coroutines
```kotlin
// Coroutine scopes
class MyViewModel : ViewModel() {
    fun fetchData() {
        viewModelScope.launch {
            val data = repository.getData()
            // Update UI
        }
    }
}

// In Activity/Fragment
lifecycleScope.launch {
    val data = fetchUserData()
    updateUI(data)
}

// Dispatchers
viewModelScope.launch(Dispatchers.IO) {
    // Network or disk operations
}

viewModelScope.launch(Dispatchers.Main) {
    // UI updates
}

// Structured concurrency
viewModelScope.launch {
    val user = async { getUser() }
    val posts = async { getPosts() }
    
    showUserData(user.await(), posts.await())
}
```

### Advanced Coroutines
```kotlin
// Error handling
viewModelScope.launch {
    try {
        val data = repository.getData()
    } catch (e: Exception) {
        // Handle error
    }
}

// Alternative error handling
viewModelScope.launch {
    val result = kotlin.runCatching { 
        repository.getData() 
    }
    result.onSuccess { data -> }
        .onFailure { error -> }
}

// Timeouts
viewModelScope.launch {
    try {
        val data = withTimeout(5000) {
            repository.getData()
        }
    } catch (e: TimeoutCancellationException) {
        // Handle timeout
    }
}

// Flows (reactive streams)
fun getData(): Flow<List<Data>> = flow {
    emit(repository.getData())
}.flowOn(Dispatchers.IO)

// Collect flows
viewModelScope.launch {
    getData().collect { data ->
        // Update UI with new data
    }
}
```

## Android Components

### Activity & Fragment
```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // ViewBinding
        val binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        // Navigation
        binding.button.setOnClickListener {
            val intent = Intent(this, DetailActivity::class.java)
            startActivity(intent)
        }
    }
}

// Fragment
class HomeFragment : Fragment() {
    private var _binding: FragmentHomeBinding? = null
    private val binding get() = _binding!!
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentHomeBinding.inflate(inflater, container, false)
        return binding.root
    }
    
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

### Lifecycle & ViewModel
```kotlin
class MainViewModel : ViewModel() {
    private val _uiState = MutableStateFlow<UiState>(UiState.Loading)
    val uiState: StateFlow<UiState> = _uiState.asStateFlow()
    
    fun loadData() {
        viewModelScope.launch {
            _uiState.value = UiState.Loading
            try {
                val data = repository.getData()
                _uiState.value = UiState.Success(data)
            } catch (e: Exception) {
                _uiState.value = UiState.Error(e.message ?: "Unknown error")
            }
        }
    }
}

// In Activity/Fragment
viewModel.uiState.onEach { state ->
    when (state) {
        is UiState.Loading -> showLoading()
        is UiState.Success -> showData(state.data)
        is UiState.Error -> showError(state.message)
    }
}.launchIn(lifecycleScope)
```

## Jetpack Compose

### Basic Composables
```kotlin
@Composable
fun Greeting(name: String) {
    Text(
        text = "Hello, $name!",
        style = MaterialTheme.typography.h4,
        color = MaterialTheme.colors.primary
    )
}

@Composable
fun MyScreen() {
    var text by remember { mutableStateOf("") }
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        TextField(
            value = text,
            onValueChange = { text = it },
            label = { Text("Enter text") }
        )
        
        Spacer(modifier = Modifier.height(16.dp))
        
        Button(
            onClick = { /* Handle click */ },
            enabled = text.isNotEmpty()
        ) {
            Text("Submit")
        }
    }
}
```

### State Management & Lists
```kotlin
@Composable
fun TodoList() {
    var todos by remember { mutableStateOf(listOf<Todo>()) }
    var newTodoText by remember { mutableStateOf("") }
    
    Column {
        Row {
            TextField(
                value = newTodoText,
                onValueChange = { newTodoText = it },
                modifier = Modifier.weight(1f)
            )
            
            Button(
                onClick = {
                    if (newTodoText.isNotBlank()) {
                        todos = todos + Todo(newTodoText)
                        newTodoText = ""
                    }
                }
            ) {
                Text("Add")
            }
        }
        
        LazyColumn {
            items(todos) { todo ->
                TodoItem(
                    todo = todo,
                    onDelete = { todos = todos - todo }
                )
            }
        }
    }
}

@Composable
fun TodoItem(todo: Todo, onDelete: () -> Unit) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .padding(8.dp),
        horizontalArrangement = Arrangement.SpaceBetween
    ) {
        Text(text = todo.text)
        IconButton(onClick = onDelete) {
            Icon(Icons.Default.Delete, contentDescription = "Delete")
        }
    }
}
```

## Android Architecture

### Repository Pattern
```kotlin
class UserRepository(
    private val localDataSource: UserLocalDataSource,
    private val remoteDataSource: UserRemoteDataSource
) {
    fun getUsers(): Flow<List<User>> = flow {
        // Emit cached data first
        emit(localDataSource.getUsers())
        
        // Then fetch from network
        try {
            val remoteUsers = remoteDataSource.getUsers()
            localDataSource.saveUsers(remoteUsers)
            emit(remoteUsers)
        } catch (e: Exception) {
            // Emit error or keep cached data
        }
    }
    
    suspend fun refreshUsers() {
        try {
            val users = remoteDataSource.getUsers()
            localDataSource.saveUsers(users)
        } catch (e: Exception) {
            // Handle error
        }
    }
}
```

### Use Cases
```kotlin
class GetUsersUseCase(
    private val userRepository: UserRepository
) {
    operator fun invoke(): Flow<List<User>> {
        return userRepository.getUsers()
    }
}

class RefreshUsersUseCase(
    private val userRepository: UserRepository
) {
    suspend operator fun invoke() {
        return userRepository.refreshUsers()
    }
}
```

## Room Database

### Entity & DAO
```kotlin
// Entity
@Entity(tableName = "users")
data class User(
    @PrimaryKey val id: Int,
    @ColumnInfo(name = "name") val name: String,
    @ColumnInfo(name = "email") val email: String,
    @ColumnInfo(name = "created_at") val createdAt: Long = System.currentTimeMillis()
)

// DAO
@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    fun getUsers(): Flow<List<User>>
    
    @Query("SELECT * FROM users WHERE id = :id")
    suspend fun getUserById(id: Int): User?
    
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertUser(user: User)
    
    @Update
    suspend fun updateUser(user: User)
    
    @Delete
    suspend fun deleteUser(user: User)
    
    @Query("DELETE FROM users")
    suspend fun deleteAll()
}
```

### Database Setup
```kotlin
@Database(
    entities = [User::class],
    version = 1,
    exportSchema = false
)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
    
    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null
        
        fun getInstance(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "app_database"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

## Networking & Retrofit

### Retrofit Setup
```kotlin
// API interface
interface ApiService {
    @GET("users")
    suspend fun getUsers(): List<User>
    
    @GET("users/{id}")
    suspend fun getUser(@Path("id") id: Int): User
    
    @POST("users")
    suspend fun createUser(@Body user: User): User
    
    @PUT("users/{id}")
    suspend fun updateUser(@Path("id") id: Int, @Body user: User): User
    
    @DELETE("users/{id}")
    suspend fun deleteUser(@Path("id") id: Int)
}

// Network module
object NetworkModule {
    private const val BASE_URL = "https://api.example.com/"
    
    fun provideRetrofit(): Retrofit {
        return Retrofit.Builder()
            .baseUrl(BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .client(provideOkHttpClient())
            .build()
    }
    
    private fun provideOkHttpClient(): OkHttpClient {
        return OkHttpClient.Builder()
            .addInterceptor(HttpLoggingInterceptor().apply {
                level = HttpLoggingInterceptor.Level.BODY
            })
            .addInterceptor { chain ->
                val request = chain.request().newBuilder()
                    .addHeader("Authorization", "Bearer token")
                    .build()
                chain.proceed(request)
            }
            .build()
    }
    
    fun provideApiService(): ApiService {
        return provideRetrofit().create(ApiService::class.java)
    }
}
```

## Dependency Injection

### Dagger Hilt
```kotlin
// Application class
@HiltAndroidApp
class MyApplication : Application()

// Module definitions
@Module
@InstallIn(SingletonComponent::class)
object AppModule {
    @Provides
    @Singleton
    fun provideDatabase(@ApplicationContext context: Context): AppDatabase {
        return AppDatabase.getInstance(context)
    }
    
    @Provides
    @Singleton
    fun provideUserDao(database: AppDatabase): UserDao {
        return database.userDao()
    }
}

// ViewModel injection
@HiltViewModel
class MainViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() { }

// Activity/Fragment injection
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {
    @Inject
    lateinit var someDependency: SomeDependency
    
    @Inject
    lateinit var viewModel: MainViewModel
}
```

### Koin (Alternative)
```kotlin
val appModule = module {
    single { AppDatabase.getInstance(androidContext()) }
    single { get<AppDatabase>().userDao() }
    single<UserRepository> { UserRepositoryImpl(get()) }
    viewModel { MainViewModel(get()) }
}

class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        startKoin {
            androidContext(this@MyApplication)
            modules(appModule)
        }
    }
}
```

## Build Configuration

### build.gradle.kts (Module)
```kotlin
plugins {
    id("com.android.application")
    id("kotlin-android")
    id("kotlin-kapt")
    id("dagger.hilt.android.plugin")
}

android {
    compileSdk = 34
    
    defaultConfig {
        applicationId = "com.example.myapp"
        minSdk = 21
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"
        
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }
    
    buildTypes {
        release {
            isMinifyEnabled = true
            isShrinkResources = true
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
    
    kotlinOptions {
        jvmTarget = "1.8"
    }
    
    buildFeatures {
        compose = true
        viewBinding = true
    }
    
    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.4"
    }
}

dependencies {
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.10.0")
    
    // Compose
    implementation("androidx.compose.ui:ui:1.5.4")
    implementation("androidx.compose.material:material:1.5.4")
    implementation("androidx.compose.ui:ui-tooling-preview:1.5.4")
    implementation("androidx.activity:activity-compose:1.8.1")
    
    // Lifecycle
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.7.0")
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.7.0")
    
    // Room
    implementation("androidx.room:room-runtime:2.6.0")
    implementation("androidx.room:room-ktx:2.6.0")
    kapt("androidx.room:room-compiler:2.6.0")
    
    // Retrofit
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
    implementation("com.squareup.okhttp3:logging-interceptor:4.12.0")
    
    // Hilt
    implementation("com.google.dagger:hilt-android:2.48.1")
    kapt("com.google.dagger:hilt-compiler:2.48.1")
    
    // Testing
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
}
```

## Testing

### Unit Tests
```kotlin
class MainViewModelTest {
    
    @get:Rule
    val instantTaskExecutorRule = InstantTaskExecutorRule()
    
    private lateinit var viewModel: MainViewModel
    private val userRepository = mockk<UserRepository>()
    
    @Before
    fun setup() {
        viewModel = MainViewModel(userRepository)
    }
    
    @Test
    fun `load users should update ui state`() = runTest {
        // Given
        val users = listOf(User(1, "John", "john@example.com"))
        coEvery { userRepository.getUsers() } returns flowOf(users)
        
        // When
        viewModel.loadUsers()
        
        // Then
        viewModel.uiState.test {
            val loadingState = awaitItem()
            assertTrue(loadingState is UiState.Loading)
            
            val successState = awaitItem()
            assertTrue(successState is UiState.Success)
            assertEquals(users, (successState as UiState.Success).data)
        }
    }
}
```

### Instrumentation Tests
```kotlin
@HiltAndroidTest
class MainActivityTest {
    
    @get:Rule
    var hiltRule = HiltAndroidRule(this)
    
    @Test
    fun testUI() {
        // Launch activity
        val scenario = launchActivity<MainActivity>()
        
        // Perform actions and assertions
        onView(withId(R.id.button)).perform(click())
        onView(withText("Expected Text")).check(matches(isDisplayed()))
    }
}
```

## Performance & Optimization

### Memory Management
```kotlin
// Use by lazy for expensive initialization
val expensiveObject: ExpensiveObject by lazy {
    ExpensiveObject()
}

// Use remember in Compose for expensive calculations
@Composable
fun ExpensiveComposable(data: List<Data>) {
    val processedData = remember(data) {
        data.filter { it.isValid }.sortedBy { it.timestamp }
    }
    // Use processedData
}

// Proper lifecycle awareness
class MyFragment : Fragment() {
    private val viewModel: MainViewModel by viewModels()
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        
        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.uiState.collect { state ->
                    // Update UI - automatically cancelled when STOPPED
                }
            }
        }
    }
}
```

### ProGuard Rules
```pro
# Room
-keep class * extends androidx.room.RoomDatabase
-keep @androidx.room.Entity class *

# Retrofit
-keepattributes Signature, InnerClasses, EnclosingMethod
-keepattributes RuntimeVisibleAnnotations, RuntimeVisibleParameterAnnotations

-dontwarn retrofit2.**
-keep class retrofit2.** { *; }
-keepattributes Signature

# Kotlin
-keep class kotlin.Metadata { *; }
-dontwarn kotlin.**
-keep class kotlin.** { *; }

# Coroutines
-keep class kotlinx.coroutines.** { *; }
```

---

## Quick Reference Card

### Most Used Kotlin Features
```kotlin
// Null safety
val length = nullableString?.length ?: 0

// Extension functions
fun String.isEmail() = contains("@")

// Data classes
data class User(val name: String, val age: Int)

// Scope functions
user?.let { updateUI(it) }

// Collection operations
users.filter { it.isActive }.map { it.name }
```

### Essential Android Libraries
- **Jetpack Compose** - Modern UI toolkit
- **Room** - Database abstraction
- **Retrofit** - HTTP client
- **Dagger Hilt** - Dependency injection
- **Coroutines** - Asynchronous programming
- **ViewModel** - Lifecycle-aware data holder
- **DataStore** - Preferences storage

### Common Patterns
```kotlin
// Repository pattern with Flow
class UserRepository {
    fun getUsers(): Flow<List<User>> = flow {
        // Emit cached then network data
    }
}

// State management in Compose
var state by remember { mutableStateOf(initialState) }

// Coroutine error handling
viewModelScope.launch {
    try {
        val data = repository.getData()
    } catch (e: Exception) {
        // Handle error
    }
}
```

### Pro Tips
1. Use **Kotlin coroutines** instead of callbacks for async operations
2. Prefer **Jetpack Compose** for new projects
3. Use **Room** with **Flow** for reactive database operations
4. Implement **clean architecture** with Use Cases
5. Use **Dagger Hilt** for dependency injection
6. Write **unit tests** for ViewModels and Use Cases
7. Use **ProGuard/R8** for release builds
8. Follow **Material Design 3** guidelines

---

*Remember: Kotlin's null safety and expressive syntax combined with Android's modern Jetpack libraries create a powerful development environment. Always test on real devices and consider performance implications of your architectural choices!*