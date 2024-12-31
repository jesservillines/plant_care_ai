# Plant Care AI - Project Roadmap

## Overview
An AI-powered mobile application for plant identification, disease detection, and care recommendations, with a focus on gardening plants and cannabis cultivation in mountainous regions like Colorado.

---

## Phase 1: Foundations & Tech Stack

### 1.1 Technology Selection
- [ ] Update core dependencies to latest stable versions
  - Python 3.11+
  - TensorFlow 2.x (evaluate TFLite/ONNX for mobile optimization)
  - Flutter for the mobile front-end
  - FastAPI for the backend and ML inference service
  - Streamlit (optional) for quick internal demos and model evaluation  
- [ ] Database selection
  - PostgreSQL for relational data
  - MongoDB for image/unstructured data storage
- [ ] Cloud infrastructure setup (AWS/GCP)

### 1.2 Architecture Design
- [ ] Design microservices architecture
  - Authentication service (user management, OAuth, roles/permissions)
  - ML inference service (serving the plant classification & disease detection model)
  - Plant tracking service (logging watering/fertilizing/pruning events, images, growth progress)
  - Weather integration service (forecast data, location-based recommendations)
  - Community/social service (forums, achievements, expert interactions)
- [ ] Design database schemas
  - Relational tables for users, plants, plant events, community posts, etc.
  - Unstructured storage for images and any large data logs
- [ ] API specification documentation
  - Document all REST endpoints (or consider GraphQL if desired)
  - Detail request/response schemas for each service
- [ ] Mobile app wireframes
  - Basic flow: Splash → Login → Plant Scanner → Dashboard → Care Tracking → Community

---

## Phase 2: Core Plant Identification & Disease Detection

### 2.1 Model Architecture
- [ ] Evaluate and select model architecture
  - Evaluate EfficientNet, MobileNetV3, and potentially Vision Transformers for accuracy vs. resource usage
  - Benchmark performance/accuracy tradeoffs
  - Target >95% accuracy on key classes (garden plants, cannabis, common diseases)
- [ ] Multi-Label Classification (if diseases and deficiencies can co-occur)
  - Plan for expansions (pests + nutrient deficiency in the same plant)

### 2.2 Dataset Collection & Preparation
- [ ] Define initial plant categories
  - Common garden vegetables
  - Herbs
  - Cannabis varieties
  - Regional plants specific to Colorado
- [ ] Data collection strategy
  - Web scraping pipeline & open-source repositories
  - Dataset curation tools & cleaning (remove duplicates, low-quality images)
  - Data augmentation pipeline (rotations, flips, color jitter) to improve generalization
- [ ] Image preprocessing pipeline
  - Standardize image resolution
  - Normalization, bounding box/cropping if needed

### 2.3 Model Training & Optimization
- [ ] Training pipeline setup
  - Scripts or notebooks for reproducible training (using TF/Keras or PyTorch)
- [ ] Model quantization/Pruning for mobile
  - Evaluate TFLite or ONNX to reduce inference time on devices
- [ ] A/B testing framework
  - Compare multiple model versions in production
- [ ] Model versioning system
  - Tag each model release (model_v1, model_v2, etc.)
  - Maintain consistent evaluation metrics

---

## Phase 3: Plant Care & Progress Tracking

### 3.1 Core Features
- [ ] Plant profile management
  - Create/edit/delete plant entries per user
  - Associate species/disease data with each profile
- [ ] Care event tracking
  - Watering
  - Fertilizing
  - Pruning
  - Repotting
- [ ] Growth timeline with photos
  - Storing multiple images per plant with timestamps
  - Display visual timeline (“2 weeks ago vs. today”)
- [ ] Care recommendations engine
  - Simple rules-based engine initially (e.g., “Water every X days”)
  - Expand to AI-based personalized suggestions over time

### 3.2 Mobile App Development
- [ ] UI/UX implementation
  - Flutter-based design with intuitive navigation
- [ ] Camera integration
  - Built-in flow for taking/uploading plant images
- [ ] Local storage management
  - Offline caching of user data (if no internet)
- [ ] Offline functionality
- [ ] Push notifications
  - Reminders for watering/fertilizing
  - Alerts for potential issues or next steps

---

## Phase 4: Community & Gamification

### 4.1 Social Features
- [ ] User profiles
  - Publicly share certain plant profiles or keep them private
- [ ] Plant sharing functionality
  - Ability to post plant progress photos in a community feed
- [ ] Community forums
  - Q&A for plant care, disease diagnosis, etc.
- [ ] Expert Verification: Tag certain users as horticulture experts
- [ ] Plant care achievements/badges

### 4.2 Gamification
- [ ] Achievement system
  - Rewards for consistent use (watering streaks), disease recovery, community help
- [ ] Progress tracking
  - Show personal improvement stats, e.g., “You’ve grown 3 healthy plants this month”
- [ ] Community challenges
  - “Grow a perfect tomato plant” or “Best cannabis harvest”
- [ ] Expert ranking system
  - Points/votes from community to recognize top contributors

---

## Phase 5: Weather Integration
- [ ] Weather API integration
  - Evaluate OpenWeather, WeatherAPI, or AccuWeather
  - Daily/weekly forecast retrieval for user’s location
- [ ] Location-based recommendations
  - Automatic or manual location input
  - “High chance of frost: Protect your plants” notifications
- [ ] Automated care adjustments
  - Reduce watering recommendations if rain is forecasted
- [ ] Weather alerts system
  - Push notifications for severe weather events

---

## Phase 6: Security & User Management
- [ ] OAuth implementation
  - Google
  - Apple
  - Email/Password
- [ ] Data privacy compliance
  - Possibly consider GDPR/CCPA if relevant
  - Provide disclaimers for cannabis identification (respect local laws)
- [ ] User data encryption
  - SSL/TLS for data in transit, hashed passwords, secure DB connections
- [ ] Access control system
  - Roles: Admin, Expert, Standard User
  - Manage forum and community moderation

---

## Phase 7: Deployment & Scaling

### 7.1 Infrastructure
- [ ] CI/CD pipeline setup
  - Automated testing, linting, building, deployment (e.g., GitHub Actions, GitLab CI)
- [ ] Monitoring system
  - Track CPU/memory usage, errors, user metrics
- [ ] Auto-scaling configuration
  - Container orchestration (Docker + Kubernetes) if usage grows significantly
- [ ] Backup systems
  - Regular DB backups, replicated storage

### 7.2 Performance
- [ ] Load testing
- [ ] Performance optimization
- [ ] CDN setup
- [ ] Cache implementation
  - Redis or similar to reduce repeated queries

---

## MVP Definition
Essential features for initial release:
1. Basic plant identification (common garden plants and cannabis)  
2. Disease detection for common issues (nutrient deficiencies, pests, fungal diseases)  
3. Simple care recommendations (placeholder or rules-based)  
4. User authentication (via OAuth or email/password)  
5. Basic plant tracking (logging water/fertilizer events)  
6. Mobile-friendly interface (Flutter app)

Once the MVP is stable, we’ll iterate toward more advanced features (weather integration, community, gamification).

---

## Future Enhancements
- Advanced analytics dashboard  
- Integration with smart garden devices  
- Marketplace for plant care products  
- Expert consultation booking  
- AI-powered plant care chatbot  
- Seasonal planting recommendations  

---

## Success Metrics
- User engagement metrics  
- Model accuracy >95%  
- App store rating >4.5  
- User retention rate  
- Community growth metrics  

---

## Budget Considerations
- Hosting: Target $10-20/month  
- API costs optimization  
- Storage optimization strategies  
- Scaling cost analysis  

---

## Short Summary for AI Workflow

Below is a **condensed overview** of all **21 stories**, designed to help an AI assistant or developer quickly identify the **main tasks** and **acceptance criteria** without being overwhelmed by the full roadmap. Refer to your **master `PROJECT_ROADMAP.md`** (or any detailed docs) for the **complete** requirements and technical details.

---

## **Phase 1: Foundations & Tech Stack**

### **Story 1: Upgrade Dependencies & Project Structure**
- **Key Goals**  
  - Update to Python 3.11, TensorFlow 2.x, configure project structure (e.g., `src/`, `tests/`, `notebooks/`).
  - Use Poetry or similar for package management.
  - Set up code quality (linting, formatting, type checks).
- **Acceptance Criteria**  
  - All dependencies install without conflicts.
  - Pre-commit hooks (if any) run successfully.
  - Basic tests (e.g., `pytest`) pass.

### **Story 2: FastAPI Backend Foundation**
- **Key Goals**  
  - Initialize a FastAPI project with a `/health` endpoint.
  - Set up configuration management (e.g., `.env`, `pydantic` settings).
  - Implement basic logging, CORS, error handling.
- **Acceptance Criteria**  
  - Server boots with `uvicorn`, `/health` returns 200 OK.
  - Configuration loads from environment variables.
  - Proper error responses for 404, 500, etc.

### **Story 3: Flutter Mobile App Skeleton**
- **Key Goals**  
  - Create a Flutter app with a basic home screen.
  - Define navigation structure (e.g., `go_router`, or `Navigator`).
  - Scaffold UI themes, fonts, placeholders.
- **Acceptance Criteria**  
  - App runs on Android/iOS simulators.
  - Basic pages (e.g., Home, Login) exist with placeholder text.
  - Linting rules (Dart `analysis_options`) pass.

---

## **Phase 2: Data Gathering & Preparation**

### **Story 4: Data Gathering Strategy & Dataset Creation**
- **Key Goals**  
  - Identify image sources (PlantVillage, PlantDoc, cannabis datasets).
  - Document licensing and legal disclaimers (especially cannabis).
  - Set up folder structure (`data/raw`, `data/processed`).
- **Acceptance Criteria**  
  - “Data Strategy” doc outlines sources, disclaimers, folder structure.
  - Minimum of 1000 images/class where possible, balanced dataset approach.

### **Story 5: Web Scraping & Download Pipeline**
- **Key Goals**  
  - Build scripts to scrape/download images (async requests, rate limiting).
  - Store metadata (source, license, timestamps) in a DB or file.
  - Handle retries, network errors, duplicates.
- **Acceptance Criteria**  
  - Pipeline completes without errors, logs success/failure rates.
  - Rate limit compliance, no banned IP or site blocking.
  - Images saved in `data/raw/<category>` with metadata.

### **Story 6: Data Labeling & Annotation System**
- **Key Goals**  
  - Create manual labeling UI (e.g., Streamlit) + optional auto-labeler.
  - Support multi-label (plant type + disease).
  - Provide export to COCO/YOLO or custom JSON formats.
- **Acceptance Criteria**  
  - Annotators can load images, assign labels, mark confidence.
  - Inter-annotator agreement or consensus logic (if >1 labeler).
  - Labeled data stored in a structured format (e.g., `annotations.json`).

### **Story 7: Data Cleansing & Augmentation**
- **Key Goals**  
  - Filter low-quality or corrupt images, remove duplicates.
  - Apply augmentation (rotate, flip, color jitter) to expand dataset.
  - Validate final dataset split (train/val/test).
- **Acceptance Criteria**  
  - Cleaned dataset with minimal duplicates/corrupt files.
  - Augmented samples verified to meet resolution/format standards.
  - Balanced distribution across classes/diseases.

---

## **Phase 3: Core Plant Identification & Disease Detection**

### **Story 8: Data Ingestion & Preprocessing Script**
- **Key Goals**  
  - Implement Python scripts to load processed images, resize/normalize.
  - Possibly use PyTorch `Dataset` or TF `ImageDataGenerator`.
  - Cache or indexing mechanism for fast retrieval.
- **Acceptance Criteria**  
  - Single command to build the final train/val sets from `data/processed/`.
  - All images consistently sized (e.g., 224×224).
  - Logging of how many images per class.

### **Story 9: Model Training Pipeline**
- **Key Goals**  
  - Build training code (PyTorch or Keras) with hyperparameters, logging.
  - Integrate early stopping, LR scheduling, etc.
  - Track metrics (accuracy, F1, confusion matrix).
- **Acceptance Criteria**  
  - Model trains without runtime errors, logs each epoch’s metrics.
  - Checkpoints saved (best accuracy) in `models/checkpoints/`.
  - Achieve initial accuracy/f1 thresholds (>90% or as specified).

### **Story 10: Model Serving & API**
- **Key Goals**  
  - Deploy trained model via FastAPI endpoint (`POST /predict`).
  - Accept image file uploads, return classification + confidence.
  - Include simple monitoring/metrics (latency, request count).
- **Acceptance Criteria**  
  - Endpoint returns JSON with predicted class(es), confidence ~<2s for typical images.
  - Proper error handling for invalid/missing images.
  - Basic load test at ~100 concurrent requests.

---

## **Phase 4: Plant Care & Progress Tracking**

### **Story 11: Database Schema & Migrations**
- **Key Goals**  
  - Use SQLAlchemy/Alembic to create tables (users, plants, events).
  - Set up relationships (Plant -> Event).
  - Provide migration scripts and rollback steps.
- **Acceptance Criteria**  
  - Running `alembic upgrade head` creates DB schema with no errors.
  - Basic test seeds data (plants, events) successfully.

### **Story 12: Basic CRUD for Plant Profiles**
- **Key Goals**  
  - Implement `POST /plants`, `GET /plants/{id}`, `PATCH /plants/{id}`, `DELETE /plants/{id}`.
  - Validate plant data (name, species).
  - Return consistent JSON responses.
- **Acceptance Criteria**  
  - Each endpoint tested via Postman/Pytest.
  - DB transactions committed, Rollback on error.
  - Proper 404, 400, 401 responses where needed.

### **Story 13: Care Event Logging**
- **Key Goals**  
  - Add route(s) to log watering/fertilizing/pruning for each plant.
  - Store event type, timestamp, notes, user_id.
  - Possibly integrate data for future recommendations.
- **Acceptance Criteria**  
  - `POST /plants/{id}/events` works, returning newly created event.
  - `GET /plants/{id}/events` lists chronological events.
  - DB relationships enforced, foreign keys referencing user/plant.

### **Story 14: Mobile App Integration for Care Tracking**
- **Key Goals**  
  - Add Flutter UI for creating/viewing care events.
  - Show timeline with plant photos or event history.
  - Integrate push notifications for reminders (optional).
- **Acceptance Criteria**  
  - User can see a plant’s care history, add new events offline if needed.
  - Data syncs when online, updates the backend.

---

## **Phase 5: Community & Gamification**

### **Story 15: Community Forum API**
- **Key Goals**  
  - Endpoints for creating forum posts, comments, likes/upvotes.
  - Basic moderation or filtering (optional).
  - Store relationships (post->comments, user->posts).
- **Acceptance Criteria**  
  - `POST /community/posts`, `GET /community/posts/{id}`, etc.
  - DB references to user_id, timestamps.
  - Possibly pagination for large threads.

### **Story 16: Gamification Badges**
- **Key Goals**  
  - Implement achievements (e.g., 10 successful plant recoveries).
  - Track user progress, awarding badges automatically.
  - Show badges in user profile/community feed.
- **Acceptance Criteria**  
  - System triggers a “Badge Granted” event when thresholds are met.
  - Badges stored in DB, user can see them in their profile.
  - Admin or system can define new badges.

---

## **Phase 6: Weather Integration**

### **Story 17: Weather Service**
- **Key Goals**  
  - Integrate with a 3rd-party API (OpenWeather, etc.) to fetch forecasts.
  - Store location data for each user (city/zip or lat/long).
  - Provide `GET /weather/{userId}` for current & upcoming weather.
- **Acceptance Criteria**  
  - Cron/scheduled job updates daily or hourly.
  - DB/caching for quick access, minimal API calls.
  - Return JSON with temperature, rainfall, etc.

### **Story 18: Weather-Based Notifications**
- **Key Goals**  
  - Generate alerts when frost/heat/rain is forecasted.
  - Send push notifications or in-app alerts (Flutter).
  - Possibly auto-adjust watering recommendations.
- **Acceptance Criteria**  
  - If forecast < 32°F, user gets “Frost Alert” at least 12-24h before.
  - If heavy rain is predicted, user sees “Reduce watering” suggestion.
  - Notification rules easy to update or extend.

---

## **Phase 7: Security & User Management**

### **Story 19: OAuth & Authentication**
- **Key Goals**  
  - Implement Google/Apple sign-in, or email/password with JWT.
  - Restrict certain endpoints to authenticated users.
  - Store hashed passwords, tokens in DB.
- **Acceptance Criteria**  
  - Successful OAuth flow returns valid tokens, user profile in DB.
  - Protected routes reject unauthenticated requests.
  - Refresh tokens or short-lived tokens approach.

### **Story 20: CI/CD Pipeline Setup**
- **Key Goals**  
  - Set up automated tests (lint, unit tests, build) on each push/PR.
  - Integrate with GitHub Actions/GitLab CI (Docker builds optional).
  - Possibly auto-deploy to staging environment.
- **Acceptance Criteria**  
  - Merging to `main` triggers test pipeline.
  - All checks must pass for successful deploy.
  - Pipeline status visible in repo (passing/failing).

### **Story 21: Docker & Production Deployment**
- **Key Goals**  
  - Containerize the FastAPI service, optionally the ML model.
  - Use Docker Compose or Kubernetes for multi-service setup (DB, API, etc.).
  - Deploy to AWS/GCP (ECS, GKE, or similar).
- **Acceptance Criteria**  
  - Running `docker-compose up` starts entire app stack locally.
  - Production environment accessible at a specified domain/IP.
  - Basic scaling config in place (optional).

---

## **How to Use This Short Summary**

1. **Reference Master Roadmap**: Each story here is a concise bullet. For deep technical details, see the corresponding entries in your full `PROJECT_ROADMAP.md`.
2. **Focus on One Story**: Copy **only** the relevant snippet (e.g., “Story 14: Mobile App Integration”) into your AI or developer request.  
3. **Avoid Overload**: Keep the context short by limiting it to the tasks and acceptance criteria for that single story.  
4. **Link to Code or Additional Docs**: If the AI needs code from a prior story, paste only that snippet or reference it by filename and function name.

---

**End of Short Summary**  

