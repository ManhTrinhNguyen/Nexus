- [Artifact Repository Manager](#Artifact-Repository-Manager)

  - [What is an Artifact Repository](#What-is-an-Artifact-Repository)
 
  - [What is an Artifact Repository Manager](#What-is-an-Artifact-Repository-Manager)
 
  - [Public vs Private Artifact Repository Managers](#Public-vs-Private-Artifact-Repository-Managers)
 
  - [Features of Repository Manager](#Features-of-Repository-Manager)
 
- [Nexus Demo Project](#Nexus-Demo-Project)

  - [Install and run Nexus on a Cloud Server](#Install-and-run-Nexus-on-a-Cloud-Server)

## Artifact Repository Manager

#### What is an Artifact Repository 

Storage of build artifacts produced by continous intergration which makes them available for automated deployment to different deployment environment 

Provides a central location

Artifacts are applications built into a single file

There are different artifact formats: (JAR, WAR, ZIP, TAR, etc.)

Artifact repository needs to support this specific format

Repository for each file/artifact type

#### What is an Artifact Repository Manager

Can store many different artifact types

Which is great, because as a company you often produce different types of artifacts

#### Public vs Private Artifact Repository Managers

**Public Repository Managers**

There are public repository managers

For example libraries/frameworks you use as a dependency (Maven Repository, NPM etc...) 

You can make own project publically available there

**Nexus Private Repository Manager**

Allow to store and upload different build Artifact . 

Also can retrieve (download) artifact later. 

Central Storage

It makes sense if I have multiple projects in a company to have Nexus where you can centrally sotre all those artifacts in case the teams and projects want to share them among each other 

I can also set up a Proxy repository for public Repository 

- For example for Maven Central Repository, you can acutally create a proxy repository in Nexus and the main reason would be to kind of consolidate all of my artifact management in 1 spot.

- So whether you are using Company internal artifacts or you are using public artifact you can fetch everything from Nexus

#### Features of Repository Manager

Integrate with LDAP . Means that in big company where is 100 of developers, you can actually pretty easily configure the access management in Nexus just by integrating LDAP and then giving some sets of permissions

REST API endpoint . Nexus is not something we manually manage you wouldn't be building the artifacts locally on my laptop and then uploading them to Nexus manually and you will also not be downloading the artifacts manually to my deployment server . This will be part of CI/CD

Backup and Restore. When I have multiple projects and everybody's building artifacts and they are pushing that to Nexus Repository, we have some storage demand . Nexus has its own storage mechanism configured by default and it stores those artifacts on the Server, but it's also important to be able to configure easy backup and restore, or just generally managing the storage of Nexus, especially if you end up having loads of artifacts on Nexus server, then you need to increase the storage size 

Multi-format support 

Metada tagging (labeling and tagging artifact). Managing and helping to manage the tags and version and so on. Bcs we may have release versions and we may have development verison and so on, and Nexus Actually denpending on the repository typ may acutally help you manage those version and texts

Cleanup Policies: Image we have CI for my application, then every commit to the master branch bascially creates a new artifact of the application. And if that happens on daily bases or multiple times a day, you can just imagine how many artifacts you would generate and this will all end up on Nexus and if other teams are doing the same, then it could be that Nexus run out of storage space bcs of that Cleanup policy are important 

Search functionality : If we end up having tons of different artifacts, especially of different types and different formats then you need to be able to search across repositories to find exactly the version that artifact that you are looking for 

User Token Support for system user


## Nexus Demo Project:

Run Nexus on Droplet and Publish Artifact to Nexus

**Technologies used:**

- Nexus, DigitalOcean, Linux, Java, Gradle, Maven
  
**Project Description:**
  
- Install and configure Nexus from scratch on a cloud server Create new User on Nexus with relevant permissions Java Gradle Project: Build Jar & Upload to Nexus
Java Maven Project: Build Jar & Upload to Nexus


#### Install and run Nexus on a Cloud Server

I will create a Server on Digital Ocean 

I will configure Firewall for this Droplet 

- Accessible at Port 22

Now I can ssh to the server using : `ssh root@<Public-ip>`

I need to install Java `openjdk-17` bcs Nexus need Java to run : `apt install openjdk-17-jre-headless`

I will move in to `cd /opt` folder and Download Nexus here . 

Link to download Nexus (https://help.sonatype.com/en/download.html)

I will copy `link address` of the Unix Archive 

Head back to the terminal and do : `wget https://download.sonatype.com/nexus/3/nexus-3.81.1-01-linux-x86_64.tar.gz`. With this command I just download a tar file Nexus 

![Screenshot 2025-06-14 at 11 39 30](https://github.com/user-attachments/assets/1642f161-2eee-41c7-98d3-5df469918ee0)

Now I have tar file locally `nexus-3.81.1-01-linux-x86_64.tar.gz`

To untar that file : `tar -zxvf nexus-3.81.1-01-linux-x86_64.tar.gz`

Now I should see 2 Folder `nexus-3.81.1-01` and `sonatype-work`
 
![Screenshot 2025-06-14 at 11 42 12](https://github.com/user-attachments/assets/36f7f40c-e0b9-453b-b3fe-c79c75ea397e)

`nexus-3.81.1-01` : Contain a runtime and the application of Nexus itself . 

- `nexus-3.81.1-01/bin` the binary to start Nexus Application

`sonatype-work/nexus3` contain my own configuration for Nexus and the data 

- This mean when you upgrade to a new version of nexus, you replace the Nexus directory, the application directory with the binaries, but you still keep the custom configuration and repository data in `sonatype-work`

- Sonotype work directory is created as a sibling, to the `nexus-3.81.1-01` directory and nexus knows the location of `sonatype-work` directory through its properties file

- `Sonatype-work/nexus3` container number of subdirectories, depending on what you configure in Nexus itself . For examole i can install plugin in Nexus If I do different subdirectories will appear in this folder .

- This directory also contain a file has a log of all IP address that accessed or is accessing the NExus application

- It also contains logs of Nexus application itslef .

- It also store actual component. So the archives, the files that you are uploading to Nexus and the metadata to them many other files and subdirectories that contain different sets of information

I can use `sonatype-work` folder to backup all the Nexus data since this folder contain all the data all the storage, all the configuration, etc ... 

NOTE: Never start services generally with `root` user bcs of security reason . I will create own Linux user for its own services and then start that service with that specific user that has only permission it needs to run the application and to interact with the application 

To create nexus user : `adduser nexus` . Set password 

Now I check the permissions of `nexus-3.81.1-01` and `sonatype-word` directory `ls -l`, both of them own by Root user and Root Group

If I want to run Nexus application with Nexus user, I need to change both of these directories permissions or ownership to `nexus user` : `chown -R nexus:nexus nexus-3.81.1-01` and `chown -R nexus:nexus sonatype-work`

I will set Nexus configuration so that it will run as a Nexus user `vim nexus-3.81.1-01/bin/nexus.rc` I will set `run_as_user="nexus"` 

Now I will switch to `nexus user` `su - nexus`

Then I will start Nexus `/opt/nexus-3.81.1-01/bin/nexus start`

Now I can check If nexus running  `ps aux` 

![Screenshot 2025-06-14 at 12 09 09](https://github.com/user-attachments/assets/30d1fc22-7e8d-4954-8f87-4a4d1483ad57)

Now I can see nexus is running 













