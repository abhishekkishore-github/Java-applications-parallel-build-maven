# Deploy two Java spring-boot apps by building the applications in parallel and deploying them on a Kubernetes cluster via Jenkins for CICD using a custom docker agent.
![flow_diagram](https://github.com/yogendra-kokamkar/Java-applications-parallel-build-maven/assets/55878086/42036f8d-30db-4610-a053-2228ee035fdd)

Here are the step-by-step details to set up an end-to-end Jenkins pipeline for Java Spring-boot application using Docker, Maven, and Kubernetes.

Prerequisites:

   -  Code hosted on a Git repository
   -  Jenkins server
   -  DockerHub Account
   -  Kubernetes Cluster
   -  AWS Account
   -  Note: For this project, all the servers are set up on AWS EC2 instances.

![pipeline-flow](https://github.com/yogendra-kokamkar/Java-applications-parallel-build-maven/assets/55878086/10109e47-c4b9-4fc5-9376-3cccd00e37fd)

 Steps:

    1. Install the necessary Jenkins plugins:
       1.1 Git plugin
       1.2 Docker plugin
       1.3 AWS plugin
   
    2. Create a new Jenkins pipeline:
       2.1 In Jenkins, create a new pipeline job and configure it with the Git repository URL for the Java Application (Note - Both the application code has been stored in the same Repo).
       2.2 Add a Jenkinsfile to the Git repository to define the pipeline stages.
    
    3. Configure Kubernetes Agent:
       3.1 Connect the Kubernetes Master Node to Jenkins via SSH
       3.2 Will use this later in the Pipeline run for deploying the application on the Kubernetes cluster
    
    4. Configure Jenkins pipeline stages:
        Stage 1: Use the Git plugin to check out the source code from the Git repository.
        Stage 2 (Parallel Stage): Use a custom docker maven container as the Jenkins agent to build both applications in parallel.
        Stage 3: Use Docker to Containerize the build application 1 and push the image to DockerHub.
        Stage 4: Use Docker to Containerize the build application 2 and push the image to DockerHub.
        Stage 5: Use the Shell Script to push the application image on AWS ECR.
        Stage 6: Update the YAML manifests files to include the latest build tag for application 1 and deploy the latest application version on the Kubernetes cluster
        Stage 7: Update the YAML manifests files to include the latest build tag for application 2 and deploy the latest application version on the Kubernetes cluster
        Note : Edit the pipeline stages codes and Kubernetes manifest files with your DockerHub username, Github email and, GitHub Username as required.

    5. Run the Jenkins pipeline:
       5.1 Trigger the Jenkins pipeline to start the CI/CD process for both Java applications.
       5.2 Monitor the pipeline stages and fix any issues that arise.

This end-to-end Jenkins pipeline will automate the entire CI/CD process for both Java applications, from code checkout to production deployment, using popular tools like Docker, Maven, and Kubernetes.
