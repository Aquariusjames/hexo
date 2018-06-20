---
title: jenkins使用
date: 2018-06-12 14:18:19
tags: 
  - jenkins
categories:
  - k8s
---

[jenkins](https://jenkins.io/doc/book/getting-started/) 是一个自动化集成工具，可以实现对程序员完全透明化，只需要专注于自己的业务代码即可

# 安装

准备工作：
硬件条件：256 MB of RAM + 1 GB of drive space
软件条件：JRE 8 docker

## docker 方式安装

1. [install docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

2. 安装jenkins

> 启动 docker container
``` bash
docker run \ 
  -u root \ 
  --rm \  
  -d \ 
  -p 8080:8080 \ 
  -p 50000:50000 \ 
  -v jenkins-data:/var/jenkins_home \ 
  -v /var/run/docker.sock:/var/run/docker.sock \ 
  jenkinsci/blueocean
```

>[download war file](http://mirrors.jenkins.io/war-stable/latest/jenkins.war)

        java -jar jenkins.war
        brows http://localhost:8080  and wait until the Unlock Jenkins page appears.



## apt 方式安装

``` bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```

# 使用

jenkins 本身是一个持续集成部署的工具，可以和很多工具平台联动。

## credentials 

credentials 主要是用来存储一些用户密码、token 这些敏感信息

配置：

Credentials > Create permission [detail...](https://jenkins.io/doc/book/using/using-credentials/#credential-security)

## pipeline（流水线）

环境配置：
 - jenkins version >= 2.x 
 - pipeline plugin in jenkins

定义一个流水线：通过编写Jenkinsfile 来完成流水线作业。 Jenkinsfile 采用 [groovy](http://groovy-lang.org/semantics.html) 语法

jenkins 支持两种类型的流水线： 一种是 Declarative Pipeline, 另一种是 scripted pipeline, 个人推荐使用第二种，简洁

使用例子：

``` groovy

# declarative pipeline
pipeline {
    agent any

    environment { 
        CC = 'clang'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}

# scripted pipeline

node {

    withEnv(['CC=lang']) {
    }
    stage('Build') {
        echo 'Building....'
    }
    stage('Test') {
        echo 'Building....'
    }
    stage('Deploy') {
        echo 'Deploying....'
    }
}

```

agent/node:  instructs Jenkins to allocate an executor (on any available agent/node in the Jenkins environment) and workspace for the entire Pipeline.

环境变量：  
http://localhost:8080/pipeline-syntax/globals#env

常用的环境变量：
BUILD_ID  
JOB_NAME  
JENKINS_URL  



### pipeline generateor

click http://localhost:8080/pipeline-syntax/

可以看到很多帮助文档，其中Snippet Generator 用来生成scripted pipeline, Declarative Directive Generator 生成 declarative pipeline

### handle credentials

通过环境变量注入的方式, 前提是已经做好了credentials, 这里只是提供了一个读取的方法

``` groovy

# secret text
environment {
    AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
    AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')
}

# username and password
environment {
    BITBUCKET_COMMON_CREDS = credentials('jenkins-bitbucket-common-creds')
}

## actually environment variables
BITBUCKET_COMMON_CREDS - contains a username and a password separated by a colon in the format username:password.
BITBUCKET_COMMON_CREDS_USR - an additional variable containing the username component only.
BITBUCKET_COMMON_CREDS_PSW - an additional variable containing the password component only.

```

其他的证书获取的方法可以参考 generator

### handle parameters

``` groovy
pipeline {
    agent any
    parameters {
        string(name: 'Greeting', defaultValue: 'Hello', description: 'How should I greet the world?')
    }
    stages {
        stage('Example') {
            steps {
                echo "${params.Greeting} World!"
            }
        }
    }
}

# scripted pipeline
properties([parameters([string(defaultValue: 'Hello', description: 'How should I greet the world?', name: 'Greeting')])])

node {
    echo "${params.Greeting} World!"
}

```

### handle failure

``` groovy
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'make check'
            }
        }
    }
    post {
        always {
            junit '**/target/*.xml'
        }
        failure {
            mail to: team@example.com, subject: 'The Pipeline failed :('
        }
    }
}

# scripted pipeline
node {
    /* .. snip .. */
    stage('Test') {
        try {
            sh 'make check'
        }catch{
            mail to: team@example.com, subject: 'The Pipeline failed :('
        }
        finally {
            junit '**/target/*.xml'
        }
    }
    /* .. snip .. */
}
```

### 多个 angent/node 运行

``` groovy
pipeline {
    agent none
    stages {
        stage('Build') {
            agent any
            steps {
                checkout scm
                sh 'make'
                stash includes: '**/target/*.jar', name: 'app' 
            }
        }
        stage('Test on Linux') {
            agent { 
                label 'linux'
            }
            steps {
                unstash 'app' 
                sh 'make check'
            }
            post {
                always {
                    junit '**/target/*.xml'
                }
            }
        }
        stage('Test on Windows') {
            agent {
                label 'windows'
            }
            steps {
                unstash 'app'
                bat 'make check' 
            }
            post {
                always {
                    junit '**/target/*.xml'
                }
            }
        }
    }
}

# scripted pipeline
stage('Build') {
    node {
        checkout scm
        sh 'make'
        stash includes: '**/target/*.jar', name: 'app' 
    }
}

stage('Test') {
    node('linux') { 
        checkout scm
        try {
            unstash 'app' 
            sh 'make check'
        }
        finally {
            junit '**/target/*.xml'
        }
    }
    node('windows') {
        checkout scm
        try {
            unstash 'app'
            bat 'make check' 
        }
        finally {
            junit '**/target/*.xml'
        }
    }
}

```

stash： 捕获对应的文件，让流水线的其他angent 一起使用

label: jenkins lable expression

## mutibranch pipeline 

多分支的scm 流水线 [detail](https://jenkins.io/doc/book/pipeline/multibranch/)

## docker pipeline

``` groovy
pipeline {
    agent {
        docker { image 'node:7-alpine' }
    }
    stages {
        stage('Test') {
            steps {
                sh 'node --version'
            }
        }
    }
}

# scripted pipeline
node {
    /* Requires the Docker Pipeline plugin to be installed */
    docker.image('node:7-alpine').inside {
        stage('Test') {
            sh 'node --version'
        }
    }
}

```

### 缓存 docker container 数据

``` groovy
pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v $HOME/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B'
            }
        }
    }
}

# scripted pipeline
node {
    /* Requires the Docker Pipeline plugin to be installed */
    docker.image('maven:3-alpine').inside('-v $HOME/.m2:/root/.m2') {
        stage('Build') {
            sh 'mvn -B'
        }
    }
}
```

### 使用多容器

``` groovy
pipeline {
    agent none
    stages {
        stage('Back-end') {
            agent {
                docker { image 'maven:3-alpine' }
            }
            steps {
                sh 'mvn --version'
            }
        }
        stage('Front-end') {
            agent {
                docker { image 'node:7-alpine' }
            }
            steps {
                sh 'node --version'
            }
        }
    }
}

# scripted pipeline
node {
    /* Requires the Docker Pipeline plugin to be installed */

    stage('Back-end') {
        docker.image('maven:3-alpine').inside {
            sh 'mvn --version'
        }
    }

    stage('Front-end') {
        docker.image('node:7-alpine').inside {
            sh 'node --version'
        }
    }
}
```

### 使用 Dockerfile

Jenkins 支持从 scm 的 Dockerfile 来构建镜像并运行，而不是从镜像仓库里面拉取

``` docker
# file Dockerfile
FROM node:7-alpine
RUN apk add -U subversion
```

Jenkinsfile
``` groovy
pipeline {
    agent { dockerfile true }
    stages {
        stage('Test') {
            steps {
                sh 'node --version'
                sh 'svn --version'
            }
        }
    }
}


```

### sidecar(解决docker 服务依赖问题)

``` groovy
node {
    checkout scm
    /*
     * In order to communicate with the MySQL server, this Pipeline explicitly
     * maps the port (`3306`) to a known port on the host machine.
     */
    docker.image('mysql:5').withRun('-e "MYSQL_ROOT_PASSWORD=my-secret-pw" -p 3306:3306') { c ->
        /* Wait until mysql service is up */
        sh 'while ! mysqladmin ping -h0.0.0.0 --silent; do sleep 1; done'
        /* Run some tests which require MySQL */
        sh 'make check'
    }
}

```

通过 docker link 来链接mysql 服务, cenos 提供了一个程序执行环境

``` groovy
node {
    checkout scm
    docker.image('mysql:5').withRun('-e "MYSQL_ROOT_PASSWORD=my-secret-pw"') { c ->
        docker.image('mysql:5').inside("--link ${c.id}:db") {
            /* Wait until mysql service is up */
            sh 'while ! mysqladmin ping -hdb --silent; do sleep 1; done'
        }
        docker.image('centos:7').inside("--link ${c.id}:db") {
            /*
             * Run some tests which require MySQL, and assume that it is
             * available on the host name `db`
             */
            sh 'make check'
        }
    }
}
```

### 构建新的镜像

``` groovy
node {
    checkout scm

    def customImage = docker.build("my-image:${env.BUILD_ID}")

    customImage.inside {
        sh 'make test'
    }
}

# 如何push

node {
    checkout scm
    def customImage = docker.build("my-image:${env.BUILD_ID}")
    customImage.push()
}

# 如何打tag

node {
    checkout scm
    def customImage = docker.build("my-image:${env.BUILD_ID}")
    customImage.push()

    customImage.push('latest')
}

# 自定义dockerfile, 添加 build 函数参数

node {
    checkout scm
    // Builds test-image from the Dockerfile found at ./dockerfiles/test/Dockerfile.
    def testImage = docker.build("test-image", "./dockerfiles/test") 

    testImage.inside {
        sh 'make test'
    }
}

node {
    //Builds my-image:${env.BUILD_ID} from the Dockerfile found at ./dockerfiles/Dockerfile.test
    checkout scm
    def dockerfile = 'Dockerfile.test'
    def customImage = docker.build("my-image:${env.BUILD_ID}", "-f ${dockerfile} ./dockerfiles") 
}

```

### 自定义 registry

``` groovy
node {
    checkout scm
    docker.withRegistry('https://registry.example.com') {
        docker.image('my-custom-image').inside {
            sh 'make test'
        }
    }
}

# authentication

node {
    checkout scm
    //add a "Username/Password" Credentials item from the Jenkins home page
    docker.withRegistry('https://registry.example.com', 'credentials-id') {
        def customImage = docker.build("my-image:${env.BUILD_ID}")
        /* Push the container to the custom Registry */
        customImage.push()
    }
}
```

### 共享库

[Extending with Shared Libraries](https://jenkins.io/doc/book/pipeline/shared-libraries/)

### pipeline 语法

1 Declarative Pipelines

``` groovy

pipeline {
    /* insert Declarative Pipeline here */
}

```

[detail](https://jenkins.io/doc/book/pipeline/syntax/)

## blueocean

一款新的UI，[detail](https://jenkins.io/doc/book/blueocean)

# jenkins in k8s

Jenkins Server和slave节点直接有几种连接方式：ssh连接和jnlp连接。Kubernetes plugin插件用的是jnlp方式。这种方式是通过运行slave.jar，指定Jenkins Server的url参数和secret token参数，来建立连接。

docker 运行jenkins slave (ssh 模式)
这种模式docker 运行一个slave 容器跟普通物理机使用完全一致，这里不做说明。

同样可以再在同一个slave节点（docker 容器）上绑定很多个工程或者任务。

docker 运行jenkins slave (jnlp 模式)
Jnlp 模式的则相对应用的比较少，jnlp 是由jenkins slave节点（物理节点，虚机或者容器均可）发起连接的，
他 会根据配置的jenkins master的url , Jenkins连接的token和jenkins slave name( lable)来进行进行连接。


# jenkins k8s plugins的使用

## 配置

安装 kubernetes plugins ，进入jenkins 系统设置，如下图所示

<div align="center"> <img src="/images/jenkins/jenkins-kube-plugin.jpg" width=""/> </div><br>

**注意事项：** 在使用自定义的镜像的时候，因为/home/jenkins 目录被挂载到了NFS上面，因此在镜像中保留的文件全部被清空掉，kube/config文件需要通过NFS才能挂载


参考资料:

- [Jenkins On Kubernetes---Jenkins上Kubernetes Plugin的使用](https://blog.csdn.net/felix_yujing/article/details/78725142)  
- [初试 Jenkins 使用 Kubernetes Plugin 完成持续构建与发布](https://blog.csdn.net/aixiaoyang168/article/details/79767649)  
- [Kubernetes集群上基于Jenkins的CI/CD流程实践](https://yq.aliyun.com/articles/180888)  


 