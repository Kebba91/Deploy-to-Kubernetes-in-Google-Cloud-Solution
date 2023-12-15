# Deploy-to-Kubernetes-in-Google-Cloud-Solution

export Email= PROJECT ID
export REGION= REGION
export ZONE = zone
export PROJECT_ID=$(gcloud config get-value project)

# TASK1 Create a Docker image and store the Dockerfile
Install marking script to help you check your progress
source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking_v2.sh)

Clone the source provided
gcloud source repos clone valkyrie-app

Create DockerFile to build image/ Configuration provided by Google
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]

docker build -t valkyrie-dev:v0.0.3 .

# Task 2. Test the created Docker image
docker run -p 8080:8080 --name my-app valkyrie-dev:v0.0.3 &

# Task 3. Push the Docker image to the Artifact Registry

# Create Repository
gcloud artifacts repositories create valkyrie-repo \
    --repository-format=docker \
    --location=$REGION \
    --description="this is my app"
# Authenticate docker registry
gcloud auth configure-docker valkyrie-repo
docker tag my-app us-central1-docker.pkg.dev/$PROJECT/valkyrie-repo/valkyrie-dev:v0.0.3
docker push $REGION-docker.pkg.dev/$PROJECT/valkyrie-repo/valkyrie-dev:v0.0.3

# Task 4. Create and expose a deployment in Kubernetes
Get the K8s credentails before deploying
gcloud container clusters get-credentials cluster-name --zone=ZONE
# Before creating deployment, ensure that the image name in the deployment.yaml file is modified
kubectl create deployment valkyrie-app --image=valkyrie:v0.0.3
kubectl expose pod valkyrie-app --port 8080 --type LoadBalancer


# CONGRATULATION, YOU COMPLETED THIS LAB












