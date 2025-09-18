# Business Requirements Document - Sankalpa Project Estimation Tool

## 1. Executive Summary

Sankalpa is a simple, fast project estimation tool that enables users to quickly estimate software project effort by selecting roles and allocating time. The platform prioritizes ease of use and speed over complex methodologies, making it accessible to anyone regardless of their project management background.

## 2. Business Objectives

### 2.1 Primary Objectives
- **Speed**: Enable project estimates to be generated in under 5 minutes
- **Simplicity**: Require minimal user input and no prior estimation experience
- **Accessibility**: Support general-purpose use across different industries and project types
- **Accuracy**: Provide reasonable estimates for typical software projects

### 2.2 Success Criteria
- User can complete an estimate from start to finish in less than 5 minutes
- 90% of users can successfully generate an estimate without external help
- Estimates are within 20-30% accuracy for typical software projects

## 3. Functional Requirements

### 3.1 Core Features (MVP)

#### 3.1.1 Role Selection
**Requirement ID**: FR-001  
**Description**: Users must be able to select from predefined software project roles
**Acceptance Criteria**:
- System displays a list of common software project roles
- Users can select multiple roles for their project
- Selected roles are clearly indicated in the interface
- Roles include typical positions: Developer, Designer, QA Tester, Project Manager, DevOps, Business Analyst

**Data Source**: Hardcoded from CSV file containing role definitions

#### 3.1.2 Time Allocation
**Requirement ID**: FR-002  
**Description**: Users must be able to specify time estimates for each selected role
**Acceptance Criteria**:
- For each selected role, user can input estimated hours/days/weeks
- System supports multiple time units (hours, days, weeks)
- Input validation ensures reasonable values (e.g., 1-2000 hours)
- Default suggested values are provided based on role type

#### 3.1.3 Estimate Generation
**Requirement ID**: FR-003  
**Description**: System generates a comprehensive project estimate based on user inputs
**Acceptance Criteria**:
- Total effort calculation (sum of all role allocations)
- Timeline estimation based on team size and parallel work assumptions
- Clear breakdown showing effort per role
- Summary view with key metrics (total hours, estimated duration, team size)

### 3.2 Enhanced Features (Future Releases)

#### 3.2.1 Cost Calculation
**Requirement ID**: FR-004  
**Description**: System calculates project cost based on role rates and time estimates
**Acceptance Criteria**:
- Each role has associated hourly/daily rates
- System calculates total cost per role and project total
- Support for different rate structures (contractor vs employee)
- Currency selection and formatting

**Data Source**: Rate information stored in CSV file

#### 3.2.2 Estimate Persistence
**Requirement ID**: FR-005  
**Description**: Users can save and retrieve previous estimates
**Acceptance Criteria**:
- Estimates can be saved with custom names
- Saved estimates can be loaded and modified
- Basic estimate history/listing functionality
- Export estimates to common formats (PDF, Excel)

#### 3.2.3 Estimate Sharing
**Requirement ID**: FR-006  
**Description**: Users can share estimates with stakeholders
**Acceptance Criteria**:
- Generate shareable links for estimates
- Export to PDF format for email sharing
- Basic collaboration features (comments, approvals)

#### 3.2.4 Project Templates
**Requirement ID**: FR-007  
**Description**: Pre-configured role and time combinations for common project types
**Acceptance Criteria**:
- Templates for: Web Application, Mobile App, API Development, E-commerce, etc.
- Users can select a template as starting point
- Templates can be customized after selection
- New templates can be created from existing estimates

**Data Source**: Template definitions in CSV files

## 4. Non-Functional Requirements

### 4.1 Performance
- **Response Time**: All user interactions complete within 2 seconds
- **Load Time**: Initial page load under 3 seconds
- **Scalability**: Support for 100 concurrent users (initial deployment)

### 4.2 Usability
- **Accessibility**: WCAG 2.1 AA compliance
- **Mobile Responsive**: Functional on mobile devices and tablets
- **Browser Support**: Modern browsers (Chrome, Firefox, Safari, Edge)
- **Learning Curve**: New users can complete first estimate without training

### 4.3 Reliability
- **Availability**: 99% uptime during business hours
- **Data Integrity**: All user inputs and calculations must be accurate
- **Error Handling**: Graceful handling of invalid inputs with clear error messages

## 5. Data Requirements

### 5.1 Role Data Structure
**Source**: `roles.csv`
**Fields**:
- Role ID
- Role Name
- Role Description
- Default Hours Estimate
- Hourly Rate (for cost calculations)
- Category (Development, Design, Testing, Management, etc.)

### 5.2 Project Template Data
**Source**: `templates.csv`  
**Fields**:
- Template ID
- Template Name
- Template Description
- Role Allocations (JSON format)
- Project Type
- Complexity Level

### 5.3 Rate Data
**Source**: `rates.csv`
**Fields**:
- Role ID
- Region/Market
- Contractor Rate
- Employee Rate
- Currency
- Effective Date

## 6. User Stories

### 6.1 MVP User Stories

**US-001**: As a project manager, I want to quickly select relevant roles for my project so that I can get a baseline estimate without detailed planning.

**US-002**: As a startup founder, I want to allocate time estimates for each role so that I can understand the effort required for my product idea.

**US-003**: As a sales person, I want to generate a project estimate quickly so that I can provide clients with ballpark figures during initial conversations.

### 6.2 Enhanced Features User Stories

**US-004**: As a business owner, I want to see cost estimates so that I can plan my budget appropriately.

**US-005**: As a consultant, I want to save my estimates so that I can reference them later and track my estimation accuracy.

**US-006**: As a team lead, I want to share estimates with stakeholders so that we can align on project scope and timeline.

**US-007**: As a frequent user, I want to use project templates so that I can speed up the estimation process for similar projects.

## 7. Business Rules

### 7.1 Estimation Rules
- Minimum project size: 40 hours total effort
- Maximum single role allocation: 2000 hours
- Default parallel work factor: 80% (accounting for dependencies and coordination)
- Buffer factor: 20% added to final estimates (configurable)

### 7.2 Role Rules
- Each project must have at least one development role
- Project Manager role is optional but recommended for projects > 500 hours
- QA allocation should be minimum 15% of development effort

### 7.3 Cost Rules
- Rates are displayed in user's selected currency
- Default rates are loaded from CSV but can be overridden per estimate
- Cost calculations include a standard 15% overhead factor

## 8. Integration Requirements

### 8.1 Current State
- **Data Sources**: Static CSV files for roles, templates, and rates
- **No External Integrations**: Standalone application

### 8.2 Future Considerations
- Potential integration with project management tools (Jira, Asana)
- API endpoints for embedding estimates in other applications
- Time tracking tool integration for actual vs estimated comparison

## 9. Constraints and Assumptions

### 9.1 Technical Constraints
- Initial data hardcoded in CSV files
- Web-based application only (no mobile apps initially)
- Single user session (no multi-user collaboration in MVP)

### 9.2 Business Constraints
- Budget focused on MVP delivery first
- Timeline driven by user feedback and adoption
- Simplicity must be maintained as features are added

### 9.3 Assumptions
- Users have basic understanding of software project roles
- Estimates are for planning purposes, not contractual commitments
- CSV data structure is sufficient for initial data management needs

## 10. Risk Assessment

### 10.1 High Risk
- **Oversimplification**: Tool may be too basic for complex projects
- **Estimation Accuracy**: Without historical data, estimates may be significantly off

### 10.2 Medium Risk
- **User Adoption**: Users may expect more sophisticated features
- **Data Management**: CSV-based approach may become limiting as data grows

### 10.3 Low Risk
- **Technical Implementation**: Straightforward web application development
- **Performance**: Simple calculations should perform well

## 11. Future Roadmap Considerations

### Phase 1 (MVP)
- Core estimation functionality
- Basic role selection and time allocation
- Simple estimate generation

### Phase 2 (Enhanced)
- Cost calculations
- Estimate saving and sharing
- Improved user interface

### Phase 3 (Advanced)
- Project templates
- Historical data analysis
- Basic reporting and analytics

### Phase 4 (Enterprise)
- API development
- Integration capabilities
- Advanced collaboration features