pipeline {
    agent any
    environment {
        ACR_Registry_Name = 'poc251122'
        ACR_Registry_Url = 'poc251122.azurecr.io'
        App_Env = 'Dev'
        Sonar_Project_Key='poc_java-maven-app'
        stage_name_temp=''
    }

    stages {
        stage('code_checkout') {
            steps {
                script{
                //echo "${env.STAGE_NAME}"
                //stage_name_temp='code_checkout'
                stage_name_temp="${env.STAGE_NAME}"
                StartdateTimee = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                //git 'https://github.com/BalajiMokara/java-maven.git'
                git 'https://github.com/BalajiMokara/bank-portal.git'
                echo "code cloned on jenkins server !"
                sleep 3
                EnddateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                }
                FuncLogFile("${StartdateTimee}","${EnddateTime}","${env.STAGE_NAME}")
            }
        }
        stage('sonar_scan') {
            steps {
                script{
                //stage_name_temp='sonar_scan'
                stage_name_temp="${env.STAGE_NAME}"
                StartdateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                def SONAR_PATH = tool (name: 'sonar470', type: 'hudson.plugins.sonar.SonarRunnerInstallation')
                    withSonarQubeEnv('SonarServer') {
                    sh """
                    $SONAR_PATH/bin/sonar-scanner -Dsonar.projectKey=poc_java-maven-app -Dsonar.java.binaries=src
                    """
                    /*
                     timeout(time: 1, unit: 'MINUTES') {
                     def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                         error "Pipeline aborted due to quality gate failure: ${qg.status}"
                         }
                     }*/
                    }
                    sleep 5
                EnddateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                }
                FuncLogFile("${StartdateTime}","${EnddateTime}","${env.STAGE_NAME}")
            }
        }
        stage('maven_build') {
            steps {
                script{
                stage_name_temp="${env.STAGE_NAME}"
                StartdateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                def MVN_PATH = tool (name: 'mvn386', type: 'maven') + "/bin/mvn"
                sh "$MVN_PATH clean package"
                sleep 3
                EnddateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                }
                
                FuncLogFile("${StartdateTime}","${EnddateTime}","${env.STAGE_NAME}")
            }
        }
        stage("docker_image_build"){
            steps{
                script{
                stage_name_temp="${env.STAGE_NAME}"
                StartdateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()    
                sh "docker build -t ${ACR_Registry_Url}/java-app ."
                EnddateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                }
                sleep 12
                FuncLogFile("${StartdateTime}","${EnddateTime}","${env.STAGE_NAME}")
            }
        }
        
        stage("push_image_to_acr"){
            steps{
                script{
                    stage_name_temp="${env.STAGE_NAME}"
                    StartdateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                    withDockerRegistry(credentialsId: 'ACR_id', url: 'http://poc251122.azurecr.io') {
                    sleep 30
                    sh "docker push ${ACR_Registry_Url}/java-app"
                    //sh "docker push poc251122.azurecr.io/java-app"
                    }
                    EnddateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                }
                FuncLogFile("${StartdateTime}","${EnddateTime}","${env.STAGE_NAME}")
            }
        }
        
        stage("deploy_on_container_instance"){
            steps{
                script{
                stage_name_temp="${env.STAGE_NAME}"
                StartdateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()    
                echo "Deploy application!!!"
                sleep 15
                EnddateTime = sh(script: "echo `date +%d-%m-%Y` `date +%H-%M-%S`%`date +%s`", returnStdout: true).trim()
                }
                FuncLogFile("${StartdateTime}","${EnddateTime}","${env.STAGE_NAME}")
            }
        }
        
        
        
    }
post{
		always{
		// echo "$stage_name_temp"
		func_pipeline_duration("${StartdateTimee}")
		}
		success{
		    echo "success"
		PipelineBuildStatus("success")
		}
		failure{
		    echo "fail"
		    //echo "$stage_name_temp"
		buildStatus("Fail")
		PipelineBuildStatus("Fail")
		}

    }
}
/// Functions ///
def func_pipeline_duration(PipelineStartTime){
    def Pipeline_sTime=PipelineStartTime.substring(PipelineStartTime.indexOf('%')+1)
    script{
    Pipeline_eTime = sh(script: "echo `date +%s`", returnStdout: true).trim()
    def Pipeline_Duration = (Pipeline_eTime as int) - (Pipeline_sTime as int)
    def gitCommitId= sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
    def gitCommitTime= sh(returnStdout: true, script: 'git log -1 --format="%at" | xargs -I{} date -d @{} +%d-%m-%Y" "%H-%M-%S').trim()
    def gitTimeUnix= sh(returnStdout: true, script: 'git log -1 --format="%at" | xargs -I{} date -d @{} +%s')
    def LeadTimeDuration = (Pipeline_eTime as int) - (gitTimeUnix as int)
    echo "gitTimeUnix: $gitTimeUnix and Pipeline_eTime:$Pipeline_eTime &  LeadTimeDuration: $LeadTimeDuration  "
    echo "yooo"
    sh """
    echo metric_pipeline_duration=${Pipeline_Duration},label_pipeline_name=${env.JOB_NAME},label_environment=${App_Env},label_build_id=${BUILD_NUMBER}  >> /home/jenkins_log/${env.JOB_NAME}.log
    echo metric_stage_sonarscan=sonarscan,label_pipeline_name=${env.JOB_NAME},label_build_id=${BUILD_NUMBER},label_environment=${App_Env},label_stage=sonar_scan,label_sonarqube_project_name=${Sonar_Project_Key}  >> /home/jenkins_log/${env.JOB_NAME}.log
    echo metric_git_commitid_time=${gitCommitTime},label_pipeline_name=${env.JOB_NAME},label_build_id=${BUILD_NUMBER},label_environment=${App_Env},label_git_commitid=${gitCommitId} >> /home/jenkins_log/${env.JOB_NAME}.log
    """
    }
}
//stage status
def buildStatus(jobStatus){
    script{
    sh """
    echo metric_stage_status=${jobStatus},label_pipeline_name=${env.JOB_NAME},label_build_id=${BUILD_NUMBER},label_environment=${App_Env},label_stage_name=${stage_name_temp}  >> /home/jenkins_log/${env.JOB_NAME}.log
    """
    }
}
def PipelineBuildStatus(jobStatus){
    script{
    sh """
    echo metric_pipeline_status=${jobStatus},label_pipeline_name=${env.JOB_NAME},label_build_id=${BUILD_NUMBER},label_environment=${App_Env}  >> /home/jenkins_log/${env.JOB_NAME}.log
    """
    }
}
def FuncLogFile(StartTime,EndTime,StageName){
    
    def sTime=StartTime.substring(StartTime.indexOf('%')+1)
    def sDate=StartTime.substring(0,StartTime.indexOf('%'))
    def eTime=EndTime.substring(EndTime.indexOf('%')+1)
    def eDate=EndTime.substring(0,EndTime.indexOf('%'))
    def finaly = (eTime as int) - (sTime as int)
    script{
    sh """
     echo metric_starttime=${sDate},label_pipeline_name=${env.JOB_NAME},label_build_id=${BUILD_NUMBER},label_environment=${App_Env},label_stage_name=${StageName}  >> /home/jenkins_log/${env.JOB_NAME}.log
     echo metric_endtime=${eDate},label_pipeline_name=${env.JOB_NAME},label_build_id=${BUILD_NUMBER},label_environment=${App_Env},label_stage_name=${StageName}  >> /home/jenkins_log/${env.JOB_NAME}.log
     echo metric_stage_duration=${finaly},label_pipeline_name=${env.JOB_NAME},label_build_id=${BUILD_NUMBER},label_environment=${App_Env},label_stage_name=${StageName}  >> /home/jenkins_log/${env.JOB_NAME}.log
     echo metric_stage_status=Success,label_pipeline_name=${env.JOB_NAME},label_build_id=${BUILD_NUMBER},label_environment=${App_Env},label_stage_name=${StageName}  >> /home/jenkins_log/${env.JOB_NAME}.log
    """
    }
}

