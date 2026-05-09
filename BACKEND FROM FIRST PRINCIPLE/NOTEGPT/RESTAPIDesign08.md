[00:00:00]
### Introduction to REST API Design
- API design is a critical and frequent task for backend engineers.
- The video focuses specifically on REST APIs, one of the most widely used API standards, excluding other technologies like RPC and GraphQL.
- Despite years of experience and standards, many developers still face confusion regarding REST API design decisions such as:
    - Whether URI path segments should be plural or singular.
    - Choosing between HTTP methods like PATCH vs PUT for updates.
    - Handling non-CRUD operations (custom actions).
    - Selecting appropriate HTTP status codes.
- The original HTTP and REST standards were created under different technological contexts (multi-page applications vs modern single-page applications).
- The goal is **not to create new standards but to clarify and standardize best practices within existing REST API guidelines** to reduce confusion and improve consistency.

[00:04:25]
### Historical Context and Origins of REST
- Tim Berners-Lee invented the World Wide Web in 1990 to facilitate global knowledge sharing.
- Within a year, foundational technologies were developed:
    - **URI (Uniform Resource Identifier)**
    - **HTTP (HyperText Transfer Protocol)**
    - **HTML (markup language for web pages)**
    - Web servers and browsers, including the first WYSIWYG HTML editor.
- These technologies evolved into modern forms: HTTP 1.1, 2.0, 3.0, modern browsers, and servers.
- The rapid exponential growth of web users caused scalability challenges the original design did not anticipate.
- Roy Fielding, co-founder of Apache HTTP Server, proposed architectural constraints in 1993 aimed at improving scalability of the web.

[00:08:30]
### REST Architectural Constraints by Roy Fielding
Fielding defined **six constraints** for scalable web architecture, collectively known as REST:

| Constraint                | Description                                                                                          | Purpose                                           |
|---------------------------|--------------------------------------------------------------------------------------------------|--------------------------------------------------|
| 1. Client-Server          | Separation of concerns: client handles UI, server manages data and logic                         | Independent evolution, better scalability         |
| 2. Uniform Interface      | Standardized communication interface with four sub-constraints (resource ID, manipulation via representation, self-descriptive messages, hypermedia as engine of application state) | Simplicity and consistency                         |
| 3. Layered System         | Hierarchical layers; each layer interacts only with adjacent layers                              | Scalability, security, adding intermediaries (e.g., load balancers) |
| 4. Cacheable              | Responses must be explicitly marked cacheable or non-cacheable                                  | Reduce server load, improve network efficiency    |
| 5. Stateless              | Each request contains all info for processing; server retains no client context                  | Reliability, scalability, any server can handle requests |
| 6. Code on Demand (optional) | Servers can extend client functionality by transferring executable code (e.g., JavaScript)    | Adds flexibility, but rarely used                   |

- These constraints **form the foundation of REST** as described in Fielding's 2000 PhD dissertation.
- Fielding and Berners-Lee jointly contributed to the HTTP 1.1 specification.

[00:14:10]
### Meaning of REST: Representational State Transfer
- **Representational**: Resources (objects/data) are represented in different formats based on client needs, commonly JSON, XML, or HTML.
    - Example: A user resource could be represented as JSON for server-to-server communication or as HTML for browsers.
- **State**: The current attributes or condition of a resource, e.g., a shopping cart’s items and quantities.
- **Transfer**: Movement of resource representations between client and server via HTTP methods (GET, POST, PUT, DELETE, PATCH, etc.).

**Summary:** REST describes an architectural style where:
- Resources have multiple representations.
- Resource state is transferred between client and server.
- The system follows specific constraints to ensure scalability and consistency.

[00:21:57]
### Anatomy of a URL and REST API Endpoint Design
- A URL consists of:
    - Scheme (`http` or `https`)
    - Authority/domain (e.g., `api.example.com`)
    - Resource path (hierarchical, separated by `/`)
    - Optional query parameters (key-value pairs, mainly for GET requests)
    - Optional fragment (for navigating to sections within a page)

- REST API URLs typically follow these conventions:
    - Start with scheme and subdomain `api.`
    - Include versioning (e.g., `/v1/`)
    - Resource names always **in plural form** in URI path segments (e.g., `/books/` even when accessing a single book).
    - Use slugs for human-readable identifiers (e.g., `/books/harry-potter`) — convert spaces to hyphens and lowercase for URL safety.
    - The forward slash `/` indicates hierarchical relationships between resources (e.g., `/books/{id}` means accessing a book resource under the books collection).

[00:31:20]
### Idempotency in REST APIs
- **Idempotency** means performing the same operation multiple times results in the same effect as performing it once — the side effects do not change with repeated calls.
- HTTP methods and their idempotency:

| HTTP Method | Idempotent? | Description                                                                                          | Example                                         |
|-------------|-------------|--------------------------------------------------------------------------------------------------|------------------------------------------------|
| GET         | Yes         | Retrieves data without changing server state                                                     | Fetch list of books                             |
| PATCH       | Yes         | Partially updates a resource; multiple identical updates have same effect                        | Update user's name partially                    |
| PUT         | Yes         | Fully replaces a resource representation                                                        | Replace entire user object                       |
| DELETE      | Yes         | Deletes a resource; repeated deletes yield same result (resource not found after first delete)  | Delete a user                                   |
| POST        | No          | Creates a new resource or triggers a custom action; repeated calls create multiple resources    | Create multiple books with same data            |

- **POST is the only non-idempotent method** generally because it creates new entities with each call or triggers custom actions.

[00:33:14]
### Detailed HTTP Methods and Use Cases
- **GET**: Fetch data, no side effect, idempotent.
- **PATCH**: Update part of resource, idempotent (e.g., change user name).
- **PUT**: Replace full resource, idempotent (requires sending full resource payload).
- **DELETE**: Remove resource, idempotent (deleting already deleted resource returns 404).
- **POST**: Create resource or perform custom action, non-idempotent (multiple calls create multiple resources).
- For **non-CRUD custom actions** (e.g., "send email"), use POST with an action path.

[00:46:39]
### API Interface Design Principles
- Before coding business logic, **design the API interface**: routes, payloads, responses, HTTP methods, status codes.
- Design should be **intuitive, consistent, and adhere to REST standards** to avoid confusion and reduce guesswork for API consumers.
- Following standards reduces integration time, bugs, and support overhead.
- Start API design from UI wireframes or user stories to understand how end-users interact with data, then map to backend resources and actions.

[00:52:36]
### Identifying Resources
- Resources are generally **nouns** extracted from requirements or wireframes.
- Example: In a project management platform, resources include:
    - `organizations`
    - `projects`
    - `tasks`
    - `users`
    - `tags`

[00:56:03]
### Workflow Summary for API Design
1. Analyze UI wireframes and user requirements to identify resources (nouns).
2. Design and finalize the database schema (covered in other videos).
3. Design API interface (routes, methods, payloads) based on resources and CRUD actions.
4. Implement API logic (programming, database integration).

[00:58:26]
### Example API Design Using Insomnia (Demo)
- Resource: `organizations`
- CRUD endpoints:
    - `GET /organizations` — list all organizations
    - `POST /organizations` — create an organization
    - `GET /organizations/{id}` — get a single organization
    - `PATCH /organizations/{id}` — update organization partially
    - `DELETE /organizations/{id}` — delete organization

- **Create organization example:**
    - Method: POST
    - URL: `http://localhost:3000/organizations`
    - Payload: JSON with fields excluding server-handled IDs and timestamps (e.g., `{ "name": "Arc One", "status": "active", "description": "some description" }`)
    - Response: `201 Created` with created entity returned.

- **List organizations example:**
    - Method: GET
    - URL: `http://localhost:3000/organizations`
    - Response: `200 OK` with paginated data structure.

[01:05:23]
### Pagination
- Pagination is essential for APIs returning lists to avoid sending large datasets in one response.
- Typical pagination response includes:

| Field         | Description                                                                                  |
|---------------|----------------------------------------------------------------------------------------------|
| `data`        | Array of resource items returned in this page                                               |
| `total`       | Total number of resources in the database                                                   |
| `page`        | Current page number                                                                          |
| `totalPages`  | Total number of pages available                                                             |  

- Query parameters used for pagination:
    - `limit` — number of items per page
    - `page` — page number requested
- Defaults: If not specified, server should set sensible defaults (e.g., `limit=10`, `page=1`).
- Pagination improves serialization performance, reduces network load, and enhances user experience with progressive data loading.

[01:16:03]
### Sorting and Filtering in List APIs
- Sorting parameters:
    - `sortBy` — field to sort on (e.g., name, createdAt)
    - `sortOrder` — ascending or descending (default descending)
- Filtering parameters allow clients to retrieve filtered subsets, e.g., filtering organizations by status (`active`, `archived`) or by name.
- Example: `GET /organizations?status=archived` returns only archived organizations.

[01:24:22]
### Update API with PATCH
- PATCH is preferred for partial updates.
- URL: `/organizations/{id}`
- Payload: only fields to update (e.g., `{ "status": "active" }`)
- Response: `200 OK` with updated resource data.
- PUT (full replacement) is less common in modern SPA/JSON APIs because partial updates are more frequent.

[01:27:25]
### Fetch Single Resource
- GET `/organizations/{id}` returns a single resource.
- No request body.
- Response code: `200 OK` if found, `404 Not Found` if missing.

[01:29:07]
### Delete API
- DELETE `/organizations/{id}` deletes a resource.
- Response status: `204 No Content` (successful deletion, no response body).
- Repeated deletes return `404 Not Found` since resource no longer exists.

[01:33:17]
### Handling Custom Actions
- Custom actions do not fit CRUD semantics (create, read, update, delete).
- Example: Archiving an organization, which may trigger multiple backend operations beyond just updating a field.
- Custom actions use POST on a route like:  
  `/organizations/{id}/archive`
- Response can be `200 OK` or other based on action result.
- POST is open-ended for custom actions per REST specification.

[01:39:25]
### Designing APIs for Other Resources (Projects Example)
- Follow same patterns as organizations:
    - Plural resource names (`/projects`)
    - CRUD endpoints with consistent usage of HTTP methods and URL structure.
- JSON payload fields should maintain **consistent naming conventions** (e.g., camelCase, consistent use of `name`, `description` fields across resources).
- Consistency in naming and structure reduces confusion for API consumers and simplifies integration.

[01:45:14]
### Consistency and Standards
- Consistency across endpoints is crucial:
    - Plural resource names
    - JSON payload field names
    - Response structures
    - Query parameter names and semantics
- Avoid abbreviations or inconsistent naming to prevent guesswork and integration errors.
- Consistency supports intuitive API consumption and reduces documentation overhead.

[01:53:35]
### Example of Custom Action: Cloning a Project
- Cloning is a custom action because it may involve complex server-side logic (copying nested tasks, sending notifications).
- API endpoint:  
  `POST /projects/{id}/clone`
- POST used to trigger the action.
- Response can be `201 Created` if a new resource is generated by the action.

[01:56:36]
### Best Practices Summary for API Design
- Provide **interactive API documentation** (e.g., Swagger/OpenAPI) to aid consumers in understanding and testing APIs.
- APIs should be **intuitive and consistent** in routes, methods, payloads, responses, and error handling.
- Implement **safe defaults** for query parameters (pagination, sorting) and payload fields (e.g., default statuses).
- Avoid abbreviations or unclear field names in JSON payloads and parameters.
- Design APIs **before coding**—use API design tools (Insomnia, Postman, Swagger) to finalize interface decisions.
- A well-designed API interface reduces integration errors, development time, and maintenance burden.

[02:02:02]
### Final Remarks
- API design is a deliberate, thoughtful process that precedes coding.
- Backend engineers should dedicate time to API interface design independent of language or framework choices.
- Following RESTful principles and best practices leads to **delightful, intuitive, and maintainable APIs**.
- This video provides a comprehensive foundation to design REST APIs effectively from start to finish.

---

**End of Summary**