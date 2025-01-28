# Demo Project: Run Nexus on Droplet and Publish Artifacts

## Project Overview
This project demonstrates my ability to set up and configure **Nexus Repository Manager** on a **DigitalOcean** Droplet, manage users and permissions within Nexus, and publish Java artifacts using both **Gradle** and **Maven**. This showcases expertise in repository management and artifact lifecycle processes.

---

## Technologies Used
- **Nexus**: Repository manager for artifact storage and distribution.
- **DigitalOcean**: Cloud platform for deploying virtual servers (Droplets).
- **Linux**: Operating system for server configuration and hosting.
- **Java**: Programming language for applications.
- **Gradle**: Build automation tool for Java applications.
- **Maven**: Build tool and dependency manager for Java projects.

---

## Project Description

### 1. **Install and Configure Nexus**
- Provisioned a new Droplet on DigitalOcean running a Linux distribution.
- Installed Nexus Repository Manager from scratch:
  - Downloaded and installed the latest Nexus binaries.
  - Configured Nexus to run as a service for stability and ease of management.
- Set up Nexus URL and verified access via a web browser.

### 2. **User Management in Nexus**
- Created a new Nexus user with appropriate roles and permissions to manage artifacts securely.
- Assigned the user permissions for uploading, downloading, and managing repositories.

### 3. **Publishing Artifacts**
#### **Gradle Project**
- Built a Java project using Gradle.
- Configured `build.gradle` to upload the generated JAR file to the Nexus repository.
- Published the artifact to Nexus using Gradle's publishing plugin.

#### **Maven Project**
- Built a Java project using Maven.
- Configured the `pom.xml` file with Nexus credentials and repository URL.
- Uploaded the generated JAR file to Nexus using Maven's deploy lifecycle.

---

## Key Deliverables
1. **Nexus Repository Manager**:
   - Fully configured and accessible on DigitalOcean Droplet.
2. **User Management**:
   - Created a secure Nexus user with precise permissions.
3. **Artifact Publishing**:
   - Successfully built and uploaded Java artifacts (Gradle and Maven) to Nexus.

---

## How to Reproduce This Setup

### Prerequisites
- A DigitalOcean account.
- SSH key pair for secure server access.
- Nexus Repository Manager installation files.
- Java applications with build configurations for Gradle and Maven.

### Steps

#### **1. Set Up a DigitalOcean Droplet**
- Log in to your DigitalOcean account and create a new Droplet with a Linux distribution (e.g., Ubuntu 22.04).
- Configure SSH key-based access to the server.

#### **2. Install Nexus Repository Manager**
- SSH into the server.
- Download Nexus binaries:
  ```bash
  wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz
  ```
- Extract and move Nexus files:
  ```bash
  tar -xvf latest-unix.tar.gz
  sudo mv nexus-* /opt/nexus
  ```
- Create a dedicated Nexus user:
  ```bash
  sudo adduser nexus
  sudo chown -R nexus:nexus /opt/nexus
  ```
- Configure Nexus to run as a service and start it:
  ```bash
  sudo nano /etc/systemd/system/nexus.service
  ```
  (Include service configurations.)
  ```bash
  sudo systemctl start nexus
  sudo systemctl enable nexus
  ```

#### **3. Configure Nexus**
- Access Nexus via `http://<Droplet_IP>:8081`.
- Log in with the default credentials and create a new admin user.
- Set up repositories for Gradle and Maven projects.

#### **4. Publish Artifacts to Nexus**

**Gradle**:
- Configure `build.gradle`:
  ```groovy
  publishing {
      publications {
          mavenJava(MavenPublication) {
              from components.java
          }
      }
      repositories {
          maven {
              url "http://<Droplet_IP>:8081/repository/maven-releases/"
              credentials {
                  username = 'your-nexus-username'
                  password = 'your-nexus-password'
              }
          }
      }
  }
  ```
- Run:
  ```bash
  gradle publish
  ```

**Maven**:
- Configure `pom.xml`:
  ```xml
  <distributionManagement>
      <repository>
          <id>nexus</id>
          <url>http://<Droplet_IP>:8081/repository/maven-releases/</url>
      </repository>
  </distributionManagement>
  ```
- Run:
  ```bash
  mvn deploy
  ```
