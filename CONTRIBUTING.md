# Contributing to Archon

Help us build the definitive knowledge and task management engine for AI coding assistants! This guide shows you how to contribute new features, bug fixes, and improvements to the Archon platform.

## 🎯 What is Archon?

Archon is a **microservices-based engine** that provides AI coding assistants with access to your documentation, project knowledge, and task management through the Model Context Protocol (MCP). The platform consists of four main services that work together to deliver comprehensive knowledge management and project automation.

## 🏗️ Architecture Overview

### Microservices Structure

Archon uses true microservices architecture with clear separation of concerns:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend UI   │    │  Server (API)   │    │   MCP Server    │    │ Agents Service  │
│                 │    │                 │    │                 │    │                 │
│  React + Vite   │◄──►│    FastAPI +    │◄──►│    Lightweight  │◄──►│   PydanticAI    │
│  Port 3737      │    │    SocketIO     │    │    HTTP Wrapper │    │   Port 8052     │
│                 │    │    Port 8181    │    │    Port 8051    │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘
         │                        │                        │                        │
         └────────────────────────┼────────────────────────┼────────────────────────┘
                                  │                        │
                         ┌─────────────────┐               │
                         │    Database     │               │
                         │                 │               │
                         │    Supabase     │◄──────────────┘
                         │    PostgreSQL   │
                         │    PGVector     │
                         └─────────────────┘
```

### Service Responsibilities

| Service        | Location             | Purpose                      | Key Features                                                               |
| -------------- | -------------------- | ---------------------------- | -------------------------------------------------------------------------- |
| **Frontend**   | `archon-ui-main/`    | Web interface and dashboard  | React, TypeScript, TailwindCSS, Socket.IO client                           |
| **Server**     | `python/src/server/` | Core business logic and APIs | FastAPI, service layer, Socket.IO broadcasts, all LLM/embedding operations |
| **MCP Server** | `python/src/mcp/`    | MCP protocol interface       | Lightweight HTTP wrapper, 14 MCP tools, session management                 |
| **Agents**     | `python/src/agents/` | PydanticAI agent hosting     | Document and RAG agents, streaming responses                               |

### Communication Patterns

- **HTTP-based**: All inter-service communication uses HTTP APIs
- **Socket.IO**: Real-time updates from Server to Frontend
- **MCP Protocol**: AI clients connect to MCP Server via SSE or stdio
- **No Direct Imports**: Services are truly independent with no shared code dependencies

## 🚀 Quick Start for Contributors

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Supabase](https://supabase.com/) account (free tier works)
- [OpenAI API key](https://platform.openai.com/api-keys) or alternative LLM provider
- Basic knowledge of Python (FastAPI) and TypeScript (React)

### Initial Setup

After forking the repository, you'll need to:

1. **Environment Configuration**

   ```bash
   cp .env.example .env
   # Edit .env with your Supabase credentials
   ```

2. **Database Setup**
   - Run `migration/complete_setup.sql` in your Supabase SQL Editor

3. **Start Development Environment**

   ```bash
   docker-compose up --build -d
   ```

4. **Configure API Keys**
   - Open http://localhost:3737
   - Go to Settings → Add your OpenAI API key

## 🔄 Contribution Process

### 1. Choose Your Contribution

**Bug Fixes:**

- Check existing issues for reported bugs
- Create detailed reproduction steps
- Fix in smallest possible scope

**New Features:**

- Optional: Open an issue first to discuss the feature
- Get feedback on approach and architecture (from maintainers and/or AI coding assistants)
- Break large features into smaller PRs

**Documentation:**

- Look for gaps in current documentation
- Focus on user-facing improvements
- Update both code docs and user guides

### 2. Development Process

1. **Fork the Repository**
   - Go to https://github.com/coleam00/archon
   - Click the "Fork" button in the top right corner
   - This creates your own copy of the repository

   ```bash
   # Clone your fork (replace 'your-username' with your GitHub username)
   git clone https://github.com/your-username/archon.git
   cd archon

   # Add upstream remote to sync with main repository later
   git remote add upstream https://github.com/coleam00/archon.git
   ```

2. **🤖 AI Coding Assistant Setup**

   **IMPORTANT**: If you're using AI coding assistants to help contribute to Archon, set up our global rules for optimal results.
   - **Claude Code**: ✅ Already configured! The `CLAUDE.md` file is automatically used
   - **Cursor**: Copy `CLAUDE.md` content to a new `.cursorrules` file in the project root
   - **Windsurf**: Copy `CLAUDE.md` content to a new `.windsurfrules` file in the project root
   - **Other assistants**: Copy `CLAUDE.md` content to your assistant's global rules/context file

   These rules contain essential context about Archon's architecture, service patterns, MCP implementation, and development best practices. Using them will help your AI assistant follow our conventions and implement features correctly.

3. **Create Feature Branch**

   **Best Practice**: Always create a feature branch rather than working directly on main. This keeps your main branch clean and makes it easier to sync with the upstream repository.

   ```bash
   git checkout -b feature/your-feature-name
   # or
   git checkout -b fix/bug-description
   ```

4. **Make Your Changes**
   - Follow the service architecture patterns
   - Add tests for new functionality
   - Update documentation as needed

5. **Verify Your Changes**
   - Run full test suite
   - Test manually via Docker environment
   - Verify no regressions in existing features

### 3. Submit Pull Request

1. **Push to Your Fork**

   ```bash
   # First time pushing this branch
   git push -u origin feature/your-feature-name

   # For subsequent pushes to the same branch
   git push
   ```

2. **Create Pull Request via GitHub UI**
   - Go to your fork on GitHub (https://github.com/your-username/archon)
   - Click "Contribute" then "Open pull request"
   - GitHub will automatically detect your branch and show a comparison
   - The PR template will be automatically filled in the description
   - Review the template and fill out the required sections
   - Click "Create pull request"

3. **Testing Requirements**

   **Before submitting, ensure:**
   - [ ] All existing tests pass
   - [ ] New tests added for new functionality
   - [ ] Manual testing of affected user flows
   - [ ] Docker builds succeed for all services

   **Test commands:**

   ```bash
   # Backend tests
   cd python && python -m pytest

   # Frontend tests
   cd archon-ui-main && npm run test

   # Full integration test
   docker-compose up --build -d
   # Test via UI at http://localhost:3737
   ```

4. **Review Process**
   - Automated tests will run on your PR
   - Maintainers will review code and architecture
   - Address feedback and iterate as needed

## 📋 Contribution Areas

### 🔧 Backend Services (Python)

**When to contribute:**

- Adding new API endpoints or business logic
- Implementing new MCP tools
- Creating new service classes or utilities
- Improving crawling, embedding, or search functionality (everything for RAG)

**Key locations:**

- **Service Layer**: `python/src/server/services/` - Core business logic organized by domain
- **API Endpoints**: `python/src/server/api_routes/` - REST API route handlers
- **MCP Tools**: `python/src/mcp/modules/` - MCP protocol implementations
- **Agents**: `python/src/agents/` - PydanticAI agent implementations

**Development patterns:**

- Services use dependency injection with `supabase_client` parameter
- Use async/await for I/O operations, sync for pure logic
- Follow service → API → MCP layer separation

### 🎨 Frontend (React/TypeScript)

**When to contribute:**

- Adding new UI components or pages
- Implementing real-time features with Socket.IO
- Creating new service integrations
- Improving user experience and accessibility

**Key locations:**

- **Components**: `archon-ui-main/src/components/` - Reusable UI components organized by feature
- **Pages**: `archon-ui-main/src/pages/` - Main application routes
- **Services**: `archon-ui-main/src/services/` - API communication and business logic
- **Contexts**: `archon-ui-main/src/contexts/` - React context providers for global state

**Development patterns:**

- Context-based state management (no Redux)
- Service layer abstraction for API calls
- Socket.IO for real-time updates
- TailwindCSS for styling with custom design system

### 🐳 Infrastructure (Docker/DevOps)

**When to contribute:**

- Optimizing container builds or sizes
- Improving service orchestration
- Adding new environment configurations
- Enhancing health checks and monitoring

**Key locations:**

- **Docker**: `python/Dockerfile.*` - Service-specific containers
- **Compose**: `docker-compose.yml` - Service orchestration
- **Config**: `.env.example` - Environment variable documentation

### 📚 Documentation

**When to contribute:**

- Adding API documentation
- Creating deployment guides
- Writing feature tutorials
- Improving architecture explanations

**Key locations:**

- **Docs Site**: `docs/docs/` - Docusaurus-based documentation
- **API Docs**: Auto-generated from FastAPI endpoints
- **README**: Main project documentation

## 🛠️ Development Workflows

### Backend Development (Python)

1. **Adding a New Service**

   ```bash
   # Create service class in appropriate domain
   python/src/server/services/your_domain/your_service.py

   # Add API endpoints
   python/src/server/api_routes/your_api.py

   # Optional: Add MCP tools
   python/src/mcp/modules/your_module.py
   ```

2. **Testing Your Changes**

   ```bash
   # Run Python tests
   cd python && python -m pytest tests/

   # Run specific test categories
   python -m pytest -m unit      # Unit tests only
   python -m pytest -m integration  # Integration tests only
   ```

3. **Code Quality**
   ```bash
   # We encourage you to use linters for all code
   # Follow service patterns from existing code
   ```

### Frontend Development (React)

1. **Adding a New Component**

   ```bash
   # Create in appropriate category
   archon-ui-main/src/components/your-category/YourComponent.tsx

   # Add to appropriate page or parent component
   archon-ui-main/src/pages/YourPage.tsx
   ```

2. **Testing Your Changes**

   ```bash
   # Run frontend tests
   cd archon-ui-main && npm run test

   # Run with coverage
   npm run test:coverage

   # Run in UI mode
   npm run test:ui
   ```

3. **Development Server**
   ```bash
   # For faster iteration, run frontend locally
   cd archon-ui-main && npm run dev
   # Still connects to Docker backend services
   ```

## ✅ Quality Standards

### Code Requirements

1. **Backend (Python)**
   - Follow existing service patterns and dependency injection
   - Use type hints and proper async/await patterns
   - Include unit tests for new business logic
   - Update API documentation if adding endpoints

2. **Frontend (TypeScript)**
   - Use TypeScript with proper typing
   - Follow existing component patterns and context usage
   - Include component tests for new UI features
   - Ensure responsive design and accessibility

3. **Documentation**
   - Update relevant docs for user-facing changes
   - Include inline code documentation for complex logic
   - Add migration notes for breaking changes

### Performance Considerations

- **Service Layer**: Keep business logic efficient, use async for I/O
- **API Responses**: Consider pagination for large datasets
- **Real-time Updates**: Use Socket.IO rooms appropriately
- **Database**: Consider indexes for new query patterns

## 🏛️ Architectural Guidelines

### Service Design Principles

1. **Single Responsibility**: Each service has a focused purpose
2. **HTTP Communication**: No direct imports between services
3. **Database Centralization**: Supabase as single source of truth
4. **Real-time Updates**: Socket.IO for live collaboration features

### Adding New MCP Tools

**Tool Pattern:**

```python
@mcp.tool()
async def your_new_tool(ctx: Context, param: str) -> str:
    """
    Tool description for AI clients.

    Args:
        param: Description of parameter

    Returns:
        JSON string with results
    """
    async with httpx.AsyncClient() as client:
        response = await client.post(f"{API_URL}/api/your-endpoint",
                                   json={"param": param})
        return response.json()
```

### Adding New Service Classes

**Service Pattern:**

```python
class YourService:
    def __init__(self, supabase_client=None):
        self.supabase_client = supabase_client or get_supabase_client()

    def your_operation(self, param: str) -> Tuple[bool, Dict[str, Any]]:
        try:
            # Business logic here
            result = self.supabase_client.table("table").insert(data).execute()
            return True, {"data": result.data}
        except Exception as e:
            logger.error(f"Error in operation: {e}")
            return False, {"error": str(e)}
```

## 🤝 Community Standards

### Communication Guidelines

- **Be Constructive**: Focus on improving the codebase and user experience
- **Be Specific**: Provide detailed examples and reproduction steps
- **Be Collaborative**: Welcome diverse perspectives and approaches
- **Be Patient**: Allow time for review and discussion

### Code Review Process

**As a Contributor:**

- Write clear PR descriptions
- Respond promptly to review feedback
- Test your changes thoroughly

**As a Reviewer:**

- Focus on architecture, correctness, and user impact
- Provide specific, actionable feedback
- Acknowledge good practices and improvements

## 📞 Getting Help

- **GitHub Issues**: For bugs, feature requests, and questions
- **Architecture Questions**: Use the GitHub discussions

## 🎖️ Recognition

Contributors receive:

- **Attribution**: Recognition in release notes and documentation
- **Maintainer Track**: Path to maintainer role for consistent contributors
- **Community Impact**: Help improve AI development workflows for thousands of users

---

**Ready to contribute?** Start by exploring the codebase, reading the architecture documentation, and finding an area that interests you. Every contribution makes Archon better for the entire AI development community.
