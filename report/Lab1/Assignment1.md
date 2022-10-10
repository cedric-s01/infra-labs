# Lab Report: CI/CD

## 1.1: Lab Setup
Er waren wat problemen met het hele maken van een Git directory, omdat het niet duidelijk was waar we deze moesten maken. Ik heb het op de vagrant machine gedaan, en dit leek te werken, na wat moeilijkheden met de ssh key etc, waarmee ik wat problemen had omdat mijn Linux-vaardigheden even afgestoft moesten worden. 

## 1.2: Building Sample Application
Deze stap ging zeer vlot, hier heb ik geen problemen mee gehad. De applicatie heeft er snel voor gezorgd dat ik <http://192.168.56.20:5050/> feilloos kon bereiken. 
Enkel voor het verwijderen van de docker container was enig opzoekwerk vereist, omdat het enige tijd geleden was dat ik docker gebruikt heb.


![SamplerunningSite](infra-2223-cedric-s01\report\Lab1\Images\SamplerunningSite.png)


## 1.3: Jenkins Docker Image
Ook deze stap verliep vlot, doordat het commando al gegeven was. Ik was niet zeker of ik het proces moest afbreken toen het opstarten van de container geen voortgang toonde, aangezien het leek dat deze gewoon vastliep. 

## 1.4: Jenkins Configuration
Stap 4 was ook vrij simpel en straightforward, ik heb er geen enkel probleem mee gehad om dit uit te voeren.


## 1.5: Using Jenkins to build Application
Deze stap was lastiger, vooral om te vinden hoe de tempdir folder terug verwijderd moest worden, maar ik heb dit vrij snel kunnen oplossen. Ook de samplerunning-container heb ik kunnen verwijderen door een extra aantal lijnen in de `Execute shell`-categorie van Jenkins te zetten. 



## 1.6: Add a job to test the application
Dit was zeer vanzelfsprekend, en werkte zonder enig probleem.
Jenkins console output:
```
Started by user admin
Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/TestSampleApp
[TestSampleApp] $ /bin/sh -xe /tmp/jenkins3293325348983291281.sh
+ curl http://172.17.0.3:5050/
+ grep You are calling me from 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed

  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100   177  100   177    0     0  35400      0 --:--:-- --:--:-- --:--:-- 44250
    <h1>You are calling me from 172.17.0.2</h1>
Finished: SUCCESS
```


## 1.7 Create a build pipeline
De pipeline werkte relatief goed, behalve dan de lines `sh 'docker stop samplerunning'` en `sh 'docker rm samplerunning'`, omdat ik deze al in de BuildSampleApp had gestoken zodat ik deze meerdere keren kon uitvoeren. 

Jenkins Error:
```Started by user admin
[Pipeline] Start of Pipeline
[Pipeline] node (hide)
Running on Jenkins in /var/jenkins_home/workspace/SampleAppPipeline
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Preparation)
[Pipeline] catchError
[Pipeline] {
[Pipeline] sh
+ docker stop samplerunning
samplerunning
[Pipeline] sh
+ docker rm samplerunning
samplerunning
[Pipeline] }
[Pipeline] // catchError
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build)
[Pipeline] build (Building BuildSampleApp)
Scheduling project: BuildSampleApp
Starting building: BuildSampleApp #17
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
BuildSampleApp #17 completed with status FAILURE (propagate: false to ignore)
Finished: FAILURE
```

## 1.8 Make a change in the application



## Reflection



## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.

Jenkins password: 8d6a710aff404ca1b568fd69b5f4119f