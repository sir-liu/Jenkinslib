#!groovy

@Library('Jenkinslib') _  //引用Library
def tools = new org.devops.tools()  // 指的就是org/devops/tools.groovy


string workspace = "/opt/jenkins/wordspace"  // 自定义工作目录
hello()
pipeline {
    // ========agent语句块开始========
    agent {
        node {
            label "master"  // 指定运行节点的标签或者名称
            customWorkspace "${workspace}"  // 自定义工作目录，调用上面的变量（可选）
        }
    }
    // ======agent块结束======
    options {  // 运行选项按需指定，不是必须的
        timestamps()  // 日志会有时间，便于排查日志
        skipDefaultCheckout()  //删除隐式checkout scm语句，声明式的脚本默认会去检查有没有配代码库，如果有的话会自动下载代码，但我没有必要去让他下载，所以这里删除
        disableConcurrentBuilds()  // 禁止并行，根据项目需要，有些项目是不能并行的，可以使用这句。
        timeout(time: 1, unit: 'HOURS')  // 设置流水线超时时间，在企业中使用流水线时会遇到很多构建超时了，一直卡在那里，一直失败，这样会消耗很多资源，占用构建队列，导致构建队列出现堵塞这种情况，所以这里设置一小时超时，这个时间已经很长了。
    }
    // ===========options语句块结束==========
    // ============stages语句块开始《一个或多个》====================
    stages {
        stage("GetCode"){  // 这是第一个小阶段的名称
            when { environment name:"rocco",value: "texttt"}  //跳过当前步骤，写在那个步骤里就跳过
            steps{  // 步骤
                timeout(time:5,unit:"MINUTES"){  // 步骤超时时间
                    script{  // 填写运行代码
                        println("获取代码")
                        sh "/usr/bin/df -h"
                        input id: '007', message: '是否要继续', parameters: [choice(choices: ['a', 'b'], description: '', name: '请选择')], submitter: 'admin'
                    }
                }
            }
        }

        stage("01"){ //添加一个任务并行，下面的两个步骤一并运行。
            failFast true //开起并行
            parallel {

                //  第一个小阶段结束
                stage("Build"){
                    steps{
                        timeout(time:20, unit:"MINUTES"){
                            script{
                                println("应用打包")

                            }
                        }
                    }
                }
                //  第二个小阶段结束
                stage("CodeScan"){
                    steps{
                        timeout(time:30, unit:"MINUTES"){
                            script{
                                print("代码扫描")

                                tools.PrintMes("this is my lib!")  // 调用Library
                            }
                        }
                    }
                }
            }
        }
        //  第三个小阶段结束
    }
    //  ======================stages阶段结束================
    //  ===============构建后执行post===================
    post {
        //  always指不管结果如何都执行
        always {
            script{
                println("always")
            }
        }
        //  always语句块结束
        //  success指构建成功才会执行，一般在企业中可能会触发一个状态作一个变更
        success {
            script{
                currentBuild.description = "\n 构建成功！"  // currentBuild是一个全局变量，description是构建描述
            }
        }
        //  success语句块结束
        //  failure指构建失败时执行，例如通知管理员
        failure {
            script{
                currentBuild.description = "\n 构建失败！"
            }
        }
        //  failure语句块结束
        //  aborted指任务取消时执行，一般也会做一个通知，和构建失败的对待方式差不多，如果有什么资源需要回收的可以写在这里
        aborted {
            script{
                currentBuild.description = "\n 构建取消"
            }
        }
        //  aborted语句块结束
    }
    //  ================post语句结束=======================
}
