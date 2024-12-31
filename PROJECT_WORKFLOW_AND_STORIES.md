#  Project Workflow & Stories

Below is a breakdown of the Plant Care AI project roadmap into small, manageable user stories. Each story is scoped to fit within typical AI coding agent context limits. You can copy and paste this markdown directly into your project management system or documentation.

---

## Phase 1: Foundations & Tech Stack

---

### Story 1: Upgrade Dependencies & Project Structure
**Description**  
Update the Python environment, dependencies, and establish a modern project structure following best practices for ML applications.

**Technical Details**
```
project_root/
├── src/
│   ├── api/                 # FastAPI application
│   │   ├── __init__.py
│   │   ├── main.py         # FastAPI app initialization
│   │   ├── routes/         # API endpoints
│   │   └── models/         # Pydantic models
│   ├── ml/                 # Machine learning code
│   │   ├── __init__.py
│   │   ├── train.py        # Training scripts
│   │   ├── predict.py      # Inference logic
│   │   └── utils.py        # ML utilities
│   └── utils/              # Shared utilities
├── tests/                  # Test files
│   ├── unit/
│   ├── integration/
│   └── conftest.py
├── notebooks/              # Jupyter notebooks
├── configs/               # Configuration files
├── docker/                # Docker-related files
├── docs/                  # Documentation
└── scripts/               # Utility scripts
```

**Tasks**  
1. **Environment Setup**
   - Create `pyproject.toml` for modern Python packaging
   - Set up Python 3.11 virtual environment
   - Configure pre-commit hooks for code quality

2. **Dependency Updates**
   - Update core ML packages:
     ```toml
     [tool.poetry.dependencies]
     python = "^3.11"
     tensorflow = "^2.15.0"
     torch = "^2.1.0"  # Optional: for model comparison
     fastapi = "^0.109.0"
     uvicorn = "^0.25.0"
     python-multipart = "^0.0.6"  # For file uploads
     sqlalchemy = "^2.0.23"
     alembic = "^1.13.1"
     pydantic = "^2.5.3"
     ```
   - Development dependencies:
     ```toml
     [tool.poetry.dev-dependencies]
     pytest = "^7.4.3"
     black = "^23.12.1"
     isort = "^5.13.2"
     mypy = "^1.8.0"
     ruff = "^0.1.9"
     ```

3. **Code Quality Setup**
   - Configure `ruff` for linting
   - Set up `black` for code formatting
   - Configure `mypy` for type checking
   - Add `.gitignore` for Python/ML projects

4. **Documentation**
   - Create project README.md with:
     - Project overview
     - Setup instructions
     - Development guidelines
     - API documentation links
   - Set up Sphinx for auto-documentation

5. **Testing Infrastructure**
   - Configure pytest
   - Add basic test templates
   - Set up GitHub Actions for CI

**Acceptance Criteria**  
1. **Environment**
   - `poetry install` successfully creates a working environment
   - All dependencies resolve without conflicts
   - Pre-commit hooks run successfully

2. **Project Structure**
   - All directories created and properly organized
   - README.md provides clear setup instructions
   - Documentation builds successfully

3. **Testing**
   - `pytest` runs successfully (even with no tests yet)
   - GitHub Actions CI workflow passes
   - Code quality checks (black, ruff, mypy) pass

4. **Verification**
   - Basic test script imports all major dependencies
   - No dependency conflicts or warnings
   - Development environment matches production requirements

**Dependencies**
- None (this is the first story)

**Estimated Effort**
- 2-3 story points (1-2 days of work)

**Technical Notes**
- Consider using `poetry` for dependency management
- Document any deprecated features from old dependencies
- Note any breaking changes in API updates
- Consider containerization requirements
- Plan for future ML model versioning

**Risk Factors**
- Potential compatibility issues between ML libraries
- Breaking changes in major version updates
- System dependencies for ML libraries
- Platform-specific requirements

**Future Considerations**
- Container build optimization
- Development/production parity
- CI/CD pipeline requirements
- Model versioning strategy
- Package distribution method

---

### Story 2: FastAPI Backend Foundation
**Description**  
Create a robust FastAPI backend structure with proper configuration management, middleware setup, and basic health/diagnostic endpoints. This will serve as the foundation for all future API development.

**Technical Details**
```
src/api/
├── __init__.py
├── main.py                # FastAPI app initialization
├── config/
│   ├── __init__.py
│   ├── settings.py        # Environment-based configuration
│   └── constants.py       # Application constants
├── core/
│   ├── __init__.py
│   ├── security.py        # Authentication/authorization
│   ├── middleware.py      # Custom middleware
│   └── exceptions.py      # Custom exception handlers
├── routes/
│   ├── __init__.py
│   ├── health.py         # Health check endpoints
│   ├── auth.py           # Authentication routes
│   └── api_v1/           # Version 1 API routes
├── models/
│   ├── __init__.py
│   ├── base.py           # Base Pydantic models
│   └── health.py         # Health check models
└── utils/
    ├── __init__.py
    └── logging.py        # Logging configuration
```

**Tasks**  
1. **Configuration Management**
   ```python
   # config/settings.py
   from pydantic_settings import BaseSettings
   
   class Settings(BaseSettings):
       APP_NAME: str = "Plant Care AI"
       DEBUG: bool = False
       API_V1_STR: str = "/api/v1"
       CORS_ORIGINS: list[str] = ["*"]
       LOG_LEVEL: str = "INFO"
       
       class Config:
           env_file = ".env"
   ```

2. **Core FastAPI Setup**
   ```python
   # main.py
   from fastapi import FastAPI
   from fastapi.middleware.cors import CORSMiddleware
   
   app = FastAPI(
       title=settings.APP_NAME,
       openapi_url=f"{settings.API_V1_STR}/openapi.json"
   )
   
   # Middleware setup
   app.add_middleware(
       CORSMiddleware,
       allow_origins=settings.CORS_ORIGINS,
       allow_credentials=True,
       allow_methods=["*"],
       allow_headers=["*"],
   )
   ```

3. **Health Check Endpoints**
   ```python
   # routes/health.py
   from fastapi import APIRouter, Response
   from models.health import HealthCheck
   
   router = APIRouter()
   
   @router.get("/health", response_model=HealthCheck)
   async def health_check():
       return {
           "status": "healthy",
           "version": settings.VERSION,
           "timestamp": datetime.utcnow()
       }
   ```

4. **Custom Exception Handling**
   ```python
   # core/exceptions.py
   from fastapi import Request
   from fastapi.responses import JSONResponse
   
   class APIException(Exception):
       def __init__(self, status_code: int, message: str):
           self.status_code = status_code
           self.message = message
   
   @app.exception_handler(APIException)
   async def api_exception_handler(request: Request, exc: APIException):
       return JSONResponse(
           status_code=exc.status_code,
           content={"message": exc.message}
       )
   ```

5. **Logging Setup**
   ```python
   # utils/logging.py
   import logging
   from logging.handlers import RotatingFileHandler
   
   def setup_logging():
       logging.basicConfig(
           level=settings.LOG_LEVEL,
           format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
           handlers=[
               RotatingFileHandler(
                   'app.log',
                   maxBytes=10000000,
                   backupCount=5
               ),
               logging.StreamHandler()
           ]
       )
   ```

**Acceptance Criteria**  
1. **Basic Functionality**
   - Server starts without errors using `uvicorn`
   - Health check endpoint returns 200 OK
   - OpenAPI documentation is accessible
   - CORS allows specified origins

2. **Configuration**
   - Environment variables properly loaded
   - Different configs for dev/prod environments
   - Sensitive data properly handled

3. **Error Handling**
   - Custom exceptions properly caught and formatted
   - Appropriate error codes returned
   - Error responses follow API schema

4. **Logging**
   - Request logging enabled
   - Error logging captures stack traces
   - Logs rotate properly

5. **Testing**
   - Unit tests for health endpoints
   - Configuration loading tests
   - Error handling tests

**Dependencies**
- Story 1 (Project Setup) completed

**Estimated Effort**
- 2 story points (1 day of work)

**Technical Notes**
- Use async endpoints where beneficial
- Follow REST best practices
- Implement proper status codes
- Consider rate limiting early

**Risk Factors**
- CORS configuration security
- Error handling completeness
- Logging performance impact
- Configuration security

**Future Considerations**
- API versioning strategy
- Rate limiting implementation
- Authentication integration
- Metrics collection
- Performance monitoring

**Testing Requirements**
1. **Unit Tests**
   ```python
   # tests/api/test_health.py
   async def test_health_check():
       response = await client.get("/health")
       assert response.status_code == 200
       assert response.json()["status"] == "healthy"
   ```

2. **Integration Tests**
   ```python
   # tests/api/test_integration.py
   async def test_error_handling():
       response = await client.get("/non-existent")
       assert response.status_code == 404
       assert "message" in response.json()
   ```

---

### Story 3: Flutter Mobile App Skeleton
**Description**  
Create a well-structured Flutter mobile application with proper architecture, state management, and navigation setup. Implement core UI components and establish the foundation for future feature development.

**Technical Details**
```
plant_care_app/
├── android/                 # Android-specific files
├── ios/                    # iOS-specific files
├── lib/
│   ├── main.dart          # App entry point
│   ├── config/
│   │   ├── constants.dart  # App constants
│   │   ├── theme.dart     # App theming
│   │   └── routes.dart    # Route definitions
│   ├── core/
│   │   ├── api/           # API client
│   │   ├── storage/       # Local storage
│   │   └── utils/         # Shared utilities
│   ├── features/
│   │   ├── auth/          # Authentication
│   │   ├── home/          # Home screen
│   │   ├── plant_scan/    # Plant scanning
│   │   └── settings/      # App settings
│   ├── models/            # Data models
│   ├── widgets/           # Shared widgets
│   └── services/          # Business logic
├── test/                  # Test files
│   ├── unit/
│   ├── widget/
│   └── integration/
└── assets/               # Images, fonts, etc.
```

**Tasks**  
1. **Project Setup & Dependencies**
   ```yaml
   # pubspec.yaml
   dependencies:
     flutter:
       sdk: flutter
     # State Management
     flutter_bloc: ^8.1.3
     provider: ^6.1.1
     # Navigation
     go_router: ^13.0.0
     # Network & Storage
     dio: ^5.4.0
     shared_preferences: ^2.2.2
     # UI Components
     flutter_svg: ^2.0.9
     cached_network_image: ^3.3.0
     camera: ^0.10.5+5
     # Utils
     logger: ^2.0.2+1
     intl: ^0.19.0

   dev_dependencies:
     flutter_test:
       sdk: flutter
     flutter_lints: ^3.0.1
     mockito: ^5.4.4
     build_runner: ^2.4.7
   ```

2. **App Theme Setup**
   ```dart
   // lib/config/theme.dart
   class AppTheme {
     static ThemeData get light => ThemeData(
       primarySwatch: Colors.green,
       brightness: Brightness.light,
       textTheme: TextTheme(
         headlineLarge: TextStyle(
           fontSize: 32,
           fontWeight: FontWeight.bold,
         ),
         // ... other text styles
       ),
       // ... other theme properties
     );

     static ThemeData get dark => ThemeData(
       primarySwatch: Colors.green,
       brightness: Brightness.dark,
       // ... dark theme properties
     );
   }
   ```

3. **Navigation Setup**
   ```dart
   // lib/config/routes.dart
   final router = GoRouter(
     initialLocation: '/',
     routes: [
       GoRoute(
         path: '/',
         builder: (context, state) => const SplashScreen(),
       ),
       GoRoute(
         path: '/home',
         builder: (context, state) => const HomeScreen(),
       ),
       GoRoute(
         path: '/scan',
         builder: (context, state) => const PlantScanScreen(),
       ),
       // ... other routes
     ],
   );
   ```

4. **API Client Setup**
   ```dart
   // lib/core/api/api_client.dart
   class ApiClient {
     final Dio _dio;
     
     ApiClient() : _dio = Dio() {
       _dio.options.baseUrl = 'http://localhost:8000/api/v1';
       _dio.options.connectTimeout = const Duration(seconds: 5);
       _dio.options.receiveTimeout = const Duration(seconds: 3);
       
       _dio.interceptors.add(LogInterceptor(
         requestBody: true,
         responseBody: true,
       ));
     }
     
     Future<Response> get(String path) async {
       try {
         return await _dio.get(path);
       } catch (e) {
         // Handle errors
         rethrow;
       }
     }
     
     // ... other HTTP methods
   }
   ```

5. **Basic Screens**
   ```dart
   // lib/features/home/screens/home_screen.dart
   class HomeScreen extends StatelessWidget {
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         appBar: AppBar(
           title: Text('Plant Care AI'),
         ),
         body: Center(
           child: Column(
             mainAxisAlignment: MainAxisAlignment.center,
             children: [
               ElevatedButton(
                 onPressed: () => context.push('/scan'),
                 child: Text('Scan Plant'),
               ),
               // ... other buttons
             ],
           ),
         ),
       );
     }
   }
   ```

**Acceptance Criteria**  
1. **Project Structure**
   - Clean architecture principles followed
   - Feature-first directory organization
   - Proper separation of concerns

2. **Basic Functionality**
   - App builds and runs on both iOS and Android
   - Navigation works between placeholder screens
   - Theme applies consistently
   - Dark/light mode support

3. **Development Experience**
   - Hot reload works correctly
   - Linting rules enforced
   - Basic CI workflow passes

4. **Testing**
   - Widget tests for core components
   - Unit tests for utilities
   - Integration tests for navigation

**Dependencies**
- Story 1 & 2 (Backend setup) completed
- Flutter SDK installed
- Android Studio/Xcode setup

**Estimated Effort**
- 3 story points (2 days of work)

**Technical Notes**
- Use BLoC pattern for state management
- Implement proper error boundaries
- Follow Flutter performance best practices
- Consider accessibility from the start

**Testing Requirements**
1. **Widget Tests**
   ```dart
   // test/widget/home_screen_test.dart
   void main() {
     testWidgets('Home screen shows scan button', (tester) async {
       await tester.pumpWidget(MaterialApp(home: HomeScreen()));
       expect(find.text('Scan Plant'), findsOneWidget);
       expect(find.byType(ElevatedButton), findsOneWidget);
     });
   }
   ```

2. **Integration Tests**
   ```dart
   // test/integration/navigation_test.dart
   void main() {
     testWidgets('Can navigate to scan screen', (tester) async {
       await tester.pumpWidget(MyApp());
       await tester.tap(find.text('Scan Plant'));
       await tester.pumpAndSettle();
       expect(find.byType(PlantScanScreen), findsOneWidget);
     });
   }
   ```

---

### Story 4: Data Gathering Strategy & Dataset Creation
**Description**  
Develop a comprehensive strategy for collecting, organizing, and validating plant images and disease data. Focus on garden plants and cannabis varieties relevant to Colorado's climate, ensuring legal compliance and data quality.

**Technical Details**
```
data/
├── raw/                    # Original downloaded images
│   ├── garden_plants/      # Common garden plants
│   │   ├── tomatoes/
│   │   ├── peppers/
│   │   └── ...
│   ├── cannabis/          # Cannabis plants (legal sources)
│   │   ├── healthy/
│   │   └── diseases/
│   └── metadata/          # Source information
├── processed/             # Cleaned and validated data
│   ├── train/
│   ├── val/
│   └── test/
└── scripts/              # Data processing scripts
    ├── download/
    ├── validation/
    └── augmentation/
```

**Tasks**  
1. **Data Source Identification**
   ```python
   # data_sources.py
   PLANT_DATA_SOURCES = {
       'public_datasets': [
           {
               'name': 'PlantVillage Dataset',
               'url': 'https://github.com/spMohanty/PlantVillage-Dataset',
               'license': 'MIT',
               'categories': ['tomato', 'pepper', 'potato']
           },
           {
               'name': 'PlantDoc Dataset',
               'url': 'https://github.com/pratikkayal/PlantDoc-Dataset',
               'license': 'CC BY-SA 4.0',
               'categories': ['various']
           }
       ],
       'cannabis_sources': [
           {
               'name': 'OpenCannabis Dataset',
               'url': 'https://example.com/cannabis-dataset',
               'license': 'Research Use',
               'legal_states': ['CO', 'CA']
           }
       ]
   }
   ```

2. **Legal Compliance Check**
   ```python
   # compliance.py
   def validate_source_legality(source_info: dict) -> bool:
       """Verify if the data source is legal for use."""
       if 'cannabis' in source_info['categories']:
           return check_cannabis_compliance(source_info)
       return check_general_compliance(source_info)

   def check_cannabis_compliance(source_info: dict) -> bool:
       """Special checks for cannabis-related data."""
       return (
           source_info.get('legal_states') and
           'CO' in source_info['legal_states'] and
           source_info.get('license') in APPROVED_LICENSES
       )
   ```

3. **Data Collection Pipeline**
   ```python
   # collector.py
   class DataCollector:
       def __init__(self):
           self.download_path = Path('data/raw')
           self.logger = setup_logger()

       async def collect_from_source(self, source: dict):
           """Download images from a data source."""
           if not validate_source_legality(source):
               self.logger.error(f"Source {source['name']} failed compliance check")
               return

           async with aiohttp.ClientSession() as session:
               tasks = []
               for category in source['categories']:
                   task = self.download_category(session, category)
                   tasks.append(task)
               await asyncio.gather(*tasks)

       def validate_image(self, image_path: Path) -> bool:
           """Validate image quality and content."""
           try:
               img = Image.open(image_path)
               return (
                   img.size[0] >= 224 and
                   img.size[1] >= 224 and
                   img.mode in ['RGB', 'RGBA']
               )
           except Exception as e:
               self.logger.error(f"Invalid image {image_path}: {e}")
               return False
   ```

4. **Metadata Management**
   ```python
   # metadata.py
   @dataclass
   class ImageMetadata:
       file_path: Path
       source: str
       license: str
       plant_type: str
       condition: str
       capture_date: Optional[datetime]
       resolution: Tuple[int, int]
       hash: str  # For duplicate detection

   class MetadataManager:
       def __init__(self, db_path: Path):
           self.conn = sqlite3.connect(db_path)
           self.setup_database()

       def add_image(self, metadata: ImageMetadata):
           """Store image metadata in SQLite database."""
           with self.conn:
               self.conn.execute("""
                   INSERT INTO images (
                       file_path, source, license, plant_type,
                       condition, capture_date, resolution, hash
                   ) VALUES (?, ?, ?, ?, ?, ?, ?, ?)
               """, astuple(metadata))
   ```

**Acceptance Criteria**  
1. **Data Sources**
   - Minimum 1000 images per plant category
   - At least 100 images per disease type
   - Properly documented source attribution
   - Legal compliance verification

2. **Image Quality**
   - Minimum resolution: 224x224 pixels
   - Clear, focused images
   - Proper lighting conditions
   - Relevant plant features visible

3. **Dataset Balance**
   - Equal representation of healthy/diseased plants
   - Balanced distribution across species
   - Various growth stages represented
   - Different environmental conditions

4. **Documentation**
   - Source attribution for all images
   - Usage rights clearly documented
   - Data collection methodology detailed
   - Quality control process documented

**Dependencies**
- Story 1 (Project Setup) completed
- Legal review of data usage rights
- Storage infrastructure ready

**Estimated Effort**
- 5 story points (3-4 days of work)

**Technical Notes**
- Use async downloads for efficiency
- Implement robust error handling
- Consider rate limiting for API sources
- Use checksums for duplicate detection

**Data Quality Checks**
```python
# quality_checks.py
def validate_dataset(data_dir: Path):
    """Run comprehensive dataset validation."""
    checks = {
        'resolution': check_image_resolution,
        'format': check_image_format,
        'corruption': check_image_corruption,
        'duplicates': check_duplicates,
        'balance': check_class_balance
    }
    
    results = {}
    for check_name, check_func in checks.items():
        results[check_name] = check_func(data_dir)
    
    return results

def check_class_balance(data_dir: Path) -> dict:
    """Verify class distribution is balanced."""
    class_counts = defaultdict(int)
    for image_path in data_dir.rglob('*.jpg'):
        class_name = image_path.parent.name
        class_counts[class_name] += 1
    
    return {
        'distribution': dict(class_counts),
        'balanced': max(class_counts.values()) / min(class_counts.values()) < 1.5
    }
```

**Risk Factors**
- Data source availability
- Legal compliance issues
- Quality inconsistency
- Class imbalance
- Storage constraints
- Download rate limits

**Future Considerations**
- Automated data collection pipeline
- Real-time dataset updates
- Community contributions
- Data versioning strategy
- Extended plant species coverage

**Performance Requirements**
- Download speed optimization
- Efficient storage utilization
- Quick metadata queries
- Scalable processing pipeline

---

### Story 5: Web Scraping & Download Pipeline
**Description**  
Implement a robust, scalable web scraping and image download system that respects rate limits, handles errors gracefully, and ensures data quality. The system should support multiple data sources and maintain detailed provenance information.

**Technical Details**
```
src/data_collection/
├── scrapers/
│   ├── __init__.py
│   ├── base.py           # Base scraper class
│   ├── plant_village.py  # PlantVillage specific scraper
│   └── plant_doc.py      # PlantDoc specific scraper
├── downloaders/
│   ├── __init__.py
│   ├── async_downloader.py
│   └── rate_limiter.py
├── validators/
│   ├── __init__.py
│   ├── image_validator.py
│   └── metadata_validator.py
└── pipeline/
    ├── __init__.py
    ├── orchestrator.py
    └── status_tracker.py
```

**Tasks**  
1. **Base Scraper Implementation**
   ```python
   # scrapers/base.py
   class BaseScraper:
       def __init__(self, config: ScraperConfig):
           self.config = config
           self.session = aiohttp.ClientSession()
           self.rate_limiter = RateLimiter(
               calls=config.max_calls,
               period=config.time_period
           )
           self.logger = setup_logger(self.__class__.__name__)

       async def scrape(self) -> List[ImageMetadata]:
           """Template method for scraping implementation."""
           raise NotImplementedError

       async def validate_url(self, url: str) -> bool:
           """Validate URL before scraping."""
           try:
               async with self.session.head(url) as response:
                   return response.status == 200
           except Exception as e:
               self.logger.error(f"URL validation failed: {e}")
               return False

       async def __aenter__(self):
           return self

       async def __aexit__(self, exc_type, exc_val, exc_tb):
           await self.session.close()
   ```

2. **Rate Limiter Implementation**
   ```python
   # downloaders/rate_limiter.py
   class RateLimiter:
       def __init__(self, calls: int, period: float):
           self.calls = calls
           self.period = period
           self.timestamps = deque()
           self._lock = asyncio.Lock()

       async def acquire(self):
           """Acquire permission to make a request."""
           async with self._lock:
               now = time.time()
               
               # Remove old timestamps
               while self.timestamps and now - self.timestamps[0] > self.period:
                   self.timestamps.popleft()
               
               if len(self.timestamps) >= self.calls:
                   sleep_time = self.timestamps[0] + self.period - now
                   await asyncio.sleep(sleep_time)
               
               self.timestamps.append(now)
   ```

3. **Async Downloader**
   ```python
   # downloaders/async_downloader.py
   class AsyncDownloader:
       def __init__(self, concurrency: int = 5):
           self.semaphore = asyncio.Semaphore(concurrency)
           self.session = aiohttp.ClientSession()
           self.logger = setup_logger('AsyncDownloader')

       async def download_image(self, url: str, save_path: Path) -> bool:
           """Download image with retry logic."""
           async with self.semaphore:
               for attempt in range(3):  # 3 retries
                   try:
                       async with self.session.get(url) as response:
                           if response.status == 200:
                               content = await response.read()
                               await self._save_image(content, save_path)
                               return True
                   except Exception as e:
                           self.logger.error(f"Download failed: {e}")
                           await asyncio.sleep(1 * attempt)  # Exponential backoff
               return False

       async def _save_image(self, content: bytes, path: Path):
           """Save image with atomic write."""
           temp_path = path.with_suffix('.tmp')
           try:
               async with aiofiles.open(temp_path, 'wb') as f:
                   await f.write(content)
               temp_path.rename(path)
           except Exception as e:
               self.logger.error(f"Save failed: {e}")
               if temp_path.exists():
                   temp_path.unlink()
               raise
   ```

4. **Pipeline Orchestrator**
   ```python
   # pipeline/orchestrator.py
   class DataPipeline:
       def __init__(self, config: PipelineConfig):
           self.config = config
           self.downloader = AsyncDownloader(config.concurrency)
           self.scrapers = self._init_scrapers()
           self.status_tracker = StatusTracker()
           self.logger = setup_logger('DataPipeline')

       async def run(self):
           """Execute the full pipeline."""
           try:
               for scraper in self.scrapers:
                   metadata_list = await scraper.scrape()
                   download_tasks = [
                       self.process_item(item)
                       for item in metadata_list
                   ]
                   results = await asyncio.gather(
                       *download_tasks,
                       return_exceptions=True
                   )
                   self._handle_results(results)
           finally:
               await self.cleanup()

       async def process_item(self, metadata: ImageMetadata):
           """Process a single image with validation."""
           if not await self.validate_metadata(metadata):
               return False

           success = await self.downloader.download_image(
               metadata.url,
               self.config.save_dir / metadata.filename
           )

           if success:
               await self.status_tracker.mark_success(metadata)
           else:
               await self.status_tracker.mark_failure(metadata)

           return success
   ```

**Acceptance Criteria**  
1. **Functionality**
   - Successfully scrapes from all configured sources
   - Downloads images with proper error handling
   - Respects rate limits and robots.txt
   - Maintains data provenance

2. **Performance**
   - Processes 1000 images/hour minimum
   - Memory usage stays under 1GB
   - CPU usage remains reasonable
   - Network bandwidth efficiently used

3. **Reliability**
   - Handles network errors gracefully
   - Implements retry logic
   - Maintains consistent state
   - Recoverable from interruptions

4. **Monitoring**
   - Logs all operations
   - Tracks success/failure rates
   - Reports progress in real-time
   - Alerts on critical failures

**Dependencies**
- Story 4 (Data Strategy) completed
- Storage system ready
- Network access configured

**Estimated Effort**
- 5 story points (3-4 days of work)

**Technical Notes**
- Use async/await for I/O operations
- Implement proper connection pooling
- Consider using proxy rotation
- Implement checkpointing

**Testing Requirements**
```python
# tests/test_scraper.py
class TestScraper:
    async def test_rate_limiting(self):
        limiter = RateLimiter(calls=2, period=1.0)
        start = time.time()
        await limiter.acquire()
        await limiter.acquire()
        await limiter.acquire()  # Should wait
        duration = time.time() - start
        assert duration >= 1.0

    async def test_download_retry(self):
        downloader = AsyncDownloader()
        with aioresponses() as m:
            m.get('http://test.com/img.jpg', 
                 status=500, 
                 repeat=2)
            m.get('http://test.com/img.jpg', 
                 status=200, 
                 body=b'image_content')
            success = await downloader.download_image(
                'http://test.com/img.jpg',
                Path('test.jpg')
            )
            assert success
```

**Risk Factors**
- Website blocking/rate limiting
- Data source API changes
- Network reliability
- Storage capacity
- Processing bottlenecks

**Monitoring Setup**
```python
# pipeline/monitoring.py
class PipelineMonitor:
    def measure_performance(
        self,
        pipeline: DataPipeline
    ) -> Dict[str, float]:
        """Measure pipeline performance metrics."""
        return {
            "images_processed": pipeline.status_tracker.success_count,
            "success_rate": pipeline.status_tracker.success_rate,
            "average_download_time": pipeline.status_tracker.avg_download_time,
            "errors": pipeline.status_tracker.error_count
        }
```

**Performance Optimization**
- Connection pooling
- Batch processing
- Caching mechanisms
- Resource cleanup

---

### Story 6: Data Labeling & Annotation System
**Description**  
Create a comprehensive data labeling and annotation system for plant images, supporting both automated and manual labeling processes. The system should handle multiple label types (plant species, diseases, growth stages) and maintain high data quality through validation.

**Technical Details**
```
src/labeling/
├── core/
│   ├── __init__.py
│   ├── label_schema.py      # Label definitions
│   ├── annotation.py        # Annotation tools
│   └── validation.py        # Quality checks
├── auto_labeling/
│   ├── __init__.py
│   ├── classifier.py        # Pre-trained classifier
│   └── confidence.py        # Confidence scoring
├── manual_labeling/
│   ├── __init__.py
│   ├── ui/                  # Streamlit UI
│   └── validation.py        # Human validation
├── export/
│   ├── __init__.py
│   ├── coco.py             # COCO format export
│   └── yolo.py             # YOLO format export
└── quality/
    ├── __init__.py
    ├── metrics.py          # Quality metrics
    └── consensus.py        # Label agreement
```

**Tasks**  
1. **Label Schema Definition**
   ```python
   # core/label_schema.py
   from enum import Enum
   from typing import List, Optional
   from pydantic import BaseModel

   class PlantType(str, Enum):
       TOMATO = "tomato"
       PEPPER = "pepper"
       CANNABIS = "cannabis"
       # ... other plants

   class DiseaseType(str, Enum):
       HEALTHY = "healthy"
       LEAF_SPOT = "leaf_spot"
       BLIGHT = "blight"
       POWDERY_MILDEW = "powdery_mildew"
       # ... other diseases

   class GrowthStage(str, Enum):
       SEEDLING = "seedling"
       VEGETATIVE = "vegetative"
       FLOWERING = "flowering"
       FRUITING = "fruiting"

   class PlantAnnotation(BaseModel):
       image_id: str
       plant_type: PlantType
       diseases: List[DiseaseType]
       growth_stage: GrowthStage
       confidence: float
       annotator_id: Optional[str]
       verification_status: bool = False
       metadata: dict = {}
   ```

2. **Annotation Interface**
   ```python
   # manual_labeling/ui/app.py
   import streamlit as st
   from PIL import Image
   
   class AnnotationUI:
       def __init__(self):
           self.current_image = None
           self.annotations = []
   
       def render(self):
           st.title("Plant Disease Annotation Tool")
           
           # Image display
           col1, col2 = st.columns([2, 1])
           with col1:
               if self.current_image:
                   st.image(self.current_image)
           
           # Annotation controls
           with col2:
               plant_type = st.selectbox(
                   "Plant Type",
                   options=[e.value for e in PlantType]
               )
               
               diseases = st.multiselect(
                   "Diseases",
                   options=[e.value for e in DiseaseType]
               )
               
               growth_stage = st.selectbox(
                   "Growth Stage",
                   options=[e.value for e in GrowthStage]
               )
               
               confidence = st.slider(
                   "Confidence",
                   min_value=0.0,
                   max_value=1.0,
                   value=0.9
               )
               
               if st.button("Save Annotation"):
                   self.save_annotation(
                       plant_type,
                       diseases,
                       growth_stage,
                       confidence
                   )
   ```

3. **Automated Pre-labeling**
   ```python
   # auto_labeling/classifier.py
   class AutoLabeler:
       def __init__(self, model_path: str):
           self.model = tf.keras.models.load_model(model_path)
           self.threshold = 0.8
   
       async def label_image(self, image_path: Path) -> PlantAnnotation:
           """Generate automatic labels with confidence scores."""
           try:
               img = self.preprocess_image(image_path)
               predictions = self.model.predict(img)
               
               if max(predictions) < self.threshold:
                   return None  # Confidence too low
                   
               return PlantAnnotation(
                   image_id=str(image_path),
                   plant_type=self.get_plant_type(predictions),
                   diseases=self.get_diseases(predictions),
                   confidence=float(max(predictions)),
                   annotator_id="auto_labeler"
               )
           except Exception as e:
               logger.error(f"Auto-labeling failed: {e}")
               return None
   ```

4. **Quality Validation**
   ```python
   # quality/consensus.py
   class ConsensusValidator:
       def __init__(self, min_annotators: int = 2):
           self.min_annotators = min_annotators
   
       def validate_annotation(
           self,
           annotations: List[PlantAnnotation]
       ) -> Optional[PlantAnnotation]:
           """Validate annotations through consensus."""
           if len(annotations) < self.min_annotators:
               return None
           
           # Calculate agreement scores
           plant_types = Counter(a.plant_type for a in annotations)
           diseases = Counter(
               disease
               for a in annotations
               for disease in a.diseases
           )
           
           # Create consensus annotation
           return PlantAnnotation(
               image_id=annotations[0].image_id,
               plant_type=plant_types.most_common(1)[0][0],
               diseases=[d for d, c in diseases.items()
                        if c >= self.min_annotators],
               confidence=self._calculate_confidence(annotations),
               verification_status=True
           )
   ```

**Acceptance Criteria**  
1. **Labeling Interface**
   - Intuitive UI for manual labeling
   - Keyboard shortcuts for efficiency
   - Batch labeling capabilities
   - Progress tracking

2. **Data Quality**
   - Inter-annotator agreement >85%
   - Automated validation checks
   - Confidence scoring
   - Quality metrics tracking

3. **Export Formats**
   - COCO format support
   - YOLO format support
   - Custom format flexibility
   - Metadata preservation

4. **Testing**
   - Widget tests for core components
   - Unit tests for utilities
   - Integration tests for annotation

**Dependencies**
- Stories 4-5 completed
- Labeled dataset subset for auto-labeler
- Annotation guidelines documented

**Estimated Effort**
- 8 story points (5-6 days of work)

**Technical Notes**
- Use Streamlit for rapid UI development
- Implement caching for performance
- Consider distributed annotation
- Version control annotations

**Testing Requirements**
```python
# tests/test_annotation.py
class TestAnnotation:
    def test_schema_validation(self):
        """Test annotation schema validation."""
        valid_annotation = PlantAnnotation(
            image_id="test1.jpg",
            plant_type=PlantType.TOMATO,
            diseases=[DiseaseType.HEALTHY],
            growth_stage=GrowthStage.VEGETATIVE,
            confidence=0.95
        )
        assert valid_annotation is not None

    def test_consensus_validation(self):
        """Test annotation consensus logic."""
        annotations = [
            PlantAnnotation(...),  # Annotator 1
            PlantAnnotation(...),  # Annotator 2
            PlantAnnotation(...),  # Annotator 3
        ]
        validator = ConsensusValidator(min_annotators=2)
        consensus = validator.validate_annotation(annotations)
        assert consensus.verification_status is True
```

**Quality Metrics**
```python
# quality/metrics.py
class AnnotationMetrics:
    def calculate_metrics(
        self,
        annotations: List[PlantAnnotation]
    ) -> Dict[str, float]:
        """Calculate annotation quality metrics."""
        return {
            "inter_annotator_agreement": self._calculate_iaa(),
            "completion_rate": self._calculate_completion(),
            "average_confidence": self._calculate_confidence(),
            "verification_rate": self._calculate_verification()
        }
```

**Risk Factors**
- Annotation consistency
- Annotator availability
- UI performance
- Data quality maintenance
- Storage requirements

**Future Considerations**
- Active learning integration
- Multi-language support
- Mobile annotation interface
- Real-time collaboration
- AI-assisted annotation

---

- All images are consistently resized and normalized.

---

### Story 7: Data Cleansing & Augmentation
**Description**  
Implement a robust data cleaning and augmentation pipeline to prepare the raw dataset for model training. The system should handle image validation, cleaning, and generate augmented samples to improve model robustness.

**Technical Details**
```
src/data_processing/
├── cleaning/
│   ├── __init__.py
│   ├── validators/
│   │   ├── __init__.py
│   │   ├── image.py        # Image quality checks
│   │   └── metadata.py     # Metadata validation
│   └── cleaners/
│       ├── __init__.py
│       ├── duplicates.py   # Duplicate detection
│       └── corrupted.py    # Corrupted file handling
├── augmentation/
│   ├── __init__.py
│   ├── transforms/
│   │   ├── __init__.py
│   │   ├── geometric.py    # Geometric transforms
│   │   ├── color.py       # Color augmentations
│   │   └── noise.py       # Noise injection
│   └── policies/
│       ├── __init__.py
│       └── auto_augment.py # AutoAugment policies
└── pipeline/
    ├── __init__.py
    ├── processor.py       # Main pipeline
    └── reporting.py       # Quality reports
```

**Tasks**  
1. **Image Validation System**
   ```python
   # cleaning/validators/image.py
   class ImageValidator:
       def __init__(self, config: ValidationConfig):
           self.config = config
           self.logger = setup_logger(__name__)
   
       async def validate_image(
           self,
           image_path: Path
       ) -> ValidationResult:
           """Validate image quality and format."""
           try:
               with Image.open(image_path) as img:
                   # Check image format
                   if img.format not in ["JPEG", "PNG"]:
                       return ValidationResult(
                           valid=False,
                           reason="Invalid format"
                       )
                   
                   # Check dimensions
                   if not self._check_dimensions(img):
                       return ValidationResult(
                           valid=False,
                           reason="Invalid dimensions"
                       )
                   
                   # Check bit depth
                   if not self._check_bit_depth(img):
                       return ValidationResult(
                           valid=False,
                           reason="Invalid bit depth"
                       )
                   
                   # Check for corruption
                   if not self._check_corruption(img):
                       return ValidationResult(
                           valid=False,
                           reason="Corrupted image"
                       )
                   
                   return ValidationResult(valid=True)
           except Exception as e:
               self.logger.error(f"Validation error: {e}")
               return ValidationResult(
                   valid=False,
                   reason=str(e)
               )
   
       def _check_dimensions(self, img: Image.Image) -> bool:
           """Check if image dimensions are valid."""
           width, height = img.size
           return (
               width >= self.config.min_dimension and
               height >= self.config.min_dimension and
               width <= self.config.max_dimension and
               height <= self.config.max_dimension
           )
   ```

2. **Augmentation Pipeline**
   ```python
   # augmentation/transforms/geometric.py
   class GeometricTransforms:
       def __init__(self, config: AugmentConfig):
           self.config = config
           self.transforms = A.Compose([
               A.HorizontalFlip(p=0.5),
               A.VerticalFlip(p=0.5),
               A.RandomRotate90(p=0.5),
               A.ShiftScaleRotate(
                   shift_limit=0.1,
                   scale_limit=0.2,
                   rotate_limit=45,
                   p=0.5
               ),
               A.RandomResizedCrop(
                   height=config.image_size,
                   width=config.image_size,
                   scale=(0.8, 1.0),
                   p=0.5
               )
           ])
   
       def __call__(
           self,
           image: np.ndarray
       ) -> List[np.ndarray]:
           """Apply geometric transformations."""
           results = []
           for _ in range(self.config.num_augmentations):
               augmented = self.transforms(image=image)
               results.append(augmented["image"])
           return results
   ```

3. **Duplicate Detection**
   ```python
   # cleaning/cleaners/duplicates.py
   class DuplicateDetector:
       def __init__(self, threshold: float = 0.95):
           self.threshold = threshold
           self.feature_extractor = self._setup_extractor()
           self.index = faiss.IndexFlatL2(2048)
   
       async def find_duplicates(
           self,
           image_paths: List[Path]
       ) -> List[List[Path]]:
           """Find duplicate images using embeddings."""
           # Extract features
           features = []
           for path in tqdm(image_paths):
               feat = await self._extract_features(path)
               if feat is not None:
                   features.append(feat)
   
           # Build FAISS index
           features_array = np.array(features)
           self.index.add(features_array)
   
           # Find duplicates
           D, I = self.index.search(
               features_array,
               k=5
           )
   
           # Group duplicates
           duplicates = []
           seen = set()
           for i, indices in enumerate(I):
               if i in seen:
                   continue
               
               group = []
               for j, dist in zip(indices, D[i]):
                   if dist < self.threshold:
                       group.append(image_paths[j])
                       seen.add(j)
               
               if len(group) > 1:
                   duplicates.append(group)
   
           return duplicates
   ```

4. **Processing Pipeline**
   ```python
   # pipeline/processor.py
   class DataProcessor:
       def __init__(self, config: ProcessingConfig):
           self.config = config
           self.validator = ImageValidator(config.validation)
           self.cleaner = ImageCleaner(config.cleaning)
           self.augmentor = ImageAugmentor(config.augmentation)
           self.logger = setup_logger(__name__)
   
       async def process_dataset(
           self,
           input_dir: Path,
           output_dir: Path
       ) -> ProcessingReport:
           """Process entire dataset."""
           try:
               # Validate images
               valid_images = await self._validate_images(
                   input_dir
               )
               
               # Clean dataset
               cleaned_images = await self._clean_dataset(
                   valid_images
               )
               
               # Generate augmentations
               augmented_images = await self._augment_dataset(
                   cleaned_images,
                   output_dir
               )
               
               # Generate report
               report = self._generate_report(
                   valid_images,
                   cleaned_images,
                   augmented_images
               )
               
               return report
           except Exception as e:
               self.logger.error(f"Processing failed: {e}")
               raise ProcessingError(str(e))
   ```

**Acceptance Criteria**  
1. **Data Validation**
   - Image format verification
   - Dimension constraints
   - Quality thresholds
   - Metadata validation

2. **Data Cleaning**
   - Duplicate detection
   - Corrupted file removal
   - Consistent naming
   - Proper organization

3. **Augmentation**
   - Multiple transform types
   - Configurable policies
   - Quality preservation
   - Balanced distribution

4. **Documentation**
   - Processing pipeline guide
   - Configuration options
   - Quality metrics
   - Error handling

**Dependencies**
- Stories 4-6 completed
- Raw dataset available
- Storage infrastructure
- Processing capacity

**Estimated Effort**
- 8 story points (5-6 days of work)

**Technical Notes**
- Use albumentations for augmentation
- Implement async processing
- Use FAISS for similarity search
- Implement checkpointing

**Testing Requirements**
```python
# tests/test_processing.py
class TestProcessing:
    async def test_image_validation(self):
        """Test image validation."""
        validator = ImageValidator(config)
        
        # Test valid image
        result = await validator.validate_image(
            "tests/data/valid.jpg"
        )
        assert result.valid
        
        # Test invalid image
        result = await validator.validate_image(
            "tests/data/corrupted.jpg"
        )
        assert not result.valid

    async def test_augmentation(self):
        """Test augmentation pipeline."""
        augmentor = GeometricTransforms(config)
        image = load_test_image()
        
        results = augmentor(image)
        assert len(results) == config.num_augmentations
        
        # Check quality
        for aug_img in results:
            assert aug_img.shape == image.shape
            assert aug_img.dtype == image.dtype
```

**Quality Metrics**
```python
# pipeline/reporting.py
class QualityMetrics:
    def calculate_metrics(
        self,
        dataset_path: Path
    ) -> Dict[str, float]:
        """Calculate dataset quality metrics."""
        return {
            "total_images": self._count_images(),
            "unique_images": self._count_unique(),
            "avg_quality_score": self._calculate_quality(),
            "class_balance": self._calculate_balance(),
            "augmentation_ratio": self._calculate_aug_ratio()
        }
```

**Risk Factors**
- Processing time
- Storage requirements
- Quality consistency
- Class imbalance
- Resource utilization

**Future Considerations**
- Online augmentation
- Advanced cleaning
- Quality scoring
- Automated balancing
- Distributed processing

---
### Story 8: Data Ingestion & Preprocessing Script
**Description**  
Implement a Python script that **loads** the **processed** dataset (from Phase 2) and applies uniform preprocessing (e.g., resizing, normalization).

**Technical Details**
```
src/data_ingestion/
├── ingestion/
│   ├── __init__.py
│   ├── loaders/
│   │   ├── __init__.py
│   │   ├── image.py       # Image loading utilities
│   │   └── metadata.py    # Metadata parsing
│   └── caching/
│       ├── __init__.py
│       ├── disk.py        # Disk-based caching
│       └── memory.py      # Memory caching
├── preprocessing/
│   ├── __init__.py
│   ├── transforms/
│   │   ├── __init__.py
│   │   ├── resize.py      # Image resizing
│   │   └── normalize.py   # Normalization
│   └── pipeline/
│       ├── __init__.py
│       └── processor.py   # Processing pipeline
└── datasets/
    ├── __init__.py
    ├── base.py           # Base dataset class
    └── plant.py          # Plant dataset implementation
```

**Tasks**  
1. **Data Loading System**
   ```python
   # ingestion/loaders/image.py
   class ImageLoader:
       def __init__(
           self,
           config: LoaderConfig,
           cache_manager: Optional[CacheManager] = None
       ):
           self.config = config
           self.cache = cache_manager
           self.logger = setup_logger(__name__)
   
       async def load_image(
           self,
           image_path: Path,
           return_metadata: bool = False
       ) -> Union[np.ndarray, Tuple[np.ndarray, Dict]]:
           """Load image with optional caching."""
           try:
               # Check cache first
               if self.cache:
                   cached = await self.cache.get(image_path)
                   if cached is not None:
                       return cached
               
               # Load image
               image = cv2.imread(str(image_path))
               if image is None:
                   raise LoaderError(
                       f"Failed to load {image_path}"
                   )
               
               # Convert to RGB
               image = cv2.cvtColor(
                   image,
                   cv2.COLOR_BGR2RGB
               )
               
               # Load metadata if requested
               if return_metadata:
                   metadata = self._load_metadata(image_path)
                   result = (image, metadata)
               else:
                   result = image
               
               # Cache result
               if self.cache:
                   await self.cache.set(
                       image_path,
                       result
                   )
               
               return result
           except Exception as e:
               self.logger.error(f"Loading error: {e}")
               return LoaderError(str(e))
   ```

2. **Preprocessing Pipeline**
   ```python
   # preprocessing/pipeline/processor.py
   class PreprocessingPipeline:
       def __init__(self, config: PreprocessConfig):
           self.config = config
           self.transforms = self._setup_transforms()
           self.logger = setup_logger(__name__)
   
       def _setup_transforms(self) -> nn.Module:
           """Setup preprocessing transforms."""
           return nn.Sequential(
               # Resize to target size
               T.Resize(
                   self.config.image_size,
                   interpolation=InterpolationMode.BICUBIC
               ),
               # Center crop
               T.CenterCrop(self.config.image_size),
               # Convert to tensor
               T.ToTensor(),
               # Normalize
               T.Normalize(
                   mean=self.config.normalize_mean,
                   std=self.config.normalize_std
               )
           )
   
       def process_batch(
           self,
           images: List[np.ndarray]
       ) -> torch.Tensor:
           """Process a batch of images."""
           try:
               # Convert to PIL
               pil_images = [
                   Image.fromarray(img)
                   for img in images
               ]
               
               # Apply transforms
               tensors = []
               for img in pil_images:
                   tensor = self.transforms(img)
                   tensors.append(tensor)
               
               # Stack into batch
               batch = torch.stack(tensors)
               
               return batch
           except Exception as e:
               self.logger.error(f"Processing error: {e}")
               raise ProcessingError(str(e))
   ```

3. **Dataset Implementation**
   ```python
   # datasets/plant.py
   class PlantDataset(Dataset):
       def __init__(
           self,
           data_dir: Path,
           split: str = "train",
           transform: Optional[A.Compose] = None,
           cache_size: int = 1000
       ):
           self.data_dir = data_dir
           self.split = split
           self.transform = transform
           
           # Setup components
           self.loader = ImageLoader(
               LoaderConfig(),
               CacheManager(cache_size)
           )
           self.processor = PreprocessingPipeline(
               PreprocessConfig()
           )
           
           # Load annotations
           self.annotations = self._load_annotations()
           
           # Setup samplers
           self.sampler = self._setup_sampler()
   
       def __getitem__(self, idx: int) -> Dict[str, torch.Tensor]:
           """Get a single sample."""
           try:
               # Get annotation
               annotation = self.annotations[idx]
               
               # Load image
               image = self.loader.load_image(
                   annotation.image_id
               )
               
               # Apply preprocessing
               processed = self.processor.process_batch(
                   [image]
               )[0]
               
               # Apply additional transforms
               if self.transform:
                   processed = self.transform(processed)
               
               return {
                   "image": processed,
                   "label": annotation.label,
                   "metadata": annotation.metadata
               }
           except Exception as e:
               self.logger.error(
                   f"Failed to load item {idx}: {e}"
               )
               return self.__getitem__(
                   (idx + 1) % len(self)
               )
   ```

**Acceptance Criteria**  
1. **Data Loading**
   - Efficient image loading
   - Metadata parsing
   - Error handling
   - Caching support

2. **Preprocessing**
   - Consistent resizing
   - Proper normalization
   - Batch processing
   - Memory efficiency

3. **Dataset Interface**
   - PyTorch compatibility
   - Efficient sampling
   - Augmentation support
   - Error recovery

4. **Performance**
   - Fast data loading
   - Memory optimization
   - CPU utilization
   - I/O efficiency

**Dependencies**
- Stories 4-7 completed
- Processed dataset available
- Storage infrastructure
- Processing capacity

**Estimated Effort**
- 5 story points (3-4 days of work)

**Technical Notes**
- Use OpenCV for image loading
- Implement memory mapping
- Use async I/O
- Enable multiprocessing

**Testing Requirements**
```python
# tests/test_ingestion.py
class TestIngestion:
    async def test_image_loading(self):
        """Test image loading system."""
        loader = ImageLoader(config)
        
        # Test valid image
        image = await loader.load_image(
            "tests/data/valid.jpg"
        )
        assert isinstance(image, np.ndarray)
        assert image.shape == (224, 224, 3)
        
        # Test caching
        cached = await loader.load_image(
            "tests/data/valid.jpg"
        )
        assert np.array_equal(image, cached)

    def test_preprocessing(self):
        """Test preprocessing pipeline."""
        pipeline = PreprocessingPipeline(config)
        image = load_test_image()
        
        processed = pipeline.process_batch([image])
        assert isinstance(processed, torch.Tensor)
        assert processed.shape == (1, 3, 224, 224)
        assert processed.dtype == torch.float32
```

**Performance Metrics**
```python
# ingestion/monitoring.py
class PerformanceMonitor:
    def measure_performance(
        self,
        dataset: PlantDataset
    ) -> Dict[str, float]:
        """Measure dataset performance metrics."""
        return {
            "load_time": self._measure_load_time(),
            "memory_usage": self._measure_memory(),
            "cache_hits": self._measure_cache_hits(),
            "throughput": self._measure_throughput()
        }
```

**Risk Factors**
- I/O bottlenecks
- Memory constraints
- Processing speed
- Cache efficiency
- Error handling

**Future Considerations**
- Distributed loading
- Smart caching
- Online preprocessing
- Custom samplers
- Memory optimization

---

### Story 9: Model Training Pipeline
**Description**  
Develop a robust, reproducible training pipeline for plant classification and disease detection models. The system should support experiment tracking, hyperparameter optimization, and model versioning while maintaining high performance and scalability.

**Technical Details**
```
src/training/
├── core/
│   ├── __init__.py
│   ├── config.py          # Training configurations
│   ├── dataset.py         # Data loading/processing
│   └── metrics.py         # Custom metrics
├── models/
│   ├── __init__.py
│   ├── architectures/     # Model definitions
│   │   ├── resnet.py
│   │   └── efficientnet.py
│   ├── losses/           # Custom loss functions
│   └── optimizers/       # Custom optimizers
├── augmentation/
│   ├── __init__.py
│   ├── transforms.py     # Data augmentation
│   └── mixup.py         # Mixup augmentation
├── training/
│   ├── __init__.py
│   ├── trainer.py       # Training loop
│   └── callbacks.py     # Custom callbacks
└── evaluation/
    ├── __init__.py
    ├── metrics.py       # Evaluation metrics
    └── visualization.py # Result visualization
```

**Tasks**  
1. **Training Configuration**
   ```python
   # core/config.py
   from dataclasses import dataclass
   from typing import List, Optional
   
   @dataclass
   class TrainingConfig:
       # Model configuration
       model_name: str = "efficientnet_b0"
       pretrained: bool = True
       num_classes: int = len(PlantType) + len(DiseaseType)
       
       # Training parameters
       batch_size: int = 32
       num_epochs: int = 100
       learning_rate: float = 1e-4
       weight_decay: float = 1e-5
       
       # Data augmentation
       augmentation_strength: float = 0.5
       mixup_alpha: float = 0.2
       
       # Early stopping
       patience: int = 10
       min_delta: float = 1e-4
       
       # Hardware
       num_workers: int = 4
       device: str = "cuda"
       
       def to_dict(self) -> dict:
           """Convert config to dict for logging."""
           return asdict(self)
   ```

2. **Custom Dataset**
   ```python
   # core/dataset.py
   class PlantDataset(torch.utils.data.Dataset):
       def __init__(
           self,
           annotations: List[PlantAnnotation],
           transform: Optional[A.Compose] = None,
           stage: str = "train"
       ):
           self.annotations = annotations
           self.transform = transform
           self.stage = stage
           
           # Setup components
           self.loader = ImageLoader(
               LoaderConfig(),
               CacheManager(cache_size)
           )
           self.processor = PreprocessingPipeline(
               PreprocessConfig()
           )
           
           # Load annotations
           self.annotations = self._load_annotations()
           
           # Setup samplers
           self.sampler = self._setup_sampler()
   
       def __getitem__(self, idx: int) -> Dict[str, torch.Tensor]:
           """Get a single sample."""
           try:
               # Get annotation
               annotation = self.annotations[idx]
               
               # Load image
               image = self.loader.load_image(
                   annotation.image_id
               )
               
               # Apply preprocessing
               processed = self.processor.process_batch(
                   [image]
               )[0]
               
               # Apply additional transforms
               if self.transform:
                   processed = self.transform(processed)
               
               return {
                   "image": processed,
                   "label": annotation.label,
                   "metadata": annotation.metadata
               }
           except Exception as e:
               self.logger.error(
                   f"Failed to load item {idx}: {e}"
               )
               return self.__getitem__(
                   (idx + 1) % len(self)
               )
   ```

3. **Training Loop**
   ```python
   # training/trainer.py
   class Trainer:
       def __init__(
           self,
           model: nn.Module,
           config: TrainingConfig,
           experiment_tracker: Optional[MLFlowLogger] = None
       ):
           self.model = model
           self.config = config
           self.tracker = experiment_tracker
           self.device = torch.device(config.device)
           
           # Setup training components
           self.criterion = self._setup_criterion()
           self.optimizer = self._setup_optimizer()
           self.scheduler = self._setup_scheduler()
           
       def train_epoch(
           self,
           dataloader: DataLoader
       ) -> Dict[str, float]:
           """Train for one epoch."""
           self.model.train()
           metrics = defaultdict(float)
           
           with tqdm(dataloader) as pbar:
               for batch in pbar:
                   # Forward pass
                   outputs = self.model(
                       batch["image"].to(self.device)
                   )
                   loss = self.criterion(
                       outputs,
                       batch["labels"].to(self.device)
                   )
                   
                   # Backward pass
                   self.optimizer.zero_grad()
                   loss.backward()
                   self.optimizer.step()
                   
                   # Update metrics
                   metrics["loss"] += loss.item()
                   metrics.update(
                       self._calculate_batch_metrics(
                           outputs,
                           batch["labels"]
                       )
                   )
                   
                   # Update progress bar
                   pbar.set_postfix(
                       loss=f"{loss.item():.4f}"
                   )
           
           # Average metrics
           return {
               k: v / len(dataloader)
               for k, v in metrics.items()
           }
   ```

4. **Model Evaluation**
   ```python
   # evaluation/metrics.py
   class PlantMetrics:
       def __init__(self):
           self.plant_metrics = torchmetrics.MetricCollection({
               "accuracy": torchmetrics.Accuracy(
                   task="multiclass",
                   num_classes=len(PlantType)
               ),
               "f1": torchmetrics.F1Score(
                   task="multiclass",
                   num_classes=len(PlantType)
               )
           })
           
           self.disease_metrics = torchmetrics.MetricCollection({
               "accuracy": torchmetrics.Accuracy(
                   task="multilabel",
                   num_labels=len(DiseaseType)
               ),
               "f1": torchmetrics.F1Score(
                   task="multilabel",
                   num_labels=len(DiseaseType)
               )
           })
       
       def update(
           self,
           preds: torch.Tensor,
           targets: torch.Tensor
       ):
           """Update metrics with batch predictions."""
           plant_preds, disease_preds = self._split_predictions(preds)
           plant_targets, disease_targets = self._split_targets(targets)
           
           self.plant_metrics.update(plant_preds, plant_targets)
           self.disease_metrics.update(disease_preds, disease_targets)
       
       def compute(self) -> Dict[str, float]:
           """Compute all metrics."""
           return {
               **self.plant_metrics.compute(),
               **self.disease_metrics.compute()
           }
   ```

**Acceptance Criteria**  
1. **Training Pipeline**
   - Reproducible training process
   - Experiment tracking with MLflow
   - Automated hyperparameter tuning
   - Multi-GPU support

2. **Model Performance**
   - Plant classification accuracy >90%
   - Disease detection F1-score >0.85
   - Inference time <100ms on CPU
   - Model size <100MB

3. **Monitoring**
   - Real-time training metrics
   - Learning rate scheduling
   - Early stopping implementation
   - Resource utilization tracking

4. **Documentation**
   - Training configuration guide
   - Model architecture details
   - Performance benchmarks
   - Deployment instructions

**Dependencies**
- Stories 4-6 completed
- GPU infrastructure available
- Storage for model artifacts
- Experiment tracking server

**Estimated Effort**
- 13 story points (8-10 days of work)

**Technical Notes**
- Use PyTorch Lightning for training
- Implement gradient clipping
- Use mixed precision training
- Implement checkpoint saving

**Testing Requirements**
```python
# tests/test_training.py
class TestTraining:
    def test_forward_pass(self):
        """Test model forward pass."""
        model = create_model(config)
        batch = create_dummy_batch()
        output = model(batch["image"])
        assert output.shape == (
            config.batch_size,
            config.num_classes
        )

    def test_backward_pass(self):
        """Test gradient computation."""
        model = create_model(config)
        optimizer = torch.optim.Adam(model.parameters())
        batch = create_dummy_batch()
        
        output = model(batch["image"])
        loss = criterion(output, batch["labels"])
        loss.backward()
        
        # Check if gradients were computed
        for param in model.parameters():
            assert param.grad is not None
```

**Performance Optimization**
```python
# training/optimization.py
class PerformanceOptimizer:
    def __init__(self, model: nn.Module):
        self.model = model

    def optimize_for_inference(self):
        """Optimize model for inference."""
        self.model.eval()
        self.model = torch.jit.script(self.model)
        self.model = torch.quantization.quantize_dynamic(
            self.model,
            {nn.Linear, nn.Conv2d},
            dtype=torch.qint8
        )
        return self.model

    def profile_model(
        self,
        input_shape: Tuple[int, ...]
    ) -> Dict[str, float]:
        """Profile model performance."""
        return {
            "flops": calculate_flops(self.model, input_shape),
            "params": calculate_parameters(self.model),
            "memory": calculate_memory_usage(self.model),
            "inference_time": benchmark_inference(self.model)
        }
```

**Risk Factors**
- GPU availability/cost
- Training instability
- Memory constraints
- Overfitting
- Performance bottlenecks

**Future Considerations**
- Model distillation
- Quantization support
- Ensemble methods
- Continuous training
- Model interpretability

---

### Story 10: Model Serving & API
**Description**  
Implement a scalable, production-ready FastAPI service for plant classification and disease detection. The system should handle image uploads, provide real-time predictions, and include comprehensive monitoring and documentation.

**Technical Details**
```
src/serving/
├── api/
│   ├── __init__.py
│   ├── main.py           # FastAPI application
│   ├── routers/
│   │   ├── __init__.py
│   │   ├── health.py     # Health checks
│   │   └── predict.py    # Prediction endpoints
│   └── middleware/
│       ├── __init__.py
│       ├── auth.py       # Authentication
│       └── logging.py    # Request logging
├── models/
│   ├── __init__.py
│   ├── loader.py         # Model loading
│   └── predictor.py      # Prediction pipeline
├── utils/
│   ├── __init__.py
│   ├── image.py          # Image processing
│   └── validation.py     # Input validation
└── monitoring/
    ├── __init__.py
    ├── metrics.py        # Prometheus metrics
    └── logging.py        # Structured logging
```

**Tasks**  
1. **FastAPI Endpoint**
   ```python
   # api/routers/predict.py
   from fastapi import APIRouter, File, UploadFile
   from pydantic import BaseModel
   
   router = APIRouter()
   
   class PredictionResponse(BaseModel):
       plant_type: str
       confidence: float
       diseases: List[Dict[str, float]]
       processing_time: float
   
   @router.post(
       "/predict",
       response_model=PredictionResponse,
       tags=["prediction"]
   )
   async def predict_image(
       image: UploadFile = File(...),
       min_confidence: float = Query(0.5, ge=0, le=1)
   ) -> PredictionResponse:
       """Predict plant type and diseases from image."""
       try:
           # Track metrics
           with MetricsTracker() as tracker:
               # Validate image
               if not await validate_image(image):
                   raise HTTPException(
                       status_code=400,
                       detail="Invalid image format"
                   )
               
               # Preprocess image
               processed = await preprocess_image(
                   image,
                   target_size=(224, 224)
               )
               
               # Get prediction
               prediction = await model_manager.predict(
                   processed,
                   min_confidence=min_confidence
               )
               
               return PredictionResponse(
                   plant_type=prediction.plant_type,
                   confidence=prediction.confidence,
                   diseases=prediction.diseases,
                   processing_time=tracker.elapsed_time
               )
       except Exception as e:
           logger.error(f"Prediction failed: {e}")
           raise HTTPException(
               status_code=500,
               detail=str(e)
           )
   ```

2. **Model Manager**
   ```python
   # models/predictor.py
   class ModelManager:
       def __init__(self, model_path: str):
           self.model = self._load_model(model_path)
           self.preprocessor = self._setup_preprocessor()
           self.device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
           self.model.to(self.device)
           self.version = self._get_model_version()
   
       async def predict(
           self,
           image: np.ndarray,
           min_confidence: float = 0.5
       ) -> PredictionResult:
           """Generate prediction for image."""
           try:
               # Preprocess image
               tensor = self.preprocessor(image)
               tensor = tensor.unsqueeze(0).to(self.device)
               
               # Get prediction
               with torch.no_grad():
                   output = self.model(tensor)
                   
               # Post-process results
               plant_probs = F.softmax(
                   output[:, :len(PlantType)],
                   dim=1
               )
               disease_probs = torch.sigmoid(
                   output[:, len(PlantType):]
               )
               
               # Get predictions
               plant_type = self._get_plant_type(
                   plant_probs[0],
                   min_confidence
               )
               diseases = self._get_diseases(
                   disease_probs[0],
                   min_confidence
               )
               
               return PredictionResult(
                   plant_type=plant_type,
                   diseases=diseases
               )
           except Exception as e:
               logger.error(f"Prediction error: {e}")
               raise PredictionError(str(e))
   
       def _format_predictions(
           self,
           plant_scores: torch.Tensor,
           disease_scores: torch.Tensor
       ) -> List[Prediction]:
           """Format model outputs as predictions."""
           results = []
           
           # Add plant predictions
           for idx, score in enumerate(plant_scores):
               if score > 0.1:  # Minimum confidence threshold
                   results.append(
                       Prediction(
                           label=PlantType(idx).name,
                           confidence=float(score),
                           type="plant"
                       )
                   )
           
           # Add disease predictions
           for idx, score in enumerate(disease_scores):
               if score > 0.1:
                   results.append(
                       Prediction(
                           label=DiseaseType(idx).name,
                           confidence=float(score),
                           type="disease"
                       )
                   )
           
           return sorted(
               results,
               key=lambda x: x.confidence,
               reverse=True
           )
   ```

3. **Monitoring Setup**
   ```python
   # monitoring/metrics.py
   class MetricsCollector:
       def __init__(self):
           # Request metrics
           self.request_counter = Counter(
               "plant_api_requests_total",
               "Total API requests",
               ["endpoint", "status"]
           )
           
           # Latency metrics
           self.prediction_latency = Histogram(
               "plant_api_latency_seconds",
               "Request latency in seconds"
           )
           
           # Model metrics
           self.prediction_confidence = Histogram(
               "plant_prediction_confidence",
               "Prediction confidence scores"
           )
           
           # Error metrics
           self.error_counter = Counter(
               "plant_api_errors_total",
               "Total API errors",
               ["error_type"]
           )
   
       def track_request(
           self,
           endpoint: str,
           status: int
       ):
           """Track API request."""
           self.request_counter.labels(
               endpoint=endpoint,
               status=status
           ).inc()
   
       @contextlib.contextmanager
       def track_prediction_time(self):
           """Track prediction latency."""
           start_time = time.time()
           yield
           self.prediction_latency.observe(
               time.time() - start_time
           )
   ```

**Acceptance Criteria**  
1. **API Functionality**
   - Image upload endpoint
   - Prediction response
   - Error handling
   - Input validation

2. **Performance**
   - Response time <500ms
   - Concurrent requests
   - Resource efficiency
   - Memory management

3. **Monitoring**
   - Request tracking
   - Error logging
   - Performance stats
   - Health checks

4. **Documentation**
   - API documentation
   - Response schemas
   - Error codes
   - Example requests

**Dependencies**
- Story 9 (Training Pipeline) completed
- Trained model available
- Infrastructure setup
- Monitoring system

**Estimated Effort**
- 8 story points (5-6 days of work)

**Technical Notes**
- Use FastAPI for endpoints
- Implement async processing
- Add request validation
- Setup monitoring

**Testing Requirements**
```python
# tests/test_api.py
class TestAPI:
    async def test_prediction(
        self,
        client: AsyncClient
    ):
        """Test prediction endpoint."""
        # Test valid image
        with open("tests/data/test_image.jpg", "rb") as f:
            response = await client.post(
                "/predict",
                files={"image": f}
            )
        assert response.status_code == 200
        data = response.json()
        assert "plant_type" in data
        assert "confidence" in data
        assert data["confidence"] > 0.5
        
        # Test invalid image
        with open("tests/data/invalid.txt", "rb") as f:
            response = await client.post(
                "/predict",
                files={"image": f}
            )
        assert response.status_code == 400
```

**Load Testing**
```python
# tests/load_test.py
class LoadTest:
    def test_concurrent_requests(self):
        """Test concurrent request handling."""
        async def make_request():
            async with AsyncClient() as client:
                return await client.post(
                    "/predict",
                    files={"image": image_data}
                )
        
        # Run concurrent requests
        results = asyncio.gather(*[
            make_request()
            for _ in range(100)
        ])
        
        # Check results
        for response in results:
            assert response.status_code == 200
```

**Risk Factors**
- Response latency
- Memory usage
- Error handling
- Scalability
- Resource constraints

**Future Considerations**
- Model A/B testing
- Batch prediction
- Caching layer
- Auto-scaling
- API versioning

---

---

## Phase 3: Core Plant Identification & Disease Detection

---

### Story 11: Model Serving Endpoint
**Description**  
Implement a production-ready FastAPI endpoint that serves the trained plant classification and disease detection model. The system should handle image uploads, provide real-time predictions with confidence scores, and include comprehensive monitoring and error handling.

**Technical Details**
```
src/serving/
├── api/
│   ├── __init__.py
│   ├── routers/
│   │   ├── __init__.py
│   │   ├── health.py      # Health check endpoints
│   │   └── predict.py     # Prediction endpoints
│   └── middleware/
│       ├── __init__.py
│       ├── auth.py        # Authentication
│       └── logging.py     # Request logging
├── models/
│   ├── __init__.py
│   ├── manager.py         # Model management
│   └── predictor.py       # Prediction pipeline
├── monitoring/
│   ├── __init__.py
│   ├── metrics.py        # Prometheus metrics
│   └── tracing.py        # OpenTelemetry tracing
└── utils/
    ├── __init__.py
    ├── validation.py     # Input validation
    └── preprocessing.py  # Image preprocessing
```

**Tasks**  
1. **FastAPI Endpoint**
   ```python
   # api/routers/predict.py
   from fastapi import APIRouter, File, UploadFile
   from pydantic import BaseModel
   
   router = APIRouter()
   
   class PredictionResponse(BaseModel):
       plant_type: str
       confidence: float
       diseases: List[Dict[str, float]]
       processing_time: float
   
   @router.post(
       "/predict",
       response_model=PredictionResponse,
       tags=["prediction"]
   )
   async def predict_image(
       image: UploadFile = File(...),
       min_confidence: float = Query(0.5, ge=0, le=1)
   ) -> PredictionResponse:
       """Predict plant type and diseases from image."""
       try:
           # Track metrics
           with MetricsTracker() as tracker:
               # Validate image
               if not await validate_image(image):
                   raise HTTPException(
                       status_code=400,
                       detail="Invalid image format"
                   )
               
               # Preprocess image
               processed = await preprocess_image(
                   image,
                   target_size=(224, 224)
               )
               
               # Get prediction
               prediction = await model_manager.predict(
                   processed,
                   min_confidence=min_confidence
               )
               
               return PredictionResponse(
                   plant_type=prediction.plant_type,
                   confidence=prediction.confidence,
                   diseases=prediction.diseases,
                   processing_time=tracker.elapsed_time
               )
       except Exception as e:
           logger.error(f"Prediction failed: {e}")
           raise HTTPException(
               status_code=500,
               detail=str(e)
           )
   ```

2. **Model Manager**
   ```python
   # models/manager.py
   class ModelManager:
       def __init__(
           self,
           model_path: Path,
           device: str = "cuda"
       ):
           self.device = torch.device(device)
           self.model = self._load_model(model_path)
           self.preprocessor = self._setup_preprocessor()
           self.version = self._get_model_version()
   
       async def predict(
           self,
           image: np.ndarray,
           min_confidence: float = 0.5
       ) -> PredictionResult:
           """Generate prediction for image."""
           try:
               # Preprocess image
               tensor = self.preprocessor(image)
               tensor = tensor.unsqueeze(0).to(self.device)
               
               # Get prediction
               with torch.no_grad():
                   output = self.model(tensor)
                   
               # Post-process results
               plant_probs = F.softmax(
                   output[:, :len(PlantType)],
                   dim=1
               )
               disease_probs = torch.sigmoid(
                   output[:, len(PlantType):]
               )
               
               # Get predictions
               plant_type = self._get_plant_type(
                   plant_probs[0],
                   min_confidence
               )
               diseases = self._get_diseases(
                   disease_probs[0],
                   min_confidence
               )
               
               return PredictionResult(
                   plant_type=plant_type,
                   diseases=diseases
               )
           except Exception as e:
               logger.error(f"Prediction error: {e}")
               raise PredictionError(str(e))
   
       def _format_predictions(
           self,
           plant_scores: torch.Tensor,
           disease_scores: torch.Tensor
       ) -> List[Prediction]:
           """Format model outputs as predictions."""
           results = []
           
           # Add plant predictions
           for idx, score in enumerate(plant_scores):
               if score > 0.1:  # Minimum confidence threshold
                   results.append(
                       Prediction(
                           label=PlantType(idx).name,
                           confidence=float(score),
                           type="plant"
                       )
                   )
           
           # Add disease predictions
           for idx, score in enumerate(disease_scores):
               if score > 0.1:
                   results.append(
                       Prediction(
                           label=DiseaseType(idx).name,
                           confidence=float(score),
                           type="disease"
                       )
                   )
           
           return sorted(
               results,
               key=lambda x: x.confidence,
               reverse=True
           )
   ```

3. **Monitoring Setup**
   ```python
   # monitoring/metrics.py
   class MetricsCollector:
       def __init__(self):
           # Request metrics
           self.request_counter = Counter(
               "plant_api_requests_total",
               "Total API requests",
               ["endpoint", "status"]
           )
           
           # Latency metrics
           self.prediction_latency = Histogram(
               "plant_api_latency_seconds",
               "Request latency in seconds"
           )
           
           # Model metrics
           self.prediction_confidence = Histogram(
               "plant_prediction_confidence",
               "Prediction confidence scores"
           )
           
           # Error metrics
           self.error_counter = Counter(
               "plant_api_errors_total",
               "Total API errors",
               ["error_type"]
           )
   
       def track_request(
           self,
           endpoint: str,
           status: int
       ):
           """Track API request."""
           self.request_counter.labels(
               endpoint=endpoint,
               status=status
           ).inc()
   
       @contextlib.contextmanager
       def track_prediction_time(self):
           """Track prediction latency."""
           start_time = time.time()
           yield
           self.prediction_latency.observe(
               time.time() - start_time
           )
   ```

**Acceptance Criteria**  
1. **API Functionality**
   - Image upload endpoint
   - Prediction response
   - Error handling
   - Input validation

2. **Performance**
   - Response time <500ms
   - Concurrent requests
   - Resource efficiency
   - Memory management

3. **Monitoring**
   - Request tracking
   - Error logging
   - Performance stats
   - Health checks

4. **Documentation**
   - API documentation
   - Response schemas
   - Error codes
   - Example requests

**Dependencies**
- Story 9-10 completed
- Trained model available
- Infrastructure setup
- Monitoring system

**Estimated Effort**
- 8 story points (5-6 days of work)

**Technical Notes**
- Use FastAPI for endpoints
- Implement async processing
- Add request validation
- Setup monitoring

**Testing Requirements**
```python
# tests/test_api.py
class TestAPI:
    async def test_prediction(
        self,
        client: AsyncClient
    ):
        """Test prediction endpoint."""
        # Test valid image
        with open("tests/data/test_image.jpg", "rb") as f:
            response = await client.post(
                "/predict",
                files={"image": f}
            )
        assert response.status_code == 200
        data = response.json()
        assert "plant_type" in data
        assert "confidence" in data
        assert data["confidence"] > 0.5
        
        # Test invalid image
        with open("tests/data/invalid.txt", "rb") as f:
            response = await client.post(
                "/predict",
                files={"image": f}
            )
        assert response.status_code == 400
```

**Load Testing**
```python
# tests/load_test.py
class LoadTest:
    def test_concurrent_requests(self):
        """Test concurrent request handling."""
        async def make_request():
            async with AsyncClient() as client:
                return await client.post(
                    "/predict",
                    files={"image": image_data}
                )
        
        # Run concurrent requests
        results = asyncio.gather(*[
            make_request()
            for _ in range(100)
        ])
        
        # Check results
        for response in results:
            assert response.status_code == 200
```

**Risk Factors**
- Response latency
- Memory usage
- Error handling
- Scalability
- Resource constraints

**Future Considerations**
- Model versioning
- A/B testing
- Batch predictions
- Caching layer
- Auto-scaling

---

### Story 12: Database Schema & Migrations
**Description**  
Create the initial PostgreSQL schema using SQLAlchemy (or similar), including tables for users, plants, and plant events.

**Technical Details**
```
src/database/
├── models/
│   ├── __init__.py
│   ├── base.py          # Base SQLAlchemy model
│   ├── plant.py         # Plant-related models
│   ├── user.py          # User-related models
│   └── prediction.py    # Prediction history
├── migrations/
│   ├── __init__.py
│   ├── env.py           # Alembic environment
│   └── versions/        # Migration versions
├── schemas/
│   ├── __init__.py
│   ├── plant.py         # Pydantic schemas
│   ├── user.py          # User schemas
│   └── prediction.py    # Prediction schemas
└── core/
    ├── __init__.py
    ├── session.py       # Database session
    └── config.py        # Database config
```

**Tasks**  
1. **SQLAlchemy Models**
   ```python
   # models/base.py
   from sqlalchemy.ext.declarative import declarative_base
   from sqlalchemy import Column, Integer, DateTime
   from datetime import datetime
   
   Base = declarative_base()
   
   class BaseModel(Base):
       """Base model with common fields."""
       __abstract__ = True
   
       id = Column(Integer, primary_key=True)
       created_at = Column(
           DateTime,
           default=datetime.utcnow,
           nullable=False
       )
       updated_at = Column(
           DateTime,
           default=datetime.utcnow,
           onupdate=datetime.utcnow,
           nullable=False
       )
   
   # models/plant.py
   class Plant(BaseModel):
       """Plant information model."""
       __tablename__ = "plants"
   
       name = Column(String(100), nullable=False)
       scientific_name = Column(String(100))
       description = Column(Text)
       care_instructions = Column(JSON)
       image_url = Column(String(255))
       
       # Relationships
       diseases = relationship(
           "Disease",
           back_populates="plant"
       )
       predictions = relationship(
           "Prediction",
           back_populates="plant"
       )
   
   class Disease(BaseModel):
       """Plant disease model."""
       __tablename__ = "diseases"
   
       name = Column(String(100), nullable=False)
       description = Column(Text)
       symptoms = Column(ARRAY(String))
       treatment = Column(Text)
       
       # Foreign keys
       plant_id = Column(
           Integer,
           ForeignKey("plants.id")
       )
       
       # Relationships
       plant = relationship(
           "Plant",
           back_populates="diseases"
       )
   
   # models/prediction.py
   class Prediction(BaseModel):
       """Prediction history model."""
       __tablename__ = "predictions"
   
       image_path = Column(String(255))
       confidence = Column(Float, nullable=False)
       metadata = Column(JSON)
       
       # Foreign keys
       plant_id = Column(
           Integer,
           ForeignKey("plants.id")
       )
       disease_id = Column(
           Integer,
           ForeignKey("diseases.id"),
           nullable=True
       )
       user_id = Column(
           Integer,
           ForeignKey("users.id")
       )
       
       # Relationships
       plant = relationship(
           "Plant",
           back_populates="predictions"
       )
       disease = relationship("Disease")
       user = relationship(
           "User",
           back_populates="predictions"
       )
   ```

2. **Pydantic Schemas**
   ```python
   # schemas/plant.py
   class PlantBase(BaseModel):
       """Base schema for Plant model."""
       name: str
       scientific_name: Optional[str]
       description: Optional[str]
       care_instructions: Dict[str, Any]
       image_url: Optional[str]
   
   class PlantCreate(PlantBase):
       """Schema for creating a Plant."""
       pass
   
   class PlantUpdate(PlantBase):
       """Schema for updating a Plant."""
       pass
   
   class Plant(PlantBase):
       """Schema for Plant response."""
       id: int
       created_at: datetime
       updated_at: datetime
       diseases: List["Disease"]
   
       class Config:
           orm_mode = True
   ```

3. **Database Migrations**
   ```python
   # migrations/versions/001_initial.py
   """Initial migration
   
   Revision ID: 001
   Revises: 
   Create Date: 2024-12-30 20:33:27.000000
   """
   from alembic import op
   import sqlalchemy as sa
   
   def upgrade():
       # Create plants table
       op.create_table(
           'plants',
           sa.Column('id', sa.Integer(), nullable=False),
           sa.Column(
               'name',
               sa.String(100),
               nullable=False
           ),
           sa.Column(
               'scientific_name',
               sa.String(100)
           ),
           sa.Column('description', sa.Text()),
           sa.Column(
               'care_instructions',
               sa.JSON()
           ),
           sa.Column('image_url', sa.String(255)),
           sa.Column(
               'created_at',
               sa.DateTime(),
               nullable=False
           ),
           sa.Column(
               'updated_at',
               sa.DateTime(),
               nullable=False
           ),
           sa.PrimaryKeyConstraint('id')
       )
       
       # Create diseases table
       op.create_table(
           'diseases',
           sa.Column('id', sa.Integer(), nullable=False),
           sa.Column(
               'name',
               sa.String(100),
               nullable=False
           ),
           sa.Column('description', sa.Text()),
           sa.Column(
               'symptoms',
               sa.ARRAY(sa.String())
           ),
           sa.Column('treatment', sa.Text()),
           sa.Column(
               'plant_id',
               sa.Integer(),
               nullable=False
           ),
           sa.ForeignKeyConstraint(['plant_id'], ['plants.id']),
           sa.PrimaryKeyConstraint('id')
       )
   
   def downgrade():
       op.drop_table('diseases')
       op.drop_table('plants')
   ```

**Acceptance Criteria**  
1. **Database Schema**
   - Normalized tables
   - Proper relationships
   - Data integrity
   - Indexing strategy

2. **Migrations**
   - Version control
   - Rollback support
   - Data preservation
   - Schema evolution

3. **Data Models**
   - Type validation
   - Relationship mapping
   - Query optimization
   - Error handling

4. **Documentation**
   - Schema diagrams
   - Migration guide
   - API documentation
   - Example queries

**Dependencies**
- PostgreSQL database
- SQLAlchemy ORM
- Alembic migrations
- Pydantic validation

**Estimated Effort**
- 5 story points (3-4 days of work)

**Technical Notes**
- Use PostgreSQL features
- Implement soft deletes
- Add database indices
- Setup connection pooling

**Testing Requirements**
```python
# tests/test_models.py
class TestModels:
    async def test_plant_creation(
        self,
        session: AsyncSession
    ):
        """Test plant model creation."""
        plant = Plant(
            name="Test Plant",
            scientific_name="Testus Plantus",
            description="A test plant",
            care_instructions={
                "water": "daily",
                "light": "partial"
            }
        )
        session.add(plant)
        await session.commit()
        
        # Test retrieval
        result = await session.get(Plant, plant.id)
        assert result.name == "Test Plant"
        assert result.scientific_name == "Testus Plantus"
    
    async def test_relationships(
        self,
        session: AsyncSession
    ):
        """Test model relationships."""
        # Create plant
        plant = Plant(name="Test Plant")
        session.add(plant)
        
        # Create disease
        disease = Disease(
            name="Test Disease",
            plant=plant
        )
        session.add(disease)
        await session.commit()
        
        # Test relationship
        assert plant.diseases[0].name == "Test Disease"
        assert disease.plant.name == "Test Plant"
```

**Database Queries**
```python
# Example queries for common operations
async def get_plant_with_diseases(
    plant_id: int,
    session: AsyncSession
) -> Optional[Plant]:
    """Get plant with related diseases."""
    query = select(Plant).options(
        joinedload(Plant.diseases)
    ).where(Plant.id == plant_id)
    
    result = await session.execute(query)
    return result.scalar_one_or_none()

async def get_recent_predictions(
    user_id: int,
    limit: int = 10,
    session: AsyncSession
) -> List[Prediction]:
    """Get recent predictions for user."""
    query = select(Prediction).where(
        Prediction.user_id == user_id
    ).order_by(
        Prediction.created_at.desc()
    ).limit(limit)
    
    result = await session.execute(query)
    return result.scalars().all()
```

**Risk Factors**
- Schema complexity
- Migration conflicts
- Performance issues
- Data consistency
- Backup strategy

**Future Considerations**
- Sharding strategy
- Read replicas
- Caching layer
- Audit logging
- Data archival
---

### Story 13: Basic CRUD for Plant Profiles
**Description**  
Implement Create, Read, Update, and Delete operations for plant profiles in FastAPI.

**Technical Details**
```
src/api/
├── routers/
│   ├── __init__.py
│   └── plants.py         # Plant CRUD endpoints
├── dependencies/
│   ├── __init__.py
│   └── auth.py          # Authentication deps
└── crud/
    ├── __init__.py
    └── plants.py        # Plant CRUD operations
```

**Tasks**  
1. **API Endpoints**
   ```python
   # routers/plants.py
   from fastapi import APIRouter, Depends, HTTPException
   from sqlalchemy.ext.asyncio import AsyncSession
   from typing import List, Optional
   
   router = APIRouter(prefix="/plants")
   
   @router.post("/", response_model=schemas.Plant)
   async def create_plant(
       plant: schemas.PlantCreate,
       session: AsyncSession = Depends(get_session),
       current_user: User = Depends(get_current_user)
   ):
       """Create new plant profile."""
       try:
           return await crud.plants.create(
               session,
               plant,
               current_user
           )
       except ValueError as e:
           raise HTTPException(
               status_code=400,
               detail=str(e)
           )
   
   @router.get("/{plant_id}", response_model=schemas.Plant)
   async def get_plant(
       plant_id: int,
       session: AsyncSession = Depends(get_session)
   ):
       """Get plant by ID."""
       plant = await crud.plants.get(session, plant_id)
       if not plant:
           raise HTTPException(
               status_code=404,
               detail="Plant not found"
           )
       return plant
   
   @router.get("/", response_model=List[schemas.Plant])
   async def list_plants(
       skip: int = 0,
       limit: int = 100,
       search: Optional[str] = None,
       session: AsyncSession = Depends(get_session)
   ):
       """List plants with optional search."""
       return await crud.plants.get_multi(
           session,
           skip=skip,
           limit=limit,
           search=search
       )
   
   @router.put("/{plant_id}", response_model=schemas.Plant)
   async def update_plant(
       plant_id: int,
       plant: schemas.PlantUpdate,
       session: AsyncSession = Depends(get_session),
       current_user: User = Depends(get_current_user)
   ):
       """Update plant profile."""
       db_plant = await crud.plants.get(session, plant_id)
       if not db_plant:
           raise HTTPException(
               status_code=404,
               detail="Plant not found"
           )
       
       return await crud.plants.update(
           session,
           db_plant,
           plant,
           current_user
       )
   
   @router.delete("/{plant_id}")
   async def delete_plant(
       plant_id: int,
       session: AsyncSession = Depends(get_session),
       current_user: User = Depends(get_current_user)
   ):
       """Delete plant profile."""
       db_plant = await crud.plants.get(session, plant_id)
       if not db_plant:
           raise HTTPException(
               status_code=404,
               detail="Plant not found"
           )
       
       await crud.plants.delete(session, db_plant)
       return {"status": "success"}
   ```

2. **CRUD Operations**
   ```python
   # crud/plants.py
   from sqlalchemy import select
   from sqlalchemy.ext.asyncio import AsyncSession
   from typing import List, Optional
   
   class PlantCRUD:
       async def create(
           self,
           session: AsyncSession,
           plant: schemas.PlantCreate,
           user: User
       ) -> models.Plant:
           """Create new plant."""
           db_plant = models.Plant(
               **plant.dict(),
               user_id=user.id
           )
           session.add(db_plant)
           await session.commit()
           await session.refresh(db_plant)
           return db_plant
   
       async def get(
           self,
           session: AsyncSession,
           plant_id: int
       ) -> Optional[models.Plant]:
           """Get plant by ID."""
           query = select(models.Plant).where(
               models.Plant.id == plant_id
           )
           result = await session.execute(query)
           return result.scalar_one_or_none()
   
       async def get_multi(
           self,
           session: AsyncSession,
           *,
           skip: int = 0,
           limit: int = 100,
           search: Optional[str] = None
       ) -> List[models.Plant]:
           """Get multiple plants."""
           query = select(models.Plant)
           
           if search:
               query = query.where(
                   models.Plant.name.ilike(f"%{search}%")
               )
           
           query = query.offset(skip).limit(limit)
           result = await session.execute(query)
           return result.scalars().all()
   
       async def update(
           self,
           session: AsyncSession,
           db_plant: models.Plant,
           plant: schemas.PlantUpdate,
           user: User
       ) -> models.Plant:
           """Update plant."""
           # Check ownership
           if db_plant.user_id != user.id:
               raise ValueError("Not authorized")
           
           # Update fields
           for field, value in plant.dict(
               exclude_unset=True
           ).items():
               setattr(db_plant, field, value)
           
           await session.commit()
           await session.refresh(db_plant)
           return db_plant
   
       async def delete(
           self,
           session: AsyncSession,
           db_plant: models.Plant
       ):
           """Delete plant."""
           await session.delete(db_plant)
           await session.commit()
   
   plants = PlantCRUD()
   ```

**Acceptance Criteria**  
1. **API Endpoints**
   - All CRUD operations implemented
   - Proper validation and error handling
   - Authentication and authorization
   - Pagination support

2. **Response Format**
   - Consistent JSON structure
   - Proper HTTP status codes
   - Error messages
   - Success indicators

3. **Data Validation**
   - Input validation
   - Type checking
   - Required fields
   - Field constraints

4. **Documentation**
   - OpenAPI/Swagger docs
   - Example requests/responses
   - Error scenarios
   - Authentication details

**Dependencies**
- FastAPI framework
- SQLAlchemy ORM
- Pydantic models
- JWT authentication

**Estimated Effort**
- 3 story points (2-3 days of work)

**Technical Notes**
- Use async/await
- Implement caching
- Add rate limiting
- Setup request logging

**Testing Requirements**
```python
# tests/api/test_plants.py
class TestPlantAPI:
    async def test_create_plant(
        self,
        client: AsyncClient,
        test_user: User
    ):
        """Test plant creation."""
        response = await client.post(
            "/plants/",
            json={
                "name": "Test Plant",
                "scientific_name": "Testus Plantus",
                "description": "A test plant",
                "care_instructions": {
                    "water": "daily",
                    "light": "partial"
                }
            },
            headers={"Authorization": f"Bearer {test_user.token}"}
        )
        
        assert response.status_code == 200
        data = response.json()
        assert data["name"] == "Test Plant"
        assert "id" in data
    
    async def test_get_plant(
        self,
        client: AsyncClient,
        test_plant: Plant
    ):
        """Test plant retrieval."""
        response = await client.get(
            f"/plants/{test_plant.id}"
        )
        
        assert response.status_code == 200
        data = response.json()
        assert data["id"] == test_plant.id
        assert data["name"] == test_plant.name
```

**API Documentation**
```yaml
/plants:
  post:
    summary: Create plant
    tags: [Plants]
    requestBody:
      required: true
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/PlantCreate"
    responses:
      200:
        description: Plant created
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/Plant"
      400:
        description: Invalid input
      401:
        description: Unauthorized

  get:
    summary: List plants
    tags: [Plants]
    parameters:
      - name: skip
        in: query
        schema:
          type: integer
          default: 0
      - name: limit
        in: query
        schema:
          type: integer
          default: 100
      - name: search
        in: query
        schema:
          type: string
    responses:
      200:
        description: List of plants
        content:
          application/json:
            schema:
              type: array
              items:
                $ref: "#/components/schemas/Plant"
```

**Risk Factors**
- Data validation
- Authorization logic
- Performance bottlenecks
- API versioning

**Future Considerations**
- Bulk operations
- Field filtering
- Advanced search
- Soft deletes
- API versioning

---

### Story 14: Care Event Logging System
**Description**  
Implement a robust plant care event tracking system that allows users to log, monitor, and analyze their plant care activities over time. This system will help users maintain consistent care routines and identify patterns in plant health.

**Technical Details**
```
src/
├── api/
│   ├── routers/
│   │   └── care_events.py    # Care event endpoints
├── models/
│   ├── care_event.py         # Event data models
│   └── event_types.py        # Event type enums
└── services/
    └── care_analytics.py     # Care pattern analysis
```

**Database Schema**
```sql
CREATE TABLE care_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    plant_id UUID NOT NULL REFERENCES plants(id),
    user_id UUID NOT NULL REFERENCES users(id),
    event_type VARCHAR(50) NOT NULL,
    event_date TIMESTAMP WITH TIME ZONE NOT NULL,
    notes TEXT,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    is_deleted BOOLEAN DEFAULT FALSE
);

CREATE INDEX idx_care_events_plant_id ON care_events(plant_id);
CREATE INDEX idx_care_events_user_id ON care_events(user_id);
CREATE INDEX idx_care_events_event_date ON care_events(event_date);
```

**API Endpoints**
1. **Create Event**
   - `POST /api/v1/plants/{plant_id}/events`
   - Creates a new care event for a specific plant
   - Supports multiple event types: watering, fertilizing, pruning, repotting, pest treatment
   ```json
   {
     "event_type": "WATERING",
     "event_date": "2024-12-30T20:57:13-07:00",
     "notes": "Full watering with filtered water",
     "metadata": {
       "water_amount_ml": 250,
       "water_type": "filtered",
       "soil_moisture": "dry"
     }
   }
   ```

2. **List Events**
   - `GET /api/v1/plants/{plant_id}/events`
   - Retrieves care history for a specific plant
   - Supports pagination and filtering
   ```
   Query Parameters:
   - start_date: ISO timestamp
   - end_date: ISO timestamp
   - event_type: string
   - page: int
   - per_page: int
   ```

3. **Update Event**
   - `PUT /api/v1/plants/{plant_id}/events/{event_id}`
   - Allows modification of event details
   - Maintains audit trail of changes

4. **Delete Event**
   - `DELETE /api/v1/plants/{plant_id}/events/{event_id}`
   - Soft deletes the event

5. **Care Analytics**
   - `GET /api/v1/plants/{plant_id}/care-analysis`
   - Provides insights about care patterns and recommendations
   ```json
   {
     "watering_frequency": "every 7 days",
     "last_fertilized": "2024-11-30",
     "upcoming_tasks": [
       {
         "task": "watering",
         "due_date": "2024-01-06",
         "priority": "high"
       }
     ],
     "care_consistency_score": 85
   }
   ```

**Event Types**
```python
from enum import Enum

class CareEventType(str, Enum):
    WATERING = "watering"
    FERTILIZING = "fertilizing"
    PRUNING = "pruning"
    REPOTTING = "repotting"
    PEST_TREATMENT = "pest_treatment"
    CLEANING = "cleaning"
    PROPAGATION = "propagation"
    MEASUREMENT = "measurement"
    ROTATION = "rotation"
    MISTING = "misting"
    CUSTOM = "custom"
```

**Required Features**
1. **Event Validation**
   - Prevent future-dated events
   - Validate event-specific metadata
   - Ensure user owns the plant

2. **Notification System**
   - Care reminders based on event history
   - Overdue care alerts
   - Achievement notifications (care streaks)

3. **Data Analytics**
   - Calculate optimal care intervals
   - Track plant growth metrics
   - Generate care recommendations

4. **Batch Operations**
   - Bulk event creation
   - Mass updates for multiple plants
   - Batch delete functionality

**Testing Requirements**
```python
class TestCareEvents:
    async def test_create_event(self):
        """Test event creation with valid data."""
        event_data = {
            "event_type": "WATERING",
            "event_date": "2024-12-30T20:57:13-07:00",
            "notes": "Regular watering"
        }
        response = await client.post(
            f"/api/v1/plants/{test_plant.id}/events",
            json=event_data
        )
        assert response.status_code == 201
        assert response.json()["event_type"] == "WATERING"

    async def test_invalid_event_date(self):
        """Test rejection of future dates."""
        event_data = {
            "event_type": "WATERING",
            "event_date": "2025-01-01T00:00:00-07:00"
        }
        response = await client.post(
            f"/api/v1/plants/{test_plant.id}/events",
            json=event_data
        )
        assert response.status_code == 400
```

**Security Considerations**
- Event access restricted to plant owner
- Rate limiting on API endpoints
- Input sanitization for notes field
- Audit logging for all modifications

**Performance Requirements**
- Event creation: <100ms
- Event listing: <200ms for up to 100 events
- Analytics calculation: <500ms
- Support for high-frequency updates

**Mobile Integration**
- Offline event creation
- Background sync
- Local caching of recent events
- Push notification support

**Acceptance Criteria**
1. **Core Functionality**
   - Users can create, read, update, and delete care events
   - Events are properly associated with plants and users
   - Event history is paginated and filterable

2. **Data Integrity**
   - No duplicate events within 1-minute window
   - All events have required fields
   - Soft delete preserves history

3. **User Experience**
   - Intuitive event type selection
   - Quick event logging (<3 taps)
   - Clear event history visualization

4. **Analytics**
   - Accurate care interval calculations
   - Meaningful care recommendations
   - Performance within specified limits

**Dependencies**
- FastAPI framework
- SQLAlchemy ORM
- Pydantic models
- Background task queue
- Push notification service

**Estimated Effort**
- Backend implementation: 4 story points
- Testing: 2 story points
- Documentation: 1 story point
- Total: 7 story points (4-5 days)

**Risk Factors**
- Complex event metadata validation
- Performance with large event histories
- Offline sync conflicts
- Push notification reliability

**Future Enhancements**
- AI-powered care recommendations
- Integration with smart sensors
- Social sharing of care routines
- Advanced analytics dashboard
- Export functionality for care history

---

### Story 15: Mobile App Integration for Care Tracking
**Description**  
Link the Flutter front-end with the care tracking API to allow users to view and add events from their phones.

**Tasks**  
- Create UI forms/screens for logging events.  
- Display a timeline or list of care events for a selected plant.  
- Implement API calls to the FastAPI endpoints.

**Acceptance Criteria**  
- Users can add an event from the mobile app and see it persisted on reload.  
- The UI shows a clear timeline (date, type, notes).

---

## Phase 5: Community & Gamification

---

### Story 16: Community Forum API
**Description**  
Add endpoints for users to create forum posts, comments, and interact within a community.

**Tasks**  
- `POST /community/posts`, `GET /community/posts` for forum posts.  
- Comments referencing a `post_id` (e.g., `POST /community/posts/{post_id}/comments`).  
- Basic data models for posts/comments (title, body, user_id).

**Acceptance Criteria**  
- Users can create, view, and comment on posts via the API.  
- Data is stored in the database with correct relationships.

---

### Story 17: Gamification Badges
**Description**  
Introduce a badge/achievement system, awarding users for milestones (e.g., consistent plant care or helpful community contributions).

**Tasks**  
- Create a table or structure for badges/achievements.  
- Add logic to track user activities (e.g., 10 successful disease diagnoses).  
- Award badges to user profiles automatically.

**Acceptance Criteria**  
- Badges appear in the user’s profile when earned.  
- The awarding logic is triggered whenever a user passes a threshold.

---

## Phase 6: Weather Integration

---

### Story 18: Weather Service
**Description**  
Integrate with a chosen weather API (OpenWeather, WeatherAPI, AccuWeather) to fetch daily/weekly forecasts for the user’s location.

**Tasks**  
- Scheduled job to request weather data for each user location.  
- Store weather info in a DB table or cache for quick access.  
- Provide an endpoint (e.g., `GET /weather/{userId}`) to retrieve the forecast.

**Acceptance Criteria**  
- The scheduled job runs without errors and updates weather data.  
- The `GET /weather/{userId}` endpoint returns valid JSON with forecast details.

---

### Story 19: Weather-Based Notifications
**Description**  
Utilize the retrieved weather data to generate automated care notifications (e.g., “Heavy rain expected, reduce watering”).

**Tasks**  
- Evaluate forecast for frost risk, rainfall, temperature changes.  
- Send push notifications or in-app alerts when certain conditions arise.  
- Integrate with the user’s location preference.

**Acceptance Criteria**  
- If a user’s forecast predicts freezing temperatures, they receive a “frost warning.”  
- Notifications are sent in a timely manner (e.g., at least 12-24 hours before).

---

## Phase 7: Security & User Management

---

### Story 20: OAuth & Authentication
**Description**  
Implement authentication mechanisms (Google, Apple, or email/password) for user login.

**Tasks**  
- Configure an OAuth library (or `fastapi-users`) for social logins.  
- Secure relevant API routes with JWT or session tokens.  
- Store user credentials in a secure manner.

**Acceptance Criteria**  
- Logging in via Google returns a valid session token.  
- Access to protected endpoints is denied without a valid token.

---

### Story 21: Deployment & Scaling

*(This can be split into multiple sub-stories if desired: CI/CD pipeline, Dockerization, auto-scaling, etc.)*

---

#### Sub-story A: CI/CD Pipeline Setup
**Description**  
Configure continuous integration/continuous deployment pipelines to automate testing, building, and deployment.

**Tasks**  
- Set up GitHub Actions, GitLab CI, or another platform.  
- Define pipelines for testing (lint, unit tests) on pull requests.  
- Optional: Automatic deployment to a staging environment.

**Acceptance Criteria**  
- Committing to `main` triggers automated tests.  
- The pipeline status is visible in the repository (passing/failing checks).

---

#### Sub-story B: Docker & Production Deployment
**Description**  
Containerize the application and deploy it to a production environment.

**Tasks**  
- Create a Dockerfile for the FastAPI service (and optionally the training environment).  
- Use Docker Compose (or Kubernetes) to manage multi-service stacks (API, DB, etc.).  
- Deploy to AWS, GCP, or any chosen platform under budget constraints.

**Acceptance Criteria**  
- Running `docker-compose up` starts the application and all services locally.  
- The production environment serves the app publicly at a configured domain or IP.


## Project Standards & Requirements

### Testing Strategy
**Unit Testing**
- All Python code must have >80% test coverage
- Use pytest for Python, Flutter test for mobile
- Mock external services (weather API, database)
- Required for all model operations, API endpoints, and business logic

**Integration Testing**
- Test API endpoints with real database connections
- Test mobile app against staging API
- Verify OAuth flows end-to-end
- Test ML model inference pipeline

**Performance Testing**
- API response times <200ms for non-ML endpoints
- Model inference <2s on mobile devices
- Load testing: Support 100 concurrent users
- Memory usage <200MB on mobile devices

### Documentation Requirements
**API Documentation**
- OpenAPI/Swagger for all endpoints
- Example requests/responses
- Error codes and handling
- Rate limits and quotas

**Code Documentation**
- Docstrings for all functions/classes
- README.md for each major component
- Architecture diagrams (draw.io)
- Setup instructions for local development

### Quality Assurance
**Code Review Process**
- Two approvals required for merges
- Automated linting (black, flake8)
- Type hints required for Python code
- No merging with failing tests

**Error Handling**
- Structured error responses
- Graceful degradation strategy
- Detailed logging for debugging
- User-friendly error messages

### DevOps & CI/CD
**Environments**
- Development: Local setup
- Staging: For testing
- Production: Live environment

**Deployment Pipeline**
- GitHub Actions for CI/CD
- Automated testing on PR
- Containerized deployments
- Blue-green deployment strategy

### Data Management
**Data Retention**
- User data: Retained for 1 year after last activity
- Images: Stored for 30 days
- Logs: 90-day retention
- Regular database backups

**Privacy Compliance**
- GDPR compliance for EU users
- Data encryption at rest
- Secure data transmission
- User data export capability

### Security Requirements
**Authentication & Authorization**
- JWT tokens with 24h expiry
- Role-based access control
- Password requirements enforcement
- 2FA for admin accounts

**API Security**
- Rate limiting: 100 requests/minute
- Input validation on all endpoints
- SQL injection prevention
- XSS protection

### Performance Requirements
**Mobile App**
- Cold start <3 seconds
- Image upload <5 seconds
- Offline capability for core features
- Battery usage optimization

**Backend**
- 99.9% uptime target
- <100ms API response time
- Automatic scaling triggers
- CDN for static assets

### User Experience
**Accessibility**
- WCAG 2.1 AA compliance
- Screen reader support
- Color contrast requirements
- Keyboard navigation

**Error Handling**
- User-friendly error messages
- Offline mode indicators
- Loading states for all actions
- Recovery suggestions

### Release Strategy
**Version Control**
- Semantic versioning (MAJOR.MINOR.PATCH)
- Feature branches
- Release branches
- Hotfix process

**Release Process**
- Weekly releases to staging
- Biweekly production updates
- Feature flags for gradual rollout
- Automated rollback capability

### Maintenance Plan
**Regular Updates**
- Security patches: Within 24h
- Dependency updates: Monthly
- Model retraining: Quarterly
- Performance optimization: Monthly

**Technical Debt**
- Dedicated 20% time for refactoring
- Monthly technical debt review
- Deprecation strategy
- Legacy code removal plan

## Usage Notes

- **Story-by-Story Delivery**  
  Each story is designed to be **self-contained**. Provide just the relevant models, code references, or acceptance criteria to your AI coding agent, ensuring the context remains small enough for a single iteration.
- **Testing & Validation**  
  After completion of each story, test thoroughly (e.g., unit tests, integration tests) before merging or moving on.
- **Iterative Refinement**  
  Complex features (like advanced disease detection) may require multiple passes or further sub-stories to refine accuracy beyond the MVP requirements.

---


