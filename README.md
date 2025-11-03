# 🔍SonarQube

## ☁️EC2 Instance Setup

1. Go to **AWS Console → EC2 → Launch Instance**  
2. Configure:
   - **Name:** `sonarqube-server`
   - **AMI:** Ubuntu 22.04 LTS
   - **Instance type:** `t3.medium` (2 vCPU, 4GB RAM minimum recommended)
   - **Key pair:** Select or create new
   - **Network Settings:**  
     - Allow **SSH (22)** and **Custom TCP (9000)**
3. Click **Launch Instance**
## ☕Install Java (Required for SonarQube)
```bash
sudo apt install openjdk-17-jdk -y
java -version
```

## 📩Download sonarqube
- [Download](https://www.sonarsource.com/products/sonarqube/downloads/success-download-community-edition/)
- with wget command
  ```sh
    wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.3.0.82913.zip
    unzip sonarqube-10.3.0.82913.zip
    sudo mv sonarqube-10.3.0.82913 /opt/sonarqube```
- version may change . 
## 👤 8. Create a Dedicated SonarQube User
```
sudo useradd -r -s /bin/false sonar
sudo chown -R sonar:sonar /opt/sonarqube
```
## 🔑Start SonarQube

```
sudo su - sonar
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
```
- Access the UI at ```http://<EC2-IP>:9000```
- Default credentials: ```admin / admin```

# ⚙️ Steps In Jenkins
##  Step 1: Install SonarQube Scanner Plugin
1. Go to Jenkins Dashboard → ```Manage Jenkins → Plugins → Available```
2. Search for SonarQube Scanner.
3. Install it and restart Jenkins.

## 🔌Connect sonarQube to Jenkins(Server authentication token)
- by secret text
- In Sonarqube Go-to : ``` Account →My account → Security → Generate Token ```
- copy the generated token
- In jenkins : ``` `Manage Jenkins → credentials → Globel -> Add credentials```
- select ``` secret text``` and paste it .
## 📡Access Sonarqube 
1. Go to Jenkins Dashboard → ```Manage Jenkins → System → SonarQube servers```
2. add Environment variables - ```Name``` ``` server URL``` ```Server authentication token```
3. Save changes✅

# Perform scan from jenkins
- by pipelinescript 
  
  ```css
       stage('SonarQube Analysis'){
            steps {
                withSonarQubeEnv(installationName: 'sonar', credentialsId: 'sonarqube-token') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

  ```
