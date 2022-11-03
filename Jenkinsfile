
def git_auth = "68f2087f-a034-4d39-a9ff-1f776dd3dfa8"
//构建版本的名称
def tag = "latest"
//Harbor私服地址
def harbor_url = "192.168.100.134:85"
//Harbor的项目名称
def harbor_project_name = "tensquare"

//Harbor的凭证
def harbor_auth = "7b5e3508-b66b-4af4-a0c0-f30465e5c55d"

node {
 
		stage('Preparation') { // for display purposes
			// Get some code from a GitHub repository
			 //[name: '*/master']  or   [name: '*/main']
				checkout([$class: 'GitSCM', branches: [[name: '${branch}']],doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [],userRemoteConfigs: [[credentialsId: ${git_auth}, url:'https://github.com/wenanlin/tensquare_parent.git']]])

		}
		stage('Build') {
			//定义镜像名称
			def imageName = "${project_name}:${tag}"
			//编译，安装公共工程
			sh "mvn -f tensquare_common clean install"
			//编译，构建本地镜像
			sh "mvn -f ${project_name} clean package dockerfile:build"
			//给镜像打标签
			sh "docker tag ${imageName}
			${harbor_url}/${harbor_project_name}/${imageName}"
			//登录Harbor，并上传镜像
			withCredentials([usernamePassword(credentialsId: "${harbor_auth}",passwordVariable: 'password', usernameVariable: 'username')]) {
				//登录
				sh "docker login -u ${username} -p ${password} ${harbor_url}"
				//上传镜像
				sh "docker push ${harbor_url}/${harbor_project_name}/${imageName}"
				//删除本地镜像
				sh "docker rmi -f ${imageName}"
				sh "docker rmi -f ${harbor_url}/${harbor_project_name}/${imageName}"
			}
		}
 
 
}

