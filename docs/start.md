# Welcome
Warmest welcome to the Shoehorn world. The friendly helper, Buddy, you've met at the login page. Buddy is here to help!

## What is Shoehorn?

Shoehorn is an Internal Developer Portal designed to bring clarity and order to your software ecosystem. In modern organizations, services, libraries, and infrastructure proliferate rapidly, making it difficult to maintain a clear picture of what exists and who owns it. Shoehorn solves this problem by providing a centralized catalog with powerful search and discovery capabilities.

Unlike heavyweight enterprise solutions, Shoehorn prioritizes simplicity and maintainability. The entire platform can be maintained by a small team, and the architecture avoids unnecessary complexity. Everything you need is included out of the box: entity management, documentation hosting, team organization, and full-text search.

The platform integrates seamlessly with your existing tools and workflows. GitHub repositories are automatically crawled to discover services and documentation. Identity providers like Keycloak handle authentication, eliminating the need for separate user management. The REST API enables custom integrations and automation.

## Why Use an Internal Developer Portal?

As engineering organizations grow, knowledge becomes scattered across wikis, README files, and tribal knowledge. New team members struggle to understand system architecture and find relevant documentation. Existing developers waste time searching for information or asking colleagues for context.

An Internal Developer Portal addresses these challenges by creating a single source of truth for software assets. Every service, library, and infrastructure component is cataloged with metadata including ownership, dependencies, and documentation links. Teams can quickly discover what already exists before building something new.

The portal also improves operational excellence by making ownership explicit. Each entity has clear owners who are responsible for maintenance and support. When incidents occur, responders can quickly identify who to contact. This accountability reduces mean time to resolution and prevents services from becoming unmaintained.

## Key Concepts

Understanding Shoehorn's core concepts helps you make the most of the platform. Entities are the fundamental building blocks, representing any software component you want to track. Common entity types include services, websites, libraries, and resources like databases or message queues.

Teams represent groups of people who build and maintain software together. Teams own entities, making responsibility clear. A team might own multiple services, or a service might be co-owned by multiple teams for shared components. The flexible ownership model adapts to your organizational structure.

Documentation is discoverable through the portal rather than buried in repository folders. Shoehorn indexes Markdown files from your repositories and makes them searchable. TechDocs support enables structured documentation that follows entities throughout their lifecycle.

Relationships connect entities to show dependencies and integrations. A service might depend on a database resource and consume APIs from other services. These relationships create a graph that visualizes your system architecture and helps understand blast radius during incidents.

## The Dashboard Experience

When you first log into Shoehorn, the dashboard provides an overview of your software catalog. Recent activity shows what's been added or updated, helping you stay aware of changes. Quick access to your team's entities makes it easy to navigate to services you work on regularly.

The search bar is prominently featured because discovery is central to the portal's value. Type a few characters and results appear instantly, categorized by type. Search across entities, documentation, teams, and repositories simultaneously. Keyboard shortcuts make search accessible from anywhere in the application.

Personalized recommendations surface relevant entities based on your team membership and recent activity. If you frequently access certain services, they'll appear in quick links for faster navigation. The dashboard adapts to your usage patterns over time.

## Exploring the Catalog

The entity catalog is the heart of Shoehorn, containing all registered software components. Browse entities by type, filter by ownership, or search by name and description. Each entity has a dedicated page showing comprehensive metadata and relationships.

Entity pages display ownership information prominently, making it clear who to contact with questions. Links to source repositories, deployment dashboards, and API documentation provide quick access to related resources. The page also shows other entities that depend on or are depended upon by this entity.

Documentation associated with an entity appears in an integrated viewer. No need to open repositories or external sites – read documentation directly in the portal. The viewer supports navigation between pages and highlights search matches when you arrive from search results.

Relationship graphs visualize how entities connect. See immediate dependencies at a glance, or expand to show the full dependency tree. These visualizations help understand system complexity and plan migrations or deprecations. Interactive graphs let you explore by clicking nodes to navigate to related entities.

## Teams and Ownership

Teams are defined in Shoehorn with members imported from your identity provider. Each team has a profile page listing members, owned entities, and contact information. Teams can be organized hierarchically to reflect your organizational structure, with parent and child relationships.

Assigning ownership to entities creates accountability and makes it clear who maintains each component. When creating or editing an entity, select one or more owning teams from a dropdown. Ownership can change over time as teams reorganize or services are transferred between groups.

Team pages show a consolidated view of everything a team owns. This perspective helps teams track their responsibilities and identify services that may need attention. Managers can use these pages during planning to understand team scope and capacity.

The portal respects team boundaries for access control. While catalog metadata is typically visible to all users, sensitive entities can be restricted to specific teams. This flexibility accommodates both open and confidential components within the same catalog.

## Search and Discovery

Search is designed for speed and relevance, powered by Typesense for instant full-text search. Results appear as you type, with no need to press enter or wait for a search button. Query highlighting shows exactly where your search terms matched, providing context even in long documents.

Results are grouped by type: entities, repositories, documentation, teams, and users. Each group shows the most relevant matches, with an option to see all results of that type. This categorization helps you quickly find the right kind of result for your query.

Filters narrow results by type, source, or custom facets. If you're only looking for services, filter to the service entity type. Searching for documentation? Filter to the docs type and get only document results. Combine filters to create precise queries for specific needs.

The command palette provides universal search access via keyboard shortcut. Press Cmd+K (or Ctrl+K) from anywhere in the application to open the search interface. Start typing immediately without clicking into a search box. Navigate results with arrow keys and press enter to jump to the selected item.

## Working with Documentation

Documentation in Shoehorn follows the docs-as-code principle, where documentation lives alongside code in repositories. Markdown files in configured directories are automatically discovered and indexed. This approach keeps documentation close to implementation and enables version control.

TechDocs take this further by structuring documentation around entities. Each entity can have associated documentation that follows a standard format. Navigation, search, and presentation are handled by Shoehorn, so teams focus on writing content rather than building documentation sites.

The documentation viewer supports common Markdown features including code blocks with syntax highlighting, tables, images, and internal links. Navigation between documentation pages works seamlessly, creating a cohesive reading experience even when pages are spread across multiple repositories.

Search within documentation is context-aware, understanding sections and headings. When you search for a term, results include the section where it appears, not just the page. This granularity helps you jump directly to relevant information without reading entire documents.

## API Integration

Shoehorn exposes a comprehensive REST API for programmatic access to catalog data. Read entity information, search the catalog, and retrieve relationships through simple HTTP requests. The API uses standard REST conventions with JSON request and response bodies.

Authentication for the API uses the same identity provider as the web interface. Generate API tokens from your user profile to authenticate programmatic requests. Tokens can be scoped to specific permissions, enabling secure integrations with least-privilege access.

Common integration scenarios include building custom dashboards, automating catalog updates, and creating chatbots that answer questions about services. The API provides all the data needed for these use cases without requiring direct database access.

Documentation for the API includes examples in multiple languages and frameworks. Interactive API explorers let you test requests directly from the browser. OpenAPI specifications are available for generating client libraries in your preferred programming language.

## Customization and Extension

While Shoehorn works great out of the box, customization enables adaptation to your specific needs. Entity schemas support custom fields through annotations and labels. Add organization-specific metadata like cost center, compliance status, or business criticality without modifying core code.

The frontend is built with Svelte 5 and follows a component-based architecture. Create custom components for specialized visualizations or workflows. The component library uses shadcn/ui for consistent styling, making custom components blend seamlessly with built-in features.

Backend plugins can extend API functionality with custom endpoints. Write plugins in Go and register them with the handler system. Plugins have access to the database and search index, enabling sophisticated custom features while maintaining type safety and performance.

Webhooks enable real-time notifications when entities change. Configure webhooks to call your services when entities are created, updated, or deleted. This integration pattern enables event-driven architectures and keeps external systems synchronized with the catalog.

## Security and Access Control

Shoehorn takes security seriously with multiple layers of protection. Authentication is delegated to your existing identity provider, leveraging proven security infrastructure. OAuth 2.0 and OpenID Connect protocols ensure secure token exchange and user identity verification.

Authorization uses Casbin for flexible role-based access control. Define policies that grant permissions based on user roles, team membership, or entity attributes. Policies are evaluated efficiently at request time, enabling fine-grained access control without performance impact.

Sensitive entities can be restricted to specific teams or users. Mark entities as confidential to limit visibility to owners and designated viewers. This feature supports organizations with both open internal development and confidential projects requiring restricted access.

API tokens are scoped to specific permissions and can be revoked at any time. Monitor token usage through audit logs to detect potential security issues. Rotate tokens regularly as part of security best practices.

## Performance and Scaling

Shoehorn is designed to handle catalogs ranging from dozens to thousands of entities. The hybrid search architecture uses PostgreSQL for structured queries and Typesense for full-text search, providing optimal performance for different query patterns.

Database queries are optimized with appropriate indexes and use efficient join strategies. The sqlc tool generates type-safe queries that are validated at compile time, preventing runtime query errors. Connection pooling manages database connections efficiently even under high load.

The frontend is built as a SvelteKit application with server-side rendering for fast initial page loads. Code splitting ensures users only download JavaScript for the pages they visit. Static assets are cached with long TTLs and versioned filenames for cache invalidation.

Horizontal scaling is supported by running multiple API instances behind a load balancer. The stateless architecture means requests can be handled by any instance. Shared database and search services provide consistency across instances.

## Community and Contribution

Shoehorn is open source and welcomes contributions from the community. The project is hosted on GitHub with public issue tracking and pull request reviews. Contributing guidelines explain the process for submitting changes and what to expect during review.

Feature requests and bug reports help improve the platform for everyone. Before opening an issue, search existing issues to avoid duplicates. Provide detailed reproduction steps for bugs and explain use cases for feature requests to help maintainers understand and prioritize.

Documentation contributions are especially valuable and great for first-time contributors. If you found something confusing or missing, submit a pull request to improve it. Documentation changes are reviewed quickly and merged frequently.

Join community discussions to connect with other Shoehorn users and maintainers. Share your experiences, ask questions, and learn how others are using the platform. Community members often help each other with implementation questions and share custom extensions.

## Getting Help

If you encounter issues or have questions, several resources are available. Start with the documentation, which covers installation, configuration, and common use cases. The troubleshooting section addresses frequent problems with step-by-step solutions.

GitHub Discussions provides a forum for questions and conversations. Search existing discussions to see if your question has been answered. Post new discussions with detailed context about your setup and what you're trying to accomplish.

For bugs and feature requests, open GitHub issues with clear descriptions and reproduction steps. Include relevant log output and configuration details when reporting bugs. Screenshots help illustrate UI issues and expected versus actual behavior.

Commercial support is available for organizations needing dedicated assistance. Support plans include priority issue resolution, architecture consulting, and custom development. Contact the maintainers for more information about support options.
