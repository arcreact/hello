
#!/usr/bin/env groovy
import groovy.json.JsonSlurper
def Environments
def versions
def recurse 

node {
    dir('/home/workspace') {
        nodes = sh (script: 'sh list_nodes.sh', returnStdout: true).trim()
    }
}

pipeline {
agent any
    parameters {
            choice(name: 'Invoke_Parameters', choices:"Yes\nNo", description: "Do you whish to do a dry run to grab parameters?" )
            choice(name: 'Environments', choices:"${Environments}", description: "")
    }
    stages {
        stage("parameterizing") {
            steps {
                script {
                    if ("${params.Invoke_Parameters}" == "Yes") {
                        currentBuild.result = 'ABORTED'
                        error('DRY RUN COMPLETED. JOB PARAMETERIZED.')
                    }
                }
            }
        }
        stage("choose version") {
            steps {
                script  
                 {
                        
                        def versionArraySort = { a1, a2 -> 
                            def headCompare = a1[0] <=> a2[0] 
                            if (a1.size() == 1 || a2.size() == 1 || headCompare != 0) { 
                                return headCompare 
                            } else { 
                                return recurse(a1[1..-1], a2[1..-1]) 
                            } 
                        } 
                        // Making Groovy to understand recursive closure 
                        recurse = versionArraySort
                        def versionStringSort = { s1, s2 -> 
                            def nums = { it.tokenize('.').collect{ it.toInteger() } } 
                            versionArraySort(nums(s1), nums(s2)) 
                        }
                        try {
                            List<String> artifacts = new ArrayList<String>()
                            def artifactsUrl = "nexus-url"
                            def artifactsObjectRaw = ["curl", "-s", "-H", "accept: application/json", "-k", "--url", "${artifactsUrl}"].execute().text
                            def jsonSlurper = new JsonSlurper()
                            def artifactsJsonObject = jsonSlurper.parseText(artifactsObjectRaw)
                            def dataArray = artifactsJsonObject.data
                            for(item in dataArray){
                                if (item.isMetadata == false)
                                artifacts.add(item.text)
                            } 
                            return artifacts.sort(versionStringSort).reverse()
                        } catch (Exception e) {
                            print "There was a problem fetching the artifacts" + e
                        }

        stage("build") {
            steps {
                script {
                    build(job: "builder-job",
                        parameters:
                        [string(name: 'Environments', value: "${params.Environments}"),
                        string(name: 'Versions', value: "${versions}"),
                        ])
                }
            }
        }
    }
}
        
