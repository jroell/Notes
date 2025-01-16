# Vurvey API Entities and Relations

This document provides a comprehensive overview of the Vurvey API's database schema and entity relationships. The documentation is organized by core domains and includes relationship diagrams to help visualize the connections between different entities.

## Core Domains

### 1. Workspace Management
```mermaid
erDiagram
    Workspace ||--o{ WorkspaceMembership : has
    Workspace ||--o{ Survey : contains
    Workspace ||--o{ WorkspaceDirectory : organizes
    Workspace ||--o{ Brand : manages
    Workspace ||--o{ WorkspaceReward : offers
    Workspace ||--o{ WorkspaceAIModel : configures
    Workspace ||--o{ AiOrchestration : manages
    Brand ||--o{ Survey : sponsors
    Brand ||--o{ BrandColor : has
    Brand ||--o{ Category : belongs_to
    Brand ||--o{ Country : targets
    Brand ||--o{ Activity : supports
    Brand ||--o{ Benefit : offers
    User ||--o{ WorkspaceMembership : belongs_to
    WorkspaceMembership }o--|| WorkspaceRole : has

    Workspace {
        string id PK
        string name
        string plan
        string billingStatus
        boolean allowAnonymous
        boolean aiInsightsEnabled
        boolean chatbotEnabled
        boolean workflowEnabled
        string customerId
        string subscriptionId
        date renewalDate
        date createdAt
        date updatedAt
        date deletedAt
    }

    Brand {
        string id PK
        string workspaceId FK
        string name
        string description
        string logoId FK
        string countryId FK
        date createdAt
        date updatedAt
    }

    WorkspaceMembership {
        string workspaceId PK,FK
        string userId PK,FK
        string role
        date createdAt
        date updatedAt
    }
```

### 2. User Management and Social Features
```mermaid
erDiagram
    User ||--o{ WorkspaceMembership : has
    User ||--o{ SurveyMembership : participates_in
    User ||--o{ Response : provides
    User ||--o{ Connection : initiates
    User ||--o{ Follower : has
    User ||--o{ FollowRequest : sends
    User ||--o{ UserRewardPreference : configures
    User ||--o{ UserCreatorType : categorized_as
    User ||--o{ UserEthnicity : identifies_with
    User ||--o{ CreatorAttributeValue : possesses
    User ||--o{ Session : maintains
    User ||--o{ AttributeValue : has
    User ||--o{ CreatorInsights : generates
    User ||--o{ ChatConversationMessage : sends
    User ||--o{ Answer : provides
    User ||--o{ AnswerTracker : tracks
    User ||--o{ ReelEditorship : edits

    User {
        string id PK
        string email
        string firstName
        string lastName
        string role
        string status
        date registeredAt
        date lastActive
        boolean isPublic
        string gender
        date birthdate
        string city
        string countryId FK
        string stateId FK
        string pictureId FK
        object meta
        date createdAt
        date updatedAt
        date deletedAt
    }

    Connection {
        string id PK
        string userId FK
        string connectedUserId FK
        date connectedAt
        date createdAt
        date updatedAt
    }

    CreatorInsights {
        string id PK
        string userId FK
        object metrics
        date generatedAt
        date createdAt
        date updatedAt
    }
```

### 3. AI and Chat System

The AI and Chat System is a comprehensive component that handles AI-powered interactions, task orchestration, and chat functionality. It integrates with various services and manages the flow of AI-driven conversations and analysis.

#### Entity Relationships
```mermaid
erDiagram
    AiOrchestration ||--o{ AiPersonaTask : executes
    AiOrchestration ||--o{ AiOrchestrationHistory : tracks
    AiOrchestration ||--|| AiOrchestrationOutputTypes : produces
    AiOrchestration ||--o{ TrainingSet : uses
    AiOrchestration ||--o{ Survey : analyzes
    ChatConversation ||--o{ ChatConversationMessage : contains
    ChatConversation ||--|| AiPersona : uses
    ChatConversation ||--o{ TrainingSet : references
    ChatConversation ||--o{ Survey : discusses
    ChatConversation ||--o{ File : attaches
    ChatConversation ||--o{ Video : references
    AiPersona ||--o{ AiTool : uses
    AiPersona ||--|| AiModel : powered_by
    AiModel ||--|| AiModelCategory : belongs_to

    AiOrchestration {
        string id PK
        string workspaceId FK
        string name
        string description
        string inputParameters
        string instructions
        string outputTypeId FK
        date createdAt
        date updatedAt
        date deletedAt
    }

    AiPersona {
        string id PK
        string name
        string systemPrompt
        string chatPrompt
        float temperature
        integer maxTokens
        string model
        date createdAt
        date updatedAt
    }

    ChatConversation {
        string id PK
        string userId FK
        string workspaceId FK
        string aiPersonaId FK
        string name
        string type
        boolean isPreview
        date lastMessageAt
        date createdAt
        date updatedAt
        date deletedAt
    }

    ChatConversationMessage {
        string id PK
        string conversationId FK
        string userId FK
        string content
        string role
        object metadata
        date sentAt
        date createdAt
        date updatedAt
    }
```

#### Key Components and Flows

1. **AI Service Integration**
```mermaid
sequenceDiagram
    participant Client
    participant API
    participant TaskProcessor
    participant LLMService
    participant AIProvider

    Client->>API: Request AI Analysis
    API->>TaskProcessor: Create AI Task
    TaskProcessor->>LLMService: Process Task
    LLMService->>AIProvider: Send Prompt
    AIProvider->>LLMService: Return Response
    LLMService->>TaskProcessor: Process Response
    TaskProcessor->>API: Update Task Status
    API->>Client: Return Results
```

2. **Task Processing Pipeline**
   - **Validation Phase**
     * Validates task parameters and context
     * Ensures required data is present
     * Checks permissions and access rights
   
   - **Context Preparation**
     * Gathers relevant workspace data
     * Loads user preferences and history
     * Prepares training data if needed
   
   - **Execution Phase**
     * Constructs optimized prompts
     * Manages LLM API interactions
     * Handles response streaming
     * Processes and validates results
   
   - **Event Publishing**
     * Notifies relevant systems of completion
     * Updates task status and metrics
     * Triggers dependent workflows

3. **AI Service Configuration**
   - **Provider Integration**
     * Supports multiple LLM providers (Anthropic, OpenAI)
     * Configurable model selection
     * Flexible API key management
   
   - **Model Parameters**
     * Adjustable token limits
     * Temperature control for response variety
     * Context window management
     * Response format specifications

4. **System Optimization**
   - **Caching Strategy**
     * Prompt template caching
     * Similar request detection
     * Response caching for common queries
   
   - **Performance Features**
     * Parallel task processing
     * Batch request optimization
     * Streaming for long responses
     * Dynamic resource allocation
   
   - **Resource Management**
     * Token usage monitoring
     * Rate limit handling
     * Cost optimization
     * Error recovery mechanisms

### 4. Survey System and Templates
```mermaid
erDiagram
    Survey ||--o{ Question : contains
    Survey ||--o{ Response : receives
    Survey ||--o{ SurveyMembership : has
    Survey ||--o{ SurveyLanguage : supports
    Survey ||--|| SurveyTemplate : based_on
    Survey ||--o{ SurveyCategory : belongs_to
    Survey ||--o{ Video : includes
    Survey ||--o{ File : attaches
    Question ||--o{ QuestionTemplate : based_on
    Question ||--o{ Choice : has
    Question ||--o{ Answer : receives
    Question ||--o{ AttributeRule : applies
    Response ||--o{ Answer : contains
    SurveyTemplate ||--o{ QuestionTemplate : contains
    QuestionTemplate ||--o{ ChoiceTemplate : has

    Survey {
        string id PK
        string workspaceId FK
        string templateId FK
        string name
        string description
        string status
        string type
        boolean isPublic
        date startDate
        date endDate
        integer targetResponses
        object settings
        date createdAt
        date updatedAt
        date deletedAt
    }

    Question {
        string id PK
        string surveyId FK
        string templateId FK
        string type
        string text
        boolean required
        integer order
        object settings
        date createdAt
        date updatedAt
    }

    Response {
        string id PK
        string surveyId FK
        string userId FK
        string status
        date startedAt
        date completedAt
        object metadata
        date createdAt
        date updatedAt
    }

    Answer {
        string id PK
        string questionId FK
        string responseId FK
        string userId FK
        string type
        string value
        object metadata
        date createdAt
        date updatedAt
    }

    SurveyTemplate {
        string id PK
        string workspaceId FK
        string name
        string description
        string type
        object settings
        date createdAt
        date updatedAt
        date deletedAt
    }
```

### 5. Content and Media Management
```mermaid
erDiagram
    Video ||--o{ Transcript : has
    Video ||--o{ VideoSegment : divided_into
    Video ||--o{ VideoHighlight : contains
    Video ||--o{ VideoTag : tagged_with
    Video ||--o{ VideoComment : receives
    File ||--o{ FileVersion : has
    File ||--o{ FileShare : shared_through
    File ||--o{ FileTag : tagged_with
    Transcript ||--o{ TranscriptWord : contains
    Transcript ||--o{ HighlightCategory : categorizes
    Reel ||--o{ ReelSegment : contains
    Reel ||--o{ ReelEditorship : edited_by
    Reel ||--o{ ReelTag : tagged_with

    Video {
        string id PK
        string workspaceId FK
        string userId FK
        string title
        string description
        string status
        string url
        integer duration
        string thumbnailUrl
        object metadata
        date createdAt
        date updatedAt
        date deletedAt
    }

    File {
        string id PK
        string workspaceId FK
        string userId FK
        string name
        string type
        string status
        string url
        integer size
        object metadata
        date createdAt
        date updatedAt
        date deletedAt
    }

    Transcript {
        string id PK
        string videoId FK
        string status
        string language
        float confidence
        object metadata
        date createdAt
        date updatedAt
    }

    Reel {
        string id PK
        string workspaceId FK
        string title
        string description
        string status
        integer duration
        string thumbnailUrl
        object settings
        date createdAt
        date updatedAt
        date deletedAt
    }
```

### 6. Engagement and Analytics
```mermaid
erDiagram
    Activity ||--o{ ActivityMetric : tracks
    Activity ||--o{ ActivityLog : records
    Activity ||--o{ ActivitySegment : categorizes
    Insight ||--o{ InsightMetric : contains
    Insight ||--o{ InsightCategory : belongs_to
    Analytics ||--o{ AnalyticsEvent : records
    Analytics ||--o{ AnalyticsMetric : tracks
    Analytics ||--o{ AnalyticsSegment : groups
    CreatorInsights ||--o{ CreatorMetric : tracks
    CreatorInsights ||--o{ CreatorSegment : categorizes

    Activity {
        string id PK
        string workspaceId FK
        string userId FK
        string type
        string action
        object metadata
        date occurredAt
        date createdAt
        date updatedAt
    }

    Insight {
        string id PK
        string workspaceId FK
        string type
        string status
        object data
        object metadata
        date generatedAt
        date createdAt
        date updatedAt
    }

    Analytics {
        string id PK
        string workspaceId FK
        string entityType
        string entityId
        string metric
        float value
        object dimensions
        date timestamp
        date createdAt
        date updatedAt
    }

    CreatorInsights {
        string id PK
        string userId FK
        object metrics
        object trends
        object recommendations
        date generatedAt
        date createdAt
        date updatedAt
    }
```

### 7. Reward and Incentive System
```mermaid
erDiagram
    Reward ||--o{ RewardClaim : claimed_through
    Reward ||--o{ RewardType : categorized_as
    Reward ||--o{ RewardProvider : provided_by
    RewardClaim ||--o{ RewardTransaction : results_in
    RewardTransaction ||--o{ TransactionStatus : tracks
    WorkspaceReward ||--o{ RewardBudget : has
    WorkspaceReward ||--o{ RewardRule : governed_by
    UserRewardPreference ||--o{ RewardType : prefers

    Reward {
        string id PK
        string workspaceId FK
        string providerId FK
        string type
        string name
        string description
        float value
        string currency
        object metadata
        date validFrom
        date validUntil
        date createdAt
        date updatedAt
        date deletedAt
    }

    RewardClaim {
        string id PK
        string rewardId FK
        string userId FK
        string status
        object metadata
        date claimedAt
        date processedAt
        date createdAt
        date updatedAt
    }

    RewardTransaction {
        string id PK
        string claimId FK
        string status
        float amount
        string currency
        object metadata
        date processedAt
        date createdAt
        date updatedAt
    }

    WorkspaceReward {
        string id PK
        string workspaceId FK
        string type
        float budget
        string currency
        object settings
        date startDate
        date endDate
        date createdAt
        date updatedAt
    }
```

### 8. Segmentation and Targeting
```mermaid
erDiagram
    Segment ||--o{ SegmentRule : defined_by
    Segment ||--o{ SegmentMember : contains
    Segment ||--o{ SegmentAttribute : has
    Attribute ||--o{ AttributeValue : has
    Attribute ||--o{ AttributeRule : governs
    AttributeRule ||--o{ Question : applies_to
    AttributeRule ||--o{ Choice : affects
    SegmentRule ||--o{ AttributeValue : uses

    Segment {
        string id PK
        string workspaceId FK
        string name
        string description
        string type
        object criteria
        boolean isActive
        date createdAt
        date updatedAt
        date deletedAt
    }

    SegmentRule {
        string id PK
        string segmentId FK
        string attributeId FK
        string operator
        string value
        integer priority
        date createdAt
        date updatedAt
    }

    Attribute {
        string id PK
        string workspaceId FK
        string name
        string type
        string category
        boolean isRequired
        object validation
        date createdAt
        date updatedAt
    }

    AttributeRule {
        string id PK
        string attributeId FK
        string entityType
        string entityId
        string operation
        object conditions
        date createdAt
        date updatedAt
    }
```

## Cross-Domain Relationships

### Survey and AI Integration
```mermaid
erDiagram
    Survey ||--o{ AiOrchestration : analyzed_by
    AiOrchestration ||--o{ Insight : generates
    AiPersona ||--o{ ChatConversation : powers
    ChatConversation ||--o{ Survey : references
    Survey ||--o{ Response : receives
    Response ||--o{ Answer : contains
    Answer ||--o{ AiAnalysis : processed_by
    AiAnalysis ||--o{ Insight : produces
```

### User Engagement Flow
```mermaid
erDiagram
    User ||--o{ Survey : participates_in
    Survey ||--o{ Response : submits
    Response ||--o{ Reward : earns
    User ||--o{ Activity : generates
    Activity ||--o{ Analytics : measured_by
    Analytics ||--o{ Insight : produces
    User ||--o{ Segment : belongs_to
    Segment ||--o{ Survey : targets
```

### Security and Access Control

The system implements a comprehensive role-based access control (RBAC) system that operates at multiple levels:

#### Workspace Level Access

1. **Workspace Roles and Capabilities**
   - **Owner**
     - Full access to workspace and all its resources
     - Can manage workspace settings, billing, and integrations
     - Can assign/revoke admin roles
     - Can delete the workspace
   
   - **Administrator**
     - Can manage workspace settings and users
     - Can create and manage surveys
     - Can manage datasets and file permissions
     - Cannot modify billing or delete workspace
   
   - **Member**
     - Can participate in workspace activities
     - Can create and manage their own surveys
     - Can access shared datasets with granted permissions
     - Cannot modify workspace settings
   
   - **Guest**
     - Limited access to specific resources
     - Can view and respond to shared surveys
     - Cannot create new resources
     - Cannot access sensitive data

2. **File and Dataset Permissions**

   a. **Permission Management**
   - Only Workspace Owners and Administrators can:
     - Set default access levels for datasets
     - Grant or revoke access permissions
     - Create and manage sharing rules
     - Set file retention policies
   
   b. **Access Levels**
   - **Private** (Default)
     - Only accessible to file owner and explicitly granted users
     - Requires specific permissions for viewing or editing
     - Not visible in workspace-wide searches
   
   - **Workspace-Public**
     - Accessible to all workspace members
     - Different permission levels can be set:
       * View Only: Can only view and download
       * Comment: Can view and add comments
       * Edit: Can modify file contents
       * Manage: Can change permissions and delete
   
   - **Anonymous**
     - Accessible via public link
     - No authentication required
     - Read-only access
     - Time-limited access possible

   c. **Permission Inheritance**
   - Files inherit default permissions from their parent dataset
   - Dataset permissions cascade to all contained files
   - Individual file permissions can be overridden
   - Permission changes can be applied:
     * To single files
     * To specific file types
     * To entire datasets
     * To all future uploads

3. **Practical Implementation**
   - Permission changes require explicit action
   - Bulk permission updates available for datasets
   - Audit trail maintained for all permission changes
   - Automatic notifications for permission changes
   - Regular permission review reminders for sensitive data

4. **Security Measures**
   - All file access is logged
   - Suspicious access patterns are flagged
   - Version history maintained for critical files
   - Automatic file scanning for security threats
   - Regular permission audits

## Integration Points

### 1. Media Processing Integration
```mermaid
sequenceDiagram
    participant Client
    participant API
    participant MediaProcessor
    participant StorageService
    participant TranscriptionService

    Client->>API: Upload Media
    API->>StorageService: Store File
    API->>MediaProcessor: Process Media
    MediaProcessor->>TranscriptionService: Generate Transcript
    TranscriptionService->>API: Return Transcript
    API->>Client: Return Results
```

---



### Survey Insights & Analysis

```mermaid
graph TD
    A[Survey Insights] --> B[Question Level]
    A --> C[Survey Level]
    
    B --> D[Response Stats]
    B --> E[AI Analysis]
    B --> F[Sentiment]
    
    C --> G[Overall Trends]
    C --> H[Key Findings]
    C --> I[Recommendations]
    
    D --> J[Charts & Graphs]
    E --> K[Pattern Detection]
    F --> L[Emotional Analysis]
    
    G --> M[Cross-Question Analysis]
    H --> N[Executive Summary]
    I --> O[Action Items]
```

1. **Question-Level Insights**
   - Individual response analysis
   - Response distribution
   - Sentiment analysis
   - Key themes and patterns
   - Word clouds for text responses
   - Video response highlights

2. **Survey-Level Insights**
   - Cross-question correlations
   - Overall sentiment trends
   - Key findings summary
   - Demographic breakdowns
   - Completion rate analysis
   - Response quality metrics

3. **Response Analysis Tools**
   - Filter responses by criteria
   - Compare segments
   - Export raw data
   - Generate reports
   - Share insights
   - Track changes over time

4. **AI-Powered Analysis**
   - Automatic theme detection
   - Sentiment classification
   - Pattern recognition
   - Anomaly detection
   - Trend identification
   - Recommendation generation

## Reels & Media Management

```mermaid
graph TD
    A[Market Research Team] --> B[Vurvey Platform]
    B --> C[Survey Creation]
    B --> D[AI Analysis]
    B --> E[Audience Management]
    B --> F[Media Processing]
    C --> G[Data Collection]
    D --> H[Insights Generation]
    E --> G
    F --> H
    G --> H
```

### Key Features
- AI-powered survey creation and analysis
- Intelligent audience segmentation
- Video and media insights
- Real-time data processing
- Collaborative workspaces
- Advanced analytics and reporting

## Getting Started

### User Roles and Permissions

```mermaid
graph TD
    A[Workspace Owner] --> B[Full Control]
    A --> C[Billing Management]
    A --> D[User Management]
    
    E[Administrator] --> F[Survey Management]
    E --> G[Data Analysis]
    E --> H[Team Management]
    
    I[Member] --> J[Survey Creation]
    I --> K[Data Access]
    I --> L[Report Generation]
    
    M[Guest] --> N[Limited Survey Access]
    M --> O[Restricted Data View]
```

#### Role Capabilities:

1. **Workspace Owner**
   - Full platform access
   - Manage billing and subscriptions
   - Configure workspace settings
   - Manage team members and roles

2. **Administrator**
   - Manage workspace settings
   - Create and manage surveys
   - Access all data and analytics
   - Manage team members

3. **Member**
   - Create and manage own surveys
   - Access shared datasets
   - Generate reports
   - Collaborate with team

4. **Guest**
   - View shared surveys
   - Limited data access
   - Basic reporting capabilities

## Workspaces

Workspaces are the central organizational unit in Vurvey, providing a collaborative environment for teams to manage research projects.

### Workspace Overview

Workspaces allow you to:
1.  **Organize Projects**: Group surveys, datasets, and media assets for specific research initiatives.
2.  **Manage Teams**: Invite team members, assign roles, and control access to resources.
3.  **Configure Settings**: Customize workspace preferences, manage integrations, and set default options.
4.  **Track Progress**: Monitor survey responses, data analysis, and team activity.

### Workspace Settings

1.  **General Settings**
    -   Workspace name and description
    -   Workspace logo and branding
    -   Default language and timezone
    -   Contact information

2.  **Team Management**
    -   Invite new members
    -   Assign roles (Owner, Administrator, Member, Guest)
    -   Manage permissions
    -   Remove members

3.  **Billing & Plans**
    -   View current plan details
    -   Manage subscription
    -   View usage and limits
    -   Upgrade or downgrade plan

4.  **AI Model Configuration**
    -   Select AI models for analysis
    -   Configure model parameters
    -   Manage AI tool access
    -   View model status

5.  **Integrations**
    -   Connect to external services
    -   Manage API keys
    -   Configure data sources
    -   Set up webhooks

### Workspace Features

```mermaid
graph LR
    A[Workspace] --> B[Team Management]
    A --> C[Project Organization]
    A --> D[Resource Sharing]
    A --> E[Access Control]
    A --> F[Analytics Dashboard]
```

1.  **Team Collaboration**
    -   Invite team members
    -   Assign roles and permissions
    -   Share resources and insights

2.  **Resource Management**
    -   Organize surveys and datasets
    -   Manage media assets
    -   Track resource usage

3.  **Settings & Configuration**
    -   Customize workspace preferences
    -   Configure default settings
    -   Manage integrations


### Survey States

```mermaid
graph LR
    A[Draft] --> B[Open]
    B --> C[Closed]
    B --> D[Blocked]
    
    style A fill:#f9f,stroke:#333
    style B fill:#9f9,stroke:#333
    style C fill:#999,stroke:#333
    style D fill:#f99,stroke:#333
```

1. **Draft**
   - Initial state for new surveys
   - Can edit all survey settings
   - Not visible to participants
   - Can test survey internally

2. **Open**
   - Survey is live and accepting responses
   - Limited editing capabilities
   - Participants can submit responses
   - Real-time analytics available
   - May be blocked if workspace billing is not current

3. **Closed**
   - No longer accepting responses
   - Can be closed manually or automatically:
     * Reaching response limit
     * Reaching closing date
     * Manual closure
   - Analytics and insights available

### Access Levels

1. **Private**
   - Only invited members can respond
   - Requires authentication
   - Ideal for targeted research

2. **Public**
   - Anyone with link can respond
   - Requires user registration
   - Good for broad research

3. **Anonymous**
   - Anyone can respond
   - No registration required
   - Best for maximum participation

### Survey Templates

1. **Screener**
   - Initial participant qualification
   - Basic demographic questions
   - Participation criteria

2. **Research**
   - In-depth market research
   - Consumer behavior analysis
   - Preference gathering

3. **Concept**
   - Product concept testing
   - Design feedback
   - Feature validation

4. **Testing**
   - Product testing feedback
   - Usability assessment
   - Performance evaluation

5. **Post-Production**
   - Customer satisfaction
   - Product feedback
   - Market performance

### Question Types

1. **Multiple Choice**
   - Single selection
   - Multiple selection
   - Ranked choice
   - Optional "Not Applicable"

2. **Text Entry**
   - Short text
   - Long text
   - Numeric input
   - Input validation

3. **Media Upload**
   - Image upload
   - Video upload
   - PDF documents
   - File size limits:
     * Images: 50MB
     * Videos: 100MB

4. **Rating Scales**
   - Star rating (1-5)
   - Slider scale
   - Custom range selection
   - Custom labels

5. **Video Response**
   - Record video answers
   - Time limit options
   - Automatic transcription
   - Sentiment analysis

6. **Barcode/UPC**
   - Product identification
   - Inventory tracking
   - Purchase verification

### Response Settings

1. **Participation Controls**
   - Single response per user
   - Multiple responses allowed
   - Response limit per survey
   - Closing date/time

2. **Required Questions**
   - Mark questions as required
   - Conditional requirements
   - Validation rules
   - Error messages

3. **Response Management**
   - View individual responses
   - Export response data
   - Real-time analytics
   - Response filtering

### Survey Creation Process

```mermaid
sequenceDiagram
    participant User
    participant Platform
    
    User->>Platform: Create New Survey
    User->>Platform: Add Questions
    User->>Platform: Configure Settings
    User->>Platform: Set Access Level
    User->>Platform: Preview & Test
    User->>Platform: Publish Survey
    Platform->>User: Survey Live
    User->>Audience: Invite Participants
    Audience->>Platform: Responses
    Platform->>User: Analyze Data
    AI->>Platform: Generate Insights
    Platform->>User: Report Generation
    User->>Platform: Share Insights
```

1. **Initial Setup**
   - Title and description
   - Set objectives
   - Set payout amount for participants

2. **Question Creation**
   - Add questions
   - Configure question types
   - Add media elements

3. **Response Settings**
   - Set participation rules
   - Configure closing conditions
   - Set response limits
   - Enable/disable features

4. **Preview & Testing**
   - Internal review what the survey will look like to participants

5. **Publication**
   - Publish survey
   - Share access link
   - Invite participants through email or Workspace Audience memebers
   
   **Review**
   - Review responses
   - Analyze data
   - Generate insights

## Datasets & AI Integration

### Training Sets (Datasets)

```mermaid
sequenceDiagram
    participant User
    participant Platform
    participant Storage
    participant AI
    
    User->>Platform: Create Dataset
    User->>Platform: Upload Files
    Platform->>Storage: Store Files
    Storage->>AI: Process & Embed
    AI->>Platform: Ready for Chat
    User->>Platform: Start Conversation
    Platform->>AI: Query Dataset
    AI->>User: Insights & Analysis
```

#### Dataset Creation
1. **Create a New Dataset**
   - Provide a name (spaces automatically converted to dashes)
   - Add description
   - Set up labels (optional)

2. **Add Files**
   - Upload directly from computer
   - Import from Google Drive
   - Supported formats:
     * Text files (.txt, .md)
     * Documents (.pdf, .docx, .pptx)
     * Spreadsheets (.csv, .xlsx)
     * Videos (for transcription)
   - Size limits:
     * Documents: 50MB
     * Videos: 100MB

3. **Processing**
   - Files are automatically processed
   - Text is extracted and embedded
   - Videos are transcribed
   - Data is prepared for AI analysis

#### Using Datasets in Chats
1. **Start a Conversation**
   - Select dataset(s) to analyze
   - Choose AI persona for analysis
   - Begin asking questions about the data

2. **Analysis Capabilities**
   - Query data across multiple files
   - Extract insights and patterns
   - Compare different datasets
   - Generate summaries
   - Create visualizations

3. **Integration with Surveys**
   - Add survey results to datasets
   - Analyze responses in real-time
   - Compare with historical data
   - Generate comprehensive reports

## AI Personas & Tools

### AI Personas

#### Creating Custom AI Personas

```mermaid
sequenceDiagram
    participant User
    participant Platform
    participant AI
    
    User->>Platform: Define Persona
    User->>Platform: Set Expertise Areas
    User->>Platform: Configure Parameters
    Platform->>AI: Initialize Model
    AI->>Platform: Ready for Use
    User->>Platform: Start Chat
    Platform->>AI: Route to Persona
    AI->>User: Specialized Response
```

1. **Persona Configuration**
   - Name and description
   - Expertise areas
   - Industry focus
   - Communication style
   - Analysis preferences

2. **Model Parameters**
   - Temperature (creativity level)
   - Response length
   - Analysis depth
   - Tool access permissions
   - Context window size

3. **Specialization Options**
   - Market research focus
   - Industry expertise
   - Analysis methodology
   - Reporting style
   - Data visualization preferences

#### Built-in Personas

```mermaid
graph TD
    A[Built-in Personas] --> B[Survey Expert]
    A --> C[Data Analyst]
    A --> D[Research Assistant]
    A --> E[Report Writer]
    
    B --> F[Question Design]
    B --> G[Response Analysis]
    C --> H[Statistical Analysis]
    C --> I[Pattern Recognition]
    D --> J[Literature Review]
    D --> K[Data Collection]
    E --> L[Report Generation]
    E --> M[Visualization]
```

## Datasets & AI Integration

### Training Sets (Datasets)

```mermaid
sequenceDiagram
    participant User
    participant Platform
    participant Storage
    participant AI
    
    User->>Platform: Create Dataset
    User->>Platform: Upload Files
    Platform->>Storage: Store Files
    Storage->>AI: Process & Embed
    AI->>Platform: Ready for Chat
    User->>Platform: Start Conversation
    Platform->>AI: Query Dataset
    AI->>User: Insights & Analysis
```

#### Dataset Creation
1. **Create a New Dataset**
   - Provide a name (spaces automatically converted to dashes)
   - Add description
   - Set up labels (optional)

2. **Add Files**
   - Upload directly from computer
   - Import from Google Drive
   - Supported formats:
     * Text files (.txt, .md)
     * Documents (.pdf, .docx, .pptx)
     * Spreadsheets (.csv, .xlsx)
     * Videos (for transcription)
   - Size limits:
     * Documents: 50MB
     * Videos: 100MB

3. **Processing**
   - Files are automatically processed
   - Text is extracted and embedded
   - Videos are transcribed
   - Data is prepared for AI analysis

#### Using Datasets in Chats
1. **Start a Conversation**
   - Select dataset(s) to analyze
   - Choose AI persona for analysis
   - Begin asking questions about the data

2. **Analysis Capabilities**
   - Query data across multiple files
   - Extract insights and patterns
   - Compare different datasets
   - Generate summaries
   - Create visualizations

3. **Integration with Surveys**
   - Add survey results to datasets
   - Analyze responses in real-time
   - Compare with historical data
   - Generate comprehensive reports

## AI Personas & Tools
