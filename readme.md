# Run Jenkins Using Docker

## 1. Start Jenkins

Make sure Docker is running, then execute:

```bash
docker pull jenkins/jenkins:lts-jdk21

docker volume create jenkins_home

docker run -d \
  --name jenkins \
  -p 8089:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  --restart unless-stopped \
  jenkins/jenkins:lts-jdk21
```

## 2. Get the Initial Admin Password

Run:

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Copy the password displayed in the terminal.

## 3. Open Jenkins

1. Open **http://localhost:8089** in your browser.
2. Paste the initial admin password.
3. Select **Install suggested plugins**.
4. Create your Jenkins admin user and finish setup.

## 4. Create a Pipeline and Add This Script

In Jenkins, create a **New Item** → enter a name → select **Pipeline** → **OK**.

Under the Pipeline section, select **Pipeline script** and add:

```groovy
node {
    stage('Checkout') {
        checkout scm
    }

    stage('Clean') {
        sh 'mvn clean'
    }

    stage('Build and Test') {
        sh 'mvn test'
    }

    stage('Package') {
        sh 'mvn package'
    }

    stage('Archive') {
        archiveArtifacts artifacts: 'target/*.war,target/*.jar',
                         allowEmptyArchive: true
    }
}
```

**Note:** `checkout scm` is intended for a Pipeline configured to load its script from source control. If you paste this script directly into the Pipeline script box, configure the Git checkout for your repository separately. The Jenkins build agent must also have Maven installed and available as `mvn`.

Save the Pipeline and click **Build Now**.
