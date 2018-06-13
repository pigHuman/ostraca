def jenkins_path = "var/lib/jenkins"
def tf_path = "${jenkins_path}/terraform/build"
def ansible_path = "${jenkins_path}/ansible"
def terraform = "/usr/local/bin/terraform"

node{
    stage('scm'){
        checkout scm
    }

    stage('build & test'){
        sh 'echo "Building code and test"'
    }

    stage ('Confirm current green server'){
        //現在のサーバーの状況を取得
        dir("${tf_path}"){
            option = "\$3"
            id = sh returnStdout: true, script: "${terraform} state show aws_lb_target_group_attachment.green_attach | grep target_id | awk '{print ${option}}'"
        }
        sh "echo ${id}"
    }

    stage('Destroy of the current green server'){
        //現在のgreenサーバを破棄
    }

    stage('create mew blue server instance'){
        //新しいblueサーバのインスタンスを作成
    }

    stage('Provisioning for new blue server'){
        //新しいblueサーバを作成
    }

    stage('swich the blue server'){
        //現blueサーバと新blueサーバのTargetGroupを切り替える
    }
}