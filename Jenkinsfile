def isProdRelease = env.BRANCH_NAME ==~ /rel_[0-9]+/
def isMainUpdated = env.BRANCH_NAME == 'master'
def notifyBySlack = 'jenkins-demo'
def prodDeploymentLocation = "/tmp/dir2"
def releaseBranch = env.BRANCH_NAME
def deployAllModules = 'false'
def restartAllModules = 'false'
def modules = [
    'mod1': 'mod1location',
    'mod2': 'mod2location',
    'mod3': 'mod3location'
]

def services = [
    'service1': 'servicelocation',
    'service2': 'servicelocaion'
]

def moduleDeployPath = [
     'modd1': 'mod1location',
    'modd2': 'mod2location',
    'modd3': 'mod3location'
]

def buildMaven = { moduleLocation ->
        sh(script: """
            echo "building maven"
        """)
}

def buildGradle = { moduleLocation ->
    sh(script: """
            echo "building gradle"
        """)
}

def buildModule = { moduleName, moduleLocation ->
    stage("Build") {
        try {
            echo "Building ${moduleName}.."
        }
        catch (Exception e) {
            currentBuild.result = 'FAILURE'
                emailext(subject: "[CI Failure] [${repositoryName}] [${env.BRANCH_NAME}] [${moduleName}]",attachLog: true,body: """Below are the details of failure. Kindly check and fix it at the earliest.\nRepository: ${repositoryName}\nModule: ${moduleName}\nBranch: ${env.BRANCH_NAME}\nAuthor: ${authorName}\nCommit: ${gitURL}/commit/${env.GIT_COMMIT}\nStage: Build\nError Message: ${e.getMessage()}\nPipeline: ${RUN_DISPLAY_URL}""",from: 'madhumitamahapatra20@gmail.com',to:  'madhumitamahapatra20@gmail.com')
            throw e
        }
    }
}

def mvnTest = { moduleLocation ->
     sh(script: """
            echo "TEST maven"
        """)
}

def gradleTest = { moduleLocation ->
        sh(script: """
            echo "Test Gradle"
        """)
}

def testModule = { moduleName, moduleLocation ->
    stage("Test") {
         sh(script: """
            echo "testing maven"
        """)
    }
}

def deployModule = { moduleName, moduleLocation ->
    stage("Deploy") {
         sh(script: """
            //deploy logic
            echo "deploy"
        """)
}


def restartService = { moduleName ->
    stage("Restart Service") {
         sh(script: """
         //restart logic
            echo "restart"
        """)
    }
}

pipeline {
    agent any
    }
    stages {
        stage('Parallel Modules') {
            steps {
                script {
                    def parallelStages = [:]
                    modules.each { moduleName, moduleLocation ->
                        parallelStages[moduleName] = {
                            stage("${moduleName}") {
                                script {
                                    
                                        buildModule(moduleName, moduleLocation)
                                        testModule(moduleName, moduleLocation)
                                    
                                }
                            }
                        }
                    }
                    parallel parallelStages
                }
            }
        }
        stage('Parallel Modules for deploy and restart') {
            steps {
                script {
                    def parallelStages = [:]
                    modules.each { moduleName, moduleLocation ->
                        parallelStages[moduleName] = {
                            stage("${moduleName}") {
                                script {
                                            deployModule(moduleName, moduleLocation)
                                            restartService(moduleName)
                                }
                            }
                        }
                    }
                    parallel parallelStages
                }
            }
        }
    }
    post {
        always {
            script {
                deleteDir()
            }
        }
    }
}
