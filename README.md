# Summary

Saas has an admin and a public website. The public website is mobile friendly. The admin website is intended to be used by internal employees. The application will use Microservice Architecture.

# Non Functional Requirements
Site should be accessible via desktop and mobile view - This will address by making the website responsive hence, it will give the mobile users a seamless experience. Also, it saves us time and cost.

Product search should be able to handle 5000 TPS 
Cart functionality should be able to handle 1000 TPS 
Purchasing functionality should be able to handle 250 TPS

    - Set the correct resources for the container as shown below,
    ```json
    spec:
        containers:
            - name: example
            image: example:latest
            resources:
                limits:
                    cpu: 500m
                    memory: 512Mi
                requests:
                    cpu: 200m
                    memory: 256Mi
    ```

    - Setup liveness and readiness probe so that the k8s cluster can hit the actuator health end point and automatically manage in case of unresponsiveness.
    ```json
    livenessProbe:
        httpGet:
            path: /actuator/health/liveness
            port: 8080
        initialDelaySeconds: 30
        periodSeconds: 10
    readinessProbe:
        httpGet:
            path: /actuator/health/readiness
            port: 8080
        initialDelaySeconds: 10
        periodSeconds: 5
    ```

    - Set up Horizontal Pod Autoscaler in the cluster to automatically up/down pods based on CPU or memory utilization
    ```json
    apiVersion: autoscaling/v2beta2
    kind: HorizontalPodAutoscaler
    metadata:
        name: example-hpa
    spec:
        scaleTargetRef:
            apiVersion: apps/v1
            kind: Deployment
            name: example-deployment
        minReplicas: 1
        maxReplicas: 5
    metrics:
        type: Resource
        resource:
        name: memory
        target:
            type: Utilization
            averageUtilization: 70
    ```

# Context Diagram

The application has two roles admin, end-customer. The admin have a dedicated admin view which is separate from the public website.

## Admin application context diagram

![Admin application Context Diagram](context-diagram/admin-context-view.png)

## Saas shop public site context diagram

![Saas Shop Context Diagram](context-diagram/shop-website.png)

# Container Diagram

## Product Service

### Features
- Keyword search of products
- Filter products
- Paginate responses

![Product Service Container Diagram](container-diagram/container-diagram-product-service.png)

## Cart Service

### Features
- Add to cart
- Fetch cart
- Remove cart

![Cart Service Container Diagram](container-diagram/container-diagram-cart-service.png)

## Notification Service

### Features
- Deliver email notitifications
- Deliver in-app notifications

![Notification Service Container Diagram](container-diagram/container-diagram-notification-service.png)

## Payment Service

### Features
- Create payment intent
- Emit payment status event
- Store payment details

![Payment Service Container Diagram](container-diagram/container-diagram-payment-service.png)

# Sequence Diagram

## Search filter products

![Search products sequence diagram](sequence-diagram/search-filter-products.png)

## Add to cart

![Add to cart sequence diagram](sequence-diagram/add-to-cart.png)

## Checkout

![Checkout sequence diagram](sequence-diagram/checkout.png)

## Payment integration

![Payment sequence diagram](sequence-diagram/stripe-payment-integration.png)

## Order

![Order sequence diagram](sequence-diagram/order.png)
