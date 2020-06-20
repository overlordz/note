# gitlab CI

##持续集成
CI，Continuous Integration，持续集成，是软件开发过程中一个非常重要的环节，在互联网敏捷开发的过程中，持续集成通常用来进行日常编译和自动化测试，来保证及时发现提交的问题，避免影响项目进度。

通常持续集成的过程包括：
提交（合并）代码
编译
测试
发布

##持续集成工具
CI工具有很多，目前最为常用应该是Jenkins。Jenkins通常包括一个master和很多个slave。master用于配置和组织节点、任务，slave则用来真正执行配置好的任务。因为用户群体的庞大，Jenkins上的各种插件，尤其是很多可视化插件都非常丰富，可以帮助很多新手快速配置所需的任务。
gitlab-ci是git官方的持续集成工具，在Git工程管理页面上，也有专门的CI配置和展示页。


##gitlab-ci runner的安装与配置



	https://packages.gitlab.com/runner/gitlab-runner/el/7/x86_64/gitlab-runner-11.3.1-1.x86_64.rpm
	#上传/安装


###启动Gitlab Runner

	gitlab-runner start
	#查看启动时完整参数
	ps -ef | grep gitlab-runner
	#查看gitlab-runner帮助信息
	gitlab-runner


###注册Gitlab Runner

	#开始注册
	gitlab-runner register
	Running in system-mode.                            
	Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
	#输入Gitlab访问的URL地址
	http://devops.efly.cc/
	Please enter the gitlab-ci token for this runner:
	#输入注册令牌：
	yYjJrXsKpHWsuyDDxf9v
	Please enter the gitlab-ci description for this runner:
	#输入描述，命名规范：[项目名称]-runner
	hci-runner     
	Please enter the gitlab-ci tags for this runner (comma separated):
	#输入tags，命名规范：[项目名称]
	#注意tags与.gitlab.yml中的tags标签要对应，否则无法调用该Runner
	hci  
	Registering runner... succeeded                     runner=ySs6BiDg
	Please enter the executor: kubernetes, docker, docker-ssh, parallels, shell, docker-ssh+machine, ssh, virtualbox, docker+machine:
	#输入Runner类型
	shell    
	Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!


###.gitlab-ci.yml
什么是.gitlab-ci.yml?
Gitlab流水线的job是由项目的.gitlab-ci.yml文件来定义，里面可以写多个不同的job。job比作为流水线上的一个作业/动作，定义工作的具体内容。

### .gitlab-ci.yml举例说明

	#定义不同的阶段，按实际增减
	stages:
	  - build
	  - test
	  - deploy
	  - rollback
	
	#定义before_script：在每个job被执行之前都会先调用执行（可选）
	#before_script:
	#  - global before script
	
	#build阶段job1:因为用不上，所以可以不定义job
	
	#发布测试环境
	test:
	  stage: test
	  script:
	    - bash /home/gitlab-runner/app/app_runner.sh build_and_deploy
	  tags:
	    - app
	  only:
	    - feature/1.0
	    
	#发布测试环境
	reload:
	  stage: test
	  script:
	    - bash /home/gitlab-runner/app/app_runner.sh breload
	  tags:
	    - app
	  only:
	    - feature/1.0
	  when: "manual"


​	

	#测试环境回滚
	rollback_application:
	  stage: test
	  script:
	    - bash /home/gitlab-runner/app/app_runner.sh rollback
	  tags:
	    - app
	  only:
	    - feature/1.0
	  when: "manual"

###配置说明

before_script 这一部分执行阶段都会进行，我这执行的是获取git commit提交的时间，赋值给NOW_DATE_TIME 变量；
variables 是定义一些变量，这里定义了测试、正式的IP地址，打包编译的文件名，supervisorctl的进程名；
build_stage、deploy_test 、deploy_master是一个个任务，可以自定义名称，它们用stage关联了某个阶段，比如build_stage关联了build阶段，deploy_test关联了deploy阶段，deploy_master也关联了deploy阶段。
only 定义哪个分支提交才会执行，我这里定义了提交到test分支就部署到测试环境，提交到master分支就发布到正式环境。
script 是任务的主要内容，需要执行的命令都写在这。在build阶段，用go build命令打包go程序。在deploy阶段就把打包后的文件复制到服务器，然后用supervisorctl重启进程。
when 定义了什么时候才执行。
tags标签指定gitlab runner。
###使.gitlab-ci.yml文件生效

	#复制`.gitlab-ci.yml`文件到Gitlab项目根目录
	git add .
	git commit -m "添加.gitlab-ci.yml文件"
	git push