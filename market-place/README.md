================================================================================
                    E-COMMERCE MARKETPLACE PLATFORM
================================================================================

A full-featured microservices-based e-commerce marketplace with real-time 
inventory management, shopping cart, order processing, and integrated payment 
system.

================================================================================
                            TABLE OF CONTENTS
================================================================================

1. Features
2. Architecture
3. Technologies Used
4. Prerequisites
5. Installation & Setup
6. Running the Application
7. API Documentation
8. User Roles
9. Project Structure
10. Environment Variables
11. Troubleshooting


================================================================================
                                FEATURES
================================================================================

FOR CUSTOMERS:
--------------
- User Authentication - Secure login with Keycloak
- Shopping Cart - Add, update, and remove items with real-time stock validation
- Order Management - Create orders and track order history
- Multiple Payment Methods:
  * Mock PayPal (for testing)
  * Real PayPal Sandbox integration
  * Cash on Delivery (COD)
- Product Search - Browse and search inventory
- Order Cancellation - Cancel orders and restore inventory

FOR ADMINS:
-----------
- Admin Dashboard - Full inventory management capabilities
- Product Management - Add, edit, and delete products
- Stock Management - Add or remove stock quantities
- Order Overview - View all orders from all users
- Order Administration - Cancel any order as admin


================================================================================
                              ARCHITECTURE
================================================================================

This project uses a MICROSERVICES ARCHITECTURE with the following services:

                         Traefik (Reverse Proxy)
                                  |
        +-------------------------+-------------------------+
        |                         |                         |
    Frontend                  Orders                  Inventory
    (React)                 (Node.js)                 (Node.js)
        |                         |                         |
        |                         +------------+------------+
        |                                      |
     Cart                                  Payments              Keycloak
  (Node.js)                               (Node.js)               (Auth)
        |                                      |                    |
        +----------------------+---------------+--------------------+
                               |
                          PostgreSQL
                          (Database)
                               |
                           RabbitMQ
                        (Message Queue)


SERVICE BREAKDOWN:
------------------

1. Frontend Service (Port 3001)
   - React application with Vite
   - User interface for browsing, shopping, and checkout
   - Keycloak integration for authentication

2. Orders Service (Port 3000)
   - Manages order creation and lifecycle
   - Publishes events to RabbitMQ
   - Handles order completion and cancellation

3. Inventory Service (Port 3004)
   - Manages product catalog
   - Handles stock levels
   - Listens to order events for inventory updates
   - Admin endpoints for product management

4. Cart Service (Port 3005)
   - User shopping cart management
   - Stock validation before adding items
   - Persistent cart storage per user

5. Payments Service (Port 3002)
   - PayPal Sandbox integration
   - Mock PayPal for testing
   - Cash on Delivery support

6. Keycloak (Port 8081)
   - Authentication and authorization
   - User management
   - JWT token generation

7. Supporting Services
   - PostgreSQL - Database for all services
   - RabbitMQ - Message queue for event-driven architecture
   - Traefik - Reverse proxy and load balancer


================================================================================
                          TECHNOLOGIES USED
================================================================================

BACKEND:
--------
- Node.js - Runtime environment
- Express.js - Web framework
- PostgreSQL - Relational database
- RabbitMQ - Message broker for event-driven communication
- Keycloak - Identity and access management
- Docker - Containerization
- Docker Compose - Multi-container orchestration

FRONTEND:
---------
- React - UI library
- Vite - Build tool and dev server
- Keycloak-js - Authentication client

DEVOPS:
-------
- Traefik - Reverse proxy
- Docker - Container platform
- NGINX - Static file server for frontend


================================================================================
                            PREREQUISITES
================================================================================

Before you begin, ensure you have the following installed:

- Docker Desktop (v20.10+)
- Docker Compose (v2.0+)
- Git
- Node.js (v18+) - Optional, for local development

SYSTEM REQUIREMENTS:
--------------------
- RAM: Minimum 8GB (16GB recommended)
- Disk Space: At least 10GB free
- OS: Windows 10/11, macOS 10.15+, or Linux


================================================================================
                        INSTALLATION & SETUP
================================================================================

STEP 1: CLONE THE REPOSITORY
-----------------------------
git clone https://github.com/yourusername/marketplace-project.git
cd marketplace-project


STEP 2: ENVIRONMENT CONFIGURATION
----------------------------------
The project uses default environment variables defined in docker-compose.yml.
No additional configuration is needed for basic setup.


STEP 3: BUILD AND START SERVICES
---------------------------------
Navigate to the project directory:
cd "market place"

Start all services:
docker-compose up -d

View logs:
docker-compose logs -f

View logs for specific service:
docker-compose logs -f frontend


STEP 4: WAIT FOR SERVICES TO INITIALIZE
----------------------------------------
All services need time to start up. Wait approximately 2-3 minutes for:
- PostgreSQL database initialization
- RabbitMQ to be ready
- Keycloak realm import
- All microservices to connect


================================================================================
                       RUNNING THE APPLICATION
================================================================================

ACCESS THE APPLICATION
----------------------
Once all services are running, access the application at:

- Frontend: http://localhost:3001
- Keycloak Admin: http://localhost:8081 (admin/admin)
- Traefik Dashboard: http://localhost:8080
- RabbitMQ Management: http://localhost:15672 (guest/guest)


DEFAULT USER ACCOUNTS
---------------------
The system comes with two pre-configured accounts:

REGULAR USER:
  Username: test
  Password: test
  Permissions: Browse products, add to cart, create orders

ADMIN USER:
  Username: admin
  Password: admin
  Permissions: All user permissions + inventory management + view all orders


================================================================================
                          API DOCUMENTATION
================================================================================

ORDERS SERVICE (Port 3000)
--------------------------
Method  Endpoint                     Auth      Description
------  --------                     ----      -----------
GET     /orders                      Required  Get current user's orders
GET     /orders/all                  Admin     Get all orders (admin only)
GET     /orders/:id                  Required  Get single order
POST    /orders                      Required  Create new order
POST    /orders/:id/complete         Required  Complete order with payment
POST    /orders/:id/confirm-cod      Required  Confirm Cash on Delivery order
POST    /orders/:id/cancel           Required  Cancel order and restore inventory


INVENTORY SERVICE (Port 3004)
-----------------------------
Method  Endpoint                     Auth      Description
------  --------                     ----      -----------
GET     /inventory/search?q=         Public    Search products
GET     /inventory/:sku              Public    Get product by SKU
POST    /inventory                   Admin     Add new product
PUT     /inventory/:sku              Admin     Update product
POST    /inventory/:sku/add-stock    Admin     Add stock quantity
POST    /inventory/:sku/remove-stock Admin     Remove stock quantity
DELETE  /inventory/:sku              Admin     Delete product


CART SERVICE (Port 3005)
------------------------
Method  Endpoint          Auth      Description
------  --------          ----      -----------
GET     /cart             Required  Get user's cart
POST    /cart             Required  Add item to cart
PUT     /cart/:sku        Required  Update item quantity
DELETE  /cart/:sku        Required  Remove item from cart
DELETE  /cart             Required  Clear entire cart


PAYMENTS SERVICE (Port 3002)
----------------------------
Method  Endpoint                           Auth      Description
------  --------                           ----      -----------
POST    /payments/create-with-credentials  Required  Create PayPal order
POST    /payments/:id/status               Required  Check payment status
POST    /payments/:id/capture              Required  Capture PayPal payment


================================================================================
                              USER ROLES
================================================================================

CUSTOMER ROLE:
--------------
- Browse and search products
- Add items to cart
- Create and manage orders
- View order history
- Cancel own orders

ADMIN ROLE (username: admin):
-----------------------------
- All customer permissions
- Add/edit/delete products
- Manage inventory stock levels
- View all orders from all users
- Cancel any order


================================================================================
                          PROJECT STRUCTURE
================================================================================

market-place/
|
+-- docker-compose.yml              Main orchestration file
|
+-- migrations/                     Database initialization scripts
|   +-- 001-create-orders.sql
|   +-- 002-seed-inventory.sql
|
+-- keycloak/                       Keycloak configuration
|   +-- realm-export.json           Pre-configured realm
|
+-- services/
|   |
|   +-- frontend/                   React frontend
|   |   +-- src/
|   |   |   +-- main.jsx
|   |   |   +-- styles.css
|   |   |   +-- cart.jsx
|   |   |   +-- searchinventory.jsx
|   |   |   +-- orderhistory.jsx
|   |   |   +-- admininventory.jsx
|   |   |   +-- allorders.jsx
|   |   |   +-- payment.jsx
|   |   +-- Dockerfile
|   |   +-- package.json
|   |
|   +-- orders/                     Orders microservice
|   |   +-- src/
|   |   |   +-- index.js
|   |   |   +-- publishOrderEvent.js
|   |   +-- Dockerfile
|   |   +-- package.json
|   |
|   +-- inventory/                  Inventory microservice
|   |   +-- src/
|   |   |   +-- index.js
|   |   |   +-- consumer.js
|   |   +-- Dockerfile
|   |   +-- package.json
|   |
|   +-- cart/                       Cart microservice
|   |   +-- src/
|   |   |   +-- index.js
|   |   +-- Dockerfile
|   |   +-- package.json
|   |
|   +-- payments/                   Payments microservice
|       +-- src/
|       |   +-- webhook.js
|       +-- Dockerfile
|       +-- package.json
|
+-- shared/                         Shared utilities
    +-- keycloakAuth.js


================================================================================
                        ENVIRONMENT VARIABLES
================================================================================

Default environment variables are configured in docker-compose.yml:

DATABASE:
---------
POSTGRES_DB: marketplace
POSTGRES_USER: marketplace
POSTGRES_PASSWORD: marketplace
DATABASE_URL: postgres://marketplace:marketplace@postgres:5432/marketplace

RABBITMQ:
---------
RABBITMQ_URL: amqp://guest:guest@rabbitmq:5672

KEYCLOAK:
---------
KEYCLOAK_URL: http://keycloak:8080
KEYCLOAK_REALM: marketplace
KEYCLOAK_CLIENT_ID: marketplace-web

PAYPAL (Optional - for real payments):
---------------------------------------
PAYPAL_CLIENT_ID: your-client-id
PAYPAL_CLIENT_SECRET: your-client-secret
PAYPAL_MODE: sandbox


================================================================================
                            TROUBLESHOOTING
================================================================================

SERVICES WON'T START:
---------------------
Stop all services:
  docker-compose down

Remove volumes (WARNING: This will delete data):
  docker-compose down -v

Rebuild and start:
  docker-compose up --build


PORT ALREADY IN USE:
--------------------
If you get "port already allocated" errors:

Windows - Check what's using the port:
  netstat -ano | findstr :3001

Mac/Linux - Check what's using the port:
  lsof -i :3001

Solution: Stop the service or change the port in docker-compose.yml


DATABASE ISSUES:
----------------
Reset database:
  docker-compose down -v
  docker-compose up postgres -d

Check database logs:
  docker-compose logs postgres


AUTHENTICATION ISSUES:
----------------------
1. Make sure Keycloak is running: http://localhost:8081
2. Clear browser cookies and localStorage
3. Check Keycloak logs: docker-compose logs keycloak


RABBITMQ CONNECTION ISSUES:
---------------------------
Check RabbitMQ status:
  docker-compose logs rabbitmq

Restart RabbitMQ:
  docker-compose restart rabbitmq


================================================================================
                                TESTING
================================================================================

TEST USER FLOW:
---------------
1. Login with test/test
2. Browse products
3. Add items to cart
4. Checkout with Mock PayPal or COD
5. View order history

TEST ADMIN FLOW:
----------------
1. Login with admin/admin
2. Navigate to "Manage Inventory" tab
3. Add a new product
4. Adjust stock levels
5. View all orders from all users


================================================================================
                      STOPPING THE APPLICATION
================================================================================

Stop all services (keeps data):
  docker-compose down

Stop and remove all data:
  docker-compose down -v

Stop specific service:
  docker-compose stop frontend


================================================================================
                        FEATURES IN DETAIL
================================================================================

EVENT-DRIVEN ARCHITECTURE:
--------------------------
The system uses RabbitMQ for event-driven communication:

- order.created - Published when order is created
- order.completed - Published when payment succeeds -> Inventory deducted
- order.cancelled - Published when order is cancelled -> Inventory restored


STOCK VALIDATION:
-----------------
- Cart service validates stock before adding items
- Inventory is only deducted after successful payment
- Cancelled orders automatically restore inventory


AUTHENTICATION FLOW:
--------------------
1. User logs in via Keycloak
2. Receives JWT token
3. Token sent with each API request
4. Services decode and validate token
5. User info extracted from token


================================================================================
                              DEVELOPMENT
================================================================================

RUNNING SERVICES LOCALLY:
-------------------------
You can run individual services locally for development:

Install dependencies:
  cd services/orders
  npm install

Run locally:
  DATABASE_URL=postgres://marketplace:marketplace@localhost:5432/marketplace
  RABBITMQ_URL=amqp://guest:guest@localhost:5672
  node src/index.js


ADDING NEW PRODUCTS:
--------------------
Use the Admin panel or directly via API:

curl -X POST http://localhost:3004/inventory \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "sku": "NEW-001",
    "name": "New Product",
    "price": 99.99,
    "quantity": 50
  }'


================================================================================
                                LICENSE
================================================================================

This project is for educational purposes.


================================================================================
                                 AUTHOR
================================================================================

Your Name
- GitHub: @hazem ehab fawzy
- LinkedIn: https://www.linkedin.com/in/hazem-ehab-20b809303/


================================================================================
                           ACKNOWLEDGMENTS
================================================================================

- Keycloak for authentication
- PayPal for payment integration
- Docker for containerization
- React for the frontend framework


================================================================================
                                SUPPORT
================================================================================

If you encounter any issues:

1. Check the Troubleshooting section
2. Review Docker logs: docker-compose logs -f
3. Create an issue on GitHub


================================================================================
              Built with Microservices Architecture
================================================================================
