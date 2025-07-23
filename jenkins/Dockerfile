FROM jenkins/jenkins:lts

USER root

# Install Docker CLI
RUN apt-get update && \
    apt-get install -y git docker.io && \
    apt-get clean

# Optional: install any other tools here (e.g., kubectl, helm)

USER jenkins
