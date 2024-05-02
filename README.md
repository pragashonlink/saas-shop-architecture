# Summary

Saas has an admin and a public website. The public website is mobile friendly. The admin website is intended to be used by internal employees. The application will use Microservice Architecture.

# Non Functional Requirements
Site should be accessible via desktop and mobile view - This will address by making the website responsive hence, it will give the mobile users a seamless experience. Also, it saves us time and cost.

Product search should be able to handle 5000 TPS 
    Set the correct resources for the container as shown below,
    `
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
    `
    Setup liveness and readiness probe so that the k8s cluster can hit the actuator health end point and automatically manage in case of unresponsiveness.
    `
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
    `
    Set up Horizontal Pod Autoscaler in the cluster to automatically up/down pods based on CPU or memory utilization
    `
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
    `   
