def jenkins_path = "/var/lib/jenkins"
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
        
            try{
                result = sh returnStdout: true, script :"${terraform} state show aws_instance.2anet_server1 | grep ${id}"
            
                cgreen_name = "2anet_server1"
            }catch(exception){
                cgreen_name = "2anet_server2"
            }
        }
        sh "echo ${cgreen_name}"
    }

    stage('Destroy of the current green server'){
        //現在のgreenサーバを破棄
        dir("${tf_path}"){
            sh "${terraform} destroy -auto-approve -target=aws_instance.${cgreen_name} ./stage1"
        }
    }

    stage('create mew blue server instance'){
        //新しいblueサーバのインスタンスを作成
        dir("${tf_path}"){
            sh "${terraform} apply -auto-approve ./stage1"
        }
    }

    stage('Provisioning for new blue server'){
        //新しいblueサーバを作成
        dir("${tf_path}"){
            option = "\$3"
            id = sh returnStdout: true, script: "${terraform} state show aws_instance.${cgreen_name} | egrep ^public_ip | awk '{print ${option}}' | tr -d '\n'"
        
        }
        sh "echo ${ip}"
        dir("${ansible_path}"){
            sh "echo '[blue_server]' > ./hosts"
            sh "echo ${ip} >> ./hosts"
            sh "ansible-playbook -i ./hosts --private-key=./2anet.pem ./ostraca.yml"
        }
    }

    stage('swich the blue server'){
        //現blueサーバと新blueサーバのTargetGroupを切り替える
    }
}