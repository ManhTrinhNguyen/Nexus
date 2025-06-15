- [Artifact Repository Manager](#Artifact-Repository-Manager)

  - [What is an Artifact Repository](#What-is-an-Artifact-Repository)
 
  - [What is an Artifact Repository Manager](#What-is-an-Artifact-Repository-Manager)
 
  - [Public vs Private Artifact Repository Managers](#Public-vs-Private-Artifact-Repository-Managers)
 
  - [Features of Repository Manager](#Features-of-Repository-Manager)
 
- [Nexus Demo Project](#Nexus-Demo-Project)

  - [Install and run Nexus on a Cloud Server](#Install-and-run-Nexus-on-a-Cloud-Server)
 
  - [Sign in to Nexus](#Sign-in-to-Nexus)
 
  - [Repository Types](#Repository-Types)
 
  - [Create new Repository](#Create-new-Repository)
 
  - [Publish Artifact to Repository](#Publish-Artifact-to-Repository)
 
    - [Create Nexus User](#Create-Nexus-User)
   
    - [Gradle Project](#Gradle-Project)
   
      - [Jar Upload](#Jar-Upload)
     
    - [Maven Project](#Maven-Project)
   
- [Nexus API](#Nexus-API)

- [Blob Store](#Blob-Store)

  - [Blob Store Type](#Blob-Store-Type)
 
  - [Blob Store State](#Blob-Store-State)
 
  - [Blob Count](#Blob-Count)
 
  - [Create a Blob store](#Create-a-Blob-store)

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

Now I can check If nexus running  `ps aux | grep nexus` 

![Screenshot 2025-06-14 at 12 09 09](https://github.com/user-attachments/assets/30d1fc22-7e8d-4954-8f87-4a4d1483ad57)

Now I can see nexus is running . And I can see the `process id is 3478`

Now I do `netstat -ltpn` I can see that `process id 3478` is running on port 8081 

![Screenshot 2025-06-14 at 12 13 58](https://github.com/user-attachments/assets/8519ecf1-a635-48b6-b061-24bac09bff44)

That mean If I want to access Nexus from browser I have to open port 8081 in my Firewall 

![Screenshot 2025-06-14 at 12 18 20](https://github.com/user-attachments/assets/b58a4a18-bd40-4599-9a78-d3bdcf51a251)

Once it configured I can access it from the browser `<droplet-public-ip:8081>`

<img width="600" alt="Screenshot 2025-06-14 at 12 19 04" src="https://github.com/user-attachments/assets/a8644c87-0478-432c-8a13-9f7e75f5f808" />

#### Sign in to Nexus

After deployed Nexus we have auto generate admin password in `/opt/sonatype-work/nexus3/admin.password` with username is `admin`

Once Login Nexus will ask me to create my new password  

#### Repository Types

I can have multiple Repositories of different formats like Helm Charts, Docker Images, Java Archive file, Javascript artifact, etc 

![Screenshot 2025-06-14 at 12 18 20](https://github.com/user-attachments/assets/46b0bc94-6010-4302-89a8-5a96c8fa5495)

By default I can see some Repostories automatically get created bcs properly its the most used one 

Each Repository has a type (hosted, proxy, group):

**Proxy Repository** : This is a repository that is linked to a remote Reposiory . For example Maven Central Repositories is a remote public repository for Maven artifacts . Developers can download jar files, other artifacts and dependencies for application

  - If a component is requested from the Remote Repository by my application . For example : I am trying to download a library with a Specific Version from Maven Central, it will go through the Proxy instead of directly to remote and first check whether the component is available locally on Nexus, if yes my application that I am developing within the company will take it from Nexus . If not there yet the request will be forwarded to remote Repository . The component  will then be retrived from remote and stored locally in Nexus . So Nexus will act as a cache .

  - Advantages : First of all it saves network bandwidth and time for retrieving the component from remote on every request . I just need to do it once  

![Screenshot 2025-06-14 at 12 52 34](https://github.com/user-attachments/assets/7edb7edd-12af-448f-8817-e69165ff9677)

- `URL: http://143.198.134.109:8081/repository/maven-central/` Which will use to access the repository and get some information of it 

- `Remote Storage : https://repo1.maven.org/maven2/` This is a location of Maven Repository

- Another Advantage of using a proxy repositroy instead of fetching from the remote directly is that it is giving developer single endpoint instead of each developer team managing their own logic

**Hotsed Repository**: This is a Repository Primary Storage for artifacts and components

- For example for a Company own components this is a typical use case where everything that is developed within the company, all the applications, the artifacts are being storted in Nexus hosted repository, so this is the primary location for these artifacts.

- By default I have `maven-realeases` and `maven-snapshots` are Maven two repository formats and each one has the version policies integrated for releases and snapshot respectively and these repositories will be used by Maven or Gradle projects to store Java artifact

- The best practice is that when I am developing an Application in Java, I have this development version of my artifact, so I release a development version to be tested and that will be a Snapshot version and when I actually yest and it's ready to be released to Production, then I have actual releases and this will end in `maven-releases` repository. So this way these 2 are separated, the name of version, the naming can also be different

- These repository types have integrated version policies for managing these artifact

- `Maven-releasees` is intended to be the repository, where you organization publishes internal releases, you can also use this repository for thrid-party component that are not available in puclic or external repositories and can therefore not be retrived via proxy repository . For examle it could be commercial library, some propriatary libraries such as Oracle, JDBC driver that maybe reference by my organization. So they are not externally available so I can create a proxy to fetch directly from them, but you can host it on this `hosted repository`  as company internal component even though it not made by you company

- `Maven-Snapshot` is intended to be the repository, where your organization publishes internal development versions and these development version also called Snapshot

**Group Repository**

I may have multiple individualy repositories in NExus and each one has its own purpose however if you need to use multiple of these repositories in my application, I don't want to configure endpoints of all of these different repositories, I want to address them using just 1 endpoint. And then have all these different repositories 

This type allow me to combine multiple repositories and even other repository groups in a single repository 


#### Create new Repository 

I click in the `Create Repository` I have ther list of different Repostory format and types I can choose from 

![Screenshot 2025-06-14 at 12 59 25](https://github.com/user-attachments/assets/87120028-8ce6-4210-a3b0-f89bd8ddd593)

#### Publish Artifact to Repository 

Java Gradle App: (https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-app)

Java Maven App: (https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-maven-app)

I will upload a Jar file from Maven and Gradle Projects .

I will use a `Maven` hosted repository 

Maven and Gradle have specific command to push to Repository 

Before I can push to Repository I have to configure both tools to connect to Nexus (Nexus Repo URL + Credentials)

- Relistic use cases is we won't give developers the admin username and password for our Nexus Repo Manager .

- We will create Nexus User with permission to upload artifact file in certain repositories and then we will use that Nexus user to upload the jar

#### Create Nexus User 

Go to Security -> Users -> Create Local User  

Usally I would integrate User System using LDAP integration and then give it Nexus Permission 

Create Roles for Nexus User : Go to Roles -> Create Roles -> Choose Nexus Role Type -> Perission is `nx-repository-view-maven2-maven-snapshots-*`

!!! Best Pratice when generally creating permission for users is to give the least necessary permission to a user to execute a task that is really needs to execute, bcs obivously we don't want user to have a lot of permissions 

Now I can assign the Role to my User 

![Screenshot 2025-06-14 at 13 37 16](https://github.com/user-attachments/assets/2f5ee7f6-d536-44aa-99a6-c5f0de740557)

Now I can use this user in my project to give Gradle and Maven credentials of this user to connect  to our nexus

#### Gradle Project 

First thing I will add a plugin for publishing a Jar to Maven formatted repository : `apply plugin: 'maven-publish'`

!!! Note: Both Gradle and Maven Project use the same maven to format to upload the Java artifacts 

This will enrich Gradle with ability to connect to Nexus and push to its Maven Repository 

Now I will configure : 

```
publishing {
    publications {
        maven(MavenPublication) {
            artifact("build/libs/my-app-$version" + ".jar"){
                extension 'jar'
            }
        }
    }

    repositories {
        maven {
            name 'nexus'
            url "http://[nexus-ip]:[nexus port]/repository/maven-snapshots/" 
            allowInsecureProtocol = true
            credentials {
                username project.repoUser
                password project.repoPassword
            }
        }
    }
}
```

- `publications` is a Jar file configuration that we will upload

  - `build/libs/my-app` : This is a location of Artifact when we build the project, Gradle will actually create a `build/libs` folder and it will generate and put the JAR in there
 
  - `$version` Version of the App 

- `repositories` is the Nexus Repository that we will upload that JAR file

  - `name: nexus` : This is a name of the Repository Manager
 
  - `url: http://143.198.134.109:8081/repository/maven-snapshots/` this is a URL of the Repository in my Repository Manager
 
  - I will create `gradle.properties` file to store `username` and `password`
 
```
gradle.properties

repoUser = tim
repoPassword = mypassword
```

In `setting.gradles` I have `rootProject.name = my-app` This will be used to generate the JAR file

- allowInsecureProtocol = true : Bcs we not access Nexus in `HTTPS` this will allow me to access the repositories using `HTTP`

#### Jar Upload 

To build Gradle Application : `gradle build`

![Screenshot 2025-06-14 at 13 59 53](https://github.com/user-attachments/assets/b516f404-1f6a-4329-8204-8fe9acffe1ab)

Once successed I should have a JAr file in `build/lib`

![Screenshot 2025-06-14 at 14 00 54](https://github.com/user-attachments/assets/b3d3cda9-8660-44e0-9074-c6606c0a3a64)

To upload JAR file I will execute `gradle publish`

- This command is not available by default . It from a `apply plugin: 'maven-publish'` that we installed. And we have configured Gradle wirh all the information necessary so that publish command knows what to publish and where to publish that artifact to

![Screenshot 2025-06-14 at 14 03 39](https://github.com/user-attachments/assets/d20c8c1f-da4e-4a66-b724-4935e596c629)

Now I should see my Java application in Maven-Snapshot Repository Manager 

![Screenshot 2025-06-14 at 14 04 57](https://github.com/user-attachments/assets/94079b1b-9ef9-4db5-87c6-d210ca26b34b)

In addition to a `Jar` file in there and I see a bunch of other files and metadata files and so on, which is the way NExus actually store components. So I uploaded 1 specific artifact which is JAR, bu some metadata and additional data and files get generated as information or addtional information for the jar file 

## Maven Project

With Maven we are also need to configure artifact and the address of the repository 

Second part is configure the credentials so that Maven can actually upload the jar to Nexus using `nexus user` that we created 

Configure Plugin that enable Maven to upload Jar file :

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-deploy-plugin</artifactId>
    <version>3.1.1</version>
</plugin>

<plugin> 
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-deploy-plugin</artifactId>
</plugin>
```

This part will basically let me configure repository for my Snapshot.

- `<id>nexus-snapshots</id>`: This id to identify the Repository if I have multiple of them configure. So we can reference which Repository from the List

```
<distributionManagement>
  <snapshotRepository>
      <id>nexus-snapshots</id> 
      <url>http://143.198.134.109:8081/repository/maven-snapshots/</url>
  </snapshotRepository>
</distributionManagement>
```

Configure Credentials so Maven can also authenticate with it using `nexus user`

- The way we configure user credentiasl for Repository in Maven is in `.m2` folder in my user directory . This is a hidden folder in my home directory

- Inside I have `repository` folder which hold all the locally downloaded dependencies of my Maven projects

- And in `.m2` folder I will create a file `settings.xml`. This is a file where Maven Global credentials can be defined . This will be accessible for all my Maven projects

```
<settings>
 <servers>
   <server>
    <id>nexus-snapshots</id>
    <username>tim</username>
    <password>My-password</password>
   </server>
 </servers>
</settings>
```

- `<settings>` list of settings here and servers is bascially credentials to any server that Maven has to authenticate to 

- `<id>nexus-snapshots</id>` the same id that I configure above

- Now I have everything ready to upload to Nexus Repository

Move to Java-Maven folder : `cd Java-Maven`

Build the Jar: `mvn package`

![Screenshot 2025-06-15 at 11 37 02](https://github.com/user-attachments/assets/b9137847-1c99-4f93-ba7d-9c08d0a23dac)

Once it success I can see a `target` folder with a Jar Artifact 

![Screenshot 2025-06-15 at 11 37 48](https://github.com/user-attachments/assets/6732ae0c-679d-4ead-bef5-89af2387f4b2)

To upload the Jar `mvn deploy` .

![Screenshot 2025-06-15 at 11 39 55](https://github.com/user-attachments/assets/6a49e3ed-bbc3-4d2f-8608-aeb3dc68d21d)

Once it  success I can see my Maven Artifact in Nexus Repository 

<img width="600" alt="Screenshot 2025-06-15 at 11 40 05" src="https://github.com/user-attachments/assets/b01918e3-bf68-427b-9e50-cc797fb0814e" />

## Nexus API

We need Nexus rest API endpoint to query Nexus repositories for different information like : 

- Which component are avaialbe

- What are the version

- Which repositoies are avaialble and so on

- Query for different information:

- Download an artifact

- Upload an artifact

- List of repositories list of component

I would need those information in CI/CD pipelines where I need to fetch the information about the available versions that maybe user can select to deploy on staging environment or production environment and so on 

To access REST Endpoint :

- Use tools like `curl` or `wget` to execute http request

- I also need to provide Credentiasl of Nexus User

- Use the Nexus user with the required permissions 

For example : `curl -u user:pwd -X GET 'http://143.198.134.109:8081/service/rest/v1/repositories'`

`/service/rest/v1/repositories`: This is an endpoint 

Once it executed I can see a list of Repository that user have permission to fetch 

![Screenshot 2025-06-15 at 11 55 20](https://github.com/user-attachments/assets/c984be94-78a5-493f-972a-82ee6618f2f1)

If I execute with admin user I can see a whole list of repositories

![Screenshot 2025-06-15 at 11 57 11](https://github.com/user-attachments/assets/0729ff47-bf51-430a-96a3-23df521df6fa)

Another query is listing all the components in a certain repository : `curl -u user:pwd -X GET 'http://143.198.134.109:8081/service/rest/v1/components?repository=maven-snapshots'`

- From this endpoint I get all a items that are in snapshots repository 

![Screenshot 2025-06-15 at 12 01 44](https://github.com/user-attachments/assets/cef3d98a-b631-42c1-82e1-db788f78ca7f)

To use Nexus API or any API: I should always reference the documentation. Not learn by heart, because 

1: API's change 

2: there are a lot of endpoints

## Blob Store

 Nexus must have some kind of storage configured to store all these `assets` and `components`

 Nexus is using Blob stores as a storage for its components

 Blob Store is what Nexus is using to manage the storage per repository for all of its components 

 **How does it work?**

 Blob Store is internal storage mechanism for binary parts of artifacts 
 
 And blob store can be on a local file on the Server where Nexus is deployed

 Or it could be a Cloud based storage like Amazon S3 . 
 
 Each Blob Store Can be use by one or multiple repositories and repository group 


In my Server where I deploy Nexus `ls /opt/sonatype-work/nexus3/blobs` . This is a file storage that keep alot of different information about Nexus like configuration and plugins etc ... And this is where blob store storage is configured so all the components assets and so on will be store here 

In `/opt/sonatype-work/nexus3/blobs` I have a default folder that automatic configured 

In that `default` folder I have all the data in the content folder 

#### Blob Store Type 

Type field = Storage Backend 

File represent `File system-based` storage 

`S3` allow Blobs to be stored in AWS S3 Cloud Storage 

#### Blob Store State 

State filed = State of the Blob store 

`Started` : Indicate it is running as expected 

`failed`: Indicate configuration issue 


#### Blob Count

If I look in the `content` folder the Data is actually split up in different volumes or blobs and this is number of blobs that is currently stored 

So the components and the artifacts files that we upload are split into this way to be store in the Blob store 

![Screenshot 2025-06-15 at 12 34 35](https://github.com/user-attachments/assets/65b5b90f-a8a6-4b2d-b029-a7ef4f734ec1)


#### Create a Blob store

The `Type` can be `File` or `S3`

Path parameter should be an absolute path  `/opt/sonatype-work/nexus3/blobs/`

**Some things to consider**

Blob store can't be modified 

Blob store used by Repository can't be deleted 

I need to decide carefully how many blob stores I create with which sizes and which one will use for Repository 

The reasone for that is Once a repository is allocated to a blob store it is there permanently . Blob store can be moved from one storage device to another for example to a larger storage device If I need more space I can do it manually but blob stores can not be split and also one repository cannot use multiple blob stores 

















