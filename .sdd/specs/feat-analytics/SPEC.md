# Feature Specification: Learning Analytics & Dashboard (`feat-analytics`)

> **Feature Module**: `feat-analytics`  
> **Status**: `Approved`  
> **Use Cases Covered**: `UC063` (GPA Velocity), `UC064` (Skill Radar Chart), `UC065` (Mistake Matrix), `UC069` (Score Histogram)  
> **API Endpoints**: #26 (`GET /analytics/student/me`), #27 (`GET /analytics/student/radar`), #28 (`GET /analytics/student/mistakes`), #29 (`GET /analytics/class/{id}/histogram`)

---

## 1. Context & Goal
- **Problem**: Raw score numbers do not tell learners which specific sub-topics they struggle with or what types of errors they make repeatedly.
- **Goal**: Aggregate test results into visual Skill Radar charts, Error Category matrices, and class distribution histograms.

## 2. Functional Requirements
- **FR-ANLY-01**: Compute student GPA velocity across chronological exam submissions (`UC063`).
- **FR-ANLY-02**: Compute topic mastery percentages and render a multi-axis Skill Radar chart (`UC064`).
- **FR-ANLY-03**: Aggregate error counts across the 5 Error Categories into a Common Mistake Matrix (`UC065`).
- **FR-ANLY-04**: Generate class score distribution histograms with Mean, Median, and Standard Deviation for teachers (`UC069`).

## 3. Data Model & Entities
- **Tables**: Aggregates from `exam_submissions`, `submission_answers`, `ai_grading_results`, `mistake_logs`.
- **DTOs**: `StudentAnalyticsDTO`, `SkillRadarDTO`, `MistakeMatrixDTO`, `ClassHistogramDTO`.

## 4. BDD Acceptance Criteria
- **Given** a student with 3 completed exams, **When** fetching `/analytics/student/radar`, **Then** return topic mastery scores between 0% and 100%.
- **Given** a class with 20 submissions, **When** fetching `/analytics/class/{id}/histogram`, **Then** return score bins with valid statistical metrics.
